# local-ca-with-ansible
Ansible roles and playbooks to build and run a local CA and issue/revoke certs


# How to use

## 1. Install required Ansible collections

Required Ansible collections are in `requirements.txt`.
Install them with:
~~~
$ ansible-galaxy collection install -r requirements.txt
~~~

## 2. Before generating your root CA cert, carefully eview the `lecal-ca.conf` (for RSA-based CAs -- most users) or `local-ca-ECC.conf` (for Elliptic Curve-based CAs) file; this is your local CA _config file_!

**WARNING** If you change any setting in this file, you are strongly advised to re-generate your root CA entirely. Your existing root CA may stop working otherwise.

Replace the CA "CN" field name:
```
[ca_server:vars]
# The 'CN' field of the CA cert -- this is only used when creating the CA cert.
# If you need to change the CA cert's 'CN' field, this software will require you to re-generate the CA cert entirely.
ca_cname="My Own Local Root CA, because I am root"
```

Replace (if you wish) also the CA location on your system. The default will create `$HOME/local-ca/` (e.g. `/home/phess/local-ca/`) to store your root CA as well as any certs you generate with this software.
```
[all:vars]
## Local CA location
local_ca_dir="~/my/own/local/ca"
```

## 3. Run the playbook to generate your root CA cert and key with the settings you chose.

This is the `local-CA-new-root.yml` playbook. Run it like this:
```
$ ansible-playbook -i local-ca.conf local-CA-new-root.yml

(lots of output follows)

$ find ~/local-ca
~/local-ca
~/local-ca/root-ca
~/local-ca/root-ca/root-ca.crt
~/local-ca/root-ca/root-ca.key
```

## 4. Geneate a new certificate and key for your server/service

The example below generates a certificate for the my-nice-server.example.com hostname, with my-nice-server and load-balancer.example.com added to the SAN (Subject Alternative Names) section:
```
$ ansible-playbook -i local-ca.conf \
     -e server_cname=my-nice-server.example.com \
     -e san_entries='my-nice-server,load-balancer.example.com' \
     local-CA-new-server-cert.yml

(lots of output follows)

$ ls ~/local-ca/certs
my-nice-server.example.com.crt
my-nice-server.example.com.key
```

As the `ls` output above shows, the new server certificate and key files are generated at `~/local-ca/certs/my-nice-server.example.com.{crt,key}`.

## 5. Inspect your certs

You are encouraged to inspect your newly-generated server certs with `openssl`:
```
$ openssl x509 -noout -text -in my-nice-server.example.com.crt
```

Please open Issues for any bugs, RFEs, and discussions.
Pull Requests are obviously welcome.

# Contact

![Email](https://cdn-icons-png.flaticon.com/128/646/646094.png "Email") phess at phess dot org
![Mastodon](https://icons.iconarchive.com/icons/fa-team/fontawesome-brands/96/FontAwesome-Brands-Mastodon-icon.png "Mastodon") @phess@mastodon.online
![Linkedin](https://dc-site.com/wp-content/uploads/2023/03/LinkedIn.png "LinkedIn") [phess](https://linkedin.com/in/phess)
![Keybase](https://keybase.io/images/icons/icon-keybase-logo-48.png "Keybase") [phess](https://keybase.io/phess)
