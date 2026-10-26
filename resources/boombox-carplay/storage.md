# Storage & Updates

By default everything is kept in `data/store.json` inside the resource (with a `.bak` copy):
playlists, favourite stations, saved places, the queue of a boombox that was put away, the queue
of every car that has one (by plate), and a journal of the boomboxes that are out in the world.
It is written at most every `flushSeconds` - only when something changed - and when the resource
stops, so a crash costs the last few seconds, not the queue: after one, each boombox that was out
is back in its owner's storage with its queue (or back in the world with `persistPlaced`).
`/gsbc doctor` → "Queue persisted" writes the store and reads it back.

MySQL instead: set `ServerConfig.Storage.driver = 'mysql'` and start `oxmysql` before this
resource. The table `gsbc_store` is created automatically. The database address stays in your
`server.cfg` (`set mysql_connection_string ...`) - it is never in this resource. If oxmysql is not
running, the resource says so at start and uses the file.

Players are identified by character (ESX identifier / QBCore-QBox citizenid) when a framework
runs, otherwise by Rockstar licence. When a player switches character, the boombox of the
character they left counts as "owner left" (`ownerLeaveGraceSeconds`), and the boombox of the
character they loaded is theirs again.

**Instances (routing buckets).** A boombox belongs to the instance it was put down in: players
in another routing bucket - an apartment shell at the same coordinates, for example - neither
see nor hear it, and cannot operate it. Cars and carried boomboxes follow their game entity.
This needs OneSync (without it the server cannot know who is in which instance).

## Backups and Reinstallation

Stop the resource before replacing it. Back up the entire `data/` folder, all edited configuration files, framework/phone adapters and custom `music/` files. With SQL storage, back up the configured table as well. Do not copy a development server's store into a new customer's installation.

Install the new runtime files, merge your settings into the supplied configuration and retain the data folder. Do not rename the resource or change item/model IDs that existing records reference. Replace the optional NPWD app if its build is updated. Restart and verify item returns, queues, mounted screens and phone registration.

File storage is the default. The release contains only `data/README.txt`; store and backup files are created when the server first saves. A crash can lose changes since the last successful flush. Never expose saved stores or include them in support screenshots without removing personal/device data.
