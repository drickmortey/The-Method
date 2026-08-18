**Authored by drickmortey**</br>
**Not peer reviewed yet**

### Resources required:
- http://play.luau.org
</br></br>

```lua
print("Baby Baby Baby oh")
```
We've been using print statements quite often to get some sort of feedback or reaction from our code. Notice that the calling brackets aren't empty. Why not `print()`? We have to put in the value that we want to show on the output.

# Parameters and their impact on society
Parameters are local variables that are defined right before anything in the function actually runs. Due to this, we can reason that the variables aren't given their values by the code inside the function. The values must be received some other way.

```lua
local function fn(parameter1, parameter2, parameter3)
print(parameter1, parameter2)
end

fn("I'm the first parameter!\n",

"'Nobody cares!', said second parameter.")
--\n inserts a line break character
--it's not the same as / (front slash)
```
Here we can see it in action. Every identifier inside the function's *definition brackets* is a parameter (separated by commas). They're ordinary local variables. So the ones that aren't initialized (not given a value), will be given `nil` (`void` doesn't exist outside the type system, as I said).
> Giving any variable the value `nil` has that variable treated as if it hasn't been initialized.

We can understand the above function as this:
```lua
local function fn()

local parameter1 = "I'm the first parameter!\n"

local parameter2 = "'Nobody cares!', said second parameter."

local parameter3 --not initialized, will be given nil

--the function can access the above variables
end
```
So why do parameters exist then? In this small example, we already knew the exact values we wanted to use every time the function is called. Suppose we don't. Here's a "print" function.

```lua
local function print()
local parameter1 --what do we even put here??

--it's going to be different every time!
end
```
Another glaring issue is that `print`'s calling brackets can have any amount of values inside. So even if the issue of "what do I put here" was solved, do we pray we have enough variables to hold every value that might be *passed in*?
> We use varargs (variable arguments) when we don't know the amount of parameters that might be plugged into the function. These are denoted by triple dots `...`.

The thing to take from this is that parameters allow us to use functions as components that take *variable* inputs (in this sense meaning they don't have a specific value each time), do stuff with them, and give an output (return values). This is the fundamental principle behind **recursion**. What the function takes in every time can be *different*.
> Functions are not just reusable code.

Also, note that the things that are *passed in* are called **arguments** (which are literals). An argument is then copied into a local variable, the **parameter**. These all have to be in proper order (parameter1 will take the first argument).

Another neat thing about parameters is that due to lexical scoping, they belong to the function they were declared in.

```lua
--[[
input -> function -> output 
]]
local function percentageOf(n1,n2)
return n1*100/n2
end

print(
    "50 is", percentageOf(50,200), "% of 200"
)
```
> We could have just put the expression `50*100/200` inside the print, but using a function instead **abstracted** it and was easier to write. The name tells us that it gives a percentage, and by reading what the function contains we can confirm that. Compare to just `50*100/200`, would you have understood it without the explanation?

Easy to understand. Two inputs, `50` and `200`, function took those and gave an output. Though we can indeed use functions as something more than a way to simplify math (as I've been showing). It can be used to solve real and "abstract" problems (e.g., what should we grow this year if we cultivated soybeans last year?).
```lua
--!nocheck
--Just an example, don't sweat it
--Also this is not scientifically accurate I can barely remember geography
local cropRotation = {
    Soybeans = 50,
    Cotton = 90,
    Corn = 30
}
local Results = {
    {120, "Soybeans"},
    {80, "Cotton"},
    {50, "Corn"}
}

local grewLastYear = {
    Soybeans = .7,
    Corn = .3
}
local function getCrop()
    local rotateValue = 0
    for i, v in grewLastYear do
        rotateValue += cropRotation[i]*v
    end

    local shouldGrow
    for i, v in ipairs(Results) do
        if rotateValue <= v[1] then
        shouldGrow = v[2]
        end
    end
    return shouldGrow
end

local sendRecommendationNotification = print
--we don't have the budget to make it

sendRecommendationNotification("You should grow", getCrop(), "not that I know though")
```
> Too much abstraction can be bad. If all you see are names that describe something, it's hard to see the actual underlying code (many things aren't 100% reliable and can break, this level of abstraction can make debugging harder). Though that is not the case here, this would be so if you have functions that use other functions that use other functions and so on (where's the real code??).

> NASA actually has a code design document that strictly mentions this issue, stating that this kind of code will not be accepted into real devices or systems because somebody's life can depend on it. You don't want a shiny shuttle with sloppy code, maintaining codebases that have this issue is hard. You go "I need to know what this function does" and read it, though it uses another function so you need to read that.. and it goes on. Eventually you lose your train of thought.

We can call `getCrop` every time we want to determine the next plant we should grow. It's solving a more complex problem than what we've seen so far.

You can make a case that this is all also possible without functions. Yeah true, but not very practical. Here's where functions really got the ball though.

## Some problems are recursive
Imagine we want to find the exit of a maze. We write a function as follows:
```lua
--VECTOR2 ONLY EXISTS IN ROBLOX LUAU

local up    = Vector2.new(0, 1)
local down  = Vector2.new(0,-1)
local left  = Vector2.new(-1,0)
local right = Vector2.new(1, 0)

local at = Vector2.new(0,0) --where we're at
local function traverse()
--imagine a function "walk" exists
local foundWay = walk(up) or walk(down) or walk(left) or walk(right)
--this will find a way in all four directions
if foundWay then
at += foundWay --mark this as our position
end
--return if it's a dead end (foundWay is falsey)
end
```
This is nice and all, but the function only runs the amount of times we call it. It'd be better if it kept traversing until there was a dead end. Well..
```lua
local function traverse()
    --code blah blah
    if foundWay then
        at += foundWay
        traverse() --hey!
    end
    --stop traversing if this is ever reached
end
```
This will create a new stack frame and use the new information to have another run at the problem. It keeps going until it reaches a return (which is implicit here). We can find the exit now by calling the function once to start recursion. We have solved the problem recursively.

Though this is not a very good example of recursive problems. The same problem can be solved with a *loop*. Any recursive problem could technically be solved with a loop. So is recursion even good??

Recursion is good for self-contained problems. Suppose we don't want to "pollute" the current scope with a bunch of variables:
```lua
--WILL ONLY WORK IN STUDIO
local function getDescendants(part)
local children = part:GetChildren()
if children[1] then --if there is a child
    for i, child in children do
        table.insert(children, child)
        for i, descendant in getDescendants(child) do
            table.insert(children, descendant)
        end
    end
end
return children
end
```
This is a better example of a recursive problem. We have used no locals from outside of the function's scope (it's all self-contained). We plug in the new information as an argument, which is received in the parameter `part`. Eventually when it reaches the return, every descendant is added to the children table of the function call that started the recursion. Technically you could do this with a loop too but recursion suits this better. Actually it's better if we differentiate between how both approach this situation:

- **Looping** will keep rerunning the code until the loop breaks, it can update its information every *iteration* (every time it "loops"). Though you want this updated information to be available on the next iteration instead of redefining the variable, so you'll have to create it in an "upper" scope (which can get untidy). Reusing variable names might get hard and using shadowing can make your code look a bit confusing. Of course you can wrap this around a function to fix all that, and return the values obtained when the loop finally breaks.

- **Recursion** on the other hand, has another go at the problem until it hits a return. This is like finding a cave in a cave in a cave in a cave and so on (where every exploration is different, values plugged in aren't the same as the previous "cave"), only stopping until you have satisfied your condition (which will return). Any return will exit recursion. Suppose our condition is going until we hit the bottom (we also don't use the *uniqueness* of every cave to do something, we simply go a tier deeper), if it's as simple as that then recursion *probably isn't what you need*, it has some downsides. Problems as simple as that are missing the point of recursion. Before we get into the true nature of recursion, we need to first know what holds it back from being better than a loop in most cases.
## Choosing to recurse, choose wisely
A recursive function by definition is a function that has a segment where it calls itself (usually plugging in new values each time it does so). This requires the creation of a new stack frame every time this happens, and the CPU has to keep a track of the previous stack frames as recursion goes on. All of this goes into memory. Though this having too much memory usage is not the case 99% of the time, practically every modern device can tank the memory usage and still not have a dent (most memory usage comes from the physical world or *data leaks*).

So we know that recursion practically doesn't really take much memory (it's also freed when recursion ends). Though we also know that no device has infinite memory, what if we just keep recursing?

This results in what's called a **stack overflow** (what the popular site is named after). There's a different stack that's used to keep track of calls, which uses the concept of *stack memory*, which you can think of as plates stacked on top of each other (where plates represent any datatype). You can keep stacking, but at some point you'll reach the limit and it'll "topple over" (or, overflow). This isn't due to plates falling over from physics, this is because every call stack is allocated a limited amount of memory. Before they are created, they need to ask the operating system "I need this much memory, tell me which block in the RAM I can use". This allocated memory is, finite. Overflow happens when we try to exceed what we're given. There are other kinds of overflows in memory too. Lua and Luau's behaviour for stack overflow is to throw an error.
> By a "block (of memory) in the RAM", I mean a literal block. It's a single object that isn't chopped up into other pieces, it's continuous and packed closely together point A to point B. This "block" in technical terms is called an "array", and CPUs love them because the data is stored close together (physically). Using arrays in the stack format fits them naturally, and since the concept of a stack is so simple, it keeps things fast. Variables are unloaded by "removing" the plates from the stack (starting at the top).

Every thread has its own call stack. It'll overflow when recursion goes 20000 calls deep, that is the limit. Tail-call optimized functions exist which reuse the same stack frame every recursion (so every call uses that). This allows the recursion to continue "forever" without overflowing. More on them near the end.

Loops don't overflow because they don't need to create new "frames" every time they want to loop, they just run the code again by jumping execution to the start of the loop.


You will always get stack overflow if you don't have a condition that returns the function to exit before you reach your limits. If you don't have a condition and just return.. well that's not really recursion. Always make sure that your condition can be satisfied from what the function is doing unless you like red messages in the output.

# Task 1: Create a recursive function that counts up to 100, and print the result. Use parameters to plug in new data.

**Hint:** Don't use any outside=variables. Input goes in and changes every call, which is passed back into the function until we reach 100.

Don't forget to initially call the function with the starting input (as an argument).

This isn't one of the practical examples I was talking about.

<details><summary> Answer </summary>

```lua
local function count(start)
    start = start + 1
    if start ~= 100 then
    count(start)
    end
    print(start) --doesn't ever reach here until it stops reaching count()
end
count(0)
```
</details>

## When to do recursion

From what I've said, recursion just looks like something you'd do to look cool. In most cases, sure. However you can benefit from it if you use it to solve the right problem instead of as a way to replace loops. Recursion mainly benefits from propagation in a problem.

## Propagation

Your great grandfather had kids who had kids who had kids (don't know if you will). When a set of data can be described like this, we can say that **propagation** is one of its properties. Your great grandfather propagated offspring whom had more offspring, where the amount produced each time can be different. Basically, this data is organized in a **hierarchy**. The **datamodel** in Roblox starts a hierarchy of **instances**, which I will describe as anything you can see in the explorer (even the *services*). Again, there is a parent-child relationship. When we have questions about this hierachy (e.g., what is every descendant of an instance?) we benefit from solving this recursively because hierarchies have the property of propagation, which go ball with recursion. 

Why do we benefit here? Try this analogy..

Propagation means spreading. Specifically, one thing creates another, or a few others, where the things that were produced might do the same. You can imagine these as roots in a tree. You have branches going down, some end early, some just keep going. Many branches are still growing. Branches branch into other branches and create new branches (crazy sentence). Propagation in the context of programming would require the things to have a *link*, everything should be linked somehow. In Roblox, instances are linked with the parent-child relationship. You can do `.Parent` to get the parent, or use a child's name to get a certain child from a parent. This allows us to trace the hierarchy, which is a defining feature of propagation.

Many Roblox APIs regarding traversal of the datamodel are recursive functions, including `GetDescendants` and `Destroy`.
> Destroy in particular has to recurse through the children of the instance in order to destroy them too. Some related APIs aren't recursive, like `FindFirstAncestorWhichIsA`, which just traces the parents until it finds one of the type you're looking for. This means hierarchy doesn't automatically = recursion better.

# Task 3: Rewrite the given function with a loop.

```lua
local function wonkyCounting(start)
  if start > 50 then
    start -= math.random(1,2)
    print(start)
    wonkyCounting(start)
    return
  end
  if start < 50 then
    start += math.random(1,2)
    print(start)
    wonkyCounting(start)
    return
  end
  
  --will only reach here once start = 50
  print("Finally, Fiddy!!!!")
  return
end

wonkyCounting(35)
```

<details> 


<summary> Answer </summary>

```lua
local start = 35
while true do
if start > 50 then
start -= math.random(1,2)
continue
end
if start < 50 then
start += math.random(1,2)
continue
end

print("Finally, Fiddy!!!!")
end
--it's fine if you wrote a version without the continue statement
--as long as it works

--also you could wrap this loop in a function
--bonus points if you thought of that

```

 </details>

Recursion is a core concept in functional programming.

We have more to talk about functions, though this article has to end here. Yeah that's right we're going to need a third one on functions 🥶.