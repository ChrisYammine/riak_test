# Riak Test

## Quick Start

Assumes `riak_test` and `riak` and/or `riak-ee` are all checked out in the same parent folder

1. Edit your `cp riak_test.config.sample` to `~/.riak_test.config` 
1. Update your new `~/.riak_test.config` file's rt_deps url
1. `cd ../riak[-ee]`
1. `make devrel`
1. `. ../riak_test/rtdev-setup.sh`
1. `cd back to riak_test`
1. `riak_test rtdev verify_build_cluster`

`~/.riak_test.config` stores multiple configurations, and you can pass them by name as the first argument to `riak_test`. First it looks for a file with that name in your local directory, if it doesn't find it, it looks for something with that name in `~/.riak_test.config`


## Bootstraping Your Test Environment

Welcome to the exciting world of riak_test. Running tests against a development version of Riak is just one of the things that you can do with riak_test. You can also test things involving upgrading from previous versions of Riak. Together, we'll get your test environment up and running. Scripts to help in this process are located in the `bin` directory of this project. 

### rtdev-build-releases.sh
The first one that we want to look at is `rtdev-build-releases.sh`. If left unchanged, this script is going to do the following:

1. Download the source for the past three major Riak versions (e.g. 1.0.3, 1.1.4, and 1.2.0)
1. Build the proper version of Erlang that release was built with, using kerl (which it will also download)
1. Build those releases of Riak.

You'll want to run this script from an empty directory. Also, you might be thinking that you already have all the required versions of erlang. Great! You can crack open the script and set the paths to your installation

```bash
R14B04=${R14B04:-$HOME/erlang-R14B04}
```

The script will check that all these paths exist. If even one of them is missing, it will prompt you to install kerl, even if you already have kerl. If you say no, the script quits. If you say yes, or all of your erlang paths check out, then go get a cup of coffee, you'll be building for a little while.

*Warning*: If you are running OS X 10.7+, then the erlang_js dependency won't compile for you, but fails silently. Fortunately, the precomipled OS X build includes this dependency in it's working form. Here's how you get it:

```bash
wget http://s3.amazonaws.com/downloads.basho.com/riak/1.0/1.0.3/riak-1.0.3-osx-x86_64.tar.gz
mkdir erlang_js-1.0.0 && tar xvzf riak-1.0.3-osx-x86_64.tar.gz -C erlang_js-1.0.0 riak-1.0.3/lib/erlang_js-1.0.0 
rm -rf riak-1.0.3/dev/dev1/lib/erlang_js-1.0.0
rm -rf riak-1.0.3/dev/dev2/lib/erlang_js-1.0.0
rm -rf riak-1.0.3/dev/dev3/lib/erlang_js-1.0.0
rm -rf riak-1.0.3/dev/dev4/lib/erlang_js-1.0.0
cp -r erlang_js-1.0.0/riak-1.0.3/lib/erlang_js-1.0.0 riak-1.0.3/dev/dev1/lib/.
cp -r erlang_js-1.0.0/riak-1.0.3/lib/erlang_js-1.0.0 riak-1.0.3/dev/dev2/lib/.
cp -r erlang_js-1.0.0/riak-1.0.3/lib/erlang_js-1.0.0 riak-1.0.3/dev/dev3/lib/.
cp -r erlang_js-1.0.0/riak-1.0.3/lib/erlang_js-1.0.0 riak-1.0.3/dev/dev4/lib/.
```

*Please run this patch before proceeding on to the next script*

### rtdev-setup-releases.sh
