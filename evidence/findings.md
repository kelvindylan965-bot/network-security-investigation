# Network Security Investigation Findings

## Introduction

I used Wireshark to investigate a sample network capture containing 1,592 packets. My aim was to understand the communication between devices and identify activities that might need further investigation.

This was a sample capture, so my findings are based only on the packets I observed.

## Finding 1: SMB Share Enumeration

I found a request from client `10.42.10.21` to server `10.42.20.40` asking for information about the network shares available on the server.

- Request frame: 165
- Response frame: 172
- Operation: `NetShareEnumAll`
- Result: `WERR_OK`
- Total entries returned: 0

The server returned a successful status, but the response showed zero share entries. This means I did not see any share names returned in this response.

Requesting information about network shares is not automatically suspicious. More information would be needed to decide whether the activity was normal or not.

**Evidence screenshot:** [`smb-share-enumeration.png`](screenshots/smb-share-enumeration.png)


## Finding 2: Windows Connectivity Check

I examined DNS frames 1 and 2 and found a query for `www.msftconnecttest.com`.

- Client: `10.42.10.21`
- DNS server: `10.42.20.53`
- Query type: A record
- Returned IP address: `198.18.42.52`

This hostname is associated with Windows internet connectivity checks. The DNS server returned an address, showing that the query received a response.

However, the returned address belongs to a range reserved for network benchmarking. I cannot tell from this packet alone why the DNS server returned that address.

**Evidence screenshot:** [`dns-response-analysis.png`](screenshots/dns-response-analysis.png)


## Finding 3: HTTP Connectivity Check

I examined the HTTP exchange associated with the Windows connectivity-check activity.

- Request frame: 8
- Response frame: 12
- Client: `10.42.10.21`
- Server: `198.18.42.52`
- Request: `GET /connecttest.txt HTTP/1.1`
- Response: `HTTP/1.1 200 OK`
- Response body: `Microsoft Connect Test`

The response is consistent with a Windows internet connectivity check. It confirms that the server returned the expected text in this sample, but it does not by itself establish whether the wider network environment was healthy or malicious.

**Evidence screenshot:** [`http-connect-test.png`](screenshots/http-connect-test.png)

## Finding 4: Windows Update DNS Query

I also found repeated DNS queries for `download.windowsupdate.com`.

For one example:

- Request frame: 1104
- Response frame: 1105
- Client: `10.42.10.21`
- DNS server: `10.42.20.53`
- Returned IP address: `198.18.42.52`
- DNS response code: `No error`
- Response time: 5.366 milliseconds

I noticed that this hostname and `www.msftconnecttest.com` both resolved to the same IP address in the packets I checked.

The hostname is associated with Windows Update, but the DNS response does not prove that an update was downloaded or installed. The shared IP address is worth investigating further, although it does not by itself prove that anything malicious happened.

I also noticed some DNS responses involving `10.42.10.22`. I kept this device separate from `10.42.10.21` because they have different IP addresses.

## Conclusion

During my investigation, I observed an SMB request to enumerate network shares and DNS queries related to Windows connectivity checks and Windows Update.

The SMB response reported zero share entries. The DNS server returned the same benchmarking-reserved IP address for two different Windows-associated hostnames.

These observations helped me understand how to use Wireshark to inspect network activity and record evidence. However, I cannot conclude that the devices were compromised based on these findings alone. More evidence and information about the sample environment would be needed.
