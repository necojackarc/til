# AWS Switch Roles

To switch roles with AWS CLI, it seems like [granted](https://www.granted.dev/) is the best tool available in 2024.

It supports SSO and API credentials, too.

You can install it via [mise](https://mise.jdx.dev/).

```sh
# Install AWS CLI via mise
mise install awscli
mise use -g awscli@<LATEST_VERSION>

# Install Granted via mise
mise install granted
mise use -g granted@<LATEST_VERSION>
```

Then add `alias assume='source $(mise which assume)'`. See [Shell alias](https://docs.commonfate.io/granted/internals/shell-alias) for more details.
