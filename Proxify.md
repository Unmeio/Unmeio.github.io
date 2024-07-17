---
layout: page
title: "Proxify"
permalink: /Proxify
remote_theme: pages-themes/midnight@v0.2.0
---
# Proxify

Proxify is module that allows event-based callback functionality for tables when indexed or manipulated, through the implementation of a 'Proxy' table to take advantage of the __index and __newindex metamethods.
This allows users to use Signals (either the built-in or your own provided Signal Class) with general or index-specific accesses/changes in a table.

Utilizing Proxify is as simple as:
```lua
--Assuming the Proxify module is in ReplicatedStorage
local Proxify = require(game:GetService('ReplicatedStorage').Proxify)

local Table = Proxify({
    Name = 'John',
    Age = 25
})

Table.__newindex:Connect(function(Table, Key, Value)
    print('Key '..Key..' set to '..Value)
end)

Table.Age = 26 -- Prints 'Key "Age" set to 26'
```

The Proxify module returns a Constructor function to translate an input table into a 'Proxy' table with Signals under the '__index' and '__newindex' indices.
Proxify creates a shallow copy of the input table, and empties the input table for use as a Proxy. This prevents any referencing issues, as any references to the original table will rightfully work through the Proxy.

Additionally, as the Proxy is emptied, it allows for the __index and __newindex metamethods to fire, and thus allows a Signal to fire for any index or assignment within the table.
Every Proxy utilizes a wrapper object known as an `IndexEvent`, which takes any Signal class that utilizes :Connect and :Fire methods. IndexEvents additionally offer a method to generate Signals for individual indices with the `:IndexOf(index)` method. 

The code by default uses my custom Signal implementation `NEvent`, but has the capability to take different Signal classes. You can utilize any Signal of your choice by plugging its constructor function into the Event variable at the top of the IndexEvent module:

### Important Notes
- As every Proxy's Signals are located in the __index and __newindex indices, do not use a Proxy as a Metatable. Your code will likely not function properly.
- Proxy tables are implemented with __iter allowing users to iterate over a Proxy's actual table with no problems.
- As mentioned before, Proxify does its work entirely in-place with the original table.
  For example, the following code would work:
  ```lua
  --Assuming the Proxify module is in ReplicatedStorage
  local Proxify = require(game:GetService('ReplicatedStorage').Proxify)
  local Table = {Hi=1}
  local Table2 = Table
  Proxify(Table)

  Table2.__newindex:Connect(print)
  Table.Hi = 5 -- Would trigger print
  ```
  Since the the original table is directly replaced with a Proxy, the lingering reference of Table2 will point at the Proxy.
## Installation
Proxify is available as a [Model by Simply_Nobody](https://create.roblox.com/store/asset/17414468091/Proxify)
