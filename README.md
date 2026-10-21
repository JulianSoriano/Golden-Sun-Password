# Golden Sun Password Generator

A web app for building a *Golden Sun* (GBA) party and generating the **Gold**, **Silver** and **Bronze** passwords that carry it over into *Golden Sun: The Lost Age*.

The whole app is one file, `index.html`. Open it in a browser, or turn on GitHub Pages for this repo to host it.

## Features

- **Party:** levels and HP / PP / ATK / DEF / AGI / LCK for Isaac, Garet, Ivan and Mia. You can enter hidden base stats directly, or the in-game stats (no equipment, all Djinn on Standby), which are converted using each character's default class multipliers.
- **Djinn:** pick which of the 28 Venus, Mercury, Mars and Jupiter Djinn you own.
- **Inventories:** 15 item slots per character. Type an item by name or ID. Consumables such as Herbs, Nuts and Game Tickets can be stacked up to 30.
- **Story events:** Hammet, Colosso, Hsu, Deadbeard, Vale and the Vault.
- **Coins:** 0 to 999,999.
- **All three tiers:** passwords are shown in rows of 10, the same way you enter them in-game, with copy buttons.
- **Validation:** out-of-range values are flagged before you copy a password that won't work.
- **Saved progress:** your build is saved in your own browser.

| Tier   | Length     | What it carries |
|--------|------------|-----------------|
| Gold   | 260 symbols | Levels, stats, Djinn, events, all items and quantities, coins |
| Silver | 61 symbols  | Levels, stats, Djinn, events, Psynergy items |
| Bronze | 16 symbols  | Levels, Djinn, events, Psynergy items |

## Credits

The password research and algorithm are the work of **[Dyrati](https://github.com/Dyrati)**. This app is a JavaScript port of Dyrati's *Golden Sun Password Generator* spreadsheet (© 2023 Dyrati, released as "No Rights Reserved"). That covers the bit layout, the checksum key, the encoding steps, and the item, Djinn and character tables.

- Dyrati's original Lua implementation: [Dyrati/Golden-Sun-Password-Transfer](https://github.com/Dyrati/Golden-Sun-Password-Transfer) ([password.lua](https://github.com/Dyrati/Golden-Sun-Password-Transfer/blob/main/password.lua))

Generated passwords were checked against the spreadsheet's own output for the same party, and they match symbol for symbol on all three tiers.

*Golden Sun* is a trademark of Nintendo and Camelot Software Planning. This is an unofficial fan project and is not affiliated with or endorsed by them.

## How the password is built

1. Everything is packed into bits: 7 per level, 1 per Djinn, 1 per story event and Psynergy item, 60 per character for stats, 9 per item ID, 5 per consumable stack, and 24 for coins.
2. A 16-bit key runs over every byte. It starts at `0xFFFF`, shifts left, and adds `0xEFDF` on carry. The final key is inverted.
3. Each byte is XORed with the key's low byte, and both key bytes are appended.
4. The bits are split into 6-bit groups. After every 9 groups, a check group is added (the sum of those 9, mod 64).
5. Each group gets its position added (mod 64) and is mapped to one of 64 password symbols. The symbol set leaves out `0`, `1`, `I`, `O`, `l` and `o`.
