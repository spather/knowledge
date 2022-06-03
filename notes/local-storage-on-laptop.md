# Setting Up local-storage Folder on Laptop
The idea is to create one folder in the home directory, ~/local-storage, in which everything that is locally stored on this computer lives. When moving stuff to a new computer, ideally only the contents of this folder need to be transferred. Everything else in ~/ should be stuff that's synced to the cloud.

## Create a local-storage and a bunch of dirs within it
```
mkdir local-storage
mkdir local-storage/Downloads
mkdir local-storage/Movies
mkdir local-storage/Music
mkdir local-storage/Pictures
mkdir local-storage/code
mkdir local-storage/tmp
```

## Replace some top-level directories links to local-storage versions

```
cd ~/
sudo rm -rf Downloads && ln -s local-storage/Downloads
sudo rm -rf Movies && ln -s local-storage/Movies
sudo rm -rf Music && ln -s local-storage/Music
```
Sadly, you can't do this with ~/Pictures on MacOS Catalina and beyond. Just leave Pictures in place and let the iCloud photos library live there. But store any actual local pictures in ~/local-storage/Pictures.

## Optionally create useful symlinks to other dirs:
```
ln -s local-storage/code
ln -s local-storage/tmp
```



