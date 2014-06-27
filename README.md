Heroku buildpack: Perl-Procfile
===============================

This is a Heroku buildpack that runs any Perl applications from Procfile

Usage
-----

Example usage:

    $ ls
    cpanfile
    Procfile
    lib/

    $ cat cpanfile
    requires 'Plack', '1.0000';
    requires 'DBI', '1.6';

    $ cat Procfile
    web: plackup --port $PORT -a app.psgi    

    $ heroku create --stack cedar --buildpack https://github.com/kazeburo/heroku-buildpack-perl-procfile.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack
    -----> Perl/Procfile app detected
    -----> Installing dependencies

The buildpack will detect that your perl-app has an `Procfile` in the root.

Libraries
---------

Dependencies can be declared using `cpanfile` (recommended) or more traditional `Makefile.PL`, `Build.PL` and `META.json` (whichever you can install with `cpanm --installdeps`), and the buildpack will install these dependencies using [cpanm](http://cpanmin.us) into `./local` directory.

