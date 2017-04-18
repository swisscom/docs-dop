+++
date = "2017-04-18T13:35:06+02:00"
title = "Credentials"
toc = true
weight = 12

+++

The credentials hash can hold credentials used to login to systems, APIs or to set passwords or keys during setup.

Example:

    credentials:
     'linux_staging_login':
       type: :username_password
       username: 'root'
       password: 'foo'
     'linux_prod_login':
       type: :ssh_key
       private_key: '/home/root/.ssh/id_dsa'
     'windows_staging_login':
       type: :username_password
       username: 'administrator'
       password: 'winfoo'
     'windows_prod_login':
       type: :kerberos
       realm: 'FOOOO'

The various DOPi plugins can use these credentials to login to a node if the type is supported by the plugin.
It is recommended to use the set_plugin_defaults mechanic to set this credentials so it does not have to be
specified in every command separately. You can also change this defaults in your steps flow.

### Credential types

The credential types and the fields you can specify are listed here

#### username_password
A simple username password pair.

1. __*username*__ (required)
2. __*password*__ (required)

#### kerberos
Settings for a kerberos login

1. __*realm*__ (required) - The kerberos realm
2. __*service*__ (optional) - The service we try to use
3. __*keytab*__ (optional) - The keytab file to use instead of the default one

#### ssh_key
And SSH key we can use to login

1. __*username*__ (required)
2. __*private_key*__ (optional)
3. __*public_key*__ (optional)

While the public_key and private_key are optional in general, they are required depending on the usage of the
credential. If the credential is used in DOPi to login to ssh, a private key will be required and the parser will
inform you of this. If the credential is used in DOPv to set the login credentials of a vm, a public_key will be
required.

public_key and private_key can both be specified inline or be read from a file. Check the documentation about
[external secrets](#external-secrets) about how to specify this correctly.

### External secrets

Secrets like passwords or keys can be stored outside of the plan so you don't have to check them into version control.
Instead of the password you can specify a hash with only one key-value pair. The key is either :file or :exec.
The value for :file is a simple file which will be read to get the password. For :exec it is an array of where
the first entry is the executable and the rest is a bunch of options or arguments which will be joined together
and passed to the executable on the command line.

    credentials:
      'linux_staging_login':
        type: :username_password
        username: 'root'
        password:
          file: '/path/to/my_external_secret'
      'linux_prod_password':
        type: :username_password
        username: 'root'
        password:
          exec: ['/path/to/my_external_secret', '--some-option', 'arg1']


