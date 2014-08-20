Perloku
=======

Deploy Perl applications in seconds.

## Step 1

Write an app:

```perl
#!/usr/bin/env perl
use Mojolicious::Lite;

get '/' => sub {
  my $self = shift;
  $self->render('index');
};

app->start;
__DATA__

@@ index.html.ep
% layout 'default';
% title 'Welcome';
Welcome to the Mojolicious real-time web framework!

@@ layouts/default.html.ep
<!DOCTYPE html>
<html>
  <head><title><%= title %></title></head>
  <body><%= content %></body>
</html>
```

## Step 2

Create a
[cpanfile](http://search.cpan.org/~miyagawa/Module-CPANfile-1.0002/lib/cpanfile.pod)
that lists dependencies:

```
requires 'Mojolicious';
```
Alternately, you may create a Makefile.PL with your dependencies:

```perl
use strict;
use warnings;

use ExtUtils::MakeMaker;

WriteMakefile(
  NAME         => 'app.pl',
  VERSION      => '1.0',
  AUTHOR       => 'Magnus Holm <judofyr@gmail.com>',
  EXE_FILES    => ['app.pl'],
  PREREQ_PM    => {'Mojolicious' => '2.0'},
  test         => {TESTS => 't/*.t'}
);
```

## Step 3

Create an executable file called Perloku which runs a server on the port
given as an enviroment variable:

```sh
#!/bin/sh
perl app.pl daemon --listen http://*:$PORT
```


Test that you can start the server:

```sh
chmod +x Perloku
PORT=3000 ./Perloku
```

## Step 4

Deploy:

```sh
git init
git add .
git update-index --chmod=+x Perloku (only if using Windows)
git commit -m "Initial version"
heroku create -s cedar-14 --buildpack http://github.com/viljoviitanen/perloku.git
git push heroku master
```


Watch:

```
Initializing repository, done.
Counting objects: 5, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 631 bytes, done.
Total 5 (delta 0), reused 0 (delta 0)

-----> Fetching custom buildpack... done
-----> Perloku app detected
-----> Using Perl v5.18.2
-----> Installing dependencies
       --> Working on /tmp/build_d1983dee-8896-4376-a701-45e97bf1051d
       Configuring /tmp/build_d1983dee-8896-4376-a701-45e97bf1051d ... OK
       ==> Found dependencies: Mojolicious
       --> Working on Mojolicious
       Fetching http://www.cpan.org/authors/id/S/SR/SRI/Mojolicious-5.31.tar.gz ... OK
       Configuring Mojolicious-5.31 ... OK
       Building Mojolicious-5.31 ... OK
       Successfully installed Mojolicious-5.31
       <== Installed dependencies for /tmp/build_d1983dee-8896-4376-a701-45e97bf1051d. Finishing.
       1 distribution installed
       Dependencies installed
-----> Discovering process types
       Procfile declares types   -> (none)
       Default types for Perloku -> web

-----> Compressing... done, 621K
-----> Launching... done, v5
       http://perloku-example.herokuapp.com deployed to Heroku

To git@heroku.com:perloku-example.git
 * [new branch]      master -> master
```

[Enjoy!](http://perloku-example.herokuapp.com)

---

Changes from original judofyr/perloku: update to new Ubuntu 14.04 based
cedar-14 stack and use system perl (v5.18.2), not custom built perl.

This can also be used on original Ubuntu 10.04 based cedar stack, but it has an
"ancient" perl (v5.10.1) which probably causes problems with recent perl apps.
