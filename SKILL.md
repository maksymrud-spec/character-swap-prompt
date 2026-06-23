---
name: character-swap-prompt
description: >
  Use this skill whenever the user provides two packs of images and wants to generate a Nano Banana 2 prompt for character swapping. Pack 1 contains reference characters on a white background with @name identifiers (e.g. @alice, @dave). Pack 2 contains scene photos with characters in environments that need to be replaced. The skill produces a single English prompt in the correct Nano Banana 2 format. Trigger this skill whenever the user uploads images and mentions @names, character replacement, Nano Banana, or asks to "remake" a scene with different characters. ALWAYS trigger this skill when the user writes "заміни персонажів" (Ukrainian for "replace characters") — even without any other context.
---

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
- Each scene has an identifier (e.g. `@32`, `@scene5`)
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

### Rules:
- `@[scene_id]` — the identifier of the scene image from Pack 2
- `@[charN]` — the identifier of the replacement character from Pack 1
- `[description of original character]` — a short visual description to identify who is being replaced, using position and 1–2 distinguishing traits (e.g. "woman with blue hair on the left", "man in red jacket in the center", "ginger woman on the left")
- Always end with: `Keep characters emotions, characters' cloth, camera position and poses from @[scene_id]`
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
**Input context:** Scene `@32` has a ginger woman on the left and a woman with blue hair on the right. Replace with `@wife` and `@alice`.

**Output:**
```
Remake @32 but with @wife instead of ginger woman on the left and @alice instead of woman with blue hair. Keep characters emotions, characters' cloth, camera position and poses from @32
```

### Example 2 — One character replaced
**Input context:** Scene `@07` has a tall man with a beard in the center. Replace with `@dave`.

**Output:**
```
Remake @07 but with @dave instead of tall bearded man in the center. Keep characters emotions, characters' cloth, camera position and poses from @07
```

### Example 3 — Three characters replaced
**Input context:** Scene `@15` has a blonde woman on the left, a man in a black jacket in the center, and a redhead girl on the right. Replace with `@lisa`, `@john`, `@kate`.

**Output:**
```
Remake @15 but with @lisa instead of blonde woman on the left, @john instead of man in black jacket in the center and @kate instead of redhead girl on the right. Keep characters emotions, characters' cloth, camera position and poses from @15
```

---

## Edge cases

- **If a character's position is ambiguous** — use outfit color or hair as the identifier instead of position
- **If the user doesn't specify who replaces whom** — ask before generating: "Which character from Pack 1 replaces which person in the scene?"
- **If there are more Pack 1 characters than scene characters** — only use the ones that match scene slots; ask the user if unclear
- **Never describe the Pack 1 character's appearance in the prompt** — Nano Banana 2 reads it directly from the reference image via the @name tag
