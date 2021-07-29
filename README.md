# nethserver-self-service-password

[Self Service Password](https://ltb-project.org/documentation/self-service-password) (SSP) is a PHP application that allows users to change their passwords in a LDAP directory.  This package integrates SSP into Nethserver.

## Installation

You'll first need to install my repo using `yum install https://repo.familybrown.org/nethserver/7/noarch/nethserver-danb35-1.0.0-6.ns7.noarch.rpm`

You'll then need to manually create the repo file for self-service password itself.  Run `nano /etc/yum.repos.d/ltb-project.repo` (or use your text editor of choice).  Its contents should be:

```
[ltb-project-noarch]
name=LTB project packages (noarch)
baseurl=https://ltb-project.org/rpm/$releasever/noarch
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-LTB-project
```

Next, import the signing GPG key using `rpm --import https://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project`.

Finally, install the packages using `yum --enablerepo=danb35 install nethserver-self-service-password`.

## Configuration Variables

By default, the password change page will be available at `https://neth_fqdn/ssp`.  You can configure this module using the following database properties:

| Property | Default | Description |
| --- | --- | --- |
| UseDefaultHost | enabled | Make self-service-password available on the default virtual host. To disable, set to **disabled**. |
| DefaultPath | /ssp | Path to self-service-password on the default virtual host (if enabled). Must include the leading slash. |
| UseVhost | disabled | Use a dedicated virtual host for self-service-password. Set to **enabled** to enable. |
| VHostName | ssp.$DomainName | If using a dedicated virtual host, the fully-qualified domain name for that virtual host (e.g., password.yourdomain.com). |
| UseEmail | false | Enable password resets by email token. This will allow users who have forgotten their passwords to email a reset token to their local email address. On clicking that link, they'll be able to reset their password. To enable, set to true. **This option is unlikely to work in an Active Directory environment.** |

Use the standard configuration database commands to change these settings, _e.g._, `config setprop ssp UseEmail true`. After making any changes, run `signal-event nethserver-self-service-password-update`. 

