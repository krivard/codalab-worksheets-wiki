# FAQ

**How do I run my run bundle on a GPU?**

See our [article on running jobs that use GPUs](https://github.com/codalab/codalab-worksheets/wiki/Execution#running-jobs-that-use-gpus).


**I received an `ImportError` because I don't have the appropriate library installed. How do I install libraries in CodaLab?**

CodaLab runs all run bundles in Docker containers. See our [article about specifying Docker containers](https://github.com/codalab/codalab-worksheets/wiki/Execution#specifying-environments-with-docker).

How to [make your own Docker images](https://github.com/codalab/codalab-worksheets/wiki/Creating-Docker-Images)

## Common Errors

**I upgraded CodaLab and now I'm getting an error like: `ValueError: unknown url type: local/rest/worksheets?specs=%2F`**

Run `cl alias main https://worksheets.codalab.org` and retry your command.