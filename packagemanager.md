---
id: zr2q6xdf3950vpzaikvpr3l
title: Package Management
desc: ""
updated: 1655272486711
created: 1651128945725
tags:
  - package-management
  - software-distribution
---

# RPM

* [Building RPM packages with rpmbuild](https://blog.packagecloud.io/building-rpm-packages-with-rpmbuild/)
* [HOWTO: GPG sign and verify RPM packages and yum repositories](https://blog.packagecloud.io/how-to-gpg-sign-and-verify-rpm-packages-and-yum-repositories/)

# AppImage

## Ubuntu Menu

Как добавить `.appimage` как программу в меню запуска: https://dev.to/msamgan/how-to-add-appimage-application-to-menu-in-ubuntu-linux-400k

_~/.local/share/applications/appName.desktop_:

```
[Desktop Entry]
Version=0.13.23
Type=Application
Name=appName
Comment=Application Description
TryExec=Path/to/AppImage
Exec=Path/to/AppImage
Icon=Path/to/AppImage.icon
Actions=Editor
```

If you want this AppImage to be accessible to to all the user of the system, place the .desktop file in _/usr/share/applications_.
