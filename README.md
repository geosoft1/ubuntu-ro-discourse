[![version](https://img.shields.io/badge/version-1.0.1-orange.svg)](https://github.com/geosoft1/ubuntu-ro-discourse)
[![license](https://img.shields.io/badge/license-gpl-blue.svg)](https://github.com/geosoft1/ubuntu-ro-discourse/blob/master/LICENSE)

## Table of contents

- [Getting started with Discourse](#getting-started-with-discourse)
- [Working with Docker](#working-with-docker)
  - [Switch to Discourse folder](#switch-to-discourse-folder)
  - [Start the container](#start-the-container)
  - [Stop the container](#stop-the-container)
  - [Enter into the container](#enter-into-the-container)
  - [Rebuild the container](#rebuild-the-container)
  - [Set date inside container](#set-date-inside-container)
- [Backup your installation](#backup-your-installation)
- [Restore your backup](#restore-your-backup)
- [Localize your Discourse](#localize-your-discourse)
- [Customize your theme](#customize-your-theme)
  - [Logo images](#logo-images)
  - [Color scheme](#color-scheme)
  - [Custom CSS/HTML](#custom-csshtml)
  - [Set color scheme](#set-color-scheme)
- [app.yml](#app.yml)
- [Configure various authentications](#configure-various-authentications)
- [Remove image uploading in posts](#remove-image-uploading-in-posts)
- [Data migration](#data-migration)
- [Essential configurations](#essential-configurations)
- [Troubleshooting](#troubleshooting)
- [Known issues](#known-issues)
- [Platform Specific Information](#platform-specific-information)
- [Release Specific Information](#release-specific-information)
- [Faq](#faq)
  - [Who should use?](#who-should-use)
  - [Is Discourse safe for use?](#is-discourse-safe-for-use)
- [References](#references)


## Getting started with Discourse

Use the original Discourse [documentation](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md). Short resume:

        sudo -s
        wget -qO- https://get.docker.com/ | sh
        sudo usermod -aG docker $USER
        mkdir /var/discourse
        git clone https://github.com/discourse/discourse_docker.git /var/discourse
        cd /var/discourse
        ./discourse-setup

## Working with Docker

#### Switch to Discourse folder

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

        ./launcher rebuild app -y

This is useful if you want to add plugins or some capabilities to the platforma.

**Remember** that to do a [backup](#backup-your-installation) before rebuild. After that all your data will be lost and you will need to restore.

#### Set date inside container

By default the container have wrong time zone. [Enter into the container](#enter-into-the-container) and run:

        dpkg-reconfigure tzdata

#### Install some useful things into container

[Enter into the container](#enter-into-the-container) and run:

        sudo apt-get install mc nano

Note that if you will need to reinstall those if you rebuild the container.To avoid that do this in `app.yml`:

    ## Plugins go here
    ## see https://meta.discourse.org/t/19157 for details
    hooks:
      after_code:
        - exec:
            cd: $home
            cmd:
              - apt-get install mc nano -y


## Backup your installation

Do this from `Admin:Backups:Backup` section and download your `tar.gz` file somewhere. Keep safe this file. Do it periodically.

## Restore your backup

Activate restoring from `Admin:Settings:Backups:allow restore`. Now your restore option is available in `Admin:Backups:Restore`.

## Localize your Discourse

Follow the [documentation](https://meta.discourse.org/t/install-a-plugin/19157) for plugin instalation and [update](https://meta.discourse.org/t/how-to-update-translations-on-running-instance-of-discourse/14840/4) translations.

First you need to [switch to Discourse folder](#switch-to-discourse-folder) and change your `app.yml` file like that:

        ## Plugins go here
        ## see https://meta.discourse.org/t/19157 for details
        hooks:
          after_code:
            - exec:
                cd: $home/config/locales
                cmd:
                  - wget https://raw.githubusercontent.com/discourse/discourse/master/config/locales/client.ro.yml
                  - wget https://raw.githubusercontent.com/discourse/discourse/master/config/locales/server.ro.yml

Save and [rebuild](#rebuild-the-container).

From the interface allow users to change their language from `Admin:Settings:Basic Setup:allow user locale`.

## Customize your theme

Making a new theme mean create a color scheme and some custom css. Also some logo images may be needed.

## Logo images

Place logo images to `Staff:Media library` topic. Copy links to `Admin:Required` proper sections:

* [Forum logo](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/images/logo-forum.png) `Admin:Required:logo url`
* [Small logo](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/images/logo-ubuntu_cof-orange-hex.png) `Admin:Required:small logo url`
* [Mobile forum logo](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/images/logo-forum-mobile.png) `Admin:Required:mobile logo url`
* [Favicon image](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/images/favicon.png) `Admin:Required:favicon url`
* [Apple logo](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/images/logo-ubuntu_cof-orange-hex.png) `Admin:Required:apple touch icon url`

Note that some `.png` images are white on transparent so don't panic if you don't see it. Are there, just put it.

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
Edit `app.yml`

    ## Any custom commands to run after building
    run:
      - exec: wget -O /var/www/discourse/app/assets/stylesheets/common/foundation/colors.scss https://raw.githubusercontent.com/cracknel/discourse$


## Custom CSS/HTML 

`Admin:Customize:CSS/HTML`

For ubuntu.ro theme use this:

[CSS](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/css) [Header](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/header.html) [Top](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/top.html) [Footer](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/footer.html)
[/body](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/body.html)

For mobiles use this:

[CSS](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/css-mobile) [Header](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/header-mobile.html) [Top](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/top-mobile.html) [Footer](https://raw.githubusercontent.com/geosoft1/ubuntu-ro-discourse/master/theme/footer-mobile.html)

**Note** that mobile CSS are needed especially if you use small devices like cellphones.

## app.yml

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
            cd: $home
            cmd:
              - apt-get install mc nano -y
    
    ## Any custom commands to run after building
    run:
      - exec: echo "Beginning of custom commands"
      ## If you want to set the 'From' email address for your first registration, uncomment and change:
      ## After getting the first signup email, re-comment the line. It only needs to run once.
      #- exec: rails r "SiteSetting.notification_email='info@unconfigured.discourse.org'"
      - exec: wget -O /var/www/discourse/app/assets/stylesheets/common/foundation/colors.scss https://raw.githubusercontent.com/cracknel/discourse$
      - exec: echo "End of custom commands"

## Configure various authentications

* [Github](https://meta.discourse.org/t/configuring-github-login-for-discourse/13745)
* [Google](https://meta.discourse.org/t/configuring-google-login-for-discourse/15858)
* Launchpad
* [Twitter](https://meta.discourse.org/t/configuring-twitter-login-for-discourse/13395)
* Facebook

## Remove image uploading in posts

See [here](https://meta.discourse.org/t/remove-image-uploading-in-posts/23743/3). Go to `Admin:Settings:Files` and change `max image size kb` and `max attachment size kb` to 0, or clear out authorized extensions. In this case you will not allow avatars.

**A very good idea** is to allow upload png,jpg files with 120x120px (for avatars) use: `max attachment size kb:0`,`authorized extensions:[png,jpg]`,`max image width:120`,`max image height:120`

## Data migration

See [this](https://meta.discourse.org/t/importing-from-fluxbb/51322) topic.

## Essential configurations

Many settings are posible with Discourse but some are essential for the proper functioning.

Preferences:Admin

* Settings
  * Required
    * title,site description,contact email,contact url,notification email,site contact username
    * logo url,logo small url,mobile logo url,favicon url,apple touch icon url
    * company short name,company full name,company domain
  * Basic Setup
    * default locale:limba română
    * allow user locale [✓]
    * top menu:latest,new,unread,top,categories
    * category colors:
    [F1592A,BF1E2E,F7941D,9EB83B,3AB54A,12A89D,25AAE2]
    [0E76BD,652D90,92278F,ED207B,8C6238,231F20,808281]
    [B3B5B4,283890]
	* fixed category positions [✓]
  * Login
    * enable google oauth2 logins
    * enable twitter logins
    * enable github logins
  * Posting
    * min post length:10
    * min first post length:10
    * min topic title length:1
    * highlighted languages: add go :wink:
  * Files
    * If you don't want to allow files upload use: '`max image size kb:0`,`max attachment size kb:0`,`authorized extensions:[]`
    * To allow upload png,jpg files with 120x120px (for avatars) use: `max attachment size kb:0`,`authorized extensions:[png,jpg]`,`max image width:120`,`max image height:120`
    * Legal: link to CoC
  * Backups
    * allow restore [✓]
  * Tags
    * tagging enabled [✓]
  * Plugins
    * daily performance report [✓]

## Troubleshooting

If `.launcher rebuild app` fail, use

     service docker stop
     service docker start
     .launcher rebuild app
	

## Known issues

Ubuntu 16.04+ virtual machines ocasionally hang.

## Platform Specific Information

For Ubuntu 16.04+ virtual machines remeber to disable standby otherwise you will have sporadic hangs.

        sudo nano /etc/default/grub

Change the following line

        GRUB_CMDLINE_LINUX_DEFAULT="consoleblank=0"

and update grub
 
        sudo update-grub

## Release Specific Information

## Faq

#### Who should use?

This is a *How it's made* design for Ubuntu Romania Community but generally speaking can be used by enybody who want to make a forum based on Discourse.

#### Is Discourse safe for use?

They says yes. Read [this](https://github.com/discourse/discourse/blob/master/docs/SECURITY.md) document.

## References

[http://design.ubuntu.com](http://design.ubuntu.com)<br>
[http://design.ubuntu.com/brand/colour-palette](http://design.ubuntu.com/brand/colour-palette)<br>
[https://design.canonical.com/2016/04/ubuntu-orange-update](https://design.canonical.com/2016/04/ubuntu-orange-update)<br>
[https://design.canonical.com/2011/07/ubuntu-orange-is-dd4814](https://design.canonical.com/2011/07/ubuntu-orange-is-dd4814)<br>

[https://meta.discourse.org/t/importing-from-fluxbb/51322](https://meta.discourse.org/t/importing-from-fluxbb/51322)<br>

[https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md)<br>
[https://meta.discourse.org/t/install-a-plugin/19157](https://meta.discourse.org/t/install-a-plugin/19157)<br>
[https://meta.discourse.org/t/configuring-google-login-for-discourse/15858](https://meta.discourse.org/t/configuring-google-login-for-discourse/15858)<br>
[https://meta.discourse.org/t/configuring-github-login-for-discourse/13745](https://meta.discourse.org/t/configuring-github-login-for-discourse/13745)<br>
[https://meta.discourse.org/t/configuring-twitter-login-for-discourse/13395](https://meta.discourse.org/t/configuring-twitter-login-for-discourse/13395)<br>
[https://meta.discourse.org/t/how-to-update-translations-on-running-instance-of-discourse/14840/5](https://meta.discourse.org/t/how-to-update-translations-on-running-instance-of-discourse/14840/5)<br>
[https://meta.discourse.org/t/remove-image-uploading-in-posts/23743/4](https://meta.discourse.org/t/remove-image-uploading-in-posts/23743/4)<br>
[https://github.com/discourse/discourse/blob/master/docs/SECURITY.md](https://github.com/discourse/discourse/blob/master/docs/SECURITY.md)<br>
[https://meta.discourse.org/t/best-way-to-customize-top-menu/35336/3](https://meta.discourse.org/t/best-way-to-customize-top-menu/35336/3)<br>

