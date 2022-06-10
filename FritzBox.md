# Pi behind a FritzBox!

DynDNS?

## Freeing Port 80

Some FritzBox models provide their own WebServer to access and change settings. 
That Webserver usually runs on Port 80, which we may want to forward to our Pi.

Following up is some things I have tried.

***DANGERZONE: No warranty this works for you and does not break anything***

1. I used the FritzBox UI to make an 'export' of my settings.
2. I Made a copy of the export, so I had a backup available
3. In the export file, in the the `webrv` section I changed the value of `port` to a different port.
4. I used https://github.com/H3rby7/fb-docker, a dockerized version of [fb tools](http://mengelke.de/.dg), to re-calculate the edited file's checksum
5. I used the Web-UI to "restore settings from a backup" (my edited file). I chose 'restore All options'.
6. I rebooted the box and the Web-UI was available under the other port.
