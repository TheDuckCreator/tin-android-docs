---
layout: post
title:  Basic Kotlin Lang
# permalink: /structure/programstructure
---
# Intro To Kotlin Programing
Prepare for Project Preparation
Using Ecilipse IDE and Kotlin Marketplace Extension

<b>Learn Kotlin: </b> https://kotlinlang.org/docs/reference

This is only my note during learning and coding something may be mistake.

Theethawat Savastham | Student at Prince of Songkla University Hatyai Thailand (At first I cannot typing in Thai in Ecilipse IDE) Now I learn in IntelliJ IDEA it can type in Thai but I'm idle.

## Structure
*  Don't Need a  Pre-processor because Kotlin is initial itself at the begining from IDE
* Don't worry about package name, It nescessory only to show that it are in the same domain. In file system they don't show in class view but in IDE it will show you the structure of class
* Function Initial run with main
* No Semicolon needed in Kotlin
## Table of Content
 * [Basic Syntax](#basic-syntax)
 * [Function](#function)
  * [Variable](#variable)
  * [String Management](#string-management)
  * [Array](#array)
  * [Class](#class)
  
## Basic Syntax
### printing out the word
```
//using print or println
print("Hello World")
```
### printing out the value of variable
It can be inside the double quote with $ sign using variable Name

```
print(" $variablename ")
//If have any calculating
print(" ${variableName1 + variableName2}")
```

## Function
* To call using like in C Language
* Using <b>fun</b> to initial the function

### Function Prototype

```
fun functionName(parameterName:ParameterType): Returntype{
}
```
Example for Very Basic Function (don't have return)

```
fun sum(a:Int,b:Int){
	println("a is $a  b is $b")
}
```

### Call function to use Return Value
In variable define or redefine

```
	var x = functionName(Parameter)
	var y:Int
	y = functionName(Parameter)
```
Beware Function and Properties are not the same
### Using the properties
In Properties we use to get something from variable you can using Kotlin Library properties
* Calling Properties

```
	var testLengthVariable = "Theethawat"
	var theethawatLength ="Theethawat".length
```
* Or calling in print statement

```
	println("Variable of testLengthVariable is ${testLengthVariable.length}")
```

## Variable
 * Using <b>val</b> can be assign a value only once
 * Using <b>var</b> can be reassign
 * Can use both Type define or not, if you not define a type you must initial value in the declare variable part and Kotlin will assign the type automatically 
 * Type Define and value Define is optional All are correct
 
 ```
 var a:Int =1
 var a = 2
 var a:Int
 ```
 * This is example that <b>Incorrect</b>
 
  ```
 var a 
 ```
 * Variable can define both in global and local (Private to function) if it be global it can be modify by any function without re parameter-initial like
 
 ```
 var x = 0
fun incrementX() { 
    x += 1 
}
 ```
 ## String Management
 ### Triming Margin
 Can use trimMargin using with prefix '|' only if use another prefix using trimMargin('Prefix')
  ```
  val str3 = """ #Hello World""".trimMargin()
  val str4 = """ #Hello World """.trimMargin("#")
  ```
  Can use ```.trimIndent ``` for trim white space before text like a white space
  and Can use triple quote """ for let string in the string can be multiple line as we code
  
  ## Array 
  ### Array Declaring 
  Can Creating Array with Initial Value , Initial Value Using Function or Uninitialized using normal form ```arrayOf``` 
  if you initial value
  ```
  val a = intArrayOf(11,13,15,17,12)
  ```
  or using Specific type of Array
  
   * BooleanArray   `booleanArrayOf(true, false)  `
   * ByteArray   `byteArrayOf(1, 2, 3)   `
   * CharArray   `charArrayOf('a', 'b', 'c')   `
   * DoubleArray   `doubleArrayOf(1.2, 5.0)   `
   * FloatArray   `floatArrayOf(1.2, 5.0)   `
   * IntArray `intArrayOf(1, 2, 3) `
   * LongArray `longArrayOf(1, 2, 3) `
   * ShortArray `shortArrayOf(1, 2, 3)` 
 
 or can design array by using function like
 
    val asc = Array(5,init = {i->i+1})
     for (b in asc){
         println(b)
     }
 
 ### Array Properties
  * Properties that can use when declare an Array
  `sortedArray(), sortedArrayDescending() `
  * Properties that can use when we using function
  `sort(), sortDescending() , min(), max(),first(), last()` it will return a view of the things 
  that you want in array (some only for Int or Number Variables)
   for example
   
        println("List of Array B is ${b.sorted()}")
 ### Collection
  Collection is like array but it's the tool that can easier management
  * List use `Listof()` (Can replace string with other type)
  
        val  myList = listOf<String>("Theethawat","Songpon")
  * Map use `mapOf()`
 
         val myMap = mapOf<Int,String>(Pair(1,"Theethawat"), Pair(2,"Theematach"))
  * Set use `setOf()`
  
        val mySet = setOf<String>("Theethawat","Sirinuch")
    using 3 types of collection and other type of Array can be random access
## Class
### Basic Concept of Class
 * It's like class in Java and sometime it's like stucture in C 
 in my own opinion
 * Use function (main or any) to call a class
 * If Compare to Java it has 3 part 
    * Field for variable declare and initial (Java divide into 
    field and Constructor) and can use argument `also()` to make this variable call the function like `val nameInput = "The Input value is $name".also(::println)`
    * `init()` can use for initial, set value and call function in class
    * function `fun()` 
 * Everythings contain public, protected, private same like other OOP 
 * Can use dot(.) for go to the attribute like in C or Java
 * For Example in file Address.kt

## Object
### Companion object 
If your class have a Companion object show that you can implement or adding the function to that object like

	class MyClass {
    	companion object { }  // will be called "Companion"
    	}

    	fun MyClass.Companion.printCompanion() { println("theethawat") }

    	fun main() {
    	MyClass.printCompanion()
    	} 
You will got an output "Theethawat", i pick it from official Document at [Object Documents in Kotlin](https://kotlinlang.org/docs/reference/object-declarations.html#companion-objects)
 
## Basic Function and library
* Kotlin will add Basic Library name kotlin.* if you use IDE or compiler
* There include many function inside
* String Function Reference is located at https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/index.html