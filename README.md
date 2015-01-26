# tick
A small Lua module that simplifies the calling of functions at a set interval
or after a delay.

## Usage
The [tick.lua](tick.lua?raw=1) file should be dropped into an existing project
and required by it.
```lua
tick = require "tick"
```
At the start of each frame `tick.update()` should be called and given the delta
time since the last call as its argument.
```lua
tick.update(dt)
```

#### tick.delay(fn, delay)
Calls the function `fn` after the given `delay` time has passed. Returns the
associated event.
```lua
-- Prints "Hello world!" after 2 seconds
tick.delay(function() print("Hello world!") end, 2)
```

#### tick.recur(fn, delay)
Calls the function `fn` at an interval of `delay`. Returns the associated
event.
```lua
-- Prints "tick!" every half-second
tick.recur(function() print("tick!") end, .5)
```

### Chaining events
To avoid having to deeply nest several functions when creating chains of
events, the `:after()` method can be called on an event returned by
`tick.delay()`. You can keep using the `:after()` method to create complex
timed sequences.
```lua
-- Prints "cat", "dog", then "owl", waiting 1 second between each print
tick.delay(function() print("cat") end, 1)
  :after(function() print("dog") end, 1)
  :after(function() print("owl") end, 1)
```

### Stopping events
An event can be stopped and removed at any point by calling its `:stop()`
method. To do this the event must be assigned to a variable when it is created.
```lua
-- Create a new event
local t = tick.delay(function() print("tick!") end, 10)
-- Remove the event before it has a chance to run
t:stop()
```

### Groups
Tick provides the ability to create event groups; these are objects which can
have events added to them, and which are in charge of updating and handling
their contained events. A group is created by calling the `tick.group()`
function.
```lua
local group = tick.group()
```

Once a group is created it acts independently of the `tick` object, and must
be updated each frame using its own update method.
```lua
group:update(dt)
```

To add a events to a group, the group's `:delay()` or `:recur()` methods should
be used.
```lua
group:delay(function() print("hello world") end, 4)
```

A good example of where groups are useful is for games where you may have a set
of events which effect objects in the game world and which you want to pause
when the game is paused.  A group's events can be paused by simply neglecting
to call its `update()` method; when a group is destroyed its events are also
destroyed.


## License
This library is free software; you can redistribute it and/or modify it under
the terms of the MIT license. See [LICENSE](LICENSE) for details.

