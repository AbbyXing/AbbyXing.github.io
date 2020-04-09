---
layout: post
title:  "Learning Through Problem Solving: Java Lambda Function BiFunction"
description: understand Java Function and BiFunction through solving an coding problem.
---

# Problem

`cons(a, b)` constructs a pair, and `car(pair)` and `cdr(pair)` returns the first and last element of that pair. For example, `car(cons(3, 4))` returns `3`, and `cdr(cons(3, 4))` returns `4`.  

Given this implementation of `cons`:

```python
def cons(a, b):
    def pair(f):
        return f(a, b)
    return pair
```
Implement `car` and `cdr`.


# Python Solution

Let's first take a look at python solution, which is very straightforward.

```python
def cons(a, b):
	def pair(f):
        return f(a, b)
    return pair

def car(pair):
    def return_first(a, b):
        return a
    return pair(return_first)

def cdr(pair):
    def return_last(a, b):
        return b
    return pair(return_last)
```
While python code for this problem is not that hard, trans it to Java would be a tough work, since we can't actually define a function in another function and return it. In Python, functions are first-class objects. They can be assigned to variables, stored in data structures, passed as arguments to other functions, and returned as values from other functions. Let's first understand what happens if running this code. The following explanation could be brute force and look weird, but I just write it this way since it might help understanding, in my opinion.  

When we call `cons(a, b)`,  it first defines a function object `pair`, which passes an argument called `f`. `f` is an anonymous function (or we say a lambda function), which has two parameters `a` and `b`. The `pair` returns function `f(a, b)`, and `cons(a, b)` returns `pairs`. Therefore, we can say when calling `cons(a, b)`, what we get is a function object `pair`, which calls the function `f(a, b)`. For understanding, I write the return object this way: `pair(f(a, b))`.  It doesn't mean that it really returns `pair(f(a, b))`, it's just for comprehending. What it truly returns is just the partially defined function: `pair`.

Then in `car(pair)`, we pass the `pair` returned from `cons(a, b)`. Remember `pair` should look like this format: `pair( f(a, b) )`, which means we need to pass in an lambda function `f`, and this `f` should look like `f(a, b)`. What his function `f` do is to help us to get the first item `a`. So we define a function called `return_first(a, b)` which returns the first item `a`, and pass it in `pair` like this: `pair(return_first)`. And when we return `pair(return_first)`, remember the definition of `pair` in `cons`, it will call the function `return_first` and return the returned value from `return_first`, so that's how we make the first item `a` returned. Same way as `cdr(pair)`.  

Now we understand the python solution, the problem is how can we trans it to Java? One solution is we can use `Function` and `BiFunction` in Java to do what we've done in python.



# Java Function and BiFunction

## 1.Function

`Function` as a functional interface, its main method `apply(T t)` receives a parameter and returns a value.

```java
public interface Function<T, R> {

    /**
     * Applies this function to the given argument.
     *
     * @param t the function argument
     * @return the function result
     */
    R apply(T t);
}
```
In `<T, R>`, `T` is function argument type, and `R` is return type. If we have a `Function<T, R> f`, we can get the returned value with type `R` by using `f.apply(T t)`.  

An simple example:
```java
public int calculate(int a, Function<Integer, Integer> f) {
    return f.apply(a);
}
```
Then we call this method:
```java
instance.calculate(3, value -> value * value) // 9
instance.calculate(3, value -> value + value) // 6
instance.calculate(3, value -> value - 1) // 2
```
We can see that we can achieve a variety of functions by defining a method. The partially defined function `f` is passed into function `calculate` as a parameter, thus we can make it "fully" defined when calling `calculate`.

## 2.BiFunction
In `Function`, we can only have 1 parameter, what if we have 2 parameters to pass? We just use `BiFunction` instead of `Function`.

```java
public interface BiFunction<T, U, R> {
    R apply(T t, U u);
 
    default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t, U u) -> after.apply(apply(t, u));
    }
}
```
The `apply` method of `BiFunction` receives two parameters and returns a value. For example:
```java
public int calculate(int a, int b, BiFunction<Integer, Integer, Integer> f) {
   return f.apply(a, b);
}
```
And we call it:
```java
instance.calculate(1, 2, (a, b) -> a + b) // 3
instance.calculate(2, 3, (a, b) -> a - b) // -1
```


# Java Solution With Explanation
Now let's take a look at a Java solution for the original problem. For better understanding, I comment python code for each line so we can clear see the correspondence between each other and what each step actually does. For me I found that writing Java solution by referring to Python solution is much easier than start from scratch.

```java
// def cons(a, b)
public static<T, U, R> Function<BiFunction<T, U, R>, R> cons(T a, U b) {
	
	// def pair(f)
	Function<BiFunction<T, U, R>, R> pair;
	
	// return f(a, b)
	pair = f -> f.apply(a, b);
	
	// return pair
	return pair;
}

// def car(pair)
public static<T, U> T car(Function<BiFunction<T, U, T>, T> pair) {
	
	// return pair(return_first)
	return pair.apply((a, b) -> a);
	
	// p.s., return_first is (a, b) -> a
}

// def cdr(pair)
public static<T, U> U cdr(Function<BiFunction<T, U, U>, U> pair) {
	
	// return pair(return_last)
	return pair.apply((a, b) -> b);
	
	// p.s., return_last is (a, b) -> b
}
```
(The above solution is inspired by [Java Daily Coding Problem #005](https://dev.to/awwsmm/java-daily-coding-problem-005-1phg) by Andrew (he/him).)  

As we can see, `pair` would be a `Function<T, U>` which has a parameter `f`, and `f` is a `BiFunction<T, U, R>` which has 2 parameters `a` and `b`with type `T` and `U`. We can't know the type of `a` and `b` so we give them generic types. Then we decide what to return in our `pair`. It's supposed to return `f(a, b)` in python, so in Java we should say `f.apply(a, b)`. And the `cons(T a, U b)` returns `pair`.  
And in `car` function, like what we did in Python, we pass the `pair`, and then the important step is to define our `return_first` function. Here we can use lambda expression to define it: `(a, b) -> a`, which means returning the first argument `a`. Finally we need to return `pair(return_first)`, which means calling the function `return_first`. So in Java we just use `apply()` method to call that function. `cdr` is similar.  

Now we finished, let's test it:
```java
System.out.println(solution.car(cons(2,3))); // 2
System.out.println(solution.cdr(cons(2,3))); // 3
```

### Thanks for reading, hope this article helps you learning new things. Happy coding today!  

Github: [AbbyXing/DailyCodingProblem](https://github.com/AbbyXing/DailyCodingProblem).  
Welcome watching and let's tackle one coding problem everyday!




