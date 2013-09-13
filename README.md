# nodev - (now deprecated)

I no longer have time to maintain nodev, so I cannot reccomend anyone to start using it. Personally, I have not used node-inspector for some time because of the lack of community support.

If you would like to assist in maintaining nodev, I would be happy to help facilitate that.

## Summary

Assists with the running and debugging of node.js based applications in development. nodev launches node-inspector alongside your app, and will reload everything when files change. nodev comes with a site-specific-browser for node-inspector on OSX, which connects with nodev via Socket.IO to reload itself dynamically. nodev is forked from [remy's nodemon](https://github.com/remy/nodemon).

I built this because, while I loved node-inspector, it was a total pain to reload. I found that I could develop much faster when the reload cycle of my development environment was quick and automaed. Hopefully everybody can enjoy these benefits when developing with node. Nodemon did a great job of reloading a node app, but didn't handle node-inspector, so I forked nodemon and nodev was born to achieve that purpose.

nodev will watch the files in the directory that nodev was started, and if they change, it will automatically restart your node application. nodev automatically launches node-inspector in the background, and launches node in debug mode.

nodev does **not** require *any* changes to your code or method of development. nodev simply wraps your node application and keeps an eye on any files that have changed. It also maintains the debugger server and makes sure node is run in debug mode. Remember that nodev is a replacement wrapper for `node`, think of it as replacing the word "node" on the command line when you run your script. When nodev starts, you can access the debugger at `http://your_server:5801/debug?port=5858`

Nodev is designed explicitly for use while developing your app. It is not meant to be used in production.

# Installation

Either through forking or by using [npm](http://npmjs.org) (the recommended way):

    npm install nodev -g
    
And nodev will be installed in to your bin path. Note that as of npm v1, you must explicitly tell npm to install globally as nodev is a command line utility.

# Usage

Nodev wraps your application, so you can pass all the arguments you would normally pass to your app. Instead of calling `node`, call `nodev`:

    nodev [your node app] [app arguments]

Any output from this script is prefixed with `[nodev]`, otherwise all output from your application, errors included, will be echoed out as expected.

	http://localhost:5801/debug?port=5858

For more documentation and usage instructions, see [remy's nodemon](https://github.com/remy/nodemon).

# License

MIT [http://opensource.org/licenses/MIT](http://opensource.org/licenses/MIT)
