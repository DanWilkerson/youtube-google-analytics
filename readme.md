# YouTube Google Analytics & GTM Plugin

This is a plug-and-play tracking solution for tracking user interaction with YouTube videos in Google Analytics. It will detect if GTM, Universal Analytics, or Classic Analytics is installed on the page, in that order, and use the first syntax it matches unless configured otherwise. It include support for delivering hits directly to Universal or Classic Google Analytics, or for pushing Data Layer events to be used by Google Tag Manager.

Once installed, the plugin will fire events with the following settings:
- Event Category: Videos
- Event Action: *&lt;Action, e.g. Play, Pause&gt;*
- Event Label: *&lt;URL of the video&gt;*

## Installation

This plugin will play nicely with any other existing plugin that interfaces with the YouTube Iframe API, so long as it is loaded after any existing code. Otherwise, if another function overwrites the window.onYouTubeIframeAPIReady property, it will fail silently. If you're seeing strange errors like 'getVideoUrl' is not a function, there is another script causing a collision that you must remedy.

### Universal or Classic Google Analytics Installation:

The plugin is designed to be plug-and-play. By default, the plugin will try and detect if you have Google Tag Manager, Universal Analytics, or Classic Google Analytics, and use the appropriate syntax for the event. If you are **not** using Google Tag Manager to fire your Google Analytics code, store the plugin on your server and include the lunametrics-youtube-v7.gtm.js script file somewhere on the page.

    <script src="/somewhere-on-your-server/lunametrics-youtube-v7.gtm.js"></script>

### Google Tag Manager Installation
Create a new Custom HTML tag and paste in the below:

    <script type="text/javascript" id="gtm-youtube-tracking">
      // script file contents go here
    </script>

In the space between the **&lt;script&gt;** and **&lt;/script&gt;** tags, paste in the contents of the lunametrics-youtube-v7.gtm.js script, found [here](https://raw.githubusercontent.com/lunametrics/youtube-google-analytics/master/lunametrics-youtube-v7.gtm.js).

**You need to add a Google Analytics Event tag that acts upon the pushes to the Data Layer the plugin executes.** Follow the steps in the [Google Tag Manager Configuration](#google-tag-manager-configuration) section for help on getting this set up.

## Configuration

### Default Configuration
By default, the script will attempt to fire events when users Play, Pause, Watch to End, and watch 10%, 25%, 50%, 75%, and 90% of each video on the page it is loaded into. These defaults can be adjusted by modifying the object passed as the third argument to the script, at the bottom.

### Player Interaction Events
By default, the script will fire events when users interact with the player by:

- Playing
- Pausing
- Watching to the end

To change which events are fired, edit the events property of the configuration at the end of the script. For example, if you'd like to fire Buffering events:

    ( function( document, window, config ) {
    
       // ... the tracking code

    } )( document, window, {
      'events': {
        'Play': true,
        'Pause': true,
        'Watch to End': true,
        'Buffering': true,
        'Unstarted': false,
        'Cued': false
      }
    } );

The available events are **Play, Pause, Watch to End, Buffering, Unstarted, and Cueing**.

### Percentage Viewed Events

By default, the script will track 10%, 25%, 50%, 75%, and 90% view completion. You can adjust this by changing the percentageTracking.each and percentageTracking.every values.

####percentageTracking.each
For each number in the array passed to this configuration, a percentage viewed event will fire.

    ( function( document, window, config ) {
    
       // ... the tracking code

    } )( document, window, {
      'percentageTracking': {
        'every': 25  // Tracks every 25% viewed
      }
    } );

####percentageTracking.every
For every n%, where n is the value of percentageTracking.every, a percentage viewed event will fire.

    ( function( document, window, config ) {
    
       // ... the tracking code

    } )( document, window, {
      'percentageTracking': {
        'each': [10, 90]  // Tracks when 10% of the video and 90% of the video has been viewed
      }
    } );

**NOTE**: Google Analytics has a 500 hit per-session limitation, as well as a 20 hit window that replenishes at 2 hits per second. For that reason, it is HIGHLY INADVISABLE to track every 1% of video viewed.

### Forcing Universal or Classic Analytics Instead of GTM

By default, the plugin will try and fire Data Layer events, then fallback to Univeral Analytics events, then fallback to Classic Analytics events. If you want to force the script to use a particular syntax for your events, you can set the 'forceSyntax' property of the configuration object to an integer:
    
    ( function( document, window, config ) {
    
       // ... the tracking code

    } )( document, window, {
      'forceSyntax': 1
    } );

Setting this value to 0 will force the script to use Google Tag Manager events, setting it 1 will force it to use Universal Google Analytics events, and setting it to 2 will force it to use Classic Google Analytics events.

### Using A Custom Data Layer Name (GTM Only)
If you're using a name for your dataLayer object other than 'dataLayer', you must configure the script to push the data into the correct place. Otherwise, it will try Universal Analytics directly, then Classic Analytics, and then fail silently.

    ( function( document, window, config ) {
    
       // ... the tracking code

    } )( document, window, {
      'dataLayerName': 'customDataLayerName'
    } );

## Google Tag Manager Configuration

Once you've added the script to your container (see [Google Tag Manager Installation](#google-tag-manager-installation)), you must configure Google Tag Manager.

Create the following Variables:

* Variable Name: videoUrl
    - Variable Type: Data Layer Variable
    - Data Layer Variable Name: attributes.videoUrl
    - This will be the URL of the video on YouTube

* Variable Name: videoAction
    - Variable Type: Data Layer Variable
    - Data Layer Variable Name: attributes.videoAction
    - This will be the action the user has taken, e.g. Play, Pause, or Watch to End

Create the following Trigger:

* Trigger Name: YouTube Video Event
    - Trigger Type: Custom Event
    - Event Name: youTubeTrack

Create your Google Analytics Event tag

* Tag Type: Google Analytics
    - Choose a Tag Type: Universal Analytics (or Classic Analytics, if you are still using that)
    - Tracking ID: *&lt;Enter in your Google Analytics tracking ID&gt;*
    - Track Type: Event
    - Category: Videos
    - Action: {{videoAction}}
    - Label: {{videoUrl}}
    - Fire On: More
        - Choose from existing Triggers: YouTube Video Event

## License

Licensed under the Creative Commons 4.0 International Public License. Refer to the LICENSE.MD file in the repository for the complete text of the license.

## Acknowledgements

Created by the honest folks at [LunaMetrics](http://www.lunametrics.com/), a digital marketing & Google Analytics consultancy. For questions, please drop us a line here or [on our blog](http://www.lunametrics.com/blog/2015/05/11/updated-youtube-tracking-google-analytics-gtm/).

Written by [Sayf Sharif](https://twitter.com/sayfsharif) and updated by [Dan Wilkerson](https://twitter.com/notdanwilkerson).
