# NAME

Apache::IncludeHook - #perl Server Side Include support

# SYNOPSIS

    PerlModule Apache::IncludeHook

    Alias /ssi /usr/local/apache/htdocs
    <Location /ssi>
      AddType text/html .shtml
      AddOutputFilter INCLUDES .shtml
      Options +Includes
    </Location>

# DESCRIPTION

Apache::IncludeHook offers support for #perl tags in 
documents parsed by the mod\_include engine included in 
the Apache 2.0 distribution.  Supported formats include

    <!--#perl sub="My::PrintArgs" -->
    <!--#perl arg="fee" sub="My::PrintArgs" arg="fie" -->
    <!--#perl arg="foe" sub="My::PrintArgs::handler" -->
    <!--#perl arg="fum" sub="My::PrintArgs->method_handler" --> here
    <!--#perl arg="I smell" sub="sub { my $r = shift; print @_ }" --> 

In Apache 1.3, mod\_include supported #perl tags out of
the box.  In 2.0, support for tags outside the standard
mod\_include realm ('echo', 'flastmod', etc) have been removed,
having been replaced with an API that allows you to hook
your own functionality into mod\_include's parsing engine.
The 'exec' tag is an example of one that is no longer natively
supported by mod\_include - mod\_cgi now supplies the base
implementaiton of this tag.

The current hope with this module is simply to carry over #perl tag
support from Apache 1.3 to 2.0.  Apache::SSI-like support
for custom tags will (possibly) come later.  keep in mind
that while this module is not inteneded to replace the old
Apache::SSI for Apache 1.3, because the new Apache 2.0 API 
includes a filtering mechansim,
you already have the ability to post-process SSI tags via
Perl (or C) output filters.

# EXAMPLE

    file.shtml:

      perl <!--#perl arg="one" sub="My::PrintArgs" --> here

    PrintArgs.pm:

      package My::PrintArgs;

      use Apache2::RequestRec ();
      use Apache2::Const -compile => 'OK';

      use strict;

      sub handler {
                                                                                  
        my ($r, @args) = @_;
                                                                                  
        print join ' ', '***', (join ' : ', @args), '***';

        return Apache2::Const::OK;
      }

which is almost identical to what you would see with mod\_perl 1.0,
save the mod\_perl 2.0 specific classes.

# NOTES

This implementation is designed to hook into the mod\_include
that ships with Apache 2.0.  It will not work with Apache 2.1.

This is alpha ware, subject to massive API changes.  Meaning,
the TIEHANDLE interface may go away and you may be forced to
use only the (currently non-existent) filter interface.  so
get to know filters now before it's too late (they're really
cool anyway).

# FEATURES/BUGS

Subrequests are still a work in progress - you can issue a
subrequest but it won't enter the filter chain again.  that is,
don't expect subrequests to SSI documents to re-enter the
filter chain and be parsed.  this is an apache limitation,
not a limitation of Apache::IncludeHook or mod\_perl.

only print STDOUT and $r->print are supported.  other methods
of sending content to the client still need to be implemented.

# AUTHOR

Geoffrey Young <geoff@modperlcookbook.org>

# COPYRIGHT

Copyright (c) 2005, Geoffrey Young

All rights reserved.

This module is free software.  It may be used, redistributed
and/or modified under the same terms as Perl itself.
