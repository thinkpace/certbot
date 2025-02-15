# certbot

To retrieve certificates from [Let's Encrypt](https://letsencrypt.org), there are multiple option [multiple challenge types](https://letsencrypt.org/docs/challenge-types/) available. To use the most common challenge type, [HTTP-01 challenge](https://letsencrypt.org/docs/challenge-types/#http-01-challenge), certbot need to be accessible from the Internet.

In scenarios you want to create certificates for private networks, which are not accessible from the Internet, I use certbot with challenge type [DNS-01 challenge](https://letsencrypt.org/docs/challenge-types/#dns-01-challenge). This works with creating a TXT record at your DNS provider. This record gets queried by Let's Encrypt and if it matches, the certificate gets issued.

My DNS provider is [Netcup](https://www.netcup.com/de) and there is a Netcup certbot plugin available at [coldfix/certbot-dns-netcup](https://hub.docker.com/r/coldfix/certbot-dns-netcup).

The following chapters cover setup and issueing of certificates on a regular basis. I publish it because it might help anybody to setup a similar solution.

# Requirements

* Ansible 2.7 or higher
* Docker runtime environment with docker compose

# Setup

First of all, please instantiate Ansible var samples and netcup-credentials.ini sample:

```
cd /YOUR/REPO/CHECKOUT/PATH/ansible
cp roles/install-certbot/vars/main-sample.yml roles/install-certbot/vars/main.yml
cp roles/install-certbot/files/netcup-credentials.ini.sample roles/install-certbot/files/netcup-credentials.ini
```

Open `roles/install-certbot/vars/main.yml` in your prefered editor and adapt settings. For each domain group under `domain_groups`, a single certificate will get issued. The very first domain a a group will get the Common Name, more domains will be issued as Subject Alternative Name (SAN) entries.

Open `roles/install-certbot/files/netcup-credentials.ini` in your prefered editor and enter your Netcup credentials.

To execute this Ansible playbook, you can use following command:

```
ansible-playbook -i /PATH/TO/INVENTORY /YOUR/REPO/CHECKOUT/PATH/ansible/ansible_playbook.yml
```

## Start

After setup, all containers needs to get started executing

```
docker compose -f docker-compose-GROUPNAME.yml up -d
```

in setup directory.

## Upgrade

Ansible role is creating a cronjob which updates base images before issueing new certificates.
