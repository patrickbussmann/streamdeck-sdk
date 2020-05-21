# Stream Deck SDK

This project is a **not official** SDK for Elgato's Stream Deck.

The idea behind was that there are few "files" or projects which want providing an layer for the Stream Deck.
But I want to develop some plugins as easy as possible and as fast as possible.

## Features

- Automatic setting management (Loading & Saving values in Property Inspector with 2 lines of code)
- Setting image easily via URL
- Drawing management (Canvas)
- HTTP Module (http.get/http.post/... easily instead of fetch)

# Installation

    $ npm install streamdeck-sdk

After that add the script to header of your html files. (i.e. `plugin.html`/`index.html` and `property-inspector.html`)

    <script src="node_modules/streamdeck-sdk/dist/StreamDeckSDK.js"></script>

When you downloaded only the `StreamDeckSDK.js` you can easily add them, too.

    <script src="StreamDeckSDK.js"></script>

# Registration Procedure

Read the steps for the plugin and for the property inspector.

## Plugin

The plugin is easily load via:

    <script>
        var plugin = new StreamDeckPlugin();
    </script>

## Property Inspector

The property inspector is easily load via:

    <script>
        var propertyInspector = new StreamDeckPropertyInspector();
    </script>

### Automatic setting management

For enabling the property inspector setting management you must add this line to your `<script>`:

    propertyInspector.enableSettingManager();

Behind the scene it loops all your `input`, `textarea` and `select` elements.
Then it checks for the `name` or `id` attribute and if it's already set in the `settings` of your Stream Deck.

If yes, it automatically set's the `value` of your element to the stored value.
On change event it will automatically store the value.
And in case of closing the property inspector there is a listener for the `beforeunload` event.
(which sends the settings to the plugin which stores it then automatically)

You can disable the automatic loading and saving for single elements by adding the `sdk-ignore` class.

This logic supports also `file` inputs.
You can show the `file` then in the plugin via `instance.setImageURL(instance.settings.myname)`.

### Example: Simple counter (with stored and changable value)

This demonstrates how easy you can develop a plugin with property inspector view.

_Do not forget to add the StreamDeckSDK.js in plugin and property inspector files as described in Installation!_

#### Plugin

The plugin shows a number (counter) and on each key press (keyUp) it will count the number plus one.
But when we got a new value from property inspector we also want to use this number.

    // First we initialize the Stream Deck Plugin
	var plugin = new StreamDeckPlugin();
	// Then we listen to the initialization process
	plugin.on('init', function(event) {
	    // Each init gives one instance (instance = 1 button on the Stream Deck)
	    // Instances having an `action` which you can check when you have multiple actions!
	    // i.e. if (instance.action === 'org.examle.firstaction') { }
		var instance = event.detail.instance;
		// Then we setting the title of the instance to the counter - else 1
		instance.setTitle((+instance.settings.counter) || 1);
		// Now we're going to listen for keyUp, so whenever we press the button and release this is called
		instance.on('keyUp', function() {
		    // Lets get the current value and count + 1
		    var count = ((+instance.settings.counter) || 1) + 1;
		    // Now lets set the title of our instance to the number
            instance.setTitle(count);
            // And then lets save the new number
            instance.setSetting('counter', count);
		});
		// We want also to listen for changed settings from the property inspector
		instance.on('didReceiveSettings', function() {
		    // So when property inspector changes the counter value set the title to it
            instance.setTitle(instance.settings.counter || 1);
		});
	});

#### Property Inspector

In the property inspector we add the element with the input to the `<body>`.

    <div class="sdpi-item">
        <div class="sdpi-item-label">Counter Value</div>
        <input class="sdpi-item-value" name="counter" />
    </div>

And in the `<script>` (maybe before closing `</body>`) we add our logic.

    <script>
        var plugin = new StreamDeckPropertyInspector();
        plugin.enableSettingManager();
    </script>

#### Done

Et voilà - we created a simple logic which sends the counter value to the plugin which shows it.
And the plugin count it up on each keypress.
The property inspector will also be updated on each `keyUp` because it's listening to the setting change event.

# Build

Run `npm run build` to build the project. The build artifacts will be stored in the `dist/` directory.

# Sample plugins

* Coming May 2020: Toggl
* Coming May 2020: INSTAR Camera Viewer

# Contributing

Feel free to open issues, pull requests or something else.

When you need a plugin feel free to request it via the issues.
