---
layout: post
title: (原創) 如何让一个thread在背景不断的执行? (使用semaphore)
author: gavinkwoe
date: !binary |-
  MjAwOS0wNi0yOCAxMzoyODoxMSArMDgwMA==
date_gmt: !binary |-
  MjAwOS0wNi0yOCAwNToyODoxMSArMDgwMA==
---
要让一个thread在背景不断的执行，最简单的方式就是在该thread执行无穷回圈，如while(1) {}，这种写法虽可行，却会让CPU飙高到100%，因为CPU一直死死的等，其实比较好的方法是，背景平时在Sleep状态，当前景呼叫背景时，背景马上被唤醒，执行该做的事，做完马上Sleep，等待前景呼叫。当背景sem_wait()时，就是马上处于Sleep状态，当前景sem_post()时，会马上换起背景执行，如此就可避免CPU 100%的情形了。
 1/**//*
 2(C) OOMusou 2006 <a href="http://oomusou.cnblogs.com/">http://oomusou.cnblogs.com</a>
 3
 4Filename    : pthread_create_semaphore.cpp
 5Compiler    : gcc 4.10 on Fedora 5 / gcc 3.4 on Cygwin 1.5.21
 6Description : Demo how to create thread with semaphore in Linux.
 7Release     : 12/03/2006
 8Compile     : g++ -lpthread pthread_create_semaphore.cpp
 9*/
10#include <stdio.h>     // printf(),
11#include <stdlib.h>    // exit(), EXIT_SUCCESS
12#include
<pthread.h>   // pthread_create(), pthread_join()
13#include <semaphore.h> // sem_init()
14
15sem_t binSem;
16
17void* helloWorld(void* arg);
18
19int main() {
20  // Result for System call
21  int res = 0;
22
23  // Initialize semaphore
24  res = sem_init(&binSem, 0, 0);
25  if (res) {
26    printf("Semaphore initialization failed!!\n");
27    exit(EXIT_FAILURE);
28  }
29
30  // Create thread
31  pthread_t thdHelloWorld; 
32  res = pthread_create(&thdHelloWorld, NULL, helloWorld, NULL);
33  if (res) {
34    printf("Thread creation failed!!\n");
35    exit(EXIT_FAILURE);
36  }
37
38  while(1) {
39    // Post semaphore
40    sem_post(&binSem);
41  }
42
43  // Wait for thread synchronization
44  void *threadResult;
45  res = pthread_join(thdHelloWorld, &threadResult);
46  if (res) {
47    printf("Thread join failed!!\n");
48    exit(EXIT_FAILURE);
49  }
50
51  exit(EXIT_SUCCESS);
52}
53
54void* helloWorld(void* arg) {
55  while(1) {
56    // Wait semaphore
57    sem_wait(&binSem);
58    printf("Hello World\n");
59  }
60}
