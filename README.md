# telegram-send

[![Version](https://img.shields.io/pypi/v/telegram-send.svg)](https://pypi.python.org/pypi/telegram-send)
[![pyversions](https://img.shields.io/pypi/pyversions/telegram-send.svg)](https://pypi.python.org/pypi/telegram-send)
[![License](https://img.shields.io/badge/License-GPLv3+-blue.svg)](https://github.com/rahiel/telegram-send/blob/master/LICENSE.txt)

Telegram-send is a command-line tool to send messages and files over Telegram to
your account or to a channel. It provides a simple interface that can be easily
called from other programs.

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Usage](#usage)
- [Install](#install)
- [Examples](#examples)
    - [alert on completion of shell commands](#alert-on-completion-of-shell-commands)
    - [periodic messages with cron](#periodic-messages-with-cron)
    - [supervisor process state notifications](#supervisor-process-state-notifications)
- [Uninstall](#uninstall)

<!-- markdown-toc end -->

# Usage

To send a message:
``` shell
telegram-send "hello, world"
```

To send a message using Markdown or HTML formatting:
```shell
telegram-send --format markdown "Only the *bold* use _italics_"
telegram-send --format html "<pre>fixed-width messages</pre> are <i>also</i> supported"
```
(for more information on supported formatting, see the [formatting documentation](https://core.telegram.org/bots/api#formatting-options))

To send a file:
``` shell
telegram-send --file document.pdf
```

To send an image with an optional caption:
``` shell
telegram-send --image photo.jpg --caption "The Moon at night"
```

Telegram-send integrates into your file manager (Thunar, Nautilus and Nemo):

![](https://cloud.githubusercontent.com/assets/6839756/16735957/51c41cf4-478b-11e6-874a-282f559fb9d3.png)

# Install

Install telegram-send system-wide with pip:
``` shell
sudo pip3 install telegram-send
```

Or if you want to install it for a single user (recommended):
``` shell
pip3 install telegram-send
```

If installed for a single user you need to add `~/.local/bin` to their path:
``` shell
echo 'PATH="$HOME/.local/bin:$PATH"' >> ~/.profile
```
(This will be in effect on the next login, do a `. ~/.profile` for the
impatient.)

And finally configure it with `telegram-send --configure` if you want to send to
your account, or with `telegram-send --configure-channel` to send to a channel.

Use the `--config` option to use multiple configurations. For example to set up
sending to a channel in a non-default configuration: `telegram-send --config
channel.conf --configure-channel`. Then always specify the config file to use
it: `telegram-send --config channel.conf "hello"`.

# Examples

Here are some examples to get a taste of what is possible with telegram-send.

## alert on completion of shell commands

Receive an alert when long-running commands finish with the `tg` alias, based on
Ubuntu's built-in `alert`. Put the following in your `~/.bashrc`:

``` shell
alias tg='telegram-send "$([ $? = 0 ] && echo "" || echo "error: ") $(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*tg$//'\'')"'
```

And then use it like `sleep 10: tg`.

To automatically receive notifications for long running commands, use [ntfy][]
with the Telegram backend.

[ntfy]: https://github.com/dschep/ntfy

## periodic messages with cron

We can combine telegram-send with [cron][] to periodically send messages. Here
we will set up a cron job to send the [Astronomy Picture of the Day][apod] to
the [astropod][] channel.

Create a bot by talking to the [BotFather][], create a public channel and add
your bot as administrator to the channel. You will need to explicitly search for
your bot's username when adding it. Then run `telegram-send --configure-channel
--config astropod.conf`. We will use the [apod.py][] script that gets the daily
picture and calls telegram-send to post it to the channel.

We create a cron job `/etc/cron.d/astropod` (as root) with the content:

``` shell
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
0 1 * * * telegram ~/apod.py --config ~/astropod.conf
```

Make sure the file ends with a newline. Cron will then execute the script every
day at 1:00 as the user `telegram`. Join the [astropod][] channel to see the
result.

[cron]: https://en.wikipedia.org/wiki/Cron
[apod]: http://apod.nasa.gov/apod/astropix.html
[astropod]: https://telegram.me/astropod
[botfather]: https://telegram.me/botfather
[apod.py]: https://github.com/rahiel/telegram-send/blob/master/examples/apod.py

## supervisor process state notifications

[Supervisor][] controls and monitors processes. It can start processes at boot,
restart them if they fail and also report on their status. [Supervisor-alert][]
is a simple plugin for supervisor that sends messages on process state updates
to an arbitrary program. Using it with telegram-send (by using the `--telegram`
option), you can receive notifications whenever one of your processes exits.

[supervisor]: http://supervisord.org
[supervisor-alert]: https://github.com/rahiel/supervisor-alert

# Uninstall

``` shell
sudo telegram-send --clean
sudo pip3 uninstall telegram-send
```

Or if you installed it for a single user:
``` shell
telegram-send --clean
pip3 uninstall telegram-send
```
