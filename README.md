# lonelyraids.com

A websocket (flask-socketio) modification to jkingsman's [Nobody.live](https://github.com/jkingsman/Nobody.live) to allow users to perform "raids" (in this case, where users all collectively tune in to the same stream stream) on twitch streamers with zero viewers.

## TODO:
- [x] add websocket to broadcast when a raid is started to all clients 
- [x] add client counter, to show users how many people are joining them on a raid
- [x] add a message to invite friends! what's a raid without your friends...
- [ ] button to override current raid, if majority of current clients hit the button 
- [ ] ABOUT drop down to describe what a raid is and how the button works
- [ ] ~change main button style~

## Architecture

A worker script (`scanner.py`) loops through the Twitch API's list of streams and spins until it inserts all streamers it finds matching the search criteria (default zero viewers), then it starts again. These streamers are inserted as expiring keys on the assumption that someone will view them and then they won't have zero viewers any more so should not be served for too long. If a stream still has zero viewers on a subsequent pass, the insertion into Redis will override the old expiration time and they'll stick around for a while longer.

Environment variables to be set:

* `CLIENT_ID`: Your Twitch application client ID (found at https://dev.twitch.tv/console)
* `CLIENT_SECRET`: Your Twitch application client secret (found at https://dev.twitch.tv/console)

Meanwhile, the Flask app in `app.py` serves the index and the endpoint to get a random streamer.



## Getting Up and Running

* Install and start Redis
* Run the stream fetcher (e.g. `CLIENT_ID=xxxxxx CLIENT_SECRET=xxxxxx scanner.py`). This will need to run continuously.
* Run the flask app (`flask run`)

This is obviously not production ready; you'll need to make sure all services are running as daemons (some config files are included in `etc`) and that your flask app is running safely (e.g. behind gunicorn/nginx/pick your poison).

## Dependencies

Update direct dependencies in `requirements.in`; use `pip-compile` to compile them down to `requirements.txt` if you update them.
