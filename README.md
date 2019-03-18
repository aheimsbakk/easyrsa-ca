easyrsa-ca
============

Automatic creation and distribution of Private certificates based on EasyRSA scripts.

Original EasyRSA scripts on Github: [https://github.com/OpenVPN/easy-rsa](https://github.com/OpenVPN/easy-rsa/tree/ca33f84aa22bc47a4ee47d43f35bfa2a550e1991) commit `ca33f84`.

Main functionality
------------------

* Sets up and configures a CA
* Creates and distributes certificates
* Renews certificates automatically
* Hooks when certificate distributed or renewed
  * Restart `systemd` services
  * Run custom script
* Regenerate CRL on every run on `easyrsa_ca_server`

This role **do not** distribution CA certificate or revocation list to `easyrsa_ca_crt_uri` and `easyrsa_ca_crl_uri`.

Important!
----------

This role must run in same operation on

* `easyrsa-ca-server`
* all servers that's issued a certificate

Versions
========

* `1.1.5` --- changed `easyrsa_ca_ca_expire` defaults

Requirements
------------

* Ubuntu 18.04

Role Variables
--------------

Configurable variables in the playbook and the default values for these variables.

### Configure EasyRSA server

- `easyrsa_ca_server` --- fqdn to server that will be the CA server, **required**
- `easyrsa_ca_crl_uri` --- URL where to find certification revocation list, default not set
- `easyrsa_ca_crt_uri` --- URL where to find the CAs certificate, default not set
- `easyrsa_ca_pki_dir` --- name of CA directory, default `/srv/ca/pki`
- `easyrsa_ca_ca_expire` --- in how many days should CA certificate expire, default `3650`
- `easyrsa_ca_cert_renew` --- how many days before expiration date a certificate is allowed to renew, default `30`
- `easyrsa_ca_crl_days` --- how many days CRL is valid, default `180`
- `easyrsa_ca_sub_ca` --- is this ca a subca, default `false`
- `easyrsa_ca_cn` --- CN used for the CA, default `Ansible CA`
- `easyrsa_chain` --- CA chain when signing this CA - use only if subca, default `""`
  - order of certs: intermidiate CA -> root CA

### Request certificate

- `easyrsa_ca_days` --- set validity in days, default `730`
- `easyrsa_ca_digest` --- digest to use in request and certificates, default `sha256`
- `easyrsa_ca_dn_mode` --- DN mode to use (cn_only or org), default `cn_only`
- `easyrsa_ca_keysize` --- size in bits of keypair to create, default `4096`
- `easyrsa_ca_subca_len` --- path length of signed sub_ca certs, default `0`
- `easyrsa_ca_use_algo` --- choose rsa or ec, default `rsa`
- `easyrsa_ca_req_cn` --- default CN to use, default `{{ ansible_fqdn }}`
- `easyrsa_ca_req_c` --- country code (2_letters), default `""`
- `easyrsa_ca_req_st` --- state/province, default `""`
- `easyrsa_ca_req_city` --- city/locality, default `""`
- `easyrsa_ca_req_org` --- organization, default `""`
- `easyrsa_ca_req_email` --- email address, default `""`
- `easyrsa_ca_req_ou` --- organizational unit, default `""`
- `easyrsa_x509_type` --- which certificate to create, defaults `server`
  - `server`
  - `client`
  - `code-signing`
  - `serverClient` -- server and client
  - `dc` --- windows domain controller
- `easyrsa_ca_subject_alt_name` --- string with one or more domains to create on the format, default to `DNS:{{ ansible_fqdn }}`
  - example: `DNS:main.domain.org,DNS:alias.domain.org,IP:10.10.10.10`

### Destination server

- `easyrsa_ca_revoke` --- revoke previous certificate - used when regenerating with new `easyrsa_ca_subject_alt_name`, default `false`
- `easyrsa_certificate_dir` --- directory for certificate, default `/etc/ssl/easyrsa`
- `easyrsa_ca_hook_services` --- list of systemd services to restart, default `[]`
- `easyrsa_ca_hook_scripts` --- list of custom script to run, default `[]`

Dependencies
------------

None


Example Playbook
----------------

    - hosts: servers
      serial: 1
      roles:
         - { role: easyrsa-ca, easyrsa_ca_server: 'my.server.org' }

Example change alt name
-----------------------

* Update your `host_vars` file with new value for `easyrsa_ca_subject_alt_name`.
* Run once
    ```
    ansible-playbook -e easyrsa_ca_revoke=1 -i hosts -l mysecond.server.org myplay.yml
    ```

License
-------

This playbook: GPLv2

EasyRSA: https://github.com/OpenVPN/easy-rsa/blob/ca33f84aa22bc47a4ee47d43f35bfa2a550e1991/COPYING.md

Full source code of EasyRSA: https://github.com/OpenVPN/easy-rsa


Author Information
------------------

Arnulf Heimsbakk

###### vim: set et ai ts=4 st=4 sw=4 spell spelllang=en:

