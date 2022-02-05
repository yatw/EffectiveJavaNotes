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