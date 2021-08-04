# Copy an SSH public key to another server

Use
```
ssh-copy-id -i ~/.ssh/some_key.pub user@remote-host
```

Or

```
cat ~/.ssh/some_key.pub | ssh user@remote-host 'cat >> ~/.ssh/authorized_keys'
```