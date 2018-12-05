The [Chaos Toolkit](http://chaostoolkit.org/) uses various extensions to drive chaos experiments against 
the multitude of technologies and layers in a typical system. The integrations 
are typically installed in the same Python environment as the `chaos` command 
itself.

To activate a new extension activate your virtual environment:

`source ~/.venvs/chaostk/bin/activate`{{execute}}

Now you can install the [Kubernetes]() integration through its Chaos Toolkit 
extension using `pip`: 

`pip install -U chaostoolkit-kubernetes`{{execute}}

The `pip` tool fetches the
[chaostoolkit-kubernetes](https://github.com/chaostoolkit/chaostoolkit-kubernetes)
extension and installs it along with any necessary dependencies.

You can now check that this extension is properly installed by executing:

`pip freeze | grep chaostoolkit`{{execute}}
