puppet-iis
==========

Puppet module for configuring IIS. It can configure application pools, sites, applications, virtual directories, settings and custom error pages.

The module is available on puppet forge: http://forge.puppetlabs.com/mirdhyn/iis
Report issues there: https://github.com/mirdhyn/puppet-iis/issues

Comments and/or remarks are welcome!

## Pre-requisites

- Windows
- IIS installed (use [opentable/windowsfeature](https://forge.puppetlabs.com/opentable/windowsfeature) for that matter)

The module works with IIS 7+.  It does not work with IIS 6 or earlier as those versions of IIS did not include the appcmd tool.

## Example Usage
```puppet
      iis_apppool { 'PuppetIisDemo':
        ensure                => present,
        managedpipelinemode   => 'Integrated',
        managedruntimeversion => 'v2.0',
      }


      iis_site { 'PuppetIisDemo':
        ensure       => present,
        bindings     => ["http/*:8000:"],
        physicalpath => 'c:\puppet_iis_demo'
      }


      iis_vdir { 'PuppetIisDemo/JSCache':
        ensure       => present,
        iis_app      => 'PuppetIisDemo/',
        physicalpath => 'c:\puppet_iis_demo\Javascript'
      }


      iis_app { 'Default Web Site/JSCache/MINIFIED':
        ensure          => present,
        applicationpool => 'PuppetIisDemo',
        physicalpath    => 'c:\puppet_iis_demo\Javascript\Javascript\MINIFIED'
      }


      iis_config { 'system.webServer/caching':
        enabled           => true,
        enablekernelcache => true
      }


      iis_config { 'system.webServer/asp':
        cache_maxdisktemplatecachefiles => 4000,
        cache_scriptfilecachesize       => 4500,
        cache_scriptenginecachemax      => 1000,
        limits_processorthreadmax       => 100
      }


      iis_config { 'cache expiration for /JSCache':
        config_section                 => 'system.webServer/staticContent',
        path                           => 'Default Web Site/JSCache',
        clientcache_cachecontrolmode   => 'UseMaxAge',
        clientcache_cachecontrolmaxage => '730.00:00:00'
      }


      iis_errorpages { 'PuppetIisDemo/':
        error_pages => [{ statusCode   => 404,
                          path         => '/err/404.asp',
                          responseMode => 'ExecuteURL' },
                        { statusCode    => 500,
                          subStatusCode => 100,
                          path          => '/err/500.100.asp',
                          responseMode  => 'ExecuteURL'}]
      }
```


## Testing
WIP (Manually tested on Windows Server 2008 R2 64bit)

## Notes
If using the rake build scipt, you need to use Ruby >= 1.9.2
