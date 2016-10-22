##Table of contents

- [Getting started with Discourse](#getting-started-with-discourse)
- [Working with Docker](#working-with-gopei)
  - [Switch to Discourse folder](switch-to-discourse-folder)
  - [Start the container](#start-the-container)
  - [Stop the container](#stop-the-container)
  - [Enter into the container](#enter-into-the-container)
  - [Rebuild the container](#rebuild-the-container)
  - [Set date inside container](#set-date-inside-container)
- [Backup your installation](#backup-your-installation)
- [Restore your backup](#restore-your-backup)
- [Localize your Discourse](#localize-your-discourse)
- [Customize your theme](#customize-your-theme)
  - [Color scheme](#color scheme)
  - [Custom CSS/HTML](#custom-css-html)
- [Troubleshooting](#troubleshooting)
- [Known issues](#known-issues)
- [Platform Specific Information](#platform-specific-information)
- [Release Specific Information](#release-specific-information)
- [Faq](#faq)
  - [Who should use?](#who-should-use)
- [References](#references)


## Getting started with Discourse

Use the original Discourse [documentation](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md). Short resume:

        sudo -s
        wget -qO- https://get.docker.com/ | sh
        sudo usermod -aG docker george
        mkdir /var/discourse
        git clone https://github.com/discourse/discourse_docker.git /var/discourse
        cd /var/discourse
        ./discourse-setup

## Working with Docker

#### Change to Discourse folder

Use `launcher` command to manage the container. Before switch to `/var/discourse` as root.

        sudo -s
        cd /var/discourse

#### Start the container

        ./launcher start app

#### Stop the container

        ./launcher stop app

#### Enter into the container

        ./launcher enter app

Note that you can't enter into container if is stopped.

#### Rebuild the container

        ./launcher rebuild app

This is useful if you want to add plugins or some capabilities to the platforma.

**Remember** that to do a [backup](#backup-your-installation) before rebuild. After that all your data will be lost and you will need to restore.

#### Set date inside container

By default the container have wrong time zone. Enter into the container and run:

        dpkg-reconfigure tzdata

#### Install some useful things into container

Enter into container and run:

        sudo apt-get install mc nano

Note that if you will need to reinstall those if you rebuild the container.

## Backup your installation

Do this from `Admin:Backups:Backup` section and download your `tar.gz` file somewhere. Keep safe this file. Do it periodically.

## Restore your backup

Activate restoring from `Admin:Settings:Backups:allow restore`. Now your restore option is available in `Admin:Backups:Restore`.

## Localize your Discourse

Follow the [documentation](https://meta.discourse.org/t/install-a-plugin/19157) for plugin instalation.

First you need to [switch to Discourse folder](#switch-to-discourse-folder) and change your `app.yml` file like that:

        ## Plugins go here
        ## see https://meta.discourse.org/t/19157 for details
        hooks:
          after_code:
            - exec:
                cd: $home/plugins
                cmd:
                  - git clone https://github.com/discourse/docker_manager.git
                cd: $home/config/locales
                cmd:
                  - wget https://raw.githubusercontent.com/discourse/discourse/master/config/locales/client.ro.yml
                  - wget https://raw.githubusercontent.com/discourse/discourse/master/config/locales/server.ro.yml

Save and [rebuild](#rebuild-the-container).

From the interface allow users to change their language from `Admin:Settings:Basic Setup:allow user locale`.

## Customize your theme

Making a new theme mean create a color scheme and some custom css.

## Color scheme

`Admin:Customize:Colors:New`

For ubuntu.ro theme use this:

        primary              000000
        secondary            ffffff
        tertiary             e95420
        quaternary           e95420
        header background    e95420
        header primary       ffffff
        highlight            e95420
        danger               e95420
        success              38b44a
        love                 fa6c8d

## Custom CSS/HTML 

`Admin:Customize:CSS/HTML`

For ubuntu.ro theme use this:

[CSS](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/css) [Header](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/header.html) [Top](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/top.html) [Footer](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/footer.html)

For mobiles use this:

[CSS](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/css-mobile) [Header](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/header-mobile.html) [Top](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/top-mobile.html) [Footer](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/footer-mobile.html)



## Known issues

For Ubuntu 16.04+ virtual machines remeber to disable standby otherwise you will have sporadic hangs.

        sudo nano /etc/default/grub

Change the following line

        GRUB_CMDLINE_LINUX_DEFAULT="consoleblank=0"

and update grub
 
        sudo update-grub




