# Network Security Investigation and Troubleshooting

## About This Project

This is a hands-on networking and security project in which I used Wireshark to investigate a sample network traffic capture.

I wanted to practise identifying network activity, examining packets, matching requests with responses, and documenting findings based on evidence.

## Tools and Technologies

- Wireshark — packet capture analysis
- DNS — examining hostname queries and responses
- SMB2 — investigating network-share requests
- Markdown — documenting findings and investigation steps

## What I Did

- Downloaded a public sample capture and verified its SHA-256 checksum against the publisher's checksum file.
- Opened the capture in Wireshark and examined its 1,592 packets.
- Investigated an SMB share-enumeration request and its response.
- Examined an HTTP connectivity-check request and verified the response body.
- Examined DNS traffic for Windows connectivity-check and Windows Update hostnames.
- Matched DNS queries and responses using transaction IDs.
- Investigated an unusual DNS result involving an IP address reserved for network benchmarking.
- Wrote findings, troubleshooting notes, and an incident investigation report.

## Key Findings

The SMB share-enumeration response reported zero returned entries.

Two Windows-related hostnames resolved to the same benchmarking-reserved IP address in the packets examined. This was recorded as an observation requiring further context, not proof of an attack.

## Project Files

- `captures/` — sample packet capture and checksum information
- `docs/packet-analysis.md` — notes on the analysis process
- `docs/troubleshooting-report.md` — troubleshooting scenario and recommended checks
- `docs/incident-report.md` — incident investigation summary
- `evidence/findings.md` — observations and interpretations
- `evidence/screenshots/` — Wireshark screenshots supporting SMB2, DNS, and HTTP findings
- `tests/validation-checklist.md` — project completion checklist

## Important Limitation

This project uses a public sample capture. The findings describe the packets examined and do not establish that a real security incident occurred.

## Current Status

The initial packet investigation, documentation, and screenshot evidence are complete. Documentation consistency and local file/link validation have been checked. Git initialization, commit, and GitHub publication remain.
