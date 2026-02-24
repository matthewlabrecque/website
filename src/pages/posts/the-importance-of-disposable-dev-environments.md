---
title: 'The Importance of Disposable Dev Environments'
pubDate: 2026-02-23
layout: ../../layouts/MarkdownPostLayout.astro
---
One of the more common things that us programmers and computer enthusiasts will encounter in our lives is the day we blow up a system. It isn't a matter of if, but when. I'm pretty sure that once in my life I had to reinstall Windows 10 five times in one week, because I kept thinking I could mess around with the registry editor and improve the performance of my computer "just a little more", and then boom: It would stop booting to save my life.

Going along with that process is the oftentimes aggravating process of having to reconfigure an entirely fresh computer from scratch, trying to remember each and every little configuration setting that you changed, remembering all the programs you always used, and (in my opinion the worst part) not only remembering which programming languages and compilers you need, but also which plugins, packages, and dependencies you need for your projects. I went through the first 23 years of my life thinking this was just how it was meant to be, and that if I wanted any form of stability with using Linux as my main operating system (need I remind developers of how terrible Microsoft has let Windows become), I would have to stick with either Debian or Fedora.

That was, until I stumbled across a video by Nathan Laundry titled [I Automated My Mac Dev Setup in 9 Minutes](https://youtu.be/Im3b6dhnp04?si=c2PVYVQdAUxLFK8S). In his video, he introduced the concept of a "instantly disposable environment", which can be broken down into two main parts:

1) Data does not live on a computer, but exists either in the cloud or on a NAS server.
2) Computer configurations should be easy to deploy on any system, meaning that if we want any additional customization of our system it should be done through some sort of automation scripting.

Being a programmer who constantly breaks stuff without even trying, I had a feeling that Nathan was on to something with his idea. So I decided to try implementing his philosophy in my own development and computer usage.

The first part is easy enough to do, I have an Android cell phone so I already pay for 2TB of Google Drive cloud storage. I simply use the `rclone` utility to automate syncing of my documents/pictures between my laptop and the cloud. Now, one might argue that someone like me who cares about privacy is being hypocritical by using Google Drive to store my data, but the reality is I'm a broke college student so I have to make do with that I have.

Scripting some sort of autoconfiguration script proved to be the more difficult one, as I have more than just programs I want to set up. I do multiple types of programming (being both a Math and CS student at college) as well as preferring a keyboard-driven layout. But I also want things to be plug and play for most things so I have a rock solid, stable base to build off of. I ended up choosing Fedora to be the baseline for my new system, mainly because `dnf` meets all the requirements I want in a package manager, I don't mind a couple Flatpaks and hell, if it's good enough for Linus Torvalds it definitely will be good enough for me as a CS/Math student who definitely isn't maintaining a huge kernel for an operating system.

Next up was writing the actual script. While many people like splitting each package into it's own script, this creates an absolute rat's nest of scripts all calling each other (if you want to die inside, just take a look at the Omakub/Omarchy source code). I instead decided to have each category of package form it's own array, and then my script would iterate through the arrays to install packages, like so:

```bash
TERMINAL_APPLICATIONS=("btop" "fastfetch" "ghostty" "git" "neovim" "rclone" "starship" "tailscale" "zsh")
for package in "${TERMINAL_APPLICATIONS[@]}"; do
  sudo dnf install "$package" -yq
done
```

The most difficult part though was the actual configuration. While GNOME is pretty good by itself, I do change a lot of keybinds (such as rebinding `<Super>#` to switch between workspaces, allowing me the main thing I like about tiling window managers). Thankfully, while the GNOME devs do try to force a highly opinionated system, there is still this lovely command called `gsettings` which allows you to override the default keybindings that are located in GNOME (I also took the liberty of rebinding Caps Lock to Escape).

```bash
for i in {1..9}; do
    gsettings set org.gnome.shell.keybindings switch-to-application-$i "[]"
    gsettings set org.gnome.desktop.wm.keybindings switch-to-workspace-$i "['<Super>$i']"
  done
  gsettings set org.gnome.desktop.input-sources xkb-options "['caps:escape']"
```

There's also my Ghostty and Neovim configurations that I use. At risk of making it unnecessarily complicated, I decided to just upload the configuration files to GitHub and then use the `wget -o` command to pull the raw file and pipe it into the configuration file, which worked good enough in testing.

Okay so I finally have this configuration script all set up with everything, but how do I actually use it when I blow up something on my laptop? Well, my workflow is pretty simple:

1) Install base Fedora Workstation, which includes `git`
2) Clone my dotfile repo which includes the script, and run it
3) Once the script has finished running, all that's left is logging into my Google Drive so I can then pull my pictures and other documents.
4) Log into Bitwarden (my password manager) and we're good to go!

Now, did taking five hours over the course of two days to write a script that configures my system for me end up being worth it? For me, I'd answer yes. Because now I don't worry about what happens if I break something, because I know I can get back to a working machine in less than 15 minutes, and I don't worry about losing data anymore.
