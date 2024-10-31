## HOWTO install kanata

```sh
# install kanata -- https://github.com/jtroo/kanata
cargo install kanata
# copy config
mkdir -p ~/.config/kanata
cp config.kbd ~/.config/kanata
# create systemd service
mkdir -p ~/.config/systemd/user
cp ./kanata.service ~/.config/systemd/user/
# necessary group membership / permissions -- https://github.com/jtroo/kanata/blob/main/docs/setup-linux.md
sudo groupadd uinput
sudo usermod -aG input $USER
sudo usermod -aG uinput $USER
# start kanata.service (as user)
systemctl --user daemon-reload
systemctl --user enable kanata.service
systemctl --user start kanata.service
systemctl --user status kanata.service   # check whether the service is running
# copy uinput rules and reload
sudo cp 99-input.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && sudo udevadm trigger 
# note: ubuntu may keep giving permission errors here
# reboot at this time may help resolve this issue
```

## refs/links
- https://github.com/jtroo/kanata/blob/main/docs/setup-linux.md
- https://github.com/dreamsofcode-io/home-row-mods/tree/main/kanata/linux
