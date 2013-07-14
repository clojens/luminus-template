# Luminus-Template

A Leiningen template for projects using [Luminus](http://www.luminusweb.net/).

The template initializes a base Luminus application.

## Requirements

Luminus requires Leiningen version 2.x

## Usage

### Precautions

The Luminus template ships out of the box with the latest [Leiningen][l2] build
tool for Clojure. Make sure to have it properly installed and that your
references are up-to-date. You may find Linux/OSX/Windows instructions on how
to setup leiningen [here][l2s].

### Create a new Luminus project

Use the `lein new` command as you normally would, from your virtual terminal
emulator (or PowerShell) command line interface, to create a new default luminus
project from template as follow:

```bash
lein new luminus *<your project name goes here>
```

### Available plugins for Luminus

Luminus has a relatively small collection plugins available to mix in the
template at construction time. So, if you would like to attach further
functionality to your template you can append [profile hints][ph] for these
extended features:

* `+bootstrap` adds [Twitter Bootstrap][tbs] CSS/JS static resources
* `+cljs` adds [ClojureScript][cljs] support to the project along with an example
* `+h2` adds models.db namespace and [H2 database][h2] dependencies
* `+postgres` adds models.db namespace and add [PostreSQL database][pg] dependencies
* `+mysql` adds models.db namespace and add [MySQL/MariaDB database][my] dependencies
* `+site` registration/authentication, uses `+bootstrap` and `+h2` by default
* `+dailycred` combined with `+site` it uses [DailyCred][dc] to authenticate
* `+http-kit` - adds the fast [HTTP Kit][kit] web server to the project

### Add plugins to project during bootstrap

To add a profile simply pass it as an argument of the terminal command, right
after your application name, e.g.:

```bash
lein new luminus myapp +bootstrap
```

You can also mix multiple profiles when creating the application, for example:

```bash
lein new luminus myapp +site +postgres
```

### Run a web (application) server

By default, luminus uses the bTo start the default (web) application ring server use:

```bash
lein ring server
```

However, should you have installed using the `+http-kit` profile,
you need to use the following instead:

```bash
lein run
```

### Creating (web) executable archives

To build as a executable [Java ARchive (JAR)][jar] standalone, using
either the ring server or http-kit, run the following command:

```bash
lein ring uberjar
```

To build a [WAR][war] (or Web application ARchive) file run:

```bash
lein ring uberwar
```

You can then easily deploy the resulting WAR to Tomcat or any other Java application
server.

### Run as standalone Java executable archive

To run the standalone executable `.jar` file, do as you would with any other Java
runtime program using the `java -jar` command:

```bash
user$ java -jar target/myapp-0.1.0-SNAPSHOT-standalone.jar

2012-12-15 19:17:23.471:INFO:oejs.Server:jetty-7.x.y-SNAPSHOT
2012-12-15 19:17:23.512:INFO:oejs.AbstractConnector:Started
SelectChannelConnector@0.0.0.0:8080
Server started on port [ 8080 ].
You can view the site at http://localhost:8080
```

### Getting help

Please keep in mind that luminus is a web framework built potentially by
combining many different external projects. Please make sure you know which
pieces are failing and to what project they belong. 

Please reference those respective project manuals for further information on
how to configure .e.g. the [virtual machine][jvmdoc], [Ring library][ringdoc]
or [http-kit][httpdoc] web server.

For example, in the previous section we used the command `java` to configure
(start) your Java Virtual Machine (JVM) instance. However, this is only but one
possible way to start and/or configure the virtual machine. since Clojure has a
modern day build-tool in the form of leiningen, many configuration options are
also addressable from within the lein `project.clj` file.

For example: to increase the memory of a JVM, one could do this the
traditional way (from the terminal), using the same `java` command with addition
of a extra argument that says `-Xms1024m` to increase the memory reserved to 1G.
Or you could do it the Clojure/leiningen way, by adding the key/value
`:jvm-opts ["-Xmx1g"]` to the `defproject` declaration inside the `project.clj`

Potentially, there are multiple places where many settings can be (mal)configured.
So while it is nearly impossible, nor desirable, to list all these settings and
the possible ways they can interact here, and while we do have all these projects
under the luminus roof, it does not mean this is a actually a luminus problem.

So please make sure something is a luminus problem before you submit a bugreport.

You may find a this [leiningen sample project definition][exproj] file useful
as a reference to the available configuration options.

### Precautions

Usage of Leiningen versions below 2.0 or software depending on older Clojure
versions (below ~ 1.4.1) will probably result in partial or complete
malfunctioning of your web application software. You should always use the latest
Clojure, Leiningen and Luminus release versions.

#### Checking for latest versions

You can use the [lein-ancient][ancien] plugin to check if there are newer
versions of Clojure projects. This will apply to all projects in the dependency
tree, including those defined in any currently active lein profiles e.g.
`~/.lein/profiles.clj` but possibly elsewhere as well.

The `lein ancient` command will only function properly inside a project folder,
so first bootstrap the new luminus project as outlined above, next `cd` into
that created directory. Only then use the command `lein ancient` as illustrated
in the example below:

```bash
[user ~]$ lein new luminus foobarr +site +http-kit +cljs
Generating a lovely new Luminus project named foobarr...
[user ~]$ cd foobarr
[user foobarr]$ lein ancient
[com.taoensso/timbre "2.3.0"] is available but we use "2.1.2"
[http-kit "2.1.6"] is available but we use "2.1.4"
[cljs-ajax "0.1.5"] is available but we use "0.1.4"
[enlive "1.1.1"] is available but we use "1.0.0"
[jline "2.11"] is available but we use "0.9.94"
[ring/ring-devel "1.2.0"] is available but we use "1.1.8"
```

Finally, you may use that list to edit either the `project.clj` file, the
global user profile `~/.lein/profiles.clj` or any other file with profiles,
depending on where you've kept these. Sadly, at time of this write-up,i the
plugin doesn't tell you where to find that reference, nor is there is
automation of the update process in Clojure/Leiningen so fire up your favorite
editor and manually track them down, updating the numbers to reflect the
correct version. So again, should you not be able to find in the project,
chances are you will find it in profile. Use `grep -irn 'search terms'` if you
really can't find it.

Finally, although luminus does the heavy lifting in terms of bootstrapping the
(boilerplate) code and dependency injections, you may always want to manually
check the project tree itself. Firstly, by using the shell command `lein
deps-tree`, you will automatically pull in any dependencies from that project
which you do not have in your local Maven2 repository `~/.m2/repository/`.
Secondly, running the command will parse the tree and probably, if lein ancient
succeeded, not throw you any errors besides the expected 'missing local
software' notifications, which it will begin downloading immediately. Most
importantly though, you need to remember that there can be conflicts between
libraries and their dependencies, that do not result in any (observable)
malfunctioning per se, but still remain desirable to gracefully be solved
(excluded or by other means). Pay extra attention to the top of the tree, since
it is there where the messages will be printed should there be issues.


## License

Copyright © 2012 Yogthos

Distributed under the Eclipse Public License, the same as Clojure.

[ph]: <http://www.luminusweb.net/docs/profiles.md>
[tbs]: <http://twitter.github.io/bootstrap/>
[cljs]: <https://github.com/clojure/clojurescript>
[h2]: <http://www.h2database.com/html/main.html>
[pg]: <http://www.postgresql.org/>
[my]: <https://mariadb.org/>
[dc]: <https://www.dailycred.com/>
[kit]: <http://http-kit.org/>
[war]: <http://en.wikipedia.org/wiki/WAR_file_format_(Sun)>
[jar]: <http://en.wikipedia.org/wiki/Jar_file>
[ancien]: <https://github.com/xsc/lein-ancient>
[jvmdoc]: <http://pic.dhe.ibm.com/infocenter/java7sdk/v7r0/index.jsp?topic=%2Fcom.ibm.java.zos.70.doc%2Fdiag%2Fappendixes%2Fcmdline%2Fcommands_jvm.html>
[exproj]: <https://github.com/technomancy/leiningen/blob/master/sample.project.clj>
[ringdoc]: <https://github.com/ring-clojure/ring/wiki>
[httpdoc]: <http://http-kit.org/server.html>
