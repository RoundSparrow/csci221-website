---
title: Test 2 from Fall 2016
layout: default
categories: [studyguides]
---

# Test 2 from Fall 2016

**Note, you may only refer to this site during the test. Do not look at other notes or code or Google, etc. Do not work together. This is not a group test.**

There is one problem (wow, how simple!) that is worth 10,000 points (oh no!)

Create a bitbucket repo called `csci221-test2`. The answers are due by Tue Dec 13, 11:59pm.

## Task

Create a novel variant of the hash map, known as a *HushMup*, that works as described below. Also create some Google Test test cases and a UML diagram. The deliverables are described again below.

## Background on hash maps

A hash map stores key/value pairs. It generally works as follows:

- Each key should have a corresponding "hash" number. This is often an integer or long. A hash function must exist to compute the hash for the key's data type. For example, if the key is an integer, the hash function can just return the key itself. If the key is a string, the hash function must compute an integer value representing the string, e.g., by adding up all the ASCII values of the characters in the string. If the key is a float, a hash code may be generated by interpreting the bits in the float as an integer instead. And so on.
- We will also need a way to compare two keys for equality. Usually `.equals()` (Java) or `==` (C++) works just fine for simple key types like integers, strings, etc; for complex user-defined classes, the user must implement a `.equals()` or `operator==()` function.
- The underlying storage for the hash map is either a linked list containing key/value pairs in each node or two arrays/vectors: one containing keys, one containing values, synchronized so that a key in position `i` in the first array corresponds to the value in position `i` in the second array.
- The `put(key, val)` function works as follows: create the hash for the key; use this hash as an array/vector/list position (if the hash is large, compute `hash % capacity` to get a number that's in the range of `[0,capacity)` of the array/vector/list). Now that we have an index, check if that index is empty in the array/vector/list. If so, save the key and value in those positions, and we're done. If the slot is not empty, there is a collision, and you have some choices: move forward one slot, compute a new hash (new index) based on the first hash, etc. Somehow, find a position to save the key and value. Note, you will need to grow the array/vector/list if it's full.
- The `get(key)` operation works as follows: compute the hash, perhaps also compute `hash % capacity` to get an index, and check the array/vector/list at that position. If it's empty, move forward one spot etc. (using the same logic for movement as `put`) until you run out of places to check; if still not found, return null. Otherwise, if there is something at that position, check that given `key` equals key in that index. If they are equal, you found the correct index and can return the value at that index (perhaps from the other array of values). If not equal, more forward and check the next two keys for equality (again, using the same logic for movement as `put`).

## HushMup features

- Any kind of key and value may be stored. Thus, a HushMup is a template class with potentially different types for key and val (but all keys are the same type and all vals are the same type).
- HushMups use two arrays for the underlying storage (not vectors or linked lists).
- HushMups have a third array, of booleans, indicating if a slot is occupied or not. We must do this because we cannot simply put nulls in the key array since there is no universal null value for arbitrary types (unlike Java objects which are actually pointers).
- The constructor of HushMup must receive a *function pointer* to the hash function for the given key type. Since the key type can be anything (a template type) the HushMup code will not know which function to call for generating a hash. Thus, it must be told, with a function pointer. Read about function pointers [from our course notes](http://csci221.artifice.cc/lecture/pointers-symbol-table.html#tocAnchor-1-10). Note that the function pointer type must refer to your template type.
- HushMups support `get` and `put` with the usual arguments (of course, template type arguments). Unlike Java, C++ does not treat every object as a pointer, so we cannot return null from `get` if the key is not found in the map. Thus, HushMups also have a `has(key)` function that returns true/false. At the top of the `get` method, an assertion is made that the map has the given key. Otherwise, the program crashes. Achieve this by doing the following: `#include <cassert>`, plus at the top of the `get(key)` method, add a line of code: `assert(has(key));`. Note, both `has` and `get` must check the `occupied` array: an old (deleted) key may be found in the keys array, but that key is only valid if the corresponding position in the `occupied` array is set to `true`.
- HushMups have a `del(key)` method that, presumably, just marks the space as unoccupied. This function should also have `assert(has(key))`.
- Whenever a HushMup needs to grow, the arrays double in size. The data in the arrays are copied into the new larger arrays, keeping their same positions.
- The HushMup's logic for handling collisions is to move forward one position, wrapping around to the other side when the end of the array is reached.
- HushMups have a `size()` function that returns the number of key/value pairs in the map. This is not  necessarily the same as the size of the arrays, known as `capacity`.

## Deliverables

- Submit `hushmup.h`, containing the implementation described above. It's a template class, so all the code belongs in the `.h` file.
- Write `testhm.cpp` to test the implementation. Use the Google Test framework. Write tests for the following scenarios:
  - Create a HushMup with whatever types and check the size without adding anything.
  - Create a HushMup to store `int` keys and values. Use an identity hash function (i.e., return the key as its own hash). Add the same key/value pair multiple times. Check that the size is 1, the `has` function returns true for the key, and the `get` function retrieves the val for the key.
  - Add more key/value pairs (different keys and values) and check `has` and `get` for each. Also check that `size()` returns the right value. Be sure to have a case where enough different key/value pairs are added to cause the arrays to grow.
  - Now delete some values using `del` and check that everything comes out right. In particular, delete a key and then check `has` again for that same key. It should return false.
  - Create a HushMup that stores string keys and values. Write a hash function for strings that adds all the ASCII values of the characters in the string and returns the sum. Check that all the HushMup features work, just like before.
- Be sure that your code works on Londo and does not leak any memory nor does it crash. Be sure you are using the `assert` described previously. Be sure that your code compiles without any warnings.
- Create a UML diagram documenting the HushMup class. You'll only have one box in the diagram, but all the fields and functions should be representing, including their protection status (public, protected, private), field types, argument types, and return types.

**Do not install Google Test yourself.** The Makefile below uses a pre-installed location.

**Use this Makefile.** Note, your code files must be named `hushmup.h` and `testhm.cpp`:

```
GTEST_DIR = /opt/gtest-1.7.0
CXX = g++
CXXFLAGS = -ansi -Wall -ggdb3 -isystem $(GTEST_DIR)/include -Wextra -lpthread
GTEST_HEADERS = $(GTEST_DIR)/include/gtest/*.h $(GTEST_DIR)/include/gtest/internal/*.h
GTEST_SRCS = $(GTEST_DIR)/src/*.cc $(GTEST_DIR)/src/*.h $(GTEST_HEADERS)

all: testhm

gtest-all.o: $(GTEST_SRCS)
                $(CXX) $(CXXFLAGS) -I$(GTEST_DIR) -c $(GTEST_DIR)/src/gtest-all.cc

gtest_main.o: $(GTEST_SRCS)
                $(CXX) $(CXXFLAGS) -I$(GTEST_DIR) -c $(GTEST_DIR)/src/gtest_main.cc

gtest.a : gtest-all.o
                $(AR) $(ARFLAGS) $@ $^

gtest_main.a : gtest-all.o gtest_main.o
                $(AR) $(ARFLAGS) $@ $^

testhm: testhm.cpp hushmup.h gtest_main.a
        $(CXX) $(CXXFLAGS) -o testhm testhm.cpp gtest_main.a

.PHONY: clean
clean:
        rm *.a *.o testhm
```

## Questions?

Write me an email with any questions you have. As much as possible, I will submit my response to the whole class (via email) so that everyone benefits from your question.
