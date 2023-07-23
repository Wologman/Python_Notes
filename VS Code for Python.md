VScode is lightweight, free, is somewhat language agnostic, and very customisable.  But it can be quite painful sometimes, especially when it comes to managing Conda and Python Environments. 

I tried PyCharm, but gave up on it after loosing bits of work due to confusion over how it manages version control.  

#### Key ideas in this page: 
- .code-workspace -- A .json file that defines settings and folders for a multi-root folder workspace
- .vscode -- As above, but single folder.  
- settings.json -- A user-wide one for all settings (eg dark-mode)
- settings.json -- Settings, prioritised for the workspace (Settings stored in the .vscode or .code-workspace  file )
- PYTHONPATH -- [[Environment Variables]], that tell Python where to look for libraries
- The interpreter path -- where VSCode looks for the python interpreter
- .env files -- Need to be located by the settings.json file for the project, or the python plugin will automatically look in the worspace folder.  Currently, not working as documented.
- Workspace folder -- where the .vscode file is located.  Or multiple folders listed in .code-workspace.

## Working with Conda

I'm still not getting this right, especially on windows.
[Follow this up](https://medium.com/analytics-vidhya/efficient-way-to-activate-conda-in-vscode-ef21c4c231f2)



### Bugs in VSCode
VScode is currently not picking up environment variables from .env files the way it claims in the documentation.  It is not enough just to leave the .env file in the workspace folder.  Either single .vscode workspaces or with the .code-workspace

This is an almighty pain in the arse, and I've spent weeks trying to understand what was going on.  It explains why QGIS won't run properly from VSCODE.  It's amazing VSCode has such a massive flaw.  People have been struggling with this off and on since 2015.

[Here is the issue tracking on GitHub](https://github.com/microsoft/vscode-python/issues/944)
[And here](https://github.com/microsoft/pylance-release/issues/275)

This is a shambles.  Different issues and paths used for Pylance + Code completion, Terminal, Debugger.  Microsoft clearly doesn't take Python all that seriously for VSCode.  

##### Workaround ideas

I haven't finished thinking this through.  How many places could I specify a python path?  Launch? Terminal settings, settings, export from the terminal, from the python code, from extrapaths in settings.   

- Switch to PyCharm, or Spyder [Here are the isntructions for PyCharm](https://docs.qgis.org/3.22/en/docs/pyqgis_developer_cookbook/plugins/ide_debugging.html)
- Leave the .env file as desired (for other editors, reproducibility), but manually add the required paths to extrapaths, terminal settings, & the debugger.  Seems reasonable if not a lot of paths.  
- PIP or Conda install dotenv in the environment for VSCode, run the code below, handle the error when running from another environment where the imports aren't needed (like QGIS for example)  
```python
	try:
		from dotenv import load_dotenv
		load_dotenv()
	except ImportError:
		pass
```
Then add the paths to the settings with extrapaths, for linting and code completion.	
```JSON
"python.analysis.extraPaths": ["extra_paths_here:another_path"],
```
- Export the python path of interest from the terminal for the session.  Or add this to the launch.json (Won't help for debugging that's all). Add it to extrapaths for linting and code completion.
```BASH
export PYTHONPATH="$PYTHONPATH:/usr/share/qgis/python/plugins:/usr/share/qgis/python"
```
### Settings###
There are two types of settings.  User wide settings, and Workspace settings.  Both stored in a settings.json file, with the workplace settings overriding most of the user settings.

#### Workspace Folder
This is important if I'm using a .env file.  Either I've opened a folder, and worked on something in it.  Then that is the workspace folder, and there will be a `.vscode` file sitting in this folder referencing a `settings.json` file.  **Or**  I've setup and saved a code workspace.  In practice this is a `<name>.code-workspace` JSON file, which will list the workspace folders like this:

```
{
  "folders": [
    {
      "path": "my-folder-a"
    },
    {
      "path": "my-folder-b"
    }
  ]
}
```

By default it is:

```
{
	"folders": [
		{
			"path": "."
		},
		{
			"path": ".."
		}
	],
	"settings": {}
}
```

### Customizing VSCode with User and Workspace Settings
[From here](https://github.com/d-w-d/python-project-template)

Customise your VSCode experience by setting values in two special `json` files. One of these files is for your global 'User Settings', and the other is for your local 'Workspace Settings'.

To get to an interface to adjust either of these sets of settings, go to
`Code > Preferences > Settings`. You'll get shown an interface that lets you choose between `User Settings` and `Workspace Settings`; under the hood, this interface is adjusting values in simple JSON files. To see the raw JSON files, you can click on the icon with curly brackets near the top right corner of the settings interface.

When you create any non-default `Workspace Settings` in the UI, VSCode will generate a corresponding `.vscode` folder in your project root directory and will place a `settings.json` file in it. This is was determines the way VSCode and its extensions behave for files _in this folder_. You can see one in this repository, with settings specific to python development. These workspace settings take precedence over your `User Settings`. (Likewise, when you set global `User Settings`, a JSON file is updated somewhere on your system; on a Mac, this file is located at `~/Library/Application\ Support/Code/User/settings.json`).

VSCode comes with myriad possible settings to place in these json files. For example, if you want to increase the size of the displayed text, you can add the following key-value pair to your user or workspace json file:
```
{
  //...
  "window.zoomLevel": 1
  //...
}
```
If you do NOT place such a key-value pair in one of your json files, then VSCode will apply a default value. In the example above, you can see the description of the setting and its default value by searching for `window.zoomLevel` in the settings UI.

Things I could setup this way would be for example different extensions enabled for different types of programming, Ipython window, default choice of virtual python environment?

### Useful Extensions
-   Trailing Spaces
-   Git Lens
-   Rainbow Brackets
-   Rainbow Indent
-   Better Comments

### Using .env files in VS Code
[Official Documentation](https://code.visualstudio.com/docs/python/environments)

I could use .env files for things like Kaggle, W&B or Co-lab API keys, and of course for the PYTHONPATH, to access project-specific libraries already on my computer like QGIS.

The only reliable way I can see to do this is to 

By default, the Python extension looks for and loads a file named `.env` in the current workspace folder, then applies those definitions. The file is identified by the default entry `"python.envFile": "${workspaceFolder}/.env"` in your user settings (see [General Python settings](https://code.visualstudio.com/docs/python/settings-reference#_general-python-settings)). You can change the `python.envFile` setting at any time to use a different definitions file.

I think it is better to put the .env file in the workplace folder and consider it part of the project.  This is more transparent.  If switching from Ubuntu to Windows, it may be necessary to have a different .env file (for example to cope with different PYTHONPATH variables) but at least the requirements are then transparent.

### The PYTHONPATH variable
[VSCode Docs](https://code.visualstudio.com/docs/python/environments#_use-of-the-pythonpath-variable)

The [PYTHONPATH](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH) environment variable specifies additional locations where the Python interpreter should look for modules. In VS Code, PYTHONPATH can be set through the terminal settings (terminal.integrated.env.*) and/or within an `.env` file.

I think it would be better practice to use a .env file, as this could be moved to a different machine, and modified, and also it can be easily checked and edited.

The only thing to note that if it is set with a .env file, then it will not effect tools run through the terminal.
_____________________________________
Found this on stack-exchange, seems to be the solution: 

The Python Extension in VS Code has a setting for `python.envFile` which specifies the path to a file containing environment variable definitions (Refer to: [https://code.visualstudio.com/docs/python/environments#_environment-variable-definitions-file](https://code.visualstudio.com/docs/python/environments#_environment-variable-definitions-file)). By default it is set to:

```python
"python.envFile": "${workspaceFolder}/.env"
```

So to add your external libraries to the path, create a file named _.env_ in your workspace folder and add the below line to it if you are using Windows:

```python
PYTHONPATH="C:\path\to\a;C:\path\to\b"
```

This would be good if it actually worked, it only requires the .env file to be moved around with the source code.  No changes should be needed for the settings.  Currently not working.

_____________________________________________________________

#### Set PYTHONPATH for Linting & Code completion
Currently this needs to be set manually in extrapaths, add this to the settings in the .vscode or .code-workspace JSON file

```JSON
"python.analysis.extraPaths": ["/home/some_sub_folder:another_one:etc"]
```

#### PYTHONPATH for Terminal Commands
This does not seem to work for me
```JSON
"terminal.integrated.env.linux": {
"PYTHONPATH": "${workspaceFolder}/.env"
}
```

#### PYTHONPATH for Debugging
This needs to go in the launch.json  file.

#### Set Interpreter path for terminal commands
So the terminal command window does not know the interpreter path unless it is set in the terminal settings, simply running the script where the editor knows the path but not the terminal may use the wrong interpereter. But specifying it in the command solves this issue.  For example in Ubuntu:

```bash
/usr/bin/python3 "/media/olly/One Touch/Documents/GIS/QGIS/Basic_QGIS_Operation.py"
```

Hitting the run button from the IDE with the python path set correctly will simply write this in the terminal.  The program is still executed from the terminal. 

It is also possible to set temporary environment variables for an operating session.

This will create a temporary variable, that is lost when the bash shell is closed

```bash
export PYTHONPATH="path1:path2:path3"   # use ; for windows
```
