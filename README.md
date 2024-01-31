```
👩  This project is part of the passbolt "lab"!
⚗️   It is used to illustrate an article or as a conversation starter.
🧪  Use at your own risks!
```

## Copyright & License

(c) 2021 Passbolt SA

Passbolt is registered trademark of Passbolt S.A.

MIT No Attribution - https://opensource.org/licenses/MIT-0

# Ansible Collection - anatomicjc.passbolt

This is the source repository for [https://galaxy.ansible.com/anatomicjc/passbolt](https://galaxy.ansible.com/anatomicjc/passbolt)

## Passbolt lookup plugin / passbolt_inventory plugin

### passbolt

This lookup plugin allows you to retrieve ansible secrets from [passbolt](https://www.passbolt.com)

It is based on [py-passbolt library](https://pypi.org/project/py-passbolt/).

### passbolt_inventory

This Plugin allows to you to retrieve all [passbolt](https://www.passbolt.com) resources the user has access to. You can use the filter anatomicjc.passbolt.check_naming to check if all resources are compliant with your internal naming guideline.

It is based on [py-passbolt library](https://pypi.org/project/py-passbolt/).

### Environment variables for configuration

| environment variable name                    | Description                                                                                                          | Comments                                |
|----------------------------------------------|----------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
| PASSBOLT_BASE_URL                            | Your passbolt instance url: https://passbolt.domain.tld                                                              | Mandatory                               |
| PASSBOLT_PRIVATE_KEY                         | Your passbolt private key in one-line format (see the below note)                                                    | PGPy backend                            |
| PASSBOLT_PASSPHRASE                          | Your passbolt private key passphrase                                                                                 | PGPy backend                            |
| PASSBOLT_GPG_BINARY                          | Path to gpg binary                                                                                                   | gnupg backend                           |
| PASSBOLT_GPG_LIBRARY                         | The python library to use                                                                                            | Allowed values: PGPy (default) or gnupg |
| PASSBOLT_FINGERPRINT                         | The key fingerprint to use for gnupg backend                                                                         | gnupg backend                           |
| PASSBOLT_VERIFY                              | The path of custom certificate authority                                                                             | exemple:  /etc/ipa/ca.crt default: True |
| PASSBOLT_CREATE_NEW_RESOURCE                 | Create a new passbolt resource if no resource found (disabled by default)                                            | true / false (default)                  |
| PASSBOLT_NEW_RESOURCE_PASSWORD_LENGTH        | Password length when creating a new resource                                                                         | Default value: 20                       |
| PASSBOLT_NEW_RESOURCE_PASSWORD_SPECIAL_CHARS | The plugin creates only alphanumerics characters by default. Set this variable to true to include special characters | true / false (default)                  |

The environment variables are overridable by variable or the --extra-vars option 

**Note**: How to set OpenPGP key in one-line format:

Linux:

```
sed -z 's/\n/\\n/g' private.asc
```

MacOS:

Install `gnu-sed` with brew:

```
$ brew install gnu-sed
```

Use gsed instead of sed:

```
gsed -z 's/\n/\\n/g' private.asc
```

## Use this plugin with AWX

Build a custom AWX Execution Environment using [Ansible Builder](https://ansible.readthedocs.io/projects/builder/en/latest/)

Add to requirements.txt:

```
py-passbolt
```

Add to requirements.txt:

```
collections:
    - name: anatomicjc.passbolt
```

Add a new Custom Credential Type to AWX:

* Name: Passbolt Credentials
* Description: Passbolt credentials for accessing Passbolt
* Configuration input:

```
fields:
  - id: passbolt_url
    type: string
    label: Passbolt Base URL
  - id: passbolt_private_key
    type: string
    label: Passbolt Private GPG Key
    secret: true
    multiline: true
  - id: passbolt_passphrase
    type: string
    label: Passbolt Private GPG Key Passphrase
    secret: true
required:
  - passbolt_url
  - passbolt_private_key
  - passbolt_passphrase
```

* Configuration injector:

```
env:
  PASSBOLT_BASE_URL: '{{ passbolt_url }}'
  PASSBOLT_PASSPHRASE: '{{ passbolt_passphrase }}'
  PASSBOLT_PRIVATE_KEY: '{{ passbolt_private_key }}'
```

* Add a new credential of the type Passbolt Credentials to AWX:
  * Set the url and passphrase
  * Upload or paste the contents of the Private key file into the Passbolt Private GPG Key field without any modifications
* Create or update an AWX template to use the custom EE and add the above defined Passbolt Credentails. The playbook executed by this template will now have access to passbolt using the lookup plugin.

## check_naming filter plugin

This plugin provides the capability to check if [passbolt](https://www.passbolt.com) resources are compliant with a naming guideline using the output a regex string and the output of the passbolt lookup or the passbolt_inventory lookup.

## Resources

* Blog post about passbolt ansible lookup plugin: [https://blog.passbolt.com/managing-secrets-in-ansible-using-passbolt-87af031ceab6](https://blog.passbolt.com/managing-secrets-in-ansible-using-passbolt-87af031ceab6)
* Gitlab repository with examples: [https://github.com/passbolt/lab-passbolt-ansible-poc](https://github.com/passbolt/lab-passbolt-ansible-poc)
* How to use this lookup plugin with AWX: [read this post from passbolt community forum](https://community.passbolt.com/t/ansible-lookup-plugin-throws-typeerror-encoding-without-a-string-argument/9222/7)