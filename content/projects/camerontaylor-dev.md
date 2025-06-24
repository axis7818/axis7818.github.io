+++
title = "camerontaylor.dev"
description = "My personal website that you are currently looking at"
weight = 10
template = "page.html"

[taxonomies]
tags = ["Software"]

[extra]
local_image = "img/profile.jpg"
+++


# [Hi, I'm Cameron! 👋](/)

![profile picture](/img/profile.jpg)

This is my personal website! It is getting an overhaul in 2025.

# Zola & tabi

It is implemented using the static site engine, [Zola](https://www.getzola.org/), and uses the [tabi](https://welpo.github.io/tabi/) theme.

To get started quickly with a static site like this, the tabi-start template repository is the quickest & easiest way to start.

#### [tabi-start GitHub repository](https://github.com/welpo/tabi-start){.centered-text}

To set up and use my own repository:

1. Fork the template repo above to my GitHub account.
1. Run the following
	```sh
	# Assuming macOS
	brew install zola
	git clone https://github.com/axis7818/axis7818.github.io
	git submodule update --init --recursive
	zola serve
	```
1. Access the site at `127.0.0.1:1111`
