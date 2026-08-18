**authored by drickmortey**</br>
**not peer reviewed yet**

We have come a long way, our programs are going to get more complicated here on. So let's switch to dark mode on Studio (for all the dark souls out there).

`alt + s` in Studio, then search up "theme" in the menu that pops up. Put the setting on "dark".

This isn't a lot.. Though in the future I will teach you how to download and use an external IDE with all the proper extensions (along with the editor theme and programming font I personally use). Once I consider you a programmer, that is. That would be a real treat, trust me.

Read 04.5 if you want to know why some examples must be run in Studio.

# Repeating code
To the main topic, we want to run our code multiple times. Loops do that. There are different kinds with some differences. This is the one that all of them use internally:

## While
This is equivalent to an if statement in that it first checks the truthiness of its condition and then it runs its code block if truthy. Except when it's time to exit the code block, it checks the condition again and runs the code in the block once more when the condition is truthy by jumping execution to the start. This continues until the condition becomes falsey and **breaks** the loop.

The syntax goes, `while` keyword, followed by the condition, then `do`-`end` which creates a code block with scope.
```lua
--THIS IN STUDIO
while task.wait(.1) do
--evaluates the function first, which causes waiting
--the function gives us the actual time it spent waiting, which is a number (truthy)

--essentially this will loop forever
print("Pain")
end

while true do --true is always true so


--since there are no waits, this will crash the Luau sandboxed environment
--this is because the loop just runs too fast

--it's a common beginner mistake to not add any waits in a loop that runs forever..
end

while false do --Evil while loop
--does not run, condition is checked and loop is broken before it even starts
end

--let's finally do something less useless
local num = 0
local condition = true

while condition do --can't use a literal here, can't set true to false if we want to break
num = num + 1
condition = num ~= 50 --stays true if num isn't 50
--becomes false and will break the loop once it reaches 50
--mind that the condition is checked every time the loop restarts 
print(num)
end
```
Every other kind of loop uses this in some way. It's the most basic way to do looping.

To explicitly break any loop without waiting for a condition check, use the `break` keyword.

```lua
while true do
print("No intention to crash Luau")
break --damnit!
end
```
`break`s are usually put inside an `if` statement to break the loop on a condition.

`while` we're at it we should talk about `continue`. It's not a keyword, but syntax sugar for something Luau doesn't let you use (see 03.5). It skips execution to the end of the loop to force the next *iteration*. These are more useful in `for` loops but can be used in any loop.
> Loops can be in loops, so `break` and `continue` only work on the loop they're in.

Since loops focus execution on going through the same code multiple times, they "halt" the script (more precisely, the thread) until said loop is broken.
```lua
--IN STUDIO
while task.wait(.1) do
--focused on doin nothin
end
print("wimper") --will never print since the loop does not break

--common beginner mistake to think the code past an infinite loop will run (in the same thread)
```
You avoid this by using multiple Luau threads (coroutines), but they're out of our scope for now.

## Repeat
This is a "modified"* while loop. First difference is it checks the condition when the loop has reached its end (meaning code inside is run at least once). Second difference is that it breaks if the condition is truthy (kind of like an inverse `if`). Third difference is syntactic:

Declare a repeat loop with the `repeat` keyword, which also acts as a `do` (creates a code block) that must be exited with an `until`.

It cannot be exited with `end` because `until` does two jobs, the condition immediately follows it and it also exits the code block. Since every loop "uses" `while` in some capacity, we can say that `while` can "replace" every `repeat`. Though you could say that for every other kind, and they don't exist for no reason.. Try to use `repeat` loops where they're better.

```lua
repeat 
--code block
until condition
```
`until` here will only break the loop if the condition is truthy (contrary to the `while` loop where it breaks when the condition is falsey).

See this example where using `repeat` is actually cleaner and easier to write:
> Note that `task.wait()` with nothing in the brackets will wait for exactly one frame.

```lua

--this makes sense, you can almost read the repeat loop as a sentence
local stop = false
repeat task.wait() until stop

--compare that to..

local stop = false
while not stop do --what??
task.wait()
end
--we have to do "not stop" because "stop" being true only makes sense that it breaks the loop
--but while loops keep running while the condition is truthy
--so we have to flip it with a "not" in order to break the loop if stop is true
--this is confusing to read at a first glance
```

Sometimes you need to run whatever's inside the loop at least once. You'll know those times. `repeat` loop works in those times. Hard times.

## for
There's two kinds. There's a table-iterative one and a number-range one (that's what I'll call it). We will not talk about the table-iterative variation because we have not talked about tables yet (so that means we need a `Loops 2`).

Keep in mind that every loop has a condition that it checks every time in order to not loop forever (unless if intentional by having the condition always satisfy the loop). The condition here is if we've looped *for* the amount of times we have specified. It checks this condition before every *iteration*, meaning at the very start before it loops again.

We have four components here:

## i
Stands for "index", can be any name because it's internally just a local variable. This holds and remembers the **amount** that we talked about.

## start value
`i` will be initialized with this value, as in `local i = start`. 


## end value
The value of `i` is increased by 1 every iteration. If that value is greater than `i`, the loop will break (the range has been met).

## increment value 
This is an optional third value that specifies what value will be added to `i` every iteration. This actually determines what the for loop checks for every iteration.

### When increment value is less than 0
It will check for `i < endValue`
### When increment value is greater than 0
It will check for `i > endValue`

### When increment value is 0
That's gonna error ✌️

---

Changing any of these three values during iteration will not affect the for loop.

This is the syntax:
`for` keyword followed by the identifier to use for the index variable, with the other three number values separated by commas and a `do` to start the block. `end` to exit it.

```lua
for i = 1, 10, 2 do
print(i) --we can get the value of "i" since it's a local

--the condition here is i > endValue
end

print("\n\n\n") --couple of new lines

for i = 10, 1, -2 do
print(i) --let's go in reverse

--the condition here is i < endValue
end
```

Great you can now run the same code thousands of times without copy pasting it for hours.

# Task 1: Create a program that adds one to a variable `counter`, looping until it's greater than 100. Recreate this program in every kind of loop we have discussed. Which one is best for the job?

**Hint:** You've already seen this like three times. The `counter` variable must be outside of the loop in order to not declare a new `counter` variable each iteration that'd reset it to its starting value. Make double sure your loop doesn't run infinitely in order to not crash your browser tab.

Remember how a `repeat` loop checks its condition vs a `while` loop.

<details>
<summary>Answer</summary>

```lua
--while
local counter = 0
while counter < 100 do
counter += 1
print(counter)
end

--repeat
local counter = 0
repeat 
counter += 1
print(counter)
until counter == 100

--numeric for
local counter = 0
for i = 1, 100 do
counter += 1
--counter = i works too
end
```
</details>

God bless if you're still sticking around in the course.