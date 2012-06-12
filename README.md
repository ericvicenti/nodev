# nodev

Assists with the running and debugging of node.js based applications in development. nodev launches node-inspector alongside your app, and will reload everything when files change. nodev comes with a site-specific-browser for node-inspector on OSX, which connects with nodev via Socket.IO to reload itself dynamically. nodev is forked from [remy's nodemon](https://github.com/remy/nodemon).

nodev will watch the files in the directory that nodev was started, and if they change, it will automatically restart your node application. nodev automatically launches node-inspector in the background, and launches node in debug mode.

nodev does **not** require *any* changes to your code or method of development. nodev simply wraps your node application and keeps an eye on any files that have changed. It also maintains the debugger server and makes sure node is run in debug mode. Remember that nodev is a replacement wrapper for `node`, think of it as replacing the word "node" on the command line when you run your script.

Nodev is designed explicitly for use while developing your app. It is not meant to be used in production.


# Installation

Either through forking or by using [npm](http://npmjs.org) (the recommended way):

    npm install nodev -g
    
And nodev will be installed in to your bin path. Note that as of npm v1, you must explicitly tell npm to install globally as nodev is a command line utility.

# Usage

nodev wraps your application, so you can pass all the arguments you would normally pass to your app:

    nodev [your node app]

For example, if my application accepted a host and port as the arguments, I would start it as so:

    nodev ./server.js localhost 8080

Any output from this script is prefixed with `[nodev]`, otherwise all output from your application, errors included, will be echoed out as expected.

Once nodev is started, you can debug by using the included site-specific-browser for OSX, or by browsing to

	http://localhost:5801/debug?port=5858

nodev also supports running and monitoring [coffee-script](http://jashkenas.github.com/coffee-script/) apps:

    nodev server.coffee

If no script is given, nodev will test for a `package.json` file and if found, will run the file associated with the *main* property ([ref](https://github.com/remy/nodev/issues/14)).

If you have a `package.json` file for your app, you can omit the main script entirely and nodev will read the `package.json` for the `main` property and use that value as the app.

# Automatic re-running

nodev (then nodemon) was original written to restart hanging processes such as web servers, but now supports apps that cleanly exit. If your script exits cleanly, nodev will continue to monitor the directory (or directories) and restart the script if there are any changes.

# Running non-node scripts

nodev can also be used to execute and monitor other programs. nodev will read the file extension of the script being run and monitor that extension instead of .js if there's no .nodevignore:

    nodev --exec "python -v" ./app.py

Now nodev will run `app.py` with python in verbose mode (note that if you're not passing args to the exec program, you don't need the quotes), and look for new or modified files with the `.py` extension.

# Monitoring multiple directories

By default nodev monitors the current working directory. If you want to take control of that option, use the `--watch` option to add specific paths:

    nodev --watch app --watch libs app/server.js

Now nodev will only restart if there are changes in the `./app` or `./libs` directory. By default nodev will traverse sub-directories, so there's no need in explicitly including sub-directories.

# Delaying restarting

In some situations, you may want to wait until a number of files have changed. The timeout before checking for new file changes is 1 second. If you're uploading a number of files and it's taking some number of seconds, this could cause your app to restart multiple time unnecessarily.

To add an extra throttle, or delay restarting, use the `--delay` command:

    nodev --delay 10 server.js

The delay figure is number of seconds to delay before restarting. So nodev will only restart your app the given number of seconds after the *last* file change.

# Ignoring files

By default, if nodev will only restart when a `.js` or `.cs` file changes.  In some cases you will want to ignore some specific files, directories or file patterns, to prevent nodev from prematurely restarting your application.

You can use the [example ignore file](http://github.com/ericvicenti/nodev/blob/master/nodevignore.example) (note that this example file is not hidden - you must rename it to `.nodevignore`) as a basis for your nodev, but it's very simple to create your own:

    # this is my ignore file with a nice comment at the top
    
    /vendor/*     # ignore all external submodules
    /public/*     # static files
    ./README.md   # a specific file
    *.css         # ignore any CSS files too

The ignore file accepts:

* Comments starting with a `#` symbol
* Blank lines
* Specific files
* File patterns (this is converted to a regex, so you have full control of the pattern)

# Controlling shutdown of your script

nodev sends a kill signal to your application when it sees a file update. If you need to clean up on shutdown inside your script you can capture the kill signal and handle it yourself.

The following example will listen once for the `SIGUSR2` signal (used by nodev to restart), run the clean up process and then kill itself for nodev to continue control:

    process.once('SIGUSR2', function () {
      gracefulShutdown(function () {
        process.kill(process.pid, 'SIGUSR2'); 
      })
    });

Note that the `process.kill` is *only* called once your shutdown jobs are complete. Hat tip to [Benjie Gillam](http://www.benjiegillam.com/2011/08/node-js-clean-restart-and-faster-development-with-nodev/) for writing technique this up.


# Using nodev with forever

If you're using nodev with [forever](https://github.com/nodejitsu/forever) you can combine the two together. This way if the script crashes, forever restarts the script, and if there are file changes, nodev restarts your script. For more detail, see [nodemon issue 30](https://github.com/remy/nodemon/issues/30).

To acheive this you need to include the `--exitcrash` flag to ensure nodev exits if the script crashes (or exits unexpectedly):

    forever nodev --exitcrash server.js

To test this, you can kill the server.js process and forever will restart it. If you `touch server.js` nodev will restart it.

Note that I *would not* recommend using nodev in a production environment - but that's because I wouldn't want it restart without my explicit instruction.

# Help! My changes aren't being detected!

nodev has three potential methods it uses to look for file changes. First, it polls using the find command to search for files modified within the last second. This method works on systems with a BSD based find (Mac, for example). 

Next it tries using node's fs.watch. fs.watch will not always work however, and nodev will try and detect if this is the case by writing a file to the tmp directory and seeing if fs.watch is triggered when it's removed. If nodev finds that fs.watch was not triggered, it will then fall back to the third method (called legacy watch), which works by statting each file in your working directory looking for changes to the last modified time. This is the most cpu intensive method, but it may be the only option on some systems.

In certain cases, like when where you are working on a different drive than your tmp directory is on, fs.watch may give you a false positive. You can force nodev to start using the most compatible legacy method by passing the -L switch, e.g. `nodev -L /my/odd/file.js`.

# License

MIT [http://rem.mit-license.org](http://rem.mit-license.org)
