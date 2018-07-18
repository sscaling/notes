Systemd
=======

Basic commands

**NOTE:** `.service` can generally be omitted

```
sudo systemctl start <name>.service
sudo systemctl status <name>.service
sudo systemctl stop <name>.service
sudo systemctl restart <name>.service
sudo systemctl reload <name>.service # If service supports reloading config
sudo systemctl enable <name>.service
sudo systemctl disable <name>.service

# List active units
systemctl list-units [--all] [--state=active|inactive|failed] [--type=service|device|mount|...]
# List ALL files, regardless if they were loaded or not
systemctl list-unit-files

# Display currently loaded unit file
systemctl cat <name>.service
systemctl list-dependencies <name>.service

# Masking / unmasking unit (making it unstartable)
systemctl [un]mask <name>.service

# Restore / reload unit files after updating
sudo systemctl daemon-reload
```

Editing unit files
------------------

```
# Edit override file
sudo systemctl edit <name>.service
# Edit original
sudo systemctl edit --full <name>.service
```

Create a new unit file
----------------------

```
touch /etc/systemd/system/name.service
chmod 664 /etc/systemd/system/name.service
** edit **
systemctl daemon-reload
systemctl start name.service
```
