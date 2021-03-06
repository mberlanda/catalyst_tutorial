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
```
# Create a SQLite database
$ touch myapp01.sql
$ sqlite3 myapp.db < myapp01.sql
$ sqlite3 myapp.db
SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
sqlite> select * from book;
1|CCSP SNRS Exam Certification Guide|5
2|TCP/IP Illustrated, Volume 1|5
3|Internetworking with TCP/IP Vol.1|4
4|Perl Cookbook|5
5|Designing with Web Standards|5
sqlite> .q
$ sqlite3 myapp.db "select * from book"
# Database access with DBIx::Class
$ cpanm DBD::SQLite
$ cpanm Catalyst::Model::DBIC::Schema
# In order to check the installed version
$ perl -MCatalyst::Model::DBIC::Schema\ 999
$ perl -MDBD::SQLite\ 999
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
$ cpanm DBIx::Class::Schema::Loader
$ cpanm MooseX::NonMoose~0.25
$ # Re-run the helper to upgrade for you
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static naming=current use_namespaces=1 \
    dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
```
```pl
# Enable the model in the controller
$c->model('DB::Book')
$c->model('DB')->resultset('Book')
$c->model('DB::Book')->search({}, {order_by => 'title DESC'});
```
```
$ export DBIC_TRACE=1
$ script/myapp_server.pl -r
```
```
# Create a Wrapper for the view
# lib/MyApp/View/HTML.pm
__PACKAGE__->config(
    # Change default TT extension
    TEMPLATE_EXTENSION => '.tt2',
    # Set the location for TT files
    INCLUDE_PATH => [
            MyApp->path_to( 'root', 'src' ),
        ],
    # Set to 1 for detailed timer stats in your HTML as comments
    TIMER              => 0,
    # This is your wrapper template located in the 'root/src'
    WRAPPER => 'wrapper.tt2',
);
$ touch root/src/wrapper.tt2
# Create A Basic Stylesheet
$ mkdir root/static/css
$ touch root/static/css/main.css
$ script/myapp_test.pl "/books/list" | lynx -stdin
```

### Basic CRUD
```
# Formless submission
http://localhost:3000/books/url_create/TCPIP_Illustrated_Vol-2/5/4
```
```pl
# Try the Chained Action
sub url_create :Chained('/') :PathPart('books/url_create') :Args(3) {
    my ($self, $c, $title, $rating, $author_id) = @_;
}
# http://localhost:3000/books/url_create/TCPIP_Illustrated_Vol-2/5/4
```
Exploring the power of DBIC
```
$ sqlite3 myapp.db
SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
sqlite> ALTER TABLE book ADD created TIMESTAMP;
sqlite> ALTER TABLE book ADD updated TIMESTAMP;
sqlite> UPDATE book SET created = DATETIME('NOW'), updated = DATETIME('NOW');
sqlite> SELECT * FROM book;
1|CCSP SNRS Exam Certification Guide|5|2017-07-17 17:02:26|2017-07-17 17:02:26
2|TCP/IP Illustrated, Volume 1|5|2017-07-17 17:02:26|2017-07-17 17:02:26
3|Internetworking with TCP/IP Vol.1|4|2017-07-17 17:02:26|2017-07-17 17:02:26
4|Perl Cookbook|5|2017-07-17 17:02:26|2017-07-17 17:02:26
5|Designing with Web Standards|5|2017-07-17 17:02:26|2017-07-17 17:02:26
6|TCPIP_Illustrated_Vol-2|5|2017-07-17 17:02:26|2017-07-17 17:02:26
9|TCP/IP Illustrated, Vol 3|5|2017-07-17 17:02:26|2017-07-17 17:02:26
sqlite> .quit
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static components=TimeStamp dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
 exists "~/personal/pl/MyApp/script/../lib/MyApp/Model"
 exists "~/personal/pl/MyApp/script/../t"
DBIx::Class::Schema::Loader::make_schema_at(): DBIx::Class::TimeStamp, as specified in the loader option "components", is not installed at /usr/local/share/perl/5.22.1/Catalyst/Helper/Model/DBIC/Schema.pm line 637
$ cpanm DBIx::Class::TimeStamp
```
http://localhost:3000/books/list_recent/15
```
# http://localhost:3000/books/list_recent_tcp/100
$ DBIC_TRACE=1 script/myapp_server.pl -r
[info] MyApp powered by Catalyst 5.90115
HTTP::Server::PSGI: Accepting connections at http://0:3000/
PRAGMA foreign_keys = ON:
SELECT me.id, me.title, me.rating, me.created, me.updated FROM book me WHERE ( ( created > ? AND title LIKE ? ) ): '2017-07-17 18:19:50', '%TCP%'
SELECT author.id, author.first_name, author.last_name FROM book_author me  JOIN author author ON author.id = me.author_id WHERE ( me.book_id = ? ): '10'
```

### Authentication

```
# Add Users and Roles to the Database
$ sqlite3 myapp.db < myapp02.sql
# Add User and Role Information to DBIC Schema
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static components=TimeStamp dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"  overwrite_modifications=true

$ cpanm Catalyst::Plugin::Session
$ cpanm Catalyst::Plugin::Session::State::Cookie
$ cpanm Catalyst::Plugin::Session::Store::File
$ cpanm Catalyst::Plugin::Authentication
$ cpanm Catalyst::Authentication::Realm::SimpleDB

# Update .conf file
$ CATALYST_DEBUG=0 perl -Ilib -e 'use MyApp; use Config::General;
    Config::General->new->save_file("myapp.conf", MyApp->config);'
```

```
# Add Login and Logout Controllers
$ script/myapp_create.pl controller Login
$ script/myapp_create.pl controller Logout
```
```
# Using Password Hashes
$ script/myapp_create.pl model DB DBIC::Schema MyApp::Schema \
    create=static components=TimeStamp,PassphraseColumn dbi:SQLite:myapp.db \
    on_connect_do="PRAGMA foreign_keys = ON"
$ cpanm DBIx::Class::PassphraseColumn
$ cpanm Catalyst::Plugin::Authentication::Store::DBIx::Class --force
$ DBIC_TRACE=1 perl -Ilib set_hashed_passwords.pl
$ sqlite3 myapp.db "select * from users"
```
I could not manage to make this part to work. I prefer to let it in standby and to continue with the Tutorial to come back later.
