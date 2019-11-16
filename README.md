Personal notes synced on Git
=============================

![Yet another note taking app](https://imgs.xkcd.com/comics/standards.png)

Almost all of the note taking apps are lacking in certain aspects.

Some lacks versioning. Some lacks the vim-style editing. Some lacks of a good way to resolve conflicts. Some lacks visibility and permanence.

Once you read the above disadvantages above, you probably realise that Git is a perfect building block for a perfect note taking app.

You can store your notes (and its history) on Github or Gitlab, which offers good degrees of visibility and permanence. You can use an editor of your choice like Vim, Emacs, Sublime, or Atom.

The only problem with using Git is that your notes aren't synced automatically in real time, and that's exactly what Git notes mainly does.

Git Notes runs as a background process. It detects file changes, immediately commit, and push. Git Notes also does pull at the startup and every hour.

Conflicts are handled in an intuitive way to programmers. Git Notes simply commit and push the git-style conflict text. You would see the conflict in the format that you, as a programmer, are already familiar with.

I hope Git Notes hits all the notes for you like it does for me. Enjoy!

  
Architecture
-------------

Our main engine observes the current state of the git repo and make one action to transition to the next state.

Here are all the states:

* __dirty__: Unstaged change -> `git add .` -> __staged__
* __staged__: Staged change -> `git commit -m 'Updated'` -> __ahead__ or __out-of-sync__
* __ahead__: Ahead of the remote branch and can fast forward -> `git push` -> __synced__
* __out_of_sync__: The remote branch has unseen commits -> `git pull` -> __ahead__ (no conflict) or __dirty__ (there are conflicts)
* __synced__: The local branch matches the remote branch

This loop runs until no changes are observed. If the engine doesn't end on __synced__, something is wrong.

When the file change is detected, we invoke the engine again.

The file changes are detected by running `git status` every 10 seconds.

  
Develop
--------

* `go build` to build the binary
* `go run .` to run the application
* `go test` to run tests
* `gofmt -w .` to format all files
* `goimports -w .` to organize imports in all files
  
  
Installation
-------------

### Ubuntu

Move `./service_conf/linux.git-notes.service` to `/etc/systemd/user/git-notes.service`

Reload service file after moving: `systemctl --user daemon-reload`

Run: `systemctl --user start git-notes.service`

Read logs: `journalctl -u git-notes.service --follow --user`


### Mac

Move `./service_conf/mac.git-notes.plist` to `~/Library/LaunchAgents/git-notes.plist`

Run:

1. `launchctl load ~/Library/LaunchAgents/git-notes.plist`
2. `launchctl start ~/Library/LaunchAgents/git-notes.plist`

If the plist file is changed, you will need to unload it first with: `launchctl unload ~/Library/LaunchAgents/git-notes.plist`.

Read logs: use Console.app. Search for `logger`.


### Windows

TBD