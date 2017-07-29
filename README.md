BOSSA 1.8
---------

BOSSA is a flash programming utility for Atmel's SAM family of flash-based ARM microcontrollers.
The motivation behind BOSSA is to create a simple, easy-to-use, open source utility to replace Atmel's SAM-BA software.
BOSSA is an acronym for Basic Open Source SAM-BA Application to reflect that goal.

The software was created by Scott Shumate with contributions from several
[contributors](https://github.com/shumatech/BOSSA/graphs/contributors).

The software is released under the terms of the BSD license as specified in the LICENSE file.

Supported Device Families
-------------------------
 * SAM7S
 * SAM7SE
 * SAM7X
 * SAM7XC
 * SAM3N
 * SAM3S
 * SAM3U
 * SAM4E
 * SAM4S
 * SAM3X\*
 * SAM3A\*
 * SAM7L\*
 * SAM9XE\*
 * SAMD21\*

\* Device families which are not tested for each release and could stop working.

Do you want to help make sure a device family is tested or do you want to see a new device family added?  Then contribute a development board with a device from that family to the BOSSA project to make it happen.  Contact scott at shumatech.com if you are interested in helping the project.

The following individuals and companies graciously provided development boards to assist the BOSSA project.
 * Atmel Corporation (SAM3N, SAM3S, SAM3U)
 * David Crocker (SAM4E, SAM4S)


Docker
------

Using docker to run a containerized application

Supported Architectures:

 * arm32v7 (e.g. Raspberry Pi 2/3) `akshmakov/bossa:arm32v7`
 * arm32v6 (e.g. Raspberry Pi 1)   `akshmakov/bossa:arm32v6`
 * amd64                           `akshmakov/bossa:latest` `akshmakov/bossa:amd64`


The docker container simply packages the latest bossa build in the environment to run it, all you need to do is provide the port from the host

```
# start a container with shell
$ docker run --rm -it --device="/dev/ttyACM0:/dev/ttyACM0" akshmakov/bossa bash
# run bossac locally, get device info
$ bossac --port=/dev/ttyACM0 --usb-port=true --info
```

You can use this image to download local images

```
# download bin to device
$ docker run --rm -it --device="/dev/ttyACM0:/dev/ttyACM0" \
  	     	      -v "$(pwd):/" akshmakov/bossa        \
	 bossac --port=/dev/ttyACM0 --usb-port=true -i -d  \
	 	-e -w=true -b=test.bin -R
		

```

You can use Docker-compose to simplify this process (RPI Example)

``` docker-compose.yml
version: '2'

services:
  # common service 
  bossa:
    image: akshmakov/bossa:arm32v7
    devices:
      - "/dev/ttyACM0:/dev/ttyACM0"
    environment:
	BOSSAC_OPTS:"--port=/dev/ttyACM0 --usb-port=true"
    volumes:
      - "/my/bin/dir:/"
    
  # entrypoint based use run with just bin name
  # use for downloading different files
  bossac:
   
    image: akshmakov/bossa:arm32v7
    devices:
      - "/dev/ttyACM0:/dev/ttyACM0"
    volumes:
      - "/my/bin/dir:/"
    entrypoint: bossac $BOSSAC_OPTS -i -d -e -w=true -R -b="


  # open a bossash shell
  bossash:
    extends: bossa
    command: bossash
    

  # environment based preconfigured use
  # e.g. production installer
  bossac2:
    extends: bossa
    devices:
      - "/dev/ttyACM1:/dev/ttyACM0"
    environment:
      - PRODUCTION_BIN=prod.bin
    command: bossac $BOSSAC_OPTS  -i -d -e -w=true -R -b=$PRODUCTION_BIN


```

Execute a sequence of production steps

```
# program test.bin to ACM0 programmer port
$ docker-compose run bossac test1.bin
# open a bossash 
$ docker-compose run bossash
# program production image to ACM1
$ docker-compose run bossac2
# different production image
$ docker-compose run -e "PRODUCTION_BIN=prod_beta.bin" bossac2
```


Many other DevOPS combinations are possible.


    