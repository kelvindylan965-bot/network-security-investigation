# Packet Analysis Notes

## Tools Used

I used Wireshark to open and examine the sample network capture. I used display filters to find specific types of network traffic without having to check every packet manually.

## 1. Checking SMB Traffic

I used the `smb2` display filter to find SMB communication.

I found a client requesting information about network shares from a server. I examined the request in frame 165 and its response in frame 172.

The response showed `WERR_OK`, but the total number of returned share entries was zero.

## 2. Checking DNS Traffic

I used DNS display filters to examine queries and responses.

I checked the query for `www.msftconnecttest.com` in frame 1 and its response in frame 2. I also checked the query for `download.windowsupdate.com` in frame 1104 and its response in frame 1105.

Both hostnames resolved to `198.18.42.52` in the packets I examined.

## 3. Matching DNS Queries and Responses

I used the DNS transaction ID to match a query with its response.

For example, frames 1104 and 1105 both used transaction ID `0x0cfa`. The response showed `No error` and arrived 5.366 milliseconds after the request.

## 4. Checking HTTP Connectivity Traffic

I used the `http` display filter to examine HTTP requests and responses.

In frame 8, client `10.42.10.21` requested `/connecttest.txt` from `198.18.42.52`. In frame 12, the server returned `HTTP/1.1 200 OK`.

The response body contained `Microsoft Connect Test`. This is consistent with a Windows internet connectivity check and connects the HTTP observation to the DNS findings.

**Evidence screenshot:** [`http-connect-test.png`](../evidence/screenshots/http-connect-test.png)

## 5. Important Lessons

- Display filters help narrow down traffic during an investigation.
- A request and its response should be examined together.
- Packet details can show the operation performed and the result returned.
- One unusual network event does not automatically mean an attack occurred.
- Findings should be based on observed evidence, and any uncertainty should be recorded.

## Limitation

My analysis is based on the packets available in this sample capture. I did not independently verify the purpose of every device or activity in the sample environment.
