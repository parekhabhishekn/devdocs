---
layout: default
group: cloud
subgroup: 08_setup
title: Import an existing Magento project
menu_title: Import an existing Magento project
menu_order: 77
menu_node: 
level3_menu_node: level3child
level3_subgroup: import
version: 2.0
github_link: cloud/access-acct/first-time-setup_import-prepare.md
---
 
This topic discusses how to can start your Magento Enterprise Cloud Edition project from an existing Magento Enterprise Edition (EE) installation. 

## Prerequisites
Before you continue, make sure you have done all of the following:

*   Your existing Magento EE code must be in Git. 

    If not, you must add it to Git before continuing.
*   Complete the tasks discussed in [Set up a Magento workspace]({{page.baseurl}}cloud/before/before-workspace.html).

This is our recommended workflow:

1.  Create a new, empty Magento Enterprise Cloud Edition project from a template.

    This new project has files and directories specific to Magento Enterprise Cloud Edition.
2.  Replace the contents of this project with your code.

    This replaces not only Magento code but static and compiled assets as well.
3.  Import your Magento database into your Magento Enterprise Cloud Edition project.
4.  TBD

{% include cloud/new-project-from-template.md %}

## Prepare to import Magento files and database {#cloud-import-prepare}
This section discusses tasks you must perform in your existing Magento EE installation to prepare it to be imported into a Magento Enterprise Cloud Edition project.

Before you continue, push all pending changes to Git.

You must do the following:

*   Add Cloud-specific files and directories to Magento EE. Without these files and directories, your Magento EE code can't be imported to Cloud.
*   Modify your existing `composer.json` to specify Cloud-specific dependencies.
*   Add `composer.lock` to Git because Cloud reads it, and not `composer.json`, during the build and deploy process.
*   Add your Magento EE authentication credentials to `auth.json` if you haven't done so already.
*   Dump your Magento EE database.

### Prepare Magento EE files {#cloud-import-prepare-files}
For your Magento EE code to import to a Magento Enterprise Cloud Edition project, you must have a directory and some files required by Cloud. Following is the list of those files:

*  [`.magento/routes.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_routes.html)
*  [`.magento/services.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_services.html)
*  [`.magento.app.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_magento-app.html)
*  `magento-vars.php`

#### Add required configuration files {#cloud-import-prepare-files-config}
To add required files to your Magento EE code:

1.  Go to the [Magento Enterprise Cloud Edition GitHub](https://github.com/magento/magento-cloud){:target="_blank"}.
2.  Select the branch corresponding to the Magento EE version you currently have.

    The following figure shows an example of selecting the `2.1.4` branch.

    ![Switch to your current EE branch]({{ site.baseurl }}common/images/cloud_cloud-git-214.png){:width="600px"}

    In the procedure that follows, you'll copy the contents of some of these files to your Magento EE system.
3.  Log in to your Magento EE system as, or switch to, the Magento file system owner.
4.  Enter the following commands in the order shown:

        cd <Magento installation dir>
        mkdir .magento
5.  One at a time, create the following files in your Magento EE system using the contents of the files in the Magento Enterprise Cloud Edition GitHub:

    *   `<Magento EE install dir>/.magento.app.yaml`
    *   `<Magento EE install dir>/magento-vars.php`
    *   `<Magento EE install dir>/.magento/services.yaml`
    *   `<Magento EE install dir>/.magento/routes.yaml`

    For example, to create `<Magento EE install dir>/.magento.app.yaml` from the 2.1.4 branch:

    1.  In the  Magento Enterprise Cloud Edition GitHub, click [**.magento.app.yaml**](https://github.com/magento/magento-cloud/blob/2.1.4/.magento.app.yaml){:target="_blank"}.
    2.  In the upper right, click **Raw**, as the following figure shows.

        ![View the raw version of the file]({{ site.baseurl }}common/images/cloud_cloud-git_raw.png){:width="600px"}
    3.  In your Magento EE project, open a text editor in the Magento EE installation directory (for example, `/var/www/html/magento2`).
    4.  Paste the raw contents of `.magento.app.yaml` from GitHub into the text editor.
    5.  Make sure the file is named `.magento.app.yaml` when you save the file.
    6.  Repeat these tasks for the other files.

        Make sure to create `magento-vars.php` in the Magento root directory.

        Make sure to create `routes.yaml` and `services.yaml` in the `.magento` subdirectory.

Modify these files as necessary as discussed in the following topics:

*  [`.magento/routes.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_routes.html)
*  [`.magento/services.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_services.html)
*  [`.magento.app.yaml`]({{ page.baseurl }}cloud/project/project-conf-files_magento-app.html)

#### Add or update `auth.json` {#cloud-import-authjson}
To enable you to install and update Magento Enterprise Cloud Edition, you must have an `auth.json` file in your project's root directory. `auth.json` contains your Magento EE [authorization credentials](http://devdocs.magento.com/guides/v2.1/install-gde/prereq/connect-auth.html) for Magento Enterprise Cloud Edition.

In some cases, you might already have `auth.json` so check to see if it exists and has your authentication credentials before you create a new one. It's located in your Magento root directory.

[Get a sample `auth.json`](https://raw.githubusercontent.com/magento/magento-cloud/master/auth.json.sample){:target="_blank"}

To create a new `auth.json` in the event you don't have one:

1.  Use a text editor to create a file named `auth.json` in your Magento root directory.
3.  Replace `<public-key>` and `<private-key>` with your Magento EE authentication credentials.

    See the following example:

        "http-basic": {
           "repo.magento.com": {
              "username": "<public-key>",
              "password": "<private-key>"
            }
        }
3.  Save your changes to `auth.json` and exit the text editor.

### Edit `composer.json` {#cloud-import-composer}
Before you push code to the Magento Enterprise Cloud Edition Git repository, you must change your `composer.json` so it meets Cloud requirements.

[View a sample `composer.json`](https://raw.githubusercontent.com/magento/magento-cloud/master/composer.json){:target="_blank"}

To edit `composer.json`:

1.  If you haven't done so already, log in to your Magento Enterprise Cloud Edition server as, or switch to, the Magento file system owner.
2.  In a text editor, open `composer.json` in the project root directory.
3.  Substitute the following value in the `require` section:

        "magento/product-enterprise-edition": "<version>",

    with

        "magento/magento-cloud-metapackage": "<version>",
    
    <div class="bs-callout bs-callout-info" id="info" markdown="1">
    Both `<version>` values _must be the same_. In other words, if your current EE version is 2.1.3, your `magento-cloud-metapackage` version must also be 2.1.3.
    </div>

4.  Update the `"files"` directive in the `autoload` section to refer to `app/etc/NonComposerComponentRegistration.php` as follows:

        "autoload": {
        "psr-4": {
            "Magento\\Framework\\": "lib/internal/Magento/Framework/",
            "Magento\\Setup\\": "setup/src/Magento/Setup/",
            "Magento\\": "app/code/Magento/"
        },
        "psr-0": {
            "": "app/code/"
        },
        "files": [
            "app/etc/NonComposerComponentRegistration.php"
        ]
    }
5.  Save your changes to `composer.json` and exit the text editor.
6.  Run `composer update` to update `composer.lock`:

        composer update
7.  Wait while dependencies are updated.
6.  When you're done, commit the changes to GitHub:

        cd <Magento EE install dir>
        git add -A && git commit -m "Add Cloud files" && git push origin <branch name>

### Back up media files
This section discusses how to use the [`magento setup:backup --media`]({{ page.basesurl }}install/cli/install-cli-backup.html) to back up media files.

1.  To back up media files, enter the following command:

        php <Magento EE install dir>/bin/magento setup:backup --media

    The backup is stored in the `<Magento EE install dir>/var/backups` directory.
2.  Transfer the media file to your Magento Enteprise Cloud Edition system:

        rsync <Magento EE install dir>/var/backups/<backup file name> <cloud ssh url>:var/media.tgz

    For example,

        rsync /var/www/html/magento2/var/backups/1487962699_filesystem_media.tgz 43bkopvkhelhy-master-l8uv4kp@ssh.us.magentosite.cloud:var/media.tgz

### Copy the encryption key
To be able to decrypt encrypted data from your imported database, copy your encryption from your existing `env.php` file. `env.php` contains a nested PHP array storing configuration data. 

1.  Open `<Magento install dir>/app/etc/env.php` in a text editor.
2.  Search for the value of `key` (it's in the `crypt` array).
3.  Copy the value to the clipboard and save it.

    You must paste the encryption key into your Magento Enterprise Cloud Edition `env.php` file later.

### Prepare the Magento EE database  {#cloud-import-prepare-db}
Create a dump of the database you want to import using mysqldump. 

The following example shows how to compress the dump so it doesn't significantly interfere with traffic from in live site. 

In the example, the dump file is named `db.sql.gz`. It's a good idea to include the date in the filename if you do multiple dumps over time.

Because the database dump can be large, we recommend you create it in a directory not tracked by Git.

Command syntax follows:

    mysqldump -h <db-host> -P <db-port> -p -u <db-user> <db-name> --single-transaction --no-autocommit --quick | gzip > ~/db.sql.gz

Example if your database is on localhost with the default port (3306), database user name is `magento`, and database name is also `magento`:

    mysqldump -p -u magento magento --single-transaction --no-autocommit --quick | gzip > ~/db.sql.gz

## Import files and Magento code {#cloud-import-files-and-db}
This section discusses how to import code from your existing Magento EE project to your Magento Enterprise Cloud Edition's Git repository `master` branch.

<div class="bs-callout bs-callout-warning" id="warning" markdown="1">
The procedure discussed in this topic replaces your new Magento Enterprise Cloud Edition project with the contents of your existing Magento installation. Any data, websites, stores, and so on will be lost.

Before you continue, make sure there is nothing in your Magento Enterprise Cloud Edition project you want to keep.
</div>

### Step 1: Create a remote Git reference {#cloud-import-ref}
This section discusses how to create a remote Git reference from your Cloud Git repository to the repository in which your Magento EE installation is located.

Before you continue, make sure you know the SSH or HTTPS URL for your Magento EE installation Git repository.

To create a remote Git reference:

1.  Log in to your local Magento Enterprise Cloud Edition development machine as, or switch to, the Magento file system owner.
2.  Make a copy of `composer.json` _in a non-tracked directory_ so it doesn't get overwritten.

        cp composer.json ../composer.json.cloud
3.  Rename your Cloud Git remote from `origin` to `cloud-project` to make it clear which repository is which:

        git remote rename origin cloud-project
4.  Add a remote upstream for your existing Magento EE installation:

        git remote add prev-project <git url>
5.  Confirm what you've done so far.

        git remote -v

    Results are displayed as follows.

    <pre class="no-copy">cloud-project   ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (fetch)
    cloud-project   ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (push)
    magento ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (fetch)
    magento ikyyrqvlgnrai@git.us.magento.cloud:ikyykimjgnrao.git (push)
    prev-project    git@github.com:mygitusername/myeereponame.git (fetch)
    prev-project    git@github.com:mygitusername/myeereponame.git (push)</pre>
6.  Make sure you're on the Cloud project `master` branch.

        magento-cloud environment:checkout master
7.  Make sure this `master` branch is set up to import code to the Cloud project.

        git branch -u cloud-project/master

### Step 2: Import your Magento EE code to your Cloud project {#cloud-import-imp}
Before you continue, make sure you've completed all tasks discussed in the preceding section.

To import your Magento EE code to Cloud:

1.  Fetch the Magento EE branch.

        git fetch prev-project
3.  Reset your Cloud `master` branch to contain the code and the commit history of your Magento EE branch:

        git reset --hard prev-project/<branch name>
4.  Push code from your Magento EE project to your Magento Enterprise Cloud Edition project, overwriting the previous contents and commit history with that of your project:

        git push -f cloud-project master

As the project builds and deploys, many messages are displayed on the screen. A successful deployment is incidated by the following messages:

    Re-deploying environment 43biovskhelhy-master-l5ut8gq.
       Environment configuration:
         mymagento (type: php:7.0, size: S, disk: 2048)
         mysql (type: mysql:10.0, size: S, disk: 2048)
         redis (type: redis:3.0, size: S)
         solr (type: solr:4.10, size: S, disk: 1024)

    Environment routes:
       http://master-o9gv6gq-43biovskhelhy.us.magentosite.cloud/ is served by application `mymagento`
       https://master-o9gv6gq-43biovskhelhy.us.magentosite.cloud/ is served by application `mymagento`

To 43biovskhelhy@git.us.magento.cloud:43bmmwdkhelhy.git
   445b5e8..b597726  master -> master

## Import the Magento database
Before you can use your existing Magento EE clode in Magento Enterprise Cloud Edition, you must import the database.

To import the Magento database in Magento Enterprise Cloud Edition, you must know:

*   The Magento Enterprise Cloud Edition environment's SSH URL
*   The database name, user name, and password of the Cloud database

<div class="bs-callout bs-callout-info" id="info" markdown="1">
This topic discusses how to import the [integration system]({{ page.baseurl }}cloud/discover-arch.html#cloud-arch-int) database. The database connection information is different for [staging]({{ page.baseurl }}cloud/discover-arch.html#cloud-arch-stage) and [production]({{ page.baseurl }}cloud/discover-arch.html#cloud-arch-prod) systems. You'll need the assistance of Magento Support before you can migrate your integration system database to staging or production.
</div>

### Find the SSH URL
You can find the environment's SSH URL in any of the following ways:

*   From the [project Web Interface]({{ page.baseurl }}cloud/project/project-webint-basic.html#project-access)
*   Using the following command:

        magento-cloud environment:ssh --pipe

An SSH URL is similar to the following:

    43bkopvkhelhy-master-l8uv4kp@ssh.us.magentosite.cloud

#### Database access
The name of the database can be found in the `$MAGENTO_CLOUD_RELATIONSHIPS` environment variable. Display the variable with the following command. The database name is stored under `databases->path`. The password is found under `databases->password`.

To find database access information:

1.  If you haven't already done so, log in to your local system as the Magento file system owner.
2.  Enter the following command:

        magento-cloud environment:ssh
3.  At the command prompt, enter the following command:

        echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp

The database connection information is displayed:

{% highlight yaml %}

"database" : [
      {
         "username" : "user",
         "query" : {
            "is_master" : true
         },
         "path" : "main",
         "port" : 3306,
         "host" : "database.internal",
         "password" : "",
         "scheme" : "mysql",
         "ip" : "192.0.2.150"
      }
   ]

{% endhighlight %}

In the preceding example, the database name is `main`, its listen port is `3306`, its host name is `database.internal`, its root user name is `user` and the user has no password.

### Transfer the database dump from Magento EE to Cloud
Use the `rsync` command as follows to transfer the database dump from your Magento EE system to the Magento Enterprise Cloud Edition environment.

Now that you have created the dump, move it to the var directory of the application you are importing into:

    rsync <db dump file name> <cloud ssh url>:var/db.sql.gz

### Drop and re-create the Cloud database
SSH into the cloud environment and empty the existing database, if it is populated. If you have done any work you would like to refer to later that's been done in the Cloud environment, then make a backup of that first. 

To drop and re-create the Cloud database:

1.  SSH to the Cloud environnment.

        magento-cloud environment:ssh
2.  Connect to the database.

        mysql -h <db-host> -P <db-port> -p -u <db-user> <db-name>

    For example, if your Cloud databases uses default values, enter:

        mysql -h database.internal -u user main
3.  Drop the database. At the `MariaDB [main]>` prompt, enter:

        drop database main;
4.  Re-create the database:

        create database <database name>;
5.  Exit the `MariaDB [main]>` prompt.

        exit
6.  At the shell command prompt, enter the followign command to re-create the database.

        zcat var/db.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h <db-host> -P <db-port> -p -u <db-user> <db-name> 

    For example,

         zcat var/db.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h database.internal -p -u user main

### Update base URLs
Before you can access Magento from your local Cloud development system, you must change the Base URLs in the Magento database. Base URLs are stored in the `core_config_data` table.

The following example shows how to change _only_ the insecure URL but you can use the same procedure to change secure URLs as well.

To update the unsecure base URL:

1.  Find the value of the integration system URL:

        magento-cloud url
2.  SSH to the Cloud integration server:

        magento-cloud ssh
3.  Connect to the database.

        mysql -h <db-host> -P <db-port> -p -u <db-user> <db-name>

    For example, if your Cloud databases uses default values, enter:

        mysql -h database.internal -u user main
4.  Show the contents of the `core_config_data` table.

        SHOW * from core_config_data;

    Note the `path` of `web/unsecure/base_url`; this is the value you'll change.
5.  Enter the following command to change the value of `path` to your integration server's unsecure base URL:

        UPDATE core_config_data SET value=<Cloud unsecure base URL> WHERE path=web/unsecure/base_url;
6.  Confirm the change by entering the following command:

        SHOW * from core_config_data;
7.  If the change was successful, enter `exit` to exit the `[Maria DB]` prompt.
8.  Continue with the next section.

<div class="bs-callout bs-callout-info" id="info" markdown="1">
For your system to be fully functional, you must also set unsecure and secure URLs for the default scope as well as for all websites, stores, and store views.
</div>

### Copy the encryption key

1.  SSH to the Cloud environnment.

        magento-cloud environment:ssh
2.  Open `app/etc/env.php` in a text editor.
3.  Replace the existing value of `key` with your Magento EE key.
4.  Save your changes to `env.php` and exit the text editor.

If `env.php` does not exist, create it with the following contents:

{% highlight php startinline=true %}
return array (
  'crypt' =>
  array (
    'key' => '<your encryption key>',
  ),
);
{% endhighlight %}

### Import media
To import media files into your Cloud environment:

1.  SSH to the Cloud environnment.

        magento-cloud environment:ssh
2.  Enter the following command to clear existing media files:

        rm -rf pub/media/*
4.  Enter the following command to extract the media files to the `pub/media` directory:

        tar -xzf var/media.tgz pub/media

### Cleanup
On the Cloud environment, flush the cache:

    bin/magento cache:flush

After the cache flushes, enter `exit` to close the SSH tunnel.

## Push the changes
TBD

## Verify the import
To verify everything imported properly, perform the following tasks in your local Cloud development environment:

1.  On your Cloud environment, enter the following commands to find the information to log in to the Magento Admin and to view the storefront:

        magento-cloud environment:url
        magento-cloud variable:list