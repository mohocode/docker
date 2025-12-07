# Use Node.js on Alpine Linux
FROM node:20-alpine

# Install needed packages
RUN apk update && apk add --no-cache \
    git \
    curl \
    bash \
    bash-doc \
    bash-completion \
    && rm -rf /var/cache/apk/*

# Install Oh My Bash
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)" || true

# Set working directory
WORKDIR /home/node/app



# Install React dependencies
RUN npm install

# Expose the port React will run on
EXPOSE 4500

# Run React dev server on port 4500
CMD ["npm", "start"]