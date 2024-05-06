layout: page
title: "Proxify"
permalink: /Proxify
# Proxify

Proxify is module that allows event-based callback functionality for tables, through the implementation of a 'Proxy' table to take advantage of the __index and __newindex metamethods.
This allows users to use Signals (either the built-in or your own Signal Class) with general or index-specific changes in a table.

Utilizing Proxify is as simple as:
```lua
--Assuming the Proxify module is in ReplicatedStorage
local Proxify = require(game:GetService('ReplicatedStorage').Proxify)

local Table = Proxify({
    Name = 'John',
    Age = 25
})

Table.__newindex:Connect(function(Table, Key, Value)
    print(`Key "{Key}" set to {Value}`)
end)

Table.Age = 26 -- Prints 'Key "Age" set to 26'
```

The Proxify module returns a Constructor function (technically a table with __call set up but ignore that, its like that to maintain type annotations) 
Proxify creates a shallow copy of the input table, and empties the input table for use as a Proxy. This prevents any referencing issues, as any previous references to the table will rightfully be towards the Proxy.

Additionally, as the Proxy is emptied, it allows for the __index and __newindex metamethods to fire, and thus allows a Signal to fire for any index or change to a table.

### Features
Every Proxy utilizes a wrapper object known as an `IndexEvent`, which takes any Signal object that conforms to the RBXScriptSignal class type.
Additionally, the code by default uses my own custom Signal Implementation `NEvent`, but has room to take different signals.
If you dig into the modules, specifically at the top of the IndexEvent module: `local Event = require(script.NEvent)`
You can utilize any Signal of your choice by pluging in the constructor function right in there.

Additionally, every Proxy holds two `IndexEvent`s by default, which offers the following functionality:
- `:Connect(Callback:(T,K,V)->nil, NewThread:boolean) : RBXScriptConnection` -- NewThread is true by default, This ensures yield-safe connections, however, allows flexibility in the case a callback needs to be ran in-thread ASAP instead of deferred.
- `:IndexOf(Index:Any) -> Signal` -- Generates a signal that fires only for specific indices.
- `:Wait()` -- Does what it says it does.

### Important Notes
- As every Proxy's Signals are located in the __index and __newindex indices, do not use a Proxy as a Metatable. I cannot guarantee your code will function properly.
- As Proxy tables are effectively empty, the `#` operator will always return 0. (I would've implement __len if roblox let me, I promise).
- Proxy tables are implemented with __iter to support iteration, though this is done with the generic next() functionality.
- As mentioned before, Proxify works entirely in-place with the original table, for example, the following code would work:
  ```lua
  --Assuming the Proxify module is in ReplicatedStorage
  local Proxify = require(game:GetService('ReplicatedStorage').Proxify)
  local Table = {Hi=1}
  local Table2 = Table
  Proxify(Table)

  Table2.__newindex:Connect(print)
  Table.Hi = 5 -- Would trigger print
  ```
  Since the original table is replaced in-place, Table2's reference was functionally switched from the original data to the Proxy seamlessly.
