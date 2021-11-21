# This Python 3 environment comes with many helpful analytics libraries installed
# It is defined by the kaggle/python Docker image: https://github.com/kaggle/docker-python
# For example, here's several helpful packages to load

import numpy as np # linear algebra
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)

# Input data files are available in the read-only "../input/" directory
# For example, running this (by clicking run or pressing Shift+Enter) will list all files under the input directory

import os
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))

# You can write up to 20GB to the current directory (/kaggle/working/) that gets preserved as output when you create a version using "Save & Run All" 
# You can also write temporary files to /kaggle/temp/, but they won't be saved outside of the current session





import itertools


all_needed = set(itertools.permutations(range(7), 7))
s1, s1_needed = [], {_ for _ in all_needed if (_[0] == 0 and _[1] == 1)}
s2, s2_needed = [], s1_needed.copy()
s3, s3_needed = [], s1_needed.copy()





def dist(s, p):
    for l in range(len(p), 0, -1):
        if tuple(s[-l:]) == tuple(p[:l]):
            return len(p) - l
    return len(p)

def pair_len(pair):
    if pair[1]:
        return len(pair[0])
    else:
        return len(pair[0]) + 100500

while all_needed or s1_needed or s2_needed or s3_needed:
    (s1, s1_needed), (s2, s2_needed), (s3, s3_needed) = sorted(((s1, s1_needed), (s2, s2_needed), (s3, s3_needed)), 
                                                               key=pair_len)
    best_score, best_tail = 999, None
    for p in itertools.chain(all_needed, s1_needed):
        score = tail_len = dist(s1, p)
        score *= 2
        s = s1[-7:]
        s.extend(p[-tail_len:])
        
        for offset in range(1, tail_len):
            permutation = tuple(s[-offset-7:-offset])
            if permutation in all_needed:
                score -= 1
            if permutation in s1_needed:
                score -= 2
        permutation = tuple(s1[-7:])
        if permutation in all_needed:
            score -= 1
        if permutation in s1_needed:
            score -= 2
        if score < best_score:
            best_score, best_tail = score, p[-tail_len:]
    if best_tail:
        s1.extend(best_tail)
        for offset in range(1, len(best_tail)):
            permutation = tuple(s1[-offset-7:-offset])
            all_needed.discard(permutation)
            s1_needed.discard(permutation)
        permutation = tuple(s1[-7:])
        all_needed.discard(permutation)
        s1_needed.discard(permutation)
#     print(len(s1), len(s1_needed), len(s2), len(s2_needed), len(s3), len(s3_needed), len(all_needed))





LETTERS = {
    0: '🎅',  # father christmas
    1: '🤶',  # mother christmas
    2: '🦌',  # reindeer
    3: '🧝',  # elf
    4: '🎄',  # christmas tree
    5: '🎁',  # gift
    6: '🎀',  # ribbon
}

S0 = ''.join(LETTERS[_] for _ in s1)
S1 = ''.join(LETTERS[_] for _ in s2)
S2 = ''.join(LETTERS[_] for _ in s3)





import pandas as pd


words = (S0, S1, S2)

submission = pd.Series(words, name='schedule')
submission.to_csv('submission.csv', index=False)

display(submission)
display(submission.apply(len).rename("Lengths"))
