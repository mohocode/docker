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


RUN npm install -g npm@latest

RUN npm install -g nodemon

USER node

# Let's make os more colorful
RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"

COPY ./.bashrc /home/node/.bashrc


WORKDIR /home/node/app

EXPOSE 3000

CMD [ "top" ]