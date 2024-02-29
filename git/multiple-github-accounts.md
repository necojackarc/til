## Multiple GitHub Accounts

The easiest way is to set SSH hosts for each account.

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
