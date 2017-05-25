+++
date = "2017-04-03T16:25:31-04:00"
highlight = true
math = false
tags = []
title = "Writing LaTeX in Vim"

[header]
  caption = ""
  image = ""

+++

As an academic I am often writing LaTeX code for publications or general
documentation. I used to use [ShareLatex](https://www.sharelatex.com/)
and [Overleaf](https://www.overleaf.com/) because I have a secret love
of GUIs (Lord forgive me!), but recently my co-authors have prefered
to work in LaTeX repos shared on [GitHub](https://github.com/). For
this reason my most recent paper was actually written entirely in Vim!
This post isn't meant to be a complete description of the best way to
edit LaTeX in Vim, but instead I want to
share some of the tools, tricks, and tips that I've
found useful when writing LaTeX in Vim.

* Vim environment setup

	I've made a nice little [GitHub
	repo](https://github.com/mbuckler/vim-config) to host my Vim
	configuration settings (.vimrc) so that porting it between my machines
	is made easier. No need to copy my .vimrc wholesale if you don't like
	everything, but these are a few options that I use:

	1. Installing [Vundle](https://github.com/VundleVim/Vundle.vim) for Vim
	package management and [sensible.vim](https://github.com/tpope/vim-sensible)
	for near-universal improvements to the default Vim settings.

	2. Tabs vs spaces... We could [fight all
	day](https://www.youtube.com/watch?v=SsoOG6ZeyUI) about using tabs or
	spaces, but you'll want to put your preferred settings in your .vimrc.

	3. Automatic text wrapping with `set wrap`. This is one of the most
	useful settings when writing LaTeX in Vim.

	4. Change out that ugly red column border with a nice grey column border
	with `set colorcolumn=+1` and `hi ColorColumn ctermbg=7`.

	5. Show line numbers for easy reference: `set number`

	6. Set your preferred spell check default. As an American I do `set
	spelllang=en_us`

* Useful Vim commands

	In addition to having a good setup for Vim, here are a few commands that
	I am constantly using.

	1. Search: `/foo`

	2. Search and replace: `:%s/foo/bar/g`

	3. Toggle line numbers: `set nu`, `set nu!`

	4. Enable pasting without auto-indentation: `set paste`, `set paste!`

	5. Turn on and off spell checking: `set spell`, `set spell!`

	6. Automatically wrap a block of text: First select with `v` for
	visual, and then use `gq` to perform the wrapping.

	7. Indent an entire section: First select with `v` and then indent
	with `>>`.

* Automatic LaTeX compilation

	This isn't Vim specific, but you're going to need a
	complation method to turn your LaTeX in to a PDF. On Ubuntu this works
	very well for me:

	1. Install texlive and latexmk from your package manager. This will require more
	than a Gigabyte of space on your disk and so will take a while, but that
	sure beats having to selectively install LaTeX packages.

		```
		sudo apt-get install texlive-full latexmk
		```

	2. Start a background process to automatically compile your LaTeX and
	generate a PDF. You can edit your document in a separate terminal tab
	and have the PDF on the other side of your screen. Seemless editing!

		```
		latexmk -pvc -pdf main.tex &
		```
