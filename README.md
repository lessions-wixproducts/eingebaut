# Eingebaut is for video playback

Eingebaut is a JavaScript library built to enable simple cross-browser video playback. It supports the vast majority of both desktop and mobile browsers. And it does both on-demand and live video.

Behind the scenes, the library will use either HTML5 `<video>` or Adobe Flash to display the video and its poster/thumbnail. As a programmer though, you will be able to interact with the video in a single JavaScript API which exposes relevant methods and events. Eingebaut also has a consistent way of working with full screen, whether through HTML5 or Flash.

# How can I use it?

To play back video, initialize an Eingebaut object:

    var eingebaut = new Eingebaut(container, device, swfLocation, callback);

The simple example of starting video playback might look like this:

    <div id="video" style="width:640px; height:360px"></div>
    <script>
      var eingebaut = new Eingebaut('#video', 'html5', 'Eingebaut.swf', function(event){
          if(event=='loaded' && eingebaut.displayDevice=='none') {
            alert('Eingebaut could find no way of playing video in your browser');
          } else {
            console.debug('Eingebaut callback:', event);
          }
        });
      eingebaut.load();
      eingebaut.setSource('my-video.mp4');
      eingebaut.setPoster('my-video-poster.jpg');
      eingebaut.setPlaying(true);
    </script>

# Boostrapping Eingebaut

The initialization through `new Eingebaut(...)` takes four arguments:

* `container`: A jQuery object or a CSS selector for the element into which you want the video display to be placed. 
* `device`: Your prefered display device. Options are `html5` and `flash`. If the prefered device is not supported by the browser, the alternative is selected.
* `swfLocation`: The relative location of the file *Eingebaut.swf*, which is used for Flash fallback.
* `callback`: A function called when an event occurs during video load, playback and full screen. The function takes a single argument, `event`, which is (logically) a string describing the current event (see full documentation below).

# Methods

* `.load()`: Load the video player of choice. (This is separated out from the initialization because the Flash display requires  the player container to be loaded into the DOM. There are use cases where this might not be the case upon initilization, but it must be on load in order for Eingebaut to work.)
* `.setSource(source, startTime)`: Set the URL of a video source and optionally a `startTime` in seconds if you want to jump in to the video. The latter option may require support for HTTP pseudo-streaming through `?start=<startTime>` by the server.
* `.getSource()`: Get the video source.
* `.setPoster(poster)`: Set a thumbnail/poster for the video to display before playback.
* `.getPoster()`: Get the URL of the thumbnail/poster.
* `.setPlaying(playing)`: Set playback status. If `playing` is true, the video will start playing.
* `.getPlaying()`: Returns a boolean value indicating whether the video is currently playing.
* `.setPaused(paused)`: Set playback status. If `paused` is true, the video will pause.
* `.getPaused()`: Returns a boolean value indicating whether the video is currently paused.
* `.setCurrentTime(currentTime)`: Seek to `currentTime`.
* `.getCurrentTime()`: Returns the current playhead time.
* `.getEnded()`: Returns a boolean value indicating whether the video has been played to its ending. 
* `.getSeeking()`: Returns a boolean value indicating whether the video is currently seeking.
* `.getStalled()`: Returns a boolean value indicating whether the video is currently stalled.
* `.getDuration()`: Get the duration, in seconds, of the video.
* `.getBufferTime()`: Get the buffer time, in scconds, of where the video has buffered to. (This is different from the usual `bufferTime` value in Flash, which specifies the programmer's preference for the optimal length of the buffer. For HTML5, Eingebaut doesn't expose the entirety of different buffer ranges, and opts for just showing the currently relevant one.)
* `.setVolume(volume)`: Set the volume, between 0 (mute) and 1 (maximum rock and roll). 11 will also work.
* `.getVolume()`: Get the volume.
* `.getIsLive()`: Returns a boolean value indicating whether the video source is a live stream.
* `.canPlayType(type)`: Tests whether a give mime type can be played by the current `displayDevice` (see below)
* `.hasFullscreen()`: Does the playback device support switching to full screen?
* `.isFullscreen()`: Is the video currently being displayed in full screen?
* `.enterFullscreen()`: Switch to full screen, if available. (On Flash this will display a Flash-native popup prompting confirmation for entering full screen. This is required because this switch cannot be initiated in code alone, but must be the direct consequence of a user mouse click interaction. See the relavant full screen events as well.)
* `.leaveFullscreen()`: Leave full screen, if available. 
* `.supportsVolumeChange()`: Does the playback device support changing volume through JavaScript?
* `.allowHiddenControls()`: Does the playback device support showing the `<video>` element without any native user interface? This covers the case of the iPhone, where HTML5 video elements will always have native play button overlayed. Eingebaut handles this annoyance by hiding the `<video>` element until playback, and is instead just showing the specified poster through a normal `<img>`.

# Video Codecs
Generally, HTML5 (`displayDevice="html5"`) will be able to play:

* `video/mp4; codecs="avc1.42E01E"`: On-demand mpeg-4/h.264 video, supported Safari, Chrome and IE if you're lucky.
* `video/webm`: On-demand WebM/VP8 video, supported by Chrome, Firefox and Opera.
* `application/vnd.apple.mpegurl`: Apple HTTP Live Streaming, usually mpeg-4/h.264 live video, supported by Safari.

Flash/OSMF (`displayDevice="flash"`) will play:

* `video/mp4; codecs="avc1.42E01E"`: On-demand mpeg-4/h.264 video, supported by Flash 10.2 (actually by 9.0.115, but Eingebaut requires 10.2 due to it use of OSMF behind the scenes.)
* `application/f4m+xml`: Adobe HTTP Dynamic Streaming, usually also mpeg-4/h.264 live video, supported by Adobe Flash.


# Callbacks

* `loaded`: Eingebaut was loaded.
* `ready`: The player is ready to interact with.
* `canplay`: The selected video is ready to play.
* `progress`: Indicates buffering progress (`bufferTime` has changed).
* `timeupdate`: Playhead time (`currentTime`) has changed.
* `seeked`: The video was seeked to a different postion.
* `play`: The video has been set to play.
* `playing`: The video has started playing.
* `pause`: The video has been paused.
* `loadedmetadata`: The display device loaded meta data on the video.
* `ended`: The video has been played to its end.
* `volumechange` : The volume has been changed.
* `enterfullscreen` : The player has entered fullscreen
* `leavefullscreen` : The player has exited fullscreen
* `fullscreenprompt` : The `flash` display device is displaying a Flash-native popup prompting for click to enter fullscreen.
* `clearfullscreenprompt` : The Flash-native popup prompting for click to enter fullscreen has been cleared.

# Dependencies and Supported Browsers/Devices

Eingebaut has been tested and works in Internet Explorer 6+, Safari 4+, Opera 9+ and Firefox 3+ along with Mobile Safari on all recent versions of iOS for iPad and iPhone.

The project depends on [jQuery](http://jquery.com/) and [SWFObject](http://code.google.com/p/swfobject/). The Flash fallback object uses a [23 fork](https://github.com/23/HLSprovider) of [HLSprovider](https://github.com/mangui/HLSprovider), but this is only needed to compile.
