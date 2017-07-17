# Catalyst-Manual-5.9009

Karen Etheridge's Catalyst-Manual


### Introduction
```
$ cpanm Catalyst::Devel
```

### Catalyst Basics
```
# Project Initialization
$ catalyst.pl Hello
# Run the server
$ script/hello_server.pl -r
# Create a view
$ script/hello_create.pl view HTML TT
 exists "~/personal/pl/Hello/script/../lib/Hello/View"
 exists "~/personal/pl/Hello/script/../t"
Couldn't load helper "Catalyst::Helper::View::TT", "Can't locate Catalyst/Helper/View/TT.pm in @INC (you may need to install the Catalyst::Helper::View::TT module) (@INC contains: ~/personal/pl/Hello/script/../lib /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/local/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at (eval 299) line 2.
$ cpanm Catalyst::Helper::View::TT
$ script/hello_create.pl view HTML TT
 exists "~/personal/pl/Hello/script/../lib/Hello/View"
 exists "~/personal/pl/Hello/script/../t"
created "~/personal/pl/Hello/script/../lib/Hello/View/HTML.pm"
created "~/personal/pl/Hello/script/../t/view_HTML.t"
$ touch root/hello.tt
# Create a controller
$ script/hello_create.pl controller Site
$ mkdir root/site
$ touch root/site/test.tt
```

Resources:

- [CPNAM Catalyst Manual](https://metacpan.org/release/Catalyst-Manual)
- [Template Toolkit](http://template-toolkit.org/)

### More Catalyst Basics

```
$ catalyst.pl MyApp
$ perl Makefile.PL
$ perl -Ilib -e 'use MyApp; use Config::General;
    Config::General->new->save_file("myapp.conf", MyApp->config);'
$ cpanm Catalyst::Plugin::StackTrace
```
```pl
# Edit the list of Catalyst plugins
# Load plugins
#lib/MyApp.pm
use Catalyst qw/
    -Debug
    ConfigLoader
    Static::Simple

    StackTrace
/;
__PACKAGE__->setup(qw/-Debug ConfigLoader Static::Simple/);
# Makefile.PL
requires 'Catalyst::Plugin::StackTrace';
```
```
# Create a Catalyst controller
$ script/myapp_create.pl controller Books
# Create a Catalyst View
$ script/myapp_create.pl view HTML TT
$ mkdir -p root/src/books
$ touch root/src/books/list.tt2
```
```pl
# lib/MyApp/View/HTML.pm
__PACKAGE__->config(
    # Change default TT extension
    TEMPLATE_EXTENSION => '.tt2',
    render_die => 1,
);
# lib/MyApp.pm
__PACKAGE__->config(
    name => 'MyApp',
    # Disable deprecated behavior needed by old applications
    disable_component_resolution_regex_fallback => 1,
);
__PACKAGE__->config(
    # Configure the view
    'View::HTML' => {
        #Set the location for TT files
        INCLUDE_PATH => [
            __PACKAGE__->path_to( 'root', 'src' ),
        ],
    },
);
```
