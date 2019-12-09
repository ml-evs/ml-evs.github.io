# Building vim from source for Python development

Here is a quick guide outlining the way I build Vim on a new machine on which
the user may not have that much power (to e.g. install new packages). Here, we
use conda to create a compatible set of build tools (GCC, binutils etc.) that
will allow us to build Vim 8.1 with Python support. 

If you don't have it, install conda and set up the base environment. I tend to
use the prefix `$HOME/.local/conda` for this, and some instructions later on may
depend on this.

    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    chmod u+x Miniconda3-latest-Linux-x86_64.sh
    ./Miniconda3-latest-Linux-x86_64.sh

Personally, I disable pip in this environment, so I don't accidentally get
myself in a mess...
    
    conda activate base
    mv $(which pip) $(which pip)___
    conda deactivate

Let's make the environment for our devtools:

    conda create -n devtools python=3.6
    conda activate devtools
    conda install gcc_linux-64 flake8 jedi pylint flake8 pep8

Time to grab vim:

    export CC=$HOME/.local/conda/envs/devtools/bin/x86_64-conda_cos6-linux-gnu-gcc
    cd $HOME/.local/opt
    git clone https://github.com/vim/vim --branch v8.1.2109
    cd vim
    LDFLAGS=-L$HOME/.local/conda/envs/devtools/lib ./configure
    --with-features=huge --enable-multibyte --enable-rubyinterp=yes
    --enable-python3interp=yes --enable-gui=gtk2 --enable-scope
    --with-python3-config-dir=$(python3-config --configdir)
    --prefix="$HOME/.local/" > configure.log

    make > make.log
    make install > makeinstall.log

