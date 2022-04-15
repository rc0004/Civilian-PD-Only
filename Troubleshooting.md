# This is a breakdown of error messages when trying to achieve a YAML patch

**The desired functionality is to loop through all civilian ships roles, and specifically change the Maximum Component Size to 19 when the ComponentBayId = 0, hence restricting the weapon size on civilian ships**


## Error Message
HR: 0x80131500, Message:(Line: 28, Col: 192, Idx: 848) - (Line: 28, Col: 192, Idx: 848): While scanning a plain scalar value, found invalid mapping.

## The YAML code producing that error

``` YAML

ShipHull:
  - update-all:
    $where: txt(def.Role) is "FreighterSmall" or txt(def.Role) is "FreighterMedium" or txt(def.Role) is "FreighterLarge" or txt(def.Role) is "ExplorationShip" or txt(def.Role) is "MiningShip":
  - (item.ComponentBayId is 0):
     MaximumComponentSize: 19
```

Line 28
```YAML
    $where: txt(def.Role) is "FreighterSmall" or txt(def.Role) is "FreighterMedium" or txt(def.Role) is "FreighterLarge" or txt(def.Role) is "ExplorationShip" or txt(def.Role) is "MiningShip":
```

## Simplified the original code to just target a single role at a time --
```YAML
ShipHull:
  - update-all:
      $where: txt(def.Role) is "FreighterSmall" 
  - (item.ComponentBayId is 0):
     MaximumComponentSize: 19
```
## Error
Can't parse update-all where clause @ Line: 28, Col: 15, Idx: 818

In the data file there was a white space after the text, so I removed that.

Still produces same error: Can't parse update-all where clause @ Line: 28, Col: 15, Idx: 818

**Then added this to line 29**

``ComponentBays:``


## New Error

Col: 6, Idx: 913) - (Line: 31, Col: 6, Idx: 913): While parsing a block mapping, did not find expected key.
Line 31: MaximumComponentSize: 19

**Source Code**
```YAML
ShipHull:
  - update-all:
      $where: txt(def.Role) is "FreighterSmall"
    ComponentBays:
    - (item.ComponentBayId is 0):
     MaximumComponentSize: 19
```

**CHANGED TO**
```YAML
ShipHull:
  - update-all:
      $where: txt(def.Role) is "FreighterSmall"
    ComponentBays:
      ComponentBay:
        - (item.ComponentBayId is 0):
          MaximumComponentSize: 19
```

Produces new error in debug.log
```
Parsing D:\Program Files (x86)\SteamLibrary\steamapps\common\Distant Worlds 2\mods\Civilian-PD-Only\data\pd_only_civ_ships.YML for static definitions
Can't parse instruction @ Line: 27, Col: 5, Idx: 791
```
**Where line 27 is - update-all:**