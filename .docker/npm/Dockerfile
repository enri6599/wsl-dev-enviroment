# Usa un'immagine Node.js
FROM node:18

# Imposta la directory di lavoro
WORKDIR /app

# Copia il resto del progetto nel container
COPY . .

# Installa le dipendenze
RUN npm install

# Espone la porta 3000
EXPOSE 3000

# Avvia l'app Nuxt in modalità sviluppo
CMD ["npm", "run", "dev"]
