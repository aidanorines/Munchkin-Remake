# Derelict Run: Rules

Derelict Run is an original competitive card game for 3-6 players. Rival salvage crews board dead starships, fight what lives inside, and strip the wrecks for parts. Planned implementation: C++ with SDL2.

Card data lives in [DOOR_CARDS.md](DOOR_CARDS.md) (Hatch deck) and [TREASURE_CARDS.md](TREASURE_CARDS.md) (Salvage deck).

## Goal

Reach Rank 10, then survive one full round. See [Extraction](#extraction).

## Components

| Component | Amount | Notes |
|---|---|---|
| Hatch deck | 80 cards | Threats, Hazards, Roles, Origins, Modifiers, Specials |
| Salvage deck | 60 cards | Gear, One-Shots, Promotions, Tricks |
| Six-sided die | 1 | Used for Escape rolls and some card effects |
| Charge tokens | 5 per player | Up to 7 for a Synth |
| Scrap tokens | Unlimited | Currency, tracked as a number per player |

## Setup

1. Shuffle each deck separately.
2. Each player starts at Rank 1 with 3 Charge and 0 Scrap.
3. Each player draws 2 Hatch cards and 2 Salvage cards.
4. Each player may play any Role, Origin, and Gear from hand.
5. Choose the first player at random. Play proceeds clockwise.

When a deck is empty, shuffle its discard pile to form a new deck.

## Core terms

- **Rank:** Player level, from 1 to 10. Rank can never drop below 1.
- **Power:** Rank + equipped Gear bonuses + active combat bonuses.
- **Threat:** A monster-type Hatch card with a Level.
- **Threat Power:** Level + active Modifiers + active card effects.
- **Fallout:** The penalty for failing to Escape a Threat.
- **Tag:** A Threat type: Vermin, Machine, Ghost, or Alien.
- **Airlock:** A shared, face-up row of up to 6 cards.
- **Spaced:** The defeat state described under [Spaced](#spaced).
- **Dormant N:** A fighter of Rank N or lower may discard this Threat with no reward and no Fallout.

## Turn structure

Each turn has these phases, in order.

### 1. Prep

- If you are in Extraction and still Rank 10, you win.
- You may take 1 card from the Airlock into your hand.
- You may equip Gear, change Role or Origin, and sell Gear.

### 2. Breach

Flip the top Hatch card face up.

- **Threat:** Start combat against it.
- **Hazard:** Apply it to yourself, then discard it.
- **Any other card:** Put it in your hand.

### 3. Sweep

Skip this phase if you fought a Threat during Breach. Otherwise choose one option:

- **Lure:** Play a Threat from your hand and fight it.
- **Sweep:** Draw 1 Hatch card face down into your hand and gain 1 Charge.

### 4. Stow

- You may equip Gear and sell Gear.
- Your hand limit is 6 cards.
- Move excess cards of your choice into the Airlock.
- If the Airlock holds more than 6 cards, discard the oldest until 6 remain.

## Combat

Run these steps in order.

1. **Reveal.** Place the Threat and apply its Special text.
2. **Request help.** The fighter may invite one ally. The ally must accept. Both players agree how the Salvage draw is split before the ally joins.
3. **Interference.** Starting left of the fighter, each player may play one legal card or pass. This repeats until every player passes in a row.
4. **Charge.** The fighter, then the ally, may each spend up to 2 Charge. Each Charge gives +2 Power to the crew.
5. **Resolve.** Compare crew Power with Threat Power.

The crew wins only if its Power is strictly higher. A tie is a loss, unless a Gunner is fighting.

### Winning

- The fighter gains the Threat's Ranks.
- The crew draws the Threat's Salvage count, split as agreed.
- Allies never gain Ranks.
- Discard the Threat and all combat-only cards.

### Losing

Each crew member makes an Escape roll against each Threat in the fight.

- Roll one die. A result of 5 or higher escapes.
- Before rolling, you may spend 1 Charge for +1 to that roll.
- On a failed roll, you suffer that Threat's Fallout.

### Multiple Threats

Reinforcements can add a second Threat to a combat. Add the Levels together for Threat Power. Winning grants the Ranks and Salvage of every Threat.

## Gear

Gear goes in a slot, and only equipped Gear gives a bonus.

| Slot | Limit |
|---|---|
| Helmet | 1 |
| Suit | 1 |
| Thrusters | 1 |
| Tool | 2 slots; a two-slot Tool uses both |
| Drone | 1 |

- **Bulky** Gear: You may equip only 1 Bulky item at a time.
- Gear cannot be equipped or swapped during combat.
- Gear with a restriction gives no bonus to a player who does not meet it.

## Scrap and buying Ranks

- Sell equipped Gear or Gear in hand during Prep or Stow, and gain Scrap equal to its Scrap value.
- Sold Gear goes to the Salvage discard pile.
- Once per turn, you may spend 10 Scrap to gain 1 Rank.
- Scrap cannot raise you to Rank 10.

## Roles and Origins

- You may hold 1 Role and 1 Origin. With no Origin card, you count as Human, which has no ability.
- You may discard your Role or Origin at any time outside combat.
- Playing a new card of the same type replaces the old one.

## Hazards

Hazards drawn during Breach affect you. A Hazard held in hand may instead be played on any player at any time, including during combat. A Hazard that has no valid target does nothing and is discarded.

## Spaced

Some Fallout and Hazards make you Spaced. When that happens:

1. Put all your equipped Gear and hand cards in the Airlock.
2. Keep your Rank, Role, Origin, and Scrap.
3. Set your Charge to 1.
4. End your turn if it is your turn.
5. Draw 2 Hatch and 2 Salvage cards at the start of your next turn.

## Extraction

1. A player can reach Rank 10 only by gaining Ranks from defeating a Threat.
2. On reaching Rank 10, that player enters Extraction and announces it.
3. Every other player takes one more turn.
4. If the player is still Rank 10 at the start of their next turn, they win.
5. If the player drops below Rank 10, Extraction ends and play continues.
6. If two players are in Extraction, the first to start a turn at Rank 10 wins.

## Timing rules

- Any effect that says "any time" can be used except during an Escape roll already in progress.
- Card text beats these rules.
- Effects resolve in the order played, and the newest resolves first if two conflict.
- Cards that say "fight" apply only during the current combat.
- A player cannot go below 0 Charge, 0 Scrap, or Rank 1.
- A Fallout that cannot be applied has no effect.

## Implementation notes

**Turn states:** `Prep`, `Breach`, `Combat`, `Sweep`, `Stow`, `EndTurn`.

**Combat states:** `Reveal`, `RequestHelp`, `Interference`, `Charge`, `Resolve`, `Escape`, `Cleanup`.

**Interference:** Keep a pass counter and reset it whenever a card is played. Leave the state when the counter equals the player count. Use a turn timer for human players.

**Card data fields:** `id`, `name`, `deck`, `type`, `count`, `level`, `salvage`, `ranks`, `tag`, `slot`, `bonus`, `scrap`, `bulky`, `restriction`, `effect`, `fallout`.

**Randomness:** Use one seeded RNG for shuffles and dice so that games can be replayed in tests.
