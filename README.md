My version of Ttorrent, a Java implementation of the BitTorrent protocol by turn
==========================================================

Description
-----------

This is my personal adaptation of turn's (http://www.turn.com) great **Ttorrent** library.
**Ttorrent** is, as described in the original project, "a pure-Java implementation
of the BitTorrent protocol, providing a BitTorrent tracker, a BitTorrent client and the related Torrent
metainfo files creation and parsing capabilities. It is designed to be embedded
into larger applications, but its components can also be used as standalone
programs."

For more information regarding **Ttorrent**s capabilities, have a look a the
original project (http://github.com/turn/ttorrent/).


How to use
----------

The following part is copied 1:1 from the original documentation, so I that I can keep a
cheat-sheet for myself.

### As standalone programs

The client, tracker and torrent file manipulation utilities will all present a
usage message on the console when invoked with the ``-h`` command-line flag.

### As a library

*Thanks to Anatoli Vladev for the code examples in #16.*

#### Client code

```java
// First, instantiate the Client object.
Client client = new Client(
  // This is the interface the client will listen on (you might need something
  // else than localhost here).
  InetAddress.getLocalHost(),

  // Load the torrent from the torrent file and use the given
  // output directory. Partials downloads are automatically recovered.
  SharedTorrent.fromFile(
    new File("/path/to/your.torrent"),
    new File("/path/to/output/directory")));

// You can optionally set download/upload rate limits
// in kB/second. Setting a limit to 0.0 disables rate
// limits.
client.setMaxDownloadRate(50.0);
client.setMaxUploadRate(50.0);

// At this point, can you either call download() to download the torrent and
// stop immediately after...
client.download();

// Or call client.share(...) with a seed time in seconds:
// client.share(3600);
// Which would seed the torrent for an hour after the download is complete.

// Downloading and seeding is done in background threads.
// To wait for this process to finish, call:
client.waitForCompletion();

// At any time you can call client.stop() to interrupt the download.
```

#### Tracker code

```java
// First, instantiate a Tracker object with the port you want it to listen on.
// The default tracker port recommended by the BitTorrent protocol is 6969.
Tracker tracker = new Tracker(new InetSocketAddress(6969));

// Then, for each torrent you wish to announce on this tracker, simply created
// a TrackedTorrent object and pass it to the tracker.announce() method:
FilenameFilter filter = new FilenameFilter() {
  @Override
  public boolean accept(File dir, String name) {
    return name.endsWith(".torrent");
  }
};

for (File f : new File("/path/to/torrent/files").listFiles(filter)) {
  tracker.announce(TrackedTorrent.load(f));
}

// Once done, you just have to start the tracker's main operation loop:
tracker.start();

// You can stop the tracker when you're done with:
tracker.stop();
```

License
-------

The original project is distributed under the Apache Software License version
2.0., hence this project is distributed under the same license conditions.
You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0 .
