# Poetry and Outdated Pip and Setuptools

Aug 4, 2022

## The Problem

When creating new poetry environments, a problem I've encountered is that `pip` and `setuptools`
are always old versions.

After creating an environment, running `$ poetry run python -m pip install --upgrade setuptools pip`
would always yield output such as this:

```
/home/peter/.pyenv/versions/3.10.5/lib/python3.10/site-packages/setuptools/command/install.py:34: SetuptoolsDeprecationWarning: setup.py install is deprecated. Use build and pip and other standards-based tools.
  warnings.warn(
Requirement already satisfied: setuptools in /home/peter/.cache/pypoetry/virtualenvs/starlite-Zk1-j2lI-py3.10/lib/python3.10/site-packages (58.3.0)
Collecting setuptools
  Using cached setuptools-63.2.0-py3-none-any.whl (1.2 MB)
Requirement already satisfied: pip in /home/peter/.cache/pypoetry/virtualenvs/starlite-Zk1-j2lI-py3.10/lib/python3.10/site-packages (21.3.1)
Collecting pip
  Using cached pip-22.1.2-py3-none-any.whl (2.1 MB)
Installing collected packages: setuptools, pip
  Attempting uninstall: setuptools
    Found existing installation: setuptools 58.3.0
    Uninstalling setuptools-58.3.0:
      Successfully uninstalled setuptools-58.3.0
  Attempting uninstall: pip
    Found existing installation: pip 21.3.1
    Uninstalling pip-21.3.1:
      Successfully uninstalled pip-21.3.1
Successfully installed pip-22.1.2 setuptools-63.2.0
```

## Things I Tried

1. Make sure using latest and greatest: `$ pyenv local 3.10.5 && pyenv shell 3.10.5`
2. Make sure `pip` and `setuptools` up-to-date:
`$ python3.10 -m pip install --upgrade setuptools pip`
3. Make sure `poetry` will use target py ver: `$ poetry env use 3.10.5`

But the result was always the same - the environment would create with outdated `pip` and
`setuptools`.

## The Solution

!!! note

    I strongly suspect this works as neatly as it does due to using `pyenv`, and you may need to
    target the specific version of python that `poetry` is installed under otherwise. I just haven't
    needed to look into it (yet!). For example `$ which poetry` on my system yields
    `/home/peter/.pyenv/shims/poetry`.

`poetry` uses `virtualenv` to create the environment, `virtualenv` has embedded wheels for `pip`
and `setuptools` that it uses to populate the environment, and those wheels only update if you
explicitly tell it to do so.

1. `$ python3.10 -m virtualenv --upgrade-embed-wheels`
2. Make sure `poetry` up-to-date: `$ poetry self update`
3. Git rid of any existing poetry environment `$ poetry env remove python`
4. Tell poetry to use the python version where you know `virtualenv` wheels are up-to-date:
`$ poetry env use 3.10.5`
5. `$ poetry install`

Here's the output from `$ python3.10 -m virtualenv --upgrade-embed-wheels`:

```
upgrade wheel for python 3.11 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade setuptools for python 3.10 with current setuptools-63.2.0-py3-none-any.whl
upgrade setuptools for python 3.11 with current setuptools-63.2.0-py3-none-any.whl
upgrade wheel for python 3.10 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade pip for python 3.10 with current pip-22.1.2-py3-none-any.whl
upgrade wheel for python 3.9 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade setuptools for python 3.9 with current setuptools-63.2.0-py3-none-any.whl
upgrade pip for python 3.11 with current pip-22.1.2-py3-none-any.whl
upgrade pip for python 3.9 with current pip-22.1.2-py3-none-any.whl
upgrade wheel for python 3.8 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade setuptools for python 3.8 with current setuptools-63.2.0-py3-none-any.whl
upgrade pip for python 3.8 with current pip-22.1.2-py3-none-any.whl
upgrade setuptools for python 3.7 with current setuptools-63.2.0-py3-none-any.whl
upgrade pip for python 3.7 with current pip-22.1.2-py3-none-any.whl
upgrade pip for python 3.6 with current pip-21.3.1-py3-none-any.whl
upgrade wheel for python 3.7 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade setuptools for python 3.6 with current setuptools-59.6.0-py3-none-any.whl
upgrade wheel for python 3.6 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade setuptools for python 3.5 with current setuptools-50.3.2-py3-none-any.whl
upgrade pip for python 3.5 with current pip-20.3.4-py2.py3-none-any.whl
upgrade wheel for python 3.5 with current wheel-0.37.1-py2.py3-none-any.whl
upgrade setuptools for python 2.7 with current setuptools-44.1.1-py2.py3-none-any.whl
upgrade pip for python 2.7 with current pip-20.3.4-py2.py3-none-any.whl
upgrade wheel for python 2.7 with current wheel-0.37.1-py2.py3-none-any.whl
upgraded wheel for python 3.8 in 0:00:07.178834 no new versions found
upgraded wheel for python 3.9 in 0:00:08.326188 no new versions found
upgraded wheel for python 3.10 in 0:00:08.820337 no new versions found
upgraded pip for python 3.9 in 0:00:08.926392 new entries found:
	NewVersion(filename=pip-22.2.2-py3-none-any.whl), found_date=2022-08-04 10:47:43.426451, release_date=2022-08-03 18:56:21, source=manual)
upgraded wheel for python 3.11 in 0:00:08.989589 no new versions found
upgraded pip for python 3.7 in 0:00:09.517188 new entries found:
	NewVersion(filename=pip-22.2.2-py3-none-any.whl), found_date=2022-08-04 10:47:43.488881, release_date=2022-08-03 18:56:21, source=manual)
upgraded wheel for python 3.5 in 0:00:09.540025 no new versions found
upgraded pip for python 3.11 in 0:00:09.721290 new entries found:
	NewVersion(filename=pip-22.2.2-py3-none-any.whl), found_date=2022-08-04 10:47:43.423945, release_date=2022-08-03 18:56:21, source=manual)
upgraded wheel for python 3.6 in 0:00:09.717932 no new versions found
upgraded pip for python 3.5 in 0:00:09.865223 no new versions found
upgraded pip for python 3.8 in 0:00:09.979156 new entries found:
	NewVersion(filename=pip-22.2.2-py3-none-any.whl), found_date=2022-08-04 10:47:43.491660, release_date=2022-08-03 18:56:21, source=manual)
upgraded pip for python 3.10 in 0:00:10.038887 new entries found:
	NewVersion(filename=pip-22.2.2-py3-none-any.whl), found_date=2022-08-04 10:47:43.422540, release_date=2022-08-03 18:56:21, source=manual)
upgraded wheel for python 3.7 in 0:00:10.107140 no new versions found
upgraded wheel for python 2.7 in 0:00:10.144852 no new versions found
upgraded pip for python 2.7 in 0:00:10.173553 no new versions found
upgraded setuptools for python 3.5 in 0:00:10.261062 no new versions found
upgraded setuptools for python 3.10 in 0:00:10.364955 new entries found:
	NewVersion(filename=setuptools-63.4.1-py3-none-any.whl), found_date=2022-08-04 10:47:43.417007, release_date=2022-08-03 16:22:45, source=manual)
upgraded pip for python 3.6 in 0:00:10.371469 no new versions found
upgraded setuptools for python 2.7 in 0:00:10.253946 no new versions found
upgraded setuptools for python 3.8 in 0:00:10.476140 new entries found:
	NewVersion(filename=setuptools-63.4.1-py3-none-any.whl), found_date=2022-08-04 10:47:43.443267, release_date=2022-08-03 16:22:45, source=manual)
upgraded setuptools for python 3.6 in 0:00:10.473055 no new versions found
upgraded setuptools for python 3.7 in 0:00:10.534038 new entries found:
	NewVersion(filename=setuptools-63.4.1-py3-none-any.whl), found_date=2022-08-04 10:47:43.490381, release_date=2022-08-03 16:22:45, source=manual)
upgraded setuptools for python 3.11 in 0:00:10.652691 new entries found:
	NewVersion(filename=setuptools-63.4.1-py3-none-any.whl), found_date=2022-08-04 10:47:43.419027, release_date=2022-08-03 16:22:45, source=manual)
upgraded setuptools for python 3.9 in 0:00:10.699067 new entries found:
	NewVersion(filename=setuptools-63.4.1-py3-none-any.whl), found_date=2022-08-04 10:47:43.425957, release_date=2022-08-03 16:22:45, source=manual)
SystemExit: None
```

It turns out that `wheel` is embedded as well! The relevant section of the `virtualenv` docs is
[here](https://virtualenv.pypa.io/en/latest/user_guide.html#wheels).

Here's [the PR](https://github.com/python-poetry/poetry/pull/2666) where `poetry` standardized to
using `virtualenv`.
