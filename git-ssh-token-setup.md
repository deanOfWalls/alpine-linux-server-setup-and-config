
# Git SSH Token Setup

This document outlines the steps taken to set up SSH authentication for GitHub on the Alpine Linux server, including generating an SSH key specifically for GitHub and configuring Git to use it.

## Steps

### 1. Generate a New SSH Key for GitHub

Run the following command to generate a new SSH key pair, with a custom label for identification:

```bash
ssh-keygen -t rsa -b 4096 -C "deanofwalls@github"
```

- When prompted to save the key, specify a custom filename for easy identification, such as `/root/.ssh/id_rsa_github`.
- Optionally, enter a passphrase for added security.

### 2. Add the SSH Key to GitHub

1. Display the public key:
   ```bash
   cat /root/.ssh/id_rsa_github.pub
   ```

2. Copy the output of the public key.

3. Go to **GitHub** > **Settings** > **SSH and GPG keys** > **New SSH key**.

4. Give the key a descriptive title and paste the public key into the "Key" field.

5. Click **Add SSH key** to save.

### 3. Configure SSH to Use the New Key for GitHub

To ensure Git uses this specific SSH key for GitHub, create or edit the SSH configuration file:

```bash
nano /root/.ssh/config
```

Add the following lines to specify that this key should be used for GitHub:

```plaintext
Host github.com
    HostName github.com
    User git
    IdentityFile /root/.ssh/id_rsa_github
```

Save and exit the file.

### 4. Update the Git Remote URL to Use SSH

Run the following command to switch from HTTPS to SSH for the GitHub remote:

```bash
git remote set-url origin git@github.com:deanOfWalls/m93p-server-setup-and-config.git
```

### 5. Test the SSH Connection

Run this command to verify the SSH connection to GitHub:

```bash
ssh -T git@github.com
```

You should see a message like:

```plaintext
Hi deanOfWalls! You've successfully authenticated, but GitHub does not provide shell access.
```

### 6. Push Changes Using SSH

You can now push changes to GitHub without re-entering credentials:

```bash
git push -u origin master
```

---

## Summary

Using SSH keys for GitHub is generally more secure and convenient for server environments, as it avoids the need to enter a username and token every time. This document serves as a guide for setting up SSH authentication for GitHub.

