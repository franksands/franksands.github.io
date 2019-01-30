# Why is Object Oriented Programming Useful? (With an RPG example, Javascript and Java)

Dec 3, 2015

This article is an adaptation of the article written by Al Sweigart and posted at the  [Invent with Python blog](http://inventwithpython.com/blog/2014/12/02/why-is-object-oriented-programming-useful-with-an-role-playing-game-example/). I found it one of the best ways to display the advantages of OOP but thought Python was not the best option for examples that clearly displayed each concept. Because of this, I adapted his article using Javascript for the initial examples and Java for the Object Oriented topics. I also changed some of the examples for to suit how I wanted to explain a scenario. This article assumes you have fundamental programming knowledge, like what are functions and variables, but mostly only that. Good reading.

UPDATE 2019-01-29: The original blog post in invent with python is no longer available, so here's the web archive [copy](https://web.archive.org/web/20141228214354/http://inventwithpython.com/blog/2014/12/02/why-is-object-oriented-programming-useful-with-an-role-playing-game-example/).

----------

This blog post is for those people still new to programming and have probably heard about “object-oriented programming”, “OOP”, “classes”, “inheritance/encapsulation/polymorphism”, and other computer science terms but still don’t get what exactly OOP is used for. In this post I’ll explain  _why_  OOP is used and how it makes coding easier. This post uses Javascript and java code, but the concepts apply to any programming language.

There are two key non-OOP concepts to understand right off the bat:

1.  **Duplicate code is a Bad Thing.**
2.  **Code will always be changed.**

Aside from small “throw-away” programs that are written for some single task and only ran once, you’ll almost always need to update your code to either  **fix bugs**  or  **add new features**. A large part of writing good software is writing software that is readable and easy to change.

If you have copy/pasted code in your program, changing it requires making the same change to multiple places in your program. This is problematic. If you miss applying the change to some places, you’ll fail to fix the bug everywhere or implement the new feature inconsistently.  **Duplicate code is a Bad Thing**.  Having duplicate code in your program is setting yourself up for bugs and headache.

Functions let you get rid of duplicate code. You write the code in a function once, and just call the function everywhere your program needs that code run. Updating the function’s code updates it everywhere the function is called automatically. Just as functions makes updating code easier, using object-oriented programming techniques also organizes your code to make changes easier.  **And code will always be changed**.

## The Role Playing Game Example

Most OOP tutorials stink. They have “Car” classes with “Honk” methods and other examples that are unrelatable to the actual programs that new coders write or have used before. So this blog will use an RPG-style video game (think World of Warcraft, Pokemon, or Dungeons & Dragons). We’re used to thinking of things in these games as a collection of integers and strings. Check out all the numbers on this Diablo status screen or D & D character sheet:

![](https://cdn-images-1.medium.com/max/1600/1*zigVhhvuKY4Bm553IrQ9fA.jpeg)

![](https://cdn-images-1.medium.com/max/1600/1*sKD19IRu0bNpjsH7Tq0C_A.jpeg)

Taking out the graphics from these RPG video games, the characters, armor, and other objects are just a bunch of integer or string values in variables. Without using object-oriented concepts, you could implement these things in Javascript code like this:
```javascript
var name = "Elsa";
var health = 50;
var magicPoints = 80;
var attack = "Axe";

print(name + " has " + health + " hit points");
```

The above variable names are pretty generic. To add monsters in this program, you’ll need to rename the player character’s variables and make new ones for the monsters:
```javascript
var heroName = "Elsa";
var heroHealth = 50;
var heroMagicPoints = 80;
var heroAttack = "Axe";

var monsterName = "Goblin";
var monsterHealth = 20;
var monsterMagicPoints = 0;
var monsterAttack = "Dagger";

print(heroName + " has " + heroHealth + " hit points");
print(monsterName + " has " + monsterHealth + " hit points");
```

But of course you’ll want to have more than one monster, but then you’ll have variables like  `monster1Name`,  `monster2Name`, etc. That’s a poor way to code, so maybe you’ll make the monster variables into arrays:
```javascript
var monsterName = ["Goblin", "Dragon", "Orc"];
var monsterHealth = [20, 300, 40];
var monsterMagicPoints = [0, 200, 0];
var monsterAttack = ["Dagger", "Fireball", "Sword"];
print(monsterName[0] + " has " + monsterHealth[0] + " hit points");
```

Then you could just have the goblin’s stats be the values at index  `0`  in the lists, the dragon’s stats are at index  `1`, and the orc’s stats are at index  `2`. This way you could have multiple monsters contained in these variables.

But this kind of code easily leads to bugs. If your lists get out of sync, the program will no longer work correctly. Say the player vanquishes the goblin at index  `0`  and the program calls a  `vanquishMonster`  function. We created a  `remove`  function for removing items from an array, but the  `vanquishMonster` function has a bug and deletes the values from all the lists except (accidentally)  `monsterAttack`:
```javascript
function vanquishMonster(monsterIndex) {
  remove(monsterName, monsterIndex);
  remove(monsterHealth, monsterIndex);
  remove(monsterMagicPoints, monsterIndex);

  //Note there is no remove for monsterAttack
}

vanquishMonster(0);
```
The monster lists end up looking like:
```javascript
monsterName = ["Dragon", "Goblin"] 
monsterHealth = [300, 18]
monsterMagicPoints = [200, 0]
monsterAttack = ["Dagger", "Fireball", "Sword"]
```
Now it looks like the dragon’s attack is the goblin’s attack. And the Orc now has the attack the dragon had, imagine a dragon fumbling with a dagger and an orc throwing fire balls.  **This is quickly getting out of hand**.  The problem is that you have  **one**  monster’s data spread out over multiple variables. You could fix this by putting a single monster’s data into a dictionary, and then have a list of dictionaries:
```javascript
monsters = [{"name":"Goblin", "health":20, "magicPoints":0, "attack":"Dagger"},
{"name":"Dragon", "health":300, "magicPoints":200, "attack":"Fireball"},
{"name":"Orc", "health":40, "magicPoints":0, "attack":"Sword"}]
```
Gah. This code is getting complex. For example, if we add an inventory for each monster, it would be a dictionary-in-a-dictionary-in-a-list. What if things like spells or inventory items also have their own attributes and need to be dictionaries? What if an inventory item could be a sack, which itself contains other inventory items? This monsters dictionary would get intense.

This is something that object-oriented programming can solve by creating a new data type.

## Creating Classes

**Classes are the blueprints for a new data type in your program.**  Object-oriented programming provides a new approach for modeling the armor, monsters, etc. much better than a hodge-podge of lists and dictionaries, even though OOP concepts take some getting used to. Here we can see how to do this moving from Javascript to Java.

In fact, since the hero will have all the same features of monsters (health, inventory, etc.) we can just have a generic  `LivingThing`  class that the hero and monsters share. Your code can be changed to look like this:
```java
public class LivingThing {
  public String name;
  public int health;
  public int magicPoints;
  public String attack;

  public LivingThing(String name, int health, int magicPoints, String attack) {
    this.name = name;
    this.health = health;
    this.magicPoints = magicPoints;
    this.attack = attack;
  }
}

LivingThing hero = new LivingThing("Elsa", 50, 80, "Axe");
LivingThing[] monsters = new LivingThing[3];
monsters[0] = new LivingThing("Goblin", 20, 0, "Dagger");
monsters[1] = new LivingThing("Dragon", 300, 200, "Fireball");
monsters[2] = new LivingThing("Orc", 20, 0, "Sword");
```
Hey look, using classes has already allowed you to use the same code for both player characters and monsters.

In the above code you are defining a new data type/class (pedantics aside, the two terms are basically the same. See also:  [Stack Overflow — What’s the difference between a type and a class?](https://stackoverflow.com/questions/468145/what-is-the-difference-between-type-and-class)) called  `LivingThing`. You can have  `LivingThing`  values/objects (again, two terms for basically the same thing) just like you can have integer values, string values, or boolean values.

Some Java-specific details about the above code:
```java
public class LivingThing {
```
The above is a class statement that defines a new class, just like function statements define a new function in Javascript. The class’s name is `LivingThing`.

```java
public String name;  
public int health;  
public int magicPoints;  
public String attack;
```
These are the properties, or member variables, of the `LivingThing` class. The idea is the same as local variables in Javascript, but they’re attached to the class, just like the members of an array, or the items in a dictionary.
```java
public LivingThing(String name, int health, int magicPoints, String attack) {
```
This is a function, or method, definition. The method is a function that is attached to a class, in the same way the properties belong to the class.  
When a method has the same name as the class, it is called a constructor, because it’s the method use to build new objects for this class.
```java
this.name = name;  
this.health = health;  
this.magicPoints = magicPoints;  
this.attack = attack;
```
This is the constructor’s body. There are two important things to notice here:

1.  the `.`  operator, used to access an object’s properties or methods
2.  the  `this`  keyword, used to access the current object, the one object that was created with the constructor.  `this.name = name`  means “fill the object property ‘name’ with the value of the argument ‘name’ ”. It’s also useful to avoid name collision and having to give different names to the constructor arguments
```java
LivingThing hero = new LivingThing("Elsa", 50, 80, "Axe");  
LivingThing[] monsters = new LivingThing[3];  
monsters[0] = new LivingThing("Goblin", 20, 0, "Dagger");  
monsters[1] = new LivingThing("Dragon", 300, 200, "Fireball");  
monsters[2] = new LivingThing("Orc", 20, 0, "Sword");
```
When creating a new object in Java, we always need to use the  `new`  keyword, and then use the constructor we defined. The above  `new LivingThing(“Elsa”, 50, 80, “Axe”)`  call creates a LivingThing object and it’s stored in the  `hero` variable. The above code also creates  `LivingThing`  objects for the three monsters and stores them in the  `monsters`  array.

And here’s where we begin to see the benefits of object-oriented programming. If another programmer is reading your code, when they see the  `LivingThing()`  call they know they can just look for `class LivingThing` and find out the details of everything they need to know about what a  `LivingThing`  is.

But an even bigger benefit comes when you try to update your  `LivingThing` class.

## Updating Classes

Say you wanted to add “hunger” levels to your RPG. If a hero or monster has a hunger level of 0, they are not at all hungry. But if their hunger level is above 100, then they take damage and their health value decreases each day. You could change your class to this:
```java
public class LivingThing {
  public String name;
  public int health;
  public int magicPoints;
  public String attack;
  public int hunger;

  public LivingThing(String name, int health, int magicPoints, String attack) {
    this.name = name;
    this.health = health;
    this.magicPoints = magicPoints;
    this.attack = attack;
    this.hunger = 0; // all living things start with hunger level 0
  }
}
```
Without changing  **any**  other line of code,  **every**  `LivingThing`  object in your game now has a  `hunger`  level! You don’t have to worry about some  `LivingThing` objects having the  `hunger`  property and some not: the very definition of what a  `LivingThing`  is has been updated.

You also don’t have to change any of the constructor calls since you didn’t add a new hunger level parameter to the constructor method. That’s because `0` is a good common-sense default value for a new  `LivingThing`  object’s hunger level. If you do add a new parameter to constructor for the hunger levels, you’ll have to update all the code that calls the constructor. But this is true for any function anyway.

If your RPG has a lot of default values for things, you can avoid a lot of “boilerplate” code by using classes with a constructor that sets the default for you.

## Methods

Methods are useful for running code that affect the object itself. For example, you could just have code that changes the health of a LivingThing object directly:
```java
hero = LivingThing("Elsa", 50, 80, "Axe");
hero.health -= 10  // Elsa takes 10 points of damage
```
But this isn’t a very robust way to handle taking damage. Lots of other game logic needs to be checked whenever something takes damage. For example, say you want to check if a character dies each time they take damage. You would need code like this:
```java
hero = LivingThing("Elsa", 50, 80, "Axe");
hero.health -= 10;  // Elsa takes 10 points of damage
if (hero.health < 0) {
    print(hero.name + ' has died!')
}
```
The problem with the above approach is that you would need that check everywhere your code decreases the health of a LivingThing object. But **duplicating code is a Bad Thing**. The non-OOP way to prevent duplicate code would be to put it in a function:
```java
public void takeDamage(livingThingObject, dmgAmount) {
    livingThingObject.health = livingThingObject.health - dmgAmount
    if (livingThingObject.health < 0) {
      print(livingThingObject.name + ' is dead!')
    }
}

hero = LivingThing("Elsa", 50, 80, "Axe");
takeDamage(hero, 10);  // Elsa takes 10 points of damage
```
This is a better solution because any updates to  _takeDamage()_  (such as factoring in armor, protective spells, bonuses, etc.) just have to be added to the  `takeDamage`  function.

However, the downside is that when your program grows in size, it’s easy for  `takeDamage()`  to get lost in among them. It isn’t so clear that  `takeDamage()` is related to the  `LivingThing`  class. If you have hundreds of functions in your program, it will be hard to figure out which ones are related to the  `LivingThing` class.

The solution is to turn this function into a  `LivingThing`  method:
```java
public class LivingThing() {
    // ...other code in the class...

    public void takeDamage(int amount) {
        this.health = this.health - amount;
        if (this.health <= 0) {
            print(this.name + " is dead!");
        }
    }
}

hero = LivingThing("Elsa", 50, 80, "Axe");
hero.takeDamage(10)  // Elsa takes 10 points of damage
```
Once your program has many classes, each with many methods and member variables, you will begin to see ho`w OOP can help organize your programs to be more manageable.

## Visibility

Methods and member variables can be marked as `public` or `private`. In Java, you can also set a method or property as `protected` or `default`, which are intermediary values, but won't be discussed here. 
`public` methods can be called by anyone and `public` member variables can be set by any code, inside or outside of the class. 
`private` methods can be called only inside the class and `private` member variables can only be read or set inside the class.

In Java, this “can be called/set” is called visibility and is strictly enforced by the compiler. The compiler will tell you if you’re trying to acess a method or property that you don’t have permission to use.

The reason for having this public/private distinction is to set expectations for how the class interacts with outside code. (See also:  [Stack Overflow — Why “private” methods in the object oriented?](https://stackoverflow.com/questions/2620699/why-private-methods-in-the-object-oriented)) The programmer of the class expects that other people won’t write code that calls the private methods or sets the private member variables.

For example, the  `takeDamage`  method factors in checking for death if health falls below `0`. You may want to add all sorts of other checks in the code. Things like armor, agility, and protective spells could factor in to reduce the damage taken. The  `LivingThing`  object might be wearing an enchanted cape that  **heals**  them by adding the damage amount to their health instead of subtracting. All of this game logic can go in the  `takeDamage`  method.

But all of OOP organizing is for nothing if you accidentally put this code in there:
```java
class LivingThing() {
  // ...code in the class...
}

hero = LivingThing("Elsa", 50, 80, "Axe");

// ...some more code...

if (someCondition) {
    hero.health -= 50;
}
```
That  `hero.health -= 50`  will subtract 50 points of health, without taking into any consideration what armor Elsa is wearing, if she has protective spells, or is wearing that enchanted healing cape. This code bluntly decrements  `health`  by 50.

It’s easy to forget about the  `takeDamage`  method and accidentally write code like this. This doesn’t check if the hero object’s health property has dropped below 0. The program continues as though Elsa is alive even if she has negative health! This is a bug we can avoid with public/private members and methods.

If you change the  `health`  member variable to  `private`, then the compiler will not let you make that mistake:
```java
public class LivingThing {
  public String name;
  private int health;
  //class other code
}

hero = LivingThing("Elsa", 50, 80, "Axe");

// ...some more code...

if (someCondition) {
    hero.health -= 50 //Compiler error, you cannot access health outside the class 
}
```
It is impossible to write a program that illegally accesses a private member or method. Object-oriented programming helps prevent these kinds of bugs. Even with techniques like Java’s Reflection ([Stack Overflow: What is reflection](http://stackoverflow.com/questions/37628/what-is-reflection-and-why-is-it-useful)), you won’t be able to access a private property or method outside the class.

**Inheritance**

Using the  `LivingThing`  class for dragons is nice, but dragons have a lot of other qualities in addition to the ones provided by  `LivingThing`. So you want to create a new Dragon class that will have member variables like  `airSpeed`  and  `breathType`  (which can be a string such as ‘fire’, ‘blizzard’, ‘lightning’, ‘poison gas’, etc).

Since Dragon objects will also have  `health`,  `magicPoints`,  `attack`, and all the other things that  `LivingThing`  objects have, you  **could**  just create a new Dragon class and copy/paste all the code from your  `LivingThing`  class. But this would result in duplicate code, which is a  **Bad Thing**.

Instead, make a Dragon class that  **inherits**  the `LivingThing`  class:
```java
public class LivingThing() {
    // ...living thing code...
}

public class Dragon extends LivingThing {
  //  ...Dragon-specific code...
}
```
This is effectively saying, “A  `Dragon`  is the same as a  `LivingThing`, with some additional methods and properties”. When you create a `Dragon` object, it will automatically have all the same methods and properties as  `LivingThing`  (saving us from duplicating the code). And it can also have dragon-specific methods and properties attached to it. Another way of saying this is that `Dragon` is a **subclass** of `LivingThing`.
Further, any code that deals with a  `LivingThing`  object can automatically handle a Dragon object because `Dragon` objects  **are**  `LivingThing` objects. This principle is called  **polymorphism**.

In practice, inheritance is easy to abuse though. You must be certain that any conceivable change or update you make to the  `LivingThing`  class would also be something you would want the  `Dragon`  class and  **every other**  subclass of  `LivingThing`  to also have. This might not always be so straightforward.

For example, what if you created  `Monster`  and  `Hero`  subclasses of the  `LivingThing`  class, and then created  `FlyingMonster`  and  `MagicalMonster` subclasses from  `Monster`. Would the new `Dragon` class be a subclass of  `FlyingMonster`  or  `MagicalMonster`? Or maybe just its own subclass of  `Monster`? One solution for this problem is  **interfaces**. An interface is like a class, but without any implementation, it’s like a contract: any class that uses this interface is bound to have this methods and properties. Another difference is that while a class can only extend  **one**  other class, it can use several interfaces, since interfaces don’t have implementation, the class is only saying it will have to implement the methods described in those interfaces. To further differentiate class and interfaces, we use the `interface` keyword when defining interfaces and we use the  `implements` keyword with when a class uses that interface:
```java
public interface FlyingMonster {
  
  public void fly(int speed);
}

public interface MagicMonster {
  
  public void spendMana(int points);
}

public class Dragon extends LivingThing implements FlyinMonster, MagicMonster {
  //class code here
}
```
Even so, this is where inheritance and OOP start to get tricky and religious arguments over the “correct” way to design classes come about. I want to keep this blog post short and simple, so I’ll leave these topics as exercises for the reader to look into. (See also  [Stack Overflow — Prefer composition over inheritance?](https://stackoverflow.com/questions/49002/prefer-composition-over-inheritance)  and  [Wikipedia — Composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance))

## Summary

I hate programming tutorials for beginners that start with object-oriented programming. OOP is a very abstract concept. Until you have some experience and are writing large programs, you won’t understand why using classes and objects makes programming easier. Instead, the neophyte is left with a steep learning curve to climb and no idea why they’re climbing it. I hope the RPG example has at least given you a glimpse as to why OOP can be helpful. There is MUCH more to OOP. If you want to learn more, try googling for “object oriented best practices” and “object oriented design patterns”.

If you are still confused by OOP concepts, feel free to write programs without classes. You don’t  **need**  them and they can result in over-engineered code. But once you have some coding experience under your belt, the benefits of object-oriented programming will become apparent. Good luck!