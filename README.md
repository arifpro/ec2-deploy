<h1 align="center">AWS EC2 deploy process</h1>
<h3 align="center">This is a simple guide to deploy a git repository on AWS EC2.</h3>

<br />
<br />
<br />

## Table of contents

- [Installation](#installation)
- [Deploy your project](#deploy-your-project)
- [Run your project](#run-your-project)

## Installation <a name="installation"></a>

- [STEP-1]: First create instance on AWS (ubuntu 22 lts)
  - [OPTION-1 `SSH`]: Connect your instance with ssh on your local machine.
    - Go to your instance on AWS console.
    - Click on CONNECT.
    - Copy the command and paste it on your terminal.
  - [OPTION-2 `BROWSER`]: You can connect terminal of your instance from AWS console.
    - Go to your instance on AWS console.
    - Click on CONNECT.
    - Click on `EC2 Instance Connect` tab and then click on `Connect` button.
- [STEP-2]: Then connect your github repository with your instance.
  - Go to `Setting` of your repository on github.
  - Then `Actions`.
  - Under dropdown menu, select `Runners`.
  - Then click on `New self-hosted runner` button.
  - Select `Linux`.
  - Copy the commands and paste it in your instance terminal.
- [STEP-3]: Create .env file on root directory (/home/ubuntu/.env) so that you don't have to push .env file on github.
  - Create file by using nano editor.

    ```sh
    # cd /home/ubuntu

    # touch .env

    nano .env
    ```

  - Paste your environment variables from local to that .env file.

    ```sh
    # .env
    DEVELOPER_GITHUB_USERNAME='arifpro'
    DEVELOPER_FULL_NAME='Md Arif Hossain'
    DEVELOPER_WEBSITE='https://devarif.me'

    # App
    APP_PORT=3000
    ```

## Deploy your project <a name="deploy-your-project"></a>

<!-- - [Without Docker](./without%20docker) -->
- [Without Docker](https://github.com/arifpro/ec2-deploy/tree/main/without%20docker)
  - [ONLY STEP]: Add `ec2-deploy.yml` file on `.github/workflows` directory of your project. (you can rename this yml file to any name you like)
      <!-- - [ec2-deploy.yml](./without%20docker/.github/workflows/ec2-deploy.yml) -->
    - [ec2-deploy.yml](https://github.com/arifpro/ec2-deploy/tree/main/without%20docker/.github/workflows/ec2-deploy.yml)
    > You will find it here [Without Docker](./without%20docker)
<!-- - [With Docker](./with%20docker) -->
- [With Docker](https://github.com/arifpro/ec2-deploy/tree/main/with%20docker)
  - [STEP-1]: Firstly, install docker on ec2 instance.
    - For ubuntu 22 lts, run the following command.

      ```sh
      sudo apt update

      sudo apt install apt-transport-https curl gnupg-agent ca-certificates software-properties-common -y

      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      
      sudo add-apt-repository “deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable”
      
      sudo apt install docker-ce docker-ce-cli containerd.io -y
      
      sudo usermod -aG docker $USER
      
      newgrp docker
      
      docker version
      ```

  - [STEP-2]: Secondly, docker-compose on ec2 instance.

    - For ubuntu 22 lts, run the following command.

      ```sh
      sudo curl -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)-$(uname -m)"  -o /usr/local/bin/docker-compose

      sudo mv /usr/local/bin/docker-compose /usr/bin/docker-compose

      sudo chmod +x /usr/bin/docker-compose
        ```

  - [STEP-3]: Add config files to your project.
    - Add `Dockerfile` and `docker-compose.yml` file on root directory of your project.
      <!-- - [Dockerfile](./with%20docker/Dockerfile) -->
      - [Dockerfile](https://github.com/arifpro/ec2-deploy/tree/main/with%20docker/Dockerfile)
      <!-- - [docker-compose.yml](./with%20docker/docker-compose.yml) -->
      - [docker-compose.yml](https://github.com/arifpro/ec2-deploy/tree/main/with%20docker/docker-compose.yml)
    - Add `ec2-deploy.yml` file on `.github/workflows` directory of your project. (you can rename this yml file to any name you like)
      <!-- - [ec2-deploy.yml](./with%20docker/.github/workflows/ec2-deploy.yml) -->
      - [ec2-deploy.yml](https://github.com/arifpro/ec2-deploy/tree/main/with%20docker/.github/workflows/ec2-deploy.yml)
    > You will find it here [With Docker](./with%20docker)
  - [STEP-4]: Push your code to github (**without** docker command on yml file).

      ```yml
      name: Push-to-EC2 # Any name you want to set

      on:
        push:
          branches: [ prod ] # Branch name
        pull_request:
          branches: [ prod ] # Branch name

      jobs:
        build:
          name: Deploy to EC2
          runs-on: self-hosted
          steps:
            - uses: actions/checkout@v3
            - name: Env file Env file copy from /home/ubuntu to project
              run: |
                    scp /home/ubuntu/.env ./
      ```

  - [STEP-5]: Run `sudo docker-compose up -d --build` on your instance terminal.
  - [STEP-6]: If everything looking good, push your code to github (**with** docker command on yml file).

    ```yml
    name: Push-to-EC2 # Any name you want to set

    on:
      push:
        branches: [ prod ] # Branch name
      pull_request:
        branches: [ prod ] # Branch name

    jobs:
      build:
        name: Deploy to EC2 on branch [prod] push
        runs-on: self-hosted
        steps:
          - uses: actions/checkout@v3
          - name: Env file copy from /home/ubuntu to project
            run: |
                  scp /home/ubuntu/.env ./
          - name: Build with Docker compose
            run: sudo docker-compose down && docker-compose up -d --build
    ```

## Run your project <a name="run-your-project"></a>

- [STEP-1]: Go to your browser and type `http://<your-instance-public-ip>:<your-app-port>`.

  ```sh
  # Example
  http://12.34.5.67:3000
  ```

> You can use Route53 of AWS to add a domain or subdomain to your instance public ip.
> By default, your instance public ip will be `http` not `https`.
> For https, you need to add ssl certificate to your domain or subdomain.
> By default, you can't see you project directly on your domain. You have to change your port to 80. Or you can use `nginx` to redirect your port to 80. Or you can also use Load Balancer of AWS. You can find more information on google.

## License

[Apache License 2.0](./LICENSE)

<style>
  .body {
    background: #22272e;
  }
</style>
