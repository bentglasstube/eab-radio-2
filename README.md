# Eat a Brick Radio 2.0

A distributed cluster of MPD streams to create an internet radio station

## Architecture

There are three different components that combine to create the radio station.

The first we'll call the Streamer.  This is what actually streams the music
for the station.  Only one is required to run the station, but multiple
streamers will make the station more robust to downtime at individual
endpoints.  These use the most bandwidth of anything so it is recommended to
run them from locations where bandwidth is not metered, such as your home.
These will also require local access to the media library, so hard disk space
should be plentiful.

The second component is the Controller.  This manages the streamers to make
sure they are all playing the same music at the same time and allows clients
to connect to the streamers as though they were a single MPD instance.  It
also manages which streamers are active and synchronizes the music collection
between them to ensure all songs appear on all streamers.

The third and final component is the WebServer.  This is just a web interface
to listen to the radio station and is not really required for a functional
station, but it does give a nice way for people to listen to the station if
they are unable or unwilling to install anything besides a browser.

## Streamer

These are basically MPD instances running with a preset configuration and a
way for rsync to access the media library.  They have a bit of additional
software to communicate with the controller.

Ideally, they will be a contained computer running all the software since I
want to just give one of these to Steve and have him plug it in at his house.
To that effect, there are some considerations:

 * NAT/Firewall    - Will have to manually configure
 * Updates         - SSH access for operator to do updates
 * Troubleshooting - Embedded LCD display to show issues

## Controller

Firstly, this will have to act as a sort of MPD proxy.  Perhaps there is some
software to manage that already.  If not, it should be fairly easy to
implement.

Secondly, it will need an API to communicate with the web server and
streamers.  Here is an outline of some of the API's functions:

  * get_stream_url      - return a url for an active streamer instance
  * get_stremers        - return a list of active streamer instances
  * register_streamer   - register a new streamer instance
  * unregister_streamer - unregister a streamer instance (optional)

## Web Server

This should basically be like the original eatabrick radio website, but with
fewer features.  Playlist management will be removed as well as song uploads.

Ideally it will show a list of the recent songs and upcoming songs and have a
built in player.  A skip song feature might be added with some kind of
rudimentary rate limiting to prevent lazy abuse.

## Considerations

The controller and the web server will most likely be run on the same physical
machine, probably hosted as a VPS or something.

I have not yet decided on how to implement the addition of new music to the
system.  Ideally the controller will be in charge of this somehow.  With SSH
access to the streamers, synchronizing the music will be straightforward.
