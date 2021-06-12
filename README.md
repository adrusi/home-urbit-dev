![cover image](https://github.com/OdysLam/home-urbit/blob/master/assets/%20urbit-home.png?raw=true)

# Introduction 

Urbit is a new OS and peer-to-peer network that’s simple by design, built to last forever, and 100% owned by its users.
Urbit OS makes the server side usable for individuals without the need for MEGACORP to run their software. Urbit is your own personal server. 
Urbit is your last computer. Welcome.

[![balena deploy button](https://github.com/OdysLam/home-urbit/blob/master/assets/Deploy%20Urbit%20with%20Balena.svg?raw=true)](https://dashboard.balena-cloud.com/deploy?repoUrl=https://github.com/OdysLam/home-urbit)

## Deploy with balena

[balena](https://www.balena.io/what-is-balena/) is a complete set of tools for building, deploying and managing fleets of connected linux devices. We opted to use balena because it manages the whole lifecycle of our device and application. We only have to download the OS from our account and load it into the SD card for the Raspberry pi. 

balena is completely free for up to 10 devices and most of it's components are Open Source. 

The same setup will work flawlessly if you install another OS into the raspberry pi and use `docker-compose up`.

## Getting Started with a new comet

1. Click on the Button above

![](/assets/deploy1.png)

2. Create a balena account (or log in). Create application with default settings.

![](/assets/deploy2.png)

3. To add a device to the application, download the OS image and [flash](https://www.balena.io/etcher/) it to an sd card. 

![](/assets/deploy3.png)

4. Insert the sd card to the Rasspberry pi 4, connect it to power + Internet. Wait to donwload your application. 

5. Visit the Cloud Dashboard to see that everything works as expected

![image](https://user-images.githubusercontent.com/13405632/121319220-aafa6e80-c914-11eb-803d-732134d693bd.png)

6. Click on the web terminal, select `urbit` and open a terminal session. Run command `/usr/sbin/get-urbit-code.sh`. This command will give you the code for your Urbit. By default, it will boot a **new** comet.

7. Visit the following address to access `~Landscape`: <ip_address>:8080. e.g 192.168.1.249:8080 (as you can see from the cloud dashboard above).

## Getting Started with a planet/star/galaxy keyfile

1. Run `git clone https://github.com/odyslam/home-urbit` on your computer. 
2. Move the `<private_key_name>.key` file inside the `urbit/keys/` directory of the repo you just download. So `~/home-urbit/urbit/keys/<private_key_name>.key`
3. Create a [balena-cloud account](https://dashboard.balena-cloud.com/apps), then create an application for device type ("Raspberry pi 4"). Let's name it `home-urbit`. 
4. Download [balenacli](https://github.com/balena-io/balena-cli/blob/master/INSTALL.md), install it and [sign into](https://www.balena.io/docs/reference/balena-cli/) your account.
5. `cd` into the repo directory `home-urbit` and run `balena push home-urbit`. 

The reason we can't follow the "Deploy with balena" button flow is that we need to add our key into the application files.

### ALTERNATIVE: Manually add the keyfile inside the urbit container

If you don't want to add your keyfile inside the docker-compose file, there is another option. 

Define the `device service environment variable` `TRANSFER_KEY`=`1`. The Urbit container will start, but stay idle. Then you can ssh into the container (either using `balena ssh` or the web terminal) and manually copy over your key into the directory `urbit/keys`. After you do, remove the environment variable. The container will restart and will pick up your key.

This is better for security/privacy reasons, as all the files that you add in the directory, will be uploaded to a remote build server. There, it will build the containers and then send them to the device. Thus, the key will temporally exist on the remote server.

## Getting Started with an existing pier

The best option here is to follow the [Alternative](#alternative-manually-add-the-keyfile-inside-the-urbit-container) option above and idle the container. Then, using a command as `scp` or better `rsync`, copy over the pier from your computer into the container. The pier should be placed inside the directory `/urbit/piers/<you_pier_name>`. 

You might find [transfer.sh](https://transfer.sh/) handy. You upload the pier to the service and then download using `curl` from inside `urbit`. 

Another option, is to manually download the source files and follow the flow that is described in [Getting Started with a planet/star/galaxy keyfile](#getting-started-with-a-planetstargalaxy-keyfile). Instead of placing the keyfile, you will place your `<your_pier_name>` inside the `/home-urbit/urbit/piers` directory, thus: `/home-urbit/urbit/piers/<your_pier_name>`.


## Getting Started without balena

1. Download an OS system (e.g [Raspberry Pi OS](https://www.raspberrypi.org/software/)). **Make sure it's 64-bit**.
2. Flash the image into an sd card.
3. Get terminal access to the machine (e.g using ssh) and [install docker](https://docs.docker.com/engine/install/debian/).
4. Install `git` , run `sudo apt-get install git`
5. Download this repository, run `git clone https://github.com/odyslam/home-urbit`
6. `cd` into the repository
7. run `sudo docker-compose up`
8. After you seee output from the Urbit container that references `localhost`, open a second terminal window. 
9. In the second window, run `sudo docker ps` to find the `ID` of the container that runs `urbit`. 
10. Run `sudo docker exec -it /bin/bash/ <container_ID`. You will get a new terminal inside the container.
11. Run `/usr/sbin/get-urbit-code.sh`. You should see a code on the terminal. That's the password for your ship. Note it down.
12. Type `exit` to exit the shell. 
13. visit `<raspberrypi_IP>` from a browser and enter the code you noted. 
14. Celebrate 🍾


## Configure minio S3 storage

![](/assets/minio.gif)

1. Visit Landscape System Preferences
2. Go to remote storage
3. Add `minio-s3:9000` as the endpoint
4. Add username/password (default are `home-urbit`/`home-urbit`)
5. 🤙

## Standing on the shoulders of giants

- [netdata/netdata](https://github.com/netdata/netdata)
- [balena](https://balena.io)
- [minio](https://min.io/)
- Urbit on Raspberry pi:
    - [Urbit grant](https://grants.urbit.org/proposals/337545546-urbian-a-customized-linux-distribution-for-urbit-appliances?tab=milestones) 
    - [Homepage](https://botter-nidnul.github.io/AArch64_Urbit_Static_Binaries.html)
- Tlon's Urbit docker image:
    - [dockerhub](https://hub.docker.com/r/tloncorp/urbit)
    - [Dockerfile](https://github.com/urbit/urbit/blob/master/nix/pkgs/docker-image/default.nix)

## TODO

- [x]  Add minio service to docker-compose.yml for a local S3 replacement
- [x]  Add support for Deploy with balena 1 click deployment
- [x]  "Design" workflow to move an existing pier into the container
- [x]  Add local nginx with username/password combo for netdata security
- [x]  Add logic to automatically detect PGID and docker/balena socket in Netdata
- [ ]  Add instructions for setting up keys/planets and copying an existing pier
- [ ]  PR this repo to awesome-urbit
- [ ]  Fix reverse proxy for multiple urbits
- [ ]  Add helpful tips (e.g balena tunnel, ssh keys, etc.)
- [ ]  Write blog post


## License

[MIT License](./LICENSE)

## Contributing

Yes, please.
