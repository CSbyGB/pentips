# Git tips

## Use multiple git accounts

- You might have a git for work and a personnal git.
- If you need to use multiple git account the best way is to set multiple local configs and one global
- In your home config file `/home/username/.gitconfig`

```bash
[init]
    defaultBranch = main

[includeIf "gitdir:/home/username/localfolderOne/"]
    path = /home/username/localfolderOne/.gitconfig

[includeIf "gitdir:/home/username/localfolderTwo/"]
    path = /home/username/localfolderTwo/.gitconfig
[safe]
    directory = /docs
```

- In you first local folder create another .gitconfig as follow

```bash
[user]
    name = First Last
    email = first.last@domain.com
    signingkey = ID-OF-GPG-KEY
[gpg]
    program = gpg
[commit]
    gpgsign = true
[init]
    defaultBranch = main
```

- In your second local folder do the same thing
- If you need more info on how to create and setup gpg key [check out this article on jigarius](https://jigarius.com/blog/signing-git-commits)
- To add a gpg key in github go to Settings>SSH and GPG key and click on "New GPG Key" and add the public key
- After this setup when you will commit and push things you should be good to go.

## Resolve user conflicts when pushing

### Temporary solution

If you get an error like this when trying to push `remote: Permission to repo.git denied to old_username`  
A temporary solution is to push over https by specifying the username in the link like this:  
`git push https://user@github.com/repo.git/`

### More permanent solution

Make a grep about the user mentioned by the error message `grep -iar gold_username`  
It takes a while but should give you the config file where the user is mentionned. mine was `.config/gh/hosts.yml`  
