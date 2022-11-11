FROM node:16

# Create app directory
WORKDIR /app

# Install app dependencies
COPY package*.json ./

# If you are building your code for production
# RUN npm ci --only=production
RUN npm install
RUN npm run build

# Bundle app source
COPY . .

EXPOSE 3000
CMD [ "npm", "start" ]