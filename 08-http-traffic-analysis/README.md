# Lab 9 – HTTP Traffic Analysis

## Objective
Capture a plain HTTP request/response and see what's visible when
traffic isn't encrypted.

## Setup
- Kali: eth1 10.10.10.1, running a simple web server
```bash
   python3 -m http.server 8080
```
- Windows 10 client: 10.10.10.10, browsed to http://10.10.10.1:8080

## Wireshark Capture
Captured on Kali's eth1 interface, filter: `http`

**Request (packet 15):**
    GET / HTTP/1.1
    Host: 10.10.10.1:8080
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) ... Chrome/151.0.0.0
    Accept-Language: en-GB,en;q=0.9,en-US;q=0.8

**Response (packet 22):**
    HTTP/1.0 200 OK
    Server: SimpleHTTP/0.6 Python/3.13.6
    Content-Type: text/html; charset=utf-8
    Content-Length: 2643

Full request/response viewed with Wireshark's Follow -> HTTP Stream,
showing the entire conversation, including the returned HTML, in plain
readable text.

## What I Learned
- GET / HTTP/1.1 = method, path, and protocol version, all in one line.
- The User-Agent header reveals exact browser/OS details -- useful in
  investigations to identify a device or spot a faked/suspicious client.
- The Server header reveals the exact web server software and version
  -- attackers actively scan for this to find outdated, vulnerable
  software.
- Status codes matter: 200 = success. Codes like 403/404/500 are often
  the first clue something's wrong during an investigation.
- Because none of this is encrypted, anyone capturing this traffic can
  read the full request, response, and headers in plain text. This is
  exactly the gap HTTPS closes -- covered next in Lab 10.

## Folder structure
lab-09-http-analysis/
├── README.md
└── screenshots/
    ├── http-stream.png
    ├── wireshark-request.png
    └── wireshark-response.png
