# Install essentials
```bash
sudo apt install curl nala
```

# Add repo's

### Signal
```bash
# 1. Install our official public software signing key
wget -O- https://updates.signal.org/desktop/apt/keys.asc | gpg --dearmor > signal-desktop-keyring.gpg
cat signal-desktop-keyring.gpg | sudo tee -a /usr/share/keyrings/signal-desktop-keyring.gpg > /dev/null

# 2. Add our repository to your list of repositories
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/signal-desktop-keyring.gpg] https://updates.signal.org/desktop/apt xenial main' |\
  sudo tee -a /etc/apt/sources.list.d/signal-xenial.list
```

### Spotify 
```bash
curl -sS https://download.spotify.com/debian/pubkey_7A3A762FAFD4A51F.gpg | sudo gpg --dearmor --yes -o /etc/apt/trusted.gpg.d/spotify.gpg  
echo "deb http://repository.spotify.com stable non-free" | sudo tee /etc/apt/sources.list.d/spotify.list
```

# Install all the things
```bash
sudo nala install curl \
					fish \
					git \
					neofetch \
					network-manager-l2tp \
					network-manager-l2tp-gnome \
					obsidian \
					podman \
					signal-desktop \
					spotify-client
```

# Finish installing
### Fish
```bash 
sudo apt-get update
curl -L https://github.com/oh-my-fish/oh-my-fish/raw/master/bin/install > install
fish install
chsh -s /usr/bin/fish
```

