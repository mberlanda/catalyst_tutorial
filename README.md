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

```

Resources:

- [CPNAM Catalyst Manual](https://metacpan.org/release/Catalyst-Manual)
- [Template Toolkit](http://template-toolkit.org/)
