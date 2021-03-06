# Friendly Cast

If you've ever tried using the official Google Cast API (Chromecast API), you know my frustration. It's very verbose and unintuitive, and there is no good reason for it. After about a day of working with this API, I had enough and decided to make my life a bit easier. There is no reason such a simple platform should have such a high barrier to entry. Now, we can all have a modern JavaScript API for the Chromecast. 

## Disclaimer

In similarity with Google, this API wrapper is a developer preview. It was made in only a few hours, after spending an entire frustrating day trying to work with the official API. I am actively breaking this right now, as you read this. Don't worry, it will be stable soon

The events and errors need more work, as I am not certain what they all mean at the moment. Further, I will need to do a bit more work to fully support custom receivers.

If you are a Chromecast veteran, or have any ideas or request, feel free to submit a issue or pull request, or contact me on [Google+](https://www.google.com/+KirilVatev).

## How to use it

Load both this library and the official Google Cast API, as such

    <script src="chromecast.js"></script>
    <script type="text/javascript" src="https://www.gstatic.com/cv/js/sender/v1/cast_sender.js"></script>

No more waiting for the Cast API to be ready/initialize/etc. After the scripts are loaded, you are ready to go. The Friendly Cast wrapper will handle the hard work of initializing everything. To start, provide your `applicationID` if you have a [registered receiver](https://developers.google.com/cast/docs/registration), or leave it blank to use the default media player:

    chromecast.setAppId();
    
    // OR
    
    var applicationID = 'xxxxxxxxxxxxxxxxxxxxxxx';
    chromecast.setAppId(applicationID);
    
Yes, folks, it's that easy. When the user tells you which video to play, just provide the URL to the Friendly Cast wrapper, and you are done:

    var urlString = 'http://server/path/to/video.mp4';
    chromecast.startCast(urlString);

## Media controls

Controlling the playing media is pretty simple as well:

    chromecast.control.pause();
    chromecast.control.play();
    
Seeking is done in seconds, relative to the beginning of the video. To seek to 5 minutes into the video:

    var time = 5 * 60;
    chromecast.control.seek(time);
    
Stopping the video is also simple. However, note that stopping the video causes you to lose the media object, and you will need to play a new video to continue:

    chromecase.control.stop();
    
    //this will do nothing
    chromecast.control.play();
    
    //play a new video (or the same one again)
    chromecast.startCast(urlString);
    
There are some auxilary methods in the media control object as well:

    chromecast.control.duration();
    //returns the total time in seconds
    
    chromecast.control.time();
    //return the current time
    //Note: this doesn't always work, I don't know why
    
    chromecast.control.status();
    //return the current status of the player
    //this will be the same status as the default Google Cast API

## Events

Rather than providing a million callback functions every step of the way, Friendly Cast emits events using a familiar on/off system.

    chromecast.on('available', function(){
        var urlString = 'http://server/path/to/video.mp4';
        chromecast.startCast(urlString);
    });
    
To remove and event, pass in the name and original function:

    var doSomething = function(){ ... };
    
    //subscribe to an event
    chromecast.on('available', doSomething);
    
    //unsubscribe to the event
    chromecast.off('available', doSomething);
    
    //subscribe and fire only once
    chromecast.once('available', doSomething);
    
To find out all of the available events, you can check out the `chromecast.Events` enumerable in the Chrome console. I also suggest you use this enumerable to subscribe to events:

    chromecast.on(chromecast.Events.ready, function(){
        //the Google Cast API is available
    });
    
More documentation will come on events as I have it.

## Errors

The Google Cast API has quite a few errors it provides. I have standardized them all under teh `error` event:

    chromecast.on(chromecast.Events.error, function(err){
        err.message; // corresponds to the closest equivalent name of the Google Cast API
        err.permanent; //if this is true, Google Cast has had a permanent error, requiring a page refresh
        err.args; //this is the original error arguments as provided by the Google Cast API
    });
    
More documentation will come on errors as I have it.

## What next?

Sit tight for media controls, queues, etc. In the meantime, you can still use the regular Google Cast API to continue, or use the `chromecast.session` object to access the current session.

## License

All code here is licensed under the MIT X11 License.