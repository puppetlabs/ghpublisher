# Puppet Forge Github Publisher

### How it works

Basically, you'll configure a service-hook against the CI service of your choice, configure that CI service to clone and execute our publisher software which will parse the version out of your module metadata, check the Forge for that version and publish your module if it's a new release.

###Limitations

Build determinations are made by parsing your module metadata (metadata.json, or in case it’s missing, the now deprecated Modulefile), not via Git tags or other mechanisms.

### Rough setup for Travis.CI

1. Log in to Travis-CI (https://travis-ci.org/) using your GitHub account.
2. Select the repository to be published and flip the switch. Try the profile page. 
3. Install the travis gem on your local machine (where you will interact with git). `gem install travis`
4. CD into the root of your module development directory. ex. ~/modules/username-foomodule
5. Add the following .travis.yml file (or modify an existing one). Save this, but you'll come back to it after the next step.
```yaml
---
language: ruby
script:
- git clone -q git://github.com/puppetlabs/ghpublisher.git .forge-releng
- .forge-releng/publish
env:
 global:
 - PUBLISHER_LOGIN=<forge user’s login>
```
6. Issue the following command: `$ travis encrypt PUBLISHER_PASSWORD=<forge user’s password>`

and add the generated secure key to the global section beneath PUBLISHER_LOGIN like so (or run your encrypt command with '--add' to automatically do it):
```yaml
env:
 global:
 - PUBLISHER_LOGIN=<forge user’s login>
  - secure: <encrypted key>
```
7. Save your changes, commit your travis.yml file and push the repository. This triggers the first build. Depending on how busy Travis is, this may take a few minutes.
By default, Travis will trigger a build on any branch in your repo. We suggest you read this Doc on restricting to particular branches with Travis.


### Travis.ci documentation

- [Restricting builds to certain branches](http://docs.travis-ci.com/user/build-configuration/#Specify-branches-to-build)
- [Configuring builds](http://docs.travis-ci.com/user/build-configuration/)
- [Validate Travis.yml file](http://docs.travis-ci.com/user/travis-lint/)
- [Secure Environment Variables](http://docs.travis-ci.com/user/build-configuration/#Secure-environment-variables)
- [Skipping a build on a per-commit basis](http://docs.travis-ci.com/user/how-to-skip-a-build/#How-to-skip-a-build)
