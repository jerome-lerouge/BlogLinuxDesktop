# Tunning Firefox

Objective playing streaming in 4 K. By default Firefox can only play 720p video (or lower) with Widevine, due to not using hardware DRM playback.

## Install MVP player and yt-dlp

`$ paru -S mvp yt-dlp
$ cp /usr/share/doc/mpv/mpv.conf ~/.config/mpv/mpv.conf`

Edit mvp.conf 

Create a minimal config for yt-dlp
`nvim ~/.config/yt-dlp/config` 

Launch a Twitch video : 

`$ mpv --ytdl=yes https://www.twitch.tv/ekfben` 

## Install Addon open with in firefox

1. Install [open with addon](https://addons.mozilla.org/fr/firefox/addon/open-with/) 
2. Go to Add-ons > Open With > Preferences.
3. Proceed with instructions to install a file in your system and test the installation.
4. Click Add browser.
5. In the dialog, write a name for this menu entry and command to start a video streaming capable player (e.g. /usr/bin/mpv).

    Optionally, add needed arguments to the player (e.g. you may want --force-window --ytdl for mpv).
