# Install SSH Key

This reusable GitHub Action installs a private SSH key onto an Ubuntu runner.

> [!CAUTION]  
> As this action processes sensitive information, please review the action steps
> in [`install/action.yml`](https://github.com/rainstormy-actions/ssh-key/blob/main/install/action.yml)
> prior to using the action.

## Usage
```yaml
# .github/workflows/example.yml
jobs:
  example:
    runs-on: ubuntu-22.04
    timeout-minutes: 1
    steps:
      - name: Install an SSH key
        uses: rainstormy-actions/ssh-key/install@v1
        with:
          known-hosts: ${{ secrets.KNOWN_HOSTS }}
          __private-key__: ${{ secrets.PRIVATE_SSH_KEY }}
          # key-name: "id_ed25519"
```

### Options
#### `known-hosts`
A newline-separated list of trusted remote host keys to be stored
in `.ssh/known_hosts`.

#### `__private-key__`
The private SSH key to be stored in `.ssh/<key-name>` and added to the SSH agent
on the GitHub Actions runner.

#### `key-name` (optional)
The name of the file in the `.ssh` directory that stores the value
of `__private-key__`.

Default value: `id_ed25519`

### Generate an SSH key
Use [1Password](https://developer.1password.com/docs/ssh/manage-keys)
or [ssh-keygen](https://manpages.ubuntu.com/manpages/noble/man1/ssh-keygen.1.html)
(OpenSSH) to generate a public-private SSH key pair.

#### Example
1. Generate a public-private SSH key pair with the _Ed25519_ algorithm:
   ```shell
   ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
   ```

2. Copy the contents of the private SSH key, `~/.ssh/id_ed25519`, in a secure
   manner.

3. [Define](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)
   a secret (e.g. named `PRIVATE_SSH_KEY`) in GitHub Actions to contain the
   private SSH key:
   ```
   -----BEGIN OPENSSH PRIVATE KEY-----
   (...)
   -----END OPENSSH PRIVATE KEY-----
   ```

4. [Access](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#using-secrets-in-a-workflow)
   the secret in the workflow:
   ```yaml
   with:
     __private-key__: ${{ secrets.PRIVATE_SSH_KEY }}
   ```

### Obtain trusted remote host keys
Use [ssh-keyscan](https://manpages.ubuntu.com/manpages/noble/man1/ssh-keyscan.1.html)
(OpenSSH) to gather public SSH keys from remote servers.

#### Example
1. Gather the public SSH keys for `github.com`:
   ```shell
   ssh-keyscan -t ed25519 github.com
   ssh-keyscan -t ecdsa github.com
   ssh-keyscan -t rsa github.com
   ```

2. [Verify](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints)
   the authenticity of `github.com` by comparing the result to the public SSH
   keys published by GitHub.

3. [Define](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)
   a secret (e.g. named `KNOWN_HOSTS`) in GitHub Actions to contain a newline
   separated list of the public SSH keys:
   ```
   github.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0SdG6UOoqKLsabgH5C9okWi0dh2l9GKJl
   github.com ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEmKSENjQEezOmxkZMy7opKgwFB9nkt5YRrYMjNuG5N87uRgg6CLrbo5wAdT/y6v0mKV0U2w0WZ2YB/++Tpockg=
   github.com ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCj7ndNxQowgcQnjshcLrqPEiiphnt+VTTvDP6mHBL9j1aNUkY4Ue1gvwnGLVlOhGeYrnZaMgRK6+PKCUXaDbC7qtbW8gIkhL7aGCsOr/C56SJMy/BCZfxd1nWzAOxSDPgVsmerOBYfNqltV9/hWCqBywINIR+5dIg6JTJ72pcEpEjcYgXkE2YEFXV1JHnsKgbLWNlhScqb2UmyRkQyytRLtL+38TGxkxCflmO+5Z8CSSNY7GidjMIZ7Q4zMjA2n1nGrlTDkzwDCsw+wqFPGQA179cnfGWOWRVruj16z6XyvxvjJwbz0wQZ75XK5tKSb7FNyeIEs4TT4jk+S4dhPeAUC5y+bDYirYgM4GC7uEnztnZyaVWQ7B381AK4Qdrwt51ZqExKbQpTUNn+EjqoTwvqNj4kqx5QUCI0ThS/YkOxJCXmPUWZbhjpCg56i+2aB6CmK2JGhn57K5mj0MNdBXA4/WnwH6XoPWJzK5Nyu2zB3nAZp+S5hpQs+p1vN1/wsjk=
   ```

4. [Access](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#using-secrets-in-a-workflow)
   the secret in the workflow:
   ```yaml
   with:
     known-hosts: ${{ secrets.KNOWN_HOSTS }}
   ```
