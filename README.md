[![Stories in Ready](https://badge.waffle.io/yukoff/openshift-cartridge-percona-server.png?label=ready&title=Ready)](https://waffle.io/yukoff/openshift-cartridge-percona-server)
# Percona Server cartridge for OpenShift (custom)

![percona-server](http://imgh.us/percona-server-logo.png) ![openshift](http://imgh.us/openshift-logo.png)

This is a custom OpenShift cartridge providing the latest Percona Server version (5.7.15 as of October 10th 2016).

## Why

Because the standard OpenShift MySQL cartridge is stuck at 5.5 and some people are keen to use the latest MySQL server features (such as improved support for spatial data). This cartridge takes things even further, replacing standard MySQL with Percona Server.

## When to use

When you need a quick and unsofisticated solution to run your application with the latest Percona Server version.

## Installing

To install this cartridge in your existing OpenShift application, go to **"See the list of cartridges you can add"**, paste the URL below in **"Install your own cartridge"** textbox at the bottom of the page and click "Next".

    http://cartreflect-claytondev.rhcloud.com/github/yukoff/openshift-cartridge-percona-server

## Setting up

Once the cartridge is created and started, you can SSH into the database gear:
```
ssh <gear-url>
```
...and connect to the server with `mysql` client like this:
```
${OPENSHIFT_DATA_DIR}.mysql/usr/bin/mysql --socket=${OPENSHIFT_MYSQL_DB_SOCKET} \
    --user="${OPENSHIFT_MYSQL_DB_USERNAME}" \
    --password="${OPENSHIFT_MYSQL_DB_PASSWORD}" \
    ${OPENSHIFT_APP_NAME}
```

If you really need it, you can enable remote access for ${OPENSHIFT_MYSQL_DB_USERNAME} like this:
```
${OPENSHIFT_DATA_DIR}.mysql/usr/bin/mysql --socket=${OPENSHIFT_MYSQL_DB_SOCKET} \
    --user="${OPENSHIFT_MYSQL_DB_USERNAME}" \
    --password="${OPENSHIFT_MYSQL_DB_PASSWORD}" \
    -e "GRANT ALL PRIVILEGES ON *.* TO '${OPENSHIFT_MYSQL_DB_USERNAME}'@'%' IDENTIFIED BY '${OPENSHIFT_MYSQL_DB_PASSWORD}' WITH GRANT OPTION; FLUSH PRIVILEGES;"
```

If you're using multiple gears, here's how you can find the MySQL gear SSH url:
```
rhc app show <application name> --gears
```

Once you've enabled remote access, you can use `rhc port-forward` and MySQL Workbench or your favorite client to connect from your development machine.

Use `OPENSHIFT_MYSQL_DB_HOST`, `OPENSHIFT_MYSQL_DB_PORT`, `OPENSHIFT_MYSQL_DB_USERNAME` and `OPENSHIFT_MYSQL_DB_PASSWORD` environment variables to connect from an application running in the main web cartridge. For instance, here's how you'd do it in a Node.js application using [Knex.js](http://knexjs.org/):
```javascript
var knex = require('knex')({
    client: 'mysql',
    connection: {
        host     : process.env.OPENSHIFT_MYSQL_DB_HOST,
        port:    : process.env.OPENSHIFT_MYSQL_DB_PORT,
        user     : process.env.OPENSHIFT_MYSQL_DB_USERNAME,
        password : process.env.OPENSHIFT_MYSQL_DB_PASSWORD,
        database : process.env.OPENSHIFT_APP_NAME,
        charset  : 'utf8'
    }
});
```

## Notes

- There's no guarantee this cartridge is production-ready. Some people use it though (on **their own responsibility**).
- This is a **lean cartridge**. A standard MySQL installation takes a huge amount of space (over 1.5GB for MySQL 5.7.5). To save space, just the necessary MySQL binaries are installed.
- ~~[FIXME] In order to avoid an OpenShift configuration conflict, **the server instance is listening on 13306 instead of the standard MySQL port 3306**.~~ Should be complete drop-in replacement for standard OpenShift's MySQL cartridge.
- Don't hesitate to make a pull-request with an updated version in [this file](https://github.com/yukoff/openshift-cartridge-percona-server/blob/master/metadata/manifest.yml#L4) if you notice this cartridge version is behind the latest [Percona Server release](https://www.percona.com/downloads/Percona-Server-5.7/LATEST).
- **Don't open issues in this repository to ask questions about `rhc port-forward`**. Please refer to the [OpenShift documentation](https://developers.openshift.com/en/managing-port-forwarding.html) to learn about it. I am (and the author of the original mysql custom cartridge - @icflorescu) not an employee of RedHat / OpenShift, nor do we have any form of consultancy agreement with them and the fact that this cartridge was open-sourced doesn't mean either of contributors are willing to offer free advice on the subject. Pull-requests and suggestions are always welcome, though.

## TODO

Add upgrade script based on the https://github.com/openshift/origin-server/blob/master/cartridges/openshift-origin-cartridge-mysql/bin/upgrade

## Related

Since you're here, there are chances you might also be interested in this [custom Node.js cartridge](https://github.com/icflorescu/openshift-cartridge-nodejs) or this [custom MongoDB cartridge](https://github.com/icflorescu/openshift-cartridge-mongodb).

## Credits and attributions

This cartridge was inspired by the [custom MySQL cartridge](https://github.com/icflorescu/openshift-cartridge-mysql), which in turn was inspired by [Ted Wennmark](https://se.linkedin.com/in/tedwennmark)'s [blog post](http://mysql-nordic.blogspot.ro/2015/02/creating-minimal-mysql-installation-for.html) on how to create a minimal MySQL installation for an embedded system.

## Credits

See contributors [here](https://github.com/yukoff/openshift-cartridge-percona-server/graphs/contributors).

If you find this repo useful, don't hesitate to give it a star and [spread the word](http://twitter.com/share?text=Checkout%20this%20custom%20Percona%20Server%20cartridge%20for%20OpenShift!&amp;url=http%3A%2F%2Fgithub.com/yukoff/openshift-cartridge-percona-server&amp;hashtags=PerconaServer,MySQL,database,OpenShift&amp;via=yu_koff).

## Copyright & License

The [Apache 2.0 License](http://github.com/yukoff/openshift-cartridge-percona-server/blob/master/LICENSE).

@icflorescu's [custom MySQL cartridge](https://github.com/icflorescu/openshift-cartridge-mysql) is licensed under the [ISC License ](https://github.com/icflorescu/openshift-cartridge-mysql/blob/master/LICENSE).
