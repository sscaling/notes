Adding a new user
-----------------

Users use the crypt(3) function for basic HTTP auth.

To add a user, use the `mkpasswd` function (frontend for crypt) [ref](https://blog.sleeplessbeastie.eu/2018/03/08/how-to-define-basic-authentication-on-haproxy/)

```
$ printf "password" | mkpasswd --stdin --method=sha-512
```

Then to the `userlist` add

```
  user <username> password <output of mkpasswd>
```
