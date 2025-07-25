---
layout: post
title: "Solving Base-p-"
description: "A Write-Up on Huntress' Base-p- Challenge"
date: 2024-11-2
categories: [CTF, writeups]
tags: [CyberSecurity, Huntress, CTF, Write-Up]
comments: false
---


## Summary
Base-p- is [Huntress's](https://huntress.com/) 23rd challenge in their 2024 CTF, written by our absolutely beloved, [Izzy Spering](https://linktr.ee/Busyizzytech). Base-p- was classed as a `Miscellaneous` challenge featuring multiple steps of engagement.

## Starting Off
Looking at Base-p- at a first glance there's two hints we can get from this: `-p-` and `Base`, the former I'll explain in just a bit if you haven't gotten it just yet, but the latter gets hinted from the name and the Challenge's own description:

<blockquote style="font-style: italic; color: lightgreen; border-left: 2px solid #ccc; padding-left: 10px; margin: 20px 0;">
    That looks like a strange encoding. I wonder what it’s based on.
</blockquote>

From that we can tell it's using a `Base-` encoding with a potential prime number, right? Well not *exactly*, it *does* use a composite number; it's the value of an unsigned 16-bit binary (and also the smallest number with exactly 17 divisors, how neat is that!), but I didn't realise this, or the aforementioned hint from earlier.

<br>

### Stage One: The Unholy Script
Now anyone reading this would be screaming going "The answer is right there!!" And you'd be right, it *is* right there, but this wasn't my first thought, if anything I thought this would be some weird rotational encoding so I started off with this:


Now if any of you *don't* know what's going on here, I essentially checked the heuristics of the encoding to try and find patterns in the code points...you can guess how that went lol.

Following this, I opted for a more...extravegent approach: Prime Number bruteforcing, cause `p` has to mean `prime`, right?...right? (Hint: It's not!) Which lead to some even more chaotic mess:

```python
def sieve_of_eratosthenes(limit):
    sieve = [True] * (limit + 1)
    sieve[0] = sieve[1] = False
    for i in range(2, int(math.sqrt(limit)) + 1):
        if sieve[i]:
            for j in range(i * i, limit + 1, i):
                sieve[j] = False
    return [i for i in range(limit + 1) if sieve[i]]

def decode_prime_base(encoded_text, base):
    decoded = 0
    for char in encoded_text:
        # Treat each character as its ASCII value
        decoded = decoded * base + ord(char)
    return decoded

def int_to_bytes(x: int) -> bytes:
    return x.to_bytes((x.bit_length() + 7) // 8, byteorder='little')

def try_decode(encoded_text, base):
    try:
        decoded_int = decode_prime_base(encoded_text, base)
        decoded_bytes = int_to_bytes(decoded_int)
        decoded_text = decoded_bytes.decode('ascii', errors='ignore')
        return decoded_text
    except Exception as e:
        return f"Failed to decode with base {base}: {str(e)}"
        
# Try decoding with different prime bases
prime_bases = sieve_of_eratosthenes(256) # 256 is just an example

all_decoded = ""

# Concatenate all decoded strings
for base in prime_bases:
    decoded = try_decode(encoded_text, base)
    if decoded:
        all_decoded += f"Base {base}:\n{decoded}\n\n"  # Add base number for clarity

# Write the concatenated output to a file
with open("all_decoded_output.txt", "w", encoding="utf-8") as f:
    f.write(all_decoded)
```

As you're beginning to see, if you didn't already, I went WAYY off base (thanks Izz for the amazing pun!) to the point I was just wasting compute and time. After trying multiple different variations of primes and composites, I finally decided to ask Doctor Google the ultimate question: "Chinese character encoding CTF." This one query should have been my first thought, but alas, I'm not built like that. After about 2 (two) seconds of looking, I found [this repo](https://github.com/OlivierLaflamme/CTF-Script-And-Template-Thrift-Shop/blob/master/Chinese%20characters%20base65536.py) which pointed out the `-p-` value of the name: `65536`. But now we back track to earlier! The implicit hint of `-p-` is actually the command flag of `nmap` that specifies all ports from 1 to 65536.

After modifying the repo's code a little (Just a filename change), we're finally onto the next step! One's I absolutely didn't mess up this time! I see you disappointed in me smh.

<br />

### Stage 2: Inflated Base64
No! Get those thoughts out of your head! I don't mean *that* kinda inflation, we're talking about file/folder decompression/compression smh. Anyways, after breaking through the first stage, we can see the new output file has some new funky encodings, I almost instantly recognised this as Base64 and chucked it at a decoder, to which I got this new fun thing:
```python
b'\x1f\x8b\x08\x00m\x0e\x03g\x00\xff\xed\x90\xbdKzQ\x18\xc7\x8fE\xe6\x0b\x9a\xe2o0K\x0b\x02\x9d\xa2\xb2hJ+\xbd\xa9\x17\xc2\x97\x88\xde \xb4\x88\n\x1dLj\x90\x884q\t\xa5\xa2\xc9T\x8a\xb0M\x10\xbc\r\x0eA\x05\r)*\xd5\xd4\xa57\xa3\xe0.%\x05\xc9/\xb0!\xcf\xe9Ohi\xd0\xe1|\xbe\xcf\xf3\x9c\xf3=<|\xd7\x8dz-\x87%b\x01\x008\xb8\x0e\x1b\x02\xa0\xc6\t\xeb$\xa3\n\xf2>$!\xa00\x17t\xe3\x8b\x00\xb0\x05\xe8\xd0@0,\x84\xaf6p\xac\x7f\xd8\x19\xce\xdf\x1c\xe1\x98I\xa81i\xff\xa9\xd5>\x9e\xd4\xd4 \xef \x98\xec8\xdf\xc6\xdb\x0b\x04o\xdd\x8f/\x13g\xa7\xb3\xdc\xb9\xb1\xe8\xda\xb7\x8f{\x97\x9dlM\x13\xb5\xf0O#\xd9\xa4\x80\x02H\xb1\xaa\x1a\xca\x16_\x04\xe9\xf6_\xa2\xd9\x8c\x97\x0e\xd9\xdc\xd2\x85\x9a\xfa}\xda\xcf>\x1c\xc8>\xcd<\x1a\xc9\xcb\xd5BY\xd2\x9f\xb1X[b\xc5a\x8d>__\t\xa4\x1fK\t\x89bJn\x8b;\xd4\x17\xbd\xf6\x88l\x90*\xbc\xdb\x0b\xdeT\xcf\xea\xf2y\xf7\xa1\xb2(\x86\x97\x06s]D\x96\xa1\x9e\xa6\x0b\x9eT8w\xe2\xdfm\x0fu\xbe\x91\xf4c\x83Rc\x08d\xf2\xd9\xaf\xff\x9b\xc5dY\xc7Z\xb1\xfc\xa9e$M\xf8\x1fP\xf5\xcapUb\xfb\x95e\xc7\xb5}\xd0\xe8\xcb\x8d\x9a\xad\xa8\xc3\x07\xf4XLe\xf1\x94\x00\xb9\xe8\xb5\xa9\xdc\x05\x00\x00'
```
This is pretty much obviously a binary of some sorts, so I decided to chuck it into CyberChef and look at the entropy and scan for any embedded files or compression methods, none of which returned very useful, so I decided to wing it and assume some form compression since well...it's too small to be a usable file and the byte order looked similar to other tasks I've done in the past. This wasn't confirmed to be a compression until I had poke a friend who was familiar with this, and it was indeed a compression method: GZip to be specific.

<br />

### Stage 3: Colours!
With the encodings and decompressions out of the way, we can move onto the next step, figuring out the last step!
If all goes well with the last two steps, we should end up with the following binary:
```python
b"\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR\x00\x00\x05x\x00\x00\x00\xc8\x08\x02\x00\x00\x00\xdd\x9b\x1e\xb2\x00\x00\x00\tpHYs\x00\x00\x0b\x13\x00\x00\x0b\x13\x01\x00\x9a\x9c\x18\x00\x00\x05\x8eIDATx\x9c\xed\xda\xbbIDQ\x18FQG\x14CC\x8b\x10&Q\x1b21\xb2\t\x0b\xb1\x11k\x10\x9f\x98\x9a\xdb\x80\xe0\xebZ\xc3\xc0f\x0egX\xab\x81\xff\x8b\x0e\xdc\xcd]-\xcb\xb2\x07\x00\x00\x00P\xd8\x1f=\x00\x00\x00\x00\xd8\x1dB\x03\x00\x00\x00\x90\x11\x1a\x00\x00\x00\x80\x8c\xd0\x00\x00\x00\x00d\x84\x06\x00\x00\x00 #4\x00\x00\x00\x00\x19\xa1\x01\x00\x00\x00\xc8\x08\r\x00\x00\x00@Fh\x00\x00\x00\x002B\x03\x00\x00\x00\x90\x11\x1a\x00\x00\x00\x80\x8c\xd0\x00\x00\x00\x00d\x84\x06\x00\x00\x00 #4\x00\x00\x00\x00\x19\xa1\x01\x00\x00\x00\xc8\x08\r\x00\x00\x00@Fh\x00\x00\x00\x002B\x03\x00\x00\x00\x90\x11\x1a\x00\x00\x00\x80\x8c\xd0\x00\x00\x00\x00d\x84\x06\x00\x00\x00 #4\x00\x00\x00\x00\x19\xa1\x01\x00\x00\x00\xc8\x08\r\x00\x00\x00@Fh\x00\x00\x00\x002B\x03\x00\x00\x00\x90\x11\x1a\x00\x00\x00\x80\x8c\xd0\x00\x00\x00\x00d\x84\x06\x00\x00\x00 #4\x00\x00\x00\x00\x19\xa1\x01\x00\x00\x00\xc8\x08\r\x00\x00\x00@Fh\x00\x00\x00\x002B\x03\x00\x00\x00\x90\x11\x1a\x00\x00\x00\x80\x8c\xd0\x00\x00\x00\x00d\x84\x06\x00\x00\x00 #4\x00\x00\x00\x00\x19\xa1\x01\x00\x00\x00\xc8\x08\r\x00\x00\x00@Fh\x00\x00\x00\x002B\x03\x00\x00\x00\x90\x11\x1a\x00\x00\x00\x80\x8c\xd0\x00\x00\x00\x00d\x84\x06\x00\x00\x00 #4\x00\x00\x00\x00\x19\xa1\x01\x00\x00\x00\xc8\x08\r\x00\x00\x00@\xe6`\xcb\xf7\xae\xae/\xb7|qj\xab\xe3\xd7\xd1\x13&....\x00IEND\xaeB`\x82" # Shortened for space reasons
```

We can tell instantly that this is a PNG file from the header data: `PNG`, `IHDR`, and `IDAT`. So we can just quickly write this out to a file, and open it. Once it's opened you get a nice present, a pretty array of colors! Now this stage is pretty basic, there's nothing in the binary data that could hint to anything embedded within it, so we can safely throw this at a color picker of your choice, to get the hex value of each segment. The reason for this, is that the flags for this CTF are in MD5, which uses a hexadecimal format, so we can safely assume that the collected hex values (see below) should construct the flag.
<br>

`#666C61`, `#677B35`, `#383663`, `#663863`, `#383439`, `#633937`, `#333065`, `#613762`, `#323131`, `#326666`, `#663339`, `#666636`, `#617D20`

<br>

Now that we have all the hexcodes extracted, we can safely drop the `#` from each one, convert each segment into ASCII which if done correctly should produce this:

<ul style="list-style-type: none; padding: 0;">
    <li style="margin: 5px 0; color: #0056b3;"><strong>666C61:</strong> <span style="color: #ff7f50;">fla</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>677B35:</strong> <span style="color: #ff7f50;">g{5</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>383663:</strong> <span style="color: #ff7f50;">86c</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>663863:</strong> <span style="color: #ff7f50;">f8c</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>383439:</strong> <span style="color: #ff7f50;">849</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>633937:</strong> <span style="color: #ff7f50;">c97</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>333065:</strong> <span style="color: #ff7f50;">30e</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>613762:</strong> <span style="color: #ff7f50;">a7b</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>323131:</strong> <span style="color: #ff7f50;">211</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>326666:</strong> <span style="color: #ff7f50;">2f6</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>663339:</strong> <span style="color: #ff7f50;">f39</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>666636:</strong> <span style="color: #ff7f50;">f66</span></li>
    <li style="margin: 5px 0; color: #0056b3;"><strong>617D20:</strong> <span style="color: #ff7f50;">a}</span></li>
</ul>


It should be obvious what to do next from here: We just need to construct the ASCII all into one line (With no spaces!) which will give you the flag!

<span>
    <code>flag{586c8cf849849c97c30ea7b2112f6f39f66a}</code>
</span>
