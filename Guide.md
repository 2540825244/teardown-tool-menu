# Foreword
This text only describe the process to edit the tool menu's script to fix errors related to invalid order function (unable to open menu) and favorite not saving across games for the Tool menu mod and its variants of the game Teardown.

This guide will be in the simplest language possible by me, so many technical details and alternate steps will be excluded.

The original tool menu mod:

https://steamcommunity.com/sharedfiles/filedetails/?id=2418422455

My version of the original that already has the problems fixed:

https://steamcommunity.com/sharedfiles/filedetails/?id=2847011618

## First steps

 1. Subscribe the tool menu mod in workshop if you haven't
 2. Open the game, go to Mod manager and find the mod in the middle row and click on its name
 3. Click "Make local copy" button
 4. On the right you should see a new mod named after the original mode + " Copy"
 5. Go to your documents folder (by default it is C:/Users/[Your Username]/Documents/)
 6. Open the folder named "Teardown"
 7. Open the folder named "mods"
 8. Open the folder named after the mod you copied + " Copy"
 9. The main.lua file (or simply shown as "main") is the file we will be looking into, open it with the text editor of your choice

## Invalid Order Function

 1. Ctrl-F to open up find dialogue
 2. Search for "-- sort by index"
 3. You should also see "table.sort(all_tools, by_index)" a few lines beneath
 4. Delete all lines from "-- sort by index" to "table.sort(all_tools, by_index)" (including these 2 lines)
 5. Paste the following codes into the place where the "-- sort by index" was
```lua
    -- sort by id
    function by_id(a, b)
        if a.id == nil then
            return true
        end
        if b.id == nil then
            return false
        end
        return a.id < b.id
    end
    table.sort(all_tools, by_id)
```
 6. Ctrl-S to save file

## Favorites not saving across sessions

 1. Ctrl-F to open up find dialogue
 2. Search for "function  load_favorite_tools()"
 3. You should see 3 "end"s one after another in separate lines a few lines down, and moving from right to left each line
 4. Delete all lines from "function  load_favorite_tools()" to the last end in the 3 ends (including these 2 lines)
 5. Paste the following codes into the place where the "function  load_favorite_tools()" was
```lua
function load_favorite_tools()

    -- load from perst.
    local fav_string = GetString(favorite_tools_reg)
    local tool_ids = split(fav_string, ',')

    -- update the favorite status of the tools
    for i, tool in ipairs(all_tools) do
        if table_contains(tool_ids, tool.id) then
			all_tools[i].favorite = true
		end
    end
end
```
 6. Ctrl-S to save file

## After editing the Lua script

 1. Make sure you saved
 2. Go back to game and disable the mod from the workshop in the middle column if you have it enabled
 3. Enable the mod you edited on the right name after the workshop mod name + " Copy"
 4. Everything should be done, load up a map to test it out

# Quick and Shorter Guide but may not be suitable for people unfamiliar with lua scripting

Edit the main.lua lua script for the mod

Change the
```lua
function generate_all_tools()
    all_tools = {}

    -- get all tools
    tool_ids = ListKeys('game.tool')
    for i, tool_id in ipairs(tool_ids) do
        tool_data = get_tool_data(tool_id)
        table.insert(all_tools, tool_data)
    end

    -- sort by index
    function by_index(a, b)
        if a.index == nil then
            return true
        end
        if b.index == nil then
            return false
        end
        return a.index < b.index
    end
    table.sort(all_tools, by_index)
end
```

to

```lua
function generate_all_tools()
    all_tools = {}

    -- get all tools
    tool_ids = ListKeys('game.tool')
    for i, tool_id in ipairs(tool_ids) do
        tool_data = get_tool_data(tool_id)
        table.insert(all_tools, tool_data)
    end

    -- sort by id
    function by_id(a, b)
        if a.id == nil then
            return true
        end
        if b.id == nil then
            return false
        end
        return a.id < b.id
    end
    table.sort(all_tools, by_id)
end
```

or better just remove the by_order function and replace it with by_id then change the table.sort to use by_id since in the variants the modder may choose to add other functionality in the function

then for the favorite

change the

```lua
function load_favorite_tools()

    -- load from perst.
    local fav_string = GetString(favorite_tools_reg)
    local tool_ids = split(fav_string, ',')

    -- update the favorite status of the tools
    for i, tool_id in ipairs(tool_ids) do
        local index = tonumber(get_tool_key(tool_id, 'index'))
        if tonumber(index) ~= nil then
            all_tools[index].favorite = true
        end
    end
end
```

to

```lua
function load_favorite_tools()

    -- load from perst.
    local fav_string = GetString(favorite_tools_reg)
    local tool_ids = split(fav_string, ',')

    -- update the favorite status of the tools
    for i, tool in ipairs(all_tools) do
        if table_contains(tool_ids, tool.id) then
			all_tools[i].favorite = true
		end
    end
end
```
