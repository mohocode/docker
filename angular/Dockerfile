# Start Angular Setup for Docker
FROM node:lts-alpine

# Add OS Pack
RUN apk add git

# add curl for oh my bash
RUN apk add curl

# Add Angular Cli - LTS
RUN npm install -g @angular/cli@latest

# add bash
RUN apk add bash
RUN apk add bash-doc
RUN apk add bash-completion


USER node

# Let's make os more colorful
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

COPY ./.bashrc /home/node/.bashrc


# Switch To target Directory
WORKDIR /home/node/app


EXPOSE 4200

CMD [ "top" ]

