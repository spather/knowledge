# List devices
```bash
adb devices
```

# Apply a command to a specific device
Needed if more than one device is connected

```bash
adb -s deviceid_from_adb_devices ... rest of command ...
```

# Get screenshot from device
```bash
adb exec-out screencap -p > /path/to/output.png
```

# Install apk 
```bash
adb install /path/to/apk.apk
```

# Redirect a port on the phone to a port on the computer
```bash
adb reverse tcp:8081 tcp:8081
```

