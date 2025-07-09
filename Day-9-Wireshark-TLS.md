# **Day 9: Wireshark Basics – TLS Protocol Analysis**

---

## **Objective**

Today, my goal is to understand how Wireshark can be used to analyze TLS traffic and identify key parts of the TLS handshake. I’ll examine packet fields like Client Hello, Server Hello, and Certificates to spot TLS versions, cipher suites, and certificate metadata—critical details a SOC Analyst uses to detect outdated encryption, suspicious certificates, or signs of encrypted traffic abuse.

---

## **Lab Setup**

### **System Requirements**

* **Operating System:** Windows 10/11 (or Linux/macOS)
* **Software:** Wireshark (latest version)

---

## **TLS Packet Structure and Fields**

TLS is a **cryptographic protocol** that provides secure communication over the internet. It runs over **TCP**, commonly on port **443**, and is used in HTTPS, FTPS, SMTPS, etc.

### **Key TLS Handshake Messages:**

| Message Type     | Description                                              |
| ---------------- | -------------------------------------------------------- |
| **Client Hello** | Client initiates secure connection, offers cipher suites |
| **Server Hello** | Server selects cipher and provides certificate           |
| **Certificate**  | Server provides digital certificate (X.509)              |
| **Key Exchange** | Client and server exchange keys for session              |
| **Finished**     | Secure session begins                                    |

---

## **Most Common TLS Display Filters**

Use these filters in Wireshark’s **Display Filter** bar:

| Filter                         | Description           |
| ------------------------------ | --------------------- |
| `tls`                          | Show all TLS traffic  |
| `tcp.port == 443`              | TLS over HTTPS        |
| `tls.handshake.type == 1`      | Client Hello messages |
| `tls.handshake.type == 2`      | Server Hello messages |
| `tls.record.version == 0x0303` | TLS 1.2 traffic       |
| `tls.record.version == 0x0304` | TLS 1.3 traffic       |

---

## **TLS Security Risks**

Understanding weak cipher suites and outdated protocols is essential for identifying insecure communications:

###  **Examples of Weak Cipher Suites**

* NULL-MD5
* EXP-RC4-MD5
* DES-CBC-SHA
* RC4-SHA
* AES128-SHA
* DHE-RSA-AES128-SHA
* ECDHE-RSA-RC4-SHA
* ECDHE-RSA-AES256-SHA

> These cipher suites are considered weak due to lack of encryption (NULL), short key lengths, or vulnerable algorithms like RC4 and DES.

### **Weak TLS/SSL Protocols**

* SSLv2
* SSLv3
* TLSv1.0
* TLSv1.1

> These versions are deprecated and vulnerable to attacks like POODLE and BEAST.

###  **Example of Insecure SSL Configuration (Nginx)**

```nginx
ssl_protocols SSLv3 TLSv1 TLSv1.1;
ssl_ciphers NULL-MD5:RC4-MD5:DES-CBC-SHA:AES128-SHA:AES256-SHA;
ssl_prefer_server_ciphers on;
```

> This config allows outdated protocols and weak ciphers, making encrypted traffic susceptible to interception or downgrade attacks.

---

## **Conclusion**

* TLS secures communication using encryption, making traffic unreadable without keys.

* Wireshark can't decrypt TLS by default but can reveal:

  * Server name (SNI)
  * Certificate chain
  * TLS versions and cipher suites

* Analyzing TLS metadata helps detect:

  * Outdated TLS versions (e.g., TLS 1.0)
  * Suspicious or self-signed certificates
  * Malicious domain encryption abuse
  * Use of weak or deprecated ciphers

