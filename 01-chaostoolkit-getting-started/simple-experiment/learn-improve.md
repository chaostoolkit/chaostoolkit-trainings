The power of Chaos Engineering is in discovering weaknesses in your system that you were originally ignorant of. In this case you have found that there is indeed a weakness in that if the `exchange.dat` file is _"deleted"_ then your service does not deal with this gracefully at all.

Now it's time to learn from that surfaced weakness and improve your service so that it does meet the requirements of the tolerances and measurements specified in your Steady State Hypothesis.

Change the code in your `service.py` so that it is the following:

<pre class="file" data-filename="service.py" data-target="replace">
# -*- coding: utf-8 -*-
from datetime import datetime
import io
import os.path
import time
import threading
from wsgiref.validate import validator
from wsgiref.simple_server import make_server

EXCHANGE_FILE = "./exchange.dat"


def update_exchange_file():
    """
    Writes the current date and time every 10 seconds into the exchange file.

    The file is created if it does not exist.
    """
    print("Will update to exchange file")
    while True:
        with io.open(EXCHANGE_FILE, "w") as f:
            f.write(datetime.now().isoformat())
        time.sleep(10)


def simple_app(environ, start_response):
    """
    Read the content of the exchange file and return it.
    """
    if not os.path.exists(EXCHANGE_FILE):
        start_response(
            '503 Service Unavailable',
            [('Content-type', 'text/plain')]
        )
        return [b'Exchange file is not ready']

    start_response('200 OK', [('Content-type', 'text/plain')])
    with io.open(EXCHANGE_FILE) as f:
        return [f.read().encode('utf-8')]


if __name__ == '__main__':
    t = threading.Thread(target=update_exchange_file)
    t.start()

    httpd = make_server('', 8080, simple_app)
    print("Listening on port 8080....")

    try:
        httpd.serve_forever()
    except KeyboardInterrupt:
        httpd.shutdown()
        t.join(timeout=1)
</pre>

Now run your service **again from its own terminal window** by executing:

 `python3 service.py`{{execute}}

And head back to the original terminal window to re-run your experiment using:

`chaos run experiment.json`{{execute}}

Now you should see a quite different report from your experiment as your Steady State Hypothesis is maintained within the tolerances set throughout the failure-injecting actions of your experiment's method.

That's it, you're done! Through the power of Chaos Engineering you have detected a weakness, fixed the weakness and now have an automated experiment that can be used to continually verify that the weakness, or perhaps others, do not raise their ugly heads again in the future!
