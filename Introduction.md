**Authored by drickmortey**

# Disclaimer

I will not assume you have any background in programming. If you do know what I will be talking about, it's better to still give it a read as I might have added information that's new to you. Make sure to do the tasks as needed.

> You do not need Roblox Studio to run some of the code examples here, try them at https://play.luau.org
---
---

# Getting started with Roblox game development

#### <i>Roblox games are made in Roblox Studio, which is only available on Windows and macOS.</i>

1. You're going to need a Roblox account, I have one dedicated for game development.

2. Download Roblox Studio here: https://create.roblox.com/docs/studio/setup and install it by double-clicking the completed file.

3. Open the application, log in, do the tutorial if you want. 

![alt text](image.png)

4. Create a new devplace, where you'll be creating and testing.

![alt text](image-1.png)

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

### That's all for the introduction.
