The [Chaos Engineering](http://www.oreilly.com/webops-perf/free/chaos-engineering.csp) book describes a Stead State Hypothesis as:

***"a model that characterizes the steady state of the system based on expected values of the business metrics."***

One way to think of this is that the Steady State describes what ***normal*** might look like for your system, within specified tolerances. While a complete discussion of what would make a good measurement and tolerances to form part of a great Steady State Hypothesis for your system is beyond the scope of this tutorial, it's enough to declare that it is the collection of measurements you can make that declares what **ok** might look like for your system, even under extreme conditions.

For your simple experiment, the Steady State Hypothesis is very simple as it checks that when we hit the service we will tolerate a set of valid response codes:

<pre class="file" data-filename="experiment.json" data-target="append">
    "steady-state-hypothesis": {
        "title": "The exchange file must exist",
        "probes": [
            {
                "type": "probe",
                "name": "service-is-unavailable",
                "tolerance": [200, 503],
                "provider": {
                    "type": "http",
                    "url": "http://localhost:8080/"
                }
            }
        ]
    },
</pre>

The Chaos Toolkit uses the Steady State Hypothesis for two purposes. It is used as a check before an experiment is run that the target system is in a recognised ***normal*** state. The Steady State Hypothesis is also used as the template for comparison of the state of your system ***after*** the experiment has been run, forming the results provided by the experimental report.

Now it's time to implement your Chaos Toolkit experiment's method...
