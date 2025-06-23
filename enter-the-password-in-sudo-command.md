---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Enter the password in sudo command

Yes, use the `-S` switch which reads the password from STDIN:

```
$echo <password> | sudo -S <command>
```

So for your case it would look like this:

```
$./configure && make && echo <password> | sudo -S make install && halt
```

of course, replace `<password>` with your password.
