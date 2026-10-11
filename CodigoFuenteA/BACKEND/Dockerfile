FROM node:18-alpine
WORKDIR /app
# Copiar archivos de dependencias
COPY package*.json ./
# Instalar dependencias de producción desde el lockfile, sin ejecutar scripts de ciclo de vida
RUN npm ci --omit=dev --ignore-scripts
# Copiar código de la aplicación
COPY src/ ./src/
COPY sql/ ./sql/
# Crear directorio para uploads y asignarlo al usuario sin privilegios
RUN mkdir -p uploads/contracts && chown -R node:node /app/uploads
# Ejecutar como usuario no root
USER node
# Exponer puerto
EXPOSE 3000
# Comando para iniciar la aplicación
CMD ["node", "src/app.js"]
