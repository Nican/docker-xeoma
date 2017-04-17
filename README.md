# docker-xeoma

This is a Docker container for running [Xeoma](http://felenasoft.com/xeoma/en/), surveillance software developed by Felena Soft. It supports a wide range of security cameras, has low CPU overhead, and a very easy-to-use interface. The container is just for the server, and does not have a user interface. Run the client on any computer and connect to the server on port 8090.

This docker image is available [on Docker Hub](https://hub.docker.com/r/coppit/xeoma/).

You can try out Xeoma using the trial version of the software, then purchase it when you are ready. Note the limitations of the trial version however -- settings aren't saved, and archived videos get deleted after 1 hour. Avoid the free version, as it cannot connect to your container. Make sure you read [the EULA](http://felenasoft.com/xeoma/en/eula/) as you are effectively agreeing to it by running this docker.

## Running

To launch the container:

`docker run -d --name=Xeoma -p 8090:8090 -v /local/path/to/config:/config -v /local/path/to/archive:/archive coppit/xeoma`

When run for the first time, a file named xeoma.conf will be created in the config dir, and the container will exit. Edit this file, setting the client password, and changing `USE_BETA` to "yes" if you want to run the beta version that is in the container. Then rerun the command. If you prefer to set environment variables for your docker container instead of using the configuration file, simply comment out the vars in the xeoma.conf. Note that the file needs to exist, or the container will recreate it.

The archive folder holds the saved video recordings. To access your xeoma server, simply download the same version from [the Xeoma website](http://felenasoft.com/xeoma/en/download/) and set it up to connect to a remote server using the IP address of the docker host and the password you selected. 

See the notes below for special networking considerations depending on your cameras, and for licensing issues.

View logs using:

`docker logs xeoma`

## Notes

### Licensing and Docker Containers

How licensing works is a bit unclear. As of version 16.12.26, the Lite version prohibits running inside virtual machines. Whether (and how!) this applies to docker containers is unclear. Your container may also need continuous internet access to validate the license.

When you register your software, the license will be stored in your config directory. So it will be carried across container updates, along with any configuration changes you made in the app. But if you ever delete the config directory, you might have to contact Felena soft for another registration key.

Be careful about choosing your networking settings before installing your license. If you have registered the software with host or bridged networking, then if you change to the other type of networking, you will see an error message. You should still be able to switch back.

However, if you have any issues, the container will append some information about the MAC address to the file macs.txt each time it starts. If you have trouble getting the license to work, try using the `--mac-address` flag to the run command to force your new container to have the same MAC address as your old one. This will only work if you are using bridged networking.

Finally, if all else fails, [use the felenasoft website for help](http://felenasoft.com/xeoma/en/support/activation-issues/).

### Discovering Cameras

Depending on how your security camera works, you might need to enable host networking by adding `--net=host` to your run command. If you are using IP cameras, you can run this container in bridged networking mode, which is more secure. However, you will need to manually enter the URL for the camera, because the camera search feature probably won't work. You can [consult this website](https://www.ispyconnect.com/sources.aspx) for information about rtsp:// URLs for accessing the camera's low and high quality video streams. 

### Support

If you find any bugs with the software that are related to the docker container, let me know and I'll investigate.  If you find bugs that are related to the actual software or cameras, etc then contact FelenaSoft.

### Automatic Upgrades

Xeoma will automatically detect new versions and update itself. You can use this feature, but just be aware that none of the changes are saved in the container. So if your container is recreated, you will need to reinstall the newer version again. I'll try to keep the container up-to-date as new stable versions are released, so that you can simply update your container.

## Credits

This docker container was initially based on the [jedimonkey/xeoma container](https://github.com/jedimonkey/xeoma-docker).
