Working in Jupyter
==================

> Write something about working in Jupyter Lab and/or Notebooks.

## Run a notebook remotely

You can process and run a remote notebook from a current notebook with the magic [run](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-run) command. The output of the remote workbook will be put into your current one.

For example, I sometimes separate the download/clean functions of a project from the analysis functions. This way, when working on the analysis you don't have to re-download the data each time you re-run the notebook, unless you want to.

So, given you have two notebooks:

- `01_download.ipynb`
- `02_analyze.ipynb`

The second notebook at "run" the first one, if you need to. Put this at the top of the second notebook and then comment/uncomment as needed:

``` python
%run 01_download.ipynb
```

## Run a notebook from the command line

You can also execute notebooks using [nbconvert](http://nbconvert.readthedocs.io/en/latest/execute_api.html) to run them from the command line. Doing this will create a copy of your notebook called `01_download.nbconvert.ipynb`

``` bash
$ jupyter nbconvert --to notebook --execute 01_download.ipynb
```

HOWEVER: it does not work if you have the `run` command in a notebook as outlined above.

> Thanks to Ben Welsh for [outlining these methods](https://github.com/palewire/jupyter-notebook-execution-examples/blob/master/notebook.ipynb).