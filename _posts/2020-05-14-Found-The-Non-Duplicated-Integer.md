---
layout: post
title: "Find The Non-Duplicated Integer In An Array"
description: use bitwise to find the non-duplicated integer in 3 problems.
---

# Intro
There is a type of questions, which gives you a non-empty array of integer, every element appears a specific number of times except for one, your goal is to find and return that single number.

### This is a simple question if you already known or read the solution, but it is difficult to think of the solution before doing it!

This article lists 3 versions of "find the single number" problems from easy to hard, and gives the solutions respectively using bitwise operations.


# Bitwise Operation
In this section, we will go through basic bitwise and explain the reasons for using bitwise and list several use case of bitwise.

## Basic Bitwise Operations

```
<<	left shift
>>	right shift
>>>	unsigned right shift
&	AND
|	OR
~	NOT
^	XOR
```

## Why use bitwise operation?
- Make the code easier to understand. `a >>1` is easier to understand than `a / 2` if we want to do a right shift.
- Efficiency. Under some circumstances, bitwise is more efficient than multiplication and division.
- In some cases we have to use bitwise. Like the 3 problems in this article.
- Resource saving. Avoid using multiple `boolean` values.

## Where to use?
The typical use case of bitwise (or we can say bitmask) is to jointly represent the status of multiple features especially the status of different authorities (like select, insert, update and delete) of a file (or other objectives). In this case, using bitwise instead of several `boolean` values can save space and improve the efficiency. For example:

```java
public final static int FLAG_1 = 1 << 0;
public final static int FLAG_2 = 1 << 1;
public final static int FLAG_3 = 1 << 2;

private int flag;

boolean isFeatureEnabled(int flag) {
  return (this.flags & flag) > 0;
}
void enableFeatures(int flags) {
  this.flags |= flags;
}
void disableFeatures(int flags) {
  this.flags &= ~flags;
}
void toggleFeatures(int flags) {
  this.flags ^= flags;
}
```

The `this.flag` jointly represents the the authorities of 3 different features 1, 2, and 3. With `&`, we can see if a specific feature is enabled. `this.flags | flags` can enable one or more features, `this.flags &= ~flags` disables one one ore more features, and `this.flags ^= flags` can toggle one or more features.


# Problem 1 (Easy)
Given a non-empty array of integers, every element appears twice except for one. Find that single one in O(n) time & constant extra space.

## solution
Hint: `0 ^ x = x`, `x ^ x = 0`

```java
public int solution(int[] a) {
	int result = a[0];
	for(int i = 1; i < a.length; i++)
		result ^= a[i];
	return result;
}
```

# Problem 2 (Medium)
Given an array of integers. All numbers occur twice except two numbers which occur once. Find these two numbers.

## solution
The extra steps for this problem comparing to the 1st is to separate the two single numbers after doing all XOR operations.

```java
public void solution(int[] a) {
	int combine = a[0];
	for(int i = 1; i < a.length; i++)
		combine ^= a[i];
	// separate the two single numbers
	int a = combine, b = combine;
	for(int i = 0; i < 32; i++) {
		if(combine & (1 << i)) { // if this bit is 1, mean a and b are different at this bit
			for(int x : a) {
				if(x & (1 << i)) {
					a ^= x;
				} else {
					b ^= x;
				}
			}
			break;
		}
	}
	System.out.println(a + " " + b);
}
```
`combine` is `a ^ b` after all XOR operations if a and b are the two single numbers. The approach to separate a and b from `a ^ b` is to find a bit which is equal to 1, which means that a and b are different at that bit. Then we loop the array again, and divide the elements in array to two subsets. One subset contains all elements that is 0 at that bit, the other subset contains all elements that is 1 at that bit. By keep doing XOR in each element of two subsets separately, we can finally get two different integers, which would be a and b.

# Problem 3 (Hard)
Given an array of integers where every integer occurs three times except for one integer, which only occurs once, find and return the non-duplicated integer in O(n) time & constant extra space.

## solution
In this problem, considering elements appear odd times not even, so the XOR is no longer working. So we use two bits storage `ones` and `two`, and a common bit mask `common_bit_mask`. `ones` stores the bits that appear only once, `twos` stores bits that appear twice. The `common_bit_mask` extracts the common bits set of `ones` and `twos`. If a bit is contained in both `ones` and `twos`, means this bit has appeared three times, which should be removed from `ones` and `twos`. After looping finished, `ones` would be the single number we want to get.

```java
public int solution(int[] a) {
		int ones = 0 ;
	    int twos = 0 ;
	    int not_threes ;

	    for( int x : a )
	    {
	    	// bits contain in both ones and x means they appear twice, so add them to twos
	        twos |= ones & x ;

	        // if bits in x is not in ones then add them to ones, if they're already in, then remove them from ones
	        ones ^= x ;

	        // get bits which appear third time
	        common_bit_mask = ones & twos ;

	        // remove bits that appear third time from ones and twos
	        ones &= ~common_bit_mask ;
	        twos &= ~common_bit_mask ;
	    } 
	    return ones;
	}
```

### Hope this article helps you learning new things. Happy coding today!  

Github: [AbbyXing/DailyCodingProblem](https://github.com/AbbyXing/DailyCodingProblem).  
Welcome watching and let's tackle one coding problem everyday!



