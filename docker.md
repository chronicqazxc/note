# Notes of Docker

1. [putting-jenkins-docker-container](https://engineering.riotgames.com/news/putting-jenkins-docker-container)
2. [container](https://docs.docker.com/engine/reference/commandline/container/)
3. [recap-and-cheat-sheet-optiona](https://docs.docker.com/get-started/part2/#recap-and-cheat-sheet-optional)
4. [image](https://docs.docker.com/engine/reference/commandline/image/)
5. [logging](https://docs.docker.com/config/containers/logging/)
6. [Exploring Docker container's file system](https://stackoverflow.com/a/20816397)
7. [Programmatically removes files/folder resides in docker container](https://stackoverflow.com/a/38591846)
8. [understanding-volumes](https://container-solutions.com/understanding-volumes-docker/)
9. [assign a port mapping to an existing Docker container?(Create a new image based on current container)](https://stackoverflow.com/a/26622041)
10. [restore-a-docker-volume-to-your-mac](https://medium.com/@jimkang/restore-a-docker-volume-to-your-mac-f79628617dee)
11. [getting-path-and-accessing-persistent-volumes-in-docker-for-mac](https://timonweb.com/posts/getting-path-and-accessing-persistent-volumes-in-docker-for-mac/)
12. [Docker for Mac Commands for Getting Into The Local Docker VM](https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm/)
13. [How do I kill all screens](https://unix.stackexchange.com/a/94528)
14. [SCP](http://www.hypexr.org/linux_scp_help.php) file from Docker VM to localhost: ```scp -r file_to_be_moved username@remote_host:/path/to/destination/dir```
15. [Copying files from host to Docker container](https://stackoverflow.com/a/31971697)
16. [Pushing and Pulling to and from Docker Hub (Backup Images)](https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html)
17. [Login container as root](https://stackoverflow.com/a/35485346)

# Bind Mount
```shell
docker run --name container_name -p 80:8080 -p 50000:50000 -v /Users/your_username/Documents/jenkins_home:/var/jenkins_home -e "TZ=Asia/Taipei" jenkins/jenkins:lts
```
[reference](https://docs.docker.com/storage/bind-mounts/)
# Create Volume (Preferred)
```shell
docker run --name container_name -p 80:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -e "TZ=Asia/Taipei" jenkins/jenkins:lts
```
[reference](https://docs.docker.com/engine/reference/commandline/volume_create/)
# Backup Volume
#### 1. Backup the data volume from the data container named data-container-to-backup
```shell
cd path_of_where_you_want_to_put_the_archive
```
```shell
docker run --rm --volumes-from container_name --name tmp-backup -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /var/jenkins_home
```
#### 2. Expand this tar file into a new container so we can commit it as part of its image
```shell
cd path_of_where_the_archive_at
```
```shell
docker run -d -v $(pwd):/backup --name data-backup ubuntu /bin/sh -c "cd / && tar xvf /backup/backup.tar && sudo chown -R $USER /backup/var/jenkins_home"
```
#### 3. Commit and push (if necessary) the image with a desired tag ($VERSION)
*Check previous step has completed, use docker ps or docker logs to inspect.*
```shell
docker commit data-backup repo/data-backup:$VERSION
docker push repo/data-backup:$VERSION (optional)
```
#### 4. Clean up
```shell
docker rm data-backup
```
#### 5. Run the data container with the data-backup image
```shell
docker run -v /var/jenkins_home --entrypoint "bin/sh" --name data-container repo/data-backup:${VERSION}
```
#### 6. Run image with volumes from the data-conainter
*Remember the repo/version of image (jenkins) should be identical to the version that been used by the archive.*
```
docker run --volumes-from=data-container --name container_name -p 80:8080 -p 50000:50000 jenkins/jenkins:2.135
```
[reference](https://stackoverflow.com/a/41279845)
# Run Jenkins container on https
## Normal approach
```
keytool -genkey -keyalg RSA -alias selfsigned -keystore jenkins_keystore.jks -storepass {password} -keysize 2048
docker run -v {volume id}:/var/jenkins_home -p 443:8443 jenkins/jenkins:2.135 --httpPort=-1 --httpsPort=8443 --httpsKeyStore=/var/jenkins_home/jenkins_keystore.jks --httpsKeyStorePassword={password}
```
[reference](https://stackoverflow.com/a/29836864)
## Jenkins migration (from http to https)
#### 1. Find out the volume_name.
```
docker inspect shdr_jenkins
```
#### 2. Shut down http Jenkins container.
```
docker stop shdr_jenkins
```
#### 3. Run a https Jenkins container with existed volume.
```
docker run --name container_name -p 443:8443 -p 50000:50000 -v {volume_name}:/var/jenkins_home -e "TZ=Asia/Taipei" jenkins/jenkins:2.135 --httpPort=-1 --httpsPort=8443
```
