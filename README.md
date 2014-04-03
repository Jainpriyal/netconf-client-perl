NETCONF Perl client
===================

       Contents
         * Abstract
         * Documents
         * Supported Platforms
         * Downloads
         * Installation
         * Running the Examples
         * Dependencies
         * Support

Abstract
========

   Each Juniper Networks routing platform running JUNOS Internet software
   release 7.5B1 or later supports the NETCONF API. The NETCONF API provides
   mechanisms to install, manipulate, and delete the configuration of network
   devices. The NETCONF API uses an Extensible markup Language (XML) based
   data encoding for the configuration data as well as operations and
   messages defined in the API.

   Because NETCONF uses XML based data encoding, you can leverage the myriad
   Perl modules in the public domain to ease the development of client
   applications that monitor and configure Juniper Networks routers. There
   are many modules in the Comprehensive Perl Archival Network (CPAN,
   http://www.cpan.org) and other Perl source repositories that provide ways
   to manipulate XML data (for example, the XML::LibXML modules).

   The Net::Netconf::Manager module provides an object-oriented interface for
   communicating with the NETCONF server so you can start using the NETCONF
   API quickly and easily. There are several modules in this library but
   client applications directly invoke the Manager object only. When the
   client application creates a Net::Netconf::Manager object, it specifies a
   router name and the login name to use when accessing the router (which
   determines the client application's access level).

   The following code segment shows how to use the Net::Netconf::Manager
   object to request information from a Juniper Networks routing platform.
   This example invokes the query called get_chassis_inventory.
    
     # Step 1: set up the query
     my $query = "get_chassis_inventory";
     my %queryargs = ( detail => 1 );
     # Step 2: Create a Netconf Manager object and connect to the Juniper
     Networks routing platform
     my %deviceinfo = (
      access => "ssh",
      login => "johndoe",
      password => "secret",
      hostname => "router11"
      );
      my $jnx = new Net::Netconf::Manager(%deviceinfo);
      unless ( ref $jnx ) {
             croak "ERROR: $deviceinfo{hostname}: failed to connect.\n";
      }
     # Step 3: send the query and receive a reply
     my $res = $jnx->$query( %queryargs );
     # Step 4: check for error
     if ($jnx->has_error) {
          croak "ERROR: in processing request \n $jnx->{request} \n";
      } 
      else {
     # Step 5: do something with the result
      }
     # Step 6: always disconnect from the server when you're done
     $jnx->disconnect();

Documents
==========

   The following documents are available at http://www.juniper.net/beta for
   the beta release and http://www.juniper.net/support for final release of
   each version of the JUNOS Internet software.

     * NETCONF API Guide
     * JUNOS XML API Configuration Reference
     * JUNOS XML API Operational Reference

Supported Platforms
===================

   The current version of this module has been tested on the following
   platforms. Later releases may support additional platforms.

     * Ubuntu 12.04LTS 
        

Downloads
============

       Client Perl applications can communicate with the NETCONF server via SSH only. The NETCONF Perl client needs a
       SSH client program (like OpenSSH) installed on the system.

       To download the publicly available version of the NETCONF Perl Client,perform the following steps:

         1. Access the Juniper Networks Web site at
            http://www.juniper.net/beta (for beta software) or
            http://www.juniper.net/support (for final release software).
         1. Click on the link labeled "NETCONF API Software" on the left.
         2. Click on the link labeled "NETCONF API Client" to download the
            Net::Netconf::Manager distribution in gzip format.
        

Installation
=============
        Instructions for UNIX Systems

         1. Make sure Perl is installed. If necessary, see Installation of
            Perl.
            % which perl
            % perl -v
            The NETCONF Perl Client requires version 5.6.1 or later of the
            perl executable. Verify that you are running that version of the
            perl executable. If not, check your PATH or install the latest
            release of perl.

         1. Download the NETCONF gzip archive from the Juniper Networks website. 
            For instructions, see Download.
         2. Unzip and untar the archive.
            On FreeBSD systems:
            % tar zxf netconf-perl-n.n.tar.gz
         3. Change to the NETCONF directory.
            % cd netconf-perl-n.n
         4. Install the prerequisites of Perl modules. 
            Following are the prerequites
            1. Expect Module
            2. File::Which
            3. XML::LibXML
        
            Steps to install Prerequisites in Ubuntu12.04LTS :
            1. apt-get install tcl tcl-dev tk tk-dev
            2. apt-get install expect expect-dev
            3. perl -MCPAN -e 'install Bundle::Expect'
            4. cpan File::Which
            5. cpan XML::LibXML
            
            After successfully installing Prerequisites install NETCONF PERL CLIENT
          
         5. Create Net::Netconf makefile.
            If installing Net::Netconf::Manager under the standard directory
            (it's normally /usr/local/lib):
            [/my/netconf-perl-n.n]% perl Makefile.PL
             
         6. Install the Net::Netconf module.
            [/my/netconf-perl-n.n]% make
            [/my/netconf-perl-n.n]% make install
                  
Running the Sample Scripts
==========================

            The NETCONF Perl distribution includes sample scripts that
            demonstrate how to use NETCONF to retrieve and change the
            configuration of a Juniper Networks routing platform. The samples
            reside in the netconf-perl-n.n/examples directory.

            Reading configuration: System Information
            This example sends a <get-system-information> request to the
            Juniper Networks routing platform and displays the result to the
            standard output.It also shows how to parse reply from server

            use Net::Netconf::Manager;
            print "Enter hostname\n";
            my $hostname = <>;
            print "Enter username\n";
            my $login= <>;
            print "Enter password\n";
            my $pass = <>;
            chomp($hostname);
            chomp($login);
            chomp($pass);
            $jnx = new Net::Netconf::Manager( 'access' => 'ssh',
                          'login' => $login,
                          'password' => $pass,
                          'hostname' => $hostname);
            if(! $jnx ) {
                  print STDERR "Unable to connect to Junos device \n";
                  exit 1;
             }
             print "Connection established: " . $jnx->get_session_id . "\n";
             my $reply=$jnx->get_system_information();
             if ($jnx->has_error) {
             print "ERROR: in processing request\n";
             # Get the error
             my $error = $jnx->get_first_error();
             $jnx->print_error_info(%$error);
             exit 1;
             }
             print "Rpc reply from server.\n";
             print ">>>>>>>>>>\n";
             print $reply;
             print "<<<<<<<<<<\n";
             #parsing reply from server
             my $config= $jnx->get_dom();
             $res= $config->getElementsByTagName("hardware-model")->item(0)->getFirstChild->getData;
             $res2= $config->getElementsByTagName("os-name")->item(0)->getFirstChild->getData;
             $res3= $config->getElementsByTagName("host-name")->item(0)->getFirstChild->getData;
             print "\nhardware information  ". $res ."\n";
             print "os-name  " .$res2 . "\n";
             print "host-name  ". $res3. "\n";
             $jnx->disconnect();
  
 Notes for private directory installation:
 
   Remember to set the PERL5LIB environment variable before installing the Perl modules and running the examples.
   PERL5LIB is an environment variable, it can be set to a colon-separated list of directories. If set, perl will first
   look for Perl modules in the PERL5LIB directories before looking in the standard directory. The instruction above
   uses the csh syntax for setting environment variable. For sh, ksh and bash, 
   use EXPORT PERL5LIB=/my/private/directory/lib.
 
 Support
===========
If you have problems with this Net::Netconf package, please e-mail support@juniper.net. We are looking forward to hearing from you.

Juniper Networks is registered in the U.S. Patent and Trademark Office and in other countries as a trademark of Juniper
Networks, Inc. Internet Processor, Internet Processor II, JUNOS, JUNOScript, M5, M10, M20, M40, M160, and its corporate, product, and service logos are trademarks of Juniper Networks, Inc.  All other trademarks, service marks, registered trademarks, or registered service marks are the property of their respective owners. Use of any Juniper Networks trademarks in a manner that is likely to cause confusion among its customers or disparages/discredits Juniper Networks is strictly  prohibited.

Copyright (c) 2005, Juniper Networks, Inc. All Rights Reserved.