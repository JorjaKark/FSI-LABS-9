# FSI-LABS-9

Proposed Report Structure (Aligned With Your Actual Method)
1. Introduction

Purpose of the lab

Why monoalphabetic substitution ciphers are vulnerable

Overview of your approach (frequency analysis + iterative refinement)

2. Understanding the Cipher
2.1 Monoalphabetic Substitution Cipher Basics
2.2 Why Frequency Analysis Works
2.3 Provided Tools and Data

ciphertext.txt

freq.py

Allowed use of tr for substitution testing
3. Frequency Analysis
3.1 Running the Statistical Analysis

To begin the analysis, I executed the provided freq.py script on the ciphertext. The script computes the frequencies of 1-grams (individual letters), 2-grams (bigrams), and 3-grams (trigrams) within the encrypted text. The following output was generated:

1-gram frequency list (top occurrences):
The most frequent ciphertext letters were:
n (488), y (373), v (348), x (291), u (280), q (276), m (264), h (235), t (183), i (166), p (156), a (116)...

2-gram frequency list (top occurrences):
The most common bigrams included:
yt (115), tn (89), mu (74), nh (58), vh (57), hn (57), vu (56), nq (53), xu (52)...

3-gram frequency list (top occurrences):
The most frequent trigrams were:
ytn (78), vup (30), mur (20), ynh (18), xzy (16)...

These frequency lists provide the statistical foundation for deducing likely plaintext substitutions.

3.2 Initial Interpretation of Letter, Bigram, and Trigram Frequencies

The frequency distribution offers immediate clues:

The most common ciphertext letter “n” is a strong candidate for plaintext ‘e’, since e is the most frequent letter in English.

High-frequency letters such as y, v, x, u are likely candidates for other common plaintext letters like t, a, o, n, i, r, s.

The repeated bigram “yt” suggests it could correspond to a common English pair such as “th”, “he”, or “in”.

The most frequent trigram “ytn” (78 occurrences) indicates it may represent an extremely common English word pattern such as “the” or “ing”.

These statistical patterns guided the first set of substitution hypotheses used in later stages.
