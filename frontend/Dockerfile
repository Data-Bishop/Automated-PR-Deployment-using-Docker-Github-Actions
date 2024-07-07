# Stage 1: Build the React application
FROM node:20-alpine as build

# Set the environment variables
ARG VITE_API_URL
ENV VITE_API_URL=$VITE_API_URL

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application source code
COPY . .

# Install TypeScript globally to ensure it's available
RUN npm install -g typescript

# Build the React application for production
RUN npm run build

# Stage 2: Serve the built application using Nginx
FROM nginx:alpine

# Copy the build output to the Nginx html directory
COPY --from=build /app/dist /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]