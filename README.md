# How To Integrate Plugin Code

If you want to build and possibly edit one of the plugins from the Pointwise 
repository, you must first integrate the source code from this repository into 
your local PluginSDK installation.

These instructions assume you have already downloaded, installed, and 
[verified][SDKdocs] your [Pointwise Plugin SDK][SDKdownload] installation. Be 
sure the SDK version you are using is compatible with the version of Pointwise 
which you are targeting.

If you plan on building the plugin for multiple platforms, I suggest that you 
place the SDK on a network drive. That way you can build plugins using the same 
SDK installation. You only need to create the plugin project one time. Once a 
plugin project is created, it can be accessed and built on all platforms 
supported by Pointwise.

In the sections below, the `PluginSDK` directory is shorthand for the full path 
to the `PluginSDK` directory of your SDK installation. That is, `PluginSDK` 
is shorthand for `\some\long\path\to\your\install\of\PluginSDK`.

The integration process, detailed below, is simple:
1. Create a new, boiler-plate plugin project in your SDK installation
1. Compile the newly created, SDK plugin project
1. Copy the files from the appropriate github plugin repository into the
   SDK plugin project and recompile

## Create a New Plugin Project
You must first create a plugin project using the `mkplugin` SDK script. This 
script is run from your machine's command shell and requires 2 options. Consult 
the plugin repository page to determine the appropriate options. You can run 
`mkplugin` with the `-h` option to see the full script usage. `mkplugin` is a Tcl 
script. You must have `tclsh` to run it. See the 
[tclsh Information section][TclshInfo] for more details.

In the following examples, `Name` is a place-holder for the actual plugin 
project name.

#### Example: C-language, unstructured CAE exporter plugin project
The following example creates a plugin project named `CaeUnsName`.
* On mac/unix: `./mkplugin -c -caeu Name`
* On windows: `mkplugin -c -caeu Name`

#### Example: C++-language, structured CAE exporter plugin project
The following example creates a plugin project named `CaeStrName`.
* On mac/unix: `./mkplugin -cpp -caes Name`
* On windows: `mkplugin -cpp -caes Name`

#### Example: C-language, grid importer plugin project
The following example creates a plugin project named `GrdpName`.
* On mac/unix: `./mkplugin -c -grdp Name`
* On windows: `mkplugin -c -grdp Name`

## Compile the New Plugin Project
To make sure there were no problems creating the plugin project, it should be 
compiled before making any changes to it. Other than possibly being displayed in the 
Pointwsie UI (and available via Glyph), the new plugin won't do anything useful. 
As long as it builds correctly here, we are ready for the next step.

In the next sections, `XxxxName` refers to the plugin project's full, decorated 
name (i.e. `CaeUnsName`).

#### Compiling on Unix and MacOSX
The `make` utility is used to compile the plugin project on linux and MacOSX 
platforms. For details, click on the **4. Build the CAE plugin** link in the 
[How to Create a CAE Plugin][SDKbuild] section.

To compile for both debug and relase on macOSX:
* `make machine=macosx XxxxName-dr`

To compile for both debug and relase on linux:
* `make machine=linux_x86_64 XxxxName-dr`

#### Compiling on Windows
Microsoft Visual Studio (VS) is used to compile the plugin project on the 
Windows platform. Before a project can be built for the first time, it must be 
added to the plugin SDK's VS solution:
* Choose the *File &gt; Open &gt; Project/Solution...* menu
* Select the appropriate `.sln` solution file in the `PluginSDK` directory
* In the *Solution Explorer* tab
  * Right-click on the *Plugins* item
  * Choose *Add &gt; Existing Project...* in the popup menu
  * Select `PluginSDK\src\plugins\XxxxName\XxxxName.vcxproj`
  * A `XxxxName` project should now be listed under the *Plugins* item 
    along with the other SDK example plugins

Now that the plugin project is part of the solution, it can be compiled:
* Right-click on the `XxxxName` project
* Choose *Build* in the popup menu

## Integrate the github Plugin Source Files into the Plugin Project
Once the `XxxxName` project is created and builds correctly as explained above, 
you can integrate the source code from this repository into your project by 
copying the repository files into the `PluginSDK\src\plugins\XxxxName` directory.
This will overwrite the boilerplate files generated by `mkplugin`. You can now 
rebuild the plugin project as outlined above.

If this repo contains a custom `modulelocal.mk` file, the project is using custom 
`.cxx` and `.h` files (files not generated by `mkplugin`) or links to third-party 
libraries. In this case, the plugin's github page should include a `README.md` 
section listing the custom files and libraries.

For MacOSX and linux builds, nothing more needs to be done. The `modulelocal.mk` 
file already hooks these custom files into `make`.

However, for Windows builds using Visual Studio, these files and libraries 
must be added to the `XxxxName` project.

To add the custom `.h` and `.cxx` files, goto the *Solution Explorer* tab and:
* Right-click on the `XxxxName` project item (added above)
* Choose *Add &gt; Existing Item...* in the popup menu (which displays a file chooser 
  dialog box)
* Browse to the `PluginSDK\src\plugins\XxxxName` directory and select the custom `.h` 
  and `.cxx` files listed in the plugin's github repo (not the libs)

To add the custom link libraries, goto the *Solution Explorer* tab and:
* Right-click on the `XxxxName` project item (added above)
* Choose *Properties* (which displays a dialog box)
  * Select the `x64` *Platform* (Pointwise only supports 64-bit plugins)
  * Select the appropriate *Configuration*
  * Navigate to *Linker &gt; General* and add library directories to 
    *Additional Libaray Directories* as needed.
  * Navigate to *Linker &gt; Input* and add *Additional Dependencies* library 
    file names (without path) as needed.
  * Repeat as needed for each *Configuration*

If the project builds correctly after copying the files, you now have a plugin 
that is functionally equivalent to the plugin distributed with Pointwise. 
The `XxxxName` plugin project is now ready for you to make your desired code 
changes.

## Closing Thoughts
Even though this new plugin is functionally equivalent, its group and user ids
are different. Because of this, Pointwise considers the `XxxxName` plugin 
created by the SDK as a different CAE solver.

There are several ways to make the plugins built by the SDK "visible" to 
Pointwise at runtime. In the [How to Create a CAE Plugin][SDKbuild] section, 
click on the **4. Build the CAE plugin** link for information on configuring 
your SDK build environment.

If you plan on publicly releasing source or binary builds of your version of 
the `XxxxName` plugin, you need to be careful about your choice of site id, site 
group name, plugin id, and plugin name. See documentation for [site.h][SDKsite.H] 
for details. Pointwise may not properly load your plugin if the values you 
choose conflict with other CAE plugins.

Being open source, we would like to incorporate any enhancements or bug fixes 
you make back into the main Pointwise build. Please do not hesitate to send us 
git pull requests for your changes. We will evaluate your changes and integrate 
them as appropriate.


[SDKdownload]: http://www.pointwise.com/plugins/#sdk_downloads
[SDKdocs]: http://www.pointwise.com/plugins
[SDKsite.H]: http://www.pointwise.com/plugins/html/d6/d89/site_8h.html
[SDKbuild]: http://www.pointwise.com/plugins/html/index.html#how_to_create_a_cae_plugin
[TclshInfo]: http://www.pointwise.com/plugins/html/dd/d8c/create_cae_plugin_project.html
