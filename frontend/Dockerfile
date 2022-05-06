FROM node:slim

WORKDIR /app

# Copy the package.json and install dependencies
COPY package*.json ./

RUN npm install

# Copy rest of the files
COPY . .

RUN chown node:node /app

# Build the projectproject

CMD npm run dev