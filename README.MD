# Effective Java - 3rd Edition Notes


## Table of Contents

* [9 General Programming](#9-general-programming)    
    * [Item 57: Minimize the scope of local variables](#item-57-minimize-the-scope-of-local-variables)   
    * [Item 58: Prefer for-each loops to traditional for loops](#item-58-prefer-for-each-loops-to-traditional-for-loops)   
    * [Item 59: Know and use the libraries](#item-59-know-and-use-the-libraries)   
    * [Item 60: Avoid float and double if exact answers are required](#item-60-avoid-float-and-double-if-exact-answers-are-required)   
    * [Item 61: Prefer primitive types to boxed primitives](#item-61-prefer-primitive-types-to-boxed-primitives)   
    * [Item 62: Avoid strings where other types are more appropriate](#item-62-avoid-strings-where-other-types-are-more-appropriate)   
    * [Item 63: Beware the performance of string concatenation](#item-63-Beware-the-performance-of-string-concatenation)   
    * [Item 64: Refer to objects by their interfaces](#item-64-refer-to-objects-by-their-interfaces)   
    * [Item 65: Prefer interfaces to reflection](#item-65-prefer-interfaces-to-reflection)   
    * [Item 66: Use native methods judiciously](#item-66-use-native-methods-judiciously)   
    * [Item 67: Optimize judiciously](#item-67-optimize-judiciously)   
    * [Item 68: Adhere to generally accepted naming conventions](#item-68-adhere-to-generally-accepted-naming-conventions)   
* [10 Exceptions](#10-exceptions)    
    * [Item 69: Use exceptions only for exceptional conditions](#item-69-use-exceptions-only-for-exceptional-conditions)
    * [Item 70: Use checked exceptions for recoverable conditions and runtime exceptions for programming errors](#item-70-use-checked-exceptions-for-recoverable-conditions-and-runtime-exceptions-for-programming-errors)
    * [Item 71: Avoid unnecessary use of checked exceptions](#item-71-avoid-unnecessary-use-of-checked-exceptions)
    * [Item 72: Favor the use of standard exceptions](#item-72-favor-the-use-of-standard-exceptions)
    * [Item 73: Throw exceptions appropriate to the abstraction](#item-73-throw-exceptions-appropriate-to-the-abstraction)
    * [Item 74: Document all exceptions thrown by each method](#item-74-document-all-exceptions-thrown-by-each-method)
    * [Item 75: Include failure-capture information in detail messages](#item-75-include-failure-capture-information-in-detail-messages)
    * [Item 76: Strive for failure atomicity](#item-76-strive-for-failure-atomicity)
    * [Item 77: Don't ignore exceptions](#item-77-dont-ignore-exceptions)


Strive for failure atomicity
    
## 9 General Programming

### Item 57 Minimize the scope of local variables
* The most powerful technique for minimizing the scope of a local variable is to declare it where it is first used.
* Nearly every local variable declaration should contain an initializer
	* that just mean you should assign the value as you declare the variable
	* one exception is variable that needs to be used outside of a try-catch loop kind of have to be declare before the try block, and assign the value inside try block
* Prefer forloop to while loop as forloop allow you to declare loop variables
	* avoid accidentally reusing an old index variable from a previous while loop

### Item 58 Prefer for-each loops to traditional for loops
* for-each loop gets rid of the clutter and the opportunity for error hiding iterator or index variable(s)
* This is especially helpful when doing nested iteration

Easy to make a error when try to print all the card in a deck:
```java
enum Suit{ CLUB, DIAMOND, HEART, SPADE}
enum Rank {ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE, TEN, JACK, QUEEN, KING}
...
static Collection<Suit> = Arrays.asList(Suit.values());
static Collection<Rank> = Arrays.asList(Rank.values());

List<Card> deck = new ArrayList<>();
for (Iterator<Suit> i = suits.iterator(); i.hasNext();)
	for (Iterator<Rank> j = ranks.iterator(); j.hasNext();)
		deck.add(new Card(i.next(), j.next());
```
With for-each loop it is succinct
```java
for (Suit: suit: suits)
	for (Rank: rank: ranks)
		deck.add(new Card(suit, rank));
```
The three common situations we cannot use for-each:
* If we need to traverse and remove element at the same time
* If we need to update the element, then we need the index
* If we are traversing multiple collections at the same time

Lastly, consider implement **Iterable** if you are writing your own class

### Item 59 Know and use the libraries

Take a look at problem with [Random](src/Item59.java)

* By using a standard library, you take advantage of the knowledge of the experts who wrote it and the experience of those who used it before you.
* Every programmer should be familiar with the basics of java.lang, java.util, and java.io, and their subpackages.
* If you can't find what you need in Java platform libraries, look into high-quality third-party libraries, such as Google's Guava library.

### Item 60 Avoid float and double if exact answers are required

* The float and double types are particularly ill-suited for monetary calculations because it is impossible to represent 0.1 as a float or double exactly

[Example](src/Item60.java)

* The right way to solve this is use BigDecimal, int or long for monetary calculations
* Use BigDecimal if you want the system to keep track of the decimal point and you don't mind the invonvenience and cost of now using a primitive type
* If the quantities don't exceed 9 decimal digits, you can use int
* If the quantities don't exceed 18 decimal digits, you can use long
* Otherwise, bigDecimal

### Item 61 Prefer primitive types to boxed primitives
1. primitives have only their values, whereas boxed primitives have identities distinct from their values
2. primitive types have only fully functional values, whereas boxed primitive type can be null
3. primitives are more time and space efficient  

* Applying the == operator to boxed primitives is almost always wrong  
```java
Comparator<Integer> naturalOrder = 
  (i,j) -> (i < j) ? -1: (i == j ? 0 : 1);
```
The problem is i == j on Integer perform identity comparision instead  
We could do this instead:
```java
Comparator<Integer> naturalOrder = (iBoxed, jBoxed) -> {
    int i = iBoxed, j = jBoxed; // Auto-unboxing
    return (i,j) -> (i < j) ? -1: (i == j ? 0 : 1);
};
```
* When you mix primitives and boxed primitives in an operation, the boxed primitive is auto-unboxed
```java
public class Unbelievable{
    static Integer i;
    public static void main(String[] args){
        if (i == 42){
            System.out.println("Unbelievable");
        }
    }
}
```
This throw NullPointerException because auto-unboxing  
* The below has serious performance issue because the program construct about 2^31 unnecessary Long instances
```java
public static void main(String[] args){
  Long sum = 0L;
  for (long i = 0; i < Integer.MAX_VALUE; i++){
    sum += i;
  }
}
```
* There are legitimate uses for boxed primitives:
    * in collections
    * in generic type

### Item 62 Avoid strings where other types are more appropriate
* Strings are poor substitutes for other value types
* When a piece of data comes into a program it is often in string form, only leave it that way if this data is textual in nature
* Strings are poor substitutes for enum types
#### Strings are poor substitutes for aggregate types
Example
```java
// Inappropriate use of string as aggregate type
String compoundKey = className + "#" + i.next();
```
* Bad if the character used to separate fields occurs in one of the fields
* Need to parse the string to access individual fields
* can't provide equals, toString, or compareTo

#### Strings are poor substitues for capabilies
* Capability means an unforgeable key
* This chapter used a ThreadLocal example, and pass in String Key to access variable when it could have been a Key Object

### Item 63 Beware the performance of string concatenation
* Using the string concatenation operator repeatedly to concatenate n strings requires time quadratic in n
```java
public String statement(){
	String result = "";
	for (int i = 0; i < numItems(); i++){
		result += lineForItem(i); // String concatenation
		return result;
	}
}
```
Use StringBuilder **with preallocated size** instead
```java
public String statement(){
	StringBuilder b = new StringBuilder(numItems() * LINE_WIDTH);
	for (int i = 0; i < numItems(); i++)
		b.append(lineForItem(i));
		return b.toString();
}
```

### Item 64 Refer to objects by their interfaces
* If appropriate interface types exist, then parameters, return values, variables, and fields should all be declared using interface types
* Reason is flexibility- if we decide to switch implementations, all we need to do is change the class name in the constructor.
* If the original implementation offered some special functionality not required by the interface contract and the code depend on that functionality, then the new implementation should provide the same functionality
* It is entirely appropriate to refer to an object by a class rather than an interface if no appropriate interface exists
	* value classes such as String and BigInteger are often final and rarely have corresponding interfaces
	* objects belonging to a framework whose fundamental types are classes rather than interfaces
	* classes that provide extra methods not found in the interface (should be rare)
	* others...
* For the above exceptions try to use the least specific class in the class hierarchy that provides the required functionality


### Item 65 Prefer interfaces to reflection
* Reflection allows one class to use another, even if the latter class did not exist when the former was compiled.
* This power comes at a price:
	* You lose all the benefits of compile-time type checking
	* The code required to perform reflective access is clumsy and verbose
	* Performance suffers
* You can obtain many of the benefits of reflection while incurring few of its costs by using it only in a very limited form.
* {There is an example in the book}
* By create instances reflectively and access them normally via their interface of superclass
* A legitimate use of reflection is to manage a class's dependencies on other classes, methods, or fields that may be absent at runtime.


### Item 66 Use native methods judiciously
* Java Native Interface (JNI) allows Java programs to call native methods in C or C++
* JNI have had three main uses
	* provide access to platform-specific facilities such as registers
	* provide access to existing libraries of native code
	* write performance-critical parts of applications in native languages for performance
* It is seldom necessary to access platform-specific facilities via JNI as the Java platform matured
* It is rarely advisable to use native methods for improved performance (as Java continue to update)
* JNI has serious disadvantages
	* not safe, vulnerable to memory corruption errors
	* platform-dependent making it less portable
	* harder to debug
	* can decrease performance because the garbage collector can't automate, or even track, native memory usage
	* there is a cost associated with going into and out of native code
	* require "glue code" that is difficult to read and tedious to write
	
	
### Item 67 Optimize judiciously
* it is easy to do more harm than good
* don't sacrifice sound architectural principles for performance
	* Good programs localize design decisions within individual components, so individual decisions can be changed without affecting other parts
* Strive to avoid design decisions that limit performance
	* The APIs, wire-level protocols, and persistent data formats, are difficult to change
	* They can place significant limitations on the performance that a system can ever achieve
* Measure performance before and after each attempted optimization
	* it is difficult to guess where the program is spending its time
* Use profiling tools to get runtime information and detect slow runtime algorithm


### Item 68 Adhere to generally accepted naming conventions
* The name of any package that will be used outside your organization should begin with your organization's Internet domain name with the components reversed
	* users must not create packages or modules whose names begin with java or javax
	* abbreviations are encouraged
![](resource/68.jpg)

## 10 Exceptions

### Item 69 Use exceptions only for exceptional conditions
* Exceptions should never be used for ordinary control flow
    * exceptions are rarely fast
    * try-catch block might inhibits certain optimization that JVm might otherwise perform
* If you are writing a library, do not force your clients to use exceptions for ordinary control flow
    * Provide state-testing method like `hasNext()` for your iterator
    * Or return an empty optional or null
    
### Item 70 Use checked exceptions for recoverable conditions and runtime exceptions for programming errors
* There are three kinds of throwables:
#### Checked Exceptions
* Used when the caller can reasonably be expected to recover
* Force the caller to **try-catch** or **declare the method throw exception**
```java
public static void writeToFile() throws IOException {
 
}
```
* All you need to do to throw a checked exception is ensure that it doesn't extend `RuntimeException`
* If you throw this one, provide useful information in the error to help caller recover
#### RuntimeException
* Indicate programming errors when recovery is impossible and there is no point keep executing the program
* When unsure if the caller can recover, throw unchecked`RuntimeException`
#### Error
* There is a strong convention that errors are reserved for JVM
* Do not define Error subclasses; do not throw them either

### Item 71 Avoid unnecessary use of checked exceptions
Checked exception force programmers to deal with problems, enhancing reliability.
But if the programmer are going to handle the exception like this:
```java
}catch (The CheckedException e){
	throw new AssertionError(); // Can't happen
}
```
Then there is no point to throw a checked exception if there is nothing they can do about it. This just clutter the program.

There is 2 thing you can try:
1. Return an optional of the desired result type
	* Downside is you lose the stacktrace 
2. Provide another method for caller to check if an exception will happen
	* hasNext() method from iterator
	* Allow caller to just call the dangerous method with no try-catch if they want
	* If this extra method will duplicate the work, then it is not a good idea

### Item 72 Favor the use of standard exceptions 
* Do not reuse Exception, RuntimeException, Throwable, or Error directly
Use the common exceptions:
![](resource/72.jpg)

### Item 73 Throw exceptions appropriate to the abstraction
* Exception throw from lower-level abstraction are often not useful to higher-level abstraction
* Higher layers should catch lower-level exceptions and throw exceptions that can be explained in terms of the higher-level abstraction

#### Exceptions translation
You can also optionally pass the exception from lower exception to HigherLevelException to help debug
```java
try{
    ... // Use lower-level abstraction method
} catch (LowerLevelException e){
    throw new HigherLevelException(e);
}
```
* while exception translation is superior to mindless propagation of exceptions from lower layers, it should not be overused
    * Best to ensure lower-level methods always succeed
    * Have higher layer silently work around these exceptions, logging if needed
    

### Item 74 Document all exceptions thrown by each method
* Never take the shortcut of declaring a method to throw some superclass of multiple exception
	* It will obscures any other exception that may be thrown
```java
public void method() throw Exception{ // Do not do this
  ...
}
```
```java
public void method() throw IOException, FileNotFoundException{ // Do this
  ...
}
```
* You should document the unchecked exceptions that a method can throw although it is not always possible in reality (with @throws tag)
* Do not use the throw keyword on unchecked exceptions
* Exception documented with @throws tag but not throw keyword give a strong visual cue to others that exception is unchecked
* If the same exception is thrown by many methods in a class for the same reason, you can document the exception in the class's documentation comment

### Item 75 Include failure-capture information in detail messages
* Stack Trace contains the exception's string representation, the result of invoking its toString method
* Leave out sensitive data like passwords, encryption keys, etc
* To capture a failure, the detail message of an exception should contain the values of all parameters and fields that contributed to the exception
* You can ensure the exception contain the necessary parameter in the message by forcing it to be in the constructor:
```java
public IndexOutOfBoundException(int lowerBound, int upperBound, int index){
   super(String.format("Lower bound: %d, Upper bound: %d, Index: %d, lowerBound, upperBound, index));
...
}
```
* Content is more important than readability (not for normal user, for programmer debug)


### Item 76 Strive for failure atomicity
* Failure-atomic means a failed operation should not modified the state of other objects. Everything should stay at the same as if the operation didn't happen
* Several ways to achieve this property:
1. Use immutable object 
2. Check parameters before performing operation
3. Order any part that may fail before any part that modifies the object
4. Create a copy and perform operation on that copy
5. Write recovery code that roll back its state (rare)
* Failure-atomic is not always desirable due to the possibility to increase the complexity (also rare)

### Item 77 Don't ignore exceptions
* It is easy to ignore exceptions by surrounding a method invocation with a try statement and empty catch block
* If you choose to ignore exception, the catch block should contain a comment explaining why it is appropriate to do so, and rename the variable to "ignored"
```java
int someNumber = 0;
try{
  someNumber = someOperation();
catch (TimeoutException | ExecutionException ignored){
  // Use default: default is desiable, not required
}
```