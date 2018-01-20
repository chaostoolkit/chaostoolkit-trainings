Now we can setup a simple system with one service that we can use to write our first Chaos Toolkit chaos engineering experiment against.

Let’s say we have a service that polls a local file every second to see if new content was added for processing. We've made the assumption the file would always be there and have not write a failure code path...

Below is the code of our service as it is now:

<pre class="file" data-filename="service.py" data-target="replace">
# -*- coding: utf-8 -*-
from datetime import datetime
import io
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

Add this code into a file called `service.py` and then check the service runs by executing:

`python3 service.py`{{execute}}

You can now click on the `Local Web Browser` tab next to your terminal to hit your new service.

***Before moving on, it's worth killing this instance of your service by hitting `CNTRL-C` a few times in the terminal window until it returns to the command prompt.***

Now that you have a service, it's time to beign to write your experiment to look for any weaknesses in your design...
