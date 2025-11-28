# **LOGBOOK 9 - Secret-Key Encryption Lab**

## **Task 1 – Frequency Analysis**

This section documents the commands executed to perform the frequency analysis attack on the monoalphabetic substitution cipher and provides direct evidence via screenshots.

---

## **Objective**

The objective of this task is to apply frequency analysis techniques to the provided ciphertext, identify statistical patterns, perform iterative substitution testing, and ultimately recover portions of the plaintext and decipher the substitution key.

---

## **Phase 1: Frequency Analysis and Initial Substitutions**

#### **1.**

* **Commands Used:**

  ```bash
  ./freq.py
  ```

  This command generates the 1-gram, 2-gram, and 3-gram frequency statistics from the ciphertext.

* **Screenshot:**
  ![Figure 1](./screenshots/screenshots-week9/task1/included/01_freq_1gram.png)

  <figcaption><strong>Figure 1</strong> – Output of <code>freq.py</code> showing the most frequent single-letter occurrences in the ciphertext.</figcaption>

  This output focuses on bigram (2-gram) frequencies generated during the same execution.

* **Screenshot:**
  ![Figure 2](./screenshots/screenshots-week9/task1/included/02_freq_2gram.png)

  <figcaption><strong>Figure 2</strong> – Top bigram frequencies revealing repeating ciphertext digraphs useful for identifying English patterns.</figcaption>


  This result displays the trigram (3-gram) statistics.

* **Screenshot:**
  ![Figure 3](./screenshots/screenshots-week9/task1/included/03_freq_trigram.png)

  <figcaption><strong>Figure 3</strong> – Trigram frequency distribution; the dominance of a particular trigram helps infer common English sequences such as “the”.</figcaption>

---

#### **2.**

* **Commands Used:**

  ```bash
  cp ciphertext.txt in0.txt
  tr 'ytn' 'THE' < in0.txt > in1.txt
  head in1.txt
  ```

  This step tests the hypothesis that ciphertext letters `ytn` map to the plaintext letters `T`, `H`, and `E`.

* **Screenshot:**
  ![Figure 4](./screenshots/screenshots-week9/task1/included/04_tr_attempt1.png)

  <figcaption><strong>Figure 4</strong> – Initial partial substitution showing the emergence of the plaintext word “THE”, validating the mapping.</figcaption>

---

## **Phase 2: Iterative Key Refinement and Partial Decryption**

#### **3.**

* **Commands Used:**

```bash
tr 'ytnvup' 'THEAND' < in0.txt > in2.txt
head in2.txt
```

* **Screenshot:**
  ![Figure 5](./screenshots/screenshots-week9/task1/included/05_tr_attempt2.png)

  <figcaption><strong>Figure 5</strong> – Expanded character substitutions, resulting in additional partially recognizable English fragments.</figcaption>

---

#### **4.**

* **Commands Used:**

  ```bash
  tr 'ytnvupmr' 'THEANDIG' < in0.txt > in3.txt
  head in3.txt
  ```

* **Screenshot:**
  ![Figure 6](./screenshots/screenshots-week9/task1/included/06_tr_attempt3.png)

  <figcaption><strong>Figure 6</strong> – Continued improvement in plaintext visibility as more correct mappings are applied.</figcaption>

---

#### **5.**

* **Commands Used:**
  (Incrementally improved substitution command.)

* **Screenshot:**
  ![Figure 7](./screenshots/screenshots-week9/task1/included/07_tr_attempt4.png)

  <figcaption><strong>Figure 7</strong> – Larger sections of readable text appear, indicating convergence toward the correct substitution key.</figcaption>

---

#### **6.**

* **Commands Used:**
  (Further mapping refinement.)

* **Screenshot:**
  ![Figure 8](./screenshots/screenshots-week9/task1/included/08_tr_attempt5.png)

  <figcaption><strong>Figure 8</strong> – The ciphertext increasingly resembles English as substitutions for medium-frequency letters are validated.</figcaption>

---

#### **7.**

* **Commands Used:**
  (Yet another intermediate substitution.)

* **Screenshot:**
  ![Figure 9](./screenshots/screenshots-week9/task1/included/09_tr_attempt6.png)

  <figcaption><strong>Figure 9</strong> – Further clarity is achieved; several English words are now fully recognizable.</figcaption>

---

#### **8.**

* **Commands Used:**
  (Additional refinements to the key are tested.)

* **Screenshot:**
  ![Figure 10](./screenshots/screenshots-week9/task1/included/10_tr_attempt7.png)

  <figcaption><strong>Figure 10</strong> – A near-complete plaintext emerges as most alphabet mappings have been identified.</figcaption>

---

#### **9.**

* **Commands Used:**
  (Likely the final or near-final substitution table test.)

* **Screenshot:**
  ![Figure 11](./screenshots/screenshots-week9/task1/included/11_tr_head_partial.png)

  <figcaption><strong>Figure 11</strong> – View of the substitution key header showing a nearly complete alphabet mapping.</figcaption>

---

#### **10.**

* **Commands Used:**
  (Finalized substitution applied to full ciphertext.)

* **Screenshot:**
  ![Figure 12](./screenshots/screenshots-week9/task1/included/12_plaintext_partial1.png)

  <figcaption><strong>Figure 12</strong> – First segment of the recovered plaintext after applying the full substitution key.</figcaption>

---

#### **11.**

* **Commands Used:**
  (Continuation of final plaintext generation.)

* **Screenshot:**
  ![Figure 13](./screenshots/screenshots-week9/task1/included/13_plaintext_partial2.png)

  <figcaption><strong>Figure 13</strong> – Second plaintext segment showing coherent English grammar and sentence flow.</figcaption>

---

#### **12.**

* **Commands Used:**
  (Plaintext continued.)

* **Screenshot:**
  ![Figure 14](./screenshots/screenshots-week9/task1/included/14_plaintext_partial3.png)

  <figcaption><strong>Figure 14</strong> – Third plaintext segment confirming correctness of the final substitution table.</figcaption>

---

#### **13.**

* **Commands Used:**
  (Plaintext final section.)

* **Screenshot:**
  ![Figure 15](./screenshots/screenshots-week9/task1/included/15_plaintext_partial4.png)

  <figcaption><strong>Figure 15</strong> – Final portion of the fully decrypted text, demonstrating the success of the frequency analysis attack.</figcaption>

---

## **Observations**

* The frequency distribution of the ciphertext closely mirrored typical English frequency patterns, enabling accurate initial guesses.
* The trigram `ytn` appeared disproportionately often, strongly suggesting a mapping to `the`.
* Each iterative `tr` substitution increased the visibility of English structure, confirming or refuting earlier assumptions.
* As substitutions accumulated, plaintext readability improved exponentially rather than linearly due to compounding contextual cues.
* The preservation of whitespace significantly reduced ambiguity when interpreting short common words.
* The final plaintext displayed consistent English grammar, verifying that the substitution key was correctly reconstructed.

---

## **Conclusions**

This task successfully demonstrated how frequency analysis, combined with iterative substitution testing, can break a monoalphabetic substitution cipher. The process required analyzing n-gram statistics, forming hypotheses consistent with English linguistic patterns, and progressively refining the substitution key until the plaintext became fully readable.

---

## **Summary**

In this task, frequency analysis was applied to a provided ciphertext to uncover patterns characteristic of English. Using `freq.py` outputs and a series of methodical substitution attempts with `tr`, a complete key was reconstructed, and the plaintext was fully decrypted. This exercise highlights the inherent weakness of monoalphabetic substitution ciphers and reinforces why more advanced cryptographic techniques are necessary for secure communications.

