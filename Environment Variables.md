[A very basic explanation](https://www.twilio.com/blog/environment-variables-python)

In a general sense, an environment variable is just a variable that is set outside the python script its self.  So it is useful in that it can be updated independently (like PYTHONPATH variables to script folders), or kept secret (for example so the script can be published, but any access keys can be kept secret.)
### os.environ
This is a *dictionary* associated with the operating system, storing environment variables. Can be accessed using the OS module as follows.

```python
>>> import os
>>> user = os.environ['USER']
>>> user
'olly'
```

This would throw an error if the variable doesn't exist.  For situations where it is optional use get().  Will return ```none``` if the variable is missing, else the default (second term).

```python
>>> database_url = os.environ.get('DATABASE_URL', 'sqlite:///')
```

### Set a Temporary Environment Variable
This will create a temporary variable, that is lost when the bash shell is closed

```bash
export DEBUG=true
```

### Using .env files

.env *files* are just a convenient way to store a bunch of environmental *variables*.  In a list, with one variable per line.  For example: 

```bash
DEBUG=true
DATABASE_URL=sqlite:///mydb.sqlite
```

Works across all operating systems, and can be stored in the root directory with the script. Then the variables can be loaded using dotenv:
```python
>>> from dotenv import load_dotenv
>>> load_dotenv('/home/miguel/my_project/.env')
```

Or accessed some other way by the IDE (like VS Codes settings.json file).   Don't forget to add the .env file to the .gitignore file, if the directory is also a public git repository!  Also don't rely solely on the IDE to access the .env file, as this may not transfer well to another system/IDE.