### Name

CBSSports::Getopt - Encapsulate Option Parsing and Usage for all CBSSports Perl Scripts

### Version

1\.1

### Synopsis

The basic usage of CBSSports::Getopt:

```perl
#!/usr/bin/perl
use strict;
use warnings;
use CBSSports::Getopt qw(GetOptions Usage);

my $opts = GetOptions( 'l|league-name' );
Usage() unless $opts->{league_name};

__END__

=head1 Name

sample-script - A sample script using CBSSports::Getopt

=head1 Usage

sample-script [options]

=head1 Options

  -h --help     Print this usage statement and exit.
  -H --man      Print the complete documentation and exit.
  -v --verbose  Turn on verbose output
     --version  Print script version information and quit
```

### Examples

#### Calling the Script's Usage

```
sample-script -h
```

You can also pass additonal options to Getopt::Long::Configure via Configure

```perl
use CBSSports::Getopt qw(GetOptions Usage Configure);

Configure( 'bundling' );
my $opts = GetOptions( 'l|league-name' );
Usage() unless $opts->{league_name};
```

### Description

The purpose of this module is to provide a simple way that script authors 
can easily define options and usage without have to duplicate code in 
each of their scripts.

This module provides the following functionality:

#### Getopt::Long for Option Parsing

Simply pass an array of Getopt::Long options to GetOptions and receive a hash 
populated with the options you defined.  See L<Getopt::Long> for details on 
option syntax.  (note that 'no_auto_abbr' and 'no_ignore_case' are enabled 
insead of Getopt::Long's defaults)

##### The following options are automatically defined for you.  

```
  -h --help     Show script usage and options
  -H --man      Show full manpage (all pod in script)
  -v --verbose  Incremental verbose ( -v -v -v, verbose = 3 );
     --version  Display version and exit.
```
You can override these options if nessesary via Configure( 'allow_preset_override' ), but are advised against.  We'd like to keep a common interface to all our scripts.

##### When specifying a short option name, you should define a long one as well.

```
GetOptions( 'l' );  VS   GetOptions( 'l|league-name' );
```

The long option name gets turned into the hash key.  The more verbose you are in choosing an option names, the easier it is to tell what the option is.  You don't get penalized for naming these options so other people can understand them.  (other people also includes yourself a month from now) :)

##### The longest option names get translated into the hash key ( with '\_' subsituted for '-' )

For example, if you pass in

```perl
my $opts = GetOptions( 'league-id|league-name|l' );
```

The result will be stored in

```perl
$opts->{league_name}
```

If two keys of the same length are passed in, the first one found will be used 
for the hash key.

##### Getopt::Long auto abbreviate is turned off by default

By default Getopt::Long auto abbreviates all long options.  Although this functionality can be clever,  it is not always clear.  Let's err on the side of caution and avoid cleverness.

##### Getopt::Long ignore case turned off by default.

Similarly, ignoring case on options may result in confusion.  In order to keep things clear, the script should always require the proper case on command line options.

##### By using CBSSports::Getopt, you will automatically get the ability to use a .rc file.

For example, say you have a script named 'doit'.  By using CBSSports::Getopt, you will automatically be able to store commonly used options in a '.doitrc' file in your home directory.  When the script runs, it will read in that optional file and will append any options to @ARGV before the command line arguments are run.  Comments and leading/trailing whitespace are removed before processing.  You can have multiples options per line.a

```
# contents of .doitrc
-u web   # run as user web
```

#### Pod::Usage for Displaying Script Usage

Usage is just simply pod within your script.  At a minimum, you should write pod within your script that contains the USAGE and OPTIONS sections.  However, you can write a whole man page if you like :)


### Interface

#### GetOptions( $option1, $option2 );

Pass in an array of options for Getopt::Long::GetOptions to parse.  The function call will return a hash reference of the options you chose to capture.  Only specifying single character options is not allowed.  Each single character option must have a long couterpart.  However, a long option can be specified without a single character counterpart.

```
'h'       # incorrect - will fail
'h|help'  # fine - will define -h and --help
'help'    # fine - will define --help
```

For example:

```perl
use CBSSports::Getopt;
my $opts = GetOptions(
    's|source-point=s', 'r|range-point=s', 'p|single-point=s', 'b|copy-to-begining',
    'e|copy-to-end',    'q|quiet',         'n|do-nothing',
);
````

If the script is executed without command line parameters, the hash reference returned from the GetOptions call (ex. $opts) will contain: 

```perl
{
  'verbose'          => undef,
  'quiet'            => undef,
  'copy_to_end'      => undef,
  'version'          => undef,
  'range_point'      => undef,
  'source_point'     => undef,
  'single_point'     => undef,
  'do_nothing'       => undef,
  'copy_to_begining' => undef
}
```

(Note: verbose and version are automatically defined for you)

#### Usage( message => $error\_message, verbose => $verbosity_level )

Call usage with an error message string which will be displayed before
the output and verbosity level.  If no verbosity level is specified, 
Usage will only show USAGE and OPTIONS pod secitons.

#### Configure( $config1, $config2 );

Configure checks for 'allow_preset_override' before passing the rest of the arguments to Getopt::Long::Configure.  See L<Getopt::Long> for details.  'allow_preset_override' allows you define '-h', '-H', and '-v' for another purpose other than their defaults.

### Configuration and Environment

By default, every command line script that uses CBSSports::Getopt will be able to pull commonly used options from an .rc file of the same name.  For example, 'auto-load-rosters' would use '.auto-load-rostersrc' from your home directory.  

Leading/trailing whitespace and comments in the '.rc' files are removed before processing.

### Dependencies

CBSSports::Getopt has the following dependancies:

* Getopt::Long
* Pod::Usage
* File::HomeDir

### Bugs and Limitations

Some scripts may use '-h', '-H' or '-v' for something other than 'help', 'verbose', 'man'.  You can override these defaults via Configure( 'allow_preset_override' );

### Author

Jeff Bisbee jbisbee@cpan.org

### Licence and Copyright

Copyright (c) 2009, Jeff Bisbee C<< <jbisbee@cbs.com> >>. All rights reserved.

This module is free software; you can redistribute it and/or
modify it under the same terms as Perl itself. See L<perlartistic>.

=head1 DISCLAIMER OF WARRANTY

BECAUSE THIS SOFTWARE IS LICENSED FREE OF CHARGE, THERE IS NO WARRANTY
FOR THE SOFTWARE, TO THE EXTENT PERMITTED BY APPLICABLE LAW. EXCEPT WHEN
OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS AND/OR OTHER PARTIES
PROVIDE THE SOFTWARE "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER
EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE
ENTIRE RISK AS TO THE QUALITY AND PERFORMANCE OF THE SOFTWARE IS WITH
YOU. SHOULD THE SOFTWARE PROVE DEFECTIVE, YOU ASSUME THE COST OF ALL
NECESSARY SERVICING, REPAIR, OR CORRECTION.

IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING
WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MAY MODIFY AND/OR
REDISTRIBUTE THE SOFTWARE AS PERMITTED BY THE ABOVE LICENCE, BE
LIABLE TO YOU FOR DAMAGES, INCLUDING ANY GENERAL, SPECIAL, INCIDENTAL,
OR CONSEQUENTIAL DAMAGES ARISING OUT OF THE USE OR INABILITY TO USE
THE SOFTWARE (INCLUDING BUT NOT LIMITED TO LOSS OF DATA OR DATA BEING
RENDERED INACCURATE OR LOSSES SUSTAINED BY YOU OR THIRD PARTIES OR A
FAILURE OF THE SOFTWARE TO OPERATE WITH ANY OTHER SOFTWARE), EVEN IF
SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF
SUCH DAMAGES.
