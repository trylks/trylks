# Installing dev tools

After the [basic installation guide for Linux](https://github.com/trylks/trylks/blob/master/linux/install.md) it is time to install some basic development tools. YMMV.

The languages that I will use are Python, Clojure (and other JVM languages), and ClojureScript (and other Node.js languages).
I would like to use Rust, but I am not that cool.
I will use LaTeX too, and it is Turing complete, but for some reason I decided to put it in a different list.
This guide is for the lazy not for the fancy, you can find better guides, but they will require more effort.
Anyway, let me know about those guides, it's always good to know about them.
I would especially like to know how to install stuff in a human lifetime without compromising the security of the system.
Docker for everything? Sounds like a plan...


## Vim

1. `sudo aptitude install vim` Done! Ha ha... it's never so easy.
2. [Pathogen](http://github.com/tpope/vim-pathogen) for plugin management. There are other options,
   like [Vundle](http://github.com/gmarik/vundle), and [Neobundle](http://github.com/Shougo/neobundle.vim).
   I find pathogen the most simple to install and use, and I am too lazy to heavily customize vim,
   I like to keep it simple to use it primarily through ssh for quick hacks, in virtual machines, etc.
   But there is one plugin that I like.
3. [vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors),
   some people [do not agree](https://medium.com/@schtoeffel/you-don-t-need-more-than-one-cursor-in-vim-2c44117d51db).
4. Some people have much more fancy vim installations (TODO: add examples)

BTW: this is my `.vimrc` (included here because it is so short :)

```
execute pathogen#infect()
syntax on
filetype plugin indent on
set tabstop=8 softtabstop=0 expandtab shiftwidth=2 smarttab autoindent
set hlsearch incsearch
set autoread
set scrolloff=3
```


## Atom

I find Atom a lot easier to use and customize.
There are other awesome editors like Spacemacs, or regular emacs (especially with ensime, maybe with neotree).
Life is too short.
From [the official documentation](https://flight-manual.atom.io/getting-started/sections/installing-atom/):

1. Add the repository: `curl -L https://packagecloud.io/AtomEditor/atom/gpgkey | sudo apt-key add -`
1. `sudo sh -c 'echo "deb [arch=amd64] https://packagecloud.io/AtomEditor/atom/any/ any main" > /etc/apt/sources.list.d/atom.list'`
1. `sudo aptitude update && aptitude -y install atom`
1. `apm install hydrogen parinfer vim-mode-plus rainbow-delimiters lisp-paredit atom-beautify proto-repl`


## Leiningen

1. Install Java: `sudo aptitude install default-jdk`
1. [Install](https://leiningen.org/)
1. Configure (ToDo)

## Node

nvm.sh is 3545 lines long, and it is loaded every time in bash.
I sure hope Tim Caswell is not installing a keylogger in my computer.
Could some of the non-lazy people check it?
Probably nothing to worry about.
Thank you, Tim!

1. NVM: `curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash`
1. Node: `nvm install node`
1. ClojureScript (ToDo)

## Python

1. `aptitude install python3-pip`
1. `pip3 install --upgrade pipenv`
1. `mkdir -p ~/code/python/whatever && cd ~/code/python/whatever && pipenv --three`

## Docker

ToDo

## Scala

1. SBT
1. ENSIME
