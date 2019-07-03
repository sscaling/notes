iptables
========

**NOTE:** will generally need `sudo`

-	`iptables -S` - List rules
-	`iptables-save -c > /path/to/iptables.rules && iptables -F` - backup iptables and clear
-	`iptables-restore < /path/to/iptables.rules` - Restore rules

---

References
----------

-	Oskar Andreasson's [tutorial](https://www.frozentux.net/iptables-tutorial/chunkyhtml/)
