**Authored by drickmortey**</br>
**Not peer reviewed yet**

We only ban players if they break rules in a game. We can't just.. ban random people. How do we do this?

```lua
local misbehaving = false

if misbehaving then
print("Hey!!")
end
```

Here we only want to print that *if* a condition is met.

A lot of what I will be talking about is already explained in excruciating detail in article 04. So this one will be relatively short.

An `if` statement creates a code block. But the catch is, it will only run when the condition it is given evaluates to a truthy value (non-nil non-false). In the bytecode, when the evaluation brings a falsey value, execution is jumped to the very end of the `if` statement (thus not running it). This is the only special thing about these code blocks, other than that they follow the standard rules of lexical scoping. Let's write one:

`if` starts the definition, follow it up with a `condition` that will be evaluated to either truthy or falsey, after that there's a `then` that acts as `do` but for `if` statements. Exit the code block with `end`.
> A condition is always a literal or an expression

```lua
local character = Player.Character
if not character then
--useful for catching when the character doesn't exist, executing "fallback" code in the process
end
```

Putting `if` statements inside other `if` statements is called  **nesting** them, having too many nested ifs makes your code truly incomprehensible. I'm personally a never-nester.

What this means is that I *invert* the condition and the code that is "triggered" to achieve the same result without nesting any more.

```lua
--Only works inside a function. I will gloss over this as we haven't talked about them yet


if mainCondition then

    if condition then

    end

    if not condition then return end

--these are both mechanically the same in most cases
--except the second one avoids nesting further

end


```
> The "never nesting" variation is also sometimes called a guard clause, used for "sanity checks". Buzzword Buzzword. Not relevant right now.

# else
This creates another block under the `if` block, which will always run in case the `if` does not evaluate the condition to a truthy value. Also acts as an `end` to an `elseif`. Must always be the last "case" in an `if` block.

```lua
local coding = true
if coding then
print("Keep going!")
else
print("End your career NOW")
end
```

# elseif
Tests another condition when the `if` does not evaluate to a truthy value. This was introduced to avoid extreme nesting in cases where you wanted to check other conditions when the "main" one failed. The author of the Lua manual says that another benefit of these is that their own code block does not need to be exited with its own `end` (it will use the one from the `if` block). This cannot follow up an `else`

```lua
local gay = false
local badPoliticalOpinions = true

if gay then
print("That's fine")
elseif badPoliticalOpinions then
print("This is not fine")
else
print("Probably sane")
end

if false then --main block doesn't run
else
print("Excuse me?")
elseif true then
--this is breaks syntax and will error
--an "else" must always be the last case
end
```

That's all there is to it.

Just for the formality we'll go over the relational operators which are used mainly in `if` statements, though they can be used in other places too.
> Remember overloads?

# ==
This trips up a lot of beginners. This is NOT the same as `=`, which is the assignment operator. This is the equality operator, it tests for equality between two values (after their evaluation), giving us a true or false. This operator is commutative.
> Some C-based languages have === as a "true equality" operator, it doesn't do any "conversions" unlike ==

```lua
local isEqual = "yello" == "hello"

if isEqual then
print("The strings are broken!")
end

--or

if "yello" == "hello" then
--yardy know
end
```

# ~=
Checks for inequality. Gives true when the first operand isn't equal to the second. This is commutative.
> Some languages have this implemented as !=
---

 ### All of the below are not commutative.
---

# >

Gives true if the first operand is larger than the second, false otherwise. Strings have an overload for this.

# <
The opposite. Gives true if the first operand is smaller than the second, false otherwise.

# >= and <=
The greater than and less than symbols but with another condition "or equal". `>=` gives true if the first operand is greater than or equal to the second. 

`if` statements are the rudimentary building blocks of logic, ternary and similar "short-circuit" logic use these in the background.

---
# Task 1: Make a coin flip program, where a random number `n == 2` is heads and `n == 1` is tails. Use `local n = math.random(1,2)`. Print the result, heads or tails.

<details> 
<summary> Answer </summary>

```lua
local n = math.random(1,2)

if n == 2 then
print("heads")
elseif n == 1 then
print("tails")
end
--[[else
print("tails")

this works too because there are only two possible outcomes

if it's not the first outcome, it's obviously always the second
]] 

```
</details>