#WRF-Solar Source and Container
This project is to help automate some workflow of compiling and executing [WRF-Solar](https://wiki.ucar.edu/pages/viewpage.action?pageId=321619872).
It is based on the work of [NCAR-Docker-WRF](https://ral.ucar.edu/projects/ncar-docker-wrf).

## Project Structure
The project is composed of
- WRF-Solar 1.2.4 source code (WRFV3 folder)
- WPS-WRF-Solar-v1.2.1 source code
- A customized Dockfile to build docker image which as a prebuilt image [here](http://docker.io)

## How to Use the Project
1. Install [Docker](https://www.docker.com/get-started)

   If you are using Windows, make sure to share drive in Docker settings since we will mount drive to the container later.

2. Docker pull the prebuilt image or build your own.
   ```shell script
   docker pull kongmingqq/wrfsolar
   ```
3. Run the Docker image. Note that we have mounted the home directory to /wrf/host in the container. Add `--rm` option if you need to delete the container.
    ```shell script
    docker run -it --name mysolar --mount type=bind,source=~,target=/wrf/host kongmingqq/wrfsolar
    ```
   You will enter bash shell, use `exit` to leave the container. Next time you may restart the container by issuing the following
   ```shell script
   docker restart mysolar
   docker attach mysolar
   ```
   If you need multiple shell windows, you may do the following after restart
   ```
   docker exec -it mysolar /bin/bash
   ```
4. Load the source into the container. If you already have the source code in your host's home folder, it should be already available
in the container (/wrf/host). Otherwise you can do a git clone of this repo, either inside the container or directly in your host.
   
   ```shell script
   git clone https://github.com/kongmingqq/wrfsolar.git
   ```

##Run Compile Command
Since the Docker container has already setup all the necessary environment variables,
it is straightforward to compile or execute WRF. Note that all the exports in the following
examples can be removed since they have been setup by the container. 

*Compile WRF*

Example command:
```shell script
cd ./WRFV3 \
 && export NETCDF=/wrf/netcdf_links \
 && export JASPERINC=/usr/include/jasper/ \
 && export JASPERLIB=/usr/lib64/ \
 && ./configure <<< $'34\r1\r' \
 && sed -i -e '/^DM_CC/ s/$/ -DMPI2_SUPPORT/' ./configure.wrf \
 && /bin/csh ./compile em_real
```

*Compile WPS*

Example command:
```shell script
cd ./WPS-WRF-Solar-v1.2.1 \
 && export NETCDF=/wrf/netcdf_links \
 && export JASPERINC=/usr/include/jasper/ \
 && export JASPERLIB=/usr/lib64/ \
 && ./configure <<< $'1\r' \
 && sed -i -e 's/-L$(NETCDF)\/lib/-L$(NETCDF)\/lib -lnetcdff /' ./configure.wps \
 && /bin/csh ./compile
```