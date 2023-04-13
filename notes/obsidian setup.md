# Obsidian Setup

* Install from [Obsidian.md](https://obsidian.md/)
* Open the app and create MainVault in a non-Dropbox synced folder
* Set up Obsidian Sync per [the instructions](https://help.obsidian.md/Obsidian+Sync/Set+up+Obsidian+Sync) - connect to the existing MainVault remote vault rather than creating a new one. 
  * In the Sync plugin preferences:
    * Enable syncing all the file types
    * Enable sycning Active Core Plugins, Core Plugin Settings, Active Community Plugins, Community Plugin Settings


## Zettelkasten Setup
Reference video: https://www.youtube.com/watch?v=E6ySG7xYgjY
* Create 4 folders:
  * Files
  * Reference Notes
  * Templates
  * ZK
* Go to Preferences, Files & Links:
  * Change "Default location for new notes" to "In the folder specified below" and then specify "ZK" in the selector that appears. 
  * Change "Default location for new attachments" to "In the folder specified below" and then specify "Files" in the selector that appears.
* Go to Preferences, Hotkeys:
  * Filter to "template"
  * Bind "Templates: Insert template" to a hotkey of choice. I chose ⌘⌥T.
* Go to Preferences, Core Plugins:
  * Enable "Templates" if not enabled
* Go to Preferences, Community Plugins, (turn off Restricted Mode if curently on), select Browse:
  * Find "Sliding Panes", install and then enable it
  * Find "Admonition", install and then enable it
* Go to Preferences, find Templates in the Core Plugins section:
  * Set "Template folder location" to the Templates folder 
* In the Templates folder, create a new note called "Core Zettel Idea". In its contents paste the following:
```
{{date:YYYMMDD}}{{time:HHmm}}
Status: #idea
Tags:

# {{title}}

---
# References
```

Tips:
* Don't use Cmd+N to create new note - use Cmd+O instead which allows fuzzy searching through existing notes so you can find something you may want to add to instead of creating a new note. 
* Can use link to a ghost note (link to note that doesn't exist) as a tag. If you use the same ghost note link in a subsquent note it will be remembered as the same entity and will be the same node in the graph. These ghost notes are referred to as MOCs (Maps of Content).
  * Later, you can add content to these ghost notes, turning them into real notes. Consider creating a template for these that at least adds a #moc tag to them, like:
```
{{date:YYYMMDD}}{{time:HHmm}}
Status: #moc
Tags:

# {{title}}
```


## Zotero/Obsidian Link
Reference video: https://www.youtube.com/watch?v=D9ivU_IKO6M
* Install Zotero from https://www.zotero.org/
* Enable Sync:
  * Go to Zotero Preferences, Sync tab
  * Link zotero.org account
* Install BetterBibtex for Zotero per [the instructions](https://retorque.re/zotero-better-bibtex/installation/) and restart Zotero
* Right click on My Library, choose Export Library, select "Better CSL JSON" as the format and check the "Keep Updated" box. Select a file path to store the exported library. Put this in the parent folder of all Obsidian vaults (or somewhere else that's not Dropbox synced). Zotero will keep this updated as the libray changes. 
* In Obsidian:
  * Install the Citations plugin from Community Plugins and enable it
  * Go to the settings for the Citations Plugin:
    * In the "Citation database path" field, enter the path to the exported Zotero library from the above steps
    * Change the "Literature note folder" to "Reference Notes"
  * After this, going to the command palette and entering "Citiations: Insert literature not link" will give a dropdown of reference articles from Zotero and will create (if it doesn't exist) a reference note in the Reference Notes folder for it and insert a link into the current document. 
