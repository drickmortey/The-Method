**Authored by drickmortey** </br>
**Peer reviewed by Downrest**

> Try the code examples at https://play.luau.org. `ctrl+enter` to run your program on desktop.

## Code execution
```lua
print("Good job") print("How is it going")
print("Did you even check the previous reads")
```
> This will display messages in the "output", it's useful for getting feedback from code that would otherwise stay silent when you run it. The output is the bottom tab on the Luau Playground site.
<p>
When running the code above, which of the two lines is executed first? In what order do the prints send their messages to the output?

It'd only make sense if it starts top-down, which it does. This means our code runs line-by-line. Though this is kind of misleading as you can have multiple **statements** in a single line, so it jumps from statement to statement, from left to right. It will only "move down" after the entire line is empty. actually your entire program could be in a single line.

`local counter = 1 for i = 1, 100 do counter += 1 print(counter) end` 

The key here is **whitespace**, which includes characters like indents, spaces, and </br>line</br>breaks.

> Press `tab` for indents, `enter` for line breaks, and the spacebar for.. spaces. There are 3 more whitespace characters, but these are the most relevant ones. 

> Reading your code is done by a program called the VM (virtual machine, also called the interpreter) which reads **bytecode** and executes the machine code that it thinks resembles your code the most.

Words are separated by *any* whitespace character, and some words put together can form *statements*. Here's a simple statement with indents for separation.

```lua
local   x   =   5
```

### Task 1: Rewrite the statement using all the mentioned separators.

<details>
<summary> Answer </summary></br>

```lua
local   x =
5
```

</br>
</details>
</br>

So words are separated by any whitespace character, which may form a **statement**. I've been highlighting that a lot.

A statement is a special instruction that says something like "create a container `x`, and put `5` inside it". In C++, every statement must be ended with a semicolon `;`. Luau being primarily made in C++, also has this feature (just because it can, I guess). It's optional to use though, and only exists to increase readability in code. **This is also a separator**.

Though there is a neat trick we can use with it. You'll get an error if you use the semicolon incorrectly, so to validate if a group of words is a statement, put a semicolon at the end of it. 

# Writing code

English has words. You can write words. But there's rules, words must be separated by spaces. You need to put full stops at the end of a sentence (how C++ needs semicolons..), there's a proper sentence structure. That's how you get meaning. There's consistent rules to define this.

Similarly, programming languages have this too. This is called **syntax**, the grammar of a language. There's a specific way you must write something in order for anybody to derive any meaning from it.

The syntax of Luau is primarily based off of whitespace. Python as a language requires colons in its syntax to define "blocks", and indents define where they start and end. Luau has replaced most of this with "any whitespace character". We'll discuss **code blocks** soon.

The words of Luau are, plainly put:

- Keywords</br>
 You'll notice they have a reddish glow in Roblox's script editor. They control the flow of your program and command Luau. Some examples: `local`, `if`, `then`, `else`, `end`. You can call out anybody's name, `bart`, and Luau wouldn't know what to do with it. These keywords will tell Luau what you want to do to `bart`.

  Some keywords are simply part of the syntax of another, this means that their proper legal syntax includes usage of multiple keywords. E.g., `for` requires you to follow it up with `in` then `do` (in the table-iterative version). `repeat` must be ended with `until`, `do` must be exited with `end`, `if` must have `then` after the conditional expression.

  ```lua
  --Look at how these statements require using multiple keywords, unlike "local". You can see they are keywords because they light up in a specific way (varies by editor).

  repeat ... until ...

  for ... in ... do ... end

  if ... then ... end

  while ... do ... end
  ```

---
- Identifiers</br>
  Names of things. You can call a container `x`, or `y`, or `JohnLuau`. When the error is `Expected identifier ...`, it really just means that it expected a name to follow what you wrote, e.g. here:

  ```lua
    local --this is going to error, it wants an identifier straight after (separated with whitespace). it doesn't know what to do with just "local"
  ```
  > In these errors, \<eof\> means "end of file", it went through everything and couldn't find anything that fixed the problem.

  An identifier has to follow the rules below

  - Must start with a letter or underscore (`_`)
  
  - Only contains alphanumerical or ASCII-supported characters that aren't recognized as part of the language, like the operator `+` or `(` and `)`. A commonly used ASCII character to represent spaces in an identifier is the underscore `_`.
  > Alphanumerical means numbers or letters (**alpha**bets).

  - Does not have spaces or whitespace, `nam e` is not a valid name.

  - Cannot be a reserved name (like any keyword. `local local = 5` is illegal).
  > Find all the reserved names here: https://create.roblox.com/docs/luau/variables. Scroll down a bit, you'll find it.

  #### <i>Also, note: VAR and var are different identifiers. It's case-sensitive. lUAU and Luau are different, for example. </i>
---
# Task 2: Write four different identifiers using only the letters "var", with the `local` syntax.

- **Hint:** The syntax is `local identifier = literal/container`. We can set the value of our container to another container too. We can give it no value with just `local identifier`, this is called "not initializing" it. More on that later.

<details>
<summary> Answer </summary></br>

```lua
local var = 0
local Var = 0
local vAr = 0
local vaR = 0

--bonus
local var
local var
local var = 5
```
</br>
</details>

> Notice how the same identifier being the name for multiple containers is no problem? This is called **shadowing** and we'll talk about it once we get to what a "container" really is.
---

  - Literals</br>
    To be brief, these are just "values". Everything in this list is a literal: </br>
    ```lua
    ""
    5
    100.5
    "hello I'm a literal"
    10+10
    ```
    Containers contain literals or references (not important right now). When you mention the identifier (name) of a container, you're basically saying "whatever value the container holds, load it in its place".

    ```lua
    local x = 5
    print(x) --"x" is a container. What shows up in the output?
    ```

    Notice how literals, keywords and identifiers alone will error. Simply writing "local" gives that error we saw earlier, so what won't error? 

    #### Luau wants statements. Anything that isn't a statement or a part of one, will error (usually as a syntax error). The interpreter reads statements in proper (left to right, top-down) order to find the machine code that represents it, it searches for meaning written with consistent grammatical rules (syntax).
    These statements follow a common pattern, e.g. `local` is **always** continued with a whitespace and an identifier (or a keyword that can take it). The interpreter basically finds these patterns, it's a program made to do that. If it sees a statement that has a pattern it doesn't know about, it's going to throw an error.
    > You can use the semicolon trick we saw earlier to help you identify if a specific group of Luau words make a statement. Though this has trouble with statements inside statements. 

    ### These "word types" don't require any whitespace for separation

    - Punctuators</br>

    What is a semicolon? I mean, it obviously doesn't fall into any of what I mentioned earlier. It's a punctuator. Any language needs punctuators to separate things and add special "effects" that aren't possible with words. There's no word that makes the reader have a small pause, we use a comma `,` for that.

    Punctuators in Luau are used for separating things (e.g., as in a list) and enclosing things, along with something else unique to the specific punctuator. We have `,`, `;`, `[]`, `{}`, `()`.
    > You can call many of these **delimiters**, things in a language that determine what starts where and ends where. `"` is one of the delimiters for defining strings. Others include `[[]]`, `'` and backticks. 

    - Operators</br>
    *These are sometimes grouped with punctuators, but I will classify them as different*

    *Most* operators don't need any separation, these are characters that take operands (inputs) to define an operation, such as `operand1 - operand2`. The list goes `#`, `-`, `+`, `*`, `/`, `//`, `%`, `^`, `..` with all of them except for `#` having a compound variation as well. Some have context-specific behaviour, e.g. `-` also does unary negation.

    Some operators aren't for arithmetic and serve purposes such as assignment (`=`) and comparison of values `==`,`>`,`<`,`~=`,`>=`,`<=`.

    These are considered "operators" by roblox (well, they do take operands) but they are considered exceptions here, they must be separated in some way like the other kinds of Luau words:
    `and`,`or`,`not`. These are all boolean operators.
    > Only boolean operators require separation.

    # Task 3: Find all statements in the code below.
    It's okay if you get it wrong.

    ```lua

    --!nocheck
    local function greatestNum(n1,n2)
    --indents make our code cleaner
      if n1 > n2 then
      return n1
      else
      return 
      n2
      end
    end
    print(
      greatestNum(10,50)
    )
    ```
    > Notice the `--!nocheck` at the top; it must not have any statement preceding it (basically, must be at the top). It itself is not a statement. These are called compiler directives and change settings of the compiler that turns code into bytecode. There are directives for other things too, so a better name is *file directives*.

    > The directive we used is `--!nocheck`, which disables the typechecker. Assuming you're running the code at Luau Playground, you'll get a "type error" without it (not in Studio). These aren't "standard" errors and won't stop your program from running, but the red line can be bothersome. We'll discuss the terrifying type system when the time comes.

    You still have to do the task if you forgot.

    **Hint:** Because a separator is any whitespace character like a line break (foreshadowing), look extra close. A statement isn't necessarily the end of a line. </br>

  <details>
  <summary> Answer </summary></br>

   ```lua

    --!nocheck
    --Wherever -- is, there's a statement ending
    local function greatestNum(n1,n2)
      if n1 > n2 then
      return n1 --
      else
      return 
      n2 --
      end --
    end --
    print(
      greatestNum(10,50) --
    ) --

    ```
    
  </br>
</details>

# Task 4: Write a program that creates three containers having numbers, the third one being the sum of the previous two. Print the third container to the output divided by the first container.

**Hint:** Remember the syntax, `local identifier = literal`. Everything inside the brackets of `print()` will be outputted.

<details>
<summary> Answer </summary></br>

```lua
local x = 5
local y = 10
local z = x + y

print(z/x)
```

</br>
</details>

While our programs were extremely simple, they were still programs. Congrats.
</p>
