1.Create a Simple static website using HTML & CSS.

2.Create a EC2 instance that allow the security rules SSH and HTTP.

3.Create a workflow Directory  in Github Repository to trigger the Github actions.

4.Created Secret variable in GitHub Directory(In the Github repository in settings/secrets/actions create new secret)
    -Secrets
        - AWS EC2 SSH key.
        - DNS of HOST.
        - Directory to save the file.
        - Uername oof the EC2 instance.
5.Created yaml code for the workflow GitHub action(Added the code in the action part of the github repository).
```
    name: Push-to-EC2

# Trigger deployment only on push to main branch
on:
  push:
    branches:
      - master

jobs:
  deploy:
    name: Deploy to EC2 on master branch push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the files
        uses: actions/checkout@v2

      - name: Deploy to Server 1
        uses: easingthemes/ssh-deploy@main
        env:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          TARGET: ${{ secrets.TARGET_DIR }}

      - name: Executing remote ssh commands using ssh key
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST_DNS }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            sudo apt-get -y update
            sudo apt-get install -y apache2
            sudo systemctl start apache2
            sudo systemctl enable apache2
            cd home
            sudo mv * /var/www/html
```
6.Check the github action is created in action in the github repository.
7.check the website is up on the EC2 instance in  the browser by public ip of the EC2.