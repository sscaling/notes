# GPG

```
$ brew install gpg

# Check current status
$ gpg --list-secret-keys --keyid-format LONG

# Add a new key
$ gpg --full-generate-key
$ gpg --list-secret-keys --keyid-format LONG
----------------------------------
sec   rsa4096/<public key id> 2018-03-15 [SC]
... snip ...

# NOTE: <public key id> is what generally needs to be configured in web apps (i.e. github)

# Export as ASCII armor format
$ gpg --armor --export <key id>

# Move to another machine
# (source)
gpg --export --armor --output=key_public.asc
gpg --export-secret-keys --armor --output=key_secret.asc

# (target)
gpg --import --armor key_public.asc
gpg --import --armor key_secret.asc
 ```
