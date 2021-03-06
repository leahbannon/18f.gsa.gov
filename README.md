## 18F's Homepage

This repository contains 18F's website, https://18f.gsa.gov.

* The `staging` branch is **automatically deployed** to our [staging site](http://staging.18f.us).
* The `production` branch is **automatically deployed** to our [production site](https://18f.gsa.gov).

**All development and pull requests should be done against the `staging` branch.**

Deployments to production will be done using PRs from `staging` to `production`.

### Getting started

This is a [Jekyll](http://jekyllrb.com) website. Install Jekyll through Rubygems (you may need `sudo`):

```bash
gem install jekyll
```

Sadly, [for the time being](https://github.com/jekyll/jekyll/issues/2327#issuecomment-55337023) you will also need Node to be installed, because Jekyll 2 couples a CoffeeScript runtime. This will eventually be removed. Install Node through `brew install node` or `apt-get install nodejs`.

Launch with Jekyll:

```bash
jekyll serve
```

The site will be visible at `http://localhost:4000`.

### Automatic deployment

You don't need to worry about this for normal development. But on the staging and production server, this project uses [Node](http://nodejs.org) and [`hookshot`](https://github.com/coreh/hookshot) to receive GitHub post-receive webhooks and update the project.

This project includes [fabric tasks](http://www.fabfile.org/) for easy remote stop/start/restart of the hook processes on the 18F website.

You will need:

* authorized access to the 18F site server
* an `18f-site` entry in your `$HOME/.ssh/config` with the necessary credentials
* to `pip install fabric`, and have your active `python` when running it be 2.X

With that, you can start, stop, and restart the staging and production hooks like so:

```
fab stop
fab start
fab restart
```

Provide `--set env=production` to any of those commands to apply it to the production hook.

#### Setting it up yourself

Install the Node dependencies with:

```bash
npm install hookshot
npm install minimist
npm install -g forever
```

18F's web server uses the `hookshot` command to listen for hooks on either of two ports.

From `/deploy`, run the hook with the appropriate port and command. It can be helpful to have `forever` and your command both log to the same file.

In development, you might use:

```bash
forever start -l $HOME/hookshot.log -a deploy/hookshot.js -p 3000 -b your-branch -c "cd $HOME/18f/18f.gsa.gov && git pull && jekyll build >> $HOME/hookshot.log"
```

You can stop and restart your hooks by supplying the same arguments you gave.

```bash
forever stop deploy/hookshot.js -p 3000 -b your-branch -c "cd $HOME/18f/18f.gsa.gov && git pull && jekyll build >> $HOME/hookshot.log"
forever restart deploy/hookshot.js -p 3000 -b your-branch -c "cd $HOME/18f/18f.gsa.gov && git pull && jekyll build >> $HOME/hookshot.log"
```

On our web server, 18F runs two separate hooks.

You may wish to use [ngrok](https://ngrok.com/) or [localtunnel](https://localtunnel.me/) in development, to test out the webhook.


### Public domain

This project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.
