Have you noticed any similarity between everything that is in the explorer tab on Roblox? Just about everything has `:Destroy()` on it, even if it looks cursed:
```lua
workspace:Destroy() --this'll error, saying that the parent property is locked

--this is because part of what "Destroy" does is setting the parent to nil
```

We can further observe the commonalities between the "explorer objects". Each one has `GetChildren`, `Clone`, and `IsA`. Other than the functions, we also have common properties like `Name` and `ClassName`.

This is because anything that can be viewed in the explorer is an **instance**. This includes every service.
> Roblox doesn't show all of its services inside the explorer because they have too many.

In [Language/03](<../Language/03 Data types and Variables>) I mentioned one of Luau's primitive datatypes as follows:

>- userdata
>   </br> Describes a type created by the host language and not Luau.

That basically means it's a datatype that Roblox provides. Emphasis on "*describes*", a userdatum (singular) is special and could be pretty much anything. So the datatype `userdata` alone doesn't tell Luau anything about a value, it doesn't know for sure that `userdatum + userdatum` is legal, because it could be `Player + SurfaceLight`. One of the notable userdata types is `Instance`. Nearly everything that Roblox's engine provides **inherits** from it.

Inheritance is when a class (container) passes down functions and data to an object. For `Instance` (no pun), all of its objects inherit the functions I mentioned and more including `WaitForChild` and `FindFirstChild`, with data like `Name`. Inheritance happens in a sort of chain, which looks like this:


![alt text](<../Images/Roblox type tree diagram.png>)

(*Open image in a new tab if you're having trouble seeing*)

`Object` is the "main" type every other *Roblox type* inherits from. It doesn't have anything to inherit from `userdata` (because `userdata` itself doesn't have anything), it's more like an indicator for Roblox types. `Instance` takes functions like `IsA` from `Object`, which `Instance` then passes down to whatever inherits from it. 
> `Instance` has the most Roblox types that inherit from it, 358 to be exact.

Inheritance here is great because Roblox's engineers now don't have to put all this data inside every new type they make, they can just say "*this class inherits from that other class*", and it keeps chaining upwards to other classes if inheritance links them.

Important clarification, classes can inherit from other classes. Objects that belong to a class inherit said data. E.g. a `Mesh` will have everything from `PVInstance`, which is a class made for things that have a place in the world in some way (like `Model`s).

Another clarification.. Roblox provides the `Instance` **type** and the `Instance` **global**, which are separate but have the same name. A lot of people get confused when they find the wrong documentation page (i.e. they intended the other one). So because I'm such a nice guy I'll list the proper webpages for you:

**The type:** https://create.roblox.com/docs/en-us/reference/engine/classes/Instance
> Note that Roblox's official documentation sometimes uses the words "*type*" and "*class*" interchangeably.

**The global:** https://create.roblox.com/docs/en-us/reference/engine/datatypes/Instance
> A global is anything you can access in the global scope (i.e., anywhere in the script) like `print` and `math`.

# The ambiguity
Roblox has a huge problem with sticking to a single strict definition, which creates uncertainty and just makes researching for these articles that much harder. In the above webpages, you can see that the `Instance` **type** is labeled as a "*class*", technically it's not wrong (albeit weird). Types can have classes too (that's how inheritance between userdata types works).

Here's where I lose it though.

The `Instance` **global** is labeled as a "*data type*". That is so blatantly misleading and not literally* true (probably the reason why looking for that webpage is confusing). Funny thing is, it's not even listed under the so called "*Roblox globals*" section. Being true to real programming principles, I'll continue calling it a global.

# The global
We gain access to two functions through this, `new` and `fromExisting`. Both are constructors, which are functions that create objects.
> "new" is a conventional name for constructors in object-oriented programming. Constructors are functions that create objects.



## new
This takes the `ClassName` of the instance and returns the constructed object (that will have no parent by default). This object will linger in memory if you hold a reference to it via a variable and still don't give it a parent (aka, a *memory leak*).
> A memory leak is when something is put in memory but is never released, this is bad and can accumulate over time.
```lua
Instance.new("Part") --creates a new part
--this doesn't have a memory leak since there's no reference held to the part

local model = Instance.new("Model")
--this will stay in memory forever unless if we destroy it or stop referencing it
model:Destroy()
--or
model = nil

--note that it'll never get freed if ANYTHING holds a reference to the object
...:Connect(function(part)
--now the part is simply held hostage
--it might not exist in the world (have no parent), but it's still going to be immortal in memory
while task.wait() do end --we will never leave this thread, so the reference will live on forever
end)
```
> Doesn't matter if there's a reference to the instance if it's parented to something in the game, it won't free the memory.

This also takes a second argument, the to-be parent of the object. You shouldn't use this, it's really bad for network performance. This is 'cause you usually want to change some properties of the instance after you create it:

- Instance created and parented (in the same function call)
- Transparency and color changed, positioned up in the sky to fall down
- Once the part is sent through the network to everybody, the server now has to send the property changes one-by-one

That's really inefficient. It could have been done in a single trip if the parenting happened at the end (which is what you should do)

![alt text](<../Images/lebron james caught using.png>)

It's not really a big deal but can get annoying. This way it always takes multiple lines to create an instance. Though we are smart so we create a better `Instance.new`

## Instance.new The Sequel
```lua
local function new(ClassName, Parent, Properties)
local inst = Instance.new(ClassName) --the only way to create instances

--this is assuming Properties is a dictionary like
--[[
{Transparency = 0.5, Anchored = true}
]]
for property, value in Properties do
inst[property] = value --set the property
end
inst.Parent = Parent --can be nil
--but the parent property takes nil too
--it'll remove the instance from the world
end

--Let's use it

local part = new("Part", workspace, {
    Transparency = 0.5,
    Anchored = true,
    Position = Vector3.new(0,5,0)
})
--we do it all inside one function call without the network issue
```
This version is more ergonomic, it's easier to write `new` and write out your properties right after.

Credits to the Parkour Legacy source code for initially showing me this function.

# Extra: The second function
I mentioned that the `Instance` global had two functions, `new` just happens to be the most used one.

That other one, `fromExisting`, is similar. It's exactly like `Clone` but with some differences.

- Copied instance will not have the descendants of the original instance under it
- `fromExisting` does not respect the `Archived` property.

```lua
local baseplate = workspace.Baseplate
baseplate:Clone() --will also put baseplate's descendants inside this clone

Instance.fromExisting(baseplate) --creates a copy of baseplate without the descendants
--which include its texture
```