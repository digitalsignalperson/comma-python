# comma-python
A persistent Jupyter python kernel for your shell

# Usage:

Execute command (and start a kernel if one isn't running):
```
,python [command]
```

Kill running kernel:
```
,python --kill
```

New kernel (and kill any existing):
```
,python --new
```

New kernel (and kill any existing) and execute command:
```
,python --new [command]
```

# Notes:

- if stdin is provided, it is assigned to `stdin`, along with `lines = stdin.splitlines()`
- you could also `jupyter console --existing shell_kernel.json`, but then you won't maintain the history in your shell
- in fish, recommend using Alt+E to edit your ,python command!
    - an experience a little bit closer to a Notebook cell or QtConsole prompt
    - Try with this micro editor config: `set -U EDITOR "micro -wordwrap true -softwrap true -autosave 9999999"`
        - with the benefits of a mouse and Ctrl+C & Ctrl+V in micro.
        - where Ctrl+Q exits with the command changes saved automatically


# Examples:

```bash
,python "import numpy as np"

ls -1t | ,python "
jpgs = [x for x in lines if x.endswith('.jpg')]
pairs = np.array(jpgs[:len(jpgs) // 2 * 2]).reshape((-1, 2))
for i, (a, b) in enumerate(pairs):
    print(f'{i=}, {a=}, {b=}')
"

,python "print(f'{i=}, {a=}, {b=}')"

,python "for i, (a, b) in enumerate(pairs):
    print(f'{i=}, {a=}, {b=}')
" | grep "i=3"

,python "x=1"
,python "print(x)"
# This also shows the repr():
,python "x"

# also ipython magic commands work, e.g.
,python "files = !ls"
```


# Bugs:

- Sometimes after creating a new kernel, calling `km.execute(to_execute)` immediately never results in a `km.iopub_channel.msg_ready()` returing True.
    - This can be hard to debug because it tends to only get stuck NOT using the debugger.
    - A reproducer that seems to work with a debugger sometimes is `,python --new "import numpy as np"`
    - The current workaround is a sleep(1) after creating a new kernel
    - Another workaround is to only create kernels with `,python --new` without another argument for a command.
- jupyter kernel process doesn't exit after kernel is shutdown #941
    - https://github.com/jupyter/jupyter_client/issues/941
    - workaround is to store the pid and os.kill it manually


# Requirements (tested with):

+ jupyter_client == 8.0.3
