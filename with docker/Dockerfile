FROM node:16

# Create app directory
WORKDIR /app

# Install app dependencies
COPY package*.json ./

# If you are building your code for production
# RUN npm ci --only=production
RUN npm install

# Bundle app source
COPY . .

# Build the app
RUN npm run build

EXPOSE 3000
CMD [ "npm", "start" ]
