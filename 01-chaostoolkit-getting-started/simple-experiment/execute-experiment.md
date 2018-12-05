Now you have your system, represented as a single Python service in `service.py`, and your experiment all set to explore a potential weakness in that system, captured in the `experiment.json` file.

Before running your experiment you need to ensure that your service is running. To do that you should **open a new terminal window** and then execute the following command in that window:

`python3 service.py`{{execute}}

Once the service is running, **navigate back to the original terminal window** and run the following command to execute your Chaos Toolkit experiment:

`chaos run experiment.json`{{execute}}

When your run your experiment the following information should be reported:

* The syntax of your `experiment.json` is checked.
* The experiment starts to execute.
* The Steady State Hypothesis is checked to ensure the system is currently in a recognisable _normal_ state, within tolerances, to begin the experiment.
* The experimental method is executed.
* An unexpected weakness is found that is affecting availability of the service.
* There are no rollbacks declared and so the experiment comes to a close.

As you should see, the experimental output states that there was a detected weakness as there was a deviance from your Steady State Hypothesis when it was assessed after the experiment's method had completed executing:

```
[2018-01-05 17:04:20 CRITICAL] Steady state probe 'exchange-file-exists' is not in the given tolerance so failing this experiment
```

Before moving on, kill your poorly performing service by hitting `CNTRL-C` a few times in the terminal window that is running the service so that it returns to the command prompt.

Once you have your service killed it's time to learn from this discovered weakness in your system, and to then improve your system based on this new knowledge...
