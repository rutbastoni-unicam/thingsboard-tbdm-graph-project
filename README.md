# THINGSBOARD#GRAPH_MODELING

**The project aims to leverage the [Thingsboard](https://thingsboard.io/) platform 
features to model the layout of buildings and premises of an organization, and the arrangement of IoT devices within it, by means of a graph-structure representation.**

The Thingsboard platform is equipped with configurable dashboards and widgets, 
capable of showing complex analyses and visualizations. However, a graph-structure 
visualization widget is missing. The objective is the development of a custom 
graphical widget for visualizing and managing the organization structure as a graph.
The widget must support the user to interactively visualize the structure by 
configuring the root nodes, and must provide controls to handle relationships 
among devices and facilities, as well as display the latest telemetry data from the devices.

The implemented project takes charge of the goal of providing an assets/device graph
visualization by means of a specifically configured Thingsboard dashboard, equipped
with a custom widget developed on top of the Angular-based [Extension ThingsBoard Platform
](https://github.com/thingsboard/thingsboard-extensions). The [3D Force-Directed Graph
](https://github.com/thingsboard/thingsboard-extensions) component was used to 
render the graph structure.

As a sample dataset for a structure of assets and devices, the arrangement of the Computer
Science faculty of the University of Camerino was utilized in this repository. Data 
has been made available for import in the Thingsboard platform, as well a ready-to-use configured
dashboard with the custom developed graph widget, and a map widget linked to it to
show the geolocation of the buildings.

![Project preview](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/utils/img/graph_dashboard_preview.png)

## Thingsboard Setup
### Platform installation
Several options are available to have a Thingsboard platform up and running, as 
explained [here](https://thingsboard.io/docs/user-guide/install/installation-options/)
(on-premise, cloud, cluster setup, etc.)
However, the fastest way to run a platform sample is to use Docker. 
Here is provided [docker-compose.yml](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/docker-compose.yml) file for running on Linux or Mac OS system, with
the thingsboard/tb-postgres version 3.6.4 (the one on which the widget was 
developed and tested) and in-Memory queue service configuration (suitable for development and testing). 
[Check the documentation](https://thingsboard.io/docs/user-guide/install/installation-options/)
for other platforms and/or architecture options (Cassandra DB, Docker for Windows, queue service with Kafka, etc.)

### Configuration of data and logs folders (Docker for Linux/Mac OS only)
These instructions come from [Docker for Linux/Mac OS installation docs](https://thingsboard.io/docs/user-guide/install/docker/) 
and are necessary to grant required writing permissions.
Before starting your Docker containers, execute the following commands to create 
directories for data storage and logs. These commands will also change the 
ownership of the newly created directories to the Docker container user.

The chown command is used to change the owner of the directories, and it requires 
sudo permissions (change location of folders also by editing 
[docker-compose.yml](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/docker-compose.yml) if 
needed). You may be prompted to enter a password to grant sudo access:

  ```bash
  mkdir -p ~/.mytb-data && sudo chown -R 799:799 ~/.mytb-data
  mkdir -p ~/.mytb-logs && sudo chown -R 799:799 ~/.mytb-logs 
  ```

### Run
Once permissions are properly configured, run the image with docker

  ```bash
    docker compose up -d 
  ```

### Logs
To see ThingsBoard container logs execute the following command

  ```bash
    docker compose logs -f mytb 
  ```

### Stop
To stop the platform using docker
  ```bash
    docker compose down
  ```

### Installation troubleshooting
You can refer [here](https://thingsboard.io/docs/user-guide/install/docker/#troubleshooting)
if you experience problems during setup, or in the appropriate troubleshooting section
of the chosen installation method documentation, if different from Docker/Linux or Mac OS.

### Accessing the web interface and default accounts
The web-application interface is available at http://localhost:8080 and these default
credentials are created at installation time:

- **System Administrator:** sysadmin@thingsboard.org / sysadmin
- **Tenant Administrator:** tenant@thingsboard.org / tenant
- **Customer User:** customer@thingsboard.org / customer

You can always change passwords for each account in account profile page.

## Sample data import into platform

### Version control configuration
Login into the web interface as the tenant administrator, go to 'Advanced features',
'Version control', and put this repository URL and the branch into configs. You can
flag 'Read-only' and skip Authentication settings, if you're only interested in 
running the demo without any further backup of your data.

Click 'Check access' and make sure that the successful verification of repository
message appears.

![Project preview](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/utils/img/version_control_config.jpg)

Save your repository settings and now you should see the list of commits of the repository.

![Project preview](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/utils/img/version_control_set.jpg)

### Import of entities into platform
From the commit list, click the 'Restore version' button (the one on the right 
with a clock-arrow icon) and choose identities to restore. By default they're 
all selected and if you leave proposed defaults untouched it's fine. At the end
of the process, you should see a summary of all restored entities:

![Project preview](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/utils/img/restored_entities_message.jpg)

### Troubleshooting import

If you experience errors in entities restoration process, try to 
play with restoration options in the dialog, or selectively 
import every kind of necessary entity in a single step, 
one-by-one (you don't need all of them for this demonstration
purpose, but at least: assets, devices, dashboards, device profiles,
asset profiles, widgets, resources).

### Fix buggy import of custom widget<a name="fix-buggy-import"></a>
Now, if you go to the _Dasbboard_ section of web interface, you will notice the 
_Graph_Project_ dashboard. However, if you open it, you will notice a message
error in the custom Entities graph widget, which is unable to load the javascript
resource. This is due to a bug in the import of resources from version control, 
which leaves in the widget the reference of the javascript resource with the 
specific identifier of the platform from which entities have been exported.
You will have to manually fix this by editing the custom widget definition.
Go to '_Resources_', '_Widget Library_' main menu, type the name of '_Entities graph_'
in the search bar, than click the '_Widget details_' icon (the one with the pencil icon),
then the _'Edit widget'_ button. The widget editing interface appears and it shows the
resource import error on the lower right block:

![Project preview](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/utils/img/widget_edit_interface.jpg)

On the upper left block, click on _'Resources'_, then click on the 
_'Javascript/CSS URL'_, and select the item from dropdown menu (assuming the resource
has been correctly imported from version control) _'Thingsboard EntitiesGraphWidgetComponent'_ 
(leave 'Is Module' checked). Hit 'Save' on the right of the upper bar, widget 
reloads and now the error should disappear and a sample preview be visible:

![Project preview](https://raw.githubusercontent.com/rutbastoni-unicam/thingsboard-tbdm-graph-project/master/utils/img/widget_resource_fixed.jpg)

### Run sample dashboard
You can now go back to the dashboards section, open the 'Graph_project' dashboard,
and play with the graph widget. You can click on the upper right icon of its block 
to enlarge it at fullscreen size. Instructions about how to adjust visualization and handle assets and devices are
printed on the bottom part of the graph.

In the dashboard list there is also the 'Safe_Project' from which the dashboard was
forked and adapted to run the graph widget, instead of the hierarchical default
display. Graph_Project dashboard is also configured to let you navigate, by means
of controls activated on nodes, to the 'floor' dahsboard sub-state 
(showing pictures and schemas of assets and devices), like the 'Safe_Project' it
inherits from.

## Widget development
### Thingsboard extensions and custom widget
Exhaustive documentation about developing custom widget for Thingsboard is available
[here](https://thingsboard.io/docs/user-guide/contribution/widgets-development/).
A widget is substantially a structured json configuration file, encapsulating html, CSS and
javascript, as well as reference to compiled resources and other kinds of configuration.

Although development is possible in pure vanilla javascript, the community provides
the [Extension ThingsBoard Platform](https://github.com/thingsboard/thingsboard-extensions),
which is an Angularjs-based collection of convenient ready-to-use objects, useful for
reusing standard Thingsboard graphical components, easily accessing authentication 
data and invoking platform API's, and so on. It has been leveraged for this project,
by forking the repository and creating the 
[thingsboard-extensions-graph-tbdm](https://github.com/rutbastoni-unicam/thingsboard-extensions-graph-tbdm)
repository, containg the 
[EntitiesGraphWidgetComponent ](https://github.com/rutbastoni-unicam/thingsboard-extensions-graph-tbdm/tree/master/src/app/widgets/components/entity)
utilized in the custom widget. Instructions about including dependencies, running
and building are included in the repository 
[README](https://github.com/rutbastoni-unicam/thingsboard-extensions-graph-tbdm/blob/master/README.md) file.

### Running widget in development mode
The widget in the repository includes the compiled Typescript bundle, anyway it 
is possible to apply modifications at the component and testing it on the fly, in 
a dashboard or in the widget editor interface, by referencing the development
real-time compiled bundle. To do so, first follow extensions project README instructions:

  ```bash
    cd ${TB_EXTENSION_WORK_DIR}
    yarn install
    yarn start
  ```

Next, edit the resource block in the widget editor interface, like described in
the [Fix buggy import of custom widget](#fix-buggy-import-of-custom-widgeta-namefix-buggy-importa),
but, instead of selecting the component from the dropdown, paste the URL of
the compiled developed bundle:

```
http://localhost:5000/static/widgets/thingsboard-extension-widgets.js
```
_Hint: if your development machine is not reachable from the Thingsboard running server
(e.g. Thingsboard installed on the cloud) you can leverage a tunnel service like 
[Ngrok](https://ngrok.com/) to expose your machine and port with a public assigned
domain for free._

You can at any time recompile the dist bundle as explained in extensions [README](https://github.com/rutbastoni-unicam/thingsboard-extensions-graph-tbdm/blob/master/README.md):

  ```bash
    cd ${TB_EXTENSION_WORK_DIR}
    yarn build
  ```

and find the compiled file at the following path:

  ```
    ${TB_EXTENSION_WORK_DIR}/target/generated-resources/thingsboard-extension-widgets.js
  ```

With compiled production bundle you will be able to load it into 
_'Thingsboard EntitiesGraphWidgetComponent'_ resource library (_'Resources'_ section
of your Thingsboard instance).

### Thingsboard REST API
You can find the full set of available REST API documentation at http://localhost:8080/swagger-ui/index.html.

Wrappers for executing REST API calls are attached to widget context object, and are 
exposed in the _WidgetContext_ interface (see [widget-component.models.ts](https://github.com/rutbastoni-unicam/thingsboard-extensions-graph-tbdm/blob/master/src/app/widgets/models/widget-component.models.ts)),
like _deviceService_, _assetService_, _entityRelationService_, and so on.
They expose all main features of REST APIs, however if something is needed and missing 
it should be possible to extend a service or create a new one.

## Author

* **Rut Bastoni** - [rutbastoni-unicam](https://github.com/rutbastoni-unicam)
