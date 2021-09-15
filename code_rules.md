# these are rules I use to guide pr reviews

## Function Creation
### functions must only do 1 thing
mutating an object/hash/array OR return a value \
mutating and then returning the **same** object is acceptable but not preferred

### name must either start with a verb or a question word (is)
eg: isBlank, buildContent, calcValue etc\
language specific exception
ruby: don't start boolean checks with is, end them with ?
### verb at start must make sense
is_ and _? return a boolean
build returns strings, html or objects \
gen generates _something_
init initializes something \
find finds something and returns it \
set sets a value internal to an object \
get returns a value from an object OR requests data from a server \
calc returns a number that

### extract guard clauses whenever reasonable
- if statement has more than three lines and there is no else/elsif/else
    - reverse the if statement and return early, returning nothing (in ruby unless may make more sense)
- if statement has more than three internal lines and the else is 1 short line or a single value
    - reverse the if statement and return the else value early 

### unextract guard clauses whenever reasonable
when you see a large pack of guard clauses at the top of the function \ 
it may just mean you need a more standard if/elsif/else if/else

### look for comments the could easily describe a method
long functions may have grouped code under a comment
consider extracting the code and using the comment to create the method/function name

## built in Function usage
### prefer functional methods, filter, map and reduce over each
#### do not use for, loop, while, until, forEach or each to generate arrays from arrays
- use map instead

#### do not use map to iterate over values when not building an array
- use each or forEach instead

#### do not use a map followed by a join to create a single string
- use reduce instead

### do not use for, loop, while, until, forEach or each to generate objects from arrays
- use reduce instead (in ruby use each_with_object)

### Javascript specific
### avoid for loops in all their incarnations
use forEach \
reason: we are a ruby shop, for loops "don't exist" (they do just no one uses them) in Ruby \
and it creates mental burden switching coding styles

### do not use `for x in obj` or `for x of obj` to iterate objects
use `Object.entries(obj).forEach(([key, value]) =>{ do something }) Object.values(obj) or Object.keys(obj)` \
reason: it's very unclear what the in and of return and you just have to remember \
one of them is not safe to use and I can't ever remember which one and have to look it up everytime

### do not use old(er) hacky methods from old javascript
use includes, find, every, some, filter, map etc
### do not use var, use let and const
### do not use Object.assign use spread syntax
### avoid defining functions inside of functions, but when you do...
use => syntax to keep this from being something unexpected
### do not use => functions at the "base" level (this rarely comes up)
it offers no benefits and is harder to read.

### Ruby specific
### do not use reduce to return an object/hash/array, use each_with_object
it's more legible and less error prone \
reason: you don't have to return the object for the next loop it 'remembers' it

### use the most specific Enumerable method (or just method) you can for the need
each_slice, partition, tally etc

### avoid metaprogramming
nobody can read that shit and it just creates mental burden and complexity for other developers \
it's tech debt waiting to happen

## Rails Specific
### never create custom endpoints that just return data
use a new controller and make the custom endpoint the show or index
### never ever ever use serialized yaml, serialized CSV or serialized json fields
use jsonb
reason: this idea is left over from before jsonb existed and can't be searched easily
### never use json fields
use jsonb
reason: json is not searchable in the same way as jsonb
### almost never store anything other than a 1 dimensional Array in a jsonb field
only store arrays of primitives or arrays of objects in jsonb (greatly prefer arrays of primitives)

if you need to store an array of database ids in a jsonb field \
you've likely made a design mistake and reversed the id storage location for a has_many relationship

if you need to store a keyed object with primitive values (strings, ints and floats) \
add the keys as fields to the ActiveRecord object \
reason: it's dumb and lazy to store a json object in a database designed to store primitive values by key

if you need to store multiple key that hold arrays
make a field for each key that stores an array as jsonb
reason: it's easier to update them individually and it's easier to validate the arrays before storage

if you need to store a very complex 3+ depth object \
something really weird is going on and you may have either A - made a bad design decision, B - are cutting a big corner \
note: check yourself before you wreck the database

### validate every single field that is stored to the database in as many ways as you can think of
presence, length, regex matches, enums refer to the rails guides for all options \
reason: many bugs come from nil class errors because a field is missing or corrupted or contains an irrelevant value
### validate this holy shit out of jsonb fields that are stored in the database
**huge** numbers of errors and complex code come from unknown/variable shape json, jsonb and serialized fields

### avoid jsonb and use only in emergencies or for very unimportant data
non-flat data storage increases errors and potentially denormalizes data

### do not use HABTM
don't be a weirdo or cut corners, take the extra 3 minutes to create a many to many relationship
reason: it very well may come up that you need to store some sort of data in the join table other than just foreign keys

### Pointers/ pass by reference errors/bad practices
## do not mutate shared objects without meaning to
both JS and ruby pass all non-primitive values by **reference** \
look for code that doesn't understand this and accidentally mutates upstream data \
this is very common and creates lots of bugs specifically in JS due to it being generally longer running \
common gotcha in Angular
accidentally mutating a parent's object passed into a component via an input
it should copy the object before working with it if it's being used for data storage for the child component





