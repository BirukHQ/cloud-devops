
# Week 2: Networking & IAM Fundamentals

## Task 1: Identify Network Information

### Objective
Identify the computer's network configuration, including its
local IPv4 address, subnet mask, default gateway, and IP type.

### Commands Used
- `ip -br addr`
- `ip route`
- `ipcalc 192.168.1.7/24`

### Network Information

| Item | Value |
|---|---|
| Operating System | Omarchy Linux |
| Network Interface | wlp2s0 |
| Local IPv4 Address | 192.168.1.7 |
| Subnet Mask | 255.255.255.0 |
| CIDR Prefix | /24 |
| Network Address | 192.168.1.0 |
| Default Gateway | 192.168.1.1 |
| Broadcast Address | 192.168.1.255 |
| Private or Public | Private |

### Explanation

My local IPv4 address is 192.168.1.7, which belongs to
the private IP address range 192.168.0.0/16.

The subnet mask is 255.255.255.0, equivalent to /24.
This means that the first 24 bits identify the network,
while the remaining 8 bits are available for host addresses.

The network address is 192.168.1.0, and the broadcast
address is 192.168.1.255.

The ipcalc command shows that this network contains
256 total addresses, with 254 conventional usable
host addresses.

My default gateway is 192.168.1.1. It allows my computer
to communicate with destinations outside my local network.


# Task 2: Practice Subnetting

## Objective
Divide the network 10.0.0.0/24 into four equal subnets.

## Calculations

Starting network: 10.0.0.0/24

1. IPv4 contains 32 bits.
2. Host bits = 32 - 24 = 8.
3. Total addresses = 2^8 = 256.
4. Four subnets require 2 additional network bits.
5. New prefix = /24 + 2 = /26.
6. New subnet mask = 255.255.255.192.
7. Addresses per subnet = 256 / 4 = 64.
8. Conventional usable hosts = 64 - 2 = 62.
9. AWS usable hosts = 64 - 5 = 59.

## Subnet Results

| Subnet | CIDR | Network | Usable Range | Broadcast |
|---|---|---|---|---|
| 1 | 10.0.0.0/26 | 10.0.0.0 | 10.0.0.1 - 10.0.0.62 | 10.0.0.63 |
| 2 | 10.0.0.64/26 | 10.0.0.64 | 10.0.0.65 - 10.0.0.126 | 10.0.0.127 |
| 3 | 10.0.0.128/26 | 10.0.0.128 | 10.0.0.129 - 10.0.0.190 | 10.0.0.191 |
| 4 | 10.0.0.192/26 | 10.0.0.192 | 10.0.0.193 - 10.0.0.254 | 10.0.0.255 |

## Explanation

The original /24 network contains 256 IP addresses.

By borrowing two host bits, I created four equal /26 subnets.

Each subnet contains 64 total addresses and 62 conventionally usable host addresses.

AWS reserves five addresses in each VPC subnet, leaving 59 usable addresses.

## Verification

I used the Linux ipcalc command to verify all four subnet calculations.

The four subnets cover the original 10.0.0.0/24 network without overlap.

## Task 3: Test DNS Resolution

### Objective
Test DNS resolution using nslookup and dig.

### Commands Used
- `nslookup example.com`
- `dig example.com`
- `dig A example.com`
- `dig AAAA example.com`

### Results


### Results

| Item | Result |
|---|---|
| Domain tested | example.com |
| DNS resolution | Successful |
| DNS resolver | 127.0.0.53 |
| IPv4 address | 172.66.147.243, 104.20.23.154 |
| IPv6 address | 2606:4700:10::ac42:93f3, 2606:4700:10::6814:179a |

### Explanation

DNS (Domain Name System) translates human-readable
domain names into IP addresses.

For example, when I request example.com, my computer
uses a DNS resolver to obtain the IP address associated
with that domain.

The A record provides IPv4 addresses, while the AAAA
record provides IPv6 addresses.

DNS resolution is important because computers need
IP addresses to establish network connections.

A successful DNS lookup does not guarantee that the
website itself is reachable.

## Task 4: Inspect an HTTPS Website

### Objective
Inspect an HTTPS website and understand its HTTP
response, TLS connection, and certificate.

### Website Tested
https://example.com

### Commands Used
- `xdg-open https://example.com`
- `curl -I https://example.com`
- `curl -v https://example.com`
- `openssl s_client -connect example.com:443 -servername example.com`

### Results

| Item | Result |
|---|---|
| Website | example.com |
| HTTP status code | 200 OK |
| Protocol | HTTP/2 |
| Content type | text/html; charset=utf-8 |
| Server | Cloudflare |
| Cache status | HIT |
| Certificate subject | CN=example.com |
| Certificate issuer | Cloudflare TLS Issuing ECC CA 3 |
| Certificate start | September 26, 2026 |
| Certificate expiry | December 25, 2026 |

### Explanation

HTTPS is HTTP running over TLS.

TLS provides encryption, integrity, and authentication
between the client and the web server.

The curl -I command sends a HEAD request and displays
the HTTP response headers without downloading the body.

The server returned HTTP/2 200, meaning the request
was successfully processed.

I used OpenSSL to retrieve the TLS certificate details.
The certificate identifies example.com and was issued
by Cloudflare TLS Issuing ECC CA 3.

DNS resolution alone does not guarantee that a website
will load. Network connectivity, TLS negotiation,
and the HTTP server response must also work correctly.



## Task 5: HTTP Status Codes

I used curl to inspect HTTP responses and understand common HTTP status codes.

### Test Results

| Test | Actual Status Code | Meaning |
|---|---|---|
| HTTPS example.com | 200 | OK |
| HTTP example.com | 200 | OK |
| httpbin 403 endpoint | 403 | Forbidden |
| httpbin 404 endpoint | 404 | Not Found |
| httpbin 500 endpoint | 500 | Internal Server Error |

### Explanation

- 200: The request was successful.
- 301: The requested resource has permanently moved to another URL.
- 403: The server understood the request but refused access.
- 404: The requested resource could not be found.
- 500: The server encountered an internal error.

Note: My HTTP example.com test returned 200 rather than 301.
This demonstrates that HTTP does not always redirect to HTTPS.

### What I Learned

HTTP status codes help diagnose whether a request succeeded,
was redirected, was refused, or failed on the server.

## Task 6: AWS IAM Policy Fundamentals

### Objective

Create and validate an AWS IAM JSON policy that follows the principle of least privilege.

### Policy Created

The file `s3-read-policy.json` contains the following policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

### Policy Explanation

- **Version:** Specifies the IAM policy language version.
- **Effect:** Allow means the specified action is permitted.
- **Action:** `s3:GetObject` allows reading or downloading objects.
- **Resource:** Specifies the S3 objects inside `example-bucket`.

### Permissions and Restrictions

This policy allows users to retrieve objects from the specified S3 bucket.

It does not grant permission to:
- Upload objects.
- Delete objects.
- List all buckets.
- Modify bucket permissions.

The `s3:ListBucket` permission is separate from `s3:GetObject`.

### Validation

The JSON policy was validated locally using:

```bash
python -m json.tool s3-read-policy.json
```

### What I Learned

- IAM controls access to AWS resources.
- IAM policies define which actions are allowed or denied.
- The principle of least privilege limits unnecessary access.
- S3 bucket permissions and object permissions use different resource ARNs.

**Note:** This policy was created and validated locally. It was not attached to a live AWS IAM identity.


## Task 7: Network Troubleshooting Fundamentals

### Objective

Practice a systematic approach to troubleshooting common networking and cloud infrastructure problems. The task covers network connectivity, DNS resolution, TLS/HTTPS communication, HTTP responses, and AWS IAM permissions.

### 1. Network Connectivity Troubleshooting

**Commands used:**

```bash
ip -br -4 addr
ip route
ping -c 4 192.168.1.1
ping -c 4 1.1.1.1
```

**Results:**

- Network interface: `wlp2s0`
- Local IPv4 address: `192.168.1.7/24`
- Default gateway: `192.168.1.1`
- Gateway connectivity: Successful
- External IP connectivity to `1.1.1.1`: Successful
- Packet loss: 0% for both ping tests.

**Conclusion:**

The network interface was active, the default gateway was correctly configured, and external IP connectivity was available.

### 2. DNS Troubleshooting

**Commands used:**

```bash
nslookup example.com
dig example.com
dig A example.com
dig AAAA example.com
```

**Results:**

- DNS resolver: `127.0.0.53`
- DNS query status: `NOERROR`
- IPv4 addresses returned:
  - `172.66.147.243`
  - `104.20.23.154`
- IPv6 addresses returned:
  - `2606:4700:10::ac42:93f3`
  - `2606:4700:10::6814:179a`

**Conclusion:**

DNS resolution was successful. Both IPv4 (A) and IPv6 (AAAA) records were returned for `example.com`.

### 3. TLS and HTTPS Troubleshooting

**Command used:**

```bash
curl -Iv https://example.com
```

This command is used to inspect the HTTPS connection, TLS negotiation, certificate verification, and HTTP response headers.

**Purpose:**

- Check whether an HTTPS connection can be established.
- Inspect TLS handshake information.
- Check certificate verification.
- Identify connection or certificate-related errors.

### 4. HTTP Application Troubleshooting

**Commands used:**

```bash
curl -I https://example.com
curl -I http://example.com
curl -s -o /dev/null -w '%{http_code}\n' https://httpbin.org/status/500
```

Additional HTTP status code tests were performed in Task 5.

**Observed results:**

- HTTPS request: `200 OK`
- HTTP request: `200 OK`
- Simulated server error: `500 Internal Server Error`

**Conclusion:**

The HTTP responses demonstrated that successful network connectivity does not necessarily mean an application is functioning correctly. HTTP status codes help identify application-level problems.

### 5. AWS IAM Troubleshooting

**Command used:**

```bash
python -m json.tool s3-read-policy.json
```

The IAM policy was reviewed to verify its JSON structure, permissions, and resource scope.

**Policy reviewed:**

- Effect: `Allow`
- Action: `s3:GetObject`
- Resource: `arn:aws:s3:::example-bucket/*`

**Conclusion:**

The policy follows the principle of least privilege by allowing object retrieval from a specific S3 bucket without granting unnecessary permissions.

The policy was created and validated locally. It was not attached to a live AWS IAM identity.

### 6. Overall Troubleshooting Approach

This task demonstrated a systematic approach to diagnosing infrastructure problems:

1. Check network interfaces and IP configuration.
2. Verify connectivity to the default gateway and external IP addresses.
3. Check DNS resolution.
4. Inspect HTTPS connections and TLS certificates.
5. Examine HTTP response codes to identify application errors.
6. Review IAM policies to identify possible authorization problems.

### What I Learned

- How to inspect network interfaces and routing tables in Linux.
- How to test local and external network connectivity.
- How DNS translates domain names into IP addresses.
- The difference between IPv4 A records and IPv6 AAAA records.
- How to investigate HTTPS and TLS connections.
- How HTTP status codes help identify application-level errors.
- How IAM policies control access to AWS resources.
- Why troubleshooting should be performed systematically, layer by layer.

