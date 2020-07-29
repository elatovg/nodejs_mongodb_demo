FROM node
COPY app /app
WORKDIR /app
RUN npm install
COPY ./wait-for-it.sh /
RUN chmod +x /wait-for-it.sh
CMD ["node", "server.js"]
