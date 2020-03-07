# How it works #

This is a kiosk builder system. It can be used to make a system that
single graphical program. This is useful for making systems that do
video conferencing, digital signage, informational displays, Internet
kiosks, and more. Right now, only Raspberry Pi 0-4 are supported.

## custom.json format ##

custom.json should look something like this:

``` json
{
    "hostName": "kiosk",
    "hardware": "raspberryPi0",
    "authorizedKeys": ["ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC050iPG8ckY/dj2O3ol20G2lTdr7ERFz4LD3R4yqoT5W0THjNFdCqavvduCIAtF1Xx/OmTISblnGKf10rYLNzDdyMMFy7tUSiC7/T37EW0s+EFGhS9yOcjCVvHYwgnGZCF4ec33toE8Htq2UKBVgtE0PMwPAyCGYhFxFLYN8J8/xnMNGqNE6iTGbK5qb4yg3rwyrKMXLNGVNsPVcMfdyk3xqUilDp4U7HHQpqX0wKrUvrBZ87LnO9z3X/QIRVQhS5GqnIjRYe4L9yxZtTjW5HdwIq1jcvZc/1Uu7bkMh3gkCwbrpmudSGpdUlyEreaHOJf3XH4psr6IMGVJvxnGiV9 mbauer@dellbook"],
    "program": {
        "package": "epiphany",
        "path": "/bin/epiphany"
    },
    "networks": {
        "my-router": "0000000000000000000000000000000000000000000000000000000000000000",
    },
    "locale": {
        "timeZone": "America/New_York",
        "country": "US"
    },
    "localSystem": {
        "system": "x86_64-linux",
        "sshUser": "me",
        "hostName": "my-laptop-host",
    }
}
```

authorizedKeys must be included to allow changing the system! If you
lose the private key, you will be locked out and have to reflash your
system, so keep them safe.

## Push to deploy ##

By default, Basalt is set up to enable push-to-deploy. This allows you
to make changes to this repo and rebuild the system. Unfortunately,
this requires setting up a remote builder which is kind of difficult
to do. Some steps are as follows:

### Remote builder ###

Before starting, you need to make sure your custom.json has the
correct values for your local computer under localSystem. This should
be a hostname that the Kiosk will be able to access. For this to work,
you also need to be a trusted-user on your local system.

First, we need to give the Kiosk SSH access:

``` sh
$ echo $(ssh root@kiosk.local cat '$HOME'/.ssh/id_rsa.pub) >> $HOME/.ssh/authorized_keys
```

Then, we need to test that we can access the local computer through
SSH:

``` sh
$ ssh root@kiosk.local
$ ssh me@my-laptop-host
```

If all is well, then we can proceed to cloning the configuration.

### Cloning ###

Once you have a remote builder configure on your kiosk, you can clone
your Kiosk repo:

``` sh
$ git clone ssh://root@kiosk.local/etc/nixos/configuration.git kiosk-configuration
```

From here, you can make some changes, and commit them to the repo.
When done, you can just do:

``` sh
$ git push
```

and read the output of the new deployment.
