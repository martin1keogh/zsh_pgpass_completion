# Zsh pgpass completion

This repository contains a small Zsh completion script for the
[psql](https://www.postgresql.org/docs/current/app-psql.html) application.  
When using this completion Zsh will show a list of the databases found in the
.pgpass file, as well as an optional alias to make it a bit easier to find the
one you're looking for.


## Installation
Please refer to [Zsh-completions](https://github.com/zsh-users/zsh-completions#zsh-completions) repository's README.md.


## Usage
This script reads the `$PGPASSFILE` file, or `$HOME/.pgpass` if `$PGPASSFILE` is undefined.  
For every line matching the [pgpass format](https://www.postgresql.org/docs/current/libpq-pgpass.html), a new
completion option is added to the list of possible choices.  
*Note*: the options will be displayed in the same order they are read from the pgpass file.

An optional description can be added to the completion output by specifying
```
# alias: <alias to show in the completion list>
```
on the line above a database's connection info.

For instance, with a .pgpass file like
```
# non alias DB example
localhost:5342:martin:martin:

# Note: wildcards are handled
# alias: local project X
localhost:5342:other:*:pass

# Project X databases
# alias: RDS fake project dev
35.127.0.0:5342:project_name:user:pass
# alias: RDS fake project staging
35.127.0.1:5342:project_name:user:pass
# alias: RDS fake project prod
35.127.0.2:5342:project_name:user:pass
```

then the completion list will look like:

```
$ psql postgresql://
postgresql://martin@localhost:5342/martin       -- 
postgresql://@localhost:5342/other              --  local project X
postgresql://user@35.127.0.0:5342/project_name  --  RDS fake project dev
postgresql://user@35.127.0.1:5342/project_name  --  RDS fake project staging
postgresql://user@35.127.0.2:5342/project_name  --  RDS fake project prod
```

## TODOs
### Additional parameters
Allow setting [additional parameters](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING) (presumably sort of like how the aliases are read today)

### Create Zsh completion groups
The completion system can create groups of matches. Being able to group databases together (eg per project or environment) would be nice.

### Other completion info
This completion script only shows databases in the completion list (and override any other completion for that matter).  
There might be some more information that could be useful to diplay (eg `_gnu_generic` might be nice).

## Bugs
Probably a lot, this is my first time writing a zsh completion script and it's
not the most beginner friendly thing around.

One known limitation is that escaped colons (':') in any of the four fields
(ie host/port/user/db_name) will cause the script to produce an invalid connection
string for this database.
