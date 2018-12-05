The Chaos Toolkit CLI, called `chaos`, is a Python program that is executed
in your terminal. It exposes various built-in commands performing different
tasks.

The Chaos Toolkit expects [Python 3](https://www.python.org/) to run. Let's
make sure you run at least Python 3.5:

`python3 -V`{{execute}}

Usually, the `python` binary refers to Python 2 so make sure you use `python3`
in your commands.

As a Python program, it is best install it in its own virtual environment.
Let's create it as follows:

```mkdir ~/.venvs && python3 -m venv ~/.venvs/chaostk```{{execute}}

Once the virtual environment installed, you need to activate it in your
current terminal session:

`source ~/.venvs/chaostk/bin/activate`{{execute}}

We're going to use `pip` to manage the dependencies and install the Chaos 
Toolkit. Let's first make sure we have the latest and greatest `pip` installed:

`pip install -U pip`{{execute}}

Then we can install the Chaos Toolkit itself by running the following:

`pip install -U chaostoolkit`{{execute}}

*Note* that `pip` may complain while installing some dependencies, these
warnings are nothing to worry about and will not prevent those dependencies
from being installed.

Now, the `chaos` command should be available:

`chaos --help`{{execute}}

Next, you need to learn how to install extensions to provide new probes and
actions to your Chaos Toolkit environment.