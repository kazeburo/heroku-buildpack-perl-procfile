Heroku/Dokku buildpack: Perl-Procfile
=====================================

This is a Heroku buildpack that makes it easy to run any Perl application
(possibly a complex one with different components) from a `Procfile`.

The difference in the approach versus e.g. [heroku-buildpack-perl][hbp] is
that it does not assume that your application is necessarily a web one, so
nothing gets installed in addition to what you setup. This can be e.g.
useful even if you have a web application, but you want to run it with
something different from Starman (e.g. use Hypnotoad with Mojolicious).

If your repository has a top-level sub-directory named either `epan` or
`dpan`, it's assumed to have a CPAN-like directory structure inside (with
`authors` and `modules` and all the rest) and it will be used for looking
for modules. This makes it easier to ship *DarkPAN* modules while still
being able to install the official ones from the official CPAN.

This buildpack is a fork of [heroku-buildpack-perl-heroku][hbpp] and will
hopefully be upstreamed.

Usage
-----

Example usage in Heroku (untested):

    $ ls
    cpanfile
    Procfile
    lib/

    $ cat cpanfile
    requires 'Plack', '1.0000';
    requires 'DBI', '1.6';

    $ cat Procfile
    web: plackup --port $PORT -a app.psgi    

    $ heroku create --stack cedar \
       --buildpack https://github.com/polettix/heroku-buildpack-perl-procfile.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack
    -----> Perl/Procfile app detected
    -----> Installing dependencies

The buildpack will detect that your perl-app has an `Procfile` in the root.

Example usage in [Dokku][], using a `.buildpacks` file inside the
repository:

    $ ls -a
    .buildpacks
    cpanfile
    .git
    Procfile
    lib/

    $ cat .buildpacks
    https://github.com/polettix/heroku-buildpack-perl-procfile.git

    $ cat cpanfile
    requires 'Plack', '1.0000';
    requires 'DBI', '1.6';

    $ cat Procfile
    web: plackup --port $PORT -a app.psgi    

    $ ssh dokku@your-node.example.com apps:create your-app

    $ git add remote dokku dokku@your-node.example.com:your-app

    $ git push dokku master
    ...
    -----> Fetching custom buildpack
    -----> Perl/Procfile app detected
    ...

Alternatively, if you don't want to use the `.buildpacks` file inside the
repo, you can use the suggestions provided in [custom buildpacks][]:

    $ ssh dokku@your-node.example.com config:set your-app \
        BUILDPACK_URL=https://github.com/polettix/heroku-buildpack-perl-procfile.git


Libraries
---------

Dependencies can be declared using `cpanfile` (recommended) or more
traditional `Makefile.PL`, `Build.PL` and `META.json` (whichever you can
install with `cpanm --installdeps`), and the buildpack will install these
dependencies using [cpanm][] into `./local` directory.

You can ship your own *DarkPAN* modules by creating a subdirectory named either
`epan` or `dpan` at the top level, and shaping it in a CPAN-compatible way.
Example:

    $ find epan -type f | sort
    epan/authors/01mailrc.txt.gz
    epan/authors/id/P/PO/POLETTIX/Some-DarkPAN-Module-0.01.tar.gz
    epan/modules/02packages.details.txt.gz
    epan/modules/03modlist.data.gz

To create it, you might want to look into:

- [epan][]
- [OrePAN2][]
- [CPAN::Faker][]

and probably a few more. When either (or both) directory is present, it is
set as a `--mirror` for [cpanm][] *before*
[http://www.cpan.org](http://www.cpan.org) so that you can e.g. ship
patched versions of official modules.


[cpanm]: http://cpanmin.us
[epan]: https://github.com/polettix/epan
[OrePAN2]: https://github.com/tokuhirom/OrePAN2
[CPAN::Faker]: https://metacpan.org/pod/CPAN::Faker
[Dokku]: http://dokku.viewdocs.io/dokku/
[custom buildpacks]: http://dokku.viewdocs.io/dokku/deployment/methods/buildpacks/#specifying-a-custom-buildpack
[hbp]: https://github.com/miyagawa/heroku-buildpack-perl
[hbpp]: https://github.com/kazeburo/heroku-buildpack-perl-procfile
