# Effective Java - 3rd Edition Notes

## 9 General Programming

### Item 57: Minimize the scope of local variables
* The most powerful technique for minimizing the scope of a local variable is to declare it where it is first used.
* Nearly every local variable declaration should contain an initializer
	* that just mean you should assign the value as you declare the variable
	* one exception is variable that needs to be used outside of a try-catch loop kind of have to be declare before the try block, and assign the value inside try block
* Prefer forloop to while loop as forloop allow you to declare loop variables
	* avoid accidentally reusing an old index variable from a previous while loop

### Item 58: Prefer for-each loops to traditional for loops
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

### Item 59: Know and use the libraries

Take a look at problem with [Random](src/Item59.java)

* By using a standard library, you take advantage of the knowledge of the experts who wrote it and the experience of those who used it before you.
* Every programmer should be familiar with the basics of java.lang, java.util, and java.io, and their subpackages.
* If you can't find what you need in Java platform libraries, look into high-quality third-party libraries, such as Google's Guava library.

### Item 60: Avoid float and double if exact answers are required

* The float and double types are particularly ill-suited for monetary calculations because it is impossible to represent 0.1 as a float or double exactly

[Example](src/Item60.java)

* The right way to solve this is use BigDecimal, int or long for monetary calculations
* Use BigDecimal if you want the system to keep track of the decimal point and you don't mind the invonvenience and cost of now using a primitive type
* If the quantities don't exceed 9 decimal digits, you can use int
* If the quantities don't exceed 18 decimal digits, you can use long
* Otherwise, bigDecimal

### Item 61: Prefer primitive types to boxed primitives
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

### Item 62: Avoid strings where other types are more appropriate
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

### Item 63: Beware the performance of string concatenation
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