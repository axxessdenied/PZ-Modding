# PZ-Modding
Some stuff about PZ-Modding as a reference.

# Basic Setup

### Development Environment
---
#### Project Zomboid

First, we'll need to set up a decent development environment to facilitate creating mods easier without interfering with our actual regular games.
Luckily, Steam makes this extremely easy.

We're going to use Steam's `-cachedir` parameter.

To utilize this all we need to do is add a new instance of Project Zomboid as a Non-Steam game and point to the actual executable.
With our new profile set up. Right click on it and go to Properties... from there under Shortcut we an set the launch options with the following:

```-cachedir=<path to development directory> -debug```

Another thing that I like to do is also create hardlinks from the development directory into the user folder where the Zomboid directory is that holds the mods.
That way I don't have to copy them over when I update them. Use git branches to create separate version for development / regular gameplay in-case you might break something during developement.

[Here](https://schinagl.priv.at/nt/hardlinkshellext/linkshellextension.html) is a useful utility to add the option to create links in Windows from the right-click context menu.

---
#### VSCode

Also, VSCode has a useful LUA Add-on called Umbrella.
Install LUA in VSCode and then press `CTRL+SHIFT+P` and type in `LUA: Open Addon Manager` and search for Umbrella to add to a project folder.

---
# Resources

Useful links

- [Zomboid Modding Guide](https://github.com/FWolfe/Zomboid-Modding-Guide)
- [Blender to Zomboid](https://theindiestone.com/forums/index.php?/topic/37647-the-one-stop-shop-for-3d-modeling-from-blender-to-zomboid/)
- [Sandbox Options](https://github.com/demiurgeQuantified/PZModdingGuides/blob/main/guides/SandboxOptions.md)

# Examples

Timer implementation
---
```lua
local next_timestamp = 0
local timer_delta = 5000 --time in milliseconds
local prevMultiplier = 0

local function example()
  local multiplier = getGameTime():getTrueMultiplier()
  local timestamp = getTimestampMs()

  if timestamp < next_timestamp and multiplier == prevMultiplier
  then
    return -- not enough time passed
  end

  next_timestamp = timestamp + timer_delta / multiplier
  prevMultiplier = multiplier

  -- do stuff
end
```

OnTick example
---
```lua
-- takes into account game multiplier to run with the game speed if desired
Events.OnTick.Add(function(ticks)
  if math.fmod(ticks, math.floor( nTicks / getGameTime():getTrueMultipler() )) ~= 0 then return end

  --do stuff
end)
```

# Recipes

How to add custom event when a creating an item with a recipe
---
```lua
recipe Example Recipe {
  Ingredient,

  Result: Item,
  Time: 50.0,
  OnCreate: Module.onCreateFunc,
}

function Module.onCreateFunc(items, result, player)
  -- do stuff
end
```
# Items

### Distributions

Example Function
---
```lua
local function addDistribution(data, locations)   
    for item, chance in pairs(data)
	do
		for i, location in ipairs(locations)
		do
			if ProceduralDistributions.list[location] and ProceduralDistributions.list[location].items
			then
				table.insert(ProceduralDistributions.list[location].items, item);
				table.insert(ProceduralDistributions.list[location].items, chance);
			end
		end
	end
end

local sBVars = SandboxVars.SomeModule

local flaskFullRarity = sBVars.LootSpawnChance * 0.05
local flaskEmptyRarity = sBVars.LootSpawnChance * 0.1

--example of adding an item
if sBVars.WorldSpawnFlasks == true
then
    addDistribution(
    {
        ["TheAlcoholic.FlaskEmpty"]     =       flaskEmptyRarity,
        ["TheAlcoholic.FlaskFull"]      =       flaskFullRarity,
    },{
        "BathroomCabinet",
        "BathroomCounter",
        "BedroomDresser",
        "BedroomSideTable",
        "ClosetShelfGeneric",
        "DeskGeneric",
        "DresserGeneric",
        "FilingCabinetGeneric",
        "FridgeOffice",
        "GarageTools",
        "JunkHoard",
        "KitchenRandom",
        "KitchenBottles",
        "LibraryCounter",
        "LivingRoomShelf",
        "LivingRoomSideTable",
        "Locker",
        "LockerArmyBedroom",
        "LockerClassy",
        "MechanicShelfMisc",
        "OfficeCounter",
        "OfficeDesk",
        "OfficeDeskHome",
        "OfficeDrawers",
        "OtherGeneric",
        "PlankStashMisc",
        "PoliceDesk",
        "RandomFiller",
        "SafehouseMedical",
        "ShelfGeneric",
        "WardrobeMan",
        "WardrobeManClassy",
        "WardrobeWoman",
        "WardrobeWomanClassy",
    }
)
else
    addDistribution(
    {
        ["TheAlcoholic.FlaskEmpty"]     =       flaskEmptyRarity,
        ["TheAlcoholic.FlaskFull"]      =       flaskFullRarity,
    },
    {}
)
end
```
