# Jenkins

## Plugins
  - Three main categories: Macro (new build steps), Interface (modify the interface) and Infrastructure
  - Developed in Java, built with Maven, and required to be open source
  - Versioning of plugins are messy, as they're third party --> can unexpectedly break
  
## Plugin Management interface:
  - Show Installed and Available plugins
    - Master list of plugins from Jenkins update center
  - Missing plugins in local Jenkins:
    1. Is it in the master list ?
	2. Is the core requirement satisfied ? (e.g. against your Jenkins version)
  - Configuration of the plugins: Normally stored in home directory with plugin name prefix
  - Disable/Enable plugin: need restart of Jenkins
    + Jenkins disable plugin by create a file with same name, with suffix 'disabled' (e.g. XXX.jpi.disabled)
	
## Performance:
  - Link: https://blog.devops.dev/monitoring-jenkins-job-with-prometheus-and-grafana-8515cfa26d48
  - Can use prometheus to monitor the performance of Jenkins
  
## Security and Compatibility
  - Security advisory: best keep visible
  - Update plugin as soon as possible: can rely on days of release to find stable update, since most problem will be resolved by new release after several days
  - If there no security fix available: 
    1. Have to understand the vulnerabilities: can you live with the problem ?
	2. It maybe because the fix is on a release that depend on later jenkins version (i.e. the problem is for your particular version of Jenkins you're running): check the plugin's main page
	
## Misc
  - Sample repo: https://github.com/FeynmanFan/usingmanagingjenkinsplugins