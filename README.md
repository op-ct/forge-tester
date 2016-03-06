## Forge Tester
Like Test that the modules you have satisify the deps for any given module

## Running a Local Puppet Forge From a Gem

### Local Puppet Forge Server
Using https://github.com/unibet/puppet-forge-server

```bash
# from a new project directory
gem install puppet-forge-server
puppet-forge-server --help

# prepare local modules directory
mkdir modules
wget -P modules/ forge.puppetlabs.com/system/releases/p/puppetlabs/puppetlabs-stdlib-4.10.0.tar.gz


# Run puppet forge
puppet-forge-server -p 8080 -m $PWD/forge_tarballs


# test
puppet module search . --module_repository=http://localhost:8080


puppet module install simp-simplib --module_repository=http://localhost:8080 --modulepath=$PWD/_modules

```

