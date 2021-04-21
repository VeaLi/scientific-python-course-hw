```python
import re
```

# TASK


1) Download any isocitrate lyase enzyme AA seq from NCBI.

2) Search a pattern of active site (“Consensus pattern”) of isocitrate lyase enzyme in PROSITE.

3) Write python script that gives the answer in the following manner: “Isocitrate lyase (FASTA ID) contains its active site XXXXXX starting from Nth position”



## Download any isocitrate lyase enzyme AA seq from NCBI.
1. [isocitrate lyase Salmonella enterica subsp. enterica serovar Typhimurium var. monophasic 4,5,12:i:-](https://www.ncbi.nlm.nih.gov/protein/ASF67065.1)


```python
def read_single(path):
    """
    read single fasta sequence
    """
    AA, ID = None, None

    with open(path, 'r', encoding='utf-8') as f:
        ff = f.readlines()
        ID = ff[0].strip().split()[0][1:]
        AA = ''.join(l.strip() for l in ff[1:])
    return ID, AA.upper()
```


```python
read_single('AA.fasta')
```




    ('ASF67065.1',
     'MKTRTQQIEELQKEWTQPRWEGITRPYSAEEVVKLRGSVNPECTLAQLGAAKMWRLLHGEAKKGYINSLGALTGGQALQQAKAGIEAIYLSGWQVAADANLASSMYPDQSLYPANSVPAVVDRINNTFRRADQIQWASGIEPNDPRYVDYFLPIVADAEAGFGGVLNAFELMKSMIEAGAAAVHFEDQLASVKKCGHMGGKVLVPTQEAIQKLVAARLAADVMGVPTLVIARTDADAADLITSDCDPYDSGFITGERTSEGFYRTHAGIEQAISRGLAYAPYADLVWCETSTPDLELARRFADAIHAKYPGKLLAYNCSPSFNWQKNLDDKTIASFQQQLSDMGYKYQFITLAGIHSMWFNMFDLAHAYAQGEGMKHYVEKVQQPEFAAAKDGYTFVSHQQEVGTGYFDKVTTIIQGGASSVTALTGSTEEAQF')



## Search a pattern of active site (“Consensus pattern”) of isocitrate lyase enzyme in PROSITE.

K-[KR]-C-G-H-[LMQR]
[Isocitrate lyase signature](https://prosite.expasy.org/PS00161)

## Write python script that gives the answer in the following manner: “Isocitrate lyase (FASTA ID) contains its active site XXXXXX starting from Nth position”


```python
def get_start_end(m):
    """
    return substring start position, content
    """
    return m.start(), m.string[m.start():m.end()]


def get_position_of_as(path, sig):
    """
    return the position and content of the active site, 
    given path to fasta file and consensus pattern as raw string
    """
    ID, AA = read_single(path)
    Nth, site = get_start_end(re.compile(sig).search(AA))
    return f"Isocitrate lyase ({ID}) contains its active site “{site}” starting from {Nth}th position"
```


```python
get_position_of_as('AA.fasta', r'K[KR]CGH[LMQR]')
```




    'Isocitrate lyase (ASF67065.1) contains its active site “KKCGHM” starting from 192th position'


