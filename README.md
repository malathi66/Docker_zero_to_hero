----
take amazon linux ec2 insatnce
sudo su
yum install docker -y
docker --version
docker version (give total client information about version)
systemctl restart docker.service  (or) service docker start  (enable docker eingen service )
systemctl status docker.service
docker version (it will give client and also docker eingen information)
docker info  (it will give docker instalation total information)
docker pull ubuntu (# pull the images from dockerhub && by default it will not create container)
docker images  (# to list images)
docker run redis (# first check the image from local then pull image from dockerhub && by default it will create container)                                                docker pull ( download image directly from docker hub) && docker run ( first it will check the image is present in the local if not it will download the image from dockerhub && it will create a default container )
docker images
docker ps  (# it list only running<up> containers) && (# ps=process status)
docker ps -a (# it will list all the containers which are running<up> and stoped<exited> )
docker run alpine
docker search jenkins (# to search jenkins images from local(using docker cli))
docker run -it ubuntu (# to create a container using image && go to container) && (-it means interactive mode)
cat /etc/os-release (# display information about ubuntu image because loged into unubtu container)
exit  (# to exit from the container)
docker run -it --name mal1 ubuntu (# create a new container(# with new name "mal1") with existing ubuntu image )
exit
docker ps -a
docker run -it --name mal2 ubuntu /bin/bash  (# create a new container(# with new name "mal1" && new command "/bin/bash") with existing ubuntu image)
docker run -it --name mal2 ubuntu "touch f1"  (# create a new container(# with new name "mal1" && new command "touch f1") with existing ubuntu image)  ?????
docker start mal1  (# to start the container && container name=mal1)
docker attach mal1 (# go to the container && the perticular container must be started )
exit
docker rm <container name/id>  (# to remove container )
docker run -it --name cont-1 ubuntu /bin/bash  (# to create a container using image(ubuntu) && enter into container)
cd /tmp/
cat>mal (# to create a file mal in tmp && write something in the file)
exit  (# exit from container)
docker commit cont-1 ubuntu-1  (#to build a new image<ubuntu-1> from container<cont-1>)
docker images (# ubuntu-1 docker image created)
docker run -it --name cont-2 ubuntu-1 /bin/bash  *(# create a new container<cont-2> using existing image<ubuntu-1>)
cd /tmp/
ll (# the file<mal1> appear hear which  is exciting in the container-1)
cat > mal2 (# create a new file<mal2> in cont-2)
exit
docker images

------------------------------Dockerfile creation---------------
vi Dockerfile (# create Dockerfile)
FROM ubuntu
RUN echo "Hello World!" >/tmp/rmt
:wq!  (# to save the Dockerfile)
docker build -t image1 .  (# to build the image<image1> from present directory<. means present directory>)
docker images
docker run -it --name container-1 image1 /bin/bash  (# to create a container<container-1> from image<image1>  && enter into the container)
cd /tmp
ll (# it will show rmt file)
exit
vi Dockerfile
FROM ubuntu
WORKDIR /tmp
RUN echo "this is malati file!" >/tmp/rmt
ENV name malathi
COPY file1 /tmp
ADD test.tar.gz /tmp
:wq!
cat > file1   (# create a file and write some content)
touch test  (#create a file<test> )
tar -cvf test.tar test  (#convert test file into tarfile)
ll (# test.tar file created)
gzip test.tar (#convert tar file to zip file)
ll (# test.tar.gz file created )
rm -rf test (# remove test file && we don't have any use of this file)
docker build -t image2 .
docker images
docker run -it --name container-2 image2 /bin/bash   (# create a container<container-2> using image<image2>  && enter into the container with /tmp directory)
docker start container-2
docker attach container-2
----------------docker volumes-------------------
vi Dockerfile  (# create Dockerfile to create volume automatically )
FROM ubuntu
VOLUME ["/disha"]
RUN touch f1
wq!
docker build -t image3 .
docker images  (# docker image <image3> created)
docker run -it --name cont-5 image3 /bin/bash  (# create a container <cont-5> using image <image3>)
docker ps -a (# container <cont-5> created)
docker start cont-5 (# start the container <cont-5> )
docker attach cont-5  (# enter into the container )
cd disha (# go to the volume <disha>)
cat > file1 (# create a fiel and write some content && )
exit
docker ps -a
docker run -it --name cont-6 --privileged=true --volumes-from cont-5 image3 /bin/bash  (# to share the volume from one container<cont-5> to another new container<cont-6> && enter into the container)
cd /disha
ll (# it will show file1, which is created in cont-5)
cat>file2  (# create a new file <file2>  && write some content)
exit
docker start cont-5
docker attach cont-5 (# go to the container <cont-5> )
cd disha/
ll (# file2<which is created in cont-6> disply here because cont-5 and cont-6 are volume shared)

------------------share volume manually from one container to another---------------------------
docker run -it --name cont-7 -v /vol2 image3 /bin/bash  (# create a volume <vol2> along with container manually)
docker run -it --name cont-8 --privileged=true --volumes-from cont-7 image3 /bin/bash  (# create a new container <cont-8> && a volume from cont-7)

------------------------------volume share from host to constainer------------------------------------

cd /home/ec2-user
touch f1 f2 f3  (# create three files in loacal </home/ec2-user>)
docker run -it --name hostcont -v /home/ec2-user:/hostvolume ubuntu /bin/bash   (# share the volumes from local to container<hostcont>)
docker volumes ls    (# to list the docker volumes)
docker volume create volume1    (# create docker volume <volume1>)
docker volume rm volume1    (# remove the docker volume<volume1>)
docker volume create volume2
docker volume prune       (#  This will remove all local volumes not used by at least one container.)
