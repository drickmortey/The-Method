**Authored by drickmortey**</br>
**Not reviewed yet**

### Resources needed:
- https://play.luau.org

# Evaluation?
This is one of the most ignored topics in Luau scripting. I believe this is the necessary step before learning *ternary logic*. How does evaluation happen? What is it?

Before that, let's discuss what an expression is. These are all expressions:
```lua
(30/100)*5000 -- 30% of 5000

50+.5

math.random()*10 --random number from 0 to 1, raised a decimal digit
```
What do all these have in common?

They all can be solved, simplified until one absolute value remains. The result of `math.random()*10` is always a single number. The process of solving an expression until only *one* value remains is called **evaluation**.

An expression is a collection of mathematical operations all acting on each other in some way, always converging down into one value even if we can't fully solve for it. Here, this isn't solveable, but we know it eventually makes a single number: `xy^2`. 

Most frameworks in math use the **BODMAS** order of operations (some differ). Since it's the most universally agreed upon set of rules, Luau uses it too, to determine which part of an expression is solved for first (as we'll see, some operations are not "**commutative**"). 

 An algebraic expression has the possibility that it is unable to be solved and can only be simplified. An expression in Luau, however, can always be solved. If it can't? Well that's just going to error.

 ## BODMAS
 We established that this is the order of operations Luau uses, we need to solve expressions in our head using this ruleset. It determines which operation has higher *precedence* (importance) over another. It's in this order:

 **B**: Solve brackets first, starting with the deepest ones.

 **O**: Then simplify the exponents where possible.

 **DM**: After that, do division or multiplication (whichever comes first).

 **AS**: Finally, do addition or subtraction (whichever comes first). 
 > This is sometimes called PEDMAS or PEMDAS in other countries, it stands for the same thing.

 The order in which we do things is very crucial, as some operations are *non-commutative*. This means if you swap around the numbers, the answer *will* change, in most cases (`2-2` is the same no matter the order). 

 ```lua
/ --division, non-commutative
- --subtraction, non-commutative

--Their inverse operations are commutative

+ --addition, commutative
* --multiplication, commutative
 ```

In commutative operations, the order of the *operands* does not matter (`5*10` and `10*5` are equal). An operand is an input in an operation, e.g. `operand1 + operand2`. The order starts mattering when an expression (collection of operations) includes non-commutative operations like division, subtraction and exponentiation (that one's obvious). You may technically not be wrong (no errors in calculations), but you may get different answers depending on what you simplified first (and with what). BODMAS aims to make it so that there's only one true way to solve an expression.

# Task 1: Solve this in your head: `((5-3)/2 + 2)*4^2`
**Hint:** Refer back to what BODMAS stands for if you need help. Innermost brackets first, working your way up.

Brackets say "treat everything inside as one value", as they too, contain an expression (within an expression). `(2-3)+1`, where the bracket necessitates we treat `2-3` as a single number, and release it from the brackets once we have simplified it down to that number.

<details>
<summary> Answer </summary></br>

```lua
--Let's go step-by-step

((5-3)/2 + 2)*4^2

(2/2 + 2)*4^2 --remove brackets once they contain one value inside (solved)

(1 + 2)*4^2

3*4^2

3*16

48 
--and that's how Luau does it
--We can rest now 💤💤
```

</br>
</details>

# Task 2: Add brackets in order to change the result of `5 - 9 * 3 / 2` from `-8.5` to something else.

**Hint**: Print the exact expression in Luau but with your brackets added. See if the answer changes.

<details>
<summary> Answer </summary></br>

```lua
--This is one of the answers

(5-9) * 3 / 2

-4 * 3 / 2

-12 / 2

-6
```
</br>
</details>

Using brackets like these will be very helpful in programming. Often times you'll need multiple operations to create a value that represents something, then use that value in the same expression. We can enclose it in brackets to treat it as a single number amounting to whatever the expression inside evaluates to. Example:

```lua
local a,b,t = 5,10,.5
print(
a + t*(b - a) --linear interpolation (lerp for short)
)  

--a represents the starting point
--(b-a) represents the distance from a to b

--the distance formula is generally (goal-start) in math

--and finally, t represents where in-between a and b we go (t is between 0 and 1).

--this allows us to get a number, between two numbers.
```
`(b-a)` represents a value that has a particular purpose. You wouldn't know what that purpose is if I didn't tell you in *comments*, so always explain those parts of your code.

---

What I just told you about is called **numeric evaluation**. There's actually three kinds. Functional evaluation is a later topic. The other one is **boolean evaluation** (I told you they weren't useless).

First we must learn boolean logic.

In this framework, there are only two input values, one or zero (true or false). In our case, we are provided with three basic components (**AND, OR, NOT** gates) that will either emit true or false depending on the inputs we plug into them.
> In CPU circuitry, a 1 (true) is when there's current flowing through a certain point (a microscopic wire). 0 is when there is no current flowing. You can create a turing-complete machine with just AND-OR gates (assuming memory is provided).

> Boole was the guy who came up with this stuff, it's related to set theory.

There's an important twist that Luau adds to this. This logic can be applied to non-boolean values as well, which we can use for all sorts of stuff (like the mysterious ternary logic I mentioned). We'll see how this works once the three basic components are listed.

Important to note is that the AND-OR components are **binary operators** in Luau and in theory. This means that they take two operands. The NOT component is a **unary operator**, meaning it takes only one operand. An operand is an input.

## AND gate
Emits 1 if both inputs are 1, else emits 0. In Luau, this means if both inputs are true, the output is also `true`.
```lua
print(true and true) --what does this emit?
print(true and false) --what about this?
```
---
## OR gate
Emits 1 if an input is 1. For Luau, this means at least one input is true.
```lua
print(false or true)--easy
print(false or false) --too easy

--try this
print((true and false) or true)
--NOTE: evaluation starts left, and goes right with respect for brackets.
```
---
## NOT gate
Takes one input and flips it. 1 into 0, for example. 
```lua
print(not true)
print(not false)
print(not not true) --flip it twice what do we get

--remember what we talked about
print(not (true and false or true))
```
---

Those are all boolean expressions, they can always be solved (meaning only one value remains). These expressions are evaluated from left to right.

Up until now, we have only talked about logic with static `true` and `false` values. But I also mentioned this works with non-boolean values. Let's talk about boolean evaluation.

To account for more than 2 kinds of values (I mean, boolean logic takes only true and false), Luau has come up with the concept of **truthy** and **falsey** values.

The rule is, a value is truthy if it's not either `false` or `nil`. Everything else is truthy, including `""` and `0`. Conversely, if your value is false or nil, it's falsey. This allows us to group all kinds of values back into the same 2 kinds boolean logic will take. But we talked about emitting ones and zeroes with the logic gates up there?

Here, our 1 is a truthy value, and 0 is a falsey value. 
```lua
print(20 and "do you get it?")
```
We can see that both inputs are 1, so what does this emit? If it's your first time, you're probably thinking `true` or `false`. It actually emits the second input, `"do you get it?"`. This is Luau's implementation of logic gates.

What we discussed in the logic gate list is highly related, though there's a subtle difference here. Instead of emitting a static 1 or 0, it emits the second input (which could be anything other than `true` or `false`). Let's go over the logic gates again.

## AND
Emits the second input if the first is truthy. This is useful for checking a condition before emitting your intended value. Will emit the first falsey input if the AND isn't satisfied.
```lua
print(character and character.Humanoid)
--Only look for "character.Humanoid" if
--the character exists (we can trust our code to produce either nil or the character in this case).
```
---
## OR
Emits the first input which is truthy (first, from left to right). This is useful for default values in case that the one you calculated was invalid or non-existent.
> Will emit the second falsey input if both are falsey
```lua
--receive input
local PartName = input or "Part"
--if we got no input, we can default it to "Part".
--This is because "input" will be falsey, triggering the OR
```
---
## NOT
Converts truthy values into true and falsey values into false, emits the flipped value of the conversion. Good for converting into boolean for specific cases. Mostly used for statements that detect falsey values.
```lua
--receive input
local CharacterName = input

--suppose CharacterName is nil
--it's converted into true
--which runs the IF block
if not CharacterName then
--this block will only run if CharacterName is falsey
--we can trust our code to have it either a string or nil
promptTryAgain()
end
```
If these use cases are difficult for you to remember, you just have to use them and you'll recognize the pattern forever. You can then easily recall the exact pattern you need when programming.

## Chaining boolean operators

When boolean operators are put in series, it looks kind of complicated. Suppose:
```lua
1 and nil or "" and 1000
```
Break it down individually, every operator takes only two inputs, with their first input being fed from the output of the operator behind it. Up there, we started the chain with the input `1`. Read it slowly and find out what the entire expression emits.

Theoretically, you can chain together infinite boolean operators. We aren't going to do that, but try your shot at finding what these boolean expressions will emit:
```lua
--here's a demonstration
local sprinting = false
print(sprinting and "Yes run" or "Why are you not running")
--let's evaluate (left -> right)

--the AND has one input that's falsey, so it emits the falsey value.

--up next, the OR receives the falsey value
--it will emit the first truthy value it got
--that would be "Why are you not running"

--There are no more operations left,
--the expression has been evaluated.

--Here, buster
local bankrupt = false
print(not bankrupt and "where's my money" or "survive another day")

local replacedByAI = true
local JohnNoob = true
local PersonReadingCourse = false
print(JohnNoob and replacedByAI
 and (PersonReadingCourse and replacedByAI) and "Oh no!"
 or not (PersonReadingCourse and replacedByAI) and "You'll never get replaced!!")
 --Take your time and use what I taught you.
```
Getting tired? Not ready for ternary logic yet? Well, we're already over that. The `and-or` pattern I've shown multiple times is the main way to do ternary operations. What "ternary" really means is that an operation takes three operands, we chain together binary operators to "mimic" it, Luau has no true ternary operators.

# Task 3: Write a boolean expression which has 8 possible conclusions depending on inputs.

**Hint:** This is a trick question!

<details>
<summary> Answer </summary>

```lua
local c1,c2,c3 = true, true, false

print(c1 and c2 or c3)
--the amount of conclusions is 2^n
--where n is the amount of inputs

--2^3 is 8
```

</details>

We will not be talking about functional evaluation because we haven't talked about functions in detail yet.

# Extra: The `if-else` expression
Not to be confused with an IF block, this is an alternate version of the `and-or` pattern. This only exists for one reason.

**There's actually a problem with the `and-or` pattern.**

You see, we use the `and-or` pattern to say "if this then that, else use this default". All of this is in one expression and not much fuss. Compare that to its equivalent with an IF block:

```lua
--let's debug this hypothetical program
--we want to know the state of 'running"
--while the code is running

--suppose we don't know the running state for example purposes
--(this will be true or false)
local running = math.random(1,2) == 1

if running then
print("Running and Running")
else
print("Not running yet")
end

--This is the same:

print(running and "Running and Running" or "Not running yet")

--both mechanically produce the same thing
--one does it in a single expression,
--other requires you to zoom out and
--add a code block dedicated for it
```
So it's obviously much better right? I think so. Too much ternary logic can make your code hard to read though.

But where's the problem? Not in that example. It's pretty specific, `and-or` is completely fine for most cases. Let's look at the problematic case:

```lua
--suppose we want a static boolean value for if a child isn't found in a Roblox instance
local noChild = workspace:FindFirstChild("Folder") and false or true

--looks fine right? 
--let's evaluate
--suppose the child is found
--noChild should become false

child and false or true

false or true

true --?? this is the exact opposite
```
`and-or` has this weirdly specific problem that happens when the starting input (sometimes called the condition) is truthy and the second input is falsey, it will take the branch path that 9 times out of 10 you did not intend (here it evaluated to `true` instead of `false`).

Isn't this a bit too silly to add a brand new language feature over? Well, thing is, `and-or` can be a bit hard to read (especially if chained with more things). The `if-else` expression makes the intention of "*if this then that, else ...*" explicit instead of behind circuit logic.

Let's try the same example with an `if-else`:
```lua
local noChild = if workspace:FindFirstChild("Folder") then false else true

--No boolean evaluation here, just "if this then that or the other thing".
```

You can use whatever you fancy. The `if-else` is an expression that must be used inside a statement (statements contain expressions) though, it can't be alone. Keep in mind that `if-else` only solves this very specific case of `and-or`. If you need more complex logic in a single expression, you need to chain together more boolean operators. Try not to make a turing-complete machine with it.