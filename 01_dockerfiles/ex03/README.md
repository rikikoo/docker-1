# docker-1 / containerized GitLab server

## Preparation
1. Create a docker-machine with enough virtual memory: `docker-machine create --virtualbox-memory "4096" --virtualbox-cpu-count "2" default`
2. Login to docker: `docker login`
3. Build the image with the command (when in the directory where this Dockerfile is):
`docker build -t ex03 .`
4. Push the image to your docker repo:
   - `docker tag ex03 username/ex03`
   - `docker push username/ex03`


## Starting the GitLab server
After the image has been built, start the container with the command:
`docker run -d --name glab -p 80:80 -p 443:443 -p 8022:22 ex03`

When you run it, the GitLab rails app will begin to "compile" and this will take
several minutes until you can access your GitLab page.
You can monitor the server's starting process with the command:
`docker logs -f glab`

After the logs show that SSH has started, on your browser go to
https://192.168.99.100 and you should see the GitLab page where
you are requested to create a new password (for root).


## Testing our own GitLab
On GitLab, try creating a new project and then cloning the repository to your computer.
git most likely won't let you clone anything from your GitLab server,
because it has a self-signed SSL certificate. To counter this (temporarily), run the command:
`git config --global http.sslVerify false`

and then `git clone https://192.168.99.100/user/project`

To clone your repo via SSH, first create a new key-pair for GitLab.
1. ssh-keygen
    - create a new file to the path `/Users/<your username>/.ssh/gitlab`
2. `eval $(ssh-agent)`
3. `ssh-add /Users/<your username>/.ssh/gitlab`
4. `cat /Users/<your username>/.ssh/gitlab.pub | pbcopy`
    - you now have your public key copied on your clipboard
5. Go to your GitLab account on the web app
6. Click your user icon on the top-right corner and select _Settings_
7. Select _Add SSH key_ from the vertical navbar on the left
8. Paste your public key to the SSH key field and click OK

Go to your project page, click on the _Clone_ button and copy the SSH link to your clipboard.
On your terminal, type `git clone` and paste the link after it and press enter.

Finally, remember to re-enable SSL verification:
`git config --global http.sslVerify true`
