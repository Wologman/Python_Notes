VSCode is lightweight, free, is somewhat language agnostic, and very customisable.  But it can be quite painful sometimes, especially when it comes to managing Conda and Python Environments. 
#### Key ideas in this page: 
- `.code-workspace` -- A `.json` file that defines settings and folders for a multi-root folder workspace
- `.vscode` -- As above, but single folder.  
- `settings.json` -- A user-wide one for all settings (eg dark-mode)
- The other `settings.json` -- Settings, prioritised for the workspace (Settings stored in the .vscode or .code-workspace  file )
- `launch.json` -- Another way to control what happens when the terminal is launched, for example export to a PYTHONPATH variable
- PYTHONPATH -- [[Environment Variables]], that tell Python where to look for libraries
- `extrapaths`  -- a setting for `settings.json` specifically intended for extra PYTHONPATHs
- The interpreter path -- where VSCode looks for the python interpreter
- Setup Conda in the terminal window
- `.env` files -- Need to be located by the settings.json file for the project, or the python plugin will automatically look in the workspace folder.  Currently, not sure if this is working as documented.
- Workspace folder -- where the `.vscode` file is located.  Or multiple folders listed in .code-workspace.

## Useful Shortcuts

| Shortcut | Description  |
| ----------- | ----------- | 
|  Ctrl + Shift + P  | Command Pallet|
| Ctrl + \` | New terminal window | 
| Ctrl + Shift + D  |  Debugger  |
| Shift + Enter  |  Runs highlighted code in terminal |
| Right click + Run in Ipython Window | Brings up Ipython window and variable explorer |
| Shift + Tab |  Moves highlighted block back one tab  |

## VSCode with Python & Conda on Windows
To make Conda commands through the shell built into VSCode on Windows you need to launch the editor through the anaconda terminal, or permanently initialize the PowerShell terminal to use Conda [[VS Code for Python#Working with Conda]].

## Different Windows and Modes
### The Terminal Window
The default shell in windows will be PowerShell, but this could be changed to cmd by changing the workspace, or user settings.  In Linux it would be BASH.  It is probably worth persevering with PowerShell even though it's less familiar, as it is a lot more powerful.

### Debugging

### IPython Window

### Previewing Markdown & Double panels

## Working with Conda

Useful references: [Kathryn-medium.com](https://medium.com/analytics-vidhya/efficient-way-to-activate-conda-in-vscode-ef21c4c231f2)  [Chris-Mamon](https://www.linkedin.com/pulse/introduction-vscode-python-conda-chris-mamon/)

If using the default PowerShell in the terminal window, then the PowerShell settings will need to be changed to intialize Conda.  Below are the steps to allow PowerShell to recognize Conda, whilst not having PowerShell defaulting to Conda (base) on opening:

- Right-click on start menu to open PowerShell as administrator
- `conda init powershell`
- `set-executionpolicy unrestricted` 
- `conda config --set auto_activate_base false` 

Now Conda commands can be made directly from the terminal window in VSCode, (Windows)  Or for that matter anything that uses the PowerShell.


## Working with Environment variables

Consider
- The project should really be IDE agnostic
- The paths to all libraries needs to be transparent
- Some variables should be kept private from version control, GIT, Kaggle etc (e.g. API keys)
- VScode needs PYTHONPATH variables for editing functionality like linting and code-completion

With the above in mind, it seems to me that best practice is to do all the following:
- Always store environment variables in a .env file, load within script using `load_dotenv()`, to meet the requirement for the project to be IDE agnostic, and work from a simple shell terminal if need be.
- Set the path to the above file in the system wide `.json` file, so that the terminal always looks for the .env file  `"python.pythonPath": "${workspaceFolder}/.env/bin/python"`
- The debugger should default to the same settings unless told otherwise by it's `launch.json` file.

Full code for loading with `dotenv`
```python
from dotenv import load_dotenv
	load_dotenv()
```

### Settings Files
There are two types of settings.  User wide settings, and Workspace settings.  Both stored in a `settings.json` file, with the workplace settings overriding most of the user settings.

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
