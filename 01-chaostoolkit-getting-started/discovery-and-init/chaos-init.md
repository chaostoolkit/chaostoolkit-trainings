The `chaos discover` command retrieves as much information as possible from your target environment. With that to hand it's now possible to create, or initialise, your experiments for this environment quickly and easily using the `chaos init` command.

***NOTE:*** You _could_ write your chaos experiments from scratch without the use of `chaos discover` and `chaos init`. These commands are there to speed up this process.

Now you can use `chaos init` to pick up the output from `chaos discover`, which can be found in the default `./discovery.json` file, and provide some insights that help you identify what probes and actions you might want to use in order to create your experiment.

To create your experiment enter:

`chaos init`{{execute}}

You will now be guided through a set of steps to identify your experiment and to select probes and actions that you can add to your experiment. The first question will be to enter what title you want to use for your experiment:

``` bash
Experiment's title:
```

Enter a meaningful name of what your experiment might be looking to discover in your system as a weakness, such as:

``` bash
Experiment's title: How is availability impacted when a service is destroyed
```

Defining a stead state hypothesis is optional. You'll then be asked if you want to define one.
Answer with 'y' for yes.

``` bash
Do you want to define a steady state hypothesis now? [y/N]: y
```

The next entry is the title for the steady state hypothesis. This is simply a title, so since you may not know what you are looking for at this point you could specify `Exploring`.

Next you'll see a lit of the probes and activities that have been detected as possible against your targetted environment when you executed the `chaos discover` command:

``` bash
Add an activity
1) all_microservices_healthy
2) deployment_is_not_fully_available
3) microservice_available_and_healthy
4) microservice_is_not_available
5) read_microservices_logs
6) service_endpoint_is_initialized
7) count_pods
8) pods_in_phase
9) pods_not_in_phase
10) read_pod_logs
Activity (0 to escape):
```

You're prompted for an activity to add to your experiment, and you can add as many as you like before you quit by entering `0`.

Enter `1` for the probe `all_microservices_healthy`. You will then see:

``` bash
Check all microservices in the system are running and available.

Raises :exc:`chaoslib.exceptions.ActivityFailed` when the state is not as expected.
Do you want to use this probe? [y/N]: y
```

Confirm adding the action with `y`

Input `1` for the tolerance of the probe.

``` bash
A steady-state probe requires a tolerance value, within which
your system is in a recognised `normal` state.

What is the tolerance for this probe?: 1
```

Just hit enter to confirm the default value for *ns* argument.

``` bash
You now need to fill the arguments for this activity. Default
values will be shown between brackets. You may simply press return
to use it or not set any value.
Argument's value for 'ns' [default]:
```

Next type `N` to not add other activities.

``` bash
Do you want to select another activity? [y/N]:

An experiment's method contains actions and probes. Actions
vary real-world events in your system to determine if your
steady-state hypothesis is maintained when those events occur.

An experimental method can also contain probes to gather additional
information about your system as your method is executed.
```

You're prompted for an experimental method to add to your experiment.
Type `N` to not add an experimental method.

``` bash
Do you want to define an experimental method? [y/N]: n

An experiment may optionally define a set of remedial actions
that are used to rollback the system to a given state.
Do you want to add some rollbacks now? [y/N]: n
```

``` bash
Experiment created and saved in './experiment.json'
```

Once you've entered `N` you will see the following output:

``` bash
Experiment created and saved in './experiment.json'
```

Congratulations! You've now created an experiment in the `experiment.json` file based on your answers to the `chaos init command.


