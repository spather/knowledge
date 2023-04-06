# Obsidian Setup

* Install from [Obsidian.md](https://obsidian.md/)
* Open the app and create MainVault in a non-Dropbox synced folder
* Set up Obsidian Sync per [the instructions](https://help.obsidian.md/Obsidian+Sync/Set+up+Obsidian+Sync) - connect to the existing MainVault remote vault rather than creating a new one. 
  * In the Sync plugin preferences:
    * Enable syncing all the file types
    * Enable sycning Active Core Plugins, Core Plugin Settings, Active Community Plugins, Community Plugin Settings


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
