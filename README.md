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

Jump into an console of the current kernel:
```
# for an ipython-like console in your terminal
,python --console

# for a graphical qt cosnole
,python --qtconsole
```

# Notes:

If stdin is provided, it is assigned to `stdin`, along with `lines = stdin.splitlines()`.

You can further customize your kernel, e.g. run specific imports or magic commands on init:
```
if is_new_kernel:
    exec_km('%pylab')
```

For an experience a little bit closer to a Notebook cell or QtConsole prompt, try using fish shell with Alt+E to edit your ,python command:
- Try using micro for mouse text selection and Ctrl+C Ctrl+V
- `set -U EDITOR "micro -wordwrap true -softwrap true -autosave 9999999"` where Ctrl+Q exits with the command changes saved automatically



# Examples:

```bash
,python "%pylab"
,python "print(arange(10))"

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

Sometimes after creating a new kernel, calling `km.execute(to_execute)` immediately never results in a `km.iopub_channel.msg_ready()` returing True.
- This can be hard to debug because it tends to only get stuck NOT using the debugger.
- A reproducer that seems to work with a debugger sometimes is `,python --new "import numpy as np"`
- The current workaround is a sleep(1) after creating a new kernel
- Another workaround is to only create kernels with `,python --new` without another argument for a command.

Some discussion here https://github.com/jupyter/jupyter_client/issues/941#issuecomment-1494576526


# Requirements (tested with):

+ jupyter_client == 8.0.3


# Development

Here's a snippet to access the kernel manager from python

```python
import jupyter_client
cf = jupyter_client.find_connection_file('shell_kernel.json')
km = jupyter_client.BlockingKernelClient(connection_file=cf)
km.load_connection_file()
```

It may be handy to jump into a REPL of the kernel, e.g.

```
jupyter console --existing shell_kernel.json
```
