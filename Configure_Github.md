# Configure Github on Archlinux #

## Add a new SSH key on Github ##

### Create the SSH public key ###

Generate the new public key:

`ssh-keygen -t ed25519 -C "jerome.lerouge@gmail.com"`

Note it's possible to change the passphrase: [Documentation SSH key passphrase on Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/working-with-ssh-key-passphrases)
We are using the default file name for the SSH key in the next instructions
> id_ed25519
>
### Add SSH key to the ssh-agent

For bash and zsh shell:

`$ eval "$(ssh-agent -s)"`

For fish shell:

`$ eval (ssh-agent -c)`

Add the SSH private key to the ssh-agent:

`$ ssh-add ~/.ssh/id_ed25519`

It added identity into ~/.ssh/id_ed25519

[Github documentation generate new SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

### Add the new SSH key to Github account ###

Copy the SSH public key to the clipboard:

`$ cat ~/.ssh/id_ed25519.pub`

## Create the repository on Github ##

- Connect on Github account and start the process for creating the new repository.
- Choose the GPL v3 copyleft license because I require that any modifications to your code be released under the same license, you should choose a copyleft license, such as the GPL v3.

## First time git setup ##

Show all the settings and where they are coming from

`$ git config --list -show-origin
$ git config --global user.name "Jérôme Lerouge"
$ git config --global user.email jerome.lerouge@gmail.coming
$ git config --global core.editor NVIM_APPNAME="Lazyvim" nvim`

## Contribute to an existing repository ##

Download a repository on Github to our machine and chane into the repo directory

`$ git clone git@github.com:jerome-lerouge/BlogLinuxDesktop.git
$ cd BlogLinuxDesktop`

Stage the new files

`$ git add *.md`

Take a snapshot of the staging area (anything that's been added)

`$ git commit -m "First save with all files currently being edited."`

Push change to github

`$ git push --set-upstream origin main`

After a modification in the Configure_Github.md, need to push file modifications on github:

`$ git commit -a -m "Add git setup and information for first commit, push modification to github."
$ git push`

## Documentation ##

[Archlinux documentation for generating public](https://wiki.archlinux.org/title/SSH_keys)

[Github Doc Authentification, connect with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

[Github Skills (course to learn basic github usage)](https://skills.github.com/)

[A quick Github SSH Clone example](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/github-clone-with-ssh-keys)
