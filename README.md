# Minibot

Dockerfile for a custom [Hubot](https://hubot.github.com) called Minibot.
Minibot is a IRC bot employed by the [Minishift team](https://github.com/orgs/minishift/teams/minishift-dev/members) to work on the #minishift channel on irc.freenode.net.

<!-- MarkdownTOC -->

- [Usage](#usage)
    - [Minishift stand-ups](#minishift-stand-ups)
    - [Sprint backlog](#sprint-backlog)
    - [CI notifications](#ci-notifications)
    - [Misc](#misc)
- [Developing](#developing)
    - [Building the Minibot image](#building-the-minibot-image)
    - [Running the Minibot image](#running-the-minibot-image)
    - [Testing webhooks](#testing-webhooks)
- [Resources](#resources)

<!-- /MarkdownTOC -->

<a name="usage"></a>
## Usage

<a name="minishift-stand-ups"></a>
### Minishift stand-ups

Minibot drives the daily stand-ups of the Minishift team on #minishift on irc.freenode.net.
For this it utilizes the [hubot-standup](https://github.com/miyagawa/hubot-standup) plugin.

In order to add and remove users to the stand-up [hubot-auth](https://github.com/hubot-scripts/hubot-auth) is used.

To add a user to the stand-ups:

    minibot <user> has a minishift member role

To remove a user from the stand-ups:

    minibot <user> doesn’t have a minishift member role

To list the roles of a user:

    minibot what roles does <user> have

<a name="sprint-backlog"></a>
### Sprint backlog

Sprint backlogs in comma separated format can be added to Minibot brain via a webhook:

    curl -X POST http://<IP>:9009/hubot/sprint -d sprint=134 -d data="$(cat sprint-134.csv)"

Once uploaded the backlog can be printed via the Minibot command:

    minibot print sprint <id>

 An issue can be marked as completed by:

    minibot mark issue <issue-id> of sprint <sprint-id> completed

 To see which sprints Minibot knows about:

    minibot list known sprints

<a name="ci-notifications"></a>
### CI notifications

At the moment only [Travis CI](https://travis-ci.org/minishift/minishift) and [AppVeyor](https://www.appveyor.com) send notifications to Minibot.
The required webhooks are configured in [.travis.yml](https://github.com/minishift/minishift/blob/master/.travis.yml) resp. [appveyor.yml](https://github.com/minishift/minishift/blob/master/appveyor.yml) in the Minishift [repository](https://github.com/minishift/minishift).

<a name="misc"></a>
### Misc

Other than that the following hubot scripts are installed:

* [hubot-timezone](https://github.com/ryandao/hubot-timezone)
* [hubot-good-karma](https://www.npmjs.com/package/hubot-good-karma)

<a name="developing"></a>
## Developing

<a name="building-the-minibot-image"></a>
### Building the Minibot image

    $ docker build -t minishift/minibot .

<a name="running-the-minibot-image"></a>
### Running the Minibot image

    $ docker run -rm -p 9009:9009 \
    -e HUBOT_AUTH_ADMIN=<comma seperated nics> \
    -e HUBOT_IRC_PASSWORD=<password> \
    -e REDISTOGO_URL=<redis-url> \
    -t minishift/minibot

To develop locally you can let the bot connect to a test room:

    $ docker run --rm -p 9009:9009 -e HUBOT_IRC_ROOMS=#foo -e HUBOT_AUTH_ADMIN=<comma separated nics> -e HUBOT_IRC_PASSWORD=<password> -e REDISTOGO_URL=<redis-url> -t minishift/minibot

<a name="testing-webhooks"></a>
### Testing webhooks

The [testdata](https://github.com/minishift/minibot/tree/master/testdata) directory contains some sample JSON payload files to test the Minibot webhook integration.
To test a webhook, execute the following against your local instance of Minibot:

    $ cd testdata
    $ curl http://<IP>:9009/hubot/travis_ci --data-urlencode payload@travis_ci.json
    # or
    $ curl http://<IP>:9009/hubot/appveyor -H "Content-Type: application/json" -d  @appveyor.json

where:

* \<IP\> is the IP of your Docker daemon

<a name="resources"></a>
## Resources

* [Hubot Documentation](https://hubot.github.com/docs/patterns/)
