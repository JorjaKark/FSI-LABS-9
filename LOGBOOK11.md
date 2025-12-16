## Task 1 Report — Becoming a Certificate Authority (CA) (OpenSSL)


In a normal PKI setup, you pay or otherwise rely on a commercial Certificate Authority (CA) to vouch for identities by signing certificates. In this lab, we *become* that trusted entity ourselves by creating a **root CA certificate**. Because it’s a *root*, it is **self-signed** (it signs its own certificate). Once that’s done, this CA can later sign certificates for servers/users in the other tasks.

> Note: OpenSSL’s CA workflow relies on a configuration file (commonly `openssl.cnf`) and a small CA “database” structure (folders + `index.txt` + `serial`) so OpenSSL can track what it issued.

---

#### 1. Installing / locating OpenSSL + copying the config

You verified OpenSSL was installed via Homebrew and set OpenSSL’s binary path, then located the system `openssl.cnf` and copied it into your working directory so you could edit it locally.

  ![Figure 1](./screenshots/screenshots-week11/task1/1.png)
  <figcaption><b>Figure 1.</b>–Installing/verifying OpenSSL via Homebrew and setting an OpenSSL path variable.</figcaption>

  
![Figure 2](./screenshots/screenshots-week11/task1/2.png)
<figcaption><b>Figure 2.</b>–Locating the Homebrew OpenSSL configuration file and copying <code>openssl.cnf</code> into the working directory.</figcaption>

#### 2 Editing `openssl.cnf` (CA defaults)

You opened the copied `openssl.cnf` in the Pico editor, navigated to the CA section, and specifically changed the setting that the lab asked you to change: **allow repeated subjects** by setting:

* `unique_subject = no` (uncommented)

That matches the lab’s note (“very likely we will do that in the lab”).

![Figure 3](./screenshots/screenshots-week11/task1/3.png)
<figcaption><b>Figure 3.</b>–Opening the copied <code>openssl.cnf</code> file using the Pico text editor.</figcaption>

![Figure 4](./screenshots/screenshots-week11/task1/4.png)
<figcaption><b>Figure 4.</b>–Viewing the <code>[ CA_default ]</code> section of <code>openssl.cnf</code> before enabling duplicate-subject certificates.</figcaption>

![Figure 5](./screenshots/screenshots-week11/task1/5.png)
<figcaption><b>Figure 5.</b>–Enabling the <code>unique_subject = no</code> option to allow issuing multiple certificates with the same subject.</figcaption>

#### 3. Creating the CA directory structure (`demoCA`)

Per the lab instructions (and matching the config defaults), we created the expected CA working directory and its subfolders, and initialized the two required files:

* `index.txt` as an empty database index
* `serial` initialized to `1000`

![Figure 6](./screenshots/screenshots-week11/task1/6.png)
<figcaption><b>Figure 6.</b>–Creating the <code>demoCA</code> directory structure and initializing the <code>index.txt</code> and <code>serial</code> files.</figcaption>


#### 4. Generating the root CA key + self-signed certificate

We ran the CA self-signed certificate generation command:

```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 \
  -keyout ca.key -out ca.crt
```

OpenSSL generated the RSA keypair (4096-bit) and prompted you for:

* a PEM passphrase (to protect `ca.key`)
* subject fields (Country, State, Organization, Common Name, etc.)

![Figure 7](./screenshots/screenshots-week11/task1/7.png)
<figcaption><b>Figure 7.</b>–Generating the self-signed root CA certificate using the <code>openssl req -x509</code> command.</figcaption>


Your entered subject information (as shown) was:

* **C** = PT
* **ST** = Porto
* **L** = Paranhos
* **O** = FEUP
* **OU** = t01-group6
* **CN** = t01-group6 Root CA
* **emailAddress** = [www.modelCA.com](http://www.modelCA.com)

![Figure 8](./screenshots/screenshots-week11/task1/8.png)
<figcaption><b>Figure 8.</b>–Entering the Distinguished Name (DN) information for the root CA certificate.</figcaption>


#### 5. Inspecting the certificate and key (the lab questions come from here)

We ran the two inspection commands the lab requested:

```bash
openssl x509 -in ca.crt -text -noout
openssl rsa  -in ca.key -text -noout
```

![Figure 9](./screenshots/screenshots-week11/task1/9.png)
<figcaption><b>Figure 9.</b>–Decoded output of the CA certificate showing version, serial number, issuer, subject, and validity period.</figcaption>


![Figure 10](./screenshots/screenshots-week11/task1/10.png)
<figcaption><b>Figure 10.</b>–Decoded certificate output displaying the RSA public key modulus and public exponent.</figcaption>


![Figure 11](./screenshots/screenshots-week11/task1/11.png)
<figcaption><b>Figure 11.</b>–X509v3 extensions confirming CA status through <code>Basic Constraints: CA:TRUE</code>.</figcaption>

![Figure 12](./screenshots/screenshots-week11/task1/12.png)
<figcaption><b>Figure 12.</b>–Decoded RSA private key output showing the 4096-bit modulus <code>n</code>.</figcaption>


![Figure 13](./screenshots/screenshots-week11/task1/13.png)
<figcaption><b>Figure 13.</b>–RSA private key output displaying the public exponent <code>e</code> and private exponent <code>d</code>.</figcaption>

![Figure 14](./screenshots/screenshots-week11/task1/14.png)
<figcaption><b>Figure 14.</b>–RSA private key output showing the prime numbers <code>p</code> (prime1) and <code>q</code> (prime2).</figcaption>

![Figure 15](./screenshots/screenshots-week11/task1/15.png)
<figcaption><b>Figure 15.</b>–RSA private key output displaying CRT parameters <code>exponent1</code> and <code>exponent2</code>.</figcaption>

![Figure 16](./screenshots/screenshots-week11/task1/16.png)
<figcaption><b>Figure 16.</b>–RSA private key output showing the CRT coefficient used for optimized decryption.</figcaption>

---

##  Answers to the lab questions (based on your outputs)

### Q1 — What part of the certificate indicates this is a CA’s certificate?

The clearest marker is the **X509v3 Basic Constraints** extension showing:

* `X509v3 Basic Constraints: critical`
* `CA:TRUE`

This appears directly in your decoded certificate output.
(See Figure 11.)

Why this matters: in X.509, **Basic Constraints** is the standard extension that tells verifiers whether the certificate is allowed to act as a CA (i.e., sign other certificates).

---

### Q2 — What part of the certificate indicates this is a self-signed certificate?

You have two strong “tells” in your output:

1. **Issuer equals Subject**
   In the decoded certificate text, the `Issuer:` line and the `Subject:` line contain the same DN values (C=PT, ST=Porto, … CN=to1-group6 Root CA, …).
   (Visible in Figure 9.)

2. **Authority Key Identifier matches Subject Key Identifier**
   In your certificate’s extensions, the **Authority Key Identifier** value is the same as the **Subject Key Identifier** value (same hex bytes). That’s exactly what you’d expect when the certificate is signed by itself.
   (Visible in Figure 11.)

---

### Q3 — Identify RSA parameters `e`, `d`, `n`, `p`, and `q` from your certificate/key output

Below I’m mapping each RSA parameter to *where it appears* in your outputs, and I’m listing the values in the same colon-hex formatting OpenSSL prints.

#### Public exponent `e`

* Found in: certificate output (Figure 10) and RSA key output (Figure 13)
* Value:

  * `e = 65537 (0x10001)`

#### Modulus `n`

* Found in: certificate output under **Subject Public Key Info → Modulus** (Figures 9–10) and also in the private key output under `modulus:` (Figure 12)
* Value (shown across many wrapped lines in your screenshots):

  * Starts with: `00:c8:15:31:ec:a3:c1:48:f8:79:ab:98:91:3c:65:...`
  * Ends with: `...:3b:bc:31:67:4d:75:2e:d0:d8:03:aa:e3:a2:0b:a5:7b:69:ab`
  * (Full value is printed in Figures 9–10 / Figure 12.)

#### Private exponent `d`

* Found in: private key output under `privateExponent:` (Figure 13)
* Value (also wrapped across many lines):

  * Starts with: `07:43:55:b3:9c:62:28:ce:f4:43:b9:5f:14:4d:30:...`
  * Ends with: `...:cd:0c:10:bf:0e:ba:15:84:dc:95:d0:29`
  * (Full value is printed in Figure 13.)

#### Prime `p` (prime1)

* Found in: private key output under `prime1:` (Figure 14)
* Value:

  * Starts with: `00:e7:9c:43:ea:f9:3f:d8:6c:12:e9:b0:74:14:f1:...`
  * Ends with: `...:3d:87:8f:11:4a:2f:e5`
  * (Full value is printed in Figure 14.)

#### Prime `q` (prime2)

* Found in: private key output under `prime2:` (Figure 14; continues into later output)
* Value:

  * Starts with: `00:dd:27:02:bf:77:01:28:dc:e1:56:db:e3:9e:6e:...`
  * Ends with: `...:13:9f:47:f2:7d:fa:4f`
  * (The start is visible in Figure 14, and the “...fa:4f” ending is visible at the top of Figure 15.)

---

## Conclusions

We:

* prepared the CA config (`openssl.cnf`) and enabled duplicate subject issuance (`unique_subject = no`);
* created the CA database structure (`demoCA`, `index.txt`, `serial=1000`);
* generated a 4096-bit RSA private key (`ca.key`) protected by a passphrase;
* generated a **self-signed Root CA certificate** (`ca.crt`);
* verified via decoded output that:

  * it is a CA cert (`Basic Constraints: CA:TRUE`);
  * it is self-signed (`Issuer == Subject`, and SKI == AKI);
  * and you extracted the RSA parameters from the printed modulus/exponents/primes.


---

## Task 2 Report — Generating a Certificate Signing Request (CSR) for a Web Server

In this task, we generate a **Certificate Signing Request (CSR)** for a web server that wishes to obtain a public-key certificate from the Certificate Authority (CA) created in Task 1. A CSR contains the server’s **public key** and **identity information**, and it is later sent to the CA, which verifies the information and issues a signed certificate.

Unlike Task 1, where a **self-signed certificate** was created using the `-x509` option, here we intentionally **omit** that option so that OpenSSL generates a **request**, not a certificate. This CSR will later be signed by our root CA in the next task.

> Note: Modern browsers enforce strict hostname validation rules. Therefore, in addition to the Common Name (CN), the CSR must include a **Subject Alternative Name (SAN)** extension containing all valid hostnames for the server.

---

### 1. Generating the server private key and CSR

We generated a new RSA key pair and a CSR for our web server using the following command:

```bash
openssl req -newkey rsa:2048 -sha256 \
-keyout server.key -out server.csr \
-subj "/CN=www.group06.com/O=FEUP/C=PT" \
-passout pass:dees \
-addext "subjectAltName = DNS:www.group06.com, DNS:www.group06A.com, DNS:www.group06B.com"
```

This command performs the following actions:

* generates a **2048-bit RSA private key**, stored in `server.key` and protected with a passphrase;
* creates a **certificate signing request**, stored in `server.csr`;
* sets the server’s identity information (CN, organization, country);
* adds a **Subject Alternative Name (SAN)** extension containing multiple DNS names.

![Figure 1](./screenshots/screenshots-week11/task2/1.png)

<figcaption><b>Figure 1.</b>–Generating a 2048-bit RSA private key and a Certificate Signing Request (CSR) with Subject Alternative Names.</figcaption>

The Common Name (CN) identifies the primary hostname of the server, while the SAN extension includes additional hostnames that will be accepted by browsers.

---

### 2. Inspecting the generated CSR

After generating the CSR, we inspected its contents to verify that all required information was correctly included:

```bash
openssl req -in server.csr -text -noout
```

![Figure 2](./screenshots/screenshots-week11/task2/2.png)

<figcaption><b>Figure 2.</b>–Decoded CSR output showing the subject information and RSA public key parameters.</figcaption>

From the decoded CSR output, we observe:

* **Subject**:

  ```
  CN=www.group06.com, O=FEUP, C=PT
  ```

  This matches exactly the identity information provided during CSR generation.

* **Public Key Information**:

  * Algorithm: RSA
  * Key size: 2048 bits
  * Public exponent: 65537 (0x10001)

![Figure 3](./screenshots/screenshots-week11/task2/3.png)

<figcaption><b>Figure 3.</b>–CSR extensions showing the Subject Alternative Name (SAN) field.</figcaption>

The CSR also contains the required **X509v3 Subject Alternative Name** extension:

```
DNS:www.group06.com
DNS:www.group06A.com
DNS:www.group06B.com
```

Including the Common Name inside the SAN extension is mandatory; otherwise, modern browsers would reject the certificate even if the CN matches the hostname.

---

## 3. Inspecting the server private key

After generating the CSR, we inspected the server’s private key to verify that the RSA parameters were correctly generated. This was done using the following command:

```bash
openssl rsa -in server.key -text -noout
```

The correct passphrase was entered, allowing OpenSSL to successfully decode the private key.

![Figure 4](./screenshots/screenshots-week11/task2/4.png)

<figcaption><b>Figure 4.</b>–Decoded RSA private key showing the modulus and public/private exponents.</figcaption>

![Figure 5](./screenshots/screenshots-week11/task2/5.png)

<figcaption><b>Figure 5.</b>–Decoded RSA private key showing the prime factors and CRT parameters.</figcaption>

From the decoded output, we observe the following:

* **Key size**:

  ```
  Private-Key: (2048 bit, 2 primes)
  ```

  This confirms that a 2048-bit RSA key pair was generated, as required.

* **Modulus (`n`)**:
  The modulus is displayed as a large hexadecimal value and represents the product of the two prime numbers (`n = p × q`).

* **Public exponent (`e`)**:

  ```
  publicExponent: 65537 (0x10001)
  ```

  This is the standard public exponent used in RSA.

* **Private exponent (`d`)**:
  Displayed under `privateExponent`, used for decryption and signing operations.

* **Prime numbers (`p` and `q`)**:
  Shown as `prime1` and `prime2`, these are the two secret primes whose product forms the modulus.

* **CRT parameters**:
  The values `exponent1`, `exponent2`, and `coefficient` are also present. These parameters are used to optimize RSA operations via the Chinese Remainder Theorem.

The successful decoding confirms that the private key is valid, correctly encrypted, and corresponds to the public key included in the CSR.

---

## Conclusions

In this task, we successfully:

* generated a **2048-bit RSA private key** for a web server;
* created a **Certificate Signing Request (CSR)** containing the server’s public key and identity;
* correctly specified the Common Name (CN) for hostname identification;
* added a **Subject Alternative Name (SAN)** extension with multiple DNS entries;
* verified the CSR contents and confirmed the presence of the SAN extension;
* successfully decoded and inspected the server’s private key, identifying all RSA parameters (`n`, `e`, `d`, `p`, `q`) and CRT values.

The generated CSR (`server.csr`) and private key (`server.key`) are now fully validated and ready to be used by the Certificate Authority created in Task 1 to issue a signed server certificate in the next task.

---
Amazing — this is **exactly** the right material for Task 3, and you executed every required step correctly. Below is a **complete Task 3 report**, written in the **same style, structure, and tone as Task 1 and Task 2**, and explicitly tied to the screenshots and the SEED Lab instructions you pasted.

You can copy–paste this directly into your report.
(I’ll assume your screenshots are numbered `1.png` → `7.png` under `screenshots-week11/task3/` — adjust paths if needed.)

---

## Task 3 Report — Generating a Certificate for the Web Server

In this task, we use the **Certificate Authority (CA)** created in Task 1 to **sign the server’s Certificate Signing Request (CSR)** generated in Task 2. The result is a valid **X.509 server certificate** that can be used by a web server to authenticate itself to clients.

In real-world deployments, CSRs are sent to trusted third-party CAs. In this lab, however, our own CA acts as a trusted root, allowing us to issue certificates locally.

---

### 1. Preparing the OpenSSL configuration file

Before signing the server certificate, we verified and modified the OpenSSL configuration file (`openssl.cnf`) used by the CA.

#### 1.1 CA default settings

The configuration file defines the CA’s working directory and database files. In particular, the `[ CA_default ]` section specifies where issued certificates, serial numbers, and keys are stored.

![Figure 1](./screenshots/screenshots-week11/task3/1.png)

<figcaption><b>Figure 1.</b>–Opening the OpenSSL configuration file (<code>openssl.cnf</code>) in the Pico editor.</figcaption>

![Figure 2](./screenshots/screenshots-week11/task3/2.png)

<figcaption><b>Figure 2.</b>–<code>[ CA_default ]</code> section showing the CA directory, database, and serial number configuration.</figcaption>

As configured:

* the CA directory is `./demoCA`;
* issued certificates are tracked via `index.txt`;
* serial numbers are stored in `serial`;
* the CA private key is stored in `demoCA/private/cakey.pem`.

---

#### 1.2 Enabling extension copying

By default, OpenSSL does **not** copy extensions from a CSR into the issued certificate. This would cause the **Subject Alternative Name (SAN)** extension to be dropped, even if it was present in the CSR.

To ensure that SAN entries are preserved, we enabled extension copying by uncommenting the following line in `openssl.cnf`:

```ini
copy_extensions = copy
```

![Figure 3](./screenshots/screenshots-week11/task3/3.png)

<figcaption><b>Figure 3.</b>–Enabling extension copying to allow SAN fields from the CSR to be included in the issued certificate.</figcaption>

This step is essential for modern TLS certificates, as browsers rely on SAN rather than the Common Name for hostname validation.

---

### 2. Signing the server CSR with the CA

Once the configuration file was correctly prepared, we used the CA’s private key to sign the server’s CSR and generate a server certificate.

The following command was executed:

```bash
openssl ca -config openssl.cnf -policy policy_anything \
-md sha256 -days 3650 \
-in demoCA/server.csr -out demoCA/server.crt -batch \
-cert demoCA/ca.crt -keyfile demoCA/ca.key
```

This command performs the following:

* uses our CA configuration file (`openssl.cnf`);
* applies the `policy_anything` policy, which does not enforce strict subject matching;
* signs the CSR using SHA-256;
* issues a certificate valid for 3650 days (10 years);
* signs the certificate using the CA’s certificate (`ca.crt`) and private key (`ca.key`).

![Figure 4](./screenshots/screenshots-week11/task3/4.png)

<figcaption><b>Figure 4.</b>–Signing the server CSR using the CA private key and certificate.</figcaption>

From the output, we observe:

* the CSR signature is verified successfully;
* a new serial number (`4096 / 0x1000`) is assigned;
* the CA database is updated with a new entry.

This confirms that the certificate was successfully issued by the CA.

---

### 3. Inspecting the generated server certificate

After issuing the certificate, we decoded it to verify its contents and ensure that all required extensions were correctly included.

The following command was used:

```bash
openssl x509 -in demoCA/server.crt -text -noout
```

![Figure 5](./screenshots/screenshots-week11/task3/5.png)

<figcaption><b>Figure 5.</b>–Decoded server certificate showing issuer, subject, and validity period.</figcaption>

From the decoded certificate output, we confirm:

* **Issuer**:
  The issuer corresponds to our Root CA created in Task 1, proving that the certificate was signed by the CA.

* **Subject**:

  ```
  C=PT, O=FEUP, CN=www.group06.com
  ```

  This matches the subject specified in the CSR.

* **Public Key**:

  * Algorithm: RSA
  * Key size: 2048 bits
  * Public exponent: 65537

---

### 4. Verifying certificate extensions

The decoded certificate also includes the expected X.509 extensions.

![Figure 6](./screenshots/screenshots-week11/task3/6.png)

<figcaption><b>Figure 6.</b>–X509v3 extensions confirming that the certificate is not a CA certificate.</figcaption>

The **Basic Constraints** extension shows:

```
CA:FALSE
```

This confirms that the issued certificate is a **server certificate**, not a CA certificate.

---

![Figure 7](./screenshots/screenshots-week11/task3/7.png)

<figcaption><b>Figure 7.</b>–Subject Alternative Name (SAN) extension included in the issued server certificate.</figcaption>

The **Subject Alternative Name (SAN)** extension contains:

```
DNS:www.group06.com
DNS:www.group06A.com
DNS:www.group06B.com
```

This verifies that:

* the SAN extension from the CSR was successfully copied into the final certificate;
* all required hostnames are valid and recognized by modern browsers.

---

## Conclusions

In this task, we successfully:

* configured OpenSSL to allow extension copying from CSRs;
* used our self-created Certificate Authority to sign a server CSR;
* generated a valid X.509 server certificate (`server.crt`);
* verified that the certificate:

  * was issued by our CA;
  * contains the correct subject information;
  * has **CA:FALSE** in its Basic Constraints;
  * includes all required **Subject Alternative Names (SANs)**.

The generated server certificate is now fully functional and ready to be deployed on a web server for TLS authentication.

---



