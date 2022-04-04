---
layout: post
title: Endianness and Byte Order
cover-img: /assets/img/Endianness.png
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [Endianness, Big-endian, Little-endian, Network byte order, Host byte order]
---

Computers only know 0s and 1s and the minimum unit is Byte, which is 8 bits. Endianness is how things are stored in memory or read by the computer.

Endianness is represented in two ways: *Big-endian* and *Little-endian*.

*Big-endian* means the highest byte of a multibyte data is stored first(at lower memory). For example, a 32-bit integer **3232235521** will be stored like this:

    Low memory    ------>   High memory
    11000000 10101000 00000000 00000001   

*Little-endian* means the lowest byte of a multibyte data is stored first(at lower memory). For example, a 32-bit integer **3232235521** will be stored like this:

    Low memory    ------>   High memory
    00000001 00000000 10101000 11000000  

Most PC architectures use *Little-endian* format, including most Windows PCs meaning when they read multibytes data, they will treat byte stored at lowest memory as the least significant byte. If a 32 bit integer is stored as follow,

    Low memory    ------>   High memory
    11000000 10101000 00000000 00000001

the output of it on a *Little-endian* machine will be **16820416**. 

Some IBM machines use *Big-endian* format, meaning they will treat byte stored at the lowest memory at the most significant byte. If a 32 bit integer is stores as follow

    Low memory    ------>   High memory
    11000000 10101000 00000000 00000001

the output of it on a *Little-endian* machine will be **3232235521**.

> Note: You can run a small piece of code to check the endianness your machine has:
```
    #include <stdio.h>
    int main() 
    {
        short int word = 0x0001;
        char *b = (char *)&word;
        printf("%s\n", b[0] ? "Little Endian" : "Big Endian");
        return 0;
    }
```

As you can see, there will be a problem when machines with different endianness communicate with each other. In networking, in order to solve the problem, the standard is to always use big-endianness when a multibyte data is about to leave the machine to the wire/network. We call it *network-byte-order*(big-endian), while anything stored on the machine is called *host-byte-order*(depending on what architecure the machine has, this can be either big-endian on big-endian machines or little-endian on little-endian machines).
    
As an example, an ipv4 address **192.168.0.1**, when travelled over the network, is stored as 32 bit integer in big-endian format:

    192      168      0        1
    Low memory ----------> High memory
    11000000 10101000 00000000 00000001
    
When the data arrives at a specific local machine, it will be interpreted differently.

On big-endian machines, which treat lowest byte as the most significant byte, the value will be the same. While on little-endian machines, which treat lowest byte as the most least significant byte, the value will be:

    00000001 00000000 10101000 11000000

which is **16820416** in decimal instead of **3232235521**.

It's a little bit more tricky for ipv6 addresses, which is a 128-bit data. The network byte order of ipv6 address: **2001:0db8:85a3:0000:0000:8a2e:0370:7334** will be:

    00100000 00000001 00001101 10111000 10000101 10100011 00000000 00000000 00000000 00000000 10001010 00101110 00000011 01110000 01110011 00110100

So on little-endian machines, certain action is needed to parse the data received from network into correct host byte order. 

For ipv4, we can use `ntohl()` to do the parsing in C++. Similarly, we also need to call `htonl()` on little endian machines to parse the data from host byte into network byte order before sending it to wire/network.

For ipv6, there is no built-in functions in C++ that can convert the byte orders. So you need to manually convert it using bit shifting machanism.
