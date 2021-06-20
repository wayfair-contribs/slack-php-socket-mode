# Slack PHP – Socket Mode

This package provides a slack-php `AppServer` implementation that allows an application written for the
[Slack App Framework for PHP][1] to be run in [Socket Mode][2]. Socket Mode uses the websocket protocol to communicate
with Slack, and allows you to run an app (even locally) without having to expose it via a public HTTP endpoint. This can
be very useful for testing Slack apps in a way that does not violate most work-related firewall restrictions.

**This package should be used for testing only, and is not designed for production use.**

> This Socket Mode implementation uses the [`amphp/websocket-client`][3] package from [Amp][4], an awesome collection of
PHP packages that make async and event loop style programming in PHP possible. Check it out sometime.

## Installation

- Requires PHP 7.4+
- Use Composer to install: `composer require slack-php/slack-socket-mode`

## Usage

When using Socket Mode, there is no web server serving your app. When you run an app configured for Socket Mode, it
establishes a connection to Slack as a websocket client. It maintains that connection to listen for Slack events until
it is explicitly terminated (e.g., via `CTRL+C`), Socket Mode is disabled in your app configuration, or an unrecoverable
error occurs. This is a different way of running PHP than many are used to, so please pay close attention.

When running an app in Slack Mode, you need an "App Token" instead of the typical "Signing Secret". The App Token is
specially purposed for making the web socket connection. You can read more about [Socket Mode][2] in the Slack
documentation to learn how to get an App Token.

### Example

This small app responds to the `/cool` slash command.

> Assumptions:
>
> - You have required the Composer autoloader to enable autoloading of the framework files.
> - You have set `SLACK_APP_TOKEN` in the environment (e.g., `putenv("SLACK_APP_TOKEN=foo");`)

```php
<?php

use SlackPhp\Framework\App;
use SlackPhp\Framework\Context;
use SlackPhp\SocketMode\SocketServer;

App::new()
    ->command('cool', function (Context $ctx) {
        $ctx->ack(':thumbsup: That is so cool!');
    })
    ->run(new SocketServer());
```

[1]: https://github.com/slack-php/slack-php-app-framework
[2]: https://api.slack.com/apis/connections/socket
[3]: https://amphp.org/websocket-client/
[4]: https://amphp.org/
