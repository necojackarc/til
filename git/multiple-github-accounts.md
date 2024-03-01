## Multiple GitHub Accounts

The easiest way is to set SSH hosts for each account.

## SSH Config

Let's say you have one personal account and one business account.
You can have the following settings in your `~/.ssh/config`:

```ssh
Host github.com
  User git
  Hostname github.com
  Identityfile ~/.ssh/github/id_ed25519

Host github-business.com
  User git
  Hostname github.com
  Identityfile ~/.ssh/github-business/id_ed25519
```

Then, instead of using `github.com`, you simply need to use `github-business` as your hostname for your business account.

Use your personal GitHub account everywhere like VS Code if your default account is your personal one, then everything should be fine!

## Git CLI

You can use `.envrc` provided by [direnv](https://github.com/direnv/direnv):

```sh
export GIT_AUTHOR_EMAIL="<Email>"
export GIT_AUTHOR_NAME="<Name>"
export GIT_COMMITTER_EMAIL="<Email>"
export GIT_COMMITTER_NAME="<Name>"
```

If you use GPG to verify commits, you'd need to add the following, too:

```sh
export GIT_CONFIG_PARAMETERS="'user.signingkey=<GPG_KEY_ID>' 'gpg.program=gpg2'"
```

## VSCode

As of 29 Feb. 2024, VS Code doesn't seem to perfectly support multiple GitHub accounts although you can log into multiple accounts. It doesn't allow you to use which account you use for which extension, to it's the safest to use only the main account, in most cases, your personal account.

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
