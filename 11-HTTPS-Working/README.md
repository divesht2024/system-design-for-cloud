
# HTTPS / SSL / TLS

## 1. What is HTTPS?

**HTTPS = HTTP + TLS**

HTTPS is used to securely communicate between a client (browser) and a server.

TLS provides:

* **Encryption** → protects data from being read
* **Authentication** → verifies the server's identity
* **Integrity** → detects data modification

> Modern HTTPS uses **TLS**. SSL is the older protocol and is no longer used for modern secure connections.

---

# 2. HTTP vs HTTPS

### HTTP

```text
Client
  ↓
HTTP Request
  ↓
Server
```

HTTP data is not encrypted.

### HTTPS

```text
Client
  ↓
Encrypted HTTPS Request
  ↓
Server
```

The communication is protected using TLS.

---

# 3. How HTTPS Works

The simplified flow is:

```text
User enters https://example.com
            ↓
           DNS
    Domain → IP Address
            ↓
      TCP Connection
     SYN → SYN-ACK → ACK
            ↓
       TLS Handshake
            ↓
    Server Certificate
            ↓
 Certificate Validation
            ↓
   Session Keys Established
            ↓
    Encrypted HTTP Request
            ↓
          Server
            ↓
    Encrypted HTTP Response
            ↓
         Browser
            ↓
     Website displayed
```

### Memory

```text
DNS
 ↓
TCP
 ↓
TLS Handshake
 ↓
Certificate Validation
 ↓
Session Keys
 ↓
Encrypted HTTP
 ↓
Response
```

---

# 4. Step 1 — DNS

The browser first needs to find the server's IP address.

```text
example.com
     ↓
    DNS
     ↓
IP Address
```

Example:

```text
example.com → 93.x.x.x
```

The actual IP may vary.

---

# 5. Step 2 — TCP Connection

For the common HTTPS-over-TCP case, the client establishes a TCP connection with the server.

TCP three-way handshake:

```text
Client              Server

  SYN  ───────────→
       ←──────── SYN-ACK
  ACK  ───────────→
```

Now the TCP connection is established.

> HTTP/3 is an exception: it uses **QUIC over UDP** instead of TCP.

---

# 6. Step 3 — TLS Handshake

After the transport connection is established, TLS negotiation begins.

### ClientHello

The client sends information such as:

* Supported TLS versions
* Supported cryptographic options
* Random value
* Key-exchange information

```text
Client
  ↓
ClientHello
  ↓
Server
```

---

### ServerHello

The server selects compatible cryptographic parameters and responds.

The server also provides its **TLS certificate**.

```text
Client
  ←
ServerHello + Certificate
```

---

# 7. Step 4 — Certificate

The server's certificate contains information such as:

* Domain name
* Server public key
* Certificate validity
* Certificate issuer
* CA signature

Example:

```text
example.com
     ↓
Certificate
     ↓
Public Key
     ↓
CA Signature
```

---

# 8. Step 5 — Certificate Validation

The browser verifies the certificate.

It checks things such as:

```text
Is the certificate trusted?
        ↓
Is the domain name correct?
        ↓
Is the certificate valid?
        ↓
Is the certificate chain trusted?
```

If validation fails, the browser can display a security warning.

---

# 9. Step 6 — Session Keys

The client and server establish shared **symmetric session keys**.

Modern TLS commonly uses ephemeral Diffie-Hellman key exchange, such as **ECDHE**, for establishing shared secrets.

The important idea:

```text
Client
   ↘
    Secure Key Establishment
   ↗
Server
        ↓
Shared Session Keys
```

These session keys are then used to protect the actual application data.

---

# 10. Why Symmetric Encryption?

HTTPS does **not** encrypt every HTTP request using public-key encryption.

Instead:

```text
Asymmetric Cryptography
        ↓
Authentication / Key Establishment
        ↓
Symmetric Session Keys
        ↓
Encrypt Application Data
```

Symmetric encryption is much faster and is suitable for protecting large amounts of data.

Common modern algorithms include:

* AES-GCM
* ChaCha20-Poly1305

---

# 11. Encrypted HTTP Communication

After the TLS handshake:

```text
HTTP Request
     ↓
TLS Encryption
     ↓
Encrypted Data
     ↓
Server
     ↓
TLS Decryption
     ↓
HTTP Request
```

The server sends its response through the same protected TLS connection.

```text
Server Response
     ↓
TLS Encryption
     ↓
Encrypted Data
     ↓
Browser
     ↓
TLS Decryption
     ↓
Response
```

---

# 12. What Does TLS Provide?

## 1. Encryption

Protects the confidentiality of data.

Example:

```text
password=hello123
```

is not sent as readable plaintext over the network.

---

## 2. Authentication

Helps the browser verify that it is communicating with the intended server.

This is done using the server's certificate and trusted CA chain.

---

## 3. Integrity

Helps detect whether protected data was modified during transmission.

```text
Original Data
     ↓
TLS Protection
     ↓
Network
     ↓
Modified Data?
     ↓
Detected
```

---

# 13. What is a CA?

**CA = Certificate Authority**

A CA is a trusted organization that issues/signs certificates.

Examples:

* Let's Encrypt
* DigiCert
* GlobalSign

The browser/operating system maintains a set of trusted CA certificates.

Basic relationship:

```text
Certificate Authority
        ↓
Signs Certificate
        ↓
Server Certificate
        ↓
Browser Validates
```

---

# 14. Public Key vs Private Key

A server certificate contains the server's **public key**.

The corresponding **private key** must remain secret on the server.

```text
Public Key
→ Can be shared

Private Key
→ Must remain secret
```

The private key should never be exposed.

---

# 15. HTTPS Ports

Common ports:

```text
HTTP  → 80
HTTPS → 443
```

For traditional HTTPS over TCP, port **443** is commonly used.

HTTP/3 also commonly uses port 443, but over **QUIC/UDP**.

---

# 16. TLS Termination

TLS can be terminated at a Load Balancer or Reverse Proxy.

Example:

```text
User
  ↓ HTTPS
ALB
  ↓ HTTP
EC2
```

Here the ALB handles the TLS connection.

Another option is encryption all the way to the backend:

```text
User
  ↓ HTTPS
ALB
  ↓ HTTPS
EC2
```

This is useful when end-to-end encryption is required.

---

# 17. HTTPS with AWS ALB

Typical architecture:

```text
User
  ↓ HTTPS :443
Route 53
  ↓
ALB
  ↓
Target Group
  ↓
EC2 / ECS
```

The ALB can have an HTTPS listener and use an AWS Certificate Manager (ACM) certificate.

---

# 18. HTTP vs HTTPS

| HTTP                                 | HTTPS                             |
| ------------------------------------ | --------------------------------- |
| No TLS protection                    | Uses TLS                          |
| Data not encrypted by HTTP           | Data protected by TLS             |
| No server authentication through TLS | Server certificate authentication |
| Port 80 commonly used                | Port 443 commonly used            |
| Less secure                          | Secure                            |

---

# 19. HTTPS vs TLS

They are not exactly the same thing.

```text
HTTPS
  ↓
HTTP
  +
TLS
```

**HTTPS** is HTTP communicated over TLS.

**TLS** is the security protocol providing encryption, authentication, and integrity.

---

# 20. What Happens if Certificate Expires?

If the certificate is expired, invalid, or doesn't match the requested hostname, the browser can show a security warning.

Example:

```text
Certificate expired
        ↓
Browser detects problem
        ↓
Security warning
```

---

# 21. Interview Questions

### Q1. What is HTTPS?

> HTTPS is HTTP running over TLS. It provides encryption, authentication, and integrity for communication between the client and server.

### Q2. How does HTTPS work?

> The client resolves the domain using DNS, establishes the transport connection, performs a TLS handshake, validates the server certificate, establishes session keys, and then exchanges HTTP data through the encrypted TLS connec
