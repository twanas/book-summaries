# API Design for c++

## Table of contents

- Chapter 1: Introduction

### Chapter 1: Introduction

APIs provide an abstraction and functional specifidation for a component/problem. Can be as small as a single function, or involve hundreds of classes, methods, free functions data types and enumerations.

In c++, they include:
- *.h files
- *.a, *.so library implementations
- documentation

Interfaces are the _most_ important code a developer writes. Whilst encouraging encapsulation and code-reuse; they will be used in unintended ways & tend to live a long time. 

API design is _difficult_ because to encourage code-reuse we often have to generalise beyond that of the initial domain problem space. We can benefit from API design practices for internal code, without paying the additional cost required to design a public API.

As API provides an abstraction over a core piece of functionality, our application code often involves _layers_ of APIs/abstractions:
- base: os specific api such as `fork()`, `getpid()`, `kill()`
- language: `c++ std::`
- networking/boost/graphics
- application

APIs provide communication _contracts_. 

