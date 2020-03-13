+++
author = "Esteban"
title = "Adding quotes to tmux status bar"
description = "Setting tmux right status bar to greet me with a funny quote periodically"
date = 2020-03-13T14:42:31+01:00
image = ""
slug = "adding-funny-quotes-tmux-statusbar"
categories = []
tags = []
draft = false
+++

After reading Jezen Thomas' cool blog post about [Showing Weather in Tmux](https://jezenthomas.com/showing-the-weather-in-tmux/), I tried it in my computer and loved the result after minor tweaks: ![weather-tmux](/images/weather-tmux.png)


 From that moment it also occurred to me that next to the *weather*, *date* and *time* I would love to have a greeting sentence in my local language, [galician](https://en.wikipedia.org/wiki/Galician_language), to bring back the warm and fuzzy feeling of being at home after being away already for five years.

Galician culture and people are known to be sarcastic and suspicious, in a gleeful and cute way. It is hard to describe it fully until you've been there and experienced the food, the mountains, the sea and the people. [It is truly an amazing place to have grown in](https://www.turismo.gal/inicio?langId=en_US).

I then wrote a small script in bash that would be called from the `.tmux.conf` file in order to show a new sentence on startup and every 15 minutes, although that's configurable I didn't feel like complicating myself that much. The important bit of the `.tmux.conf` file is:

```sh
set-option -g status-interval 4 # Refresh interval for the status bar
set-option -g status-right-length 200 # Truncate right status bar after that length
set -g status-right "#[fg=colour11](#[default]#($HOME/gits/configs/tmux/quotes.sh)#[fg=colour11]) #[fg=green][#[default]#($HOME/gits/configs/tmux/weather.sh)#[fg=green]]#[fg=green][#[fg=colour45]%d-%m-%Y#[fg=green]]#[fg=green][#[fg=colour15]%H:%M#[default]#[fg=green]]"
```

The last big line is going to format the right status bar in tmux to show the information as previous image. As seen at the beginning of the line, it calls the `quotes.sh` in each refresh interval in order to set the status. The script looks like:

```bash
#!/bin/bash

EXPRESSIONS=(
    "Marcho que teño que marchar"
    "Vaiche boa",
    # ETC..
)

# Seed random generator
RANDOM=$$$(date +%s)

MINUTE=$(date +"%T" | awk -F ":" '{ print $2 }')
SECOND=$(date +"%T" | awk -F ":" '{ print $3 }')


# If no frasegallega.current means first time the script is run.
# Silly way to update the sentence only each 15 min as opposed to the
# 4~5 seconds the status-interval is set to (or whatever number). 
if [ ! -f "/tmp/frasegallega.current" ]; then
    touch /tmp/frasegallega.current

    # Get first expression.
    SELECTED_EXPRESSION=${EXPRESSIONS[$RANDOM % ${#EXPRESSIONS[@]} ]}

    # Write to Shell
    echo $SELECTED_EXPRESSION > /tmp/frasegallega.current
    echo $SELECTED_EXPRESSION
else
    # Get current expression.
    CURRENT_EXPRESSION=$(cat /tmp/frasegallega.current)

    # Each 15 minutes
    if [[ $MINUTE =~ 00|15|30|45 ]] && [[ $SECOND =~ ^0[0-3]$ ]]; then

	# Get random expression.
	SELECTED_EXPRESSION=${EXPRESSIONS[$RANDOM % ${#EXPRESSIONS[@]} ]}

	echo $SELECTED_EXPRESSION
	echo $CURRENT_EXPRESSION

	# If the random expression is the current one just keep trying
	while [ "$SELECTED_EXPRESSION" == "$CURRENT_EXPRESSION" ];
	do
	    SELECTED_EXPRESSION=${EXPRESSIONS[$RANDOM % ${#EXPRESSIONS[@]} ]}
	done

	# Set the new one as the current
	echo $SELECTED_EXPRESSION > /tmp/frasegallega.current
    else
	echo $CURRENT_EXPRESSION
    fi
fi
```

So it will set the quote first time in a temp file and then every 15 minutes will change it with a new one, it is as easy as use your own list of sentences, or query an API and parse the return JSON with *jq* or whatever. This is how it looks now: ![quotes-tmux](/images/quotes-tmux.png)



Have fun!

