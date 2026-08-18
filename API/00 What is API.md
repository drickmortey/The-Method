*Relevant resource:* [Guide](<../Guide.md>)
# Getting our definitions right
In technical terms, an API is something gives you access to a bigger thing (in a controlled and "safe" way) that you have no free access to. Luau has its own API, like the `math` library. That library is considered API because you do not have access to the underlying functions, i.e you cannot change their code, they are a unique and separate part built into the language with special properties (e.g., *fastcalling*). In fact, the functions in the `math`, `string`, `table`, and other Luau libraries aren't even written in Luau, they're in C++ (a much faster language). That aspect is mainly what makes it API.

API itself stands for **Application Programming Interface**, which is funky talk for "*independent program gives you a way to access and control it on its own terms*". Making games on Roblox, that independent program is the **Roblox engine** . That is not to say Luau doesn't have its own API, it controls how the programmer can access its more low-level components through said interfaces.

Engine API can be found here: https://create.roblox.com/docs/reference/engine.

Despite being labeled "engine API", it also lists Luau's API? You see I do have a problem with that simplification. So the definition for API that we will use throughout this course is strictly **the API of the Roblox engine.**

Without API to let Luau access the engine in some way, we can't really make games. We need to tell the engine "*make a part here*", "*copy this change to everybody's device*", or "*lick that player*". These are things that games need to be games.

Roblox API can only be used in Roblox's own Luau sandboxed environment. So Luau playground can't run that kind of code.