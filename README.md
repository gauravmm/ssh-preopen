# Insert-Punny-Name-Here

This tool speculatively opens SSH ControlMaster connections when you use `bash` autocomplete to fill in a server name. It saves you `<duration>` of annoyance every day!

## How it Works

When you type in an `ssh` command and auto-complete the server name, it examines your `~/.ssh/config` file and suggests completions. If only a few suggestions are presented, it opens a ControlMaster connection to these servers in the background; when you eventually select the correct server `ssh` automatically uses the ControlMaster connection, saving you precious `<duration>`,

It supports these types of commands to connect to server `yggdrasil`:

 - `ssh yg<tab>`
 - `ssh freyr@yg<tab>`
 - `ssh -o Option=Value ... yg<tab>`
 - `ssh -o Option=Value ... freyr@yg<tab>`

It does not support changing verbosity for connection debugging: `ssh -v`, `ssh -vvv`, etc. (ControlMaster client connections have the same verbosity as the master connection.)

## Usage

This requires a reasonably modern version of `bash`. We recommend BASH 4.4.18 or later; this corresponds to the default shell on Ubuntu 16.04 and later.

To use it once, download the script and run `source ssh_autocomp.bash`. To install it, save it somewhere and add the `source` line to your `~/.bashrc` or `~/.bash_profile` or similar.

Configuration information is stored in the head of the file itself. You can set the following variables:

 - `SSH_CONFIG_PATHS`: a space-separated list of paths to search for `HOST` directives.
 - `MAX_CM_OPEN`: the maximum number of ControlMaster connections to open; if there are more than these many possible servers in your auto-complete then use a heuristic to decide which connections to open.
 - `HEURISTIC`: the heuristic to use to decide which connections to open; values are `""`, `"last-n-conn"`
 - `DEBUG`: set this to `""` to disable debug output, or any other value to enable it. 
 - `HEURISTIC_CACHE_LNC`: cache for `last-n-conn` heuristic

### Heuristics

 - `""`: If a blank heuristic is specified no automatic connections are made.
 - `"last-n-conn"`: Open the `$MAX_CM_OPEN` servers most recently connected to; this heuristic updates its list of recently-connected servers every day or whenever any file in `SSH_CONFIG_PATHS` is updated.

### Debugging

Set `DEBUG` to any non-blank value and, in a separate terminal window, run `tail -f debug.log` to see debug messages as they are generated. Remember to run `source ssh_autocomp.bash` each time you change the file!
