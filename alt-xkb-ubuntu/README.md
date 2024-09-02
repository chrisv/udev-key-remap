# Ubuntu

Tested/used config in Ubuntu LTS versions 20/22/24 (Lenovo ThinkPad X370) in X/Gnome (default per LTS version), X/xmonad *and* X/Wayland

## make caps be esc:

```
$ dconf write /org/gnome/desktop/input-sources/xkb-options "['caps:escape']"
```

## or swap them:
```
$ dconf write /org/gnome/desktop/input-sources/xkb-options "['caps:swapescape']"
```


## verify:

```
$ dconf read /org/gnome/desktop/input-sources/xkb-options
['caps:escape']
```
