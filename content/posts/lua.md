+++
title = "lua"
date = "2023-09-01T21:04:56+02:00"
author = "Greg Loyse"
authorTwitter = "" #do not include @
cover = ""
tags = ["lua", "Programming Languages", "Comparison", ]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# intro

# reference

## operators


```lua
+ - * / %                     -- arithmetic
== != < > <= =>               -- relational
and or not                    -- logical
& | ^ ~ << >>                 -- bitwise

= += *= /= %=                 -- assignment
<<= >>= &= ^= |=              -- assignment bitwise

-> . * &                      -- pointer & member access

.. #                          -- str concat, str len

```

no conditional ternary operator


## control flow

### conditionals

```lua
if condition then
    -- code to be executed if condition is true
end

if condition then
    -- code to be executed if condition is true
else
    -- code to be executed if condition is false
end


if condition1 then
    -- code to be executed if condition1 is true
elseif condition2 then
    -- code to be executed if condition1 is false and condition2 is true
else
    -- code to be executed if both condition1 and condition2 are false
end

```

### loops

```lua
for key, value in pairs(table) do
    -- code to be executed
end

for i = start, stop[, step] do
    -- code to be executed
end


while condition do
    if another_condition then
        break
    end
    -- code to be executed while condition is true
end

```


## collections

single data structure `table`. 
use this in different ways to achieve behaviour of other collections.

Note `table` is 1-indexed.

```lua
-- array
local myArray = {10, 20, 30, 40, 50}
myArray[2]

-- dictionary / associative array / struct
local myDict = {name="John", age=30, occupation="engineer"}
myDict["name"]
myDict.age)

-- set
local mySet = {apple=true, banana=true, cherry=true}
if mySet["apple"] then
    print("Apple exists in the set.")  -- Output: Apple exists in the set.
end

-- array multi-dimensional
local my2DArray = { {1, 2}, {3, 4}, {5, 6} }

```

## functions

no support for docs or default parameters. use idioms instead.

```lua
--- Adds two numbers together.
-- @param a: (number) The first number to add. Defaults to 0.
-- @param b: (number) The second number to add. Defaults to 0.
-- @return: (number) The sum of a and b.
function add(a, b)
    -- Type checking for a
    if a and type(a) ~= "number" then
        error("Parameter 'a' must be a number.")
    end

    -- Type checking for b
    if b and type(b) ~= "number" then
        error("Parameter 'b' must be a number.")
    end

    -- Default values for a and b
    a = a or 0
    b = b or 0

    return a + b
end

-- Test the function
add(3, 4)  -- Output should be 7
add(3)     -- Output should be 3 (second parameter defaults to 0)
add()      -- Output should be 0 (both parameters default to 0)
```

## object oriented programming

No native support though `table` allows object-oriented programming styles.

### classes and objects

```lua
Person = {}
Person.__index = Person

-- Class constructor
function Person.new(name, age)
    local self = setmetatable({}, Person)
    self.name = name
    self.age = age
    return self
end

-- Class method
function Person:describe()
    return self.name .. " is " .. self.age .. " years old."
end

-- Create an object of 'Person'
john = Person.new("John", 30)

-- Call a method on the object
print(john:describe())  -- Output: John is 30 years old.

```

### inheritance

Lua's metatables can be used to simulate inheritance.
Prototypal inheritance.

```lua
-- Base class 'Animal'
Animal = {}
Animal.__index = Animal

function Animal.new(name)
    local self = setmetatable({}, Animal)
    self.name = name
    return self
end

function Animal:speak()
    return self.name .. " makes a noise."
end

-- Derived class 'Dog'
Dog = setmetatable({}, Animal)
Dog.__index = Dog

function Dog.new(name)
    local self = setmetatable(Animal.new(name), Dog)
    return self
end

function Dog:speak()
    return self.name .. " barks."
end

-- Create objects
myAnimal = Animal.new("Generic animal")
myDog = Dog.new("Fido")

-- Call methods
print(myAnimal:speak())  -- Output: Generic animal makes a noise.
print(myDog:speak())     -- Output: Fido barks.


```

### encapsulation

```lua

-- Encapsulation using closures
function makePerson(name, age)
    local self = {}
    local secret = "This is a secret!"

    function self.getName()
        return name
    end

    function self.getSecret()
        return secret
    end

    return self
end

local john = makePerson("John", 30)
print(john.getName())  -- Output: John
-- print(john.secret)  -- This would be nil, secret is not directly accessible
print(john.getSecret())  -- Accessible through a method

```

### polymorphism

```lua

function describeAnimal(animal)
    print("The animal speaks: " .. animal:speak())
end

-- Polymorphism in action
describeAnimal(myAnimal)  -- Output: The animal speaks: Generic animal makes a noise.
describeAnimal(myDog)     -- Output: The animal speaks: Fido barks.

```

## metatable

extend semantics of `table`. eg. sets, matrices, user-defined types.


```lua

-- Define a metatable with an __add meta-method
local mt = {
  __add = function(a, b)
    local result = {}
    for i = 1, #a do
      result[i] = a[i] + b[i]
    end
    return result
  end
}

-- Create two tables
local t1 = {1, 2, 3}
local t2 = {4, 5, 6}

-- Set the metatables for these tables
setmetatable(t1, mt)
setmetatable(t2, mt)

-- Add the tables
local t3 = t1 + t2  -- Calls the __add meta-method defined in mt

-- Print the result
for i, v in ipairs(t3) do
  print(v)  -- Output will be 5, 7, 9 (element-wise addition of t1 and t2)
end
```

## concurrency

co-routine based.
No native support for threads and multi-processing.

Each co-routine must yield to controlling routine to allow another coroutine to run.

This isn't parallel execution but can be useful for tasks like iterators, background tasks, managing multiple IO-bound tasks.

```lua
-- Create two coroutines
co1 = coroutine.create(function()
  for i = 1, 5 do
    print("co1", i)
    coroutine.yield()
  end
end)

co2 = coroutine.create(function()
  for i = 1, 5 do
    print("co2", i)
    coroutine.yield()
  end
end)

-- Interleave the execution of co1 and co2
for i = 1, 5 do
  coroutine.resume(co1)
  coroutine.resume(co2)
end

```

useful for event driven programming (LOVE game development framework, Nginx web server...).


## `C` interop


```c
#include <lua.h>
#include <lauxlib.h>

// C function to be called from Lua
static int l_multiply_by_2(lua_State *L) {
    int n = luaL_checknumber(L, 1);  // Get the first argument from the stack
    lua_pushnumber(L, n * 2);  // Push the result onto the stack
    return 1;  // Number of return values
}

// Register the C function in Lua
int luaopen_mylib(lua_State *L) {
    luaL_Reg funcs[] = {
        {"multiply_by_2", l_multiply_by_2},
        {NULL, NULL}
    };
    
    luaL_newlib(L, funcs);
    return 1;
}

```

```bash
gcc -shared -o mylib.so -fPIC -I/usr/include/lua5.1 my_code.c -llua5.1
```

```lua
local mylib = require "mylib"
local result = mylib.multiply_by_2(21) -- should be 42

```
