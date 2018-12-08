Once again, chaos engineering has highlighted a weakness in the system. In the current system we have a set of naive service implementations, especially in the `consumer`, that can easily result  in a cascading failure being surfaced to your users. Now we can learn from the chaos experiment.

One way of overcoming this weakness is to implement a circuit breaker in the `consumer`  so that in the event of the downstream `provider` service not responding there is a fallback strategy. In this case the `consumer` will return a limited response, but it will be the required `200` HTTP response code, rather than a cascading failure `500` response.

You can see the code for the improved `consumer` [here](https://github.com/chaostoolkit/chaostoolkit-samples/tree/master/service-down-not-visible-to-users/03-after/simple-spring-boot-feign-direct-consumer).

To enable the improved services you first need to take down the existing ones by executing the following command:

`kubectl delete deployment my-consumer-app my-provider-app`{{execute}}

***NOTE*** **You might get a message saying that the my-provider-app is already not deployed. That's because your chaos experiment removed it! That's fine, the main thing is to delete the deployment for the my-consumer-app anyway so that you can deploy the version with a circuit breaker.**

With the existing deployments removed, you can now deploy the fixed versions of the services using the following commands:

`kubectl create -f 03-after/provider-deployment.json`{{execute}}

`kubectl create -f 03-after/consumer-deployment.json`{{execute}}

Once again, give things time to settle in Kubernetes and test that the deployment is working using the following `curl` command:

`curl <your service address>/invokeConsumedMicroservice`

Once the service is responding healthily you can re-run your experiment to verify that the weakness has been overcome:

`chaos run experiment.json`{{execute}}

You're done! You now have an automated chaos engineering experiment that you can run as often as you like to ensure that this, and potentially other, weakness are not introduced into your system.

