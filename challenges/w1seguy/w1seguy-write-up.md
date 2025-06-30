# TryHackMe – [Room Name]

> 🚫 This write-up follows TryHackMe’s guidelines. No flags, credentials, or direct answers are shared.

_A walkthrough of the [W1seGuy](https://tryhackme.com/room/w1seguy) challenge._

<br>

## 📚 Table of Contents
- [🧠 Overview](#-overview)
- [📡 Target Information](#-target-information)
- [🧠 Lessons Learned](#-lessons-learned)
- [📚 Resources](#-resources)

<br>

## 🧠 Overview
- **Category:** Crypto
- **Difficulty:** Easy
- **Date Completed:** 2025-06-30
- **Tools Used:** `netcat`, `cyberchef`

<br>

## 📡 Target Information
| Detail         | Value          |
|----------------|----------------|
| IP Address     | `10.10.135.18`    |
| OS             | Linux |
| Notable Ports  | `1337` |

<br>

## ✅ Task 1 Source Code

Have a look at the given source code before moving onto task 2.

The given source code can be downloaded by clicking the "Download Task Files".

**Source Code**
``` python
import random
import socketserver 
import socket, os
import string

flag = open('flag.txt','r').read().strip()

def send_message(server, message):
    enc = message.encode()
    server.send(enc)

def setup(server, key):
    flag = 'THM{thisisafakeflag}' 
    xored = ""

    for i in range(0,len(flag)):
        xored += chr(ord(flag[i]) ^ ord(key[i%len(key)]))

    hex_encoded = xored.encode().hex()
    return hex_encoded

def start(server):                                                                                                                                         
    res = ''.join(random.choices(string.ascii_letters + string.digits, k=5))                                                                               
    key = str(res)                                                                                                                                         
    hex_encoded = setup(server, key)
    send_message(server, "This XOR encoded text has flag 1: " + hex_encoded + "\n")
    
    send_message(server,"What is the encryption key? ")
    key_answer = server.recv(4096).decode().strip()

    try:
        if key_answer == key:
            send_message(server, "Congrats! That is the correct key! Here is flag 2: " + flag + "\n")
            server.close()
        else:
            send_message(server, 'Close but no cigar' + "\n")
            server.close()
    except:
        send_message(server, "Something went wrong. Please try again. :)\n")
        server.close()

class RequestHandler(socketserver.BaseRequestHandler):
    def handle(self):
        start(self.request)

if __name__ == '__main__':
    socketserver.ThreadingTCPServer.allow_reuse_address = True
    server = socketserver.ThreadingTCPServer(('0.0.0.0', 1337), RequestHandler)
    server.serve_forever()

```

## ✅ Task 2 Get Those Flags!

Your friend told me you were wise, but I don't believe them. Can you prove me wrong?

When you are ready, click the Start Machine button to fire up the Virtual Machine. Please allow 3-5 minutes for the VM to start fully.

The server is listening on port 1337 via TCP. You can connect to it using Netcat or any other tool you prefer.

### 🚩 First Flag

After reading the second task description, it explicitly says that the server is listening on port 1337 via TCP. Let's connect to it using netcat like they recommended.

```bash
nc 10.10.135.18 1337
```
The output that was given:

```
This XOR encoded text has flag 1: 19097d111b7c205c041f0839442b1f39755301080c2f42590a210d49023e3f35495a1e3f397f1816
What is the encryption key? 
```
It seems to be waiting for some sort of input. It's asking for an encryption key.

After testing some input, here is what I received:

```
What is the encryption key? 1
Close but no cigar
```
```
What is the encryption key? 2
No way you got it! Here is your flag THM{Try_Again} :)
```
The THM{Try_Again} is a false flag.

I attempted various random inputs and received the same outputs.

Since the flags are encoded, we can attempt to decode them by using some tools.

A notable tool to use in this case is [CyberChef](https://gchq.github.io/CyberChef/). Let's use this tool.

Given that most flags in TryHackMe start with THM{, we can get those first 4 bytes using CyberChef conversion.

I took the first 4 bytes of the given encoded XOR (19097d11) and put them into the 'Key' field. I then wrote "THM{" in the input box.

> Please note that these screenshots were pulled from another source.

![Cyber Chef Conversion](https://miro.medium.com/v2/resize:fit:720/format:webp/1*rm3gSM-nbghhKSz6TuYm_g.png)

Next, we will use the “From Hex and XOR operation” in CyberChef, incorporating the output key.

Since the source code specifies that the key length is 5 characters, the last character could be any letter or digit. Therefore, you can manually try all possible combinations of letters and digits for the final character.

![Cyber Chef Conversion](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Gm3nA7GrL3gFMCM39wIUhQ.png)

**Note:** Make sure to switch your XOR from Hex to UTF8.

After brute forcing the last digit in the key field, I was able to discover the 1st flag. Every character (including both lowercase and uppercase) and number was inserted and changed out until the flag made sense.

### 🚩 Second Flag

In order to retrieve the second flag, we would have to take our XOR key, which in my case would be `oxrtd`, and input that into the "What is the encryption key?" prompt. After doing so, we retrieved the second flag.

```
What is the encryption key? oxrtd
Congrats! That is the correct key! Here is flag 2: ..."
```

## 🧠 Lessons Learned

- XOR encryption with a repeating key is predictable when you know part of the plaintext (e.g., THM{) — this makes known-plaintext attacks very effective.

- CyberChef is an incredibly useful tool for quick cryptographic analysis, especially when combining hex decoding and XOR operations.

- Understanding the source code logic before attacking the challenge makes the process faster — reading the server-side logic revealed that the key was 5 characters and used ASCII letters/digits.

- Brute forcing one unknown character in a short key (only 62 possibilities for the 5th char) is totally feasible manually or with a simple script.

- It's critical to verify "false flags" — challenges may use decoys like THM{Try_Again} to test if you’re blindly accepting the first output you see.

- Always test assumptions: I initially assumed the key would be random, but the deterministic structure (5-character ASCII) significantly reduced complexity.

<br>

## 📚 Resources

- [XOR Cipher Overview](https://en.wikipedia.org/wiki/XOR_cipher)
- [CyberChef](https://gchq.github.io/CyberChef/)
- [TryHackMe](https://tryhackme.com)
