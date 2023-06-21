# **Twoge**

<h1 align="center">
	<img
		width="500"
		alt="Project Twoge"
		src="./static/img/twoge-cover.png">
</h1>

Twoge is a social media platform dedicated solely to tweets about Dodge. This application is built using Flask, SQLAlchemy, and PostgreSQL.

<h1 align="center">
	<img
		width="100"
		alt="Project Twoge"
		src="./static/img/twoge.png">
</h1>

## **Getting started**

First we forked off of chandras repos into our own
I then added action secrets to include
1. DOCKER_USERNAME
2. DOCKER_PASSWORD
3. AWS_ACCESS_KEY_ID
4. AWS_SECRET_ACCESS_KEY

Next we created a Dockerfile
## FROM python:alpine

RUN apk update && \
    apk add --no-cache build-base libffi-dev openssl-dev
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 80
CMD python ./app.py*

then...
## git add .
##**git commit -m"first-commit"**

next we initiate Elastic Beanstalk
## ** eb init**
## ** eb create twoge**
once everything is running, we add in our database URI with the eb setenv command
From here I created a .github directory then a workflows directory and finally my pipelin .yml file

##**mkdir .github **
##**mkdir workflows **
##**touch cicd-pipeline.yml**
##**name: Deploylent CICD pipeline 

on:
  push:
    branches:
      - master

env:
  DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
  DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
  AWS_REGION: 'eu-west-1'

jobs:
  build_and_push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          ref: master

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ env.DOCKER_USERNAME }}
          password: ${{ env.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: ${{ env.DOCKER_USERNAME }}/twoge-cicd:latest

  eb_deploy:
    needs: build_and_push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          ref: master

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: 3.x

      - name: Install EB CLI
        run: |
          pip install awsebcli --upgrade

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Init Elastic Beanstalk
        run: |
          echo n | eb init 

      - name: Deploy to Elastic Beanstalk
        run: |
          eb deploy**

Next I commented out the elastic beanstalk info on my .gitignore

I then added commited and pushed to my master branch which trigers the github action

##**git push origin master**
AND BOOOOOOM WE HAVE PIPELINE WORKING LIVE @
##**http://twoge-env.eba-xjmwayy6.eu-west-1.elasticbeanstalk.com/posts**

Next I made a new directory and ran a git clone of chandras repo to run all of this locally
I then made a new docker compose file
##**version: '3'
services:
  database:
    image: postgres
    container_name: twoge-database
    environment:
      POSTGRES_USER: Preston
      POSTGRES_PASSWORD: password
      POSTGRES_DB: twoge_db
    ports:
      - 5432:5432
    networks:
      - twoge-net
    healthcheck:
      test: ["CMD", "pg_isready" ,"-q", "-d",  "twoge_db", "-U", "Preston"]
      timeout: 20s
      retries: 10
  api:
    build: .
    container_name: twoge-api
    environment:
      SQLALCHEMY_DATABASE_URI: postgresql://Preston:password@database:5432/twoge_db
    ports:
      - 80:80
    networks:
      - twoge-net
    # volumes:
    #   - .:/app
    depends_on:
      database:
        condition: service_healthy
  

networks:
  twoge-net:
    name: twoge-net_network
    driver: bridge**

Finally, I ran the compose up 
##** docker compose up**

## **troubles**
1. forgot you had to add and commit into git to get the app working on elastic beanstalk
2. forgot to comment out the elastic beanstalk info on my .gitingore file
3. writing the docker compose file with the postgres database info and URI format to connect

Twoge has a single table in its database, which stores information about each tweet. The table has the following attributes:

- **`date_posted`**: The date and time when the tweet was posted.
- **`author`**: The user who posted the tweet.
- **`title`**: The title of the tweet.
- **`content`**: The content of the tweet.
