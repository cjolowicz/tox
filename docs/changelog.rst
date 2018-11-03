.. _changelog:

Changelog history
=================

Versions follow `Semantic Versioning <https://semver.org/>`_ (``<major>.<minor>.<patch>``).
Backward incompatible (breaking) changes will only be introduced in major versions
with advance notice in the **Deprecations** section of releases.

.. include:: _draft.rst

.. towncrier release notes start

v3.5.3 (2018-10-28)
-------------------

Bugfixes
^^^^^^^^

- Fix bug with incorrectly defactorized dependencies - by :user:`bartsanchez` (`#706 <https://github.com/tox-dev/tox/issues/706>`_)
- do the same transformation to ``egg_info`` folders that ``pkg_resources`` does;
  this makes it possible for hyphenated names to use the ``develop-inst-noop`` optimization (cf. 910),
  which previously only worked with non-hyphenated egg names - by
  :user:`hashbrowncipher` (`#1051 <https://github.com/tox-dev/tox/issues/1051>`_)
- previously, if a project's ``setup.py --name`` emitted extra information to
  stderr, tox would capture it and consider it part of the project's name; now,
  emissions to stderr are printed to the console - by :user:`hashbrowncipher` (`#1052 <https://github.com/tox-dev/tox/issues/1052>`_)
- change the way we acquire interpreter information to make it compatible with ``jython`` interpreter, note to create jython envs one needs ``virtualenv > 16.0`` which will be released later :user:`gaborbernat` (`#1073 <https://github.com/tox-dev/tox/issues/1073>`_)


Documentation
^^^^^^^^^^^^^

- document substitutions with additional content starting with a space cannot be alone on a line inside the ini file - by :user:`gaborbernat` (`#437 <https://github.com/tox-dev/tox/issues/437>`_)
- change the spelling of a single word from contrains to the proper word, constraints - by :user:`metasyn` (`#1061 <https://github.com/tox-dev/tox/issues/1061>`_)
- Mention the minimum version required for ``commands_pre``/``commands_post`` support. (`#1071 <https://github.com/tox-dev/tox/issues/1071>`_)


v3.5.2 (2018-10-09)
-------------------

Bugfixes
^^^^^^^^

- session packages are now put inside a numbered directory (instead of prefix numbering it,
  because pip fails when wheels are not named according to
  `PEP-491 <https://www.python.org/dev/peps/pep-0491/#id9>`_, and prefix numbering messes with this)
  - by :user:`gaborbernat` (`#1042 <https://github.com/tox-dev/tox/issues/1042>`_)


Features
^^^^^^^^

- level three verbosity (``-vvv``) show the packaging output - by :user:`gaborbernat` (`#1047 <https://github.com/tox-dev/tox/issues/1047>`_)


v3.5.1 (2018-10-08)
-------------------

Bugfixes
^^^^^^^^

- fix regression with ``3.5.0``: specifying ``--installpkg`` raises ``AttributeError: 'str' object has no attribute 'basename'`` (`#1042 <https://github.com/tox-dev/tox/issues/1042>`_)


v3.5.0 (2018-10-08)
-------------------

Bugfixes
^^^^^^^^

- intermittent failures with ``--parallel--safe-build``, instead of mangling with the file paths now uses a lock to make the package build operation thread safe and is now on by default (``--parallel--safe-build`` is now deprecated) - by :user:`gaborbernat` (`#1026 <https://github.com/tox-dev/tox/issues/1026>`_)


Features
^^^^^^^^

- Added ``temp_dir`` folder configuration (defaults to ``{toxworkdir}/.tmp``) that contains tox
  temporary files. Package builds now create a hard link (if possible, otherwise copy - notably in
  case of Windows Python 2.7) to the built file, and feed that file downstream (e.g. for pip to
  install it). The hard link is removed at the end of the run (what it points though is kept
  inside ``distdir``). This ensures that a tox session operates on the same package it built, even
  if a parallel tox run builds another version. Note ``distdir`` will contain only the last built
  package in such cases. - by :user:`gaborbernat` (`#1026 <https://github.com/tox-dev/tox/issues/1026>`_)


Documentation
^^^^^^^^^^^^^

- document tox environment recreate rules (:ref:`recreate`) - by :user:`gaborbernat` (`#93 <https://github.com/tox-dev/tox/issues/93>`_)
- document inside the ``--help`` how to disable colorized output via the ``PY_COLORS`` operating system environment variable - by :user:`gaborbernat` (`#163 <https://github.com/tox-dev/tox/issues/163>`_)
- document all global tox flags and a more concise format to express default and type - by :user:`gaborbernat` (`#683 <https://github.com/tox-dev/tox/issues/683>`_)
- document command line interface under the config section `cli <https://tox.readthedocs.io/en/latest/config.html?highlight=cli#cli>`_ - by :user:`gaborbernat` (`#829 <https://github.com/tox-dev/tox/issues/829>`_)

v3.4.0 (2018-09-20)
-------------------

Bugfixes
^^^^^^^^

- add ``--exists-action w`` to default pip flags to handle better VCS dependencies (`pip documentation on this <https://pip.pypa.io/en/latest/reference/pip/#exists-action-option>`_) - by :user:`gaborbernat` (`#503 <https://github.com/tox-dev/tox/issues/503>`_)
- instead of assuming the Python version from the base python name ask the interpreter to reveal the version for the ``ignore_basepython_conflict`` flag - by :user:`gaborbernat` (`#908 <https://github.com/tox-dev/tox/issues/908>`_)
- PEP-517 packaging fails with sdist already exists, fixed via ensuring the dist folder is empty before invoking the backend and `pypa/setuptools 1481 <https://github.com/pypa/setuptools/pull/1481>`_ - by :user:`gaborbernat` (`#1003 <https://github.com/tox-dev/tox/issues/1003>`_)


Features
^^^^^^^^

- add ``commands_pre`` and ``commands_post`` that run before and after running
  the ``commands`` (setup runs always, commands only if setup suceeds, teardown always - all
  run until the first failing command)  - by :user:`gaborbernat` (`#167 <https://github.com/tox-dev/tox/issues/167>`_)
- ``pyproject.toml`` config support initially by just inline the tox.ini under ``tool.tox.legacy_tox_ini`` key; config source priority order is ``pyproject.toml``, ``tox.ini`` and then ``setup.cfg`` - by :user:`gaborbernat` (`#814 <https://github.com/tox-dev/tox/issues/814>`_)
- use the os environment variable ``TOX_SKIP_ENV`` to filter out tox environment names from the run list (set by ``envlist``)  - by :user:`gaborbernat` (`#824 <https://github.com/tox-dev/tox/issues/824>`_)
- always set ``PIP_USER=0`` (do not install into the user site package, but inside the virtual environment created) and ``PIP_NO_DEPS=0`` (installing without dependencies can cause broken package installations) inside tox - by :user:`gaborbernat` (`#838 <https://github.com/tox-dev/tox/issues/838>`_)
- tox will inject some environment variables that to indicate a command is running within tox: ``TOX_WORK_DIR`` env var is set to the tox work directory,
  ``TOX_ENV_NAME`` is set to the current running tox environment name, ``TOX_ENV_DIR`` is set to the current tox environments working dir - by :user:`gaborbernat` (`#847 <https://github.com/tox-dev/tox/issues/847>`_)
- While running tox invokes various commands (such as building the package, pip installing dependencies and so on), these were printed in case they failed as Python arrays. Changed the representation to a shell command, allowing the users to quickly replicate/debug the failure on their own - by :user:`gaborbernat` (`#851 <https://github.com/tox-dev/tox/issues/851>`_)
- skip missing interpreters value from the config file can now be overridden via the ``--skip-missing-interpreters`` cli flag - by :user:`gaborbernat` (`#903 <https://github.com/tox-dev/tox/issues/903>`_)
- keep additional environments config order when listing them - by :user:`gaborbernat` (`#921 <https://github.com/tox-dev/tox/issues/921>`_)
- allow injecting config value inside the ini file dependent of the fact that we're connected to an interactive shell or not  - by :user:`gaborbernat` (`#947 <https://github.com/tox-dev/tox/issues/947>`_)
- do not build sdist if skip install is specified for the envs to be run - by :user:`gaborbernat` (`#974 <https://github.com/tox-dev/tox/issues/974>`_)
- when verbosity level increases above two start passing through verbosity flags to pip - by :user:`gaborbernat` (`#982 <https://github.com/tox-dev/tox/issues/982>`_)
- when discovering the interpreter to use check if the tox host Python matches and use that if so - by :user:`gaborbernat` (`#994 <https://github.com/tox-dev/tox/issues/994>`_)
- ``-vv`` will print out why a virtual environment is re-created whenever this operation is triggered - by :user:`gaborbernat` (`#1004 <https://github.com/tox-dev/tox/issues/1004>`_)


Documentation
^^^^^^^^^^^^^

- clarify that ``python`` and ``pip`` refer to the virtual environments executable - by :user:`gaborbernat` (`#305 <https://github.com/tox-dev/tox/issues/305>`_)
- add Sphinx and mkdocs example of generating documentation via tox - by :user:`gaborbernat` (`#374 <https://github.com/tox-dev/tox/issues/374>`_)
- specify that ``setup.cfg`` tox configuration needs to be inside the ``tox:tox`` namespace - by :user:`gaborbernat` (`#545 <https://github.com/tox-dev/tox/issues/545>`_)


v3.3.0 (2018-09-11)
-------------------

Bugfixes
^^^^^^^^

- fix ``TOX_LIMITED_SHEBANG`` when running under python3 - by :user:`asottile` (`#931 <https://github.com/tox-dev/tox/issues/931>`_)


Features
^^^^^^^^

- `PEP-517 <https://www.python.org/dev/peps/pep-0517/>`_ source distribution support (create a
  ``.package`` virtual environment to perform build operations inside) by :user:`gaborbernat` (`#573 <https://github.com/tox-dev/tox/issues/573>`_)
- `flit <https://flit.readthedocs.io>`_ support via implementing ``PEP-517`` by :user:`gaborbernat` (`#820 <https://github.com/tox-dev/tox/issues/820>`_)
- packaging now is exposed as a hook via ``tox_package(session, venv)`` - by :user:`gaborbernat` (`#951 <https://github.com/tox-dev/tox/issues/951>`_)


Miscellaneous
^^^^^^^^^^^^^

- Updated the VSTS build YAML to use the latest jobs and pools syntax - by :user:`davidstaheli` (`#955 <https://github.com/tox-dev/tox/issues/955>`_)


v3.2.1 (2018-08-10)
-------------------

Bugfixes
^^^^^^^^

- ``--parallel--safe-build`` no longer cleans up its folders (``distdir``, ``distshare``, ``log``). - by :user:`gaborbernat` (`#849 <https://github.com/tox-dev/tox/issues/849>`_)


v3.2.0 (2018-08-10)
-------------------

Features
^^^^^^^^

- Switch pip invocations to use the module ``-m pip`` instead of direct invocation. This could help
  avoid some of the shebang limitations.  - by :user:`gaborbernat` (`#935 <https://github.com/tox-dev/tox/issues/935>`_)
- Ability to specify package requirements for the tox run via the ``tox.ini`` (``tox`` section under key ``requires`` - PEP-508 style): can be used to specify both plugin requirements or build dependencies. - by :user:`gaborbernat` (`#783 <https://github.com/tox-dev/tox/issues/783>`_)
- Allow to run multiple tox instances in parallel by providing the
  ``--parallel--safe-build`` flag. - by :user:`gaborbernat` (`#849 <https://github.com/tox-dev/tox/issues/849>`_)


v3.1.3 (2018-08-03)
-------------------

Bugfixes
^^^^^^^^

- A caching issue that caused the ``develop-inst-nodeps`` action, which
  reinstalls the package under test, to always run has been resolved. The
  ``develop-inst-noop`` action, which, as the name suggests, is a no-op, will now
  run unless there are changes to ``setup.py`` or ``setup.cfg`` files that have
  not been reflected - by @stephenfin (`#909 <https://github.com/tox-dev/tox/issues/909>`_)


Features
^^^^^^^^

- Python version testenvs are now automatically detected instead of comparing
  against a hard-coded list of supported versions.  This enables ``py38`` and
  eventually ``py39`` / ``py40`` / etc. to work without requiring an upgrade to
  ``tox``.  As such, the following public constants are now deprecated
  (and scheduled for removal in ``tox`` 4.0: ``CPYTHON_VERSION_TUPLES``,
  ``PYPY_VERSION_TUPLES``, ``OTHER_PYTHON_INTERPRETERS``, and ``DEFAULT_FACTORS`` -
  by :user:`asottile` (`#914 <https://github.com/tox-dev/tox/issues/914>`_)


Documentation
^^^^^^^^^^^^^

- Add a system overview section on the index page that explains briefly how tox works -
  by :user:`gaborbernat`. (`#867 <https://github.com/tox-dev/tox/issues/867>`_)


v3.1.2 (2018-07-12)
-------------------

Bugfixes
^^^^^^^^

- Revert "Fix bug with incorrectly defactorized dependencies (`#772 <https://github.com/tox-dev/tox/issues/772>`_)" due to a regression (`(#799) <https://github.com/tox-dev/tox/issues/899>`_) - by :user:`obestwalter`

v3.1.1 (2018-07-09)
-------------------

Bugfixes
^^^^^^^^

- PyPi documentation for ``3.1.0`` is broken. Added test to check for this, and
  fix it by :user:`gaborbernat`. (`#879
  <https://github.com/tox-dev/tox/issues/879>`_)


v3.1.0 (2018-07-08)
-------------------

Bugfixes
^^^^^^^^

- Add ``ignore_basepython_conflict``, which determines whether conflicting
  ``basepython`` settings for environments containing default factors, such as
  ``py27`` or ``django18-py35``, should be ignored or result in warnings. This
  was a common source of misconfiguration and is rarely, if ever, desirable from
  a user perspective - by :user:`stephenfin` (`#477 <https://github.com/tox-dev/tox/issues/477>`_)
- Fix bug with incorrectly defactorized dependencies (deps passed to pip were not de-factorized) - by :user:`bartsanchez` (`#706 <https://github.com/tox-dev/tox/issues/706>`_)


Features
^^^^^^^^

- Add support for multiple PyPy versions using default factors. This allows you
  to use, for example, ``pypy27`` knowing that the correct intepreter will be
  used by default - by :user:`stephenfin` (`#19 <https://github.com/tox-dev/tox/issues/19>`_)
- Add support to explicitly invoke interpreter directives for environments with
  long path lengths. In the event that ``tox`` cannot invoke scripts with a
  system-limited shebang (e.x. a Linux host running a Jenkins Pipeline), a user
  can set the environment variable ``TOX_LIMITED_SHEBANG`` to workaround the
  system's limitation (e.x. ``export TOX_LIMITED_SHEBANG=1``) - by :user:`jdknight` (`#794 <https://github.com/tox-dev/tox/issues/794>`_)
- introduce a constants module to be used internally and as experimental API - by :user:`obestwalter` (`#798 <https://github.com/tox-dev/tox/issues/798>`_)
- Make ``py2`` and ``py3`` aliases also resolve via ``py`` on windows by :user:`asottile`. This enables the following things:
  ``tox -e py2`` and ``tox -e py3`` work on windows (they already work on posix); and setting ``basepython=python2`` or ``basepython=python3`` now works on windows. (`#856 <https://github.com/tox-dev/tox/issues/856>`_)
- Replace the internal version parsing logic from the not well tested `PEP-386 <https://www.python.org/dev/peps/pep-0386/>`_ parser for the more general `PEP-440 <https://www.python.org/dev/peps/pep-0440/>`_. `packaging >= 17.1 <https://pypi.org/project/packaging/>`_ is now an install dependency by :user:`gaborbernat`. (`#860 <https://github.com/tox-dev/tox/issues/860>`_)


Documentation
^^^^^^^^^^^^^

- extend the plugin documentation and make lot of small fixes and improvements - by :user:`obestwalter` (`#797 <https://github.com/tox-dev/tox/issues/797>`_)
- tidy up tests - remove unused fixtures, update old cinstructs, etc. - by :user:`obestwalter` (`#799 <https://github.com/tox-dev/tox/issues/799>`_)
- Various improvements to documentation: open browser once documentation generation is done, show Github/Travis info on documentation page, remove duplicate header for changelog, generate unreleased news as DRAFT on top of changelog, make the changelog page more compact and readable (width up to 1280px) by :user:`gaborbernat` (`#859 <https://github.com/tox-dev/tox/issues/859>`_)


Miscellaneous
^^^^^^^^^^^^^

- filter out unwanted files in package - by :user:`obestwalter` (`#754 <https://github.com/tox-dev/tox/issues/754>`_)
- make the already existing implicit API explicit - by :user:`obestwalter` (`#800 <https://github.com/tox-dev/tox/issues/800>`_)
- improve tox quickstart and corresponding tests - by :user:`obestwalter` (`#801 <https://github.com/tox-dev/tox/issues/801>`_)
- tweak codecov settings via .codecov.yml - by :user:`obestwalter` (`#802 <https://github.com/tox-dev/tox/issues/802>`_)


v3.0.0 (2018-04-02)
-------------------

Bugfixes
^^^^^^^^

- Write directly to stdout buffer if possible to prevent str vs bytes issues -
  by @asottile (`#426 <https://github.com/tox-dev/tox/issues/426>`_)
- fix #672 reporting to json file when skip-missing-interpreters option is used
  - by @r2dan (`#672 <https://github.com/tox-dev/tox/issues/672>`_)
- avoid ``Requested Python version (X.Y) not installed`` stderr output when a
  Python environment is looked up using the ``py`` Python launcher on Windows
  and the environment is not found installed on the system - by
  @jurko-gospodnetic (`#692 <https://github.com/tox-dev/tox/issues/692>`_)
- Fixed an issue where invocation of tox from the Python package, where
  invocation errors (failed actions) occur results in a change in the
  sys.stdout stream encoding in Python 3.x. New behaviour is that sys.stdout is
  reset back to its original encoding after invocation errors - by @tonybaloney
  (`#723 <https://github.com/tox-dev/tox/issues/723>`_)
- The reading of command output sometimes failed with ``IOError: [Errno 0]
  Error`` on Windows, this was fixed by using a simpler method to update the
  read buffers. - by @fschulze (`#727
  <https://github.com/tox-dev/tox/issues/727>`_)
- (only affected rc releases) fix up tox.cmdline to be callable without args - by
  @gaborbernat. (`#773 <https://github.com/tox-dev/tox/issues/773>`_)
- (only affected rc releases) Revert breaking change of tox.cmdline not callable
  with no args - by @gaborbernat. (`#773 <https://github.com/tox-dev/tox/issues/773>`_)
- (only affected rc releases) fix #755 by reverting the ``cmdline`` import to the old
  location and changing the entry point instead - by @fschulze
  (`#755 <https://github.com/tox-dev/tox/issues/755>`_)


Features
^^^^^^^^

- ``tox`` displays exit code together with ``InvocationError`` - by @blueyed
  and @ederag. (`#290 <https://github.com/tox-dev/tox/issues/290>`_)
- Hint for possible signal upon ``InvocationError``, on posix systems - by
  @ederag and @asottile. (`#766 <https://github.com/tox-dev/tox/issues/766>`_)
- Add a ``-q`` option to progressively silence tox's output. For each time you
  specify ``-q`` to tox, the output provided by tox reduces. This option allows
  you to see only your command output without the default verbosity of what tox
  is doing. This also counter-acts usage of ``-v``. For example, running ``tox
  -v -q ...`` will provide you with the default verbosity. ``tox -vv -q`` is
  equivalent to ``tox -v``. By @sigmavirus24 (`#256
  <https://github.com/tox-dev/tox/issues/256>`_)
- add support for negated factor conditions, e.g. ``!dev: production_log`` - by
  @jurko-gospodnetic (`#292 <https://github.com/tox-dev/tox/issues/292>`_)
- Headings like ``installed: <packages>`` will not be printed if there is no
  output to display after the :, unless verbosity is set. By @cryvate (`#601
  <https://github.com/tox-dev/tox/issues/601>`_)
- Allow spaces in command line options to pip in deps. Where previously only
  ``deps=-rreq.txt`` and ``deps=--requirement=req.txt`` worked, now also
  ``deps=-r req.txt`` and ``deps=--requirement req.txt`` work - by @cryvate
  (`#668 <https://github.com/tox-dev/tox/issues/668>`_)
- drop Python ``2.6`` and ``3.3`` support: ``setuptools`` dropped supporting
  these, and as we depend on it we'll follow up with doing the same (use ``tox
  <= 2.9.1`` if you still need this support) - by @gaborbernat (`#679
  <https://github.com/tox-dev/tox/issues/679>`_)
- Add tox_runenvreport as a possible plugin, allowing the overriding of the
  default behaviour to execute a command to get the installed packages within a
  virtual environment - by @tonybaloney (`#725
  <https://github.com/tox-dev/tox/issues/725>`_)
- Forward ``PROCESSOR_ARCHITECTURE`` by default on Windows to fix
  ``platform.machine()``. (`#740 <https://github.com/tox-dev/tox/issues/740>`_)


Documentation
^^^^^^^^^^^^^

- Change favicon to the vector beach ball - by @hazalozturk
  (`#748 <https://github.com/tox-dev/tox/issues/748>`_)
- Change sphinx theme to alabaster and add logo/favicon - by @hazalozturk
  (`#639 <https://github.com/tox-dev/tox/issues/639>`_)


Miscellaneous
^^^^^^^^^^^^^

- Running ``tox`` without a ``setup.py`` now has a more friendly error message
  and gives troubleshooting suggestions - by @Volcyy.
  (`#331 <https://github.com/tox-dev/tox/issues/331>`_)
- Fix pycodestyle (formerly pep8) errors E741 (ambiguous variable names, in
  this case, 'l's) and remove ignore of this error in tox.ini - by @cryvate
  (`#663 <https://github.com/tox-dev/tox/issues/663>`_)
- touched up ``interpreters.py`` code and added some missing tests for it - by
  @jurko-gospodnetic (`#708 <https://github.com/tox-dev/tox/issues/708>`_)
- The ``PYTHONDONTWRITEBYTECODE`` environment variable is no longer unset - by
  @stephenfin. (`#744 <https://github.com/tox-dev/tox/issues/744>`_)


v2.9.1 (2017-09-29)
-------------------

Miscellaneous
^^^^^^^^^^^^^

- integrated new release process and fixed changelog rendering for pypi.org -
  by `@obestwalter <https://github.com/obestwalter>`_.


v2.9.0 (2017-09-29)
-------------------

Features
^^^^^^^^

- ``tox --version`` now shows information about all registered plugins - by
  `@obestwalter <https://github.com/obestwalter>`_
  (`#544 <https://github.com/tox-dev/tox/issues/544>`_)


Bugfixes
^^^^^^^^

- ``skip_install`` overrides ``usedevelop`` (``usedevelop`` is an option to
  choose the installation type if the package is installed and ``skip_install``
  determines if it should be installed at all) - by `@ferdonline <https://github.com/ferdonline>`_
  (`#571 <https://github.com/tox-dev/tox/issues/571>`_)


Miscellaneous
^^^^^^^^^^^^^

- `#635 <https://github.com/tox-dev/tox/issues/635>`_ inherit from correct exception -
  by `@obestwalter <https://github.com/obestwalter>`_
  (`#635 <https://github.com/tox-dev/tox/issues/635>`_).
- spelling  and escape sequence fixes - by `@scoop <https://github.com/scoop>`_
  (`#637 <https://github.com/tox-dev/tox/issues/637>`_ and
  `#638 <https://github.com/tox-dev/tox/issues/638>`_).
- add a badge to show build status of documentation on readthedocs.io -
  by `@obestwalter <https://github.com/obestwalter>`_.


Documentation
^^^^^^^^^^^^^

- add `towncrier <https://github.com/hawkowl/towncrier>`_ to allow adding
  changelog entries with the pull requests without generating merge conflicts;
  with this release notes are now grouped into four distinct collections:
  ``Features``, ``Bugfixes``, ``Improved Documentation`` and ``Deprecations and
  Removals``. (`#614 <https://github.com/tox-dev/tox/issues/614>`_)


v2.8.2 (2017-10-09)
-------------------

- `#466 <https://github.com/tox-dev/tox/issues/466>`_: stop env var leakage if popen failed with resultjson or redirect

v2.8.1 (2017-09-04)
-------------------

- `pull request 599 <https://github.com/tox-dev/tox/pull/599>`_: fix problems with implementation of `#515 <https://github.com/tox-dev/tox/issues/515>`_.
  Substitutions from other sections were not made anymore if they were not in ``envlist``.
  Thanks to Clark Boylan (`@cboylan <https://github.com/cboylan>`_) for helping to get this fixed (`pull request 597 <https://github.com/tox-dev/tox/pull/597>`_).

v2.8.0 (2017-09-01)
--------------------

- `#276 <https://github.com/tox-dev/tox/issues/276>`_: Remove easy_install from docs (TL;DR: use pip). Thanks Martin Andrysík (`@sifuraz <https://github.com/sifuraz>`_).

- `#301 <https://github.com/tox-dev/tox/issues/301>`_: Expand nested substitutions in ``tox.ini``. Thanks `@vlaci <https://github.com/vlaci>`_. Thanks to Eli Collins
  (`@eli-collins <https://github.com/eli-collins>`_) for creating a reproducer.

- `#315 <https://github.com/tox-dev/tox/issues/315>`_: add ``--help`` and ``--version`` to helptox-quickstart. Thanks `@vlaci <https://github.com/vlaci>`_.

- `#326 <https://github.com/tox-dev/tox/issues/326>`_: Fix ``OSError`` 'Not a directory' when creating env on Jython 2.7.0. Thanks Nick Douma (`@LordGaav <https://github.com/LordGaav>`_).

- `#429 <https://github.com/tox-dev/tox/issues/429>`_: Forward ``MSYSTEM`` by default on Windows. Thanks Marius Gedminas (`@mgedmin <https://github.com/mgedmin>`_) for reporting this.

- `#449 <https://github.com/tox-dev/tox/issues/449>`_: add multi platform example to the docs. Thanks Aleks Bunin (`@sashkab <https://github.com/sashkab>`_) and `@rndr <https://github.com/rndr>`_.

- `#474 <https://github.com/tox-dev/tox/issues/474>`_: Start using setuptools_scm for tag based versioning.

- `#484 <https://github.com/tox-dev/tox/issues/484>`_: Renamed ``py.test`` to ``pytest`` throughout the project. Thanks Slam (`@3lnc <https://github.com/3lnc>`_).

- `#504 <https://github.com/tox-dev/tox/issues/504>`_: With ``-a``: do not show additional environments header if there are none. Thanks `@rndr <https://github.com/rndr>`_.

- `#515 <https://github.com/tox-dev/tox/issues/515>`_: Don't require environment variables in test environments where they are not used.
  Thanks André Caron (`@AndreLouisCaron <https://github.com/AndreLouisCaron>`_).
- `#517 <https://github.com/tox-dev/tox/issues/517>`_: Forward ``NUMBER_OF_PROCESSORS`` by default on Windows to fix ``multiprocessor.cpu_count()``.
  Thanks André Caron (`@AndreLouisCaron <https://github.com/AndreLouisCaron>`_).

- `#518 <https://github.com/tox-dev/tox/issues/518>`_: Forward ``USERPROFILE`` by default on Windows. Thanks André Caron (`@AndreLouisCaron <https://github.com/AndreLouisCaron>`_).

- `pull request 528 <https://github.com/tox-dev/tox/pull/528>`_: Fix some of the warnings displayed by pytest 3.1.0. Thanks Bruno Oliveira (`@nicoddemus <https://github.com/nicoddemus>`_).

- `pull request 547 <https://github.com/tox-dev/tox/pull/547>`_: Add regression test for `#137 <https://github.com/tox-dev/tox/issues/137>`_. Thanks Martin Andrysík (`@sifuraz <https://github.com/sifuraz>`_).

- `pull request 553 <https://github.com/tox-dev/tox/pull/553>`_: Add an XFAIL test to reproduce upstream bug `#203 <https://github.com/tox-dev/tox/issues/203>`_. Thanks
  Bartolomé Sánchez Salado (`@bartsanchez <https://github.com/bartsanchez>`_).

- `pull request 556 <https://github.com/tox-dev/tox/pull/556>`_: Report more meaningful errors on why virtualenv creation failed. Thanks `@vlaci <https://github.com/vlaci>`_.
  Also thanks to Igor Sadchenko (`@igor-sadchenko <https://github.com/igor-sadchenko>`_) for pointing out a problem with that PR
  before it hit the masses ☺

- `pull request 575 <https://github.com/tox-dev/tox/pull/575>`_: Add announcement doc to end all announcement docs
  (using only ``CHANGELOG`` and Github issues since 2.5 already).

- `pull request 580 <https://github.com/tox-dev/tox/pull/580>`_: Do not ignore Sphinx warnings anymore. Thanks Bernát Gábor (`@gaborbernat <https://github.com/gaborbernat>`_).

- `pull request 585 <https://github.com/tox-dev/tox/pull/585>`_: Expand documentation to explain pass through of flags from deps to pip
  (e.g. ``-rrequirements.txt``, ``-cconstraints.txt``). Thanks Alexander Loechel (`@loechel <https://github.com/loechel>`_).

- `pull request 588 <https://github.com/tox-dev/tox/pull/588>`_: Run pytest wit xfail_strict and adapt affected tests.

v2.7.0 (2017-04-02)
-------------------

- `pull request 450 <https://github.com/tox-dev/tox/pull/450>`_: Stop after the first installdeps and first testenv create hooks
  succeed. This changes the default behaviour of ``tox_testenv_create`` and ``tox_testenv_install_deps`` to not execute other registered hooks when the first hook returns a result that is not ``None``.
  Thanks Anthony Sottile (`@asottile <https://github.com/asottile>`_).

- `#271 <https://github.com/tox-dev/tox/issues/271>`_ and `#464 <https://github.com/tox-dev/tox/issues/464>`_:
  Improve environment information for users.

  New command line parameter: ``-a`` show **all** defined environments -
  not just the ones defined in (or generated from) envlist.

  New verbosity settings for ``-l`` and ``-a``: show user defined descriptions
  of the environments. This also works for generated environments from factors
  by concatenating factor descriptions into a complete description.

  Note that for backwards compatibility with scripts using the output of ``-l``
  it's output remains unchanged.

  Thanks Bernát Gábor (`@gaborbernat <https://github.com/gaborbernat>`_).

- `#464 <https://github.com/tox-dev/tox/issues/464>`_: Fix incorrect egg-info location for modified package_dir in setup.py.
  Thanks Selim Belhaouane (`@selimb <https://github.com/selimb>`_).

- `#431 <https://github.com/tox-dev/tox/issues/431>`_: Add 'LANGUAGE' to default passed environment variables.
  Thanks Paweł Adamczak (`@pawelad <https://github.com/pawelad>`_).

- `#455 <https://github.com/tox-dev/tox/issues/455>`_: Add a Vagrantfile with a customized Arch Linux box for local testing.
  Thanks Oliver Bestwalter (`@obestwalter <https://github.com/obestwalter>`_).

- `#454 <https://github.com/tox-dev/tox/issues/454>`_: Revert `pull request 407 <https://github.com/tox-dev/tox/pull/407>`_, empty commands is not treated as an error.
  Thanks Anthony Sottile (`@asottile <https://github.com/asottile>`_).

- `#446 <https://github.com/tox-dev/tox/issues/446>`_: (infrastructure) Travis CI tests for tox now also run on OS X now.
  Thanks Jason R. Coombs (`@jaraco <https://github.com/jaraco>`_).

v2.6.0 (2017-02-04)
-------------------

- add "alwayscopy" config option to instruct virtualenv to always copy
  files instead of symlinking. Thanks Igor Duarte Cardoso (`@igordcard <https://github.com/igordcard>`_).

- pass setenv variables to setup.py during a usedevelop install.
  Thanks Eli Collins (`@eli-collins <https://github.com/eli-collins>`_).

- replace all references to testrun.org with readthedocs ones.
  Thanks Oliver Bestwalter (`@obestwalter <https://github.com/obestwalter>`_).

- fix `#323 <https://github.com/tox-dev/tox/issues/323>`_ by avoiding virtualenv14 is not used on py32
  (although we don't officially support py32).
  Thanks Jason R. Coombs (`@jaraco <https://github.com/jaraco>`_).

- add Python 3.6 to envlist and CI.
  Thanks Andrii Soldatenko (`@andriisoldatenko <https://github.com/andriisoldatenko>`_).

- fix glob resolution from TOX_TESTENV_PASSENV env variable
  Thanks Allan Feldman (`@a-feld <https://github.com/a-feld>`_).

v2.5.0 (2016-11-16)
-------------------

- slightly backward incompatible: fix `#310 <https://github.com/tox-dev/tox/issues/310>`_: the {posargs} substitution
  now properly preserves the tox command line positional arguments. Positional
  arguments with spaces are now properly handled.
  NOTE: if your tox invocation previously used extra quoting for positional arguments to
  work around `#310 <https://github.com/tox-dev/tox/issues/310>`_, you need to remove the quoting. Example:
  tox -- "'some string'"  # has to now be written simply as
  tox -- "some string"
  thanks holger krekel.  You can set ``minversion = 2.5.0`` in the ``[tox]``
  section of ``tox.ini`` to make sure people using your tox.ini use the correct version.

- fix `#359 <https://github.com/tox-dev/tox/issues/359>`_: add COMSPEC to default passenv on windows.  Thanks
  `@anthrotype <https://github.com/anthrotype>`_.

- add support for py36 and py37 and add py36-dev and py37(nightly) to
  travis builds of tox. Thanks John Vandenberg.

- fix `#348 <https://github.com/tox-dev/tox/issues/348>`_: add py2 and py3 as default environments pointing to
  "python2" and "python3" basepython executables.  Also fix `#347 <https://github.com/tox-dev/tox/issues/347>`_ by
  updating the list of default envs in the tox basic example.
  Thanks Tobias McNulty.

- make "-h" and "--help-ini" options work even if there is no tox.ini,
  thanks holger krekel.

- add {:} substitution, which is replaced with os-specific path
  separator, thanks Lukasz Rogalski.

- fix `#305 <https://github.com/tox-dev/tox/issues/305>`_: ``downloadcache`` test env config is now ignored as pip-8
  does caching by default. Thanks holger krekel.

- output from install command in verbose (-vv) mode is now printed to console instead of
  being redirected to file, thanks Lukasz Rogalski

- fix `#399 <https://github.com/tox-dev/tox/issues/399>`_.  Make sure {envtmpdir} is created if it doesn't exist at the
  start of a testenvironment run. Thanks Manuel Jacob.

- fix `#316 <https://github.com/tox-dev/tox/issues/316>`_: Lack of commands key in ini file is now treated as an error.
  Reported virtualenv status is 'nothing to do' instead of 'commands
  succeeded', with relevant error message displayed. Thanks Lukasz Rogalski.

v2.4.1 (2016-10-12)
-------------------

- fix `#380 <https://github.com/tox-dev/tox/issues/380>`_: properly perform substitution again. Thanks Ian
  Cordasco.

v2.4.0 (2016-10-12)
-------------------

- remove PYTHONPATH from environment during the install phase because a
  tox-run should not have hidden dependencies and the test commands will also
  not see a PYTHONPATH.  If this causes unforeseen problems it may be
  reverted in a bugfix release.  Thanks Jason R. Coombs.

- fix `#352 <https://github.com/tox-dev/tox/issues/352>`_: prevent a configuration where envdir==toxinidir and
  refine docs to warn people about changing "envdir". Thanks Oliver Bestwalter and holger krekel.

- fix `#375 <https://github.com/tox-dev/tox/issues/375>`_, fix `#330 <https://github.com/tox-dev/tox/issues/330>`_: warn against tox-setup.py integration as
  "setup.py test" should really just test with the current interpreter. Thanks Ronny Pfannschmidt.

- fix `#302 <https://github.com/tox-dev/tox/issues/302>`_: allow cross-testenv substitution where we substitute
  with ``{x,y}`` generative syntax.  Thanks Andrew Pashkin.

- fix `#212 <https://github.com/tox-dev/tox/issues/212>`_: allow escaping curly brace chars "\{" and "\}" if you need the
  chars "{" and "}" to appear in your commands or other ini values.
  Thanks John Vandenberg.

- addresses `#66 <https://github.com/tox-dev/tox/issues/66>`_: add --workdir option to override where tox stores its ".tox" directory
  and all of the virtualenv environment.  Thanks Danring.

- introduce per-venv list_dependencies_command which defaults
  to "pip freeze" to obtain the list of installed packages.
  Thanks Ted Shaw, Holger Krekel.

- close `#66 <https://github.com/tox-dev/tox/issues/66>`_: add documentation to jenkins page on how to avoid
  "too long shebang" lines when calling pip from tox.  Note that we
  can not use "python -m pip install X" by default because the latter
  adds the CWD and pip will think X is installed if it is there.
  "pip install X" does not do that.

- new list_dependencies_command to influence how tox determines
  which dependencies are installed in a testenv.

- (experimental) New feature: When a search for a config file fails, tox tries loading
  setup.cfg with a section prefix of "tox".

- fix `#275 <https://github.com/tox-dev/tox/issues/275>`_: Introduce hooks ``tox_runtest_pre``` and
  ``tox_runtest_post`` which run before and after the tests of a venv,
  respectively. Thanks to Matthew Schinckel and itxaka serrano.

- fix `#317 <https://github.com/tox-dev/tox/issues/317>`_: evaluate minversion before tox config is parsed completely.
  Thanks Sachi King for the PR.

- added the "extras" environment option to specify the extras to use when doing the
  sdist or develop install. Contributed by Alex Grönholm.

- use pytest-catchlog instead of pytest-capturelog (latter is not
  maintained, uses deprecated pytest API)

v2.3.2 (2016-02-11)
-------------------

- fix `#314 <https://github.com/tox-dev/tox/issues/314>`_: fix command invocation with .py scripts on windows.

- fix `#279 <https://github.com/tox-dev/tox/issues/279>`_: allow cross-section substitution when the value contains
  posargs. Thanks Sachi King for the PR.

v2.3.1 (2015-12-14)
-------------------

- fix `#294 <https://github.com/tox-dev/tox/issues/294>`_: re-allow cross-section substitution for setenv.

v2.3.0 (2015-12-09)
-------------------

- DEPRECATE use of "indexservers" in tox.ini.  It complicates
  the internal code and it is recommended to rather use the
  devpi system for managing indexes for pip.

- fix `#285 <https://github.com/tox-dev/tox/issues/285>`_: make setenv processing fully lazy to fix regressions
  of tox-2.2.X and so that we can now have testenv attributes like
  "basepython" depend on environment variables that are set in
  a setenv section. Thanks Nelfin for some tests and initial
  work on a PR.

- allow "#" in commands.  This is slightly incompatible with commands
  sections that used a comment after a "\" line continuation.
  Thanks David Stanek for the PR.

- fix `#289 <https://github.com/tox-dev/tox/issues/289>`_: fix build_sphinx target, thanks Barry Warsaw.

- fix `#252 <https://github.com/tox-dev/tox/issues/252>`_: allow environment names with special characters.
  Thanks Julien Castets for initial PR and patience.

- introduce experimental tox_testenv_create(venv, action) and
  tox_testenv_install_deps(venv, action) hooks to allow
  plugins to do additional work on creation or installing
  deps.  These hooks are experimental mainly because of
  the involved "venv" and session objects whose current public
  API is not fully guaranteed.

- internal: push some optional object creation into tests because
  tox core doesn't need it.

v2.2.1 (2015-12-09)
-------------------

- fix bug where {envdir} substitution could not be used in setenv
  if that env value is then used in {basepython}. Thanks Florian Bruhin.

v2.2.0 (2015-11-11)
-------------------

- fix `#265 <https://github.com/tox-dev/tox/issues/265>`_ and add LD_LIBRARY_PATH to passenv on linux by default
  because otherwise the python interpreter might not start up in
  certain configurations (redhat software collections).  Thanks David Riddle.

- fix `#246 <https://github.com/tox-dev/tox/issues/246>`_: fix regression in config parsing by reordering
  such that {envbindir} can be used again in tox.ini. Thanks Olli Walsh.

- fix `#99 <https://github.com/tox-dev/tox/issues/99>`_: the {env:...} substitution now properly uses environment
  settings from the ``setenv`` section. Thanks Itxaka Serrano.

- fix `#281 <https://github.com/tox-dev/tox/issues/281>`_: make --force-dep work when urls are present in
  dependency configs.  Thanks Glyph Lefkowitz for reporting.

- fix `#174 <https://github.com/tox-dev/tox/issues/174>`_: add new ``ignore_outcome`` testenv attribute which
  can be set to True in which case it will produce a warning instead
  of an error on a failed testenv command outcome.
  Thanks Rebecka Gulliksson for the PR.

- fix `#280 <https://github.com/tox-dev/tox/issues/280>`_: properly skip missing interpreter if
  {envsitepackagesdir} is present in commands. Thanks BB:ceridwenv


v2.1.1 (2015-06-23)
-------------------

- fix platform skipping for detox

- report skipped platforms as skips in the summary

v2.1.0 (2015-06-19)
-------------------

- fix `#258 <https://github.com/tox-dev/tox/issues/258>`_, fix `#248 <https://github.com/tox-dev/tox/issues/248>`_, fix `#253 <https://github.com/tox-dev/tox/issues/253>`_: for non-test commands
  (installation, venv creation) we pass in the full invocation environment.

- remove experimental --set-home option which was hardly used and
  hackily implemented (if people want home-directory isolation we should
  figure out a better way to do it, possibly through a plugin)

- fix `#259 <https://github.com/tox-dev/tox/issues/259>`_: passenv is now a line-list which allows to intersperse
  comments.  Thanks stefano-m.

- allow envlist to be a multi-line list, to intersperse comments
  and have long envlist settings split more naturally.  Thanks Andre Caron.

- introduce a TOX_TESTENV_PASSENV setting which is honored
  when constructing the set of environment variables for test environments.
  Thanks Marc Abramowitz for pushing in this direction.

v2.0.2 (2015-06-03)
-------------------

- fix `#247 <https://github.com/tox-dev/tox/issues/247>`_: tox now passes the LANG variable from the tox invocation
  environment to the test environment by default.

- add SYSTEMDRIVE into default passenv on windows to allow pip6 to work.
  Thanks Michael Krause.

v2.0.1 (2015-05-13)
-------------------

- fix wheel packaging to properly require argparse on py26.

v2.0.0 (2015-05-12)
-------------------

- (new) introduce environment variable isolation:
  tox now only passes the PATH and PIP_INDEX_URL variable from the tox
  invocation environment to the test environment and on Windows
  also ``SYSTEMROOT``, ``PATHEXT``, ``TEMP`` and ``TMP`` whereas
  on unix additionally ``TMPDIR`` is passed.  If you need to pass
  through further environment variables you can use the new ``passenv`` setting,
  a space-separated list of environment variable names.  Each name
  can make use of fnmatch-style glob patterns.  All environment
  variables which exist in the tox-invocation environment will be copied
  to the test environment.

- a new ``--help-ini`` option shows all possible testenv settings and
  their defaults.

- (new) introduce a way to specify on which platform a testenvironment is to
  execute: the new per-venv "platform" setting allows to specify
  a regular expression which is matched against sys.platform.
  If platform is set and doesn't match the platform spec in the test
  environment the test environment is ignored, no setup or tests are attempted.

- (new) add per-venv "ignore_errors" setting, which defaults to False.
   If ``True``, a non-zero exit code from one command will be ignored and
   further commands will be executed (which was the default behavior in tox <
   2.0).  If ``False`` (the default), then a non-zero exit code from one command
   will abort execution of commands for that environment.

- show and store in json the version dependency information for each venv

- remove the long-deprecated "distribute" option as it has no effect these days.

- fix `#233 <https://github.com/tox-dev/tox/issues/233>`_: avoid hanging with tox-setuptools integration example. Thanks simonb.

- fix `#120 <https://github.com/tox-dev/tox/issues/120>`_: allow substitution for the commands section.  Thanks
  Volodymyr Vitvitski.

- fix `#235 <https://github.com/tox-dev/tox/issues/235>`_: fix AttributeError with --installpkg.  Thanks
  Volodymyr Vitvitski.

- tox has now somewhat pep8 clean code, thanks to Volodymyr Vitvitski.

- fix `#240 <https://github.com/tox-dev/tox/issues/240>`_: allow to specify empty argument list without it being
  rewritten to ".".  Thanks Daniel Hahler.

- introduce experimental (not much documented yet) plugin system
  based on pytest's externalized "pluggy" system.
  See tox/hookspecs.py for the current hooks.

- introduce parser.add_testenv_attribute() to register an ini-variable
  for testenv sections.  Can be used from plugins through the
  tox_add_option hook.

- rename internal files -- tox offers no external API except for the
  experimental plugin hooks, use tox internals at your own risk.

- DEPRECATE distshare in documentation

v1.9.2 (2015-03-23)
-------------------

- backout ability that --force-dep substitutes name/versions in
  requirement files due to various issues.
  This fixes `#228 <https://github.com/tox-dev/tox/issues/228>`_, fixes `#230 <https://github.com/tox-dev/tox/issues/230>`_, fixes `#231 <https://github.com/tox-dev/tox/issues/231>`_
  which popped up with 1.9.1.

v1.9.1 (2015-03-23)
-------------------

- use a file instead of a pipe for command output in "--result-json".
  Fixes some termination issues with python2.6.

- allow --force-dep to override dependencies in "-r" requirements
  files.  Thanks Sontek for the PR.

- fix `#227 <https://github.com/tox-dev/tox/issues/227>`_: use "-m virtualenv" instead of "-mvirtualenv" to make
  it work with pyrun.  Thanks Marc-Andre Lemburg.


v1.9.0 (2015-02-24)
-------------------

- fix `#193 <https://github.com/tox-dev/tox/issues/193>`_: Remove ``--pre`` from the default ``install_command``; by
  default tox will now only install final releases from PyPI for unpinned
  dependencies. Use ``pip_pre = true`` in a testenv or the ``--pre``
  command-line option to restore the previous behavior.

- fix `#199 <https://github.com/tox-dev/tox/issues/199>`_: fill resultlog structure ahead of virtualenv creation

- refine determination if we run from Jenkins, thanks Borge Lanes.

- echo output to stdout when ``--report-json`` is used

- fix `#11 <https://github.com/tox-dev/tox/issues/11>`_: add a ``skip_install`` per-testenv setting which
  prevents the installation of a package. Thanks Julian Krause.

- fix `#124 <https://github.com/tox-dev/tox/issues/124>`_: ignore command exit codes; when a command has a "-" prefix,
  tox will ignore the exit code of that command

- fix `#198 <https://github.com/tox-dev/tox/issues/198>`_: fix broken envlist settings, e.g. {py26,py27}{-lint,}

- fix `#191 <https://github.com/tox-dev/tox/issues/191>`_: lessen factor-use checks


v1.8.1 (2014-10-24)
-------------------

- fix `#190 <https://github.com/tox-dev/tox/issues/190>`_: allow setenv to be empty.

- allow escaping curly braces with "\".  Thanks Marc Abramowitz for the PR.

- allow "." names in environment names such that "py27-django1.7" is a
  valid environment name.  Thanks Alex Gaynor and Alex Schepanovski.

- report subprocess exit code when execution fails.  Thanks Marius
  Gedminas.

v1.8.0 (2014-09-24)
-------------------

- new multi-dimensional configuration support.  Many thanks to
  Alexander Schepanovski for the complete PR with docs.
  And to Mike Bayer and others for testing and feedback.

- fix `#148 <https://github.com/tox-dev/tox/issues/148>`_: remove "__PYVENV_LAUNCHER__" from os.environ when starting
  subprocesses. Thanks Steven Myint.

- fix `#152 <https://github.com/tox-dev/tox/issues/152>`_: set VIRTUAL_ENV when running test commands,
  thanks Florian Ludwig.

- better report if we can't get version_info from an interpreter
  executable. Thanks Floris Bruynooghe.


v1.7.2 (2014-07-15)
-------------------

- fix `#150 <https://github.com/tox-dev/tox/issues/150>`_: parse {posargs} more like we used to do it pre 1.7.0.
  The 1.7.0 behaviour broke a lot of OpenStack projects.
  See PR85 and the issue discussions for (far) more details, hopefully
  resulting in a more refined behaviour in the 1.8 series.
  And thanks to Clark Boylan for the PR.

- fix `#59 <https://github.com/tox-dev/tox/issues/59>`_: add a config variable ``skip-missing-interpreters`` as well as
  command line option ``--skip-missing-interpreters`` which won't fail the
  build if Python interpreters listed in tox.ini are missing.  Thanks
  Alexandre Conrad for PR104.

- fix `#164 <https://github.com/tox-dev/tox/issues/164>`_: better traceback info in case of failing test commands.
  Thanks Marc Abramowitz for PR92.

- support optional env variable substitution, thanks Morgan Fainberg
  for PR86.

- limit python hashseed to 1024 on Windows to prevent possible
  memory errors.  Thanks March Schlaich for the PR90.

v1.7.1 (2014-03-28)
-------------------

- fix `#162 <https://github.com/tox-dev/tox/issues/162>`_: don't list python 2.5 as compatible/supported

- fix `#158 <https://github.com/tox-dev/tox/issues/158>`_ and fix `#155 <https://github.com/tox-dev/tox/issues/155>`_: windows/virtualenv properly works now:
  call virtualenv through "python -m virtualenv" with the same
  interpreter which invoked tox.  Thanks Chris Withers, Ionel Maries Cristian.

v1.7.0 (2014-01-29)
-------------------

- don't lookup "pip-script" anymore but rather just "pip" on windows
  as this is a pip implementation detail and changed with pip-1.5.
  It might mean that tox-1.7 is not able to install a different pip
  version into a virtualenv anymore.

- drop Python2.5 compatibility because it became too hard due
  to the setuptools-2.0 dropping support.  tox now has no
  support for creating python2.5 based environments anymore
  and all internal special-handling has been removed.

- merged PR81: new option --force-dep which allows to
  override tox.ini specified dependencies in setuptools-style.
  For example "--force-dep 'django<1.6'" will make sure
  that any environment using "django" as a dependency will
  get the latest 1.5 release.  Thanks Bruno Oliveria for
  the complete PR.

- merged PR125: tox now sets "PYTHONHASHSEED" to a random value
  and offers a "--hashseed" option to repeat a test run with a specific seed.
  You can also use --hashsheed=noset to instruct tox to leave the value
  alone.  Thanks Chris Jerdonek for all the work behind this.

- fix `#132 <https://github.com/tox-dev/tox/issues/132>`_: removing zip_safe setting (so it defaults to false)
  to allow installation of tox
  via easy_install/eggs.  Thanks Jenisys.

- fix `#126 <https://github.com/tox-dev/tox/issues/126>`_: depend on virtualenv>=1.11.2 so that we can rely
  (hopefully) on a pip version which supports --pre. (tox by default
  uses to --pre).  also merged in PR84 so that we now call "virtualenv"
  directly instead of looking up interpreters.  Thanks Ionel Maries Cristian.
  This also fixes `#140 <https://github.com/tox-dev/tox/issues/140>`_.

- fix `#130 <https://github.com/tox-dev/tox/issues/130>`_: you can now set install_command=easy_install {opts} {packages}
  and expect it to work for repeated tox runs (previously it only worked
  when always recreating).  Thanks jenisys for precise reporting.

- fix `#129 <https://github.com/tox-dev/tox/issues/129>`_: tox now uses Popen(..., universal_newlines=True) to force
  creation of unicode stdout/stderr streams.  fixes a problem on specific
  platform configs when creating virtualenvs with Python3.3. Thanks
  Jorgen Schäfer or investigation and solution sketch.

- fix `#128 <https://github.com/tox-dev/tox/issues/128>`_: enable full substitution in install_command,
  thanks for the PR to Ronald Evers

- rework and simplify "commands" parsing and in particular posargs
  substitutions to avoid various win32/posix related quoting issues.

- make sure that the --installpkg option trumps any usedevelop settings
  in tox.ini or

- introduce --no-network to tox's own test suite to skip tests
  requiring networks

- introduce --sitepackages to force sitepackages=True in all
  environments.

- fix `#105 <https://github.com/tox-dev/tox/issues/105>`_ -- don't depend on an existing HOME directory from tox tests.

v1.6.1 (2013-09-04)
-------------------

- fix `#119 <https://github.com/tox-dev/tox/issues/119>`_: {envsitepackagesdir} is now correctly computed and has
  a better test to prevent regression.

- fix `#116 <https://github.com/tox-dev/tox/issues/116>`_: make 1.6 introduced behaviour of changing to a
  per-env HOME directory during install activities dependent
  on "--set-home" for now.  Should re-establish the old behaviour
  when no option is given.

- fix `#118 <https://github.com/tox-dev/tox/issues/118>`_: correctly have two tests use realpath(). Thanks Barry
  Warsaw.

- fix test runs on environments without a home directory
  (in this case we use toxinidir as the homedir)

- fix `#117 <https://github.com/tox-dev/tox/issues/117>`_: python2.5 fix: don't use ``--insecure`` option because
  its very existence depends on presence of "ssl".  If you
  want to support python2.5/pip1.3.1 based test environments you need
  to install ssl and/or use PIP_INSECURE=1 through ``setenv``. section.

- fix `#102 <https://github.com/tox-dev/tox/issues/102>`_: change to {toxinidir} when installing dependencies.
  this allows to use relative path like in "-rrequirements.txt".

v1.6.0 (2013-08-15)
-------------------

- fix `#35 <https://github.com/tox-dev/tox/issues/35>`_: add new EXPERIMENTAL "install_command" testenv-option to
  configure the installation command with options for dep/pkg install.
  Thanks Carl Meyer for the PR and docs.

- fix `#91 <https://github.com/tox-dev/tox/issues/91>`_: python2.5 support by vendoring the virtualenv-1.9.1
  script and forcing pip<1.4. Also the default [py25] environment
  modifies the default installer_command (new config option)
  to use pip without the "--pre" option which was introduced
  with pip-1.4 and is now required if you want to install non-stable
  releases.  (tox defaults to install with "--pre" everywhere).

- during installation of dependencies HOME is now set to a pseudo
  location ({envtmpdir}/pseudo-home).  If an index url was specified
  a .pydistutils.cfg file will be written with an index_url setting
  so that packages defining ``setup_requires`` dependencies will not
  silently use your HOME-directory settings or PyPi.

- fix `#1 <https://github.com/tox-dev/tox/issues/1>`_: empty setup files are properly detected, thanks Anthon van
  der Neuth

- remove toxbootstrap.py for now because it is broken.

- fix `#109 <https://github.com/tox-dev/tox/issues/109>`_ and fix `#111 <https://github.com/tox-dev/tox/issues/111>`_: multiple "-e" options are now combined
  (previously the last one would win). Thanks Anthon van der Neut.

- add --result-json option to write out detailed per-venv information
  into a json report file to be used by upstream tools.

- add new config options ``usedevelop`` and ``skipsdist`` as well as a
  command line option ``--develop`` to install the package-under-test in develop mode.
  thanks Monty Tailor for the PR.

- always unset PYTHONDONTWRITEBYTE because newer setuptools doesn't like it

- if a HOMEDIR cannot be determined, use the toxinidir.

- refactor interpreter information detection to live in new
  tox/interpreters.py file, tests in tests/test_interpreters.py.

v1.5.0 (2013-06-22)
-------------------

- fix `#104 <https://github.com/tox-dev/tox/issues/104>`_: use setuptools by default, instead of distribute,
  now that setuptools has distribute merged.

- make sure test commands are searched first in the virtualenv

- re-fix `#2 <https://github.com/tox-dev/tox/issues/2>`_ - add whitelist_externals to be used in ``[testenv*]``
  sections, allowing to avoid warnings for commands such as ``make``,
  used from the commands value.

- fix `#97 <https://github.com/tox-dev/tox/issues/97>`_ - allow substitutions to reference from other sections
  (thanks Krisztian Fekete)

- fix `#92 <https://github.com/tox-dev/tox/issues/92>`_ - fix {envsitepackagesdir} to actually work again

- show (test) command that is being executed, thanks
  Lukasz Balcerzak

- re-license tox to MIT license

- depend on virtualenv-1.9.1

- rename README.txt to README.rst to make bitbucket happier


v1.4.3 (2013-02-28)
-------------------

- use pip-script.py instead of pip.exe on win32 to avoid the lock exe
  file on execution issue (thanks Philip Thiem)

- introduce -l|--listenv option to list configured environments
  (thanks  Lukasz Balcerzak)

- fix downloadcache determination to work according to docs: Only
  make pip use a download cache if PIP_DOWNLOAD_CACHE or a
  downloadcache=PATH testenv setting is present. (The ENV setting
  takes precedence)

- fix `#84 <https://github.com/tox-dev/tox/issues/84>`_ - pypy on windows creates a bin not a scripts venv directory
  (thanks Lukasz Balcerzak)

- experimentally introduce --installpkg=PATH option to install a package
  rather than create/install an sdist package.  This will still require
  and use tox.ini and tests from the current working dir (and not from the
  remote package).

- substitute {envsitepackagesdir} with the package installation
  directory (closes `#72 <https://github.com/tox-dev/tox/issues/72>`_) (thanks g2p)

- issue `#70 <https://github.com/tox-dev/tox/issues/70>`_ remove PYTHONDONTWRITEBYTECODE workaround now that
  virtualenv behaves properly (thanks g2p)

- merged tox-quickstart command, contributed by Marc Abramowitz, which
  generates a default tox.ini after asking a few questions

- fix `#48 <https://github.com/tox-dev/tox/issues/48>`_ - win32 detection of pypy and other interpreters that are on PATH
  (thanks Gustavo Picon)

- fix grouping of index servers, it is now done by name instead of
  indexserver url, allowing to use it to separate dependencies
  into groups even if using the same default indexserver.

- look for "tox.ini" files in parent dirs of current dir (closes `#34 <https://github.com/tox-dev/tox/issues/34>`_)

- the "py" environment now by default uses the current interpreter
  (sys.executable) make tox' own setup.py test execute tests with it
  (closes `#46 <https://github.com/tox-dev/tox/issues/46>`_)

- change tests to not rely on os.path.expanduser (closes `#60 <https://github.com/tox-dev/tox/issues/60>`_),
  also make mock session return args[1:] for more precise checking (closes `#61 <https://github.com/tox-dev/tox/issues/61>`_)
  thanks to Barry Warsaw for both.

v1.4.2 (2012-07-20)
-------------------

- fix some tests which fail if /tmp is a symlink to some other place
- "python setup.py test" now runs tox tests via tox :)
  also added an example on how to do it for your project.

v1.4.1 (2012-07-03)
-------------------

- fix `#41 <https://github.com/tox-dev/tox/issues/41>`_ better quoting on windows - you can now use "<" and ">" in
  deps specifications, thanks Chris Withers for reporting

v1.4 (2012-06-13)
-----------------

- fix `#26 <https://github.com/tox-dev/tox/issues/26>`_ - no warnings on absolute or relative specified paths for commands
- fix `#33 <https://github.com/tox-dev/tox/issues/33>`_ - commentchars are ignored in key-value settings allowing
  for specifying commands like: python -c "import sys ; print sys"
  which would formerly raise irritating errors because the ";"
  was considered a comment
- tweak and improve reporting
- refactor reporting and virtualenv manipulation
  to be more accessible from 3rd party tools
- support value substitution from other sections
  with the {[section]key} syntax
- fix `#29 <https://github.com/tox-dev/tox/issues/29>`_ - correctly point to pytest explanation
  for importing modules fully qualified
- fix `#32 <https://github.com/tox-dev/tox/issues/32>`_ - use --system-site-packages and don't pass --no-site-packages
- add python3.3 to the default env list, so early adopters can test
- drop python2.4 support (you can still have your tests run on
- fix the links/checkout howtos in the docs
  python-2.4, just tox itself requires 2.5 or higher.

v1.3 2011-12-21
---------------

- fix: allow to specify wildcard filesystem paths when
  specifying dependencies such that tox searches for
  the highest version

- fix issue `#21 <https://github.com/tox-dev/tox/issues/21>`_: clear PIP_REQUIRES_VIRTUALENV which avoids
  pip installing to the wrong environment, thanks to bb's streeter

- make the install step honour a testenv's setenv setting
  (thanks Ralf Schmitt)


v1.2 2011-11-10
---------------

- remove the virtualenv.py that was distributed with tox and depend
  on >=virtualenv-1.6.4 (possible now since the latter fixes a few bugs
  that the inlining tried to work around)
- fix `#10 <https://github.com/tox-dev/tox/issues/10>`_: work around UnicodeDecodeError when invoking pip (thanks
  Marc Abramowitz)
- fix a problem with parsing {posargs} in tox commands (spotted by goodwill)
- fix the warning check for commands to be installed in testenvironment
  (thanks Michael Foord for reporting)

v1.1 (2011-07-08)
-----------------

- fix `#5 <https://github.com/tox-dev/tox/issues/5>`_ - don't require argparse for python versions that have it
- fix `#6 <https://github.com/tox-dev/tox/issues/6>`_ - recreate virtualenv if installing dependencies failed
- fix `#3 <https://github.com/tox-dev/tox/issues/3>`_ - fix example on frontpage
- fix `#2 <https://github.com/tox-dev/tox/issues/2>`_ - warn if a test command does not come from the test
  environment
- fixed/enhanced: except for initial install always call "-U
  --no-deps" for installing the sdist package to ensure that a package
  gets upgraded even if its version number did not change. (reported on
  TIP mailing list and IRC)
- inline virtualenv.py (1.6.1) script to avoid a number of issues,
  particularly failing to install python3 environments from a python2
  virtualenv installation.
- rework and enhance docs for display on readthedocs.org

v1.0
----

- move repository and toxbootstrap links to https://bitbucket.org/hpk42/tox
- fix `#7 <https://github.com/tox-dev/tox/issues/7>`_: introduce a "minversion" directive such that tox
  bails out if it does not have the correct version.
- fix `#24 <https://github.com/tox-dev/tox/issues/24>`_: introduce a way to set environment variables for
  for test commands (thanks Chris Rose)
- fix `#22 <https://github.com/tox-dev/tox/issues/22>`_: require virtualenv-1.6.1, obsoleting virtualenv5 (thanks Jannis Leidel)
  and making things work with pypy-1.5 and python3 more seamlessly
- toxbootstrap.py (used by jenkins build slaves) now follows the latest release of virtualenv
- fix `#20 <https://github.com/tox-dev/tox/issues/20>`_: document format of URLs for specifying dependencies
- fix `#19 <https://github.com/tox-dev/tox/issues/19>`_: substitute Hudson for Jenkins everywhere following the renaming
  of the project.  NOTE: if you used the special [tox:hudson]
  section it will now need to be named [tox:jenkins].
- fix issue 23 / apply some ReST fixes
- change the positional argument specifier to use {posargs:} syntax and
  fix issues `#15 <https://github.com/tox-dev/tox/issues/15>`_ and `#10 <https://github.com/tox-dev/tox/issues/10>`_ by refining the argument parsing method (Chris Rose)
- remove use of inipkg lazy importing logic -
  the namespace/imports are anyway very small with tox.
- fix a fspath related assertion to work with debian installs which uses
  symlinks
- show path of the underlying virtualenv invocation and bootstrap
  virtualenv.py into a working subdir
- added a CONTRIBUTORS file

v0.9
----

- fix pip-installation mixups by always unsetting PIP_RESPECT_VIRTUALENV
  (thanks Armin Ronacher)
- `#1 <https://github.com/tox-dev/tox/issues/1>`_: Add a toxbootstrap.py script for tox, thanks to Sridhar
  Ratnakumar
- added support for working with different and multiple PyPI indexservers.
- new option: -r|--recreate to force recreation of virtualenv
- depend on py>=1.4.0 which does not contain or install the py.test
  anymore which is now a separate distribution "pytest".
- show logfile content if there is an error (makes CI output
  more readable)

v0.8
----

- work around a virtualenv limitation which crashes if
  PYTHONDONTWRITEBYTECODE is set.
- run pip/easy installs from the environment log directory, avoids
  naming clashes between env names and dependencies (thanks ronny)
- require a more recent version of py lib
- refactor and refine config detection to work from a single file
  and to detect the case where a python installation overwrote
  an old one and resulted in a new executable. This invalidates
  the existing virtualenvironment now.
- change all internal source to strip trailing whitespaces

v0.7
----

- use virtualenv5 (my own fork of virtualenv3) for now to create python3
  environments, fixes a couple of issues and makes tox more likely to
  work with Python3 (on non-windows environments)

- add ``sitepackages`` option for testenv sections so that environments
  can be created with access to globals (default is not to have access,
  i.e. create environments with ``--no-site-packages``.

- addressing `#4 <https://github.com/tox-dev/tox/issues/4>`_: always prepend venv-path to PATH variable when calling subprocesses

- fix `#2 <https://github.com/tox-dev/tox/issues/2>`_: exit with proper non-zero return code if there were
  errors or test failures.

- added unittest2 examples contributed by Michael Foord

- only allow 'True' or 'False' for boolean config values
  (lowercase / uppercase is irrelevant)

- recreate virtualenv on changed configurations

v0.6
----

- fix OSX related bugs that could cause the caller's environment to get
  screwed (sorry).  tox was using the same file as virtualenv for tracking
  the Python executable dependency and there also was confusion wrt links.
  this should be fixed now.

- fix long description, thanks Michael Foord

v0.5
----

- initial release