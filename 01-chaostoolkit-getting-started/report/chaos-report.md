At this point you would usually run a chaos experiment to produce a report, but for this tutorial we're only interested in the final step of the `discover`->`init`->`run`->`report` workflow in Chaos Tool,it so instead we'll create a representative output from the `chaos run` command that you can then use to report on.

The contents of the output from the `chaos run` command will be the following. Create the `chaos-report.json` file with the following content or grab it (instructions after the file).

<pre class="file">
{
  "status": "failed",
  "start": "2018-01-24T13:25:55.195573",
  "rollbacks": [],
  "experiment": {
    "tags": [
      "tutorial",
      "filesystem"
    ],
    "dry": false,
    "steady-state-hypothesis": {
      "title": "The exchange file must exist",
      "probes": [
        {
          "provider": {
            "type": "http",
            "url": "http://localhost:8080/"
          },
          "type": "probe",
          "tolerance": [
            200,
            503
          ],
          "name": "service-is-unavailable"
        }
      ]
    },
    "title": "Does our service tolerate the loss of its exchange file?",
    "method": [
      {
        "type": "action",
        "provider": {
          "func": "rename",
          "type": "python",
          "module": "os",
          "arguments": {
            "dst": "./exchange.dat.old",
            "src": "./exchange.dat"
          }
        },
        "name": "move-exchange-file"
      }
    ],
    "description": "Our service reads data from an exchange file, can it support that file disappearing?"
  },
  "platform": "Linux-4.4.0-66-generic-x86_64-with-Ubuntu-16.04-xenial",
  "duration": 0.009195566177368164,
  "run": [
    {
      "status": "succeeded",
      "start": "2018-01-24T13:25:55.198676",
      "output": null,
      "activity": {
        "type": "action",
        "provider": {
          "func": "rename",
          "type": "python",
          "module": "os",
          "arguments": {
            "dst": "./exchange.dat.old",
            "src": "./exchange.dat"
          }
        },
        "name": "move-exchange-file"
      },
      "duration": 0.000509,
      "end": "2018-01-24T13:25:55.199185"
    }
  ],
  "chaoslib-version": "0.12.1",
  "end": "2018-01-24T13:25:55.202614",
  "node": "b2d6b69a2f14"
}
</pre>

You can grab this `chaos-report.json` file using the following `curl` command:

`curl https://raw.githubusercontent.com/chaostoolkit/chaostoolkit-tutorials/master/01-chaostoolkit-getting-started/report/assets/chaos-report.json > chaos-report.json`{{execute}}

Now that you have the raw experimental output that would normally be produced by the `chaos run` command, you can take this and produce a report in a given format by executing the following:

`chaos report --export-format=html5 chaos-report.json report.html`{{execute}}

This will produce a `.html` format version of the report. The `--export-format` option selects the output that you want, the next parameter is the source, raw report file that came from `chaos run`, and the final parameter is the filename of the report that you would like to product.

Optionally, to produce a great-looking `pdf` export you can execute the following:

`chaos report --export-format=pdf chaos-report.json report.pdf`{{execute}}

