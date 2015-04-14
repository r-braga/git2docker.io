git2docker.io

Install Server:
OS: OpenSuse 13.2

You will need install docker.

curl https://raw.githubusercontent.com/cooltrick/git2docker.io/master/install.sh | sh

Create a User:

adduser user
passwd user


Usage:

Add SSH key: 

ssh-keygen

cat ~/.ssh/id_rsa.pub | ssh user@X.X.X.X git2docker


Testing using git2docker.conf ( like heroku )

git clone https://github.com/heroku/node-js-sample

cd node-js-sample

git init

Create a git2docker.conf file:

echo state=build > git2docker.conf

git add --all
git commit -m "build"
git remote add git2docker user@X.X.X.X:node-js-sample

git push git2docker master

==============================================================
@linux:/tmp/node-js-sample> git push git2docker master
Counting objects: 391, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (316/316), done.
Writing objects: 100% (391/391), 214.55 KiB | 0 bytes/s, done.
Total 391 (delta 46), reused 387 (delta 45)
remote: =======> Working - node-js-sample
remote: 
-----> Using u1000 to run an application
-----> Node.js app detected
       
-----> Reading application state
       package.json...
       build directory...
       cache directory...
       environment variables...
       
       Node engine:         0.12.x
       Npm engine:          unspecified
       Start mechanism:     npm start
       node_modules source: npm-shrinkwrap.json
       node_modules cached: false
       
       NPM_CONFIG_PRODUCTION=true
       NODE_MODULES_CACHE=true
       
-----> Installing binaries
       Resolving node version 0.12.x via semver.io...
       Downloading and installing node 0.12.2...
       Using default npm version: 2.7.4
       
-----> Building dependencies
       Installing node modules
       express@4.12.3 node_modules/express
       ├── merge-descriptors@1.0.0
       ├── utils-merge@1.0.0
       ├── cookie-signature@1.0.6
       ├── methods@1.1.1
       ├── fresh@0.2.4
       ├── escape-html@1.0.1
       ├── cookie@0.1.2
       ├── range-parser@1.0.2
       ├── content-type@1.0.1
       ├── finalhandler@0.3.4
       ├── vary@1.0.0
       ├── parseurl@1.3.0
       ├── serve-static@1.9.2
       ├── content-disposition@0.5.0
       ├── path-to-regexp@0.1.3
       ├── depd@1.0.1
       ├── qs@2.4.1
       ├── etag@1.5.1 (crc@3.2.1)
       ├── on-finished@2.2.0 (ee-first@1.1.0)
       ├── debug@2.1.3 (ms@0.7.0)
       ├── proxy-addr@1.0.7 (forwarded@0.1.0, ipaddr.js@0.1.9)
       ├── send@0.12.2 (destroy@1.0.3, ms@0.7.0, mime@1.3.4)
       ├── accepts@1.2.5 (negotiator@0.5.1, mime-types@2.0.10)
       └── type-is@1.6.1 (media-typer@0.3.0, mime-types@2.0.10)
       
-----> Checking startup method
       No Procfile; Adding 'web: npm start' to new Procfile
       
-----> Finalizing build
       Creating runtime environment
       Exporting binary paths
       Cleaning npm artifacts
       Cleaning previous cache
       Caching results for future builds
       
-----> Build succeeded!
       
       node-js-sample@0.2.0 /tmp/build
       └── express@4.12.3
       
-----> Discovering process types
       Procfile declares types -> web
remote: node-js-sample Started
remote: 49153 
To demo@localhost:node-js-sample
 * [new branch]      master -> master


@linux:/tmp/node-js-sample> curl http://localhost:49153/
Hello World!
==============================================================

Testing using Dockerfile

mkdir apache-demo
cd apache-demo

echo "FROM httpd:2.4" > Dockerfile
echo "EXPOSE 80" >> Dockerfile

Create a git2docker.conf file:

echo state=dockerfile > git2docker.conf

git init
git add --all
git commit -m "build"
git remote add git2docker user@X.X.X.X:apache-demo

git push git2docker master


@linux:/tmp/apache-demo> git push git2docker master
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 303 bytes | 0 bytes/s, done.
Total 4 (delta 0), reused 0 (delta 0)
remote: =======> Working - apache-demo
remote: 
remote: Sending build context to Docker daemon 3.072 kB
remote: Sending build context to Docker daemon 
remote: Step 0 : FROM httpd:2.4
remote:  ---> 4ea677a2d898
remote: Step 1 : EXPOSE 80
remote:  ---> Running in 2fe3a7300cdf
remote:  ---> 11d40bb1d4fe
remote: Removing intermediate container 2fe3a7300cdf
remote: Successfully built 11d40bb1d4fe
remote: apache-demo Started
remote: 49154 
To demo@localhost:apache-demo
 * [new branch]      master -> master


@linux:/tmp/node-js-sample> curl http://localhost:49154/
<html><body><h1>It works!</h1></body></html>


To remove the Containers

Just change the state flag to remove or delete and 

git add --all
git commit -m "delete"
git push git2docker master

==============================================================

How it Works:

You can deploy a Container using git2docker.conf file or using Dockerfile.


Using git2docker.conf


git2docker.conf Options:

===============================
state flag options

build

Build the application using the source code.

build:logs

Build the application using the source code and start the container showing logs.

delete or remove

Stop and remove the Container

stop

Stop the Container

start

Start a stoped Container

start:logs

Start a stoped Container and show logs

logs

Show logs of a Started Container


dockerfile or Dockerfile

Force the git2docker to use a Dockerfile

===============================
domain Flag Option:

ex: domain=app.linux.site

===============================
pre-exec Option:

Option is used to execute a command before start the application

ex: pre-exec=bundle exec rake db:create db:migrate db:seed


===============================
git flag Option:

If you have your code at an external repository like github, git2docker will download the git and deploy the application.

ex: git=https://github.com/heroku/node-js-sample

===============================


git2docker.conf example

state=build
domain=app.domain.lnx
pre-exec=bundle exec rake db:create db:migrate db:seed