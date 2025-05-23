---
id: 2zu8t2vvtwwdp765hf5ikje
title: Latex
desc: ''
updated: 1646895646334
created: 1646895143821
---

* [CTAN](https://www.ctan.org/)
* [Installing on Ubuntu](https://linuxconfig.org/how-to-install-latex-on-ubuntu-20-04-focal-fossa-linux)
* [Installing additional LaTeX class and style files](https://www.volkerschatz.com/tex/tpacks.html)
* [Installing LaTeX Packages(LINUX)](https://medium.com/@kiprono_65591/installing-latex-packages-linux-61ed4d514ad6)
* [How to incorporate a TEXMF tree managed by distro's package manager when using TeX Live from upstream on a GNU/Linux system?](https://tex.stackexchange.com/questions/262063/
how-to-incorporate-a-texmf-tree-managed-by-distros-package-manager-when-using-t)

* [$TEXMFHOME setting](https://tex.stackexchange.com/questions/71469/texmfhome-setting)
```shell
kpsewhich --var-value TEXMFLOCAL

$ kpsewhich -var-value TEXMFHOME
/home/gpoo/texmf
$ export TEXMFHOME=$HOME/.texmf
$ kpsewhich -var-value TEXMFHOME
/home/gpoo/.texmf

You can export the variable in your .bashrc file as:

if [ -d ~/.texmf ] ; then
    export TEXMFHOME=~/.texmf
fi
```


* https://github.com/xdanaux/fontawesome-latex
* https://github.com/xdanaux/moderncv

* https://github.com/xdanaux/moderncv/issues/79

```
In the moderncv.cls file:
change (close to line 193):
\RequirePackage{l3regex}

to:

\usepackage{expl3}
\expandafter\def\csname ver@l3regex.sty\endcsname{}
\RequirePackage{l3regex}```
```



* [Where can I put local latex style files and packages?](https://www.ias.edu/math/computing/faq/local-latex-style-files)

```shell
mkdir -p texmf/tex/latex
# Bibtex .bst
# $HOME/texmf/bibtex/bst/

texhash 
# or 
mktexlsr

# export TEXINPUTS=::/path/to/mydir
```
