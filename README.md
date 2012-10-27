Git Hooks
=========

This is a pair of git hooks that I use on my gitolite server that facilitate a few interesting things:

### Repository-specific pre- and post-receive hooks`

You can embed little scripts inside the config, specific to each repo. Each script will be invoked once for
every branch with an optional regex filter. For example:

    repo testing
      config hooks.pre         = "some_pre_command.sh"
      config hooks.pre.filter  = "master$"
      config hooks.post        = "some_post_command.sh"
      config hooks.post.filter = "master$"

### Simple mirroring

Gitolite has a sophisticated mirroring system built in for mirroring between muliple gitolite instances. This
doesn't really help when you want to push to Github or S3. To set up a mirror, create a config key for that
repo named `mirror.<name>` with the value set to the remote URL.

    repo testing
      config mirrors.github = "git@github.com:example/testing.git"
      config mirrors.s3     = "amazon-s3://.jgit@bugsplat-git-repos/testing.git"
      config mirrors.other  = "user@example.com:example/testing.git"
      
To push to S3 you'll need to create a bucket on S3 and create a file in the gitolite
user's home directory that contains your access key and secret key in this format:

    accesskey: YOUR-AWS-ACCESS-KEY
    secretkey: YOUR-AWS-SECRET-KEY
    
Your mirror URL will then be in the format `amazon-s3://<filename>@<s3-bucket-name>/<repo_name>.git`.
In the example above, the key file is at `$GITOLITE_HOME/.jgit`.

### Maintain a local clone

Sometimes it's really useful to have a local clone of a particular repository. These hooks let you do that
by setting a config entry named `hooks.clone.bath` to the path where the clone should exist. Note that any
changes made to this clone will be erased the next time you push.

    repo testing
      config hooks.clone.path = "/var/repos/testing"


## Install

Copy `pre-receive` and `post-receive` to `$GITOLITE_HOME/.gitolite/hooks/common` and make sure they're executable.
Copy `jgit` to `$GITOLITE_HOME/bin/jgit`.
