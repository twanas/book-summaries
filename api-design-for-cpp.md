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

Each class with a central purpose, best to offer methods as `actions` rather than `get/set`. Exception to this rule is ADT/POD (Abstract data type) which are simply `struct` with all public members.

**Model key objects**

Objective is to identify the colleciton of major objects, operations they provide and how they relate to one another. Objecdt model should be driven by the API requirements.

**Hide implementation details**

Physical hiding: Declaration vs definition - precise terms with specific meanings in c/c++. To declare provides _type_ information without any _memory_ information. Avoid definitions in headers, and implicit inlining (questionable advice?).

Logical hiding: encapsulation. Users will not _respect_ public API boundary, so do not give them access. A major driver for this is managing [_invariants_](https://en.wikipedia.org/wiki/Invariant_(mathematics)#Invariants_in_computer_science)

Where possible, remove private methods from your header file(s), by moving them to be `static functions` with a `.cpp` file. This can be done when private members only access public members of a class - Lakos 1996.

_Do_ use nested classes to hide impementation classes.

**Concerns with virtual functions**

- Calls must be resolved at runtime by performing a `vtable lookup`
- Adding, reordering or removing a virtual function will break binary compatibility
- Virtual functions cannot always be inlined

Despite this, if using virtuals:
- always declare destructor as virtual
- never call virtual functions from c'tor/d'tor

**Convenience API**

Can layer minimal API(s) with a convenience API to make it easier to use.

**Difficult to misuse**

Prefer `enum` over `bool`.

```
class Date:
public: 
  Date(Year year, Month month, Day day) {};
};
```

NB: Strong types over `int` for year/month/day. Single c'tor holding invariant of half-initialised date.


**Consistent** 

Below violates principle of least surprise:

```
void *calloc(size_t count, size_t size); // allocates size * count bytes
void *malloc(size_t size); // allocates size bytes
```

Whereas the stl `size()` offers a consistent count of elements across `std::vector`, `std::list`, `std::set` and so forth.

**Orthogonal**

Methods should not have side effects. Calling a method that sets a property should _only_ change that property. This creates a more predictable API.

Orthogonal APIs can be achieved by : reducing redundancy; increasing independence

**Robust resource allocation**

Communicate the `ownership` of allocated objects by return type (`std::unique_ptr`, `std::shared_ptr`)

**Recuce class coupling**

When we have a choice, prefer declaring a function as a _non-member, non-friend_ function rather than a member function. This improves encapsulation and reduces coupling.

**Manager classes*

Manager's own and coordinate several lower-level classes. This allows dependencies to be _broken_.

Compare
```
void doSelect() {   // tight coupling between all input types
  if (mouseInput) {
    ...
  } else if (tableInput) {
    ...
  }
}
```

To 

```
struct Input {
  ...
};
void doSelect(Input*); // unaware of underlying type
```

**Callbacks, observers, notifications**

General issues to be aware of:
- reentrancy: api calling out to unknown user code, the user code may well call back into API. 
- lifetime management: clients should have a _clean way to disconnect_ and stop receiving updates
- event ordering: sequence of callbacks or notifications should be clear 

Callbacks are pointers to a function within a module A which is passed to module B. They do not have link dependencies and are effective in breaking cyclic dependencies. Sometimes it is useful to provide a 'closure' within the callback function. A closure is a piece of data that module A passes to B, and module B includes in the callback to A. 

Observer patttern has an object maintain a list of dependent objects (observerws) and notifies them by calling one of their methods.

Notifications: generalises the observer pattern through a centralised notificaiton system. Senders do not need to know about receivers beforehand, further reducing the sender-receiver coupling. Signals and slots are a popular notification scheme. Signals are callbacks with multiple targets (slots).
