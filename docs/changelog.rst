==========
Change Log
==========

API and Python module versioning information.

List of features and available or target versions, organized by component.
(See :doc:`components`). Link to documentation in :doc:`reference` section and
appropriate content in :doc:`userguide` or :doc:`developerguide` if available.
Should be accompanied by appropriate annotations elsewhere in the docs.
I.e. See `Sphinx docs. <http://www.sphinx-doc.org/en/stable/markup/para.html>`_

.. rubric:: 0.0.6

Interface and feature updates

- Updates to :mod:`gmx.version` module
- Automatically set and restore from MD simulation checkpoints in the session working directory.
- Allow control of whether simulation output is appended or truncated
  (PR `#126 <https://github.com/kassonlab/gmxapi/pull/126>`_).
- Allow plugins to issue a stop signal to MD simulations
  (reference `#62 <https://github.com/kassonlab/gmxapi/issues/62>`_ for gromacs-gmxapi and sample_restraint repos).
- Changes to :mod:`gmx.exceptions`
- Allow full CMake-driven install
- Updated example notebooks in sample_restraint repository.

Internal

- Improved CI testing
- `#64 <https://github.com/kassonlab/gmxapi/issues/64>`_ Unique work spec identification.

Bug fixes

- `#66 <https://github.com/kassonlab/gmxapi/issues/66>`_ Docker does not access current gmxpy version.
- `#123 <https://github.com/kassonlab/gmxapi/issues/123>`_ Race condition in session closing.

.. rubric:: 0.0.5

- allow multiple MD restraints
- documentation updates
- provide Dockerfile for convenience
- continuous integration testing on Travis-CI

.. rubric:: 0.0.4

Initial beta with support for pluggable pair restraints
