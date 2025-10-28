# LambaMOO Programming Tutorial (very rough draft)

_by Steven J. Owens (unless otherwise attributed)_

MOO, of which LambaMOO is the first and biggest, is an interactive, multiplayer online game that includes the ability for players to build and even program new things or features into the game. The language lacks a formal name but is generally referred to as "moo code" or sometimes "moocode".

While there is a fair amount of information about LambdaMOO programming in various places, there is also a lack of a comprehensive explanation, especially of much of the programming environment. These incomplete notes are the start of an attempt to remedy that. This is just a beginning, but maybe someday it'll be an end.

There are a bunch of other resources out there, starting with the LambdaMOO Programmer's Manual:

[http://www.moo-cows.com/docs/manuals/ProgrammersManual.html](http://www.moo-cows.com/docs/manuals/ProgrammersManual.html)

Also there are a variety of tutorials. I'm not trying to duplicate or replace them. Instead, this is a combination of filling in the gaps about the MOO programming environment and jumpstarting you into MOO programming.

### Outline of Current Draft

*   A Brief History of MUDs and MOOs
*   Basic Terminology
*   The Server and the Db
*   Objects, Verbs and Properties
*   Object Oriented
*   Core Db, MinimalCore, LambdaCore
*   Built-in Functions, Utils and the System Object
*   The Root Class, Nothing and $nothing
*   Verb Invocation and Property Access
*   Data Types
*   Lists
*   Eval
*   Comments
*   Variable Declaration and Scoping
*   Perms and Args
*   Flow Control, for, if, while, suspend, fork
*   Type Conversion
*   Threading, Ticks and Tasks
*   Core Classes
*   Sight and Sound in MOOCode
*   Location in MOOCode
*   The MOO Help System

**Note:** _See end for draft outline of planned revision_

## A Brief History of MUDs and MOOs

The following is all off the top of my head and based on my
recollections.  I don't care enough to try to google on stuff.

### MUD and MUD2

MUD and MUD2 were the granddaddies of it all, written by Richard
Bartle and Roy Trubshaw, back in the UK in the late 70s/very early
80s.

MUD stood for "Multi User Dungeon" and it was a very hack & slash D&D
sort of experience. Later, other people tried to make it sound more
legitimate by redefining it as "Multi User Dimension."

### AberMUD

A while after MUD2, Alan Cox (who later became a major Linux
kernel dev) and friends at the university of Aberswythe tried to
recreate MUD2, or at least a MUD2-like thing.

### LPMud

Written in the late 80s by Lars Pensjo (except I misspelled
that because it's a Finnish name and there's an umlaut or something
over the "o").  For some reason they downcased the "MUD" acronym.

In general LPMuds had a very hack & slash D&D flavor/culture.  There
was no player persistence on LPMuds; when you quit the game, your
player character disappeared and their stuff was left on the ground.

LPMud was programmable in a sense, though I never programmed in an LP.
There was no provision for regular players to program/customize LPMud.

I didn't spend much time on LPMuds.

### DikuMUD

Written in 1990 and first opened to the public in 1991, DikuMUD was,
like LPMud, inspired by AberMUD, but was intended to be an alternative
to LPMud.  I don't know much about it, I don't know that I ever logged
into one, though it's quite possible.

### TinyMUD

Written in the late 80s by Jim Aspnes at Carnegie Mellon University,
in Pittsburgh.  Eponymous with the TinyMUD that Aspnes ran at CMU for
a few years, which was later, after it was no longer regularly
running, renamed "TinyMUD Classic" to distinguish it from later
TinyMUDs.

TinyMUD was the first MUD I ever played.

TinyMUD was specifically designed to be lightweight in terms of system
resources, hence the "tiny".

Player character objects were persistent in TinyMUD.

TinyMUD was very much a "social" MUD, maybe a "role playing, not roll
playing" MUD.  It had zero game/RPG support.

TinyMUD was the first, or one of the first, MUDs to allow all players
to build stuff and it supported complex boolean locks that could,
combined with player-buildable rooms and exits, be used to construct
interesting puzzles.

TinyMUD was also, my friends recollect, the source of using the
convention of prefixing server commands with the "@" character,
i.e. "@commandname", as opposed to the commands used for regular
interaction within the user-created world of the TinyMUD, which had no
prefix.

Around about then, there was an explosion of different MUD types.  My
impression at the time was that a lot of them were TinyMUD
derivatives, TinyMUSh and TinyMUCK being two of the more popular
flavors that I can think of, offhand.  TinyMUSH had a macro system and
TinyMUCK was programmable via a Forth-like language (which is a bit of
a niche type of programmign language.  LPMud still existed, and there
was

### MOO

Around 1990, a guy named Stephen White, aka Ghondarl or Ghond, created
MOO, which stands for MUD, Object-Oriented.  "Object oriented" is term
for a particular kind of programming language, I get a little bit into
that further down.

MOO had a limited programming language, perhaps more of a
macro language.

I was an acquaintance of Ghond's, I logged into his MOO (retroactively
named "AlphaMOO" by the MOO community) for an hour or two, while he
was working on it.

### LambdaMOO

Early 1991, Pavel Curtis opened LambdaMOO.

For LambdaMOO Pavel developed the moo coding language (unnamed but
referred to generally as "moo code") into a significantly more
powerful and sophisticated programming language than MOO.  White has
been quite clear that he credits Pavel with doing major work and
considers LambdaMOO to be Pavel's baby.

MOO and LambdaMOO's code are entirely unrelated to TinyMUD and
similar, but it was philosophically descended from TInyMUD, in that it
emphasized the social aspect, and users being able to build "live" in
the MOO, via in-MOO commands.

LambdaMOO enables regular users to program in the MOO.  To do this,
you need to have the MOO object that represents your player character
be programmer enabled, this is done by setting a flag on your player
object; this flag is referred to as the "programmer bit", just an
object property that is set to the value 1 to enable it.  LambdaMOO is
profligate in handing out programmer bits.  Some MOOs are not.

Most people refer to the underlying server of LambdaMOO as simply
"MOO". Ghondarl's original MOO is pretty much nonexistent at this
point.


#### Servers and DBs and Cores

There's the MOO server code, and there's the database of in-MOO
created code, referred to as the "db".

The "db" is generally broken down, mentally, into the "core db" that a
given MOO was started with, and then all the user-created content that
came later.

lambdacore is the full-featured db core from the original LambdaMOO.

minimaldb is the bare bones, stripped down core if you want to start
from scratch, or as nearly from scratch as is reasonable.

JaysHouse was another popular, programmer-heavy MOO which produced a
core db that saw some popularity.

In general, MOO coding is what's called in the programming world a
"live coding" system, meaning you can interact with the running system
and modify the code "life", and the changes take effect immediately,
without having to restart/reload the system.

Smalltalk is one of the more famous "real programming languages" that
provided live coding.

Everything in MOO is an object.  Objects can have data attached to
them, stored in "properties", and code attached to them, stored in
"verbs".

#### LambdaRPG

MOO has no built in RPG mechanics, but you can easily build one in MOO
code.

It gets a bit tricky if you're not a wizard, because non-wizards
cannot make changes to the core code that everything else is based on.

LambdaMOO has a non-wizard-coded RPG system, LambdaRPG but it was
awkward to code.

There have been various MOOs that had wizard-built RPGs, I don't know
of any RPG core db that's widely available.

The LambdaRPG system still exists on LambdaMOO, login and do "help
lambdarpg".  You might need to add the RPG help database object, #17233,
to your player object's .help property.

## Basic Terminology

I'm going to assume you have a basic familiarity with programming concepts, but don't get too stressed out if you don't, you'll be able to pick that up as you go along. If you're totally, completely new at this, I explain some general concepts at the end of this page, in a section titled: _Real Basics of Programming in Moocode_

I'm going to assume you have a basic familiarity with muds and with moo concepts, but just to be clear:

#### Objects, Verbs and Properties

MOO is a mud that consists of objects.

MOO objects have properties and verbs.

Properties are values stored on the object and referenced in code via the "." syntax, for example:

```somevariable = object.propertyname ;```

Verbs are code stored on the object and invoked in code via the ":" syntax, for example:

```somevariable = object:verbname() ;```

Note that for the most part, properties and verbs are defined in moocode, but there are 8 built-in properties, i.e. properties that ALL objects have, inherently, without having been added via moocode programming commands:

```
;$code_utils.builtin_props
=> {"name", "r", "w", "f", "programmer", "wizard", "owner", "location", "contents"}
```

For more info on the built-in properties see the LambdaMOO Programmers Manual, "Properties On Objects":

[https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC6](https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC6)

#### Object Oriented

MOO stands for "Mud, Object-Oriented". If you don't understand what "object oriented" means, it's an approach for organizing the code and data in a system. I've added a brief description below.

MOO's object-oriented approach is slightly different from many object-oriented programming languages. In most object-oriented languages, you have a division between the definition of an object (its blueprint, so to speak) and instances of the object in the system. The object definitions (called classes) exist off in some abstract place that the rest of the code generally can't get at, your code in the system never deals with them directly. Instead your code creates "instances" of a given class and use the instance.

In the MOO world, unlike in those other languages, the object is defined by example. You create an object instance in the system and then dynamically (aka "on the fly") add verbs (using the @verb command) and properties (using the @property command) to your object instance to make your prototype. Then you can create a new object that is "descended" from the first object. The new object in turn can be dynamically modified with more verbs and properties, and then you can create more objects that descend from the second object.

Another way that MOO is fairly unusual is that every object in the moo server's db has a number associated with it, often abbreviated "objnum" in moocode.  For example, #1449 is the object number of my player object at LambdaMOO.  There's more on object numbers below, I'm mainly bringing them up here for two reasons.  First, because if you already know some other programming language, object numbers may strike you as a bit weird.  Second, because if you don't already know how to program, there's a strong temptation to put the actual object number value in your code.  

In the general programming world this is often called "hardcoding" the value, and a hardcoded number is sometimes called a "magic number".  Generally speaking, it's considered bad style.  In moocode specifically, it's recommended that you put the necessary object number value in an object property and reference that property.

If object-oriented programming is new to you, here's a brief explanation:

The simplest approach is procedural - a sequential series of instructions. Procedural languages have concepts for organizing the code, like subroutines (also called functions or methods, depending on what programming language you're talking about).

Object-oriented languages usually have their own version of subroutines (typically they're called methods, or in MOOcode, verbs), but they also have the concept of organizing a set of subroutines (methods, verbs) and data (instance variables, or properties) into a single group. This conceptual grouping is what we call an "object".

### The Server and the Db

You have the server and the database (colloquially "the db"). The server is the foundation; it loads and runs the database, which defines the objects and their properties and verbs. Much of the code that makes the up the basic moo-ishness of things is implemented in the db, in moocode.

**Note:** _Most people tend to associate the term "database" with a "relational database", and most relational database programs tend to keep most of their data in disk storage. The purely technical meaning of "database" is "an organized collection of information." LambdaMOO's database is not relational, it is an object database, and it is kept entirely in-memory. The only reason I'm pointing this out is to head off any chance of you confusing the moo database for a relational database._

#### Built-in Functions, Utils and the System Object

The moo language has a number of predefined functions, which are generally referred to as functions or sometimes as "built-in functions" or "built-ins" for short.

Most built-ins are documented in MOO's online help. Sometimes there's more than one help entry that matches a built-in function name, so as a rule of thumb append an empty parentheses to the functionname() when you use the help command. For example "help tonum()" will get you help on the built-in function named tonum (see "Data Types", below).

In addition to the built-ins, there are a large number of handy little
utility verbs. Since verbs have to be defined on objects, these
utility verbs are organized on several objects, one object per
category of verbs. They are generally referred to as "the utils" or
sometimes "the $utils", because the individual grouping objects tend
to be kept track of in properties on the \#0 object, and you can refer
to these properties by using $propertyname, like $string\_utils, or
$list\_utils.



Speaking of object #0, object #0 is The System Object. This is used as a central place to stick values that the wizards want to keep track of on a moo wide basis, in properties on #0\. You can reference these properties with #0.propertyname, but there's a shortcut, as I used above, the dollar sign, $, so you can reference the properties in your code by simply using $propertyname.

Anytime you see $ in a moo verb, mentally translate it to "#0.", i.e.:

    $string_utils:english_ordinal

is really

    #0.string_utils:english_ordinal

There are a great many such useful properties on \#0 besides the utils,
but here are the utils defined on LambdaMOO's \#0 as of this writing:
  * \#0.building\_utils
  * \#0.byte\_quota\_utils
  * \#0.code\_utils
  * \#0.command\_utils
  * \#0.convert\_utils
  * \#0.gender\_utils
  * \#0.generic\_utils
  * \#0.list\_utils
  * \#0.lock\_utils
  * \#0.match\_utils
  * \#0.math\_utils
  * \#0.matrix\_utils
  * \#0.object\_quota\_utils
  * \#0.object\_utils
  * \#0.perm\_utils
  * \#0.quota\_utils
  * \#0.seq\_utils
  * \#0.set\_utils
  * \#0.string\_utils
  * \#0.time\_utils
  * \#0.trig\_utils
  * \#0.wiz\_utils

There are also several verbs defined directly on \#0, but most of them
are system related and are not particularly relevant to your average
moo coder.

#### The Root Class, Nothing and $nothing

Object #1 is The Root Class. All objects are descended from Object #1.

Object #-1 is "nothing" , the null object, the object that does not exist. Things that are not located anywhere in particular have their location property set to #-1\. Verbs or functions that do not come up with a valid result typically return the value #-1.

There's actually a root property, $nothing, that contains the value #-1, but I'm pretty sure that $nothing does not define #-1, it merely points to it and gives you a more expressive way to use the value #-1. I.e. instead of putting the characters #-1 in your code, you put $nothing and anyone reading the code (including you, a few months or years later!) finds it easier to understand what your intent was.

While we're on the topic, besides $nothing there's also $ambiguous_match which equals #-2 and $failed_match which equals #-3.  These are used by the built-in parser.

For more information, see the LambdaMOO Programmer's manual section "MOO Value Types" and "The Built-In Command Parser":

[https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC3](https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC3)

[https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC8](https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC8)

### Verb Invocation and Property Access

In code you invoke verbs with the ":" operator.

```foo = object:methodname() ;```

In code you reference properties to get the values in them with the "." operator.

```foo = object.propertyname ;```

You can generally chain properties for verb or property reference, assuming that each link in the chain is a valid object reference, e.g.:

```title = player.foo.bar:title();```

```name = player.foo.bar.name ;```

Here's a more realistic example of that:

```roomtitle = player.location.title();```

```name = player.location.name ;```

### Case Insensitivity

Moo, unlike unix, linux, etc, doesn't care whether letters are upper case or lower case. This seems to be generally true for variable names, object names, verb names, etc. I guess Pavel (or Ghond before him) just didn't want to deal with an endless number of users getting their capitalization mixed up and screaming "This is broken!".

```
>;;foo=5; me:tell(Foo)
5
=> 0
[used 154 ticks, 0 seconds.]
>;;foo=5; me:tell(FOO)
5
=> 0
[used 154 ticks, 0 seconds.]
>;;foo=5; me:tell(fOO)
5
=> 0
[used 154 ticks, 0 seconds.]
>
```

### Data Types

MOO has four data types:

*   numbers (integers and floats)
*   strings (i.e. text)
*   lists of elements
*   moo object numbers (sometimes called objnums).

Numbers default to integers. Integers is a math term commonly used in programming languages, it means no fractions or decimal places. If you divide 1/2 in MOO, you get 0\. Surprisingly, you get get a heck of a lot done with just integers.

Originally MOO only did integers, but these days MOO also does floats, aka floating point numbers, i.e. numbers with a decimal point. That's how MOO knows that a number is a float, by the presence of a decimal point:

```
somefloat = 5.0 ;
someotherfloat = 5.0/2.0;
otherotherfloat = somefloat/someotherfloat ;
```
In the example above, "someotherfloat" will contain the value "2.5" and "otherotherfloat" will contain the value "2.0".

Note:  It's called a floating point number because we don't have infinite memory, so we have to be clever about how we store the value in memory. In the case of floating point numbers, we store a decimal as essentially an integer plus an extra bit of data that tells us what column the decimal point goes in.  I.e. the decimal point "floats" around the integer.

You can't mix integers and floats, so in MOOcode you need to either include the decimal point in all the numbers in a statement or use the "tofloat()" built-in function to convert the value in a variable into a float (see "Type Conversion", below). Otherwise you get a type mismatch error:

```
;1/2.0
#-1:Input to EVAL, line 3:  Type mismatch
... called from built-in function eval()
... called from #217:eval_cmd_string (this == #1449), line 19
... called from #217:eval*-d (this == #1449), line 13
(End of traceback)
```

You can use two periods to indicate a range of values, i.e. 1..10 means 1, 2, 3, 4, 5, 6, 7, 8, 9 and 10\. The two periods are called the "range operator" and this mostly comes up in lists (although can also use it in other places, like foreach loops) so I'll talk about it more down in the section titled "Lists".

Strings is just programmerspeak for "text", i.e. "a string of characters". The term dates all the way back to 1959 and the really early computer languages. Moo knows something is text because it's surrounded by double-quotes:

```astringvariable = "some text"```

Lists are really fancy and get their own section (below).

Object numbers are references to objects in the MOO database. Moo recognizes object numbers because they're preceded by a pound sign #, also called a hash sign and officially named the octothorpe :-).

```puffsobjectnumber = #1449```

### Lists

You define a list by enclosing a comma-separated series of elements in curly braces

```foo = {"one", "two", "three"} ;```

```bar = {1, 2, 3} ;```

Note that the elements don't have to all be the same type of value, you can mix and match:

```baz = {1, "two", 3} ;```

You reference a specific element in a list by appending a number in [square brackets] to the variable name. The number is called an _index_, and accessing a list element this way is called indexing:

```xyzzy = foo[2] ;```

1 is the index of the very first item in the list, as you would expect if you're a normal human being:

```
foo = {"a", "b", "c"} ;
player:tell("The element at index 1 is: ", foo[1])
```

The player sees:

```The element at index 1 is: a```

If you're a programmer rather than a normal human being, you expect the index of the very first element to be 0, for technical reasons which I will skip for now.  Getting used to zero-indexing is usually a major early stumbling block for non-programmers, which is probably why moo's inventors (Pavel and Ghond) decided to go with 1 instead. But if you're already a programmer, you're going to expect it to be zero, which is why I'm pointing this out.

Note that lists can contain any sort of data, including other lists:

```foo = {"one", "two", "three", {1, 2, 3} } ;```

Nested lists can get quite complicated. One particularly common form of nested lists is called associative lists, or alists for short, and the $list_utils have a number of special verbs for dealing with alists. Other languages usually use what's called a dictionary type (often "dict" for short) or "hashtable" or "map" to serve the same purpose.

**Note:** _hashtable as a name describes how it's implemented; map as a name describes the act of using one value to look up another value. I'm explaining this mainly because the terms sometimes come up in programming discussions, so it's useful to be familiar with them._

```somealist = {{1, "January"}, {2, "February"}, {3, "March"}}```

Note that the above isn't really a good example of an alist, since you could just make a list of month names and use the position (index) of each month name instead of needing use the associative list utility functions to look them up. Here's a more realistic example, where you're storing players high scores in some in-moo game, using their object numbers as the key:

```somealist = {{#1449, 65050}, {#68, 743634}, {#107439, 34544}, {#93202, 235423}, {#94285, 10025}}```

Most of the examples above are lists with elements that are all the same data type, but lists can contain mixed data types:

```{"foo", 1, "bar", 1.5, #-1}```

Sometimes you'll have a list of mixed data types like above, but of course the order of the data types is something your code has to keep track of. For example, you might have a message system where each list contains the time received as an integer, the person the message was from as an objectnumber, and the message text as a string. And then you'd store all of your {time, personnumber, messagetext} lists in another big list.

The trick with lists is not the syntax, it's the way lists are used in day-to-day MOO coding. For example, if you do this, you end up having a list that contains two lists:

```
foo = {1, 2, 3}  ;
bar = {4, 5} ;
baz = {foo, bar} ;
```

baz will now contain {{1, 2, 3}, {4, 5}}

You expand a list with the at sign @:

```
foo = {1, 2, 3}  ;
bar = {4, 5} ;
baz = {@foo, @bar} ;
```

baz will now contain {1, 2, 3, 4, 5}

While you only use { and } when you're creating a list, and @ when you expand a list, the "moo way" is that you do a whole lot of list creation and expansion. For example the "right" way to append to a list is to create a new list and use the @ to expand the old list inside the new list.

```
baz = {1, 2, 3, 4, 5};
baz = {@baz, 6} ;
```

baz will now contain {1, 2, 3, 4, 5, 6}

If you're new at programming, the above is an example of a very common bit of programming idiom that can be a little confusing, so I'm going to take a slight detour to talk about it.  

What you are literally doing here is creating a new list that contains both the contents of the old list, baz, and the new value, 6, and then storing the reference to that new list in the variable name baz, which previously used to store the reference to the old list. In a more longer form, it might look like this:

The second line of the example above actually does two things.  First, it creates a new list {@baz, 6} and second, it stores a reference to that new list in the variable name baz, which previously used to store the reference to the old list.  Let's do it again, only this time make it more explicit what's going on.

```
baz = {1, 2, 3, 4, 5};
bat = {@baz, 6} ;
baz = bat ;
```

We do this because we don't want to have to have all of our code after this point refer to the bat variable instead of the baz variable.  If we don't, now we have two variables to keep track of, and besides we're not going to use the baz variable again, so we might as well re-use it to hold the newly expanded list.

But of course, that's longer and using the extra variable name "bat" doesn't serve much purpose; usually in cases like this you'll never use the bat variable again.

Using the shorter version may seem a little less clear at first, but you end up doing this sort of thing so often that you quickly get used to it, and it's worth shortening it that way because it makes the code less cluttered and that makes it easier to read.

**Note:** _Sometimes it may make the code more clear if you be more explicit, as in the longer version. Sometimes making it shorter makes that bit a little less clear but makes it easier to understand the rest of the code. That's a judgement call that you'll learn to make as you get better at programming. In fact a great majority of programming is making that kind of judgement call._

You can use the range operator (..) in a list index to indicate a range of elements. So for example if you wanted to get the second, third and fourth element of a list, you'd index mixedlist[2..4].

```
foo = {"a", "b", "c", "d", "e", "f"}  ;
bar = foo[2..4] ;
```

bar will now contain {"b", "c", "d"}.

MOO list indexes and ranges can't do the things that fancier languages can, they have to be just a single number [4] or two numbers separated by two periods [2..4]. You have to do any fancy math yourself. For example, if you want to get from element 3 to the end of the list, you have to use the built-in function length() to get the length of the list:

```
foo = {"a", "b", "c", "d", "e", "f"}  ;
bar = foo[2..length(foo)] ;
```

You'll also want to take a close look at the $list_utils functions ("help $list_utils").

## Object Verb and Property Basics

In the MOO world you have objects, which have verbs and properties. 

A verb is a method, function, subroutine, whatever. It's invoked on an object using the ":", like so:

```object:verb(argument) ```

A property is a slot on the object where you can store data, any kind of data, whether it's a string, a number, a list, or an object number.

I don't want to duplicate the MOO Programmer's Manual, so go look these up there, for more details, but the basic commands for verbs and properties are:

```
@verb object:verbname this none this
@rmverb object:verbname
@rmverb# object:int

@args object:verbname any any any
@chmod object:verbname +r

@rename object:verbname to newverbname
@rename object:verbname to oldverbname newverbname

@property object.propertyname
@rmproperty object.propertyname

@rename object.propertyname to newpropertyname
@chmod object.propertyname -r
```

Note the ```this none this``` in the ```@verb``` example.  For that, and for the ```@chmod``` command, see "Perms and Args" below, and also read the MOO Programmer's Guide.

There are some gotchas to ```@rename```, see the section below, "Renaming Verbs and Properties".

### Eval

Eval is a really, really handy command that basically allows you to write little one-line MOOCode programs and execute them. It works just the same as say or emote, e.g.

Enter: "foo<enter>  
See: You say, "Foo"

Enter: :grins toothily<enter>  
See: Puff grins toothily.

Enter: ;1+1<enter>  
See: =>2

One minor but important distinction is that only you see the results of your eval.

Eval is handy for exploring and experimenting, and also really useful for setting and looking at property values on your own objects, and directly invoking verbs.

One of the first property values you should set with eval is your player.eval_env property. This may already be set for you; to check, just type the following:

```;player.eval_env<enter>```

You should see:

```=> "here=player.location;me=player;"```

If it's not set, type the following:

```;player.eval_env="here=player.location;me=player;"<enter>```

Once you execute this command, you'll be able to use the eval shortcuts "me" for your player object, and "here" for your player object's current location. This turns out to be incredibly handy.

There's also a nifty little property, player.eval\_ticks. See the
section below "Threads, Ticks and Tasks" for more on that.

I never really got in the habit of using multi-statement evals. Generally I find when I hit that point that it's simpler/easier to just create a real verb to mess with. "Help eval" will explain how it works, but here's the basics:

You start a multi-line eval with a double-semicolon, i.e.:

```
;;foo = {1, 2, 3}; bar = {4, 5};  for x in ({@foo, @bar}) me:tell(x) ; endfor<enter>
```

Also note that multi-line evals don't automatically print the return value of the statement, so you need to explicitly print whatever you want to see in the body of your eval statement by calling me:tell(). The built-in function toliteral() is helpful for this, you can just call:me:tell(toliteral(whatevervariable)):

```
>;;foo = {"a", "b", "c", "d", "e", "f"}  ; bar = foo[2..length(foo)] ; me:tell(toliteral(bar)) ;
{"b", "c", "d", "e", "f"}
=> 0
[used 161 ticks, 0 seconds.]
```

## Basic MOOCode Gotchas

### Comments

Effectively, MOOCode doesn't have comments. MOO actually has comments, but nobody uses them and nobody, AFAIK, ever has. I can't even remember what the comment character is - // maybe?

The problem is, early MOO was designed with the idea that the users would store/maintain their own source code, and thus the compiler discarded comments, didn't bother to save them in the compiled verb bytecodes. When users used the in-server editing tools, which decompiled the code for the user to edit it, presto, the comments were gone. (This compiling/decompiling also led to the happy accident that code indenting/formatting is defacto specified by the decompiler.)

As a result of this, the custom of using string literals for comments arose. E.g.:

```"this is a string literal" ;```

This is so ingrained that by default the help command "help objectname:verbname" will give the user any string-literal comments at the top of of the verb source.

So, current usage is to use string-literals, as follows:

```"this is a MOOCode faux comment." ;```

Of course, note that you need a semi-colon to terminate the line, and of course any embedded quotes must be escaped with a backslash, i.e.:

```"this is a MOOCode faux comment with some \"embedded quotes\" in it." ;```

## Variable Declaration and Scoping

Objects, and data stored in properties on objects are the only persistent things in the moo world.

Within a verb, variables are dynamically declared, and exist only within the scope of the verb. If you call out to another verb and include parameters in that call, the value is passed, not the original variable (i.e. all verb arguments are pass-by-value). The second verb can modify the parameter value all it wants, but the original variable, in the first verb, will remain unchanged.

Note that although a verb call can pass an object, it's really passing an object reference by value. The second verb can't do anything to that object reference to change the object reference in the first verb. But the second verb _can_ use that reference to access verbs and properties on the object, thus changing the persistent object.

### A Short Digression on Dynamically Typed Variables

Moo code is dynamically typed. This means that you don't have to go through a lot of bureacracy to set up a variable, you can just throw a variable assignment in anywhere and presto! It's a variable.

```foo = 3 ;```

This is a lot more convenient (and fun) but it also gives you a lot more rope to hang yourself. You can be in the middle of a verb and accidentally typo your variable name as "foop" instead of "foot" (don't ask me how you typoed a "p" instead of a "t", maybe you have a weird keyboard layout) and moo will happily create the new variable "foop" and assign it, leading to all sorts of unexpected craziness.

Fortunately, hanging yourself isn't all that dangerous in the MOO world, so you'll usually get a chance to spot the problem and fix it.

However, unlike a lot of dynamically typed languages, moocode isn't as friendly at letting you mix types. In perl, for example, you could do:

```$message = "The number is " + $somenumvariable; ```

And perl will try to Do Something Useful and assume that you really meant to convert $somenumbervariable into a string and concatenate them. However, in moocode, the compiler will complain because somenumvariable is a number, not a string. You have to use the builtin function tostr() to convert it to a string:

```message = "The number is " + tostr(somenumvariable); ```

There are other towhatever() builtins, mainly tonum() which converts to a numeric value and toobj() which converts the value it's given to an object reference number. See "Type Conversion" below.

## Perms and Args

You can see the existing verbs on your object using the @display command (I consider this command indispensable as an object browser).

You can set the permissions using @chmod.

You can set the parser arguments using @args.

Now we get into how the args work. This immediately gets into parsing and matching, since that's the whole point of verb args. These topics deserve a section of their own, and I hope to find time to write it. However, you can't really do any moocoding without some arg basics, so here's a short primer meant to give you just sufficient info to get started.

In moo programming, "arguments" generally refers to the somewhat Zork-like command-line parser, though "@args" refers to the verb arguments.

When somebody types a normal command in moo, whatever they type is fed to a chunk of the moo server code called the The Built-In Parser, hereafter just "the parser".  The parser looks at the words and tries to figure out what verb it should run.  It runs that verb, and it puts the breakdown of the words that were entered into a predefined set of variables, called context variables, that the verb code can then access.

One of those predefined variables is "args", which just contains a list of all the words.  The code in your verb doesn't have to define args, it can just assume it's there and access it.  In your verb code, usually one of the first things you do is use the @ sign (i.e. @args) to expand that list. So "@args" became moo coder shorthand for referring to the parsed verb argument list.

**Note:** _args is what is called a "context variable". There are some other standard context variables, some of them are discussed in the following paragraphs._

Command-line arguments consist of the verb name followed by three args elements, e.g.:

- verbname any in this
- verbname this to any
- verbname any in any
- verbname none none none
- verbname any any any

And so forth.SCram

The "foo preposition bar" form gets automatically parsed by the MOO's command-line parser; it finds the verb name and argument structure that best matches, and invokes it.

"this" is a string that matches of the object the verb is defined on, i.e. the object name or one of the object aliases (see note below on aliases).

"any" is any string. It might match an object in the same location (room) or it might just be a string that the user arbitrarily chose. Your verb code has to decide what to do with that.

**Note:** _Besides the object.name property, which just about every object in the moo db has, most objects also have object.aliases, which contains a list of alternative names for the object. These are also used by the arg parsing and object matching code._

There are some rules for precedence - the parser will try to match to player:verbname() before trying player.location:verbname(), and so forth.

For more information about the possible verb args, see:

[https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC7](https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC7)

### Context Variables

As mentioned above, the "args" variable is automatically available to any code inside a verb. The args variable contains a list of all of the arguments passed into the variable. Since it's so often immediately expanded with the @ sign, it's often referred to in discussion as "@args".

The args variable is one example of a "context variable". There are several of these super handy context variables. Besides the parser-related context variables I'll get into below, there are:

- "this", contains the object reference for the object that the verb is defined on.
- "player", contains the object reference for whichever player started the current chain of execution, usually by invoking a verb interactively, using the command line.
- "here" and "me" aren't actually context variables, but they're customarily defined in your player.eval_env property for use with the eval command, both of which are discussed further below.

**Note:** _player doesn't **always** contain the invoking player's object reference, there's a special built-in function named set_task_perms() that will set it to the verb owner's player object reference, or if the verb owner has wizard perms (player.wizard=1), can be used to set it to an arbitrary player. This gets into tricky moo security topics, however. If you're curious about it, also see the built-in function callers()._

Somewhat related to the topic, there're also several standard error or result code values that are available in verb code, much like the $ variable. These are mainly related to the built-in function typeof(), and are discussed a little in the section "Type Conversion" below.

### Parser-related Context Variables

When the parser invokes the verbs, it pre-populates several special context variables that each verb starts with; argstr, dobjstr, iobjstr, and several others. Let's look at an example:

You have an object named "box" with a "put" verb and the arguments "any in this", which was defined by using the @verb command:

```
@verb box:put any in this
```

You could also define it in two steps:

```
@verb box:put 
@args box:put any in this
```

You type:

```
put gold in box
```

The parser looks for a player:put command, doesn't find one

The parser looks for a room:put command, doesn't fine one

The parser looks for a "gold" with a put command and the appropriate args, doesn't find one.

The parser looks for a "box" with a put command and the appropriate args, and gets a match.

The parser invokes box:put().

The code inside the verb box:put() can assume that there will be a dobjstr (direct object string) variable containing the string "gold" and an iobjstr (indirect object sring) variable containing the string "box".

The parser will also try to match the dobjstr (direct object string) and iobjstr (indirect object string) and set the dobj variable and iobj variable for the verb to any matched objects.

Note that verbs with args of "none none none" aren't meant to have parameters at all.

Args of "any any any" is for verbs that expect to do some sort of custom parsing on their own.

For more information about the Built-In Parser (including a list of the context variables), see:

[https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC8](https://www.hayseed.net/MOO/manuals/ProgrammersManual.html#SEC8)

#### this none this and @chmod +x

There's also a special set of arguments, "this none this" for verbs meant to be invoked only by other verbs:

Generally speaking, if your verb doesn't have its args set to "this none this", you won't be able to invoke it from another verb.

In addition, such not-to-be-directly-invoked verbs must be @chmodded to +x, to allow them to be invoked. If they're not +x, the moo will report a rather confusing "verb not found" error.

If you use the ```@verb``` command with ```this none this``` args, it sets the ```x``` perm to turned on.

```
@chmod +x box:put_helper_verb
```

The reason for this is that not-to-be-directly-invoked verbs are expected (in well-designed code) to do the heavy lifting, the important and tricky stuff, and Pavel (or possibly Ghond) wanted to avoid having lots of half-done code be invocable by anybody.

## Renaming Verbs and Properties

Remember the verb and property commands above, in the section "Object Verb and Property Basics".

There's a minor gotcha to know about with ```@verb``` and ```@rmverb``` when it comes to duplicate verb names or duplicate property names.

The ```@property``` command will refuse to create two properties with the same name:

```
>@create $thing named testthing
You now have testthing with object number #13133 and parent generic thing (#5).
>@property testthing.test 777
Property added with value 777.
>@property testthing.test 778
Property #13133.test already exists.
>@rmproperty testthing.test
Property removed.
```

However, the ```@verb``` command will happily let you create two verbs with the same name, though it will at least warn you -- after the fact:

```
>@verb testthing:test this none this
Verb added (1).
>@verb testthing:test none none none
Warning:  Verb `test' already defined on that object.
Verb added (2).
>@display test:
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
#13133:test                     Puff (#1449)         r d    none none none
-------------------------------- finished ---------------------------------
```

That's at least slightly better than ```@rename```, which won't even warn you.

```
>@verb testthing:test this none this
Verb added (1).
>@verb testthing:foo none none none
Verb added (2).
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
#13133:foo                      Puff (#1449)         r d    none none none
.bar                     Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
>@rename testthing:foo to test
Verb name changed.
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
#13133:test                     Puff (#1449)         r d    none none none
.bar                     Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
```

Note that different args don't change anything when it comes to two verbs with the same name.  The args still have all the other implications, just that they don't matter for verb name matching.  In these examples I'm using two different sets of args, ```this none this``` and ```none one none```, so it's easy to see which verb is which.  

Also note that the ```this none this``` verb has the ```x``` bit turned on, while the ```none none none``` verb does not.  As said above in the section "this none this and @chmod +x", the ```this none this``` args mean it's supposed to be verb that is called from other verbs, not from the parser.  It needs to have the ```x``` bit turned on to be called from another verb, so the ```@verb``` command helpfully does that for you.

If you accidentally end up with two same-named verbs, you have a few options.  

The ```@rename object:verbname to newverbname``` command will affect the first matching verb in the list, so you can rename the first verb to something unique and then use ```@rmverb``` like usual on the second verb.

The ```@rmverb object:verbname``` command will affect the last matching verb, so you can simply ```@rmverb object:verbname``` and that will remove the second verb.

The ```@rmverb# object:int``` command enables you to remove verbs by their order in the list of verbs on the object.  Remember, lists in lambdamoo are one-indexed.

Let's try ```@rename```:

```
>@rename testthing:test to testfoo                                                                                                                                       
Verb name changed.                                                                                                                                                       
>@display test:                                                                                                                                                          
testthing (#13133) [ readable ]                                                                                                                                          
  Child of generic thing (#5).                                                                                                                                           
  Location Puff (#1449).                                                                                                                                                 
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST                                                                                                                          
#13133:testfoo                  Puff (#1449)         rxd    this none this                                                                                               
#13133:test                     Puff (#1449)         r d    none none none                                                                                               
-------------------------------- finished ---------------------------------
>@rename testthing:test to testbar
Verb name changed.
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:testfoo                  Puff (#1449)         rxd    this none this
#13133:testbar                  Puff (#1449)         r d    none none none
.bar                     Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
>@rmverb testthing:testbar
Verb #13133:testbar (2) {none none none} removed.
```

Note in that last message, it referred to ```testbar (2)```.  That ```(2)``` is because it's the second verb in the object's verb list.  That's the int value you would use with the ```@rmverb# object:int``` command.  Let's try that out. 

```
>@verb testthing:test this none this
Verb added (1).
>@verb testthing:test none none none
Warning:  Verb `test' already defined on that object.
Verb added (2).
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
#13133:test                     Puff (#1449)         r d    none none none
.bar                     Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
```

With an object with only two verbs it's easy to figure out the ```int``` param for ```@rmverb#``` but if you have a lot of verbs, it can be a pain. That's when the ```verbs(objectnum)``` function comes in handy:

```
>;verbs(#13133)
=> {"test", "test"}
[used 2 ticks, 0 seconds.]
```

Note that when we used the ```verbs()``` function we had to give it an object number, not the object string.  The ```@verb``` command runs via the moo parser, which does the necessary matching to figure out that "testthing" is ```#13133```, while running ```verbs()``` via eval means we have to figure out the object number ourselves.

Okay, let's go ahead and try ```@rmverb# object:int``` now:

```
>@rmverb# testthing:2
Verb #13133:test (2) {none none none} removed.
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
.bar                     Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
```

Note that @rename works on properties, too, but unlike with verbs, you can't have a property with multiple names.  If you try, you'll just end up with a property with a space in the name, and that'll be impossible to reference:

```
>@rename testthing.bar to foo bar
Property name changed.
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
#13133:test                     Puff (#1449)         r d    none none none
.foo bar                 Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
>;#13133.foo
#-1:Input to EVAL, line 3:  Property not found
... called from built-in function eval()
... called from #217:eval_cmd_string (this == #1449), line 19
... called from #217:eval*-d (this == #1449), line 13
(End of traceback)
>;#13133."foo bar"
Line 1:  syntax error
1 error.
>;#13133.(foo bar)
Line 1:  syntax error
1 error.
>;#13133.'foo bar'
Line 1:  syntax error
1 error.
```

Fortunately, ```@rename``` can fix it:

```
>@rename testthing.foo bar to foo
Property name changed.
>@display testthing:.
testthing (#13133) [ readable ]
  Child of generic thing (#5).
  Location Puff (#1449).
  Size: 0 bytes at Wed Dec 31 19:00:00 1969 EST
#13133:test                     Puff (#1449)         rxd    this none this
#13133:test                     Puff (#1449)         r d    none none none
.foo                     Puff (#1449)          r c    1
-------------------------------- finished ---------------------------------
```


### Flow Control: if, for, while, suspend, fork

A simple list of instructions, one after the other, is technically a program, but you probably want some more sophisticated options than just a straight one-after-the-other list of instructions. You want things like being able to check something and execute one set of instructions or the other (if/endif), or being able to repeat an instruction some number of times (for/endfor) .

The built-in functions suspend() and fork() aren't really technically flow control operators, but they play a similar role, so I'm going to talk about them here.

Some general rules of thumb:

  * Every flow control structure starts with a keyword (if, for, while).
  * Every flow control structure ends with a matching keyword that starts with "end" (endif, endfor, endwhile).
  * The lines between the start and end are called a "block" of code.
  * The line that starts the flow control structure usually contains an expression. 

Let's jump right in and show some examples of if, while, for, suspend
and fork. I'll get into them in more detail after.

Use "if" for conditionals - test some condition, and either carry out
the set of commands in the block or not. 

```
    if (some expression that evaluates to 0 or not-0)
      "do something" ;
    endif
```

You can also use if/else for when you want have a set of instructions
for when the if test results in not-true.

```
    if (some expression that evaluates to 0 or not-0)
      "do something" ;
    else
      "do something else" ;
    endif
```

The while and for flow controls are for looping, i.e. doing something one or more times:

```
    while (some expression that evaluates to 0 or not-0)
      "do something repetitive";
    endwhile

    for foo in (some expression that produces a list)
      "do something with foo" ;
    endfor
```

The built-in function suspend() is for pausing.

```
    suspend(some number of seconds you want the task to pause) ;
```

The built-in function fork() is for starting a new, separate task that continues on and does its own thing.

```
    fork (somenumber of seconds) 
      "do something somenumber of seconds later, in a separate task" ;
    endfork
    "immediately after scheduling the fork, continue on with the rest of the program" ;
```

Note that the number of seconds can be zero (0) so the fork starts right away.

Sometimes that separate task can run for a long, long time, in which case it might be handy to have the taskid:

```
fork taskidvariable (somenumber of seconds) 
  this.task_id = taskidvariable ;
  "do something somenumber of seconds later, in a separate task" ;
endfork
"immediately after scheduling the fork, continue on with the rest of the program" ;
```

The taskidvariable is optional. If it's there, it gets filled with the taskid for the task that the fork creates. But for it to be useful, you have to do something with it.  Typically that's store the taskidvariable's contents in some property, as we did above, so you can later on use it to check on, or if necessary kill, the task.

#### tests

The if and while flow control structures have a "test". In general, this test an expression that evaluates to 0 for false or not-0 for true. Not-0 includes negative values and strings and object numbers. Basically, it includes anything but the number 0.

```
if (0)
  player:tell("This line never gets executed") ;
endif
if (1)
  player:tell("This line always gets executed.") ;
endif
if (player.iftest)
  player:tell("This line only gets executed if player.iftest contains a non-zero value.") ;
endif
while (1 == player.iftest)
  player:tell("This line keeps getting executed until somebody sets player.iftest to something besides 1.") ;
endwhile
```

The test expression is often a comparison, using the double-equal-sign comparison operator (x == y). It returns 1 for true if the two arguments are equal, otherwise returns 0 for false. There's also != for not-equal, which returns 1 for true if they are not equal, otherwise returns 0 for false.

```
if (1 == player.iftest)
  player:tell("This line only gets executed if player.iftest contains the value 1.") ;
endif
```

The test expression can also invoke a verb:

```
if (player:iftestverbname())
  player:tell("This line only gets executed if the verb  player:iftestverbname() returns a non-zero value.") ;
endif
```

#### for loop

For loops in moocode are kinda neat; "for x in (y)" is nice and readable. Hey, Java added something similar (foreach) in 2014, it only took them an extra 17 years (ooh that makes me feel all smalltalkerly/LISPerly inside). The y part of "for x in (y)" can be anything that returns a list, including:

A list slice reference...

```
>;;for x in (me.owned_objects[1..10]) me:tell(x) ; endfor
#1449
#1560
#1565
#1570
#1573
#1574
#1575
#1578
#1579
#1708
=> 0
[used 4001 ticks, 0 seconds.]
```

Or even just a range...

```
>;for x in [1..10] me:tell(x) ; endfor
1
2
3
4
5
6
7
8
9
10
=> 0
[used 3776 ticks, 0 seconds.]
```

## suspend

Suspend is pretty straight-forward; it just pauses the execution for however many seconds.

```
>;;suspend(20) ; me:tell("boo!") ;
...20 seconds pass...
boo!
=> 0
```

## fork

Fork takes a little more discussion. Fork fires off a separate task. In this example I used all of the optional arguments. The somenumber of seconds part delays the new task from starting until some number of seconds in the future. Meanwhile, the rest of the verb continues on without delay.

You're allowed to set the delay at 0 if you just want to fire off the new task immediately. This is useful when you want to do some things that require time (perhaps a scripted set of comments by a robot) but meanwhile you want the rest of the verb to proceed as usual.

The body of the fork is what gets executed by the new task (I usually just put a one-line callout to some other verb in there and put all the gory stuff in the separate verb).

The taskidvariable part lets you define a new variable to hold the taskid of the new task. Of course, you have to do something with the taskid that gets put in that variable, like store it on an object property so you can kill it later with @kill, when it's sucking down all of your ticks like a great tick-sucking vortex of doom.

(But, if you messed up and forgot to save the taskid, check out the @forked command).

Here are some examples of if/for/while/forked in action. Note that I'm demonstrating all of them via eval, so each example is all in one line. In normal code you'd have each bit on a separate line. for example, the first if/else/endif example would be five lines in a normal verb (if, the body of the if, else, the body of the else, endif).

```
>;;if(1) player:tell("foo") ; else player:tell("bar") ; endif
foo
=> 0
>
>;;if(0) player:tell("foo") ; else player:tell("bar") ; endif
bar
=> 0
>
>;;if(-1) player:tell("foo") ; else player:tell("bar") ; endif
foo
=> 0
>
>;; i = 3 ; while(i) me:tell("i is ", i) ; i = i -1 ; endwhile
i is 3
i is 2
i is 1
=> 0
>
>;;fork taskidvar (2) me:tell("later.") ; endfork
=> 0
(two seconds pass)
later.
>
```

### Type Conversion

Moocode isn't friendly enough to auto-convert between types for you, most of the time. You have objects references, ints, strings, floats. Since it's MOO, by definition a lot of the time you're going to be dealing with other people's data.

Use the typeof() function to see what type a particular parameter is.

Use the toliteral() function to convert it to a string you can print out (especially useful for looking at hairy lists-of-lists).

Use the various tofoo() functions to convert back and forth:

*   toobj() (converts a string like "#1449" into an objectnumber reference variable)
*   toint() or tonum() (converts "1" to the value 1; originally ints were the only numerics in moo)
*   tostr() (converts the numeric value 1 to "1" or an objectnumber variable #1449 to "#1449")
*   tofloat() (converts the string "1.1" to the float value 1.1 or the int value 1 to the float value 1.0)
*   toliteral() (converts whatever value to a print-friendly value)
*   typeof() (returns an int value corresponding to the type of the value you passed in)

Note that typeof returns an int value, but there are several standard values that are defined in moocode. INT is 0, OBJ is 1, STR is 2, ERR is 3, etc. See "help typeof" for more info. You could just check to see if the return value is 0 or 1, etc, but it's a lot smarter to use those predefined variables. That way, when you look at a bunch of code you wrote in a drunken binge, you'll have some vague idea wtf you were intending to do.

### Tasks

I just noticed that the official moo help docs don't have anything
comprehensive about task-related built-ins.  The following should be
integrated more cleanly into the rest of this tutorial, but in the
meantime, here's a start.

Here are the non-built-in help entries about tasks that I'm aware of:

*   help tasks
*   help forked-tasks
*   help @forked
*   help @kill

Also see the help entries for the following built-in functions:

*   task_id()   => INT
*   task_stack(INT task-id [, include-line-numbers])  => LIST
*   kill_task(INT <task-id>)   => none
*   suspend([INT <seconds>])   => value
*   queued_tasks()   => LIST
*   resume(INT task-id [, value])   => none

Messing with set_task_perms can be dangerous if you don't know
what you're doing, but if you do, here are the built-ins you want
to know about:

*   set_task_perms (OBJ <player>)   => none
*   caller_perms ()   => obj

Also also, the read() function suspends the current task, this gets a
bit complicated, you can't call read() if you've already called
suspend(), etc. See the help entries for those built-ins.

Also relevant, the fork() built-in (which doesn't seem to have a help
entry) can take an optional argument to give you the task_id of the
forked task:

```
fork task_id_variable (1)
  player:tell("The task ID is ", taskidariable);
endfor
```

Finally, see the original LambdaMOO programmer's manual:

[MOO-Code Evaluation and Task Manipulation](https://tecfa.unige.ch/guides/MOO/ProgMan/ProgrammersManual_54.html)

Which lists:

*   raise()
*   call_function()
*   function_info()
*   eval()
*   set_task_perms()
*   caller_perms()
*   ticks_left()
*   seconds_left()
*   task_id()
*   suspend()
*   resume()
*   queue_info()
*   queued_tasks()
*   kill_task()
*   callers()
*   task_stack()



## MOOCode In The Large

### Threading, Ticks and Tasks

Like just about all computers, MOO doesn't really do many things at
once, it just fakes it. Unlike other systems, MOO uses a rather
distinctive approach to faking it. It's not _really_ multitasking,
but it fakes it rather well, especially for a multi-user, multi-coder
system.

At any given point in time, the MOO server is running only one verb
invocation. This is called a task. What happens if that task gets
out of hand? What if some idiot codes a verb to calculate pi to the
final decimal place?

To keep this from causing problems, MOOcode has some internal
cost-accounting. Each task is given a certain number of "clock ticks"
at the start; last I looked I think it was 40,000 ticks. Each
operation inside the task costs some number of ticks, arbitrarily
decided by the original developers of MOO, based on how expensive they
thought that operation would be. If the task uses up all of the
ticks, the verb invocation dies with an error about being out of
ticks.

Ticks get renewed over time, so verbs that have to do a whole lot of
stuff will use suspend() to pause for some period of time and let
their ticks recharge. $command\_utils:suspend\_if\_needed(10) 
is often used for this, to only suspend() if the verb is in danger of
running out of ticks. Of course, suspend\_if\_needed() burns up ticks
in itself, so it's wise to always give it an argument of at least 5,
more like 10 seconds for the pause.

There's also a player-wide tick allocation, so if a particular player
has lots of verbs that have heavy tick usage, the performance impact
will fall more on that player, and less on the rest of the MOO.

If you want to learn more about just what costs how many ticks, do:

    ;me.eval_env=1

And now the examples I gave you in the Flow Control section will
produce output like:

    
    >;if(1) player:tell("foo") ; else player:tell("bar") ; endif
    foo
    => 0
    [used 377 ticks, 0 seconds.]
    >;if(0) player:tell("foo") ; else player:tell("bar") ; endif
    bar
    => 0
    [used 382 ticks, 0 seconds.]
    >;if(-1) player:tell("foo") ; else player:tell("bar") ; endif
    foo
    => 0
    [used 377 ticks, 0 seconds.]
    

Note that there are a lot of odd variables that go into actual tick
costs, so they will tend to vary a little from invocation to
invocation. Don't get hung up on that facet.

## Core Classes

The real trick to moocoding is, just as with Java, learning the APIs. With MOOCode, learning how the core infrastructure objects hook into each other (and hence how you can code an object and drop it into this system of interacting objects) is critical.

The @classes command displays a list of the "important" classes for one of about five or six subsets that the wizards have decided are worth keeping track of. One of the most important is the Generics, and the key generics are:

*   $player
*   $room
*   $exit
*   $thing

You should probably also study up on object #1, The Root Class, to see what verbs you can expect to see on all objects, since The Root Class is the grandaddy object from whom all objects are descended.

While these classes aren't the only important classes, they account for the fundamental MOO experience. The vast majority of player-created objects in the MOO database are descendants of $player, $room, $exit or $thing.



### Overview

There are help entries for $room ("help $room") and $exit ("help $exit") but not for $player and $thing. Here's a quick overview of all four.

Okay, so players are roughly self-explanatory, but the real essence of a player is that player objects have incoming telnet connections attached to them, and will feed incoming text to the MOO parser, and feed output text back into the outgoing telnet.

Outgoing text usually reaches the player object via player:tell() and usually is passed to the outgoing telnet connection when player:tell() calls player:notify(). Generally speaking most code isn't supposed to call player:notify() directly.

In addition, players are generally descendants of various classes that provide useful basic commands like say, emote, etc, and for other MOO features that players care about, like the in-MOO mail system, @who, some old features like setting line length and linewrapping (mostly irrelevant now due to decent MUD client programs) and so forth.

Rooms are locations; like any MOO object they have a contents property containing a list of objects they contain, but they also have lots of verbs oriented towards players who are contained in them. Players and rooms both work closely with the MOO parser to create the whole MOO experience, both in terms of making the verbs work and in terms of creating the sense of space and place.

$room:look() is the verb that the parser invokes if you simply type "look" with no arguments.

$room:look() calls room:look_self() to return text describing the room.

$room:look_self() calls room:description() and $room:tell_contents() and sends the results to player:tell().

$room:description() defaults to just returning the text in the property room.description

$room:tell_contents() defaults to just listing the objects in the property room.contents, but they can and do often work differently.

There are some room generics, for example, that dynamically compose the description from objects that are in the room that are designed to work with such rooms, and then the room leaves any such objects out of the room:tell_contents() output.

Rooms also have two properties, $room.entrances and $room.exits. Both properties contain a list of $exit objects. An entrance is just an exit from some other room that has this room as a destination. When you enter a command, if the MOO parser can't find a verb whose name and arguments match the command, it then tries to match an exit. If it does, it calls the exit's :invoke() verb, which then moves you from that room to the destination room.

Finally we have $thing, which provides the basic features you expect to find in well, a thing; something that is neither intrinsically a part of the room, nor an autonomous entity like a player, nor an abstraction for movement like an exit. You can pick up a $thing using its get verb. You can drop a thing using its drop verb, and so forth. Things are kind of obvious and kind of minimal, but you have to start somewhere. A lot of stuff in LambdaMOO is ultimately a descendant of $thing.

## Detailed Examples

The two examples that come to mind are also probably the two most common cases as well as fundamental to that sense of there-ness that makes MOO work, which are: sight/sound and location.

## Sight and Sound in MOOCode

Sight/sound is pretty straight-forward. I lump them together because really, they're just lines of text that are being sent to the player's network connection.

Everything has a :tell() verb, starting with #1:tell(), which just does nothing. This makes it very convenient to code verbs that emit text and just send it to all objects in the room.

The player object has a special verb, player:notify(), that sends a line of text to the player's network connection. player:notify() is so special that nobody actually invokes it directly; it's almost only ever used by player:tell() (and occasionally by player:tell_lines(), see note below). In fact, player:notify() has a special permission check in its code to prevent it from being called from anything else but verbs on the player.

player:tell(), at its simplest, relays lines to player:notify(). More complicated versions of :tell() do checking for unwanted messages or attempts to "spoof" the player.

(Note: spoofing is when you send text to a player with the intent to deceive, usually by having the text not identify its source or pretending to be output that resulted from a player command; it's generally considered rude on its own, and when done for malicious purposes extremely rude and/or socially objectionable).

**Note:** player:tell_lines() works much like player:tell() except it can take a list of strings as an argument; it, in turn, calls player:notify_lines(), which loops through the list and calls player:notify() with each individual line.

Okau, so now we know what happens to a line of text after it gets to the player. This is the mechanism that everything in the MOO uses to send text to your player object's network connection. When you look at the room, for example, the room's verbs in turn call player:tell() to feed you descriptions, a list of what's in the room, etc. This is a fairly common practice and is considered normal.

## Location in MOOCode

Location in MOO is based on two special object properties: "object.location" and "object.contents". All objects in MOO have these two properties built into them. Every object always has a location value (always a single object number), and every object has a contents value (always a list, possibly empty). The server itself inherently keeps track of these, so every object can only have one location at a time, and will only (and can only) be in a single other object.contents list.

The built-in function, "move()" updates three different object properties in one atomic step, so they can never get out of sync:

*   object.location
*   source.contents
*   destination.contents

However, you shouldn't be messing with move(), instead you'll use object:moveto(#destination).

When you move an object, the object's :moveto() verb may have some special checks coded in it to prevent you moving it. Most players don't like being moved around by random people. See "help locking"; also, a lot of older players use a player class that has a "sessile" property that, when set to 1, keeps anything from moving them. These days players mostly just use the (relatively) more recently @refuse command ("@refuse moves") to protect themselves from idiots.

For the most part, objects are moved into/out of rooms, and into/out of players (the stuff you're "carrying"). There are also generic container objects - things that you can put stuff into and get stuff out of. And, when all else fails, you can probably expect to find your missing object off in #-1, which appears to be where the server sticks anything when it can't figure out where to put it.

For the most part what happens when you move something is that the following verbs are called in something vaguely like this order:

*   call objecttobemoved:moveto(destinationobject)
*   objecttobemoved:moveto() calls destination:accept(objecttobemoved)
*   destinationobject:accept(objectobemoved) calls through to destination:acceptable(objecttobemoved) to figure out if an object actually is acceptable, then returns the value
*   destinationobject:enterfunc(objecttobemoved) gives the destination an opportunity to react to the arrival of the object after it has entered the destination (though normally special effects are taken care of by the exits or by the verb that's calling object:moveto()).
*   sourceobject:exitfunc(objecttobemoved) gives the source an opportunity to do/say something after the object has left the source.

When you are in a room and you issue the "look" command, the parser finds $room:look(), which in turn calls $room:look_self(). look_self() is the generic verb for an object to assemble what a player sees when the object is looked at. As is often the case in moocode, the look_self verb doesn't just return a description, it in turn calls player:tell().

What follows is pretty much the same process for most objects:

*   player types "look<enter>"</enter>
*   parser matches player.location:look_self()
*   room:look_self() is called.
*   room:look_self() calls room:title()
*   room:look_self() calls room:description()
*   room:look_self() calls room:tell_contents()
*   the above verbs call player:tell()

As it turns out, room:description() is not part of $room:look_self(). I'm not sure where that get added to look_self() (though the :description() verb is defined on the Root Class, #1).

## The Moo Help System

Moo has a help system.  The first two things to know about are:

```
help help
help index
```

The ```help index``` command will list the available help indices.

Then you can ```help foo-index``` to see the available entries on ```foo-index```.

But mostly I'm bringing this up in case you want to get into adding your own help databases,
in which case, start here:

```
help $generic_help
```

A quick overview of moo help from a programmer's point of view:

```$help``` is the main help object, which is a child of ```$generic_help```.

Look at ```help $generic_help``` for information on how to add your own help information to the help system.

There's a convention that if you put string literal comments as the first lines in the verb source, the moo  treats that as help info.  As ```help help``` says:

```
help object:verbname   -- prints any documentation strings that are present
                          at the beginning of the program for that verb.
```

You can also define a property, ```object.help_msg``` that contains a string or list of strings.

You can also define a verb, ```object:help_msg()```, that returns a string or list of strings.

In addition, you can create your own help index and, if you have sufficient perms, add it to the main help system.  For more information on this, read at ```help $generic_help```.

I just did some poking about in the code, so I'll summarize what I just learned about how it works, which is:

1. You enter something like "help foo".
2. Your ```player:help()``` verb invokes ```$code_utils:help_db_list()``` to assemble a list of help objects by looking for a ```.help``` property on:
    1. Your player object, i.e. ```player.help```.
    2. Every ancestor of your player object.
    3. If your location is a room, ```location.help```.
    4. Every ancestor of your location.
3. Each help property can contain either a single object or a list of objects.
4. The objects can either:
    1. Be a child of ```$generic_help```, or
    2. Implement verbs for ```find_topics()```, ```get_topic()```, and ```dump_topic()``` that work they way they do on ```$generic_help```.
5. Your ```player:help()``` verb passes the list of help objects and your arg string into ```$code_utils:help_db_search()```
6. ```$code_utils:help_db_search()``` iterates through the list of help objects calling ```helpobject;find_topics(argstr)```.
7. ```$code_utils:help_db_search()``` returns the list of hits.
8. ```player:help() displays``` the results to you.

## The End of the MooCode Stuff

That's all for now. The sections below are two things.

At the very end is an outline for my next draft of this tutorial. Really, this draft was just a brain dump of a lot of different topics that I thought were underexplored by the current docs, back when I wrote this. The brain dump is a bit jumbled and some things get talked about multiple times, etc. The outline is for a more organized attack on the whole topic.

In between the outline and here is a section adapted from an article I originally wrote about Java, that tries to help an absolute beginner and gets into things like "what's a statement" and "what is syntax", etc.

# Real Basics of Programming in Moocode 

These details get skipped a lot. If you've played a little bit with some programming language, like BASIC, you may want to skip this, but unless you're confident, I suggest you at least skim it. Even if you are confident, I get into some programming in-jokes and stuff, further on, that might help ease the shock of getting into the programming world.

If you're confident, go back to the top of the article.

**Note:** _The following is focused on really basic stuff, but it still assumes the context is the moo programming language. For example, when I give a bit of code as example I'm not going to stop and point out the details that are particular to moocode and maybe don't work the same way in some other programming language. Sorry, but there's too much to explain here already :-)._

## A Program

A program is a huge, complex list of step-by-step instructions that the computer carries out. These instructions are written in a particular combination of words and punctuation.

## A Short Example

Okay, so let's give you a short example of what a bit of program might look like:

```
player:tell("Hello.") ;
somenumber = 2 ;
anothernumber = 3 ; 
theothernumber ;
theothernumber = somenumber + anothernumber;
message = "the other number is " + tostr(theothernumber) ;
player:tell(message) ;
theothernumber = theothernumber + 1 ;
message2  = "the other number is now " + tostr(theothernumber) ;
player:tell(message2) ;
```

This sort of human-readable stuff that makes up the program is usually called the _source code_, not an important detail but if you're curious as to why [read this](#CompiledInterpreted).

If you open up the source code for a program in an editor, you see a bunch of words, punctuation and _whitespace_ (whitespace is spaces, tabs, or carriage returns/newlines, basically any character in the source that doesn't produce a visible letter, number or punctuation, but instead just changes the location of other characters).

_By the way "editor" is the programmer term for a word processor. Generally editors (classics like emacs, or vi, or programs like (shudder) Notepad on windows) are much more focused on function and less on form. Editors are about moving words around, word processors are about making them print out pretty. And if you're wondering why programmers have a special term for word processor programs, it's the other way around - what do you think was used to write the source code for the first word processor programs?_

_While we're at it, in the modern era a lot of programmers don't use just plain old editors anymore, they use IDEs. IDE stands for Integrated Development Environment and is a GUI programming editor with a bunch of other extra tools and features. Some programmers swear by them, some swear at them. MOO doesn't have any IDEs._

The first step the computer takes in converting the human-readable words to computer instructions is breaking them down into _tokens_, chunking them up according to certain rules. For starts, whitespace separates the words, and a change from letters to punctuation usually separates the words.

Almost nobody in the programming world talks about tokens, unless they're messing around with learning how programming langauges are built. It's really just a fancier word for "chunks", and the main reason I'm bringing it up is because it lets me say that when you get right down to it, the "instructions" are a series of tokens; and now I get to explain the different kinds of tokens without having to waffle about keywords versus punctuation and various silly crap like that. They're all just tokens.

## Identifiers: Keywords, Variables, Literals

Strings of letters like "somenumber", are _identifiers_.

An identifier is either:

*   a _keyword_ (a predefined word that is an instruction to the computer)
*   a name for a _variable_ (a holder for some data)
*   a _literal_ value

In the example, `player` is a keyword, while `somenumber`, `anothernumber`, and `theothernumber` are variable names.

**Variable** names account for most of the identifiers in any large given program, since there're a limited number of keywords. On the other hand, those keywords get repeated a lot in any program.

**Keywords** in moocode are often called _built-ins_, as in, "this feature is built into the moo server", as opposed to the various _$util libraries_ that are written in moocode in the db but available to all users.

In other languages keywords are sometimes called "reserved words" (meaning that you can't use them for a variable name, because they're reserved to mean something special). Every now and then you even run into a reserved word that doesn't actually do anything, the people who designed that programming language just thought it would be a good idea to keep that keyword reserved for some reason.

There are also rules for what you can put in a variable name, but for now just use normal words (nouns mostly), avoid using a keyword, and avoid doing anything fancy with punctuation or numbers, and you'll be fine.

A **literal** means that instead of having a variable that contains a value, you just literally have the value typed right there in the code, like the number 343243, for example.

Programmer-speak for a bit of text is a _String_, as in "a string of letters", or really "a string of characters", since text can include number characters and punctuation characters, as well as letter characters. To put a literal String in a program, put quotes around it, "like this".

## Punctuation

Most punctuation in the code is one of two things, either 1) operators or 2) start/end indicators for an expression, statement or block. See [Source Code and Fun With Punctuation](#source-code-and-fun-with-punctuation) for a little more detail.

### Operators

An _operator_ is basically a bit of puncuation that **does something**, like the plus character + adds to values together, or the minus character - subtracts them.

Most of the usual math symbols do what you'd expect them to, except for the equals sign =, which is an important exception I'll get to in just a second.

Another exception is that the plus sign "+" can be used to add two strings of characters together, so `"foo" + "bar"` works out to have the same value as `"foobar"`. There's not much use for `"foo" + "bar"` in programming, but there's a lot of use for gluing together strings.

Note that in moocode you can only add strings to strings and numbers to numbers. If you want to concatenate a string to a number, you have to use the built-in tostr() function to convert somenumber into a string, like this:

```message = "The number is:  " + tostr(somenumber) ;```

See the section "Type Conversion" for more about tostr() and similar built-in functions.

Many programming languages have increment "++" and decrement "--", but moocode does not. 

In other programming languages, foo++ adds 1 to foo, foo-- subtracts one from foo. A lot of languages use this to make it more succinct to loop through a list, adding one at a time to the index (the number for which item of the list or array we want to use), but moocode doesn't need it as much because it has "for x in y".

### The Equals Sign `=`, Assignment and Comparison

The equals sign is used for _assignment_, meaning, storing a value into a variable. If you see:

`a = 5`

That means "store the value 5 in variable a." When programmers write this out, they usually say something like "let a equal five" or "a is assigned the value five".

If you try to put a literal on the left side, like this:

`5 = b`

...the compiler yells at you. This turns out to be a very good thing, we'll talk about it in a moment.

You can also assign one variable to another, which means that the value in the second variable gets copied to the first variable as well:

`a = b`

**Note**: A lot of programming languages require you to be very formal about defining variables ahead of time, "initializing" them (assigning the initial value), etc. Moocode is all hippy and free love, so you can pretty much create a new variable whenever you want, simply by assigning a value to a variable name. There's a downside to this; if you try to assign a different value to an existing variable but mistype the variable name, moo will happily create the new variable and let things keep rolling on... and you'll have no idea WTF happened when the wrong value causes problems somewhere entirely else in your code.

## Equals Signs, Assignment and Comparison, or Stupid Programmer Mistakes

Using a single equals sign for assignment is a very firmly established tradition in programming. This is sort of unfortunate, because it leads to one of the most common, and most frustrating typos in the programming world, which is "confusing assignment (=) with comparison (==)".

Most people first learn about the equals sign for comparison, like "is a equal to b?", and for finding out the value, like "two plus two equals 4." The motion sort of goes left-to-right, the same way you read english. In programming, when you see the equals sign, the motion goes the other way, from right to left - the value on the right gets stored in the variable on the left.

You use a double-equals sign `==` to do comparison. You write "is a equal to b?" like this:

`a == b`

It is very, very easy, even for an experienced programmer, to slip and write this instead:

`a = b`

When this does happen, it can lead to very weird behavior, which is frustratingly hard to track down, and when you finally find it, you feel really stupid. Bear in mind, it's usually buried among pages and pages of other code, so it's really easy to look at it and see what you're expecting to see, not the error.

One really good rule of thumb that I picked up somewhere is "literal on the left". If you're doing a comparison with a literal value, put the literal value on the left side. For example:

`5 == a`

This is good because it forces you to think in a slightly different way about it, and because the compiler will yell if you slip and write `5 = a`.

## Syntax

There are rules about how tokens can be used and how they can go together. These rules are called _syntax_. As you start to program, you'll be hearing about syntax a lot, mainly because a lot of the more common mistakes beginners make are syntax errors, usually finicky typos that are just damned hard to remember, until they become ingrained by habit. Don't get frustrated, it's not you; even experienced programmers often (usually) make stupid typos in their first draft of a piece of code.

### Expressions

An _expression_ is the smallest piece of code that can be treated like a value. So a variable is an expression. So is a literal. An operation, like `1 + 1` is an expression. So is `somenumber + anothernumber`. I guess you could say, as a rule of thumb, that anything that you could take out and replace with a literal value, is an expression.

Another way to say that is, "anything that returns a value". "Returns a value" is programmer-speak for "results in a value being sent back". There's a `return` keyword that does this from a method (which is basically a grouping of code, also sometimes called procedures, subroutines, or functions). So a call to a method that returns a value is also an expression.

### Statements

If an expression is a term, a _statement_ is a phrase. Since it's a phrase, not a sentence, you don't put a period at the end of it, you put a semi-colon `;` at the end of it.

For example: `somenumber = 2 + 2 ;`

## Source Code and Fun With Punctuation

Most punctuation is a single character, so there's nothing complicated as to what separates different punctuation tokens. There are some two-character combinations and there are some matched sets. The matched sets are usually used for organizing things, to start and end sections:

Usually a left parenthesis "(" has to have a right parenthesis ")" somewhere. Mostly parentheses are used for verb calls and any verb call parameters are put between the parentheses, e.g. player:tell("hello").

The characters "{" and "}", most programmers call curly brackets but appear to have a dozen or more possibly-correct names, depending on who you ask. Some programming languages use curly brackets to define the start and stop of a program block, for example the contents of a while loop. Moocode uses "end-whatever" to indicate the end of a block, for example if/endif, while/endwhile, for/endfor, and so forth.

What MOO actually uses curly brackets for is defining lists of things. A list is pretty much just what it says, a sequential collection of elements. If you're familiar with arrays, think of a list as a smart array, that grows and shrinks as necessary. Here's an example:

`stringslist = {"one", "two", "three"};`

Here's another example:

`numberslist = {1, 2, 3};`

A list can contain mixed types of elements, for example:

`mixedlist = {1, "two", 3};`

Most languages use square brackets "[" and "]" to _index_ into an array or list, for example if you one the "two" from the mixedlist example, you'd use:

`twostring = mixedlist[2];`

One exception to all this left-right nonsense is the angle brackets, "<" and ">". Moocode, like most languages, only uses angle brackets for mathematical stuff, like greater-than or less-than, etc.

Qouble-quotes like " usually have to be in a matched set. Pretty much no programming language uses "smart quotes", where the quotes are angled opposite each other. When programmers specifically want to say not-smart-quotes they usually say "straight quotes".

Some programming languages have special meanings for the single-quote \' and back-tick \` characters. Moocode did not at first, but have since used them for catching errors, see the section "Catching Errors in Expressions" in the LambdaMOO Programmer's Tutorial.

## Multitasking

When you get right down to it, computers can only do one thing at a time. They can just do them so fast it looks like they're doing several things at once. This is called "multitasking." Juggling is a popular metaphor for this, but I prefer the chess master metaphor.

The computer acts sort of like a brilliant chess player who can play against twenty ordinary people at once - make a move on this chess board, move to the next chess board and make a move, move to the next chess board, etc. It may look like the chess player is doing twenty things at once, but really he's just walking up to a chess board, studying the context - the layout of the chess pieces - in a glance and deciding on the right move.

Moo uses a system called tasks and ticks to manage this. This is discussed in more detail up above.

## Source Code: Compiled and Interpreted Programs

The human-readable version of the program is called the _source code_. Why this is, you don't really need to know, nor do you really need to know what compiled or interpreted mean. But if you're interested, read on.

The computer itself only understands numbers. Everything that you see a computer do that looks like it deals with words, is in the end boiled down to numbers. The program is too - the human-readable version is **purely** for your convenience. And believe me, it is one heck of a convenience.

Something has to convert that human-readable stuff to machine code (pure 1s and 0s). This is called a compiler, or sometimes an interpreter. A compiler converts just once. The result is a set of 1s and 0s that the machine runs. An interpreter converts on the fly; it's a program that runs your program, interpreting your words and issuing the right 1s-and-0s version of the commands as necessary.

But in real life, nothing is that simple.

First of all, there's a lot of give and take in terms of whether a particular program is a compiler, per se, or an interpreter. Many "interpreters" actually compile the program right when you run the program. Some compilers (like the moo compiler, and also the java compiler) compile the program halfway, to a generic format called bytecode, and then it's run on a _virtual machine_ (like the moo server, or the Java Virtual Machine, JVM for short), which is basically another take on the interpreter, a program that runs a program.

Second of all, there is seldom a one-for-one word-to-code translation. Most often, a short set of words converts into dozens or more machine codes. On top of that, many compilers and interpreters then rearrange commands to make it all run faster, while still doing exactly what you said to do.

## RTFM, "Use The Source, Luke", and "ask smart questions"

As you learn and ask people for help, you'll hear two phrases very often, RTFM (Read The Fucking Manual) and "Use the Source, Luke" (sometimes abbreviated UTSL). A growing trend is the third phrase, ["learn how to ask smart questions"](http://www.catb.org/~esr/faqs/smart-questions.html), after an essay written by Eric S. Raymond about how to do just that.

All of these boil down to "do your own homework before you ask me for help." Like reading this page. All of us have been there before, and it sucks, we'll spend tons of energy to **help** you figure out how to do it yourself, but we're not going to do it **for** you.

## Foo, Bar, Baz: Metasyntactic Variables and Stupid Programmer Jokes

A lot of programmers use the words _foo_, _bar_, and _baz_ a lot in examples. This is so common that there's actually now a jargon phrase for it, the metasyntactic variables.

The reason these show up so often is that often you need to explain something, and you need to explain it with an example, and the example has to refer to some things, purely for the purpose of the example. So programmers use "foo" and "bar" (from the military slang FUBAR, for "Fucked Up Beyond All Recognition). The third variable "baz", is just a corruption of "bar", because you often need a third example variable.

Why not use real words? Three reasons:

*   Because you may not want to distract yourself from the main problem long enough to think of a good, plausible example.
*   Because you want your reader/listener to be concentrating on HOW you're doing whatever, not on WHAT you're doing it to.
*   Because "foobar" is a stupid programmer joke of long-standing tradition.

## The End

That's the end of our little "the really real realest basics of programming" section, so you can go back to the top and read the regular tutorial.

# Outline of Planned Revision

-   Basic MOOCode Gotchas
    -   Comments  
    -   Variable Declaration and Scoping
    -   Lists
    -   Flow Control: for, if, while, suspend, fork
    -   Types, typeof(), toobj(), toint() or tonum(), tostr(), toliteral(), tofloat()
-   MOOCode In The Large
    -   Object References (Object Numbers)
    -   Implicit Environment References: this, player, caller, callers()
    -   Verb Invocation, Property Access
    -   Perms and Args
    -   Help
    -   Parameter Passing
    -   Threading, Ticks and Tasks
-   The MOOCoder's Toolbox
    -   @display, @args, @chmod
    -   Eval ;
    -   Eval Environment: ;player.eval_env="here=player.location;me=player;"
    -   Multi-Line Evals ;;
-   The Framework
    -   Parsing
    -   Matching
    -   Feature Objects
    -   Core Classes
    -   $help
    -   $room
    -   $player
    -   $exit
    -   $thing
    -   $features
    -   $utils
    -   #0
    -   #-1
-   Some "interesting" functions and properties
    -   max_object()
    -   $playerdb
    -   player.owned_objects

# Original Source

The source code for this file was taken from [Steve's website, darksleep.com/notablog/articles](http://www.darksleep.com/notablog/articles/LambdaMOO_Programming_Tutorial) and converted to markdown and is stored in this repository for posterity. It is not HTML5.
