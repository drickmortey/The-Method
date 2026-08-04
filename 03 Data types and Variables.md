**Authored by drickmortey**</br>
**Peer reviewed by Downrest**

### Resources needed:
- https://play.luau.org/
---

Take a look here:
```lua
50
"The Text (trademarked)"
```
What's the difference between these two literals? We can clearly tell they're not the same kind. What if we added them together? What's stopping us from doing that?

Luau needs to keep track of what your literals *are*, it needs to know the **type**. You can't do arithmetic with number and text, this is common sense (I mean what would you even do?). Luau will first need to check if the types line up, it should be `number + number`. Every single value in Luau carries a data type, we can view this using `type()`.

```lua
print(
    type("Sinister words here") --What does this send to the output?
)
```
> Sometimes we know what we want out of illegal operations like `"text"+5`. We can define what will happen to these two (maybe combine into `"text5"`), this is called **overloading**. There's different kinds, this one's called *operator overloading*. Unfortunately we cannot add overloads to text, but we can to *tables*.. 

# What's a data type?
<p>

Sometimes plainly called datatype, it tells Luau what is and isn't possible with the literal. E.g. a number can be multiplied. Internally (at the low level), every piece of data is a sequence of bits (ones and zeroes). So technically we <b>could</b> do `"hello" + 50`, there's nothing fundamentally "wrong" about it. But this will result in undefined behaviour (UB). You will hear about it quite often when learning middle-to-low level languages. However, Luau doesn't struggle with this, it will simply reject any operation where the datatypes are incorrect.

We'll learn why literals take up so much more space than their actual value implies once we cover *tables*. 16 bytes is pretty big compared to "what could have been".

---
# All the datatypes

There are 10 total (8 in Lua).

- number
- string (text)
- boolean (true or false)
> Trust me this is not useless (true).
- nil
> Represents absence of value. You're asked for the 7th person's name in a list. There is no 7th person, what do you send back? `false`? No that's not it.. Hmm.
- function 
> Block of code. Can be reused if you have the reference to the function. All your code is under a function. (Oversimplification)
- thread
> List of instructions the CPU goes through. Contains functions (containers of code).
- userdata
> Describes a type that is created by the host language and not Luau.

### And the one type to rule them all

- tables

    Lua and Luau are known for this one datatype. Every single design pattern you'll see is possible because of it, ECS, OOP, DOD, AOS, SOA, prototypal programming (not calling it PP), to name a few.

    Has metatables to extend its behaviour (e.g. overloads, as we discussed). Uses a key-value pair system. Tables should have their own dedicated article and are out of the scope of this one, so we won't go over them.

### These types are additions not in Lua

- vector (represents a 3D value)
- buffer </br>


    kind of a "table" (opposite in some ways), lets you shuffle around and manipulate bytes to do crazy tricks. Used a lot in data compression. Instead of 16 bytes (which may have a lot of wasted unspent space), we can try maximum compression. This compression is needed to make networking systems fast, the less you send over the network the better.


> `local x = 5`. We want to send  this through the network, it's 16 bytes. But we could compress it to one.. This is why custom networking libraries are so good.
---

I think it's about time we stop calling **variables** "containers". They're called variables. They vary, meaning their value can be anything and can be changed. The `local` keyword is for creating **local variables**. There's two kinds of variables.

### Global

```lua
--!nocheck

--We need --!nocheck to disable the warn that we get in Luau Playground when using a global variable where a local variable works better

x = 5 --notice there's no local keyword, this is one full statement
```
These are generally considered "bad", but as the author of the Lua manual calls it, "global variables are a necessary evil in programming languages". They're actually not bad if they're *in* the language *in* the first place. They allow some neat tricks, but most usages can be replaced with local variables (in fact, local variables are better in those cases). **They're about 10% slower than locals** (I'll refer to them as that). The reason is a bit complicated, wait for when we uncover the truth about globals. *Incorrect usage will lead to bad code, so for now you should always use locals*. **These go in the global scope**.

### Local
```lua
local x = 5 --using the local keyword 
```
You will be using locals most of the time, if not all the time. They're faster to access than globals. **They go in the local scope**.


The main mechanical difference arises in how they interact with **scope**.
---

# Let's create a scope 

We will use the `do` and `end` keywords to create a code block. Every code block has its own scope, this is called its *local scope*. Scope exists everywhere, as your entire script is in a code block itself. I call the one at the top the "outermost scope".

```lua
--Hello from the outermost scope

do --starts a code block

--we can have multiple inside each other, this is called nesting

do 
    do end
end

end --ends a code block
```
Again, every code block has its own scope. Scope determines whether or not a variable is accessible inside your block. What should this print?
```lua
--!nocheck
do
    local var = 50
end
print(var)
```
We're trying to access a variable that was created in a local scope, in the outermost scope. We can't, the rule is that anything local can only be accessed inside its scope, or deeper in. Here's what I mean:
```lua
do
    local var = "Hello from above"
    do --this block has its own scope. every block does, but it can still access variables from its "parent" block.
        print(var) 
    end
end
```

This is a more sophisticated example
```lua
local counter = 0 --our local

while counter < 50 do --create a block
counter = counter + 1 --add one to the counter's current value, and set that as the new one
print(counter) --let's see it in the output
end
```
This is consistent with the rules. We can create a local variable and access it in blocks that are deeper in the hierarchy.

### But why?

This is due to stack memory in Luau, local variables are put on the stack. Also, locals are resolved at compile-time (when source code is turned into bytecode). Suppose:

```lua

print(x)
do
local x = "This shouldn't work"
end
```
The variable literally doesn't exist yet. OK, why don't we wait until it does?
```lua
--REQUIRES ROBLOX STUDIO TO RUN (the task library doesn't exist in standalone Luau)
task.spawn(function()
while task.wait() do
print(x)
end)
do
local x = 5
end
```
When resolving the locals, Luau will see that we're accessing `x`. There is no local that is called `x` at that point, so it assumes it's a global variable. This would technically be valid, only that `x` would be `nil` (more on that when we talk about globals in detail).

Breaking the rules of lexical scoping (how scoping works) is proving to be hard. Let's try having the local exist when we access it out-of-scope.
```lua
do
local x = 5
end
print(x)
```
..Due to how stack memory works, this isn't going to do it either. Whenever `end` is reached, it will "**unload**" all the locals from the scope of the block that it ends. So no, `x` doesn't exist by the time we want to print it, and Luau will again think it's a global when resolving it at compile-time.

There are cases where the local will exist when accessing it, it will be in stack memory. The problem is with Luau thinking it's a global because at that point, there are no locals with the name `x` (from the examples). 

To recap

### The rule where locals may only be accessed in its own scope or scopes within, exists because it's literally not possible to break it.
You can replace "scope" with "block" in that sentence if it helps you understand better. They aren't interchangeable, a scope is a part of a block, not the other way around.

Notice that I mentioned "global scope". When something is in there, it can be accessed from **any** scope. That's all to it. Test this code:

```lua
--!nocheck
local function a() --this contains some code that we can run with a()
var = "Hello from the global scope"
end --this function hasn't run yet, we have only defined it for now
local function b()
print(var) --if it's in the global scope when this runs, we'll see it
end

a()
b()
```
Lotta cool stuff you can do with that, but it's unsafe and can lead to anomalous bugs that will be hard to track down if you don't know what you're doing. Stick to locals.

---
## Why lexical scoping is actually good
Isn't lexical scoping bad if it keeps getting rid of my variables whenever the block ends? Can't even access it in the block above where I say `local var`? No. No, this is absolutely essential. Luau would be a bad language without lexical scoping.

1. Do you want to think of a new name (that makes sense and is easy to type) for every new variable? With lexical scoping, you can easily reuse names for variables that have stopped existing and thus don't use that name anymore.

2. It lets Luau be faster. Instead of checking if it's on the stack, it **knows** it's on the stack. 

3. It makes Luau more predictable. We know the local belongs to its block and is immune to outside interference. I call this "locality". When working with independent code blocks, you don't have to think about what's outside. All the local variables in the block, belong to that block and blocks within it, nobody else can mess it up.

4. `local` always forces creation of a new variable. When a global already exists, doing `global = value` will overwrite it instead. We sometimes *need* more variables, especially for a *trick* in the type system, using identical identifiers makes it work. 

5. It makes refactoring (changing code structure) easier. Look:

```lua
local gun --suppose this is a gun
local function burstFire()
    local fired = 0
    while fired >= 3 do
        shoot()
        fired  = fired + 1
    end
end

--Imagine there's many lines of code here
```
We want to refactor this by putting the `burstFire` function somewhere else. Since the `fired` local is "owned" by that function and will never be accidentally overwritten out-of-scope, it's as easy as copy pasting it below.
```lua
local gun

--Imagine there's many lines of code here
local function burstFire()
    local fired = 0
    while fired >= 3 do
        shoot()
        fired  = fired + 1
    end
end
```
Our function was "portable", easy to move around. Lexical scoping also allows us to wield a brand new trick called **shadowing**. Shadowing is when two local variables are given the same name.
```lua
local variable = 9090
local variable = "this is distinct"
```
What happens?

Two unique variables are created under the same identifier. They're both different. This is useful for reusing variable names in a deeper scope when you don't need the one from outside.

```lua
local amount = 50
AddCash(amount)

--this block creates a ballpit
do 
local amount = 10000
GenerateBalls(amount)
end --here, this variable that shadows the previous one is unloaded

--in this scope, amount is still 50. that original one still exists
```
Shadowing has another useful trick in the type system, but we haven't talked about that yet.

# Task 1: Shadow a number variable 4 times, each time in a deeper scope. Put each variable in the function below after creating it.

```lua
local function increment()
    local counter = 0

    return function(n)
    counter += n
    print(counter)
    end
end
increment = increment()
```

**Hint:** Shadowing is when the exact same identifier is used for multiple variables that are accessible in the same block. `local x; local x;`, the second one shadows the previous one.
> Not assigning a value to a variable during declaration means you haven't initialized it. These variables are given the value `nil` by default. These are identical in every way: `local var; local var = nil`.

<details>
<summary> Answer </summary></br>

```lua
local function increment()
    local counter = 0

    return function(n)
    counter += n
    print(counter)
    end
end
increment = increment()

local number = 1
increment(number)
do 
local number = 10
increment(number)
    do 
    local number = 20
    increment(number)
        do
        local number = 100
        increment(number)
        end
        --number = 20 in this scope
    end
    -- number = 10 in this scope
end
```
Shadowing isn't necessary here, you could just do `number = 100` or whatever. But to get the point across..
</br>
</details>

P.S, if a local doesn't have lexical scoping, you have reinvented global variables.

# Extra: The `const` keyword
This is a fairly new addition to the language and I believe it has developed enough to be considered. You can think of it as an alternate version of `local`. These variables must always be initialized (given a non-nil value at declaration), because you can't overwrite them later and having a forever nil variable is pointless.

In algebra, a constant is the opposite of a variable. Its value does not change and will not change. `1` is always `1`.

Taking this philosophy, a "const" variable is a local variable whose value you cannot change. If Luau detects this, it will throw an error during compilation. This is for values whose value should not change, suppose:

```lua
--[[const will not light up because it's a Luau feature,
these code examples are highlighted in Lua]]
const TESTING = true 

if TESTING then
    ActivateDebuggingTools()
end
```
Our example here is kind of useless. Nothing was trying to change `TESTING` in the first place. Also, it's a convention (common thing to do) to have a constant variable's name be in all capital, this conveyed to the programmer that it's probably a bad idea to change its value. Though all that was before `const` came around to enforce this idea. 

Const variables aren't any faster than locals too. They're "enforced" during compilation (not while code is being run), if Luau notices we're trying to overwrite the variable, it throws an error hoping we'll rewrite the program into a version that doesn't. At the end, it really is just a local variable.

## MY OPINION
I personally never use `const`. Yes, when reading the variable declaration you can definitely see that it was intended to never be changed. That's if, you're reading the declaration. On the autocomplete, you can't see that. The best way to tell a programmer to NOT change the variable is to have its identifier in all caps. I believe this is enough. Take that how you will.

</p>