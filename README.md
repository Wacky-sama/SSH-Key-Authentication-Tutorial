# SSH Key Authentication Tutorial (Client - Server)

### Overview
SSH key authentication allows secure, password-less logins to remote servers. Instead of passwords, it uses a pair of cryptographic keys — public and private — for authentication.

### 1. Requirements
- Client: Computer you’ll connect from (e.g., laptop)
- Server: Computer you’ll connect to (e.g., Ubuntu Server)
- You already can log in via SSH with a username/password
- OpenSSH is installed on both client and server

### 2. Step 1 – Generate SSH Keys (Client)
On the client machine, run:
```bash
ssh-keygen
```
By default:
- OpenSSH now generates Ed25519 keys, which are secure, fast, and recommended.
- You can still specify another type (e.g., RSA) with -t, but Ed25519 is preferred.
When prompted:
- File to save key: Press Enter for default (~/.ssh/id_ed25519)
- Passphrase: Optional extra security
Result:
- Private key: ~/.ssh/id_ed25519 (keep secret, never share)
- Public key: ~/.ssh/id_ed25519.pub (safe to share with the server)

### 3. Step 2 - Copy Public Key to Server
Method 1 - Using ssh-copy-id:
```bash
ssh-copy-id username@server_ip
```
This appends your public key to the server’s ~/.ssh/authorized_keys.

Method 2 - Manual:
```bash
scp ~/.ssh/id_ed25519.pub username@server_ip:~/
ssh username@server_ip
mkdir -p ~/.ssh
cat ~/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
rm ~/id_ed25519.pub
```

### 4. Step 3 – Test the Connection
From the client:
```bash
ssh username@server_ip

```
If it logs in without a password, key authentication is working.

### 5. Step 4 - (Optional) Disable Password Authentication
On the server:
```bash
sudo nano /etc/ssh/sshd_config
```
Set the following:
```bash
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH:
```bash
sudo systemctl restart ssh
```
Warning: Make sure your key works first, or you could lock yourself out.

### 6. Step 5 – Use SSH Config for Convenience
Create/edit on the client:
```bash
nano ~/.ssh/config
```
Example config:
```bash
Host myserver
    HostName server_ip
    User username
    IdentityFile ~/.ssh/id_ed25519**
    IdentitiesOnly yes**
```

Now connect with:
```bash
ssh myserver
```

Note: If you are using only the default key (~/.ssh/id_ed25519) and have no other keys, you can omit 'IdentityFile' and 'IdentitiesOnly'. However, including them is recommended for clarity and to avoid issues if you add more keys in the future.

1. Generate keys with ssh-keygen
2. Copy public key to server (ssh-copy-id or manual)
3. Test login without a password
4. (Optional) Disable password authentication
5. Use SSH config for quick connections

### 8. Notes
- Keep your private key secure.
- Use a passphrase for extra protection.
- You can have multiple keys for different servers.