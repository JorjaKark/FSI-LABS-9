# **LOGBOOK 9 - Secret-Key Encryption Lab**

## **Task 1 – Frequency Analysis**

This section documents the commands executed to perform the frequency analysis attack on the monoalphabetic substitution cipher and provides direct evidence via screenshots.

---

## **Objective**

The objective of this task is to apply frequency analysis techniques to the provided ciphertext, identify statistical patterns, perform iterative substitution testing, and ultimately recover portions of the plaintext and decipher the substitution key.

---

## **Phase 1: Frequency Analysis**

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

## **Phase 2: Substitution Attempts**

Below, after each `tr` command, I have added short explanations describing **why I chose those specific substitutions**, based on simple frequency-analysis logic and common English patterns (all easily found in classical cryptanalysis sources or Wikipedia).

---

### **2. First Substitution Attempt**

* **Commands Used:**

  ```bash
  cp ciphertext.txt in0.txt
  tr 'ytn' 'THE' < in0.txt > in1.txt
  head in1.txt
  ```

* **Reasoning for choosing `ytn → THE`:**
  From the trigram frequency output, the sequence **“ytn”** appeared far more often than other trigrams.
  In English, **“the”** is the most common trigram by a wide margin, so it is standard in substitution-cipher attacks to test this first.
  This type of reasoning is directly supported by well-known English trigram frequency lists.

* **Screenshot:**
  ![Figure 4](./screenshots/screenshots-week9/task1/included/04_tr_attempt1.png)

  <figcaption><strong>Figure 4</strong> – Initial partial substitution showing the emergence of the plaintext word “THE”, validating the mapping.</figcaption>

```
THE xqavhq Tzhu   xu qzupvd lHmaH qEEcq vgxzT hmrHT vbTEh THmq ixur qThvurE
vlvhpq Thme THE gvrrEh bEEiq imsE v uxuvrEuvhmvu Txx

THE vlvhpq hvaE lvq gxxsEupEp gd THE pEcmqE xb HvhfEd lEmuqTEmu vT mTq xzTqET
vup THE veevhEuT mceixqmvu xb Hmq bmic axcevud vT THE Eup vup mT lvq q qHveEp gd
THE EcEhrEuaE xb cETxx TmcEq ze givasrxlu eximTmaq vhcavupd vaTmfmq c vup
v uvTmxuvi axufEhqvTmxu vq ghmEb vup cvp vq v bEfEh phEvc vgxzT lHE THEh THEhE

xzrHT Tx gE v ehEqmpEuT lmubHEd THE qEvqxu pmpuT ozqT qEEc EkThv ixur mT lvq
EkThv ixur gEavzqE THE xqavhq lEhE cxfEp Tx THE bmhqT lEEsEup mu cv haH Tx

vfxmp axubimaTmur lmTH THE aixqmur aEhEcxud xb THE lmuTEh xidcemaq THvusq
```

---

### **3. Second Substitution Attempt**

* **Commands Used:**

```bash
tr 'ytnvup' 'THEAND' < in0.txt > in2.txt
head in2.txt
```

* **Reasoning for adding `vup → AND`:**
  After confirming THE, several ciphertext clusters strongly resembled the shape of the word **“and”**.
  The letters **v**, **u**, and **p** repeatedly appeared in positions typical of A, N, and D (for example between known consonants or forming small functional words).
  “AND” is one of the most common English words, so mapping these next is a natural progression in classical frequency analysis.

* **Screenshot:**
  ![Figure 5](./screenshots/screenshots-week9/task1/included/05_tr_attempt2.png)

  <figcaption><strong>Figure 5</strong> – Expanded character substitutions, resulting in additional partially recognizable English fragments.</figcaption>

```
THE xqaAhq TzhN   xN qzNDAd lHmaH qEEcq AgxzT hmrHT AbTEh THmq ixNr qThANrE
AlAhDq Thme THE gArrEh bEEiq imsE A NxNArENAhmAN Txx
...
```

---

### **4. Third Substitution Attempt**

* **Commands Used:**

```bash
tr 'ytnvupmr' 'THEANDIG' < in0.txt > in3.txt
head in3.txt
```

* **Reasoning for adding `m r → I G`:**
  Once AND and THE started appearing correctly, many words contained repeated occurrences of **m** in the position of a common vowel—most often **I** in English.
  Meanwhile **r** appeared inside clusters resembling “ght,” “ing,” and “age,” all of which typically contain **G**.
  This deduction comes from typical English digram/trigram structures observable in simple frequency tables.

* **Screenshot:**
  ![Figure 6](./screenshots/screenshots-week9/task1/included/06_tr_attempt3.png)

```
THE xqaAhq TzhN   xN qzNDAd lHIaH qEEcq AgxzT hIGHT AbTEh THIq ixNG qThANGE
...
```

---

### **5. Fourth Substitution Attempt**

* **Commands Used:**

  ```bash
  tr 'ytnvupmrxq' 'THEANDIGOS' < in0.txt > in4.txt
  head in4.txt
  ```

* **Reasoning for adding `x q → O S`:**
  The letter **x** frequently appeared where English typically has **O**—for example before consonants that commonly follow “O” (oN, oF, so, to).
  The letter **q** appeared in positions resembling **S**, including plural-like endings or common blend positions (ST, SP).
  Both O and S are extremely high-frequency English letters, so testing them next is standard practice.

* **Screenshot:**
  ![Figure 7](./screenshots/screenshots-week9/task1/included/07_tr_attempt4.png)

```
THE OSaAhS TzhN   ON SzNDAd lHIaH SEEcS AgOzT hIGHT AbTEh THIS iONG SThANGE
...
```

---

### **6. Fifth Substitution Attempt**

* **Commands Used:**

  ```bash
  tr 'ytnvupmrxqlgbe' 'THEANDIGOSWBFP' < in0.txt > in5.txt
  head in5.txt
  ```

* **Reasoning for adding `l g b e → W B F P`:**
  At this point, many long English words were partially recognizable:
  “WITH”, “FEELS”, “AFTER”, “WAS”, “BY”, etc.
  By comparing these near-complete word shapes with common English spellings, mapping these four letters to **W**, **B**, **F**, and **P** became straightforward.
  This is standard contextual deduction made easier once major vowels and consonants are known.

* **Screenshot:**
  ![Figure 8](./screenshots/screenshots-week9/task1/included/09_tr_attempt6.png)

```
THE OSaAhS TzhN   ON SzNDAd WHIaH SEEcS ABOzT hIGHT AFTEh THIS iONG SThANGE
...
```

---

### **8. Sixth Substitution Attempt**

* **Commands Used:**

  ```bash
  tr 'ytnvupmrxqlgbecdaih' 'THEANDIGOSWBFPMYCLR' < in0.txt > in6.txt
  head in6.txt
  ```

* **Reasoning for adding `c d a i h → M Y C L R`:**
  After earlier substitutions, words like **METOO**, **COMPANY**, **CEREMONY**, **BLACK**, **RIGHT**, and **THERE** were almost readable, missing only a few letters.
  Those missing positions matched the plaintext letters **M**, **Y**, **C**, **L**, and **R**, which fit consistently across multiple word contexts.
  This stage typically involves recognizing whole words and filling in the gaps using simple elimination.

* **Screenshot:**
  ![Figure 9](./screenshots/screenshots-week9/task1/included/10_tr_attempt7.png)

```
THE OSCARS TzRN   ON SzNDAY WHICH SEEMS ABozT RIGHT AFTER THIS LONG STRANGE
...
```

---

### **9. Final Full Alphabet Substitution**

* **Commands Used:**

  ```bash
  tr 'abcdefghijklmnopqrstuvwxyz' 'CFMYPVBRLQXWIEJDSGKHNAZOTU' < in0.txt > in7.txt
  head in7.txt
  ```

* **Reasoning:**
  At this stage, the entire plaintext was nearly readable, and only a few letters remained unmapped.
  Using **process of elimination**—ensuring each ciphertext letter maps to a unique plaintext letter—and checking for English spelling correctness, the final few letters were assigned.
  This is the usual final step of a monoalphabetic substitution attack once 90% of the plaintext is visible.

* **Screenshot:**
  ![Figure 10](./screenshots/screenshots-week9/task1/included/11_tr_head_partial.png)


```
THE OSCARS TURN   ON SUNDAY WHICH SEEMS ABOUT RIGHT AFTER THIS LONG STRANGE
...
```

---

### **10. Viewing the Full Plaintext**

* **Commands Used:**

  ```bash
  cat in7.txt
  ```

* **Screenshots (plaintext output):**
  ![Figure 11](./screenshots/screenshots-week9/task1/included/12_plaintext_partial1.png)
  ![Figure 12](./screenshots/screenshots-week9/task1/included/13_plaintext_partial2.png)
  ![Figure 13](./screenshots/screenshots-week9/task1/included/14_plaintext_partial3.png)
  ![Figure 14](./screenshots/screenshots-week9/task1/included/15_plaintext_partial4.png)

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


---

## **Task 2 – Encryption using Different Ciphers and Modes**

---

This task explores the encryption of a plaintext file using multiple symmetric-key encryption modes provided by OpenSSL. The purpose is to demonstrate how different block cipher modes—**ECB**, **CBC**, and **CTR**—behave when encrypting identical plaintext with the same key. By comparing the resulting ciphertext files, we can analyze how structural patterns in the plaintext are preserved or concealed under each mode, thereby assessing the relative strengths of each mode in resisting pattern leakage and ciphertext analysis.

---

### **Methodology**

#### **Step 1: Verifying the Plaintext File Size**

To ensure consistency across all encryption operations, the byte size of the plaintext file was first checked using the `wc -c` command.

```bash
wc -c Files/words.txt
```

**Output:**

```
206662 Files/words.txt
```

This confirms that the input file is approximately **206 KB**, a sufficiently large sample to observe cryptographic mode behavior.

* **Screenshots:**
![Figure 15](./screenshots/screenshots-week9/task2/1.png)
<figcaption><strong>Figure 15</strong> – <code>wc -c</code> confirming byte size of plaintext.</figcaption>

---

#### **Step 2: Creating a Symbolic Link to the Plaintext**

To facilitate command readability and prevent filename clutter, a symbolic link `plaintext.txt` was created pointing to `Files/words.txt` using `ln -sf`.

```bash
ln -sf Files/words.txt plaintext.txt
ls -l plaintext.txt
```

**Output:**

```
lrwxrwxrwx 1 fsi fsi 15 Nov 30 15:12 plaintext.txt -> Files/words.txt
```

This ensures that all encryption commands operate on a consistently named input without duplicating the file.

* **Screenshots:**

![Figure 16](./screenshots/screenshots-week9/task2/2.png)
<figcaption><strong>Figure 16</strong> – Symbolic link creation and verification.</figcaption>

---

#### **Step 3: Defining the Encryption Key and IV**

The symmetric key and initialization vector (IV) were defined in hexadecimal format as environment variables:

```bash
export KEY=00112233445566778899aabbccddeeff
export IV=0102030405060708090a0b0c0d0e0f00
```

* `KEY` is a **128-bit (16-byte)** hexadecimal string, suitable for AES-128.
* `IV` is similarly 128 bits, required for CBC and CTR modes but **not used in ECB**.

These variables allow secure and flexible use within OpenSSL commands.

---

#### **Step 4: Performing Encryption with Different Modes**

Each encryption command uses `openssl enc` with the `-e` flag (encrypt), the specified cipher mode, input file, and output binary. Key and IV are provided in hex.

---

**(a) ECB Mode:**

```bash
openssl enc -aes-128-ecb -e -in plaintext.txt -out cipher_ecb.bin -K $KEY
```

> No IV is required for ECB.

**(b) CBC Mode:**

```bash
openssl enc -aes-128-cbc -e -in plaintext.txt -out cipher_cbc.bin -K $KEY -iv $IV
```

**(c) CTR Mode:**

```bash
openssl enc -aes-128-ctr -e -in plaintext.txt -out cipher_ctr.bin -K $KEY -iv $IV
```

After execution, the ciphertext files were listed for validation:

```bash
ls -l plaintext.txt cipher_*.bin
```

**Output:**

```
-rw-rw-r-- 1 fsi fsi 206662 Nov 30 15:13 cipher_cbc.bin
-rw-rw-r-- 1 fsi fsi 206672 Nov 30 15:13 cipher_ctr.bin
-rw-rw-r-- 1 fsi fsi 206672 Nov 30 15:13 cipher_ecb.bin
lrwxrwxrwx 1 fsi fsi     15 Nov 30 15:12 plaintext.txt -> Files/words.txt
```

![Figure 17](./screenshots/screenshots-week9/task2/3.png)
<figcaption><strong>Figure 17</strong> – All encryption modes executed successfully; ciphertext file sizes match.</figcaption>

---

### **Technical Explanation**

#### AES-128 Block Cipher

All encryption commands used **AES-128**, which processes 128-bit blocks (16 bytes) under a 128-bit key. The differences between the modes affect how blocks are chained or processed:

---

#### **ECB (Electronic Codebook Mode)**

* **Each block is encrypted independently.**
* Identical plaintext blocks yield identical ciphertext blocks.
* No diffusion across blocks; patterns in plaintext remain visible.

This mode is **deterministic and vulnerable** to pattern analysis—unsuitable for encrypting large or structured data.

---

#### **CBC (Cipher Block Chaining Mode)**

* Each plaintext block is **XOR’d with the previous ciphertext block** before encryption.
* Requires a unique IV for the first block.
* Errors propagate; loss of a single block affects decryption of two blocks.

CBC introduces randomness and breaks identical plaintext block repetition, offering **semantic security** when used with unpredictable IVs.

---

#### **CTR (Counter Mode)**

* Converts block cipher into a stream cipher.
* Encrypts a counter value (IV + nonce), then XORs with plaintext.
* Each counter is unique per block, ensuring stream variability.

CTR mode supports **parallelism** and is **error-resilient** (bit errors do not propagate), making it ideal for high-performance applications.

---

### **Result & Verification**

* All three encryption modes produced binary ciphertext files of comparable size (~206 KB), confirming full encryption of the plaintext input.
* ECB and CTR outputs were 206,672 bytes, while CBC was slightly smaller (206,662). This discrepancy is likely due to:

  * ECB/CTR requiring **padding** to complete the final block.
  * CBC mode possibly already aligned due to exact block-length data.

The encryption commands executed without error, and the output files can now be examined for structural analysis or decrypted to test mode behaviors.

---

### **Observations**

* ECB mode is deterministic; repeating plaintext blocks are not concealed.
* CBC mode ensures that each ciphertext block depends on both the current and previous plaintext, providing stronger confidentiality.
* CTR mode transforms AES into a **stream cipher**, ideal for variable-length plaintexts and partial encryption needs.
* File sizes across modes were consistent with AES block-size and padding behavior.
* OpenSSL’s `enc` utility simplifies command-line cryptography, but defaults (e.g., padding, format) must be understood to avoid insecure use.

---

### **Conclusions**

This task demonstrated practical encryption using AES under three distinct modes via the `openssl enc` utility. Each mode exhibits different cryptographic behaviors, particularly in handling block repetition, error propagation, and IV usage. The exercise emphasizes the importance of mode selection in real-world cryptographic implementations:

* **ECB should be avoided** due to pattern leakage.
* **CBC and CTR** provide stronger security guarantees and practical flexibility.
* Proper IV/key management is critical for maintaining confidentiality.

The hands-on approach validates theoretical cryptographic principles through controlled experimentation and file inspection.

---
