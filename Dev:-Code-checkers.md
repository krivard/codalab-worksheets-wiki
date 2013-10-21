##Notes on using PyLint

PyLint (http://pylint.org, http://docs.pylint.org/index.html) is a code checker which attempts to automatically detect errors and helps achieve consistent coding standard.

From the virtual environment, PyLint installs with pip:
```
pip install pylint
```
At the time of writing, pip install PyLint 1.0.0 which has a severe issue on Windows with trailing whitespace: see https://bitbucket.org/logilab/pylint/commits/02db08561a8e. A workaround is to manually update `format.py` which is located under `Lib\site-packages\pylint\checkers`.

PyLint's rules are configurable. To generate a default config file:
```
pylint --generate-rcfile > .pylint-conf
```
To use the generated config (assuming `.pylint-conf` is located in the current directory):
```
pylint –-rcfile=.pylint-conf <module or package>
```

Below is a set of rules that I have found the need to customize.

* Output format. PyLint can format its output multiple ways. I find the HTML format to be convenient.
```
output-format=html
```
* Line length. PyLint enforces an 80 character line length by default. It is on the short side.
```
max-line-length=120
```
* Number of methods on a class. PyLint likes at least two methods on a class. It also has an upper-limit which is too low when writing a class derived from TestCase for example.
```
min-public-methods=1
max-public-methods=100
```
* Dealing with Django generated members such as `objects`:
```
generated-members=REQUEST,acl_users,aq_parent,objects,^.DoesNotExist$
```
* The `disable` flag allows to turn off rules using their ID:
```
disable=R0921
```
    * R0921: Abstract class not references (http://stackoverflow.com/questions/8261526/how-to-fix-pylint-warning-abstract-class-not-referenced).


###Related tools

pep8: https://pypi.python.org/pypi/pep8/. Compared to PyLint, pep8 feels more lightweight in its feedback and probably too much so. 

