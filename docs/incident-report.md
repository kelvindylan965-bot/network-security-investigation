# Incident Investigation Report

## 1. Summary

I investigated a sample network capture using Wireshark to understand the communication between devices and identify activities that needed further checking.

I observed an SMB request to enumerate network shares and DNS responses that returned the same IP address for two Windows-related hostnames.

## 2. Evidence Collected

### SMB activity

- Client: `10.42.10.21`
- Server: `10.42.20.40`
- Request frame: 165
- Response frame: 172
- Operation: `NetShareEnumAll`
- Result: `WERR_OK`
- Share entries returned: 0

### DNS activity

- Client: `10.42.10.21`
- DNS server: `10.42.20.53`
- Hostnames: `www.msftconnecttest.com` and `download.windowsupdate.com`
- Returned IP address: `198.18.42.52`
- Example frames: 1, 2, 1104, and 1105

The DNS response in frame 1105 showed `No error` and a response time of 5.366 milliseconds.

### HTTP connectivity-check activity

- Client: `10.42.10.21`
- Server: `198.18.42.52`
- Request frame: 8
- Response frame: 12
- Request: `GET /connecttest.txt HTTP/1.1`
- Response: `HTTP/1.1 200 OK`
- Response body: `Microsoft Connect Test`

This exchange is consistent with a Windows connectivity check. It provides additional context for the DNS observations but does not establish that the network was compromised.

**Evidence screenshot:** [`http-connect-test.png`](../evidence/screenshots/http-connect-test.png)

## 3. Assessment

The SMB traffic shows that the client requested network-share information, but the response contained zero entries.

The DNS traffic shows that two Windows-related hostnames resolved to the same address in the packets I examined. That address belongs to a range reserved for network benchmarking.

These observations may be worth investigating further, but they do not prove that an attack or compromise occurred.

## 4. Actions Recommended

If I were investigating this on a real network, I would:

1. Confirm whether the DNS behavior was expected in that environment.
2. Review the DNS server configuration and relevant network logs.
3. Investigate the purpose of the SMB share-enumeration request.
4. Collect more evidence before deciding whether the activity was suspicious.
5. Document any confirmed issue and the steps taken to resolve it.

## 5. Limitations

This investigation used a public sample capture. I did not have access to the original network configuration, device owners, or other operational records.

My conclusions are limited to the packets examined. I cannot confirm that a security incident occurred.

## 6. Conclusion

This exercise helped me practise using Wireshark display filters, examining request-and-response pairs, interpreting packet details, and documenting findings.

The investigation identified network activity that deserved closer examination, but more evidence would be needed to reach a firm conclusion.
