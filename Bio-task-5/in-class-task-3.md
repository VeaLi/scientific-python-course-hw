# 3


```python
'''
Let's practice in Bio package using.
Task:
1. read fasta file containing several DNA seqs (nucl.fasta)
2. subset seqs that have GC content > 45 and coding protein with aromaticity > 0.01
3. write a new fasta file with those protein(!) seqs
4. return the percentage of seqs that passed your filter
Hint: Bio.SeqIO, Bio.SeqRecord, Bio.SeqUtils
'''

import numpy as np
from Bio import SeqIO
from Bio.Seq import Seq
from Bio.SeqUtils import GC
from Bio.SeqUtils.ProtParam import ProteinAnalysis


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
                str(record.seq.transcribe().translate()))
            calc_arom = analysed_seq.aromaticity()

            # so, now you can filter
            if calc_gc >= filt_gc and calc_arom >= filt_arom:
                sequences[record.id] = record.seq

    # write a new fasta file with aminoacids

    with open(output_file, "w", encoding='utf-8') as f:
        for seq_id, seq in sequences.items():
            f.write(f'>{seq_id}\n{seq}\n')

    # print the percentage
    print(f'Filtered: {np.round(((1 - len(sequences)/c)*100),2)}%')


my_own_filtering('nucl.fasta', 'nucl.fasta.filtered')
```

    Filtered: 85.71%
    
