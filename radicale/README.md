# Home Assistant Community(someday?) Add-on: Radicale v{{ version.split('-')[0]  }}

[![Release][release-shield]][release] ![Project Stage][project-stage-shield] ![Project Maintenance][maintenance-shield]

[![Discord][discord-shield]][discord] [![Community Forum][forum-shield]][forum]

Radicale add-on made by me, for me, for now. But feel free to give it a spin :)

## About

This add-on runs [Radicale][radicale] - an python calendar server.

Some comments on the configuration:

* `credentials` is a list of objects with keys `username` and `password`.
* `encryption_method` is one of (plain|md5|bcrypt) if anything else than
  plain is configured the `password` key in `credentials` has to be 
  encrypted in the choosen format. A litle more on that below.
* `rights_type` can be one of (authenticated|owner_only|owner_write|from_file)
  __owner_only__ being the default. If set to __from_file__ the option
  `rights_file` has to be filled out.
* `rights_file` only has effect if `rights_type` is set to __from_file__.
   It is a list of objects with the keys `section`, `user`, `collection`
   and `permissions`. they are transformed into radicals rights_file
   format:

   \[`section`\]
   user: `user`
   collection: `collection`
   permissions: `permissions`

   [See more on the format here][radicale_rigts]
* `log_level` takes python valid log levels. see the [logging module][python_logging] for
  more information.


## A little more on password encryption.

### `encryption_method` __md5__
```bash
$ openssl passwd -apr1 -salt $(tr -dc ./[:alnum:] </dev/urandom | head -c8)
Password:
$apr1$e/P2LXL6$ftPekvI2mLieC9D2E3EMp.
$
```

### `encryption_method` __bcrypt__
bcrypt needs to be installed. It is not part of the standard library
```python
>>> import bcrypt
>>> bcrypt.hashpw(b"password", bcrypt.gensalt())
b'$2b$12$cW1uOHNRxdkgqq15FAdMzOu/FToDBqCEJLUqIi1zH5bQlYfwFkoO2'
>>>
```

## Example config

```json
{
    "credentials": [
        {"username": "user1", "password": "$apr1$e/P2LXL6$ftPekvI2mLieC9D2E3EMp."}
    ],
    "rights_type": "from_file",
    "rights_file": [
        {
            "section": "root",
            "user": ".+",
            "collection": "",
            "permissions": "R"
        },
        {
            "section": "principal",
            "user": ".+",
            "collection": "{user}",
            "permissions": "RW"
        },
        {
            "section": "calendars",
            "user": ".+",
            "collection": "{user}/[^/]+",
            "permissions": "rw"
        }
    ],
    "log_level": "info",
    "encryption_method": "md5"
}
```

The `rights_file` will be converted to the radicale format

```ini
[root]
user = .+
collection = 
permissions = R

[principal]
user = .+
collection = {user}
permissions = RW

[calendars]
user = .+
collection = {user}/[^/]+
permissions = rw
```

Read more on the format [here][radicale_rights].

[Check out the repository on github][github]

{% if channel == "edge" %}
## WARNING! THIS IS AN EDGE VERSION!

This Home Assistant Add-ons repository contains edge builds of add-ons.
Edge builds add-ons are based upon the latest development version.

- They may not work at all.
- They might stop working at any time.
- They could have a negative impact on your system.

This repository was created for:

- Anybody willing to test.
- Anybody interested in trying out upcoming add-ons or add-on features.
- Developers.

If you are more interested in stable releases of our add-ons:

<https://github.com/hassio-addons/repository>

{% endif %}
{% if channel == "beta" %}
## WARNING! THIS IS A BETA VERSION!

This Home Assistant Add-ons repository contains beta releases of add-ons.

- They might stop working at any time.
- They could have a negative impact on your system.

This repository was created for:

- Anybody willing to test.
- Anybody interested in trying out upcoming add-ons or add-on features.

If you are more interested in stable releases of our add-ons:

<https://github.com/hassio-addons/repository>

{% endif %}

[radicale]: https://radicale.org/3.0.html
[radicale_rights]: https://radicale.org/3.0.html#documentation/authentication-and-rights
[python_logging]: https://docs.python.org/3/library/logging.html#logging-levels
[discord-shield]: https://img.shields.io/discord/478094546522079232.svg
[discord]: https://discord.me/hassioaddons
[github]: {{ url }}
[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg
[forum]: https://community.home-assistant.io
[maintenance-shield]: https://img.shields.io/maintenance/yes/2020.svg
[project-stage-shield]: https://img.shields.io/badge/project%20stage-production%20ready-brightgreen.svg
[release-shield]: https://img.shields.io/badge/version-{{ version }}-blue.svg
[release]: {{ url }}/tree/{{ version }}