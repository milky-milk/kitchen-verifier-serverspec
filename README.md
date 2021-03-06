[![Gem Version](https://badge.fury.io/rb/kitchen-verifier-serverspec.svg)](http://badge.fury.io/rb/kitchen-verifier-serverspec)
[![Gem Downloads](http://ruby-gem-downloads-badge.herokuapp.com/kitchen-verifier-serverspec?type=total&color=brightgreen)](https://rubygems.org/gems/kitchen-verifier-serverspec)
[![Build Status](https://travis-ci.org/neillturner/kitchen-verifier-serverspec.png)](https://travis-ci.org/neillturner/kitchen-verifier-serverspec)

# Kitchen::Verifier::Serverspec

A Test Kitchen Serverspec Verifer without having to transit the Busser layer.

This supports running serverspec both remotely on the server and locally on your workstation.
Runners are supported to provide logic to run serverspec initially supporting ansiblespec.


## Installation

On your workstation add this line to your Gemfile:

    gem 'kitchen-verifier-serverspec'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install kitchen-verifier-serverspec

When it runs it install serverspec on the remote server or the workstation if remote_exec set to false.
This can be configured by passing a Gemfile like this:

```
source 'https://rubygems.org'

gem 'net-ssh','~> 2.9'
gem 'serverspec'
```

this allows extra dependencies to be specified and the version of serverspec specified.

## Usage


An example of the verifier serverspec options in your `.kitchen.yml` file:

```yaml
verifier:
  name: serverspec

suites:
  - name: base
    verifier:
      patterns:
      - modules/mycompany_base/spec/acceptance/base_spec.rb
```

See example [https://github.com/neillturner/puppet_repo](https://github.com/neillturner/puppet_repo)

or with environment variables

```yaml
verifier:
  name: serverspec

suites:
  - name: base
    verifier:
      patterns:
      - roles/tomcat/spec/tomcat_spec.rb
      bundler_path: '/usr/local/bin'
      rspec_path: '/home/vagrant/bin'
      env_vars:
        TARGET_HOST: 172.28.128.7
        LOGIN_USER: vagrant
        SSH_KEY: 'spec/tomcat_private_key.pem'
```

or run on your workstation

```yaml
verifier:
  name: serverspec
  remote_exec: false

suites:
  - name: base
    provisioner:
      custom_facts:
        role_name1: base
    verifier:
      patterns:
      - modules_mycompany/mycompany_base/spec/acceptance/base_local_spec.rb
      env_vars:
        TARGET_HOST: 127.0.0.1
        TARGET_PORT: 2222
        LOGIN_USER: vagrant
        SSH_KEY: 'c:/repository/puppet_repo/private_key.pem'
```

# Custom Runners

Custon runners can be defined and run to provide further customization.
There is a runner that automatically runs the ansiblespec files for all the hosts from the
ansible provisioner.

This can be run by specifying in the kitchen yml file:

```yaml
verifier:
  name: serverspec

suites:
  - name: base
    verifier:
      runner_url: https://raw.githubusercontent.com/neillturner/serverspec-runners/master/ansiblespec_runner.rb
      require_runner: true
      bundler_path: '/usr/local/bin'
      rspec_path: '/home/vagrant/bin'
      env_vars:
        TARGET_HOST: 172.28.128.7
        LOGIN_USER: vagrant
        SSH_KEY: 'spec/tomcat_private_key.pem'
```


# Serverspec Verifier Options

key | default value | Notes
----|---------------|--------
sleep | 0 |
remote_exec | true | specify false to run serverspec on workstation
custom_serverspec_command | nil | custom command to run serverspec. Can be multiline. See examples below.
additional_serverspec_command | nil | additional command to run serverspec. Can be multiline. See examples below.
format | 'documentation' | format of serverspec output
color | true | enable color in the output
default_path | '/tmp/kitchen' | Set the default path where serverspec looks for patterns
patterns | [] | array of patterns for spec test files
gemfile | nil | custom gemfile to use to install serverspec
custom_install_commmand | nil | Custom shell command to be used at install stage. Can be multiline. See examples below.
additional_install_commmand | nil | Additional shell command to be used at install stage. Can be multiline. See examples below.
test_serverspec_installed | true | only run install_command if serverspec not installed
extra_flags | nil | extra flags to add to ther serverspec command
remove_default_path | false | remove the default_path after successful serverspec run
http_proxy | nil | use http proxy when installing ruby, serverspec and running serverspec
https_proxy | nil | use https proxy when installing puppet, ruby, serverspec and running serverspec
sudo | nil | use sudo to run commands
env_vars | {} | environment variable to set for rspec
bundler_path | nil | path for bundler command
rspec_path | nil | path for rspec command
runner_url | https://raw.githubusercontent.com /neillturner/serverspec-runners/ master/ansiblespec_runner.rb | url for custom runner
require_runner | false | run the custom runner instead of rspec directly

#### custom_install_command example usage

* One liner
```yaml
    custom_install_command: yum install -y git
```
* Multiple lines, a.k.a embed shell script
```yaml
  custom_install_command: |
     command1
     command2
```
* Multiple lines join without new line
```yaml
  custom_install_command: >
     command1 &&
     command2
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Run style checks and RSpec tests (`bundle exec rake`)
4. Commit your changes (`git commit -am 'Add some feature'`)
5. Push to the branch (`git push origin my-new-feature`)
6. Create new Pull Request
