## Task 1 Report — Becoming a Certificate Authority (CA) (OpenSSL)

### 1) What this task is doing (in plain words)

In a normal PKI setup, you pay or otherwise rely on a commercial Certificate Authority (CA) to vouch for identities by signing certificates. In this lab, we *become* that trusted entity ourselves by creating a **root CA certificate**. Because it’s a *root*, it is **self-signed** (it signs its own certificate). Once that’s done, this CA can later sign certificates for servers/users in the other tasks.

A key detail from the instructions you pasted: OpenSSL’s CA workflow relies on a configuration file (commonly `openssl.cnf`) and a small CA “database” structure (folders + `index.txt` + `serial`) so OpenSSL can track what it issued.

---

### 2) Evidence from your screenshots (what you actually did)

#### 2.1 Installing / locating OpenSSL + copying the config

You verified OpenSSL was installed via Homebrew and set OpenSSL’s binary path, then located the system `openssl.cnf` and copied it into your working directory so you could edit it locally.

  ![Figure 1](./screenshots/screenshots-week11/task1/1.png)
  <figcaption><b>Figure 1.</b>–Installing/verifying OpenSSL via Homebrew and setting an OpenSSL path variable.</figcaption>

  
![Figure 2](./screenshots/screenshots-week11/task1/2.png)
<figcaption><b>Figure 2.</b>–Locating the Homebrew OpenSSL configuration file and copying <code>openssl.cnf</code> into the working directory.</figcaption>

#### 2.2 Editing `openssl.cnf` (CA defaults)

You opened the copied `openssl.cnf` in the Pico editor, navigated to the CA section, and specifically changed the setting that the lab asked you to change: **allow repeated subjects** by setting:

* `unique_subject = no` (uncommented)

That matches the lab’s note (“very likely we will do that in the lab”).

![Figure 3](./screenshots/screenshots-week11/task1/3.png)
<figcaption><b>Figure 3.</b>–Opening the copied <code>openssl.cnf</code> file using the Pico text editor.</figcaption>

![Figure 4](./screenshots/screenshots-week11/task1/4.png)
<figcaption><b>Figure 4.</b>–Viewing the <code>[ CA_default ]</code> section of <code>openssl.cnf</code> before enabling duplicate-subject certificates.</figcaption>

![Figure 5](./screenshots/screenshots-week11/task1/5.png)
<figcaption><b>Figure 5.</b>–Enabling the <code>unique_subject = no</code> option to allow issuing multiple certificates with the same subject.</figcaption>

#### 2.3 Creating the CA directory structure (`demoCA`)

Per the lab instructions (and matching the config defaults), you created the expected CA working directory and its subfolders, and initialized the two required files:

* `index.txt` as an empty database index
* `serial` initialized to `1000`

![Figure 6](./screenshots/screenshots-week11/task1/6.png)
<figcaption><b>Figure 6.</b>–Creating the <code>demoCA</code> directory structure and initializing the <code>index.txt</code> and <code>serial</code> files.</figcaption>


#### 2.4 Generating the root CA key + self-signed certificate

You ran the CA self-signed certificate generation command:

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
* **OU** = to1-group6
* **CN** = to1-group6 Root CA
* **emailAddress** = [www.modelCA.com](http://www.modelCA.com)

![Figure 8](./screenshots/screenshots-week11/task1/8.png)
<figcaption><b>Figure 8.</b>–Entering the Distinguished Name (DN) information for the root CA certificate.</figcaption>


#### 2.5 Inspecting the certificate and key (the lab questions come from here)

You ran the two inspection commands the lab requested:

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

## 3) Answers to the lab questions (based on your outputs)

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

## 4) Quick wrap-up (what this proves)

From your screenshots, you successfully:

* prepared the CA config (`openssl.cnf`) correctly, including enabling duplicate subject issuance (`unique_subject = no`);
* created the CA database structure (`demoCA`, `index.txt`, `serial=1000`);
* generated a 4096-bit RSA private key (`ca.key`) protected by a passphrase;
* generated a **self-signed Root CA certificate** (`ca.crt`);
* verified via decoded output that:

  * it is a CA cert (`Basic Constraints: CA:TRUE`);
  * it is self-signed (`Issuer == Subject`, and SKI == AKI);
  * and you extracted the RSA parameters from the printed modulus/exponents/primes.

If you want, I can also reformat the RSA parameters (`n, d, p, q`) into cleaner single-line hex strings (no wraps) *using exactly what’s visible in your figures*, but I’ll keep it as-is unless you tell me how strict your report format needs to be.
