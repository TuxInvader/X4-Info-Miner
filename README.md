# X4 Information Miner

This repo contains 2 scripts to extract information from X4 Foundations. The one you want to use is the savefile miner: `x4-save-miner.py`, the second script `x4-cat-miner.py` is for extracting data from the game files for use with the save miner.

The only requirement besides the standard python libraries is lxml
```
pip3 install lxml
```

If you would rather not install lxml globally, create a virtual environment in the repo and use its interpreter directly.

On Linux and macOS:
```
python3 -m venv venv
venv/bin/pip3 install -r requirements.txt
venv/bin/python3 x4-save-miner.py save.xml -o
```

On Windows:
```
py -m venv venv
venv\Scripts\pip3 install -r requirements.txt
venv\Scripts\python x4-save-miner.py save.xml -o
```

## Changes

* 2025-05-05: Ver 1.0.8
  - Add `-f` option to show print Faction statistics
* 2025-03-31: Ver 1.0.7
  - Add `-K` option to show Kha'ak stations
  - proximity: Fix N/S/E/W directions
  - proximity: Include player location in output if they are in the same sector as the object
* 2025-03-29: Ver 1.0.6
  - Exclude wrecks from ownerless ships output
  - XML dump option is now uppercase `-X`
  - Add `-x` option to show Xenon Ship locations
  - Add `-k` option to show Kha'ak Ship locations
  - Add `-r` option to include wrecks in ship output (old behaviour)
* 2025-03-20: Ver 1.0.5
  - Fix multi-language support in the x4-cat-miner.py
* 2025-03-20: Ver 1.0.4
  - Various bug fixes - merci Emerson
* 2025-03-19: Ver 1.0.3
  - Added Code Finder (-c) to print information about any 'coded' object
  - Ships output now includes the name if it has one.
* 2025-03-19: Ver 1.0.2
  - Added XML Dumper (-x) to dump XML information about any 'coded' object
  - Fix for (-w) when used without (-p)
  - Shell usage has changed, the get[Object|Ship|Station]() functions have been pluralised and now return arrays
    getShip('FOO-001') would return the first match, getShips('FOO-001') returns all matches. Singular methods have been removed.
* 2025-03-16: Ver 1.0.1 
  - Added Proximity information (-p)
  - Added Player Location (-w)
* 2025-03-15: Ver 1.0.0 - Initial release

## x4-save-miner

The `x4-save-miner.py` script is used to extract useful information from any save file. By default it runs at a low information level and only reveals the sector information, running at higher info levels reveals more "spoilers". The choice is yours.

Usage:
```
usage: x4-save-miner.py [-h] [-o] [-l] [-d] [-e] [-c CODE] [-p] [-w] [-r] [-x] [-k] [-K] [-b] [-B] [-X XML] [-q] [-i INFO] [-f] [-s] savefile

positional arguments:
  savefile              The savegame you want to analyse

options:
  -h, --help            show this help message and exit
  -o, --ownerless       Display ownerless ship locations
  -l, --lockboxes       Display lockbox locations
  -d, --datavaults      Display Data Vault locations
  -e, --erlking         Display Erlking Data Vault locations
  -c CODE, --code CODE  Display location of the items with code
  -p, --proximity       Display The proximity to the closest station
  -w, --whereswally     Display player location information
  -r, --wrecks          Include wrecks in output
  -x, --xenon           Display Xenon ship locations
  -k, --khaak           Display Khaak ship locations
  -K, --khaakstations   Display Khaak Station locations
  -b, --buccaneers      Display Duke's Buccaneers ship locations
  -B, --buccaneerstations
                        Display Duke's Buccaneers Station locations
  -X XML, --xml XML     Dump the XML for a specific resource by code
  -q, --quiet           Suppress warnings in interactive mode
  -i INFO, --info INFO  information level [1-3]. Default is 1 (sector only)
  -f, --factions        Display faction relative strengths
  -s, --shell           Starts a python shell to interract with the XML data (read-only)
```

The savefile can be compressed or uncompressed. It is the importing of the data that takes most of the time, once imported accessing the data is fast.

The flags are not mutually exclusive, you can use them all together. eg:

```
$ ./x4-save-miner.py ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz -olde -i2
```

The amount of information displayed depends on the `-i`,`--info` setting. At level `1` it will display only the sector in which the object resides, level `2` will also show the position within the sector (x,y,z in meters), and level `3` will show you the components on board. 

For example, getting the ownerless ships at level `1` (the default) returns information like:
```
$ ./x4-save-miner.py ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz -o

Ship: IKP-411, Class: ship_l, Macro: ship_par_l_destroyer_01_a_macro
  SpawnTime: 0
  Sector: Faulty Logic VII (XXL-865)
```

Running at level `2` would return:
```
$ ./x4-save-miner.py ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz -o -i2

Ship: IKP-411, Class: ship_l, Macro: ship_par_l_destroyer_01_a_macro
  SpawnTime: 0
  Sector: Faulty Logic VII (XXL-865)
  Location: {'x': -165396, 'y': -489, 'z': 145876, 'pitch': 0, 'roll': 30, 'yaw': -111}
```

And at level `3` it would return:
```
$ ./x4-save-miner.py ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz -o -i3

Ship: IKP-411, Class: ship_l, Macro: ship_par_l_destroyer_01_a_macro
  SpawnTime: 0
  Sector: Faulty Logic VII (XXL-865)
  Location: {'x': -165396, 'y': -489, 'z': 145876, 'pitch': 0, 'roll': 30, 'yaw': -111}

  Engines:
    engine_par_l_travel_01_mk1_macro
    engine_par_l_travel_01_mk1_macro
    engine_par_l_travel_01_mk1_macro
  Shields:
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_m_standard_02_mk1_macro
    shield_par_l_standard_01_mk1_macro
    shield_par_l_standard_01_mk1_macro
  Weapons:
    weapon_par_l_destroyer_01_mk1_macro
    weapon_par_l_destroyer_01_mk1_macro
  Turrets:
    turret_par_m_gatling_02_mk1_macro
    turret_par_m_gatling_02_mk1_macro
    turret_par_m_gatling_02_mk1_macro
    turret_par_m_gatling_02_mk1_macro
    turret_par_m_gatling_02_mk1_macro
    turret_par_m_laser_02_mk1_macro
    turret_par_l_laser_01_mk1_macro
    turret_par_l_laser_01_mk1_macro
    turret_par_l_laser_01_mk1_macro
  Software:
    software_dockmk2
    software_scannerobjectmk2
    software_trademk1
  Consumables:
    weapon_gen_mine_02_macro: 19
    ship_gen_xs_repairdrone_01_a_macro: 3
    ship_gen_xs_lasertower_01_a_macro: 19
    ship_gen_s_fightingdrone_01_a_macro: 1
    weapon_gen_mine_03_macro: 17
    weapon_gen_mine_01_macro: 45
    countermeasure_flares_01_macro: 10
    ship_gen_s_lasertower_01_a_macro: 17

```

You can also pass `-p` to get the proximity to the closest station in the sector. Using `-p` will (obviously?) provide information at level: 2. 

Here's an Erlking Vault example. The nearest station has code JSV-655, and the vault is 151 KM to the West (left) and 46 KM to the North (up) of that station. It's also 26 KM above the station. 

```
Vault: ZFN-855, Known2Player: True
  Sector: Windfall III The Hoard (EXA-561)
  Location: {'x': -233300, 'y': 23136, 'z': -44598, 'pitch': 0, 'roll': 0, 'yaw': 0}

            The closest station is: JSV-655, distance: 160 km
            Target is 151 km to the west (X Axis)
            Target is 46 km to the north (Z Axis)
            Target is 26 km above (Y Axis)

```
Note: The proximity is show in KM, the location (x,y,z) on the line above is in meters.

An example of Erlking Data Vaults:

```
$ ./x4-save-miner.py -e ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz -i3

Erlking Vaults
===============
Vault: ZZL-662, Known2Player: True
  Sector: Avarice I (GIX-981)
  Location: {'x': -22100, 'y': -5674, 'z': 131278, 'pitch': 0, 'roll': 0, 'yaw': -113}
  Wares:

Vault: CTY-692, Known2Player: True
  Sector: Avarice I (GIX-981)
  Location: {'x': -180027, 'y': -36605, 'z': -294587, 'pitch': 0, 'roll': 0, 'yaw': 0}
  Wares:

Vault: ZFN-855, Known2Player: True
  Sector: Windfall III The Hoard (EXA-561)
  Location: {'x': -233300, 'y': 23136, 'z': -44598, 'pitch': 0, 'roll': 0, 'yaw': 0}
  Wares:
    inv_decryptionmodule: 4
    inv_seminar_piloting_0: 2
    Blueprint: weapon_pir_xl_battleship_01_mk1
    Credits: 15766600

Vault: PQD-875, Known2Player: True
  Sector: Windfall IV Aurora's Dream (AWK-124)
  Location: {'x': -229999, 'y': 4159, 'z': -55641, 'pitch': 0, 'roll': 0, 'yaw': 55}
  Wares:

Vault: FXI-254, Known2Player: True
  Sector: Windfall IV Aurora's Dream (AWK-124)
  Location: {'x': -73950, 'y': -5823, 'z': 276225, 'pitch': 0, 'roll': 0, 'yaw': 0}
  Wares:
    inv_hallucinogenics: 1
    inv_decryptionmodule: 2
    inv_advancedtargetingmodule: 3
    modpart_shieldgeneratorcoil_t1: 1
    Blueprint: shield_pir_xl_battleship_01_standard_01_mk1
    Credits: 12328300

```
As you can see, I have opened three vaults and the remaining two still have wares and blueprints inside.

### Interactive Mode
You can also poke around inside your save file by starting the script in interactive mode. In this mode, python will parse the save file into an `lxml.etree` structure and then drop you into an interactive interpreter.

```
$ ./x4-save-miner.py -s ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz

Python Shell starting...

Available Functions: 

  getShips('code')          # Fetch information about a specific ship code (returns an array of ships)
  getStations('code')       # Fetch information about a specific station (returns an array of stations)
  getObjects('code')        # Fetch information about any object with a code (returns an array of objets)
  getSectors('code')        # Fetch information about a specific sector (returns an array of sectors)
  getSectorObjects('code')  # Fetch stations and ships currently inside the given sector
  printXML('code')          # Print the XML for a resource and its children
  getDupes('code')          # Fetch all duplicates or those with provided code
  dumpDupes('code')         # Print all duplicates or those with provided code

Fetch interesting (special) resources

  setLevel(int)                                          # Set information level for print functions
  update[Ownerless|LockBoxes|DataVaults|ErlkingVaults]() # Update locations for these objects
  print[Ownerless|LockBoxes|DataVaults|ErlkingVaults]()  # Print these objects information

  Eg. Display the ownerless ship locations:

  >>> updateOwnerless()
  >>> printOwnerless()

Or you know, just use python. The root of the xml tree is in var `root`. Other vars include:
lists:      sectors duplicates warnings allComponents allStations allShips freeShips
            xenonShips khaakShips khaakStations bucShips bucStations dataVaults
            erlkingVaults lockboxes flotsam other
dicts:      sectorNames sectorCodes shipCodes stationCodes vaultCodes lockboxCodes allCodes
            ignoredConnections sector_zone_offsets sector_macros

Examples

  >>> print(json.dumps(dict(phq.attrib), indent=6)) 
  >>> printShip(getShips('ULC-584')[0],3)

Python 3.12.3 (main, Feb  4 2025, 14:48:35) [GCC 13.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> 
```

### Duplicate codes

You probably thought that those station and ship codes were unique. Well that's not necessarily the case, I have many duplicates in some of my save games and you may have too. This wont cause an issue when printing data using one of the flags [`-o`,`-l`,`-d`,`-e`], but it is something to bear in mind if you go poking around in interactive mode. The functions should warn you if you request data which has a duplicate though. 

From version 1.0.3, the getShip() and similar functions have been replaced with getShips() and similar which return an array of matching items.

```
$ ./x4-save-miner.py -s ~/.config/EgoSoft/X4/11524914/save/quicksave.xml.gz 

Python Shell starting...

WARNING: Duplicate code found for: NMQ-431
WARNING: Duplicate code found for: XYT-702
WARNING: WARNING: Duplicate is another SHIP. Two or more ships have the same code: XYT-702
WARNING: Duplicate code found for: KSR-468
WARNING: Duplicate code found for: YRS-090
... SNIP ...
```

These dupes are not a problem for the game, because each object also as an `id` which is unique and I'm sure that's how the resources get managed by the game engine.

## x4-cat-miner

The `x4-cat-miner.py` script is used to extract offset information from zones/regions from the CAT/DAT files shipped with game. This information is necessary in order to be able to locate objects in space using co-ordinates from the centre of any sector. It also parses the language files to map the identification codes to their languages.

You do not need to run this unless Egosoft releases a new DLC or you want to change the language in the other scripts output (the files in this repo are configured for English).

Usage:
```
usage: x4-cat-miner.py [-h] [-l LANGID] x4folder

positional arguments:
  x4folder              The location of your X4 installation

options:
  -h, --help            show this help message and exit
  -l LANGID, --langid LANGID
                        The language ID for names (default == 44 (English))
```

The langid is the language id for the name mappings. The default is `44` which I think is UK English.

It will generate two json files `x4-names.json` which maps the cluster/sector/zone macros to their system names, and `x4-offsets.json` which maps the macros to their three dimensional offsets. Both of these files will then be used by the `x4-save-miner` script to provide useful information from your save files.



