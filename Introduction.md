**Authored by drickmortey**
**Peer reviewed by Downrest**

# Disclaimer

I will not assume you have any background in programming. If you do know what I will be talking about, it's better to still give it a read as I might have added information that's new to you. Make sure to do the tasks as needed. *Some tasks may have multiple answers*.

> You do not need Roblox Studio to run some of the code examples here, try them at https://play.luau.org
---
---

# Getting started with Roblox game development

#### <i>Roblox games are made in Roblox Studio, which is only available on Windows and macOS.</i>

1. You're going to need a Roblox account, I have one dedicated for game development.

2. Download Roblox Studio here: https://create.roblox.com/docs/studio/setup and install it by double-clicking the completed file.

3. Open the application, log in, do the tutorial if you want. 

![alt text](Images/image.png)

4. Create a new devplace, where you'll be creating and testing.
![alt text](Images/image-1.png)

5. Once you're in, keep in mind that the newly created place only exists on your computer right now. You need to publish it to Roblox so that you can get autosave and access it later once you leave. **Press alt+P to publish**. You can also find this option in the dropdown menu that shows up when you click `File` on the top left; `Save to File` is here too.

### Pro tip: <i>Saving a copy of the place to your files will allow you to use it while offline, you will only need a connection to initially load Studio.</i>

6. In the menu that shows up, give your very own devplace a name more inspired than "<i>Untitled Experience</i>" and hit the `Create` button. If you can't find it, your taskbar is probably hiding it. Fiddle around and resize the window if you need to.

#### Good job, but we won't be using this devplace for a while.
---
---

# The language and its history

<p> 

   Roblox is launched in 2006 and they need a language that is simple and fast, easy enough for kids to understand. They pick Lua, it's a general-purpose embeddable language that would be easy to integrate into the engine. Their implementation of Lua is as a scripting language, and so developers could start writing scripts.

   After millions of scripts had already been written in Lua, its weaknesses couldn't be ignored. It was hard to maintain large programs and small mistakes could create very anomalous bugs.

   Thus in 2019, Roblox switched to Luau. It's a heavily modified version of Lua 5.1 that aimed to address the common developer complaints. Luau is faster, has more to it, and is still nearly identical to its predecessor. Code from 15 years ago will still work in this new language. Let's learn it. 

</p>

## Does light mode bother you? I'll show you how to switch later. Read it until you find it.
</br> </br>

> *You can skip this if you don't care*
# EXTRA: Who is Luau for?

<p>

Luau has primarily three target audiences.

- Roblox game developers (the main users),

- People who want a reliable scripting language to just build with (outside of Roblox) with all of Luau's features,

- People who use Lua but would like Luau's added features and quality of life improvements, e.g. the `continue` statement (very useful in game development scenarios).

Emphasis on that last point. Before Lua users jump in, there's caveats (get some lose some). No you shouldn't switch to Luau just for compound operators.

#### Lua is a general-purpose language, Luau is a **sandboxed** scripting language that wants to be embedded into an application.

Sandboxed means that Luau cannot do anything outside of its "box". E.g. it cannot access your system files (`loadfile` and `dofile` from Lua are completely gone). In the name of sandboxing, the `IO` (input output) library has been obliterated. The `OS` library is not nearly what it used to be.

> Embed means to include one software inside another, as a component.

And so the Roblox team embedded Luau into the Roblox engine. Shocker. You can embed Luau into your own engine too! Do you have one?

Lua and Luau are both maintained by different people who have different goals for the language. Luau doesn't agree with some of Lua's design choices. Past Lua 5.1, Luau has only adopted some of the new changes. Many additions aren't included, which you may or may not like. The dreaded and controversial `goto` statement from Lua 5.2 is absent from Luau. (P.S one of the Luau bugs is now a feature, completely adding behaviour not in Lua)

Luau is intended for game engines and has things to facilitate that (it comes with vectors, neat). Its VM is completely rewritten, has a type system to help with understanding and maintaining big, interweaved code, and its bytecode looks much different than Lua's. It wants to encourage "sustainable" programming with good practices and simplify game-dev from Lua. No doubt you can't write malware in properly sandboxed Luau, so you can expect your platform to stay safe.


# EXTRA EXTRA! The RFCs

This is a site dedicated to proposals for what to add to Luau and what to change. People make Github pull requests on the official Luau repository and add a detailed post describing what they want, why, and examples of it.

You can check out a list of what's being considered (or not, depending on status) right here: https://rfcs.luau.org/.

Maybe you'll write your own RFC one day and be a lawyer for type inference with class-object inheritance that ACTUALLY works (those who.. ykw forget it).
</p>

