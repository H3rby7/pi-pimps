# Kodi

Log info on how to get it work

## Widevine Issue

For Netflix and Amazon Streams.

1. `apt-get install libwidevinecdm0`
2. `sudo cp  /opt/WidevineCdm/_platform_specific/linux_arm/libwidevinecdm.so ~/.kodi/cdm/libwidevinecdm.so`
3. `chown pi:pi libwidevinecdm.so`

It took several attempts before, maybe one might need to go through he whole 'extract widevine from official chrome image' some time before to get all the necessary files.

## Hiding Power Options

1. `sudo cp /usr/share/kodi/addons/skin.estuary/xml/Home.xml /usr/share/kodi/addons/skin.estuary/xml/Home.xml.backup`
2. `cat -n /usr/share/kodi/addons/skin.estuary/xml/Home.xml | grep shutdown` to find the lines linked to the power options
3. `sudo vi  /usr/share/kodi/addons/skin.estuary/xml/Home.xml`
    1. Comment out the Power-Options `<!--<include>...</include>-->`
4. Restart Kodi, verify the Power-Options Button is gone.

