# ec2-deploy

A simple tool to deploy a git repository to an EC2 instance.

## Installation

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

## Deploy your project

<!-- - [With Docker](https://github.com/0xavalon/wind-web-payroll/tree/main/with%20docker) -->
- [With Docker](./with%20docker)
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

  - [STEP-3]: Push your code to github (**without** docker command on yml file).

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
            - name: Env file copy
              run: |
                    scp /home/ubuntu/.env ./
      ```

  - [STEP-4]: Run `sudo docker-compose up -d --build` on your instance terminal.
  - [STEP-5]: If everything looking good, push your code to github (**with** docker command on yml file).

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
          - name: Env file copy
            run: |
                  scp /home/ubuntu/.env ./
          - name: Build with Docker compose
            run: sudo docker-compose down && docker-compose up -d --build
    ```
<!-- - [Without Docker](https://github.com/0xavalon/wind-web-payroll/tree/main/without%20docker) -->
<!-- - [Without Docker](./without%20docker) -->

## Run your project

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
