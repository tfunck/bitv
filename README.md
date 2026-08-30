# Stella Tenebris — campaign site

A single-page site that renders itself from the text files in `data/`. There is no build step. Edit a file, push, refresh: the chart, faction register, dossier, and rules all update. The solar system graphic is generated from the body files at page load; it is not an image.

## Publishing (once)

1. Create a GitHub repository and put this whole folder in it (`index.html` at the root).
2. Repository Settings → Pages → Deploy from a branch → `main`, root folder.
3. The site appears at `https://<username>.github.io/<repo>/` after a minute or two.

After that, every commit updates the live site. Files can be edited directly in the GitHub web interface, including from a phone.

## Local preview

Browsers block `fetch` on files opened directly, so double-clicking `index.html` shows an error screen by design. To preview locally, run `python3 -m http.server` in this folder and open `localhost:8000`.

## Editing

### Bodies (major locations)

One file per body in `data/`, listed in `data/manifest.json`. Format:

```
---
name: The Dream
kind: station
orbit: 2
angle: 40
factions: mithril, stpa
---

Description paragraphs in markdown.

## Dockside
A minor location: any `##` section inside a body file
renders as a nested site on that body's card.
```

Frontmatter keys:

- `name` — display name.
- `kind` — glyph: `star`, `blackhole`, `shattered`, `planet`, `ruins`, `station`, `facility`.
- `orbit` — ring 1 (inner) to 4 (outer). A body with no `orbit` and no `pos` sits at the star's position.
- `angle` — degrees around the orbit: 0 is right, 90 is down, 180 is left, -90 (or 270) is up.
- `pos` — optional `x, y` override (canvas is 700 × 400); used instead of orbit/angle. Legon uses this.
- `blockade` — `true` draws the dashed interdiction ring.
- `label` — `above` places the name above the glyph (default is below; bodies near the bottom edge flip automatically).
- `factions` — comma-separated faction `short` tags; renders the chips and links the faction register highlight.

**Site connections and the deck schematic**: a site header can declare which sites it connects to:

```
## Landing Bay | connects: Service Tunnels, Assembly Halls
```

Targets are site names within the same body; declare each connection once, on either side. Any body with at least one resolved connection renders a "deck schematic" on its card: an automatically laid-out wiring-diagram graph of its sites, with tappable boxes that jump to each site's text. Sites are placed by distance from the first-listed site, so list the entry point (docks, airlock, front door) first for the most natural left-to-right flow. Unconnected sites still render as text sections; they are simply left off the schematic.

**To add a body**: create the file, add its filename to the `bodies` list in `manifest.json`. Done.

### Factions

All in `data/factions.md`, one `##` section each:

```
## Stella Tenebris Planetary Authority | short: STPA | tier: V | relations: Clankers -2, Mithril +1, Mindful 0

Description in markdown. A section with no text shows
"NO DATA ON FILE" on the site, which is a feature.
```

The `short` tag is the id that body files reference.

**Relations** run -2 (war) to +2 (allied or in close collusion), and drive the relations wheel below the faction register. Rules:

- Define each pair once, on either faction's line. Targets can use the `short` tag or the full name.
- A `0` means the factions interact regularly but neutrally, and is drawn as a dashed line.
- Leave a pair out entirely if the factions don't interact; absent pairs are not drawn.
- Line style: thick red -2, thin red -1, dashed gray 0, thin amber +1, thick amber +2. Node size reflects tier.
- Tapping a node opens that faction's card and dims all edges except its own.

### NPCs

All in `data/npcs.md`:

```
## Vasquez | faction: Mithril | location: The Dream

Dossier text.
```

### Rules

`data/rules.md` is the SRD, rendered verbatim on the Rules tab with a jump list built from its `##` headings. To update the rules, replace the file.

## Automatic links

Every body name, faction name (or short tag), NPC name, and minor-location heading is auto-linked wherever it appears in card text: mentioning The Dream in any description produces a link that opens The Dream's card, and a minor-location name jumps to its section inside its parent body. This needs no markup; just write the names. Longest names win ("Mithril Inc" links as a whole rather than as "Mithril"), an entry never links to itself on its own card, and names shorter than three characters are ignored. The Rules tab is left unlinked.

### Title and stamp

`title` and `stamp` in `manifest.json` set the nameplate and the revision mark.
