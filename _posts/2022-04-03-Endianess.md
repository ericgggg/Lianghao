---
layout: post
title: Endianness and Byte Order
cover-img: /assets/img/Endianness.png
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [Endianness, Big-endian, Little-endian, Network byte order, Host byte order]
---

Computers only know 0s and 1s and the minimum unit is Byte, which is 8 bits. Endianness is how things are stored in memory or read by the computer.

Endianness is represented two ways: Big-endian and Little-endian:
    Big-endian mean the highest byte of a multibyte data is stored first(at lower memory). For example, a 32-bit integer 3232235521 will be stored like this:

        Low memory    ------>   High memory
        11000000 10101000 00000000 00000001   

    Little-endian means the lowest byte of a multibyte data is stored first(at lower memory). For example, a 32-bit integer 3232235521 will be stored like this:

        Low memory    ------>   High memory
        00000001 00000000 10101000 11000000  

Most PC architectures use Little-endian format, including most Windows PCs meaning when they read multibytes data, they will treat byte stored at lowest memory as the least significant byte. If a 32 bit integer is stored as follow,

    Low memory    ------>   High memory
    11000000 10101000 00000000 00000001
the output of it on a Little-endian machine will be 16820416. 

Some IBM machines use Big-endian format, meaning they will treat byte stored at the lowest memory at the most significant byte. If a 32 bit integer is stores as follow

    Low memory    ------>   High memory
    11000000 10101000 00000000 00000001
the output of it on a Little-endian machine will be 3232235521.

Note: You can run a small piece of code to check the endianness your machine has:

    #include <stdio.h>
    int main() 
    {
        short int word = 0x0001;
        char *b = (char *)&word;
        printf("%s\n", b[0] ? "Little Endian" : "Big Endian");
        return 0;
    }

As you can see, there will be problem when machine with different endianness communicating with each other. In networking, in order to solve the problem, the standard is to always use Big-endianness when