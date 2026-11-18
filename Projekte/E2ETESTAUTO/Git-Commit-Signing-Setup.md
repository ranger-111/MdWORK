# Git Commit Signing Setup

The SAP enterprise enforces **signed commits** on all repositories in `ecs-e2e-testing`. Without signing configured, every push will be rejected with:

```
push declined due to repository rule violations
Commits must have verified signatures.
```

## Prerequisites

- Git 2.34+
- An SSH key (ed25519 recommended). Check if you already have one: `ls ~/.ssh/id_ed25519.pub`
- If not, generate one: `ssh-keygen -t ed25519 -C "your-i-number"`

## Setup (one-time)

### 1. Configure git to use SSH signing

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

### 2. Register your SSH key as a signing key on GitHub

> This is separate from your authentication key — you need to add it explicitly as a **signing key**.

1. Copy your public key: `cat ~/.ssh/id_ed25519.pub`
2. Go to [https://github.tools.sap/settings/ssh/new](https://github.tools.sap/settings/ssh/new)
3. Set **Key type** to **Signing Key**
4. Paste the key and save

### 3. Add your key to the SSH agent (macOS)

If your key has a passphrase, add it to the keychain so you are not prompted on every commit:

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

## Verify

Make a test commit and push to any branch:

```bash
git commit --allow-empty -m "test: signing check"
git push origin <your-branch>
```

If it goes through without a `rule violations` error, signing is working.

## Existing commits on a branch are not signed

If you already have unsigned commits on a branch that you cannot push, re-sign them:

```bash
git rebase --exec 'git commit --amend --no-edit -S' origin/main
```

Then force-push:

```bash
git push --force-with-lease origin <your-branch>
```
