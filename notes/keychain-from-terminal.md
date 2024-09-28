# Create and Access Keychain Secrets from the Terminal (on Mac)

Add a secret as follows:

```
security add-generic-password -a spather -s "Service Name" -w 'your password goes here'
```

Access it later with:
```
security find-generic-password -s "Service Name" -a spather -g
```

This will display a whole blob of info with the password as the last line. Interestingly, the password is output on std error. To extract just the password:

```
security find-generic-password -s "Service Name" -a spather -g 2>&1 | grep ^password | sed 's/.*"\(.*\)".*/\1/'
```
