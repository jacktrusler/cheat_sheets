# Java 
![java](./svgs/java.svg "Java")  
*A language so revolutionary that it was named after some guy getting a cup of coffee in his office.*

### Basics

- Interfaces are an abstract blueprint on how to create a class.  
- Classes are a blueprint of an object that is stored in the heap in memory.  
- Java is almost entirely an object oriented programming language, basically everything is an 
object with methods.
- The exception to the rule are primitive types (byte, short, int, long, float, double, boolean, and char)
- Primative types have wrapper objects, these can be null, primatives can never be null.

### Running Java Programs
You must define a class with a main function in the form of:
```java
//SomeClass.java
public class SomeClass {
    public static void main(String[] args){
      //...your program
    }
}
```
the `main` function serves as the entry point for the program, and can accept any number of arguments. When
the program must first be compiled before it can be executed. The flow goes as follows:

You start off with .java files which get compiled into .class files and then are exectued by the Java 
Virtual Machine.

![Running Files](./images/java_files.png "Running Files")

### Autoboxing
You cannot put primative values into a collection, collections can only hold object reference. To do this you must
__box__ primative values into a wrapper class (i.e. Integer for int). If you must later get a primative 
type, you must __unbox__ that type using the Integer.intValue method (in the case of ints).

Because of this java implements something called Autoboxing, which automatically boxes and unboxes interchangable
primatives (such as int) with their wrapper class (Integer). This makes it so you can mostly ignore the type
issues of converting from Integer to int and visa versa. However, it comes at a performance cost because all the extra
objects must be garbage collected, and should not be used for critical loops. From the javadocs: 

> So when should you use autoboxing and unboxing? Use them only when there is an “impedance mismatch” between reference types and primitives, for example, when you have to put numerical values into a collection. It is not appropriate to use autoboxing and unboxing for scientific computing, or other performance-sensitive numerical code. An Integer is not a substitute for an int; autoboxing and unboxing blur the distinction between primitive types and reference types, but they do not eliminate it.



