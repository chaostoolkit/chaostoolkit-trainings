In the previous step you created a system comprising two Spring Boot microservices running on top of Kubernetes. However even in such a simple system there can be weaknesses through assumptions made by the teams working on the services.

On such common weakness is a Cascading Failure. This is where the failure of a downstream service can catastrophically affect an upstream service, and even impact the experience your users get when using the system!

To explore this type of weakness you already have an experiment that looks for it in your two-service system in the `experiment.json` file:

```json
{
    "version": "1.0.0",
    "title": "System is resilient to provider's failures",
    "description": "Can our consumer survive gracefully a provider's failure?",
    "tags": [
        "service",
        "kubernetes",
        "spring"
    ],
    "steady-state-hypothesis": {
        "title": "Services are all available and healthy",
        "probes": [
            {
                "type": "probe",
                "name": "all-services-are-healthy",
                "tolerance": true,
                "provider": {
                    "type": "python",
                    "module": "chaosk8s.probes",
                    "func": "all_microservices_healthy"
                }
            },
            {
                "type": "probe",
                "name": "consumer-service-must-still-respond",
                "tolerance": 200,
                "provider": {
                    "type": "http",
                    "url": "http://192.168.39.7:31546/invokeConsumedService"
                }
            }
        ]
    },
    "method": [
        {
            "type": "action",
            "name": "stop-provider-service",
            "provider": {
                "type": "python",
                "module": "chaosk8s.actions",
                "func": "kill_microservice",
                "arguments": {
                    "name": "my-provider-app"
                }
            },
            "pauses": {
                "after": 15
            }
        }
    ],
    "rollbacks": []
}
```

The key feature of this experiment is the second probe in the `steady-state-hypothesis` block. The `consumer-service-must-still-respond` probe checks that the `consumer` responds appropriately regardless what the experiment's method does to the system.

The experiment's method simply kills the `provider` service so that it is unreachable. Under these conditions you _hope_ the consumer will be able to continue functioning, if perhaps in a limited manner. To _prove_ this, you can run the experiment.

Before you run the experiment though you need to make sure the `consumer-service-must-still-respond` probe in the `steady-state-hypothesis` block is pointing at the correct URL for your `consumer` service. You saw how to get the correct URL endpoint for the `consumer` service in your system in the previous step by using `get-consumer-service-address.sh`:

`/root/get-consumer-service-address.sh`{{execute}}

Take the URL endpoint reported by this command and edit the `experiment.json` in the current directory so that the `consumer-service-must-still-respond` probe points to the correct URL for your `consumer` service. Use `vi` or an other editor from the terminal to complete this operation, for example by using the following command:

`vi experiment.json`{{execute}}

Now that you've pointed the experiment at your `consumer` service's endpoint, you can activate the Python virtual environment so that the `chaos` command is available to run your experiment:

`source ~/.venvs/chaostk/bin/activate`{{execute}}

Finally you can run your chaos experiment:

`chaos run experiment.json`{{execute}}

When you run this experiment with the existing system you should see something 
like the following:

```bash
[2018-01-05 19:50:29 INFO] Validating experiment's syntax
[2018-01-05 19:50:31 INFO] Experiment looks valid
[2018-01-05 19:50:31 INFO] Running experiment: System is resilient to provider's failures
[2018-01-05 19:50:31 INFO] Steady state hypothesis: Services are all available and healthy
[2018-01-05 19:50:31 INFO] Probe: all-services-are-healthy
[2018-01-05 19:50:31 INFO] Probe: consumer-service-must-still-respond
[2018-01-05 19:50:31 INFO] Steady state hypothesis is met, we can carry on!
[2018-01-05 19:50:31 INFO] Action: stop-provider-service
[2018-01-05 19:50:31 INFO] Pausing after activity for 15s...
[2018-01-05 19:50:46 INFO] Probe: all-services-are-healthy
[2018-01-05 19:50:46 INFO] Steady state hypothesis: Services are all available and healthy
[2018-01-05 19:50:46 INFO] Probe: all-services-are-healthy
[2018-01-05 19:50:46 INFO] Probe: consumer-service-must-still-respond
[2018-01-05 19:50:56 CRITICAL] Steady state probe 'consumer-service-must-still-respond' is not in the given tolerance so failing this experiment
[2018-01-05 19:50:56 INFO] Let's rollback...
[2018-01-05 19:50:56 INFO] No declared rollbacks, let's move on.
[2018-01-05 19:50:56 INFO] Experiment ended with status: failed
```

The `CRITICAL` message has highlighted a weakness! By killing the `provider` service the `consumer` service is no longer responding with the expected HTTP response code of `200`. In fact it's responding with a 500 error! This is a cascading failure so the chaos experiment has done it's job of helping you discover this weakness, now it's time to learn from this discovery and improve the system...