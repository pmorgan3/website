---
id: server-configuration
title: "Configuration du serveur"
---

Il s’agit principalement de la documentation de la configuration de base du serveur Linux, mais je pense qu’il est important de documenter et de partager les étapes que j’ai suivies pour démarrer définitivement Verdaccio sur mon serveur. Vous aurez besoin d'autorisations root (ou sudo) pour les opérations suivantes.

<div id="codefund">''</div>

## Gérer en tant qu'utilisateur distinct

Premièrement créez l’utilisateur verdaccio:

```bash
$ sudo adduser --system --gecos 'Verdaccio NPM mirror' --group --home /var/lib/verdaccio verdaccio
```

Or, in case you do not have `adduser`:

```bash
$ sudo useradd --system --comment 'Verdaccio NPM mirror' --create-home --home-dir /var/lib/verdaccio --shell /sbin/nologin verdaccio
```

You create a shell as the verdaccio user using the following command:

```bash
$ sudo su -s /bin/bash verdaccio
$ cd
```

The `cd` command sends you to the home directory of the verdaccio user. Make sure you run verdaccio at least once to generate the config file. Edit it according to your needs.

## À l'écoute de toutes les adresses

If you want to listen to every external address set the listen directive in the config to:

```yaml
# you can specify listen address (or simply a port)
listen: 0.0.0.0:4873
```

If you are running verdaccio in a Amazon EC2 Instance, [you will need set the listen in change your config file](https://github.com/verdaccio/verdaccio/issues/314#issuecomment-327852203) as is described above.

> Configure Apache or nginx? Please check out the [Reverse Proxy Setup](reverse-proxy.md)

## Garder verdaccio en opération pour toujours

You can use node package called ['forever'](https://github.com/nodejitsu/forever) to keep verdaccio running all the time.

First install `forever` globally:

```bash
$ sudo npm install -g forever
```

Make sure you've run verdaccio at least once to generate the config file and write down the created admin user. You can then use the following command to start verdaccio:

```bash
$ forever start `which verdaccio`
```

You can check the documentation for more information on how to use forever.

## Durée de redémarrage du serveur

You can use `crontab` and `forever` together to start verdaccio after a server reboot. When you're logged in as the verdaccio user do the following:

```bash
$ crontab -e
```

This might ask you to choose an editor. Pick your favorite and proceed. Add the following entry to the file:

    @reboot /usr/bin/forever start /usr/lib/node_modules/verdaccio/bin/verdaccio
    

The locations may vary depending on your server setup. If you want to know where your files are you can use the 'which' command:

```bash
$ which forever
$ which verdaccio
```

## Using systemd

Instead of `forever` you can use `systemd` for starting verdaccio and keeping it running. Verdaccio installation has systemd unit, you only need to copy it:

```bash
$ sudo cp /usr/lib/node_modules/verdaccio/systemd/verdaccio.service /lib/systemd/system/ && sudo systemctl daemon-reload
```

This unit assumes you have configuration in `/etc/verdaccio/config.yaml` and store data in `/var/lib/verdaccio`, so either move your files to those locations or edit the unit.