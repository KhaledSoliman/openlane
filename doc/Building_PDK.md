# Prerequisites:
 - [Magic VLSI Layout Tool](http://opencircuitdesign.com/magic/index.html) is needed to run open_pdks -- version >= 8.3.60

# Quick-start:

To build the PDK using the OpenLANE Makefile, run:
```bash
    export PDK_ROOT=<absolute path to where skywater-pdk and open_pdks will reside>
    make manual-pdk
```

# Manually Setting up the PDK: skywater-pdk

- Clone and build at least one [skywater-pdk](https://github.com/google/skywater-pdk) standard cell Library inside the pdks directory:
    - To setup one standard cell library only

    ```bash
        export PDK_ROOT=<absolute path to where skywater-pdk and open_pdks will reside>
        cd  $PDK_ROOT
        git clone https://github.com/google/skywater-pdk.git
        cd skywater-pdk
        git checkout bd7b0f6a274a4cec839023a5b94b5b216a8d9231
        git submodule update --init libraries/sky130_fd_sc_hd/latest
        make sky130_fd_sc_hd
    ```
    - To setup other SCLs:
        - replace sky130_fd_sc_hd with any of the following list:
            - sky130_fd_sc_hs
            - sky130_fd_sc_ms
            - sky130_fd_sc_ls
            - sky130_fd_sc_hdll

- Setup the configurations and tech files for Magic, Netgen, OpenLANE using [open_pdks](https://github.com/RTimothyEdwards/open_pdks):

    ```bash
        cd $PDK_ROOT
	    git clone https://github.com/RTimothyEdwards/open_pdks.git
        cd open_pdks
        git checkout 48db3e1a428ae16f5d4c86e0b7679656cf8afe3d
        ./configure --with-sky130-source=$PDK_ROOT/skywater-pdk/libraries --with-sky130-local-path=$PDK_ROOT
		cd sky130
		make
		make install-local
    ```

**Note**: You can use different directories for sky130-source and local-path. However, in the instructions we are using $PDK_ROOT to facilitate the installation process

Alternatively you can use [this repo](https://github.com/efabless/sky130A-prebuilt) and use the instructions there to reproduce the PDK on the same or a different commit/version.

# Running OpenLANE with the Manually Built PDK

## Running the Locally Built Docker Image

Issue the following command to open the docker container from /path/to/openlane to ensure that the output files persist after exiting the container:

```bash
    docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) openlane:rc4
```

## Running the Pulled Auto-Built Docker Image
If you pulled the docker image from dockerhub instead of building it locally, then run the following command:

```bash
    export IMAGE_NAME=efabless/openlane:rc4
    docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) $IMAGE_NAME
```
