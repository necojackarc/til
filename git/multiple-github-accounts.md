## Multiple GitHub Accounts

The easiest way is to set SSH hosts for each account, then let Git pick the right host and identity based on where the repository lives.

Let's say you have one personal account and one business account, and you keep all business repositories under `~/work/business/`.

## SSH Keys

Create a dedicated key for each account:

```sh
mkdir -p ~/.ssh/github-business
ssh-keygen -t ed25519 -C "<Business Email>" -f ~/.ssh/github-business/id_ed25519
```

Then, register `~/.ssh/github-business/id_ed25519.pub` to your business account (Settings → SSH and GPG keys).

## SSH Config

You can have the following settings in your `~/.ssh/config`:

```ssh
Host github.com
  User git
  HostName github.com
  IdentityFile ~/.ssh/github/id_ed25519
  IdentitiesOnly yes

Host github-business
  User git
  HostName github.com
  IdentityFile ~/.ssh/github-business/id_ed25519
  IdentitiesOnly yes
```

`IdentitiesOnly yes` is the important part.
Without it, `ssh` offers every key held by `ssh-agent` as well as `IdentityFile`.
GitHub identifies you by the first key it recognises, so you can end up authenticated as your personal account even when connecting to `github-business`.
You'd then see `Repository not found`, or worse, push with the wrong account without noticing.

Check which account each host authenticates as:

```sh
ssh -T git@github.com            # Hi <personal-user>!
ssh -T git@github-business       # Hi <business-user>!
```

## Git Config

Instead of switching hosts by hand, let Git rewrite URLs and switch your identity per directory.

*~/.gitconfig*

```ini
[user]
  name = <Name>
  email = <Personal Email>

[url "git@github-business:<ORG_NAME>/"]
  insteadOf = ssh://git@github.com/<ORG_NAME>/
  insteadOf = git@github.com:<ORG_NAME>/
  insteadOf = https://github.com/<ORG_NAME>/

# Keep includeIf last so the business values win inside its repos.
[includeIf "gitdir:~/work/business/"]
  path = ~/.gitconfig-business
```

*~/.gitconfig-business*

```ini
[user]
  name = <Name>
  email = <Business Email>
```

- `includeIf` applies `~/.gitconfig-business` to every repository under `~/work/business/`. The trailing `/` is required.
- `insteadOf` sends anything under `<ORG_NAME>` through the `github-business` host. Remotes can stay as plain `github.com` URLs, so you can clone as usual (e.g. `ghq get <ORG_NAME>/<REPO_NAME>`) and existing clones keep working.

Unlike `.envrc`, this works without direnv and applies to commits made from any tool, including IDEs.

### Cover every remote URL form

`insteadOf` only rewrites a URL that starts with the exact string you give it.
The same repository can be written as `ssh://git@github.com/<ORG_NAME>/...`, `git@github.com:<ORG_NAME>/...` or `https://github.com/<ORG_NAME>/...`, so list all three.
If one is missing, remotes written in that form silently keep using the default `github.com` host and your personal key.

To see the form a remote is actually written in, read the raw value. `git remote get-url` shows the URL after rewriting:

```sh
git config --get remote.origin.url    # as written in .git/config
git remote get-url origin             # after insteadOf
```

### Keep includeIf last

Git reads config from top to bottom, and the last value it reads wins.
`includeIf` reads `~/.gitconfig-business` at the position where `includeIf` is written.
So if you put a setting below `includeIf`, such as your personal `user.signingkey`, it overrides the business one inside business repositories.

### Keep machine-specific settings out of your dotfiles

If you manage `~/.gitconfig` in a dotfiles repository, you may not want the business settings in it.
In that case, add the following at the end of `~/.gitconfig`, and put the `url` and `includeIf` blocks into `~/.gitconfig.local` instead:

```ini
[include]
  path = ~/.gitconfig.local
```

Git skips the file if it doesn't exist, so the same `~/.gitconfig` works on machines without a business account.

### GPG

If you use GPG to verify commits, add the following to `~/.gitconfig-business`, too:

```ini
[user]
  signingkey = <GPG_KEY_ID>
[gpg]
  program = gpg2
```

Use a GPG key whose email is your business email, and register it to your business account.
GitHub shows a commit as verified only when the email of the signing key matches the commit email and is verified on the account.

If `commit.gpgsign` is `true` for all repositories, set the business `signingkey` here even if you don't care about verification.
Otherwise, your business commits are signed with your personal key.

### Check it

Check it inside a business repository:

```sh
git config --show-origin user.email        # comes from ~/.gitconfig-business
git config --show-origin user.signingkey   # comes from ~/.gitconfig-business
git remote get-url origin                  # git@github-business:<ORG_NAME>/...
git ls-remote origin HEAD                  # authenticates as the business account
```

## Troubleshooting

### `user.email` comes from `.git/config`

A setting in the repository's own `.git/config` overrides `includeIf` in `~/.gitconfig`.
This happens when you once ran `git config user.email ...` without `--global` in that repository.
Remove it:

```sh
git config --local --unset user.name
git config --local --unset user.email
```

### Environment variables override the config

Git gives the following environment variables priority over every config file, including `includeIf`:

- `GIT_AUTHOR_NAME`, `GIT_AUTHOR_EMAIL`, `GIT_COMMITTER_NAME` and `GIT_COMMITTER_EMAIL` override `user.name` and `user.email`.
- `GIT_CONFIG_PARAMETERS` and `GIT_CONFIG_COUNT` override any key they set, such as `user.signingkey`.

If `git config` shows the business email but your commits still have the personal one, check them:

```sh
env | grep '^GIT_'
```

Remove them from your shell startup files (e.g. `~/.env` or `~/.bashrc`) or `.envrc`.

## GitHub CLI

`gh` supports multiple accounts. Run `gh auth login` for each account, and switch with `gh auth switch`.

The active account is global, though. To switch it per directory, you can use `.envrc` provided by [direnv](https://github.com/direnv/direnv):

*~/work/business/.envrc*

```sh
export GH_TOKEN=$(gh auth token --user <business-user>)
```

## VSCode

As of 29 Feb. 2024, VS Code doesn't seem to perfectly support multiple GitHub accounts although you can log into multiple accounts. It doesn't allow you to choose which account you use for which extension, so it's the safest to use only the main account, in most cases, your personal account.

See https://github.com/microsoft/vscode/issues/127967 for more details.

### Gitlens

Due to the issue above, you can't use [GitHub Pull Requests](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github) with multiple accounts.
However, if what you want to do is to open a PR by clicking `#<num>` in a commit message, you can achieve them with [Gitlens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens):

It's a bit tedious but the configuration varies depending on a workspace, you have to set the following for each workspace:

*/path/to/your/repo/.vscode/settings.json*

```json
{
  "gitlens.autolinks": [
    {
      "prefix": "#",
      "url": "https://github.com/<ORG_NAME>/<REPO_NAME>/pull/<num>",
      "alphanumeric": false
    }
  ]
}
```

Replace <ORG_NAME> and <REPO_NAME> accordingly.
