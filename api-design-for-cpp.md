# API Design for c++

## Table of contents

- Chapter 1: Introduction
- Chapter 2: Qualitites

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

### Chapter 2: Qualities

_A good api should be a joy to use - but also fade into the background_ Henning (2009).

API should be provide a coherent solution for a problem, formulated such that it models the actual domain of the problem. Modelling the _key objects_ of that domain will increase usability as concepts correlate with existing knowledge and experience.

```
struct Order {
  uint64_t id;
  uint64_t price;
  uint32_t quantity;
};

void sendOrder(const Order&) {
}
```

**Good abstractions** 

Providing a high-level, logical abstraction such that a non-technical reader should be able to understands the concepts of the interface and how it is _meant to work_.

Each class with a central purpose, best to offer methods as `actions` rather than `get/set`. Exception to this rule is ADT (Abstract data type) which are simply `struct` with all public members.

**Model key objects**

Objective is to identify the colleciton of major objects, operations they provide and how they relate to one another. Objecdt model should be driven by the API requirements.

**Hide implementation details**

Physical hiding: Declaration vs definition - precise terms with specific meanings in c/c++. To declare provides _type_ information without any _memory_ information. Avoid definitions in headers, and implicit inlining (questionable advice?).

Logical hiding: encapsulation. Users will not _respect_ public API boundary, so do not give them access.





