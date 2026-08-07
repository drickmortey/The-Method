**Authored by drickmortey**</br>
**Not peer reviewed yet**

### Resources required:
- http://play.luau.org
</br></br>


```lua

print()
math.random()
```
What do these two lines have in common?

Notice that their syntax is very straight-forward. It's just an identifier followed by parentheses `()`. There's no keywords in sight. They're fine as-is, this means they are considered statements. Try putting a `;` in front of them and see if it errors.

If we remove the parentheses, there's an error. It reads,</br> "`Incomplete statement: expected assignment or a function call`".</br>
</br>
Thus, without the `()`, it isn't considered a statement.

These are functions. If you remember article 03, I briefly spoke of them as containers of code that can be reused if you have the reference to the function. Here's what I mean:

```lua
--"print" contains a reference to the function that has code in it
print(1,2,3,4,5)
```
> Multiple things can go inside the brackets. They enclose whatever's inside, the stuff has to be separated with commas (except the last one).

It's common sense that a reference alone isn't a complete statement, what do you want Luau to do with it? Same problem with identifiers being alone. We can conclude that `()` is telling Luau how to use the reference. In article 03, I defined `()` as a punctuator. I also mentioned that punctuators (other than the usual, like enclosing) also do *something that is special to them*.

These two as punctuators are called "calling brackets". Though these are actually parentheses. Back to the error where it `expected assignment or function call`, it's apparent that the calling brackets are what do the "function call" that it was expecting.

When a function is "called", the CPU essentially places a bookmark at the **call site** (where the calling happened) to remember where it left off and jumps execution to the start of the code inside the function in order to run it. Every function call has Luau make an entire object that will handle everything (e.g. local variables) inside that function respectively . This object is called a **stack frame**, and since a fresh one is made every function call, the two stack frames made in `print(); print()` have different identities. This means the same function can be called multiple times, even while one is "being called", because the stack frames are unique. They don't share any memory, both have different variables that are **local to them**.
> There's an optimization Luau puts into place that reuses a stack frame if your function meets the criteria. This is called a tail-call, one of the notable requirements is that *returning must be the last instruction*.

It is to be noted that function references are stored inside local or global variables (e.g., `print` is a global). This is what the `expected assignment` meant, we can either run the function with the reference in the variable or we can reassign the variable to hold something else. This is completely legal
```lua
math = print
print = 5

math(print + print) --crazy
```
> When defining a function, it will usually create a local or global variable to store the reference to it. When a function's reference isn't being used anywhere and isn't stored anymore, it gets cleaned up eventually.

# Defining your functions
This is how we create a *function literal*, it creates a reference that points to the **closure** that is generated. A closure is a "defined" version of a function that contains bytecode and **upvalues** (explained later). To make every function call unique, new stack frames are made. But the closure (the "source") remains the same.
```lua
function() --start defining a function, creates a code block

-- the parentheses aren't calling the function here,
--[[it acts as a separator that let's Luau differentiate whether or not
this function is "anonymous" or "named".

named functions are those that have a variable pointing to its reference

anonymous functions are basically just function literals]]
end --exit the function's code block and stop defining the literal
```
This here creates an anonymous function. These are useful, you will see. But we know that literals alone will error, we need a complete statement. We can complete the statement by assigning this literal to a variable.

```lua
local variable = function()
--our function literal is stored in the variable now.
--because local identifier = literal is a valid statement, this won't error

variable() --this however, will error
--the error implies there is no variable called "variable"
--Was that your expected error?
end 
```
One thing of note is that the variable doesn't exist until the end of the statement. Sounds obvious right? Well, we sometimes want to *use* the variable during declaration, especially in multi-declarations (`local x,y,z = 1 2 3`) and function declarations (inside the function's code). For the latter, using the variable in the function would be like.. *using the function in the function*. This is called **recursion**, we'll talk about it later.
```lua
--Variable must exist before using it for recursion
local var --statement ends here, variable is valid
var = function()
var() --this will now work but will cause "stack overflow"
end
```
Doesn't look like a huge deal right? And, recursion (although having very genuine and real use cases), isn't very common in functions. Though that setup for recursion looks kind of ugly. To address this problem and make stuff easier, Lua and Luau both have a second way to define a function.

This will create a variable so that it's properly created for use **during** the function declaration, also it looks prettier.

```lua
local function var() --[[This is what I meant by differentiating
between named and anonymous functions]]

--The variable's name is inside the function declaration
--This will create a local variable that has the value nil
--Then afterwards, it can be reassigned
--The literal is evaluated and the reference is put inside the variable

var() --works like usual (stack overflows as well)
end
```
> The syntax for this is `function identifier() end`. Adding local before `function` makes the variable local. We can remove the `local` in `local identifier = function() end` to make the variable global instead.

You can see how this streamlines the process of recursion and makes the function more readable. So this is unequivocally the better way, right?

Not exactly. While the previous method was more verbose, its specific syntax actually allows us to perform a neat trick *regarding the type system*, but that's for when we get on that topic. For now, the second method is better for you, though I think the first is more "direct". We can say the second is *syntax sugar*.

Functions that do recursion look trippy and contradictory, how does the bytecode compiler not explode when it sees a function that's calling itself when its declaration isn't even complete yet? Well, this was in mind when creating the language. It knows what to do in these cases, that's why recursion works.

# What happens when the function ends
By a function ending, I mean when it reaches its `end`. The `function` keyword creates a code block, which as stated, must be exited with `end`. This in turn also unloads every local variable defined inside the code block (these are stored on the stack frame, unique to each call). Let's see:

```lua
local fn = function()
print("reached here")
end

fn()

--alright let's walk through this
--the function is defined (closure created, reference to it put in variable)
--it's then called (stack frame created, execution jumped to start of function)

--we then call the print function
--now we have reached "end", so we unload everything from the stack frame

--What now? do we continue down, and run the function with fn() again?
--this would continue forever!
```
This of course doesn't happen, the CPU always leaves a "bookmark" to remember where to continue from. But it doesn't do that with `do end` (for obvious reasons)? The only purpose of `end` is to exit a code block and unload the locals? How does it **return** to where it was?

```lua
local function fn()
return --This is how. This instruction makes us return to the call site
end

fn() --this is the call site, where the function was called
```
Sure, that makes sense. This feels wrong though. `end` is never reached, also in every function so far there have not been any `return` statements? It's a statement just like `continue`, except it also falls in the category of a keyword.

Let's answer the second question first. To be brief, Lua and Luau automatically insert a `return` at the end of a function. This is called an *implicit return*, we didn't write out `return`, but in the bytecode it pretends we did.
```lua
--Both functions are equivalent
local function fn() end --implicit return here

local function fn() return end --makes no difference
```
In fact, a function **must** have a return or it will fall back to that dumb problem we encountered earlier. A function cannot function if it doesn't eventually return (no pun intended).

Now for the other question, I kind of lied. `end` does not "unload" or "exit" a code block. It doesn't even have any associated bytecode. Neither does `do` (only has bytecode in a very specific case). Both are resolved at compilation time and don't exist while your code is running. They are an important part of syntax though, lexical scoping is damn near impossible without them. So,

they're more like markers.

Imagine a construction crew needs to demolish a building with fat stacks of explosives. They first need to mark out where their allowed area starts and ends to make sure nothing bad happens. `do` and `end` basically do that, it's up to the construction crew to do everything else (e.g. when to unload variables). That's what they are, just things to mark "here" and "there". The compiler resolves these and generates bytecode that strictly *adheres* the lexical scopes that they "define". 

Though it is still completely fine to think about them with the analogies I gave before this bombshell. They mechanically do exactly that even if it's technically not them doing it.

Back to the problem
```lua
local function fn()
return
end --doesn't need to reach here since it doesn't do anything
--it has no bytecode that says "do that"
--the bytecode is generated in a way that follows the principle of "end"

--after compilation, your code is ready to run
--the "end" is nowhere in sight (no pun)
--so it only really needs to hit the "return" part (required)
--every function will always return (implicitly or explicitly)
```

# Task 1: Write a local function that adds 1 to a number variable and prints it every time it is called. Call it 5 times.

**Hint:** Declare the number variable outside of the function because it'll keep getting re-declared and reset every time you run the function. In more technical terms, declare the variable outside of the function's scope.

To add one to a variable, take its current value and.. add one to it. Then set that as the variable's new value.

The `print` function can print any datatype to the output.

<details>
<summary> Answer </summary></br>

```lua
local counter = 0
local function count()
counter = counter + 1
print(counter)
end
count()count()count()count()count()
--() acts as a separator and whitespace character too if used right
```

</br>
</details>

---
# Returning values

The `return` statement also has a second ability. It can also bring back values to the call site. Look here:
```lua
--!nocheck
local function fn() end

local receive = fn() --call site
```
`fn` has the ability to return any literals, which will be fed into the `local identifier = literal` syntax. The call site is exactly where the function was called. So you can think of it as putting the values directly in place of `fn` once it finishes execution.

We did not return any values in that example. So what is the value of `receive`? What did it, receive?

When a function **actually** returns nothing (not `nil`, which is still a value) and a variable tries to hold it, it will default to `nil`. This "*true nothing*" is what I call `void`, a common concept that appears in other programming languages in many shapes and forms. The Luau type system represents `void` with empty parantheses `()`. Outside of the type system in Luau, `void` does not exist, and cannot be assigned to any variable or used in any way. Assigning to a variable instead gives it `nil` as we saw.
> The `void` type is used in C++ to declare that a function returns nothing. It's not even considered a **data**type since it's nothing.
  
Let's try returning an actual value now.

```lua
local function fn()
return "Hellooooo"
end

local greeting = fn()
print(greeting) --Nice
```

We can even do multiple values:

```lua
local function fn()
return 1,2,3,4,5,6,7,8,9,10
end

print(fn()) 
--we don't need variables to receive the returned values
--we can use them just like literals
```
Here, before running `print`, it runs `fn` in order to evaluate its return values. More on functional evaluation  -> not now.

## Why returning is useful
Imagine you want to run a calculation and use that somewhere as a literal. That's easy with functions, we can run code and return the values we want to use as literals.
```lua
local earned = 2
local rebirths = 2
local totalMultiplier = 97

local function calculatePoints()
local scaleDown = rebirths >= 3 and .9 or 1 --help balancing
local rebirthFactor = (rebirths^1.1)*scaleDown

return (earned*rebirthFactor)*totalMultiplier
end --imagine doing all this every time you want to calculate

print(calculatePoints() / 5)
earned = 200
print(calculatePoints() * 5)
```
> You can omit the 0 in decimal numbers for below-zero values like `.9`.

Functions in Luau are so much more than this, they're **first-class functions**. This means they're handled like any other value (string, number, etc.). We can pass them into other functions just like values, assign them to a variable, return a function from a function, or create one without a name just for a function literal. This opens up many, many possibilities and begins root of the **functional programming paradigm**.
> A paradigm is a distinct style of programming in approach, architecture, and goal.

# Task 3: Write a function that returns a function, and receive the returned result in a variable. All functions should print something different. Call the function <u>that is in the variable</u> once.

**Hint:** Functions are first-class, you can toss them around as you would with numbers. After all, you can only have a reference to a closure. The reference itself is pretty harmless and light.

The functions that you return inside the "main" one do not need to be named. You can create a function literal with `function() end` and pass that around.

<details>
<summary> Answer </summary></br>

```lua
local function chain()
print("Called once!")
return function ()
    print("Called two times!")
end
end

local receive = chain()

receive()

--Note we can also do chain()()
--this is because chain() returns a literal that we can run again with ()
--if that returned a function too we could run that with () as well
```
</br>
</details>

Functions like `print` can take in values inside their calling brackets, it'd be cool if we could take in values from our functions' calling brackets. I'm afraid it's not feasible to cover the topic of functions in a single article. This will have to wait.