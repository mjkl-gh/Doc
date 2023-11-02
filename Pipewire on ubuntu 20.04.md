# Pipewire on ubuntu 20.04
copied from: 

Open your terminal and follow these steps:


1.  Install the package:
    
    ```bash
    sudo add-apt-repository ppa:pipewire-debian/pipewire-upstream && \
    sudo nala update && sudo nala install pipewire libspa-0.2-bluetooth pipewire-audio-client-libraries
    ```
    
2.  Reload the daemon:
    
    ```bash
    systemctl --user daemon-reload
    ```
    
3.  Disable PulseAudio:
    
    ```bash
    systemctl --user --now disable pulseaudio.service pulseaudio.socket
    ```
    
4.  If you are on Ubuntu 20.04, you also need to “mask” the PulseAudio by:
    
    ```bash
    systemctl --user mask pulseaudio
    ```
    

I am not sure but, if possible, you can try to run this on other versions too.  
5. After a new update of Pipewire, you also need to enable `pipewire-media-session-service`:

```bash
   systemctl --user --now enable pipewire-media-session.service
```

6. Reboot
 
7.  You can ensure that Pipewire is now running through:
    
    ```bash
    pactl info
    ```
    
    This command will give the following output, in Server Name you can see:
    
    ```
    PulseAudio (on PipeWire 0.3.28)
    ```
    
    Things should be working by now and you can see your microphone.
    

## Troubleshooting

If it doesn’t show up, then try restarting Pipewire by this command:

```
systemctl --user restart pipewire
```

> **Edit:** You need to uninstall ofono and phonesim from your system if you have them installed.

```
sudo apt remove ofono
sudo apt remove ofono-phonesim
```

If it’s still not showing your microphone, you can try rebooting once and remove and pair your Bluetooth device again to check if it works now.

## Uninstalling

If you want to rollback all the changes we did, you can do it by using:

```
systemctl --user unmask pulseaudio
systemctl --user --now disable pipewire{,-pulse}.{socket,service}    
systemctl --user --now enable pulseaudio.service pulseaudio.socket
```