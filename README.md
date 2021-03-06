# MiniMeteor

MiniMeteor is an effort to simplify Docker image creation for Meteor projects. Works with Meteor 1.3.3 or higher.

## Usage

There are two options: build by simply invoking a script, or create your Dockerfile.

#### First method: execute a script

*Best for continuous integration services, eg. CircleCI, Travis, GitLab, etc.*

```$ curl https://aedm.github.io/minimeteor/build.sh | sh -s```*```myDockerTag[s]```*

Execute this in your project directory and it builds a Docker image. You don't need Meteor to be installed, only Docker.

#### Second method: use a Dockerfile

*Good fit for automatic builds on Docker Hub, Quay.io, etc.*

`FROM aedm/minimeteor`

And then run `$ docker build -t myDockerTag .` (or let Docker Hub do it automatically)


## How to run the Docker image?

MiniMeteor exposes port 3000 by default.

`$ docker run -d -e ROOT_URL=... -e MONGO_URL=... -p 80:3000 myDockerTag`


## Build options

#### NPM_TOKEN 

Use private NPM modules. (Works with Dockerfile only.)
 
`$ docker build --build-arg NPM_TOKEN={your-private-npm-token} -t myDockerTag .`


## Troubleshooting

#### Build fails on Docker Cloud: _"tar: Directory renamed before its status could be extracted"_
By default Docker Cloud uses "small" build instances. Use "medium" or "large" build nodes instead. 


## How does it work?

The shell script version uses Alpine Linux to minimize footprint. It runs several passes to build the bundle and recompile NPM binaries for Alpine. The final build is copied into an [alpine-node](https://hub.docker.com/r/mhart/alpine-node/) container.
 
The Dockerfile version installs Meteor and some build tools into a debian/slim container, builds the bundle, copies the Node.js executable from the Meteor installation into the project directory, and removes all tools.


#### Small images

One of MiniMeteor's goals is to use as little overhead as possible. Here are some image sizes for the default Meteor application: (generated by `meteor create`)

Method | Compressed size on Docker Hub | Uncompressed size
--- | --- | ---
MiniMeteor via shell script | 20M | 64.0M
MiniMeteor via Dockerfile | 40M | 114.5M
[Meteor Launchpad](https://hub.docker.com/r/jshimko/meteor-launchpad/) by jshimko | 139M | 389.8M 

If you know about any other projects, please create an issue, and I'll include them in this table. Most projects I know about (eg. MeteorD and Meteor-Tupperware) are broken since Meteor 1.4.2. 


#### Which Node.js version does MiniMeteor use?

It uses the exact same version that a given Meteor release uses.


#### Does it run the webapp as `root` inside the container?

No, MiniMeteor runs Meteor and Node.js as a non-root user.


#### Can I make it work with Meteor versions below 1.3.3?

The Dockerfile version should work with Meteor 1.3. It's unknown whether it works with versions below that. The script version requires `fibers@1.0.13` which was added in Meteor 1.3.3. 


## Do you accept pull requests?

Yes. Please note that they might take a while, since my test framework performs a build against *every* Meteor version (above 1.3.3) to make sure it doesn't break older projects. And it runs on a potato. 
