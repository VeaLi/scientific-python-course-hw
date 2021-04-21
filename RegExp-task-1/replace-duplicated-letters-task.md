```python
import re
```


```python
S = 'Mus musculus (Y456), Agalma elegans (AB34), Frillagalma vityazi, Cordagalma tottoni'
```

# Replace all duplicated letters to one


```python
# replace all duplicated letters
rgx = re.compile(r'(.)\1')
rgx.sub(r'\1', S)
```




    'Mus musculus (Y456), Agalma elegans (AB34), Frilagalma vityazi, Cordagalma totoni'



# Mus musculus -> M. musculus


```python
rgx = re.compile(r"[A-Z]\w+\s")
for match in rgx.findall(S):
    S = re.sub(match, match[0] + '. ', S)

S
```




    'M. musculus (Y456), A. elegans (AB34), F. vityazi, C. tottoni'



# Mus musculus (Y456) -> M. musculus Y456


```python
rgx = re.compile(r"[A-Z]\w+\s")
for match in rgx.findall(S):
    S = re.sub(match, match[0] + '. ', S)

rgx = re.compile(r"[\(\)]")
for match in rgx.findall(S):
    S = re.sub("\\" + match, '', S)

S
```




    'M. musculus Y456, A. elegans AB34, F. vityazi, C. tottoni'


