# GUIDA PER LA CONFIGURAZIONE DI UN AMBIENTE LOCALE CON SAIL E NGINX PROXY MANAGER

## GENERAZIOINE DEI CERTIFICATI

PER LA GENERAZIONE DEI CERTIFICATI ABBIAMO BISOGNO DI 'mkcert'

- windows `choco install mkcert`
- il modo più rapido per installarlo in linux è attraverso brew `brew install mkcert`

> _Per installare BREW:_
>
> 1. Installare requisiti:
>    `sudo apt-get install build-essential procps curl file git`
> 2. Per installare BREW:
>    `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
> 3. Aggiungere brew a .bashrc:
>    `echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> /home/${utente}/.bashrc`

Installa il CA `mkcert -install`

**Per generare la coppia cerrificato chiave:**

```
mkcert "*.dominion.test" "dominio.test"
```

I domini con wildcard (\*) sono validi solo per il sottodominio in cui è presente l'asterisco

## Nginx

Per ogni sito che vuoi accedere tramite ssl puoi aggiungere ssl in nginx tramite custom dopo averli generati con mkcert

Poi li puoi legare ai siti.

Per le applicazioni che utilizzano npm bisogna eseguire con `npm run dev -H 0.0.0.0` altrimenti non è visibile tramite Nginx Proxy Manager su docker
