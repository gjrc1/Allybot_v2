# AllyBot v2

A bot for generating and maintaining item pages on the [Project: Gorgon Wiki](https://wiki.projectgorgon.com).

## About

This project is based on the original work of [Alleryn](https://github.com/alleryn/ProjectGorgonWiki-AllyBot).

I took over development in April 2025 after Alleryn stopped maintaining AllyBot. If Alleryn decides to resume development, I will remove this fork.

For more information about the original project, see [Alleryn's Wiki Page](https://wiki.projectgorgon.com/wiki/User:Alleryn#AllyBot).

## Requirements

- **Python:** Version 3.5 or newer should work, although this has not been fully verified.
- **Requests:** The Python `requests` package must be installed.

Install the dependency with:

`pip install requests`

## Project Structure

| Directory | Description |
|---|---|
| `Archive/` | Stores previous versions of JSON data and generated wiki data. |
| `Backups/` | Contains Python file backups created by `Backup.py`. |
| `Data/` | Stores formatted JSON files and generated wiki data. This is the main working directory. |
| `Generators/` | Contains Python scripts responsible for generating wiki data. |
| `Jsons/` | Stores raw JSON data downloaded from the Project Gorgon CDN. |
| `Logs/` | Contains log files generated during execution. |

---

## How to Run

### A. After a Game Version Update

Follow these steps whenever the game version changes, for example, from `v341` to `v342`.

### Step 1 — Download Game Data

Run:

`python JsonCacher.py`

This script:

1. Downloads the latest JSON data from `cdn.projectgorgon.com`.
2. Saves the new files in `Jsons/vXXX/`.
3. Moves the previous version's data to `Archive/Jsons/vXXX/`.
4. Generates reports identifying changes that may require manual updates.

#### Files Requiring Review

After running `JsonCacher.py`, inspect the following files in the new version directory.

**1. `removedItems.txt`**

Lists items removed from the game.

- Usually empty.
- If items were removed, consider updating their wiki pages with Lore or Trivia explaining their removal.

**2. `nameChangedItems.txt`**

Lists renamed items.

- Manually move the relevant information from the old wiki pages to the new ones.
- Consider documenting the name changes in Lore or Trivia sections.

**3. `addedKeywordsForPython.txt`**

Lists new item keywords that must be added to:

`Generators/WikiGetItemCategoryLink.py`

Manually define a suitable plural form for each keyword.

**4. `addedKeywordsForWiki.txt`**

Lists keywords that must be added to the wiki template:

[Template:ItemCategory](https://wiki.projectgorgon.com/wiki/Template:ItemCategory)

**5. `removedKeywords.txt`**

Lists removed item keywords.

- Usually empty.
- You may remove obsolete keywords from the Python generator and wiki templates.
- This cleanup is optional but can slightly improve efficiency.

**6. `addedNpcsForNpclocationsJson.txt`**

**Important: This step must be completed even when the report is empty.**

First, copy `npclocations.json` from the archived version to the new version.

Example:

Source: `Archive/Jsons/v341/npclocations.json`

Destination: `Jsons/v342/npclocations.json`

Next:

1. Open `addedNpcsForNpclocationsJson.txt`.
2. Identify any newly added NPCs.
3. Find their locations through in-game exploration or other players.
4. Update `npclocations.json`.
5. If a location is unknown, use `Unknown`.

This process remains manual to ensure that NPC locations are reviewed before generating wiki data.

**7. `removedNpcs.txt`**

Lists removed NPCs.

- You may remove obsolete entries from `npclocations.json`.
- Temporary event NPCs, such as Moxie from the Crafting Caravan, may disappear and return.
- It may be easier to keep temporary NPC entries instead of repeatedly removing them.

**8. `addedRecipesForRecipeTemplate.txt`**

Lists recipes that require updates to the following wiki templates:

- [Template:Recipe](https://wiki.projectgorgon.com/wiki/Template:Recipe)
- [Template:RecipeToItem](https://wiki.projectgorgon.com/wiki/Template:RecipeToItem)

**9. `removedRecipes.txt`**

Lists recipes removed from the game.

Review the entries and clean up obsolete recipe information where appropriate.

### Step 2 — Format JSON Files

Run:

`python FormatJsons.py`

This processes the downloaded JSON data and saves the formatted files in the corresponding version directory under `Data/`.

### Step 3 — Generate Wiki Data

Run:

`python GenerateWikiData.py`

Generates the wiki text that will later be compared, versioned, and uploaded.

At this stage, the generated text contains version placeholders.

### Step 4 — Compare Wiki Data

Run:

`python CompareWikiData.py`

Compares the newly generated wiki text against the previous version.

The script records which files have changed, allowing subsequent steps to process only modified wiki content.

### Step 5 — Apply Version Numbers

Run:

`python VersionizeWikiData.py`

Replaces the placeholder `__VERSION__` with the current game version.

Only wiki text that has changed since the previous version is processed.

### Step 6 — Upload Wiki Data

Run:

`python UploadWikiData.py <username> <password>`

Replace `<username>` and `<password>` with your bot account credentials.

**Requirements:**

- A registered wiki bot account.
- Username and password provided through registration with a wiki administrator.

#### Resuming Interrupted Uploads

If an upload is interrupted, AllyBot records completed work in:

`CompletedSections.txt`

To resume:

1. Fix the issue that interrupted the upload.
2. Run `UploadWikiData.py` again.
3. The script uses `CompletedSections.txt` to avoid uploading already completed sections.

To force an already uploaded section to be processed again, manually edit `CompletedSections.txt`.

#### Automatic Cleanup

After a successful upload, AllyBot:

1. Archives the relevant Python files.
2. Archives data from the previous version.
3. Updates `botVersion.txt`.
4. Prepares the project for the next game update.

---

### B. After Updating the Bot's Code

If you modified AllyBot itself without a new game version:

1. Run `python Backup.py` to back up the Python files to `Backups/`.
2. Run `python FormatJsons.py`.
3. Run `python GenerateWikiData.py`.
4. Run `python CompareWikiData.py`.
5. Run `python VersionizeWikiData.py`.
6. Run `python UploadWikiData.py <username> <password>`.

---

## Troubleshooting

### Missing Item Information

**Problem:** Some item pages do not display certain information, such as **"Producing with recipe"**.

**Possible cause:** The recipe uses a `resultEffect` that is not recognized by the formatter.

**Solution:**

1. Identify the recipe associated with the affected item.
2. Check its `resultEffect` value.
3. Search for that value in `FormatJson.py`.
4. If the effect is missing, add the appropriate handling.
5. Regenerate the wiki data.

This ensures that recipes using the previously unrecognized effect can be processed correctly.

---

## Credits

**Original author:** [Alleryn](https://github.com/alleryn/ProjectGorgonWiki-AllyBot)

**Original project:** [ProjectGorgonWiki-AllyBot](https://github.com/alleryn/ProjectGorgonWiki-AllyBot)

**Current maintenance:** Development continued in April 2025 following the original author's departure.

**Wiki:** [Project: Gorgon Wiki](https://wiki.projectgorgon.com)
