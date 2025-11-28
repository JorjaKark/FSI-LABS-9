# **Frequency Analysis Attack on a Monoalphabetic Substitution Cipher**

## **1. Introduction**

Monoalphabetic substitution ciphers replace each plaintext letter with a unique ciphertext letter using a fixed mapping. Historically used in classical cryptography, these ciphers are now known to be insecure due to their susceptibility to **frequency analysis**, which exploits statistical regularities in natural languages such as English.

The objective of this lab was to:

1. Recover the plaintext of a provided English article encrypted using a monoalphabetic substitution cipher.
2. Reconstruct the encryption key.
3. Demonstrate the practicality and power of frequency analysis through iterative pattern recognition.

The ciphertext was stripped of punctuation and numbers but retained spaces, significantly simplifying the analysis compared with fully realistic monoalphabetic ciphers.

---

## **2. Background**

### **2.1 Monoalphabetic Substitution Ciphers**

A monoalphabetic cipher uses a single fixed permutation of the alphabet. For example, plaintext `a` may map to ciphertext `q`, plaintext `b` to ciphertext `m`, etc., for all occurrences throughout the message.

While brute-forcing all (26!) possible keys is computationally infeasible, the cipher leaks structural information: **English is not random**. Certain letters, bigrams, and trigrams appear far more often than others.

### **2.2 Frequency Analysis**

The English language has well-documented statistical tendencies:

* **Single-letter frequency:**
  e, t, a, o, i, n, s, r, h are most common.
* **Bigram frequency:**
  th, he, in, er, an, re, on…
* **Trigram frequency:**
  the, ing, and…

By comparing these known distributions with ciphertext statistics, attackers can infer letter mappings and progressively reveal the plaintext.

### **2.3 Lab Simplifications**

Before encryption, the instructors:

1. Converted all text to lowercase.
2. Removed punctuation and numbers.
3. Preserved whitespace.
4. Applied a random substitution using the Unix `tr` command.

The lab also provided a Python script `freq.py` to compute 1-gram, 2-gram, and 3-gram frequencies.

---

## **3. Frequency Analysis**

### **3.1 Running `freq.py`**

The first step was to examine the statistical frequencies of the ciphertext. Running the script produced the following outputs.

#### **1-gram Frequencies**

<figure>
  <img src="1-freq.png" alt="1-gram frequency analysis output" />
  <figcaption><strong>Figure 1:</strong> Output of <code>freq.py</code> showing top 1-gram frequencies.</figcaption>
</figure>

The letter **n** was the most frequent. In English, **e** is typically the most common letter, making n→e a strong initial hypothesis.

#### **2-gram Frequencies**

<figure>
  <img src="2-freq.png" alt="2-gram frequency analysis output" />
  <figcaption><strong>Figure 2:</strong> Top bigrams in the ciphertext.</figcaption>
</figure>

The bigram **yt** appeared most often, suggesting a likely mapping to **th** or **he**, the two most common English bigrams.

#### **3-gram Frequencies**

<figure>
  <img src="3-freq.png" alt="3-gram frequency analysis output" />
  <figcaption><strong>Figure 3:</strong> Top trigrams in the ciphertext.</figcaption>
</figure>

The trigram **ytn** dominated. Because **the** is by far the most common English trigram, the mapping y→t, t→h, n→e became highly plausible.

---

## **4. Initial Substitution Attempts**

### **4.1 Testing the Hypothesis for “THE”**

To test the mapping y→T, t→H, n→E (uppercase used for clarity), the following command was used:

```
tr 'ytn' 'THE'
```

<figure>
  <img src="4-tr.png" alt="First tr output showing THE appearing" />
  <figcaption><strong>Figure 4:</strong> After substituting y→T, t→H, n→E, multiple occurrences of <em>THE</em> appear, validating the hypothesis.</figcaption>
</figure>

Immediately, readable instances of **THE** emerged in grammatically appropriate locations, strongly confirming the mapping.

---

## **5. Progressive Reconstruction of the Key**

Frequency analysis continued with the next most common letters.

### **5.1 Adding More High-Frequency Letters**

Repeated iterations of `tr` refined the mapping. Examples:

<figure>
  <img src="5-tr.png" alt="Second tr output" />
  <figcaption><strong>Figure 5:</strong> Additional mappings reveal fragments such as AND, IN, and other common words.</figcaption>
</figure>

<figure>
  <img src="6-tr.png" alt="Third tr output" />
  <figcaption><strong>Figure 6:</strong> More substitutions reveal longer meaningful fragments.</figcaption>
</figure>

### **5.2 Recognizing Common Patterns**

As more plaintext appeared, context and English grammar accelerated progress.

For example:

* A pattern resembling **_ING** became clear:
  leading to identification of ciphertext letters mapping to I, N, G.
* Articles and common connectors such as **AND**, **TO**, **OF**, **IS** emerged naturally.

Screenshots below show the evolution of readability as substitutions accumulated:

<figure>
  <img src="7-tr.png" alt="Fourth tr output" />
  <figcaption><strong>Figure 7:</strong> Larger English fragments appear as the mapping improves.</figcaption>
</figure>

<figure>
  <img src="8-tr.png" alt="Fifth tr output" />
  <figcaption><strong>Figure 8:</strong> Further clarity as more letters are substituted.</figcaption>
</figure>

<figure>
  <img src="9-tr.png" alt="Sixth tr output" />
  <figcaption><strong>Figure 9:</strong> Additional sections of plaintext now clearly readable.</figcaption>
</figure>

### **5.3 Near-Complete Key Recovery**

By this stage, only a handful of letters remained uncertain.

<figure>
  <img src="10-tr.png" alt="Seventh tr output" />
  <figcaption><strong>Figure 10:</strong> Most of the ciphertext now decodes cleanly.</figcaption>
</figure>

<figure>
  <img src="11-tr-head.png" alt="Key reconstruction head" />
  <figcaption><strong>Figure 11:</strong> Header view showing nearly complete mapping.</figcaption>
</figure>

---

## **6. Final Substitution Key**

After resolving the last few ambiguous letters, the full substitution table was reconstructed.

> **Note:** Include your verified substitution table here.
> If you provide your final substitution screenshot, I can format the exact table.

---

## **7. Fully Recovered Plaintext**

Once the full key was applied, the plaintext revealed a complete English article discussing the Oscars, Weinstein scandal, award-season politics, and the #MeToo movement.

Below are four segments illustrating the final decoded text:

<figure>
  <img src="12-cat-full-result-partial1.png" alt="Plaintext segment 1" />
  <figcaption><strong>Figure 12:</strong> Final plaintext (Part 1).</figcaption>
</figure>

<figure>
  <img src="13-cat-full-result-partial2.png" alt="Plaintext segment 2" />
  <figcaption><strong>Figure 13:</strong> Final plaintext (Part 2).</figcaption>
</figure>

<figure>
  <img src="14-cat-full-result-partial3.png" alt="Plaintext segment 3" />
  <figcaption><strong>Figure 14:</strong> Final plaintext (Part 3).</figcaption>
</figure>

<figure>
  <img src="15-cat-full-result-partial4.png" alt="Plaintext segment 4" />
  <figcaption><strong>Figure 15:</strong> Final plaintext (Part 4).</figcaption>
</figure>

---

## **8. Discussion**

### **8.1 Effectiveness of Frequency Analysis**

This lab demonstrates that monoalphabetic substitution ciphers provide no meaningful security:

* Statistical leakage allows rapid identification of several key letters.
* Recognition of English patterns (articles, suffixes, context) accelerates later deductions.
* With long texts, accuracy approaches 100% with little or no ambiguity.

### **8.2 Challenges Encountered**

* Some low-frequency letters (e.g., q, j, z) required contextual inference rather than frequency alone.
* Conflicts occasionally arose when initial guesses were slightly off; iterative correction was necessary.
* The ability to retain spaces significantly simplified the process.

### **8.3 Limitations of the Cipher**

If the ciphertext had:

* removed spaces,
* applied homophonic substitution,
* or used polyalphabetic substitution,

the attack would have been significantly more difficult or infeasible.

---

## **9. Conclusion**

Through the combined use of unigram, bigram, and trigram frequency analysis, contextual recognition, and iterative application of the Unix `tr` command, the full substitution key and plaintext were successfully recovered.

This exercise highlights the fundamental weakness of monoalphabetic substitution ciphers and reinforces the necessity of modern cryptographic schemes for secure communication.

---

## **Appendix A – Sample Commands Used**

```
./freq.py
tr 'ytn' 'THE'
tr 'ytnvup' 'THEAND'
tr 'ytnvupmr' 'THEANDIG'
...
tr 'abcdefghijklmnopqrstuvwxyz' 'CFMPYVBRLQXWIEJDSGKHNAZOTU'
```
