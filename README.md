Commando.io - Dev-Ops Evolved
-----------------------------

Commando.io is a web-based interface for streamlining the use of SSH for deployments and system administration tasks across groups of remote servers.

GitHub fundamentally changed the way developers use revision control by creating a beautiful user interface and social platform. Commando.io does the same for managing servers & dev-op tasks.

The goal of Commando.io is to make it super simple to execute commands on groups of servers and visualize the results. Additionally Commando.io provides IT compliance and accountability, as every command executed is logged with who executed what, when, and why. Finally all commands are versioned and centrally stored.

Screenshots And Additional Details
--------------------------------

[ ![Web servers tagged and grouped.](http://cdn.commando.io/home/images/screenshots/small/servers.png) ](http://cdn.commando.io/home/images/screenshots/xlarge/servers.png)
[ ![Adding a recipe.](http://cdn.commando.io/home/images/screenshots/small/add-recipe.png) ](http://cdn.commando.io/home/images/screenshots/xlarge/add-recipe.png)
[ ![Executing a recipe on a group of servers.](http://cdn.commando.io/home/images/screenshots/small/execute.png) ](http://cdn.commando.io/home/images/screenshots/xlarge/execute.png)
[ ![A collection of various groups.](http://cdn.commando.io/home/images/screenshots/small/groups.png) ](http://cdn.commando.io/home/images/screenshots/xlarge/groups.png)

### See http://commando.io for additional details. ###

Important Notes
---------------

#### This is a very early alpha build of Commando.io. A few important pieces of the software have not been implemented. For security, do not expose Commando.io publicly! ####

* Users and log-in. **Again, please do not expose Commando.io publicly**. Run it locally, and use web-server authentication for now. A fully featured users and log-in system is coming.
* The ability to view execution history is not implemented. Execution history is written to *MongoDB*, but there is not an interface to view it yet.
* SSH connections and executions still happen via `PHP` using the `ssh2` extension. This is going to be replaced with a separate dedicated `node.js` SSH worker using websockets. PHP won't make SSH connections and executions in the near future.

Installation
------------

**1.)** Clone the repo, `git clone git://github.com/nodesocket/commando.git`, or [download the latest release](https://github.com/nodesocket/commando/tarball/master).

**2.)** Execute `$ php -f install.php`, or view `/install.php` from a browser. *The install script requires write access to the filesystem to copy and update configuration files.*

**3.)** Add the public and private SSH keys you wish to connect with into the `/keys` directory.

**4.)** Edit `/app.config.php` and provide the correct paths for:

     SSH_PUBLIC_KEY_PATH
     SSH_PRIVATE_KEY_PATH

**5.)** Create a user in MySQL to connect with.

**6.)** Edit `/classes/MySQLConfiguration.php` and provide the connection details to MySQL.

**7.)** Import the MySQL schema located in `/schema/latest.sql` into MySQL.

```` bash
	$ mysql --user=USERNAME --pass=PASSWORD --host=SERVERHOST DATABASE < /schema/latest.sql
````

**8.)**	Assign the MySQL user created above to the newly imported database `commando`.    

**9.)** Create a database `commando` and a collection `executions` in MongoDB. *If you need MongoDB hosting check out https://mongohq.com or https://mongolab.com.*

**10.)** Create the following standard indexes on the `executions` collection in MongoDB:   

```` json
    { "executed" : 1 }
    { "groups" : 1 }
    { "recipes.id" : 1 }
    { "servers.id" : 1 }
    { "recipes.interpreter" : 1 }
````

**11.)** Create a user in MongoDB to connect with.

**12.)** Edit `/classes/MongoConfiguration.php` and provide the connection details to MongoDB.

**13.)** Setup rewrite rules in the web-server.

#### nginx ####
```` nignx
location ~ ^[^.]+$ {
    fastcgi_param SCRIPT_FILENAME $document_root/controller.php;
    fastcgi_param SCRIPT_NAME /controller.php;
    fastcgi_param PATH_INFO $uri;
}
````

#### lighttpd ####
```` lighttpd
$HTTP["host"] =~ "^(your-domain\.com)$" {
        url.rewrite-once = (
                "^[^.]*$" => "controller.php/$1"
        )
}
````

Requirements
------------

### PHP ####
Version **5.3.0** or greater.

#### PHP Extensions ####
+ **mysqli**
+ **json**
+ **mongo** (https://github.com/mongodb/mongo-php-driver)
+ **ssh2** (http://pecl.php.net/package/ssh2)

#### MySQL####
Version **5.0** or greater running the **InnoDB** storage engine. *MyISAM is not supported.*

#### MongoDB ####
Version **2.0** or greater is highly recommended. Older versions of MongoDB may work.

Support, Bugs, And Feature Requests
-----------------------

Create issues on GitHub (https://github.com/nodesocket/commando/issues).

Versioning
----------

For transparency and insight into our release cycle, and for striving to maintain backward compatibility, Commando.io will be maintained under the semantic versioning guidelines.

Releases will be numbered with the follow format:

`<major>.<minor>.<patch>`

And constructed with the following guidelines:

+ Breaking backward compatibility bumps the major (and resets the minor and patch)
+ New additions without breaking backward compatibility bumps the minor (and resets the patch)
+ Bug fixes and misc changes bumps the patch

For more information on semantic versioning, visit http://semver.org/.

Contact
-------

+ http://commando.io
+ commando@nodesocket.com
+ http://twitter.com/commando_io

License & Legal
---------------

Copyright 2012 NodeSocket, LLC

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this work except in compliance with the License. You may obtain a copy of the License in the LICENSE file, or at:

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.