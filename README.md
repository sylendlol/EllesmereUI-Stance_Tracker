# EllesmereUI-Stance_Tracker

A stance / form / aura / aspect tracker, built as a module for the **Dworak** section of [EllesmereUI](https://www.curseforge.com/wow/addons/ellesmereui).
It shows one icon for what **you** are running right now: your Warrior stance, Druid form, Paladin aura or Hunter aspect.

Made for **WoW Forever** (Interface 16001) with **EllesmereUI 9.5.4**. The `.toc` also lists the 12.0.x interface numbers.

```
   [icon]              ← the spell's own icon
Devotion Aura          ← optional name (below, above, left or right, or off)
```

## Features

### What it tracks

| Class | Tracks | Spells |
| --- | --- | --- |
| Warrior | Stance | Battle, Defensive, Berserker |
| Druid | Form | Bear / Dire Bear, Cat, Travel, Aquatic, Moonkin, Tree of Life, Flight |
| Paladin | Aura | Devotion, Retribution, Concentration, Sanctity, Shadow / Frost / Fire Resistance, Crusader |
| Hunter | Aspect | Hawk, Monkey, Cheetah, Pack, Beast, Wild, Viper, Dragonhawk |

Spells your client doesn't have are left out of the settings page automatically.

### Only your own
- **Other players' auras never show.** In a group you often get another paladin's aura or another hunter's Aspect of the Pack. The tracker ignores them and shows only what you cast yourself.
- **Same-name auras:** identical auras don't stack, so another paladin's Devotion Aura can push yours off. When that happens, your own aura still shows.
- **Rank-proof:** every rank of a spell counts. Spells are matched by spell ID and by their localized name.

### Per-class settings
- **Track &lt;Class&gt;:** turns the whole class on or off.
- **One toggle per stance, form, aura and aspect**, each with its spell icon on the settings page. Turn one off and nothing shows while it is active. For example, show only Aspect of the Cheetah and the Pack as a reminder to switch back.
- **When None Active:** with nothing up, show nothing, or show a dimmed icon labeled *No Aura*, *No Aspect*, *No Stance* or *Caster Form* in the Warning Color. On by default for Paladin and Hunter, off for Warrior and Druid.
- Your own class is listed first on the page.

### Look
- Always the **spell's own icon**, never the stance bar's generic "active" texture.
- Icon size and an adjustable border: 0–6 px (0 removes it), in the UI Accent Color, your class color or a custom color.
- **Show Name** on or off. When it's on, you can set the name's position (below, above, left or right), font, text size and color.
- **Visibility:** Only In Combat, Hide When Mounted, Hide When Dead, and a separate opacity for out of combat.
- Movable in EllesmereUI **Unlock Mode** (group *Dworak*). Click-through, so it never blocks your mouse.
- The UI Accent Color from Global Settings applies live.
- The settings page has a live preview that cycles through your class's selected entries.

## Requirements

- [EllesmereUI](https://www.curseforge.com/wow/addons/ellesmereui)
- **EllesmereUIDworak**, the host addon that owns the *Dworak* section of the EllesmereUI panel

## Installation

1. Copy the `EllesmereUIStance` folder into `World of Warcraft\_classic_beta_\Interface\AddOns\`. Use your client's own folder if it's different.
2. Make sure `EllesmereUI` and `EllesmereUIDworak` are installed and enabled.
3. Log in or `/reload`. The module appears under **EllesmereUI → Dworak → Stance Tracker**.

## Commands

| Command | What it does |
| --- | --- |
| `/stance` or `/euistance` | Opens the settings page |
| `/stance debug` | Prints your stance bar, your own and other players' aura state, and what was detected |

## Settings

| Section | Options |
| --- | --- |
| Stance Tracker | On/off, Only In Combat, Hide When Mounted, Hide When Dead, Out of Combat Opacity, Warning Color |
| Display | Icon Size, Icon Border, Border Color, Show Name, Name Position, Text Size, Font, Text Color |
| Warrior Stances / Paladin Auras / Hunter Aspects / Druid Forms | Track &lt;Class&gt;, When None Active, one toggle per spell |

Settings are saved per EllesmereUI profile in `EllesmereUIStanceDB`, so one profile can hold settings for all four classes.

## How it works

- **Stances and forms** come from the stance bar (`GetShapeshiftForm` / `GetShapeshiftFormInfo`). On WoW Forever, Paladin auras live on the stance bar too, so they are read the same way.
- **Buff-based auras and aspects** are read with `C_UnitAuras.GetAuraDataBySpellName(..., "HELPFUL|PLAYER")`. The `PLAYER` filter returns only auras you cast. If that API is missing, it falls back to `AuraUtil.FindAuraByName` or `UnitBuff`.
- **Icons:** the stance bar returns a generic "active" texture for the active form, so the icon always comes from the spell itself (`C_Spell.GetSpellTexture`).
- **Secret values:** all checks only test whether an aura is there (wrapped in `pcall`, every value checked with `issecretvalue`). In combat and in restricted content the client withholds aura data, so the lookup can come back empty even with the aura up. There, only "found" is trusted. Otherwise the tracker keeps your last known aura or aspect, updated by your own casts (`UNIT_SPELLCAST_SUCCEEDED`), and checks again when combat ends.
- Updates are event-driven (stance bar changes, `UNIT_AURA` for you, combat, mount and death) and grouped into one refresh. Nothing runs on a timer.
- No EllesmereUI file is modified. The module registers through `EllesmereUIDworak.AddModule`.

## Known limitations

- **Early version.** Tested in game with Paladin and Hunter. Warrior and Druid are not tested yet. Please report anything odd together with the `/stance debug` output.
- Only these four classes are supported. Other stance bar abilities, such as Rogue Stealth or Shaman Ghost Wolf, are not tracked.
- It shows a single icon, the one active stance, form, aura or aspect, not a full bar.

## Author

Dworak
Dworak
