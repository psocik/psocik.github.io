If you would like to see if there were attempts to login and when to local account in windows 10/11 windows has built in feature for that. To enable this feature, you will have to setup the **Windows Registry**, so Run *regedit* and navigate to the following key:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```

If key **DisplayLastLogonInfo** is there set it to **1**. If not create new DWORD (32-bit).with the value **1**.

Restart your computer.

Code for *key.reg*:

```INI
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System]
"DisplayLastLogonInfo"=dword:00000001

```

If you would like to disable this feature just do the same thing, but set value of **DisplayLastLogonInfo** to 0.

Restart your computer.

Have a nice monitoring :)
