---
layout: post
title: "Think in bitwise"
category: tech
keyword: csapp, 15213, lab
description: "I'm taking 15513 (Introduction to computer systems, online version of 15213) course of cmu this summer. Some concepts and knowledge this course is talking about is familiar to me, but the lab assignment is really tough. I'm writting this post to keep some notes on the first lab - datalab, full of bitwise operation."
---

## Prerequisite

Bitwise operation allows programmers to directly operate number variables in low-level perspective. Types of number could include signed and unsigned integer and float-point number (more on this later). Basically in C, you can use the following operations: left shift and right shift (<< and >>), bitwise and, or, xor, not. Here is an interesting and enlightening passage about bitwise operation, which shocked me a lot when I first read it. But mind yourself, it is written in Chinese. 

OK. If you are familiar with those operations, we can get to the banquet now!

Oh, sorry. One more thing. Without further instruction, all the problems mentioned below can only be solved using <<, >>, &, |, ~, !, ^, + and any control flow statement (if, switch, for, while, ?: statement, etc) and logic operation (&&, ||, etc) is strictly forbidden. Within these rules, you should reduce the total number of operations as much as possible. This gives us the motivation to keep improving our implementation, and then, the fun of this lab.

## Appetizer

Now I'm gonna show you some higher level usage of bitwise operation by explaning my solutions to the lab problems.

### isEqual

    /* 
     * isEqual - return 1 if x == y, and 0 otherwise 
     *   Examples: isEqual(5,5) = 1, isEqual(4,5) = 0
     *   Legal ops: ! ~ & ^ | + << >>
     *   Max ops: 5
     *   Rating: 2
     */
    int isEqual(int x, int y) {
        // To be solved
    }

Quite easy eh. Since we know, one simple way to check if two numbers are identical is to theck the result of xor. Exactly. So we simply return the negation of the xor result. Like the following: 

    int isEqual(int x, int y) {
        return !(x^y);
    }

This problem is fairly easy, but the idea is important: using xor to check the equivalence of two numbers. 

### byteSwap

    /* 
     * byteSwap - swaps the nth byte and the mth byte
     *  Examples: byteSwap(0x12345678, 1, 3) = 0x56341278
     *            byteSwap(0xDEADBEEF, 0, 2) = 0xDEEFBEAD
     *  You may assume that 0 <= n <= 3, 0 <= m <= 3
     *  Legal ops: ! ~ & ^ | + << >>
     *  Max ops: 25
     *  Rating: 2
     */
    int byteSwap(int x, int n, int m) {
        // To be solved
    }

At first glance, it's easy. But the first few ideas came to my mind involved if statement. Hmm, need to think over it again. Yeah, maybe I could extract the nth byte out into a variable, right shift n bits, left shift m bits and put it into mth byte. The same to mth byte. Great. So here comes the first version of my solution.

    int byteSwap(int x, int n, int m) {
        int nl = 0;
        int ml = 0;
        int nb = 0;
        int mb = 0;
        n <<= 3;
        m <<= 3;
        
        nl = 0xff << n;
        ml = 0xff << m;
        nb = x & nl;
        mb = x & ml;

        nb >>= n;
        nb <<= m;
        nb &= ml;
        mb >>= m;
        mb <<= n;
        mb &= nl;
        
        x &= ~(nl | ml);
        x |= (nb | mb);
        return x;
    }

Quite readable and easy to understand. But the total operations reaches 17, while many people can reduce it down to 10. How? After carefully inspecting these codes, I found most operations are wasted in shifting to get the nb and mb to the right location. This could be improved. But, how? If I want to change two bytes, I have to clean the target byte with & and put the byte into it with | (or +). Since no if statement available, the source byte cannot be shift to the target location directly using (n-m or m-n) because I cannot decide wheather to shift left or right. Wait. In "isEqual" problem, we use the property of xor that the result of xor of two identical numbers equals 0. And with another property of xor, commutative law, we can eliminate a number just by xor itself! Great idea.

So the solution comes out naturally. We first xor the nth byte and mth byte into a new variable. And then shift left to nth byte and xor the original number. Now the nth byte stores the information of mth byte because we xor nth byte twice. Do the same to mth byte and we successfully exchange two bytes!

    int byteSwap(int x, int n, int m) {
        int t = 0;
        n <<= 3;
        m <<= 3;

        t = 0xff & ((x>>n) ^ (x>>m));

        x ^= t<<n;
        x ^= t<<m;

        return x;
    }

Magic xor operation. So another tricky usage of xor. Now, things get more fun.

### sign

    /* 
     * sign - return 1 if positive, 0 if zero, and -1 if negative
     *  Examples: sign(130) = 1
     *            sign(-23) = -1
     *  Legal ops: ! ~ & ^ | + << >>
     *  Max ops: 10
     *  Rating: 2
     */
    int sign(int x) {
        // To be solved
    }

In 2's complement, the most significant bit defines it to be a negative (1) or a non-negative (0) number. But it cannot distinguish positive numbers from 0. On the other hand, if we use ! operation, we could distinguish 0 from non-zero numbers, but not positive from negative. So we could combine these two operations and get the answer. One thing to be noted, right shifting an int variable x will result in 0 (when x is non-negative) or -1 (when x is negative; -1 == 0xFFFFFFFF, right shift signed number will automatically fill the sign bit). Here comes the code: 

    int sign(int x) {
        return ((!x)^1) | (x>>31);
    }

## Main course

OK. We have seen some easy problems and know how to think in bitwise operation. Now we are taking some real challenges.

### logicalNeg

    /* 
     * logicalNeg - implement the ! operator using any of 
     *              the legal operators except !
     *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
     *   Legal ops: ~ & ^ | + << >>
     *   Max ops: 12
     *   Rating: 4 
     */
     int logicalNeg(int x) {
        // To be solved
     }


To make it more bitwise way, this function should return 0 if in argument has any 1 in any bit, otherwise return 1. In this case, the most obvious way is to or every bit and return the result. Unfortunately, it exceeds the maximun op. But it is a right way. Now that we cannot test only 1 bit at one time, we may test more bits at one time. Half is a good idea. 

First, we let the high 16 bits or to low 16 bits of x. Now all the 1(s) in x (if any) comes to low 16 bits of x. And then, let the second byte from the least significant byte or to the least significant byte. All the 1(s) in x (if any) comes to least significant byte. Repeat this process until all the 1s comes to the least significant bit. Now we just need to return the negation of that bit. But the number of operations reaches 12, the maximum limitation. Seems that we need to change a way to think.

    int logicalNeg(int x) {
        x |= (x>>16);
        x |= (x>>8);
        x |= (x>>4);
        x |= (x>>2);
        x |= (x>>1);
        x = ~x;
        return x & 1;
    }

Think deeper, and you will find that the key of this problem lies in distinguishing 0 from non-zero numbers. In bitwise world, 0 is unique because the 2's complement of 0 equals 0 itself, while other numbers are not. But if we solely take advantage of this, we will come back to the awkward situation where we need to find if a number is 0. We should go further. 

Transforming a 32-bit number into a 1-bit number mostly depends on one bit (usually either the MSB or the LSB). And the only one bit in a number that is predicable is sign bit (MSB). Think about this. The sign bit of the 2's complement and the origin of a non-zero number must be different. While 0 is the same (both 0). So we can take advantage of this and solve the problem within 6 operations. 

    int logicalNeg(int x) {
        x = ((~x) & (~(~x+1)))>>31;
        return x & 1;
    }

### howManyBits

    /* howManyBits - return the minimum number of bits required to represent x in
     *             two's complement
     *  Examples: howManyBits(12) = 5
     *            howManyBits(298) = 10
     *            howManyBits(-5) = 4
     *            howManyBits(0)  = 1
     *            howManyBits(-1) = 1
     *            howManyBits(0x80000000) = 32
     *  Legal ops: ! ~ & ^ | + << >>
     *  Max ops: 90
     *  Rating: 4
     */
    int howManyBits(int x) {
        // To be solved
    }

Finally here comes the real boss. Look at the max op. It's 90! It is a long story optimizing this function. I will finish it later.

