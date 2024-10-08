This is the README file distributed together with the
[Oddmuse](https://oddmuse.org/) script.

## Installing Oddmuse on a Debian System running Apache

The following instructions require a number of tools. You can make sure
they're all installed by issuing the following command as `root`:

    apt-get install coreutils apache2 sudo wget w3m perl \
        libwww-perl libxml-rss-perl diffutils

You probably created an account for yourself. You might have to add this
user to the `sudo` group. Here's how I created my own user as `root`:

    adduser alex
    usermod -a -G sudo alex

Now you can login as `alex` and do everything else using `sudo`.

You need to copy wiki.pl into your cgi-bin directory, and you need to
make the script executable. You might also have to change its owner to
an appropriate user on your system.

    sudo wget -O /usr/lib/cgi-bin/wiki.pl \
      http://git.savannah.gnu.org/cgit/oddmuse.git/plain/wiki.pl
    sudo chmod +x /usr/lib/cgi-bin/wiki.pl
    sudo chown www-data.www-data /usr/lib/cgi-bin/wiki.pl

If you're on SUSE, the user might not be `www-data` but `wwwrun` without
appropriate group:

    sudo chown wwwrun.root /usr/lib/cgi-bin/wiki.pl

You should be able to test it right now! Visit
`http://localhost/cgi-bin/wiki.pl`. If your site is available from the
outside, you will be able to use a normal browser. If don't have a
domain name yet, you'll probably have to use a text browser like `w3m`.

    w3m http://localhost/cgi-bin/wiki.pl

If you create pages in this wiki, these will get stored in a temporary
directory. You need change the data directory from `"/tmp/oddmuse"` to
like `"/var/local/oddmuse"`. The best way to do this without changing
`wiki.pl` is by editing `/etc/apache2/sites-available/default`. Add the
following line:

    SetEnv WikiDataDir /var/local/oddmuse

Enable the default site by calling the following command:

    sudo a2ensite default

Reload the Apache configuration by calling the following command:

    sudo service apache2 reload

You need to create the new data directory. You webserver runs CGI
scripts as `www-data`. Thus, you need to change the owner and group of
the directory to `www-data`.

    sudo mkdir -p /var/local/oddmuse
    sudo chown www-data.www-data /var/local/oddmuse

Done! Visit your wiki and start editing. Click on the edit link (the
first link below the navigation bar, at the bottom of the page). This
will allow you to enter some text for this page. Click the Save button
and you are done.

To add new pages, edit the homepage and add links to new pages by
putting their names in `[[double square brackets]]`.

Enjoy your wiki experience.

Visit <https://www.oddmuse.org/> to learn more about the translation
files and modules that are part of this package.

## Checking the Apache Setup

If you think this information doesn't work for you, here are some things
to check.

Apache's config directory is `/etc/apache2/apache2.conf`. This is where
we get the `www-data` username from. It says:

    # These need to be set in /etc/apache2/envvars
    User ${APACHE_RUN_USER}
    Group ${APACHE_RUN_GROUP}

Checking `/etc/apache2/envvars` we see the following:

    export APACHE_RUN_USER=www-data
    export APACHE_RUN_GROUP=www-data

So that's what we're using in the `chown` command in our instructions
above.

The default site is configured in
`/etc/apache2/sites-available/default`. In order for it to be *enabled*,
there must be a symlink from a file in `/etc/apache2/sites-enabled` to
the file in `sites-available`. You can enable it using the following
command:

    sudo a2ensite default

This file also lists the directories we've used in our instructions
above.

    ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/

This means that `http://localhost/cgi-bin/wiki.pl` will call
`/usr/lib/cgi-bin/wiki.pl`

Don't forget to reload the Apache configuration as shown above, or
simply restart it all:

    sudo service apache2 graceful

## Using just Perl

You can use Mojolicious as your web server. There is a simple
\##server.pl## which you can use. Here's how you might start it:

    mkdir ~/oddmuse
    WikiDataDir=$HOME/oddmuse perl server.pl daemon

This makes the server available on `http://localhost:3000/wiki`. Make
sure you create the directory before starting the server! If you don't,
you'll get a strange error: \`STDERR: : No such file or directory at ...
perl5/Mojolicious/Plugin/CGI.pm\`.

If it works, feel free to upgrade to Hypnotoad.

    WikiDataDir=$HOME/oddmuse hypnotoad server.pl

Note: Hypnotoad uses a different default port. The above makes the
server available on `http://localhost:8080/wiki`. Hypnotoad will keep
forking new processes. To stop it, use the `-s` flag.

    hypnotoad -s server.pl

## License

Permission is granted to copy, distribute and/or modify this document
under the terms of the GNU Free Documentation License, Version 1.1 or
any later version published by the Free Software Foundation.

This program is free software; you can redistribute it and/or modify it
under the terms of the GNU General Public License as published by the
Free Software Foundation; either version 2 of the License, or (at your
option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General
Public License for more details.

Both the GNU Free Documentation License, and the GNU General Public
License are distributed together with this script. See the files
[FDL](https://github.com/kensanata/oddmuse/blob/master/FDL) and
[GPL](https://github.com/kensanata/oddmuse/blob/master/GPL),
respectively.
