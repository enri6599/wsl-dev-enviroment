# GUIDA PER LA CONFIGURAZIONE DI UN AMBIENTE LOCALE CON DOCKER E TRAEFIK


Copiare la cartella traefik sulla WSL


## GENERAZIOINE DEI CERTIFICATI

PER LA GENERAZIONE DEI CERTIFICATI ABBIAMO BISOGNO DI 'mkcert'
- il modo più rapido per installarlo in linux è attraverso brew

Per installare BREW:
1. Installare requisiti: `sudo apt-get install build-essential procps curl file git`
2. Per installare BREW: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
3. Aggiungere brew a .bashrc: `echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> /home/${utente}/.bashrc`


E' necessario installare mkcert anche su windows per rendere accettabile il certificato su chrome.

`choco install mkcert`

Installa il CA `mkcert -install`

SULLA WSL ORA ESEGUI
```
brew install mkcert
mkcert -install

rm -r $(mkcert -CAROOT)
cp -r $(wslpath $(mkcert.exe -CAROOT)) $(mkcert -CAROOT)
```

Questo comando fa in modo che il CA che creiamo su WINDOWS sia disponibile sul linux da dove creeremo i certificati su WSL

Ora all'interno della cartella traefix che hai inserito all'interno del tuo sistema WSL
Lancia questo comando che ti genererà il certificato per la lista di siti che andrai ad utilizzare, è possibile utilizzare * come wildcard
Attenzione *.docker.localhost sono utilizzati dal container traefik per fare da proxy, non li eliminate.
```
mkcert -cert-file certs/local-cert.pem -key-file certs/local-key.pem "docker.localhost" "*.docker.localhost" "domain.local" "*.domain.local" "*.test"
```

## LANCIO DI TRAEFIK
Lanciate questo comando per creare il network docker che ci servirà dopo.
```
docker network create web
```

nella cartella di traefik potete lanciare 
```
docker compose up
```


## CREAZIONE DEI DOCKER DELLE APPLICAZIONI CHE VOGLIAMO SERVIRE
al composer presente aggiungere i parametri

```
labels:
    - 'traefik.enable=true' # ABILITA TRAEFIK SUL CONTAINER
    - 'traefik.http.routers.${SERVICE}.rule=HostRegexp(`${WEB_DOMAIN}`)' 
    - 'traefik.docker.network=web'
    - 'traefik.http.services.${SERVICE}.loadbalancer.server.port=80'
    # Activation of TLS
    - "traefik.http.routers.${SERVICE}.tls=true"
```

${SERVICE} è il nome univoco che vogliamo dare al servizio di traefik per il container
${WEB_DOMAIN} è l'url a cui vogliamo che traefik faccia da proxy

traefik.http.routers.${SERVICE}.tls=true 
abilita il protocollo sicuro per il container

Non dobbiamo dimenticarci di aggiungere il network 'web' ai network del container che vogliamo rendere disponibile con traefik
Dobbiamo anche aggiungere ai network, il network web come external.

Grazie a traefik non è più necessario esporre le porte all'esterno perché solo traefik è esposto

Nei casi in cui si voglia conntettere tra container di 2 applicazioni differenti sarebbe possibile accedeci attraverso il nome del container ma nel caso si abbia la necessità di accederci attraverso il nome di dominio effettivo bisogna aggiungere al docker-compose la seguente riga

`hostname: ${WEB_DOMAIN}` dove web_domain è l'hostname che vogliamo dare al container