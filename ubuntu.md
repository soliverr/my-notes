---
id: kq7mfnx14pscuplyvg056jy
title: Ubuntu
desc: ''
updated: 1709306166837
created: 1654683054069
---

# Список версий дистрибутивов

https://wiki.ubuntu.com/Releases


# Ubuntu 22.04 (Jammy)

## Не работает шаринг экранов ни в одной программе

[Screen Share Not working in Ubuntu 22.04 (In all platforms zoom, teams, google meet, anydesk , etc.,)](https://askubuntu.com/questions/1407494/screen-share-not-working-in-ubuntu-22-04-in-all-platforms-zoom-teams-google-m)

```sh
$ echo $XDG_SESSION_TYPE
wayland
```

Решение:

```
sudo nano /etc/gdm3/custom.conf
# Uncomment this line.
# WaylandEnable=false
```

```
sudo sed -i -e 's/#WaylandEnable=false/WaylandEnable=false/' /etc/gdm3/custom.conf
sudo systemctl reboot
```

После перезагрузки:

```sh
$ echo $XDG_SESSION_TYPE
x11
```

## Key is stored in legacy trusted.gpg keyring

* https://askubuntu.com/a/1398346/1037999
* https://askubuntu.com/questions/1403556/key-is-stored-in-legacy-trusted-gpg-keyring-after-ubuntu-22-04-update

```
$ man apt-key

DEPRECATION
       Except for using apt-key del in maintainer scripts, the use of apt-key is deprecated. This section shows how to replace existing use of apt-key.

       If your existing use of apt-key add looks like this:

       wget -qO- https://myrepo.example/myrepo.asc | sudo apt-key add -

       Then you can directly replace this with (though note the recommendation below):

       wget -qO- https://myrepo.example/myrepo.asc | sudo tee /etc/apt/trusted.gpg.d/myrepo.asc

       Make sure to use the "asc" extension for ASCII armored keys and the "gpg" extension for the binary OpenPGP format (also known as "GPG key public ring"). The binary OpenPGP format works for all apt
       versions, while the ASCII armored format works for apt version >= 1.4.

       Recommended: Instead of placing keys into the /etc/apt/trusted.gpg.d directory, you can place them anywhere on your filesystem by using the Signed-By option in your sources.list and pointing to the
       filename of the key. See sources.list(5) for details. Since APT 2.4, /etc/apt/keyrings is provided as the recommended location for keys not managed by packages. When using a deb822-style
       sources.list, and with apt version >= 2.4, the Signed-By option can also be used to include the full ASCII armored keyring directly in the sources.list without an additional file.
```

## Проблемы запуска Python в VS Code из snap

```$ python3
/usr/bin/python3: symbol lookup error: /snap/core20/current/lib/x86_64-linux-gnu/libpthread.so.0: undefined symbol: __libc_pthread_init, version GLIBC_PRIVATE

$ set | grep GTK
GTK_EXE_PREFIX=/snap/code/126/usr
GTK_IM_MODULE_FILE=/home/oliver/snap/code/common/.cache/immodules/immodules.cache
GTK_MODULES=gail:atk-bridge
GTK_PATH=/snap/code/126/usr/lib/x86_64-linux-gnu/gtk-3.0
GTK_USE_PORTAL=1
```

* https://askubuntu.com/questions/1462295/ubuntu-22-04-both-eye-of-gnome-and-gimp-failing-with-undefined-symbol-error/1462494#1462494

Установка `gtk-4` не помогает, нужно убрать переменную `GTK_PATH`:

> run "Preferences: Open User Settings (JSON)" and add this to your settings.json: 
> 
>```
>"terminal.integrated.env.linux": {
>    "GTK_PATH": ""
>}
>```

## Аутентификатор из Flatpak

* https://gitlab.gnome.org/World/Authenticator

Не видит камеру после установки:

```sh
$ flatpak run com.belmoussaoui.Authenticator
+ flatpak run com.belmoussaoui.Authenticator
2023-05-03T09:07:48.163565Z  INFO authenticator::application: Authenticator (com.belmoussaoui.Authenticator)
2023-05-03T09:07:48.163581Z  INFO authenticator::application: Version: 4.3.1 ()
2023-05-03T09:07:48.163584Z  INFO authenticator::application: Datadir: /app/share/authenticator
2023-05-03T09:07:48.169354Z DEBUG oo7::keyring: Application is sandboxed, using the file backend
2023-05-03T09:07:48.169381Z DEBUG oo7::portal: Loading default keyring file
2023-05-03T09:07:48.170549Z DEBUG oo7::portal::secret: Retrieve service key using org.freedesktop.portal.Secrets
2023-05-03T09:07:48.170581Z DEBUG oo7::portal::secret: Creating a '/org/freedesktop/portal/desktop/request/1_650/oo7_OBnkqra4iB' proxy and listening for a response
2023-05-03T09:07:48.172075Z DEBUG oo7::keyring: org.freedesktop.portal.Secrets is not available, falling back to the Sercret Service backend
2023-05-03T09:07:48.173156Z DEBUG oo7::dbus::service: Starting an encrypted Secret Service session
2023-05-03T09:07:48.180425Z  INFO authenticator::models::providers: Loading providers
2023-05-03T09:07:48.181099Z  INFO authenticator::models::database: Running DB Migrations...
2023-05-03T09:07:48.181276Z  INFO authenticator::models::database: Database pool initialized.
2023-05-03T09:07:57.504326Z  INFO authenticator::widgets::camera: The camera state changed: Not Found
2023-05-03T09:08:07.933799Z  INFO ashpd::desktop::request: Creating a org.freedesktop.portal.Request /org/freedesktop/portal/desktop/request/1_656/ashpd_Z3ju5mgN51
2023-05-03T09:08:07.942414Z ERROR authenticator::widgets::camera: Failed to stream ZBus Error: org.freedesktop.DBus.Error.ServiceUnknown: The name org.freedesktop.portal.Desktop was not provided by any .service files
```

Установить пакет:

```sh
xdg-desktop-portal/jammy-updates,now 1.14.4-1ubuntu2~22.04.1 amd64 [остались файлы настроек]
  desktop integration portal for Flatpak and Snap


$ sudo apt install xdg-desktop-portal
```

Не может импортировать аккаутны из Google Authenticator

# makedeb

https://www.makedeb.org/

```
bash -ci "$(wget -qO - 'https://shlink.makedeb.org/install')"

$ gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 4D64390375060AA4
$ gpg --armor --export  4D64390375060AA4 | sudo tee /etc/apt/trusted.gpg.d/kubic.asc
$ sudo apt update

```