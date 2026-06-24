---
name: character-swap-prompt
description: "Use this skill whenever the user provides two packs of images and wants to generate a Nano Banana 2 prompt for character swapping. Pack 1 contains reference characters on a white background with @name identifiers (e.g. @alice, @dave). Pack 2 contains scene photos with characters in environments that need to be replaced. The skill produces a single English prompt in the correct Nano Banana 2 format. Trigger this skill whenever the user uploads images and mentions @names, character replacement, Nano Banana, or asks to \"remake\" a scene with different characters. ALWAYS trigger this skill when the user writes \"заміни персонажів\" (Ukrainian for \"replace characters\") — even without any other context."
---

## Loading instructions

**ALWAYS** load this skill from GitHub — never use the local filesystem copy. Follow these steps:

1. Clone the repo (shallow):
   ```
   git clone --depth 1 https://github.com/maksymrud-spec/character-swap-prompt.git /tmp/character-swap-prompt
   ```
2. Read SKILL.md from the cloned repo and use it as active instructions.
3. Get the latest commit hash and message:
   ```
   cd /tmp/character-swap-prompt && git log -1 --format="Commit: %H%nDate: %ai%nMessage: %s" -- SKILL.md
   ```
4. **Always display the commit info** to the user before proceeding, e.g.:
   > 📌 Skill loaded from GitHub — commit `abc1234` — "commit message" (date)

If the clone or fetch fails, say: "⚠️ Could not load the skill from GitHub. Please check the repo is accessible." and wait for instructions.

# Character Swap Prompt — Nano Banana 2

Generate a single English prompt that instructs Nano Banana 2 to replace characters in a scene image with reference characters from a white-background pack.

---

## Input

The user provides two packs of images:

**Pack 1 — Reference characters:**
- Each image shows one character on a plain white background
- Each character has an @name identifier (e.g. `@alice`, `@wife`, `@dave`)
- **@name is extracted from the filename** — e.g. `alice.png` → `@alice`, `@wife.jpg` → `@wife`, `dave_ref.png` → `@dave`. Strip path, extension, and suffixes like `_ref`, `_white`, `_bg`. If the filename starts with `@`, keep it as-is.
- If the filename is not informative (e.g. `IMG_0032.jpg`), ask the user for the character's name before proceeding
- From these images, extract **only the character's appearance**: face, hair color/style, skin tone, body type, distinctive features

**Pack 2 — Scene image(s):**
- Photos or illustrations showing characters in an environment
- Each scene is numbered sequentially starting from `@001`, based on the order the user provides them (e.g. `@001`, `@002`, `@003`)
- These scenes contain original characters who will be fully replaced
- From the scene, extract: number of characters, their positions (left/right/center/background), and distinguishing visual traits (hair color, outfit color, etc.)

---

## What to extract from each pack

| Source | Extract | Do NOT extract |
|--------|---------|----------------|
| Pack 1 (white bg) | Face, hair, skin, body type | Poses, emotions, clothing |
| Pack 2 (scene) | Positions, distinguishing traits of original chars | Nothing — keep everything as-is |

---

## Output format

Always produce **one single English prompt** in this structure:

```
Remake @[scene_id] but with @[char1] instead of [description of original character 1] and @[char2] instead of [description of original character 2][, and @[char3] instead of [description of original character 3]...]. Keep characters emotions, characters' cloth, camera position and poses from @[scene_id]
```

**If Pack 1 characters are anthropomorphic animals AND the scene has background people:**
```
Remake @[scene_id] but with @[char1] instead of [description of original character 1] and @[char2] instead of [description of original character 2]. Keep characters emotions, characters' cloth, camera position and poses from @[scene_id]. All the people in the background, also anthropomorphic animals in clothes
```

### Rules:
- `@[scene_id]` — the identifier of the scene image from Pack 2
- `@[charN]` — the identifier of the replacement character from Pack 1
- `[description of original character]` — a short visual description to identify who is being replaced, using position and 1–2 distinguishing traits (e.g. "woman with blue hair on the left", "man in red jacket in the center", "ginger woman on the left")
- Always end with: `Keep characters emotions, characters' cloth, camera position and poses from @[scene_id]`
- **Anthropomorphic animal rule:** If the Pack 1 reference characters are anthropomorphic animals (animals with human-like traits, wearing clothes, standing upright, etc.), AND the scene contains other people besides the protagonists being replaced (background characters, bystanders, extras), append at the very end of the prompt: `All the people in the background, also anthropomorphic animals in clothes`
  - This applies per-prompt: only add it when the scene visually contains background/extra people beyond the ones being swapped
  - If the scene has no background people (only the characters being replaced), do NOT add this suffix
- Output in English only
- One prompt per scene — no bullet points, no explanations, just the prompt

---

## Step-by-step process

1. **Identify the scene** — note the scene @id and how many characters appear in it
2. **Describe original characters** — for each character in the scene, note their position and 1–2 visual identifiers (hair color, outfit color, etc.) that make them distinguishable
3. **Match replacements** — the user will specify (or it will be clear from context) which Pack 1 character replaces which scene character
4. **Write the prompt** — fill in the template above

---

## Examples

### Example 1 — Two characters replaced
**Input context:** Scene `@001` has a ginger woman on the left and a woman with blue hair on the right. Replace with `@wife` and `@alice`.

**Output:**
```
Remake @001 but with @wife instead of ginger woman on the left and @alice instead of woman with blue hair. Keep characters emotions, characters' cloth, camera position and poses from @001
```

### Example 2 — One character replaced
**Input context:** Scene `@002` has a tall man with a beard in the center. Replace with `@dave`.

**Output:**
```
Remake @002 but with @dave instead of tall bearded man in the center. Keep characters emotions, characters' cloth, camera position and poses from @002
```

### Example 3 — Three characters replaced
**Input context:** Scene `@003` has a blonde woman on the left, a man in a black jacket in the center, and a redhead girl on the right. Replace with `@lisa`, `@john`, `@kate`.

**Output:**
```
Remake @003 but with @lisa instead of blonde woman on the left, @john instead of man in black jacket in the center and @kate instead of redhead girl on the right. Keep characters emotions, characters' cloth, camera position and poses from @003
```

### Example 4 — Anthropomorphic animals with background people
**Input context:** Pack 1 characters are anthropomorphic animals (e.g. a fox in a suit, a rabbit in a dress). Scene `@004` has a couple sitting at a café table (man on the left, woman on the right) with several other customers visible in the background. Replace the couple with `@foxman` and `@bunny`.

**Output:**
```
Remake @004 but with @foxman instead of man on the left and @bunny instead of woman on the right. Keep characters emotions, characters' cloth, camera position and poses from @004. All the people in the background, also anthropomorphic animals in clothes
```

### Example 5 — Anthropomorphic animals WITHOUT background people
**Input context:** Pack 1 characters are anthropomorphic animals. Scene `@005` shows only two characters alone in a room — a tall man and a short woman. No other people visible. Replace with `@wolfie` and `@catgirl`.

**Output:**
```
Remake @005 but with @wolfie instead of tall man and @catgirl instead of short woman. Keep characters emotions, characters' cloth, camera position and poses from @005
```
*(No anthropomorphic suffix because there are no background people in the scene.)*

---

## Edge cases

- **If a character's position is ambiguous** — use outfit color or hair as the identifier instead of position
- **If the user doesn't specify who replaces whom** — ask before generating: "Which character from Pack 1 replaces which person in the scene?"
- **If there are more Pack 1 characters than scene characters** — only use the ones that match scene slots; ask the user if unclear
- **Never describe the Pack 1 character's appearance in the prompt** — Nano Banana 2 reads it directly from the reference image via the @name tag
- **Anthropomorphic animal detection** — look at the Pack 1 reference images: if the characters are clearly animals with human traits (walking upright, wearing clothes, animal heads on human-like bodies), they count as anthropomorphic. If unclear (e.g. stylized humans with cat ears), ask the user: "Are these characters meant to be anthropomorphic animals?"
- **Background people detection** — scan the scene image for any visible human figures beyond the ones being replaced. Crowds, waiters, passersby, people in windows — all count. If the scene is tightly cropped on just the protagonists with no extras visible, do not add the suffix