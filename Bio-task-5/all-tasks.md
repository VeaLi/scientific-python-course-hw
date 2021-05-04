using [biopython.org/wiki/Seq](https://biopython.org/wiki/Seq)


```python
import re
import warnings

import numpy as np
from Bio import BiopythonWarning, SeqIO, pairwise2
from Bio.pairwise2 import format_alignment
from Bio.Seq import Seq
from Bio.SeqUtils import GC
from Bio.SeqUtils.ProtParam import ProteinAnalysis
from Bio.SubsMat import MatrixInfo as matlist
```


```python
# 1
'''
Let's remeber how to use dictionaries.
Task: return a dictionary where 
    * keys are IDs of seqs from an input fasta file (prot.fasta),
    * key's values are seqs itself. 
'''


def my_own_fasta_parser(inFile="prot.fasta"):

    sequences = {}

    with open(inFile, 'r', encoding='utf-8') as f:
        f = f.readlines()
        for line in f:
            if '>' in line:
                seq_id = line.replace('>', '').strip()
            else:
                sequences[seq_id] = line.strip()

    return sequences


my_own_fasta_parser()
```




    {'seq0': 'FQTWEEFSRAAEKLYLADPMKVRVVLKYRHVDGNLCIKVTDDLVCLVYRTDQAQDVKKIEKF',
     'seq1': 'KYRTWEEFTRAAEKLYQADPMKVRVVLKYRHCDGNLCIKVTDDVVCLLYRTDQAQDVKKIEKFHSQLMRLMELKVTDNKECLKFKTDQAQEAKKMEKLNNIFFTLM',
     'seq2': 'EEYQTWEEFARAAEKLYLTDPMKVRVVLKYRHCDGNLCMKVTDDAVCLQYKTDQAQDVKKVEKLHGK',
     'seq3': 'MYQVWEEFSRAVEKLYLTDPMKVRVVLKYRHCDGNLCIKVTDNSVCLQYKTDQAQDVK',
     'seq4': 'EEFSRAVEKLYLTDPMKVRVVLKYRHCDGNLCIKVTDNSVVSYEMRLFGVQKDNFALEHSLL',
     'seq5': 'SWEEFAKAAEVLYLEDPMKCRMCTKYRHVDHKLVVKLTDNHTVLKYVTDMAQDVKKIEKLTTLLMR',
     'seq6': 'FTNWEEFAKAAERLHSANPEKCRFVTKYNHTKGELVLKLTDDVVCLQYSTNQLQDVKKLEKLSSTLLRSI',
     'seq7': 'SWEEFVERSVQLFRGDPNATRYVMKYRHCEGKLVLKVTDDRECLKFKTDQAQDAKKMEKLNNIFF',
     'seq8': 'SWDEFVDRSVQLFRADPESTRYVMKYRHCDGKLVLKVTDNKECLKFKTDQAQEAKKMEKLNNIFFTLM',
     'seq9': 'KNWEDFEIAAENMYMANPQNCRYTMKYVHSKGHILLKMSDNVKCVQYRAENMPDLKK',
     'seq10': 'FDSWDEFVSKSVELFRNHPDTTRYVVKYRHCEGKLVLKVTDNHECLKFKTDQAQDAKKMEK'}




```python
# 2
'''
Another super easy task.

We have the same fasta file (prot.fasta).
Now we want to get a list with the ids of protein seqs that have 
a relative frequency higher than a given threshold for a given residue.

And don't forget to use my_own_fasta_parser function from a previous task!
'''


def my_own_residue_abundance(input_file, residue, threshold=0.2):

    seq_ids = []
    sequences = my_own_fasta_parser(input_file)
    residue = re.compile(r"{}".format(residue))

    for seq_id, sequence in sequences.items():
        if (len(residue.findall(sequence)) / len(sequence)) >= threshold:
            seq_ids.append(seq_id)
    return seq_ids


my_own_residue_abundance("prot.fasta", 'K', threshold=0.14)
```




    ['seq1', 'seq10']




```python
# 3
'''
Let's practice in Bio package using.
Task:
1. read fasta file containing several DNA seqs (nucl.fasta)
2. subset seqs that have GC content > 45 and coding protein with aromaticity > 0.01
3. write a new fasta file with those protein(!) seqs
4. return the percentage of seqs that passed your filter
Hint: Bio.SeqIO, Bio.SeqRecord, Bio.SeqUtils
'''


def my_own_filtering(input_file, output_file, filt_gc=45, filt_arom=0.125):

    sequences = {}
    c = 0

    with open(input_file, "r") as content:

        for record in SeqIO.parse(content, "fasta"):
            c += 1

            # calculate GC content using Bio
            calc_gc = GC(record.seq)

            # calculate aromaticity using Bio
            analysed_seq = ProteinAnalysis(
                str(record.seq.transcribe().translate(to_stop=True)))
            calc_arom = analysed_seq.aromaticity()

            # so, now you can filter
            if calc_gc >= filt_gc and calc_arom >= filt_arom:
                sequences[record.id] = record.seq

    # write a new fasta file with aminoacids

    with open(output_file, "w", encoding='utf-8') as f:
        for seq_id, seq in sequences.items():
            f.write(f'>{seq_id}\n{seq}\n')

    # print the percentage
    print(
        f'Percentage of seqs that passed the filter: : {np.round(((len(sequences)/c)*100),2)}%'
    )


my_own_filtering('nucl.fasta', 'nucl.fasta.filtered')
```

    Percentage of seqs that passed the filter: : 28.57%
    


```python
# 4
"""
Continue practicing in Bio package using:
Task:
complete the following code that should be able to return 
the best alignment of two amino acid seqs (pairwise2.align.globalds)
based on BLOSUM62 matrix from Bio.SubsMat.MatrixInfo.
http://rosalind.info/glossary/blosum62/
"""




def balign(first_seq, second_seq, matlist):

    # Load the matrix
    matrix = matlist.blosum62

    # using example from https://gist.github.com/JoaoRodrigues/8c2f7d2fc5ae38fc9cb2
    gap_open = -10
    gap_extend = -0.5

    # Generate the alignments
    alns = pairwise2.align.globalds(first_seq, second_seq, matrix, gap_open,
                                    gap_extend)

    # Extract the best alignment (first one in the alns list)
    top_aln = alns[0]

    # Print the alignment, ...
    aln_A, aln_B, score, begin, end = top_aln
    print(format_alignment(aln_A, aln_B, score, begin, end))


balign("ACCGT", "ACG", matlist)
```

    ACCGT
    ||  .
    AC--G
      Score=0.5
    
    


```python
# 5
""" You have some DNA sequence: AGTACTAGAGCATTCTATGGAG.
Find out which peptides could be created from it and sort them by their length.
Use as much Biopython modules as possible.
"""
warnings.filterwarnings("error")

peptides = []
dna = list("AGTACTAGAGCATTCTATGGAG")
for i in range(0, len(dna)):
    seq = dna[i:]
    try:
        s = Seq(''.join(seq))
        s = s.transcribe().translate(to_stop=True)
    except BiopythonWarning as e:
        while len(seq) % 3 != 0:
            seq.append('N')
        s = Seq(''.join(seq))
        s = s.transcribe().translate(to_stop=True)

    if str(s):
        peptides.append(str(s))

sorted(peptides, key=lambda x: len(x), reverse=True)
```




    ['STRAFYGX',
     'VLEHSME',
     'TRAFYGX',
     'LEHSME',
     'RAFYGX',
     'EHSME',
     'SILWX',
     'AFYGX',
     'HSME',
     'ILWX',
     'FYGX',
     'SME',
     'LWX',
     'YGX',
     'ME',
     'WX',
     'GX',
     'Y',
     'E',
     'X',
     'X']




```python
# 6
""" TASK: Try to create one-line function (without (!!!) using Bio package) 
that returns reverse complementary to a given sequence. 
Hint: using dictionaty & list comprehensions might be helpful.
"""


def rev_compl_one_line(seq):
    return ''.join(list(reversed([{'c': 'g', 'g': 'c', 'a': 't', 't': 'a'}[s] for s in list(seq.lower().strip())]))).upper()


assert rev_compl_one_line('AGTACACTGGT') == 'ACCAGTGTACT'

rev_compl_one_line('AGTACACTGGT')
```




    'ACCAGTGTACT'


