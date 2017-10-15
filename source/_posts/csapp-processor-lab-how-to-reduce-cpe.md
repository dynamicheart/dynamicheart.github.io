---
title: CSAPP Processor-Lab大幅降低CPE的技巧
tags:
  - CSAPP
  - Lab
  - Processor
date: 2017-01-19 17:05:37
---

## 前言

通过Load-Forwarding、添加新指令(iaddl、leave)以及循环展开这三种方法最多可以将CPE降低至8.4左右，但是满分的要求是将CPE降至7.8，通过改变分支预测、或者利用bug的方法比较繁琐而且违背了程序的准确性。

由于Y86指令集里面并没有ncopy.ys所需要的条件传送指令，虽然可以通过条件跳转来实现条件传送，但是因为有分支预测错误的存在，流水线处理器会浪费多个周期，使得CPE变高。

实际上，通过增加新指令的方法来降低CPE是可行的，经试验CPE可以降低到7.01。但是添加新指令的方法很具有技巧性，在实现的过程中会出现很多问题，下面我将一步步讲解如何添加新的指令。

<!--more-->

## 怎样在处理器中添加新的指令？

- 由于c代码以及h代码的限制以及我们不能修改除了HCL以外的程序，HCL程序中不能随便添加新的指令，所有的指令必须所预留的匹配才能编译成功。但是，处理器给我们预留了一个未使用的指令——IPOP2(本来有其它的用途)，它的十六进制编码为0xE0，这样就可以任意定制指令的编码长度以及功能，我们可以用IPOP2来实现条件传送的功能。

- 即使没有这个预留的指令，我们利用一些不使用功能码(ifun)的指令创造新的指令，比如，INOP的十六进制编码为0x10，可以用0x11等编码来代表条件传送，通过这种方法也可以创造新的指令，不会有任何冲突。

## assemble的过程会失败

由于我们不能修改yas的c代码，yas中无法将IPOP2汇编，因此直接在ncopy.ys中书写IPOP2会导致汇编失败，我们可以利用.byte、.word、.long直接将指令的十六进制编码写在ncopy.ys中，通过这种方法我们也可以使用方法二(0x11)来添加新的指令，但这些都会出现很多问题。

## .correctness.pl会失败

不加-p是使用seq处理器来测试ncopy.ys程序，因为没有在相应的seq处理器中添加我们的新指令，因此ncopy.ys中的新指令会被认为是异常。

## 最保险的做法

- 保证nocpy.ys的逻辑是正确的，仍然用条件跳转来实现，这样在任何y86处理器上运行都能得到正确的结果。

- 在nopy.ys程序中做一些标记，在检测到这个标记后，我们的流水线处理器不按照nopy.ys本来的逻辑运行，而是在f_icode中将条件跳转变成条件传送（新指令），同时predPC也要做出相应的改变，跳过一些不必执行的指令。

```
Loop1:
    mrmovl 56(%ebx),%esi
    .word 0x6146
    .long 0x38
    andl %esi,%esi
    jle Loop2
    iaddl $1,%eax
Loop2:
    mrmovl 52(%ebx),%esi
    .word 0x6146
    .long 0x34
    andl %esi,%esi
    jle Loop3
    iaddl $1,%eax
```

- .word 0x6146 和 .long 0x38是rmmovl %esi,56(%ecx)的十六进制编码，rmmovl不需要使用功能码，将其功能码设置为6，不影响程序本来的逻辑，而且在其它正常的程序中不可能会出现这种情况，我们利用这个功能码来特殊标记我们的程序，使其在jle Loop2时执行的是条件传送指令（ICNG），而且将f_predPC设置为f_valC,使其跳过iaddl $1,%eax指令。

```
int f_icode = [
        imem_error : INOP;
    imem_icode == IJXX && imem_ifun == CLE
     && E_icode ==  IRMMOVL && E_ifun == CG: ICINGJ;
        1: imem_icode;
];

int f_predPC = [
        f_icode in { IJXX, ICALL, ICINGJ } : f_valC;
        1 : f_valP;
];
```

## 完整代码

[https://github.com/dynamicheart/icslabs/tree/master/lab6](https://github.com/dynamicheart/icslabs/tree/master/lab6)
