=============
Python module
=============

Goal: Expose Gromacs functionality in an interface
that can be easily driven and interacted with from Python (or other
high-level languages) and external APIs for e.g. "cloud" computing resources,
container systems, work flow / compute resource managers. Data graph execution
at various levels may be deferred to other tools, but should at least be
facilitated by the design.

There are several broad classes of use cases that warrant different levels of access,
abstraction, performance, and invasiveness into the existing code.
In rough order of increasing invasiveness.

1. Post-run analysis (expose and extend the current C++ API, file readers, wrapped tools)
2. Preparation, preprocessing, and editing of simulation jobs or on-disk
   information (encapsulation of tools like `pdb2gmx`, `grompp`, `trajedit`,
   `solvate`, and of data structures such as topologies, `tpr` files,
   trajectories and checkpoints)
3. Controlling Gromacs code (scripting, managing compute resources, high level
   workflow and data flow, clearly separating model parameters / simulation
   parameters / workflow parameters / runtime parameters)
4. Live analysis (hooks into MD loop: callbacks, custom loggers)
5. Extension of Gromacs code (adding custom potentials, logic, low level
   workflow and data flow)

The APIs developed should allow access to these functionalities.
A user should be able to link together tasks programatically, dynamically, and
with no requirements of filesystem I/O.

Gromacs command-line functionality should be easily achieved in a Python script. This
does not mean that all CLI tools should be simply exposed to Python, since
many represent trivial data transformations, tasks that are well-handled by other
Python-based tools, or easily-scripted tasks. Common scriptable tasks can be
provided by high-level convenience functions or "bottled" workflows. More
elemental functionality that should be available through the C++ API can be
exposed through bindings to the Python API.

With Python's reference counting and conceptual differences about the mutability
of data, it is appropriate to support a few different modes of access to Gromacs
data structures in the C++ API. When it is necessary to copy data, explicit API syntax should
make it clear to the user. When possible, the Python buffer protocol can be used
to give Python direct access to memory shared by C++ data structures. In general,
though, for data and objects that exist only to be used in subsequent API calls,
proxy objects can be used at a high level with interactions between objects
deferred to the C++ library API where the parallelism framework and optimizations
already exist. This also helps with the notion that the Python interpreter
expects to "own" objects that are handed to it through bindings.

The design should emphasize idiomatic Python usage.

Other Python Gromacs interfaces
===============================

`gmxscript <https://github.com/pslacerda/gmx>`__ : all-python CLI
wrapper manages files, provides "checkpointed" re-runnable workflow with
mdp rewriting and CLI replacement

`GromacsWrapper <http://gromacswrapper.readthedocs.io/en/latest/>`__
(Beckstein) : all-python CLI wrapper provides thorough scriptable
interface with error handling

`pmx <https://github.com/dseeliger/pmx>`__ (D. Seeliger) : Manipulate
Gromacs files and data structures

`grompy <https://github.com/GromPy>`__ (Rene Pool) : patches old Gromacs
code to provide ctypes interface to Gromacs libraries

`gmx\_wrapper <https://github.com/khuston/gmx_wrapper>`__ : very
bare-bones CLI wrapper

`GromacsPipeline <https://biod.pnpi.spb.ru/gitweb/?p=alexxy/gromacs.git;a=commit;h=1241cd15da38bf7afd65a924100730b04e430475>`__
(Redmine 1625) adds SIP bindings to run Gromacs analysis modules in a
single pass on a trajectory

Existing Python tools to leverage
=================================

Some of the tools available from the ``gmx`` command-line interface
are bundled largely for convenience,
and Python users may be better served by turning to other projects
rather than relying on exposing
redundant functionality from ``libgromacs``.
In other cases, Gromacs developers may simply want to be aware of the
other tools and interoperability requirements.

-  `pypdb <https://github.com/williamgilpin/pypdb>`__ A Python API for
   the RCSB Protein Data Bank (PDB)
-  `chemlab <http://chemlab.github.io/chemlab/>`__ Qt-powered molecular
   viewer
-  `chemview <https://github.com/gabrielelanaro/chemview>`__ molecular
   viewer for IPython from the author of chemlab
-  `MDTraj <http://mdtraj.org/>`__ Read, write and analyze MD
   trajectories
-  `MDAnalysis <http://www.mdanalysis.org>`__ analyze trajectories from
   molecular dynamics
-  `Biopython <https://github.com/biopython/biopython>`__ tools for
   computational molecular biology
-  several packages named "Biopy" do not seem relevant
-  `PyMOL <http://www.pymol.org/>`__ molecular viewer
-  `mmLib <http://pymmlib.sourceforge.net/>`__ Python Macromolecular
   Library analysis, manipulation, and viewing
-  `msmbuilder <http://msmbuilder.org/>`__ Statistical models for
   Biomolecular Dynamics
-  `PyEMMA <http://emma-project.org/>`__ estimation, validation and
   analysis Markov models from molecular dynamics (MD) data
-  `ExSTACY <http://extasy-project.org>`__ Collection of tools that
   couples large ensemble Molecular Dynamics calculations with analysis
   to provide improvements in sampling. Includes
-  `EnsembleMD <https://github.com/radical-cybertools/radical.ensemblemd>`__
   framework for running ensemble workflows
-  `COCO <https://bitbucket.org/extasy-project/coco>`__
-  `LSDMap <https://sourceforge.net/projects/lsdmap/>`__ Locally Scaled
   Diffusion Maps
-  `pyPcazip <https://bitbucket.org/ramonbsc/pypcazip>`__ principle
   component analysis
-  `Plumed <http://www.plumed.org>`__ free energy calculation using
   collective variables
-  pdb tools
-  OpenMM
-  msmbuilder
-  BioXL

Naming
======

The package should have a name that does not collide with other known
projects, particularly any projects on pypi.
"gmx" is available on pypi, but similar existing package names include

-  ``gmx-script``
-  ``python-gmx``

``pygmx`` is the name used for a bindings package posted to the Gromacs
list that resides at https://biod. pnpi.spb.ru/~alexxy/pygmx

There are several packages named ``grompy``

The Beckstein lab has ``GromacsWrapper`` which exists on
PythonHosted.org, GitHub, and PyPi.

Python version
==============

Python 3.3 has a lot of improvements and changes, including better 2.7 compatibility

Python 3.4

* built-in pip
* enum types
* new pickle protocol

Python 3.5

* typing and coroutines
* RecursionError exception
* Generators have gi_yieldfrom
* memoryview tuple indexing
* hexadecimal methods

Linux distributions released after June 2013 and supported at least to June 2019.

| Ubuntu 14.04 (trusty): Python 3.4
| Ubuntu 16.04 (denial): Python 3.5
| Debian 8 (jessie): Python 3.4
| Debian 9 (stretch): Python 3.5
| Linux Mint 18 (rosa): 3.4
| Linux Mint 17 (sarah): 3.5
| Fedora 23: 3.4
| Fedora 24+: 3.5
| RHEL 7: n/a
| CentOS 7: n/a

Suggestion: Initially support Python 2.7 and 3.3, with both immediately
deprecated. Plan to require Python 3.4+ when Python 2.7 is end-of-lifed in 2020.
Python 3.4 is widely supported by distributions that, as of a projected public
release, will have been released less than five years ago and will be supported
for at least another year. Built-in `pip` and enum types would be very nice to
be able to rely on. If Python 2.7 is supported, then we should not support less
than Python 3.3, but it may be hard enough to take advantage of features in 3.4+
that there isn't a good reason to require it without motivation from a clear
roadmap of which versions Gromacs will support in the future.

Use cases and Scenarios
=======================

Traditional HPC scenarios
-------------------------
An incomplete list of some contemporary modes of scientific computing.

large-scale MPI job on HPC cluster
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Traditional large-scale MPI job on HPC cluster

|  1. User submits job requesting many resources (e.g. N m-core ranks)
|  2. Queuing system simultaneously launches N processes with access to MPI
    environments and interconnect libraries installed on the compute nodes
|  3. Each process uses its linked MPI library and runtime environment to access its MPI communicator
|  4. Each process runs a copy of the same object code with different(ly processed) input
|  5. Processes communicate through the MPI communicator as needed
|  6. Processes terminate individually (but hopefully simultaneously) and job
    completes, leaving results in a shared filesystem or first transferring data to a master rank

user-managed bag-of-jobs on HPC cluster
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Traditional independent bag-of-jobs on HPC cluster with shared storage

|  1. User submits many isolated job scripts
|  2. Queuing system executes job script on reserved resources as available.
|  3. Job script uses unique parameters, environment variables, and job ID to run a unique simulation asynchronously.
|  4. User retrieves and/or analyzes data

Alternate scenarios

|  1b. User submits a job script many times (or as a job array) with different parameters
|  4b. Queuing system job dependencies chain tasks (unreliable in practice) using shared filesystem

pilot-managed jobs on HPC cluster
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Traditional independent bag-of-jobs on pilot-managed HPC resource allocation

|  1. User submits pilot job
|  2. Queuing system notifies user when pilot job is running
|  3a. User submits bag-of-jobs to pilot manager
|  4a. Pilot manager transfers scripts and data and connects to compute nodes to execute tasks as resources are available in the pilot job

Alternate scenario: Managed ensemble workflow on pilot-managed HPC resource allocation

|  3b. User specifies workflow elements and parameters to ensemble manager
    (e.g. cross-correlate all permutations of trajectory pairs)
|  4b. Ensemble manager generates pilot work units using pilot interface
|  5. Pilot manager finds available resources, transfers data and connects to
    compute nodes to execute remote commands (which may invoke single processes,
    multi-threaded processes, or MPI process groups).
|  6. Ensemble manager collates results

commodity cloud computing
~~~~~~~~~~~~~~~~~~~~~~~~~
Various scenarious, depending on environment and user preferences, involving VMs
or Docker images. Avoiding expensive data storage costs can require a lot of
scripting and testing of chained tasks.

Target scenarios
----------------

Following are an assortment of tasks that we are targeting with the Python
interface and API design.

Programmatically launch many similar simulations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run -> analyze -> run chained tasks in pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Data graph execution
~~~~~~~~~~~~~~~~~~~~

Implement/adapt a new execution manager
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Scripted workflow
~~~~~~~~~~~~~~~~~

| 1. `Load pdb data`_
| 2. `Perform energy minimization`_
| 3. `Solvate`_
| 4. `Add ions`_
| 5. `Perform energy minimization`_
| 6. `Equilibration`_
|   6.1. NVT
|   6.2. NpT
| 7. Launch simulation job

Load pdb data
~~~~~~~~~~~~~

| 1. Read and clean pdb file
| 2. Get topology from factory using built-in force field definition
| 3. Produce ``.gro`` and ``.top`` files
| 4. `Set up a simulation box`_

Alternate: bottled workflow

| 1a. use a utility / helper function ``like pdb2gmx``
|   1.1. proceed to 4.

Alternate: non-automated topology construction

| 2a. Start from an empty or existing Topology object
|   2a.1. get or define a forcefield data structure
|   2a.2. use member functions of Topology object to modify

Set up a simulation box
~~~~~~~~~~~~~~~~~~~~~~~

| 1. Read a structure (``.gro``) file
| 2. Edit data
| 3. Write out new structure file

Alternate: no file I/O

| 1a. Create or reuse a AtomData object
|   1a.1 set box parameters for object
|   1a.2 done

Perform energy minimization
~~~~~~~~~~~~~~~~~~~~~~~~~~~

| 1. Create input record
|   1.1 Read mdp file
|   1.2 Save tpr file
| 2. Optionally configure filesystem output and then run.

Alternate: avoid redundant file I/O

| 1a. Operate on and produce Topology, Particles, and System objects without
   file I/O using previously generated objects and mdp file
| 2a. Run energy minimizer with no filesystem output

Alternate: skip mdp file

| 1b. Skip mdp file with granular functionality and parameters in Python data structures
|   1.1 set parameters for minimizer, general integrator, and neighborlist
|   1.2 create System and Context objects
|   1.3 create minimizer object and bind everything together

or

| 1c. reuse the last minimizer System and Context
|   1c.1. update minimizer parameters and configuration
|   1c.2. proceed to 2.

Alternate: additional logging

| 2d. Optionally configure Reporter objects before running

Solvate
~~~~~~~

1. Load solvent and coordinates files
2. Use utility to create new configuration from appropriate data
3. Save solvated ``.gro`` file

Alternate: use package data instead of files

| 1a. Specify solvent molecule accessed through the module

Alternates: System methods instead of utility functions

| 2a. Update an existing System with provided solvent data
| 2b. Update an existing System with the default solvent

Add ions
~~~~~~~~

| 1. Get configuration with force field
|  1.1 Read new mdp file
|  1.2 Save new tpr file
| 2. Use utility to insert ions and produce a new System object
| 3. Optionally save new configuration

Alternate:

| 1a. re-use an instantiated System

User alternative:

| 2a. new system constructed with fancy constructor

Implementation alternative:

| 2b. utility produces just a configuration

Alternate: operate on objects

| 2c. Use generic ``change_residues`` method with various modes of operation.

Equilibration
~~~~~~~~~~~~~

| 1. Configure from mdp and other files using convenience function to
   construct System
| 2. Optionally save input record
| 3. Run simulation
| 4. Optionally save output to file or just retain object for later API call

Implementation option:

| 1a. convenience function can handle a variety of input argument types and
   forward to an appropriate helper function for files, current instances, etc.

Examples
========

Set up and run a simple simulation
----------------------------------

Consider the entire workflow as a directed acyclic graph.

With various implicit functionality and helper functions, a simple script may
look like this.

.. code-block:: python

    system = gmx.System.from_file('nvt.mdp', structure=pdbsource, topology=pdbsource, T_init=T)

    # Custom analysis code can be attached as an API oject, or a convenient but
    # lower performing call-back.
    system.integrator.callback.append(lambda timestep: do_something(timestep),
                        period=gmx.units.ps(1.0))

    # System is only allowed to have one "runnable" object bound,
    # which is implicitly system.integrator
    status = gmx.run(system, time)

Here I use a lambda to indicate callback signature. Though it is conceivable
to serialize lambdas and closures for non-local execution contexts, it is
questionable whether this is a rabbit-hole we would want to go down. The
context layer can, however, do some introspection and in the worst case
can just implicitly retrieve state to the calling Python interpreter every
``period`` timesteps. In such a case, it would be the remote execution
context that holds a thin proxy object representing the (Sink) callback node.

Note that rather than try to figure out what data a call-back needs, the call-back
can be constructed as a functor or with a closure that binds to the system or
integrator API objects before the simulation launches, allowing the library
freedom to provide a call-back with only the access and data it needs.

Continuing the notion that data constitutes graph edges and computation constitutes
graph nodes. Special terminal nodes are Source and Sink objects that only provide
ports for data flow in one direction. The following assumes a framework in which
standard data stream types have standard names or other means of discovery.
Non-standard data streams could be extracted from catch-all bundled data or
represented with classes defined in additional extensions.

The above script implicitly translates to the following, which a user could
just as well use if desiring more control or additional data handles.


.. code-block:: python

    # Or use other tools for preparation...
    import pypdb
    pdbfile = pypdb.get_pdb_file(somerecord, filetype='pdb', compression=True)
    # repair sequence errors, sanitizing
    record = myscripts.clean_pdb(pdbfile)
    builder = gmx.TopologyBuilder(gmx.forcefields.amber99sb-ildn, water='tip3p')
    # Create an object that is both a StructureSource and TopologySource
    record = builder.from_pdb(record)

    # set up a (set of) simulation(s) with API calls, and include energy log
    options = gmx.Options.from_mdp('nvt.mdp')
    options.temperature_coupling('Nose-Hoover').t = [323, 323, 323]
    options.neighbors('Verlet').ns_type = 'grid'

    # Set and initialize electrostatics options::

    pme = options.coulombtype('PME')
    pme.order = 4
    pme.fourierspacing = 0.16
    nvt = gmx.md.Integrator(options)

    # Create an object that is a VelocitySource with trivial iterator.
    # Initialize with temperature atomic mass data. No inputs. Yields a set of
    # velocities.
    v_init = gmx.ThermalVelocities(**params)

    # Note:
    (gmx.core.VelocitySource(v_init) is v_init.velocity) == True

    system = gmx.System(structure=record, topology=record, velocity=v_init)
    system.integrator = nvt

    # Remove the last logger-like object, if any
    try:
        if isinstance(nvt.observers[-1], gmx.Logger):
            nvt.observers.pop()
    except IndexError:
        pass

    # Add outputs
    gmx.md.FileLogger(nvt, **log_params)
    gmx.io.TrajectoryFile(nvt, **trajout_params)

    # Custom analysis code can still be attached as above, or a convenient but
    # lower performing call-back used.
    nvt.callback.append(lambda timestep: do_something(timestep),
                        period=gmx.units.ps(1.0))

    # run simulation(s)
    status = gmx.run(nvt)

    # Check for energy convergence and rerun

    def converged(quantity):
        return something_clever(quantity)

    while not converged(nvt.potential_energy):
        options.integrator.nsteps += 10000
        status = gmx.run(nvt)

Note: A task like energy minimization can be implemented as a "bottled workflow"
implemented at the highest level, as a series of API objects connected in the
simulation--analysis graph, as a plugin, or as an "integrator".

For additional musings, see :doc:`library`


Trajectory analysis
-------------------

Implicit context management
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    import gmx
    from gmx import TrajectoryFile

    # instantiate trajectory file object
    mytraj = TrajectoryFile(filename, 'r')
    # note nothing is in it yet
    dir(mytraj)

    # add my_filter to to tool chain
    my_filter = gmx.Filter(coordinates=mytraj.coordinates)
    help(my_filter) # we see the output streams available

    # bind low-pass output to saxs modeling to tool chain
    mysaxs = gmx.Saxs(coordinates=my_filter.lowpass, mass=mytraj.mass)
    filehandle = gmx.DataFile('saxs.xvg', 'w')
    filehandle.write(mysaxs.data)

    # get data handle to high-pass output and process into numpy
    try:
        X = [numpy.array(frame.position.extract(), copy=False) for frame in mytraj]
    except gmx.Error as e:
        e.msg == "No execution context"
    # No context is active, and initializing one implicitly could cause poorly-
    # defined behavior for the unevaluated API objects, partiularly if more are
    # added after the extract

    # evaluate remaining tasks and finish
    gmx.run()
    # or
    status = gmx.Context(**runtime_params).resolve(gmx.run())

    # only get the last frame
    X = numpy.array(mytraj.positions.extract(), copy=False)


Explicit context management
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    # instantiate trajectory file object
    mytraj = TrajectoryFile(filename, 'r')
    # note nothing is in it yet
    dir(mytraj)

    # add filter to to tool chain
    my_filter = gmx.Filter(coordinates=mytraj.coordinates)
    help(my_filter) # we see the output streams available

    # bind low-pass output to saxs modeling to tool chain
    mysaxs = gmx.Saxs(coordinates=my_filter.lowpass, mass=mytraj.mass)
    gmx.DataFile('saxs.xvg', 'w').write(mysaxs.data)

    context = gmx.LocalContext()
    mytraj.runner.initialize(context, options)
    # get data handle to high-pass output and process into numpy
    X = [numpy.array(positions.extract(), copy=False) for positions in my_filter.highpass]

    # evaluate remaining tasks and finish
    context.resolve()

Idiomatic context management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    with context, gmx.DataFile('saxs.xvg', 'w') as fh:
        # API objects can create or discover the initialized context from the library
        # context must be initialized in the library in order to perform extract
        # instantiate trajectory file object
        mytraj = TrajectoryFile(filename, 'r')

        # add filter to to tool chain
        my_filter = gmx.Filter(coordinates=mytraj.coordinates)

        # bind low-pass output to saxs modeling to tool chain
        mysaxs = gmx.Saxs(coordinates=my_filter.lowpass, mass=mytraj.mass)
        fh.write(mysaxs.data)

        # get data handle to high-pass output and process into numpy
        X = [numpy.array(frame.position.extract(), copy=False) for frame in mytraj]
        # Triggers evaluation and data retrieval

        # evaluate remaining tasks and finish by leaving the ``with`` block

    try:
        Y = [numpy.array(frame.position.extract(), copy=False) for frame in mytraj]
    catch gmx.Error as e:
        e.msg == "No data"



Sea-level Scenario: Simulate counter-ion effects on peptide from PDB
--------------------------------------------------------------------

Reimplement the CLI workflow described in the funnel web spider toxin
tutorial at
http://cinjweb.umdnj.edu/~kerrigje/pdf_files/fwspidr_tutor.pdf

1. Prepare a configuration from PDB data
2. In vacuo energy minimization
3. Solvate
4. Add ions
5. Solvated energy minimization
6. Two-step equilibration

Assumes

.. code-block::  python

     import gmx

Prepare a configuration from PDB data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Read and clean pdb file.

Consume ``fws.pdb`` and produces ``.gro`` and ``.top`` files. e.g.
``pdb2gmx -ignh -ff amber99sb-ildn -f fws.pdb -o fws.gro -p fws.top -water tip3p``

.. code-block::  python

     # Depending on how pdb2gmx is currently implemented...
     cleanpdb = gmx.util.clean_pdb('fws.pdb', ignore_hydrogens=True)
     (atoms, topology) = gmx.util.pdb2gmx(cleanpdb,
                             forcefield=gmx.forcefield.amber99sb-ildn)
     # or
     (atoms, topology) = gmx.util.pdb2gmx('fws.pdb',
                             forcefield=gmx.forcefield.amber99sb-ildn,
                             ignore_hydrogens=True)

     # Alternatively
     import pypdb
     pdbfile = pypdb.get_pdb_file('1OMB', filetype='pdb', compression=True)
     # Then use other tools to "clean" the PDB record

Build topology using force field and structure data

.. code-block::  python

     # return a gmx.Topology object for atoms in pdbfile
     topology = gmx.TopologyBuilder(gmx.forcefields.amber99sb-ildn).from_pdb(pdbfile)
     # then extend topology information with chosen water model
     topology.add_moleculetype('SOL', gmx.amber99sb-ildn.tip3p_flexible)

Produce .gro and .top files
with a convenience function.

.. code-block::  python

     gmx.AtomData.from_pdb(pdbfile).save('fws.gro')
     topology.save('fws.top')

Alternatively

.. code-block::  python

     # Use utility to read and write files. Bottled workflow.
     gmx.utils.pdb2gmx(force_field=gmx.forcefield.amber99sb-ildn,
                                     pdb_file="fws.pdb",
                                     coords_file="fws.gro",
                                     topology_file="fws.top",
                                     water=gmx.forcefield.amber99sb-ildn.tip3p)

Includes: Set up the box

Set up the box.
~~~~~~~~~~~~~~~

e.g. ``editconf -f fws.gro -o fws-PBC.gro -bt dodecahedron -d 1.2``

.. code-block::  python

     #Read the file into a Atom data object, intuiting the file type by extension
     grofile = gmx.AtomData('fws.gro')
     # Edit the atom data object
     grofile.set_box(gmx.SimulationBox(pbc='dodecahedron', L=1.2))
     # Write out file using the appropriate writer for the file extension
     grofile.save('fws-PBC.gro')

     # or

     # Edit the atom data object
     atoms.set_box(gmx.SimulationBox(pbc='dodecahedron', L=1.2))

In vacuo energy minimization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Prepare input record. e.g. ``grompp -f em-vac-pme.mdp -c fws-PBC.gro -p fws.top -o em-vac.tpr``

.. code-block::  python

     # using convenience function for mdp file
     minimization = gmx.System.from_mdpfile(mdpfile='em-vac-pmd.mdp',
                                                     topology=topology,
                                                     atoms=grofile,
                                                     )
     # or
     minimization = gmx.System.from_mdpfile(mdpfile='em-vac-pmd.mdp',
                                                     topology=topology,
                                                     atoms=atoms,
                                                     )
     # optionally
     # save initialization file
     minimization.save('em-vac.tpr')

More granularly, we might use key-value parameters and access objects more directly.
periodic boundary conditions are already defined in the configuration box
no bond types are replaced by constraints, so we could ignore "constraints=none".
If we were to replace bond types with constraints, is this really a
runtime parameter rather than a topology parameter? If so, the integrator
will have a reference to the constraint scheme and could configure it.

Implicitly created objects (e.g. the neighborlist and electrostatics)
have parameters that can be documented with the rest of the class,
so we should not use the matplotlib strategy of passing ``**kwargs`` along
such that it is hard to figure out what options are available and how they
are processed. If we want to provide convenience, we could bundle options
as dictionaries to be passed to, e.g., an nlist_params argument to the
integrator.

.. code-block::  python

     # With no arguments, use current gmx code to detect and allocate compute resources.
     context = gmx.Context()
     # With default 'context=None', implicitly use gmx.Context()
     minimization = gmx.System(context=context, structure=atoms, topology=topology)
     minimizer = gmx.md.Steep(emtol=500., nsteps=1000, coulombtype='PME', nlist='grid')

     # In practice, kwargs will likely come from parameters files.
     # classes or module attibutes may have shorthand string names.
     minimization_params = {'emtol': 500,
                            'nsteps': 1000}
     integration_params = {'coulombtype': 'PME',
                           'constraints': [],
                           'nlist': 'grid'}
     nlist_params = {'frequency': 1,
                     'rcut': 1.0}

     # bind the Integrator.
     minimization.integrator(minimizer)

For implicit creation and binding, use the class
methods ``gmx.System.from_*()``, which avoid overly-complicated System
constructor and can be extended to package common sets of parameters
and simple workflows. E.g. maybe ``gmx.System.from_minimization()``
Maybe the ``from_`` is cumbersome.

Optionally, add loggers

.. code-block::  python

     # optionally
     # get an energy group of all atoms in the system
     egroup = gmx.Group(minimization.all_atoms())
     minimization.reporters.append(gmx.reporter.LogEnergy(period=1, groups=[egroup]))


Run energy minimizer.
e.g. ``mdrun -v -deffnm em-vac``

.. code-block::  python

     # optionally set output behavior
     minimization.filename_base('em-vac')
     # Run with execution context implicitly configured
     minimization.run()

Solvate.
~~~~~~~~

Fill the box with water. e.g.
``genbox -cp em-vac.gro -cs spc216.gro -p fws.top -o fws-b4ion.gro``

After adding atoms, we will use the same integration method with the
same electrostatics, topology, and neighborlist parameters, but a few
simulation parameters change.

For user-friendliness, AtomData can use getattr(x, to_pdata) or
something to see if an automatic conversion is possible, or objects in the
gmx.solvent submodule could already be AtomData objects.
Similarly, the solute and solvent arguments in solvate() could try to cast
to AtomData objects.

.. code-block::  python

     solvent = gmx.AtomData('spc216.gro') # load solvent molecule coordinates
     grofile = gmx.AtomData('em-vac.gro') # load energy-minimized configuration
     atoms = gmx.util.solvate(solute=grofile,
                      solvent=solvent,
                      topology=gmx.Topology('fws.top'))

     # or maybe
     solvent = gmx.AtomData(gmx.data.spc216)
     # and we are still holding the topology object, which was extended
     # earlier with a solvent definition from gmx.amber99sb-ildn.tip3p_flexible
     atoms = gmx.utils.solvate(solute=grofile, solvent=solvent, topology=topology)

     # or (alternate use)

     atoms = minimization.atoms # load energy-minimized configuration
     atoms = gmx.util.solvate(configuration=atoms,
                      solvent=solvent,
                      topology=topology)
     # Note that 'atoms' now refers to a new object and will need to be reattached.
     system.load_configuration(atoms)

     # or (implementation option)

     # Use utility to solvate a loaded system
     atoms = gmx.util.solvate(system=minimization)

     # Optionally save configuration to file
     atoms.save('fws-b4ion.gro')

Note that 'atoms' now refers to a new object and will need to be reattached with
``system.load_configuration(atoms)``
This invalidates the neighborlist, domain decomposition, etc.
The validity of the topology (i.e. does it define the solvent?) could be
checked with the solvate command, but should definitely be checked whenever
load_configuration() is called. If done at the call, we impose a requirement
that topology must be updated before configuration, which seems reasonable.
Again, some refinement may still need to occur conceptually on the
encapsulation of structure data versus topology data in terms of configuration,
atom typing, molecule / residue type definitions, full system topology,
and miscellaneous metadata, such as the "names" and such used for file
I/O and/or due to differences in conventions for the contents of molecular
data files.

Add ions to solvated system.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

e.g.
``grompp -f em-sol-pme.mdp -c fws-b4ion.gro -p fws.top -o ion.tpr``

Presumably we need a tpr to get the PME and neighbor parameters from the mdp file?
Need to figure out what is really needed by genion. It may be that it is
more appropriate as an integrator, like Steep(). Maybe we need a different
term. Integrator is too specific, and so is MD. Simply Updater?

.. code-block::  python

     minimization = gmx.System.from_mdpfile(
                                     mdpfile='em-sol-pmd.mdp',
                                     topology=topology,
                                     atoms=gmx.AtomData('fws-b4ion.gro'),
                                     )
     minimization.save('ion.tpr')

Use utility to insert ions. e.g. ``genion -s ion.tpr -o fws-b4em.gro -neutral -conc 0.15 -p fws.top -g ion.log``

Solvate, insert-molecule, and genion perform similar functions with different
algorithms and options.
This should probably be
reconsidered as something more abstract. I.e. an alchemy module or
add/change atom methods to invoke these algorithms with appropriate
parameters as arguments.

.. code-block::  python

     # Should the utility be allowed to modify the input in place?
     # e.g. gmx.util.genion(system=minimization)
     # For early iterations, I think not...
     # Create a new System from the input System
     minimization = gmx.util.genion(system=minimization, conc=0.15, neutral=True)

     # implementation alternative: return AtomData

     atoms = gmx.util.genion(system=minimization, conc=0.15, neutral=True)
     minimization.load_configuration(atoms)

     # or (implementation alternative) is genion sufficiently coupled
     # to System objects to simply be a fancy construction helper?

     minimization = gmx.System.genion(system=minimization, conc=0.15, neutral=True)

     # Alternatively...

     minimization.change_residues(mode='solvate', ...)
     minimization.change_residues(mode='pack', ...) # genion
     # mode='trans' or mode='trans_rot'
     # for the two filling mechanims in insert-molecule

     minimization.save('fws-b4em.gro')

Minimize energy in solvated system.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

e.g. ``grompp -f em-sol-pme.mdp -c fws-b4em.gro -p fws.top -o em-sol.tpr``

.. code-block::  python

     # Prep simulation
     minimization = gmx.System.from_mdpfile(
                                     mdpfile='em-sol-pmd.mdp',
                                     topology=topology,
                                     atoms=gmx.AtomData('fws-b4em.gro'),
                                     )

     # Optionally: suppress output configured in mdp file
     for r in minimization.reporters:
         minimization.reporters.remove(r) # or maybe `del r`

     # Optionally, If we already have a handle to mimimizer, we can just reuse it.
     minimimizer.set_param(emtol=250.0, nsteps=5000)
     # The minimizer will notice that its convergence is invalidated both by the
     # new value of emtol and by the updated atoms. The new nsteps parameter
     # is used the next time it tries to converge.

     # mdrun -v -deffnm em-sol
     minimization.filename_base('em-sol')
     minimization.run() # generates trajectory and other configured output

Two step equilibration.
~~~~~~~~~~~~~~~~~~~~~~~

Compare to
::

     $ grompp -f nvt-pr-md.mdp -c em-sol.gro -p fws.top -o nvt-pr.tpr
     $ mdrun -deffnm nvt-pr
     $ grompp -f npt-pr-md.mdp -c em-sol.gro -p fws.top -o npt-pr.tpr
     $ mdrun -deffnm npt-pr

Set up and run two System objects in sequence.

.. code-block::  python

     # grompp -f nvt-pr-md.mdp -c em-sol.gro -p fws.top -o nvt-pr.tpr
     # mdrun -deffnm nvt-pr
     nvt = gmx.System.from_mdpfile(mdpfile='nvt-pr-md.mdp',
                                  topology=topology,
                                  atoms='em-sol.gro',
                                  fname_base='nvt-pr')
     # implementation option: allow other parameter value types
     nvt = gmx.System.from_mdpfile(mdpfile='nvt-pr-md.mdp',
                                  topology=topology,
                                  atoms=minimization,
                                  fname_base='nvt-pr')
     # optionally save input record
     nvt.save('nvt-pr.tpr')

     nvt.run()

     # grompp -f npt-pr-md.mdp -c em-sol.gro -p fws.top -o npt-pr.tpr
     # mdrun -deffnm npt-pr
     npt = gmx.System.from_mdpfile(mdpfile='npt-pr-md.mdp',
                                  topology=topology,
                                  atoms='em-sol.gro',
                                  fname_base='npt-pr')
     # or
     npt = gmx.System.from_mdpfile(mdpfile='npt-pr-md.mdp',
                                  topology=topology,
                                  atoms=nvt,
                                  fname_base='npt-pr')

     npt.save('npt-pr.tpr')
     npt.run()

Construct topology and structure objects
----------------------------------------
There remains a question of the relationships between the AtomData,
Topology (system topology), and Molecule (molecular topology) structures.
I think this will be sorted out in part through examination of current Gromacs
architecture and in part through user input and/or inspiration from popular
tools like MDTraj and MDAnalysis. The interface to these data structures may
or may not have much in common with the implementation, as there is a lot of
opportunity for relational data and lazy attributes.

Note that system topologies and structure data are likely to be generated at the
same time, but common use cases may involve substituting entire sets of coordinates
or system topologies (in part or in whole).

From pdb and force field definitions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TODO

From raw data and force field definitions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TODO

From a currently instantiated simulation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TODO

From generic structure and force field information
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TODO

From bare metal?
~~~~~~~~~~~~~~~~

TODO

Run with frozen N and C terminals
---------------------------------

Make index for residue groups
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TODO

Configure simulation with frozen terminals
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
e.g.

| ``make_ndx –f clg_b4md.pdb –o clg_ter.ndx``
| Prompted input: ``r 1-36 & a C N CA`` for residue selection, get the new group number and rename for convenience: ``name 15 Terminal``, then ``v`` to view and verify.
| Additional MDP entries:
|    ``energygrp_excl = Terminal Terminal Terminal SOL``
|    ``freezegrps = Terminal``
|    ``freezedim = Y Y Y``
| ``grompp –f md.mdp –c pr.gro –p clg.top –n clg_ter.ndx –o md.tpr``

Run simulation with index
~~~~~~~~~~~~~~~~~~~~~~~~~

TODO

Trajectory Manipulation tasks
-----------------------------

Extract a simulation frame
``trjconv -f traj.xtc -s file.tpr -o time_3000ps.pdb -dump 3000``

Access atom coordinates from the trajectory

| 1a. Create Trajectory object from file
| 1b. Get handle to Trajectory or Frame object held by simulation
| 1c. Use Trajectory object created via API
| 2. Retrieve Frame

Re-center a molecule
``trjconv –f traj.xtc –o traj_center.xtc –s str_b4md.gro –pbc nojump -center``

Make the dummy gro file for the g_covar analysis.
``trjconv –s ../md.tpr –f dangle.trr –o resiz.gro –n covar.ndx –e 0``

Concatenate trajectories
``trjcat –f md1.xtc md2.xtc md3.xtc ... (etc) –o mdall.xtc -settime``


TensorFlow analogy
==================

The following is a mind-expanding thought experiment, not a goal. Taken to an
exterme, data flow and execution abstraction as a thin layer on top of
TensorFlow might look like the following.

.. code-block::  python

    coordinates, topology = gmx.utils.pdb2gmx(force_field=gmx.forcefield.amber99sb-ildn,
                                    pdb_file="fws.pdb",
                                    coords_file="fws.gro",
                                    topology_file="fws.top",
                                    water=gmx.watermodel.tip3p)
    # Create timestep variable
    current_step = gmx.Step(0)

    # Create particle positions variable
    xyz = gmx.ParticleCoordinates()
    xyz.initialize(coordinates) # operation deferred until "graph" is evaluated

    # Create velocities variable
    v = gmx.Velocities()
    v.initialize(gmx.ThermalVelocities(**params))

    # Create a node to hold system state at a time step
    state = gmx.SystemState()

    # Create operation to invoke non-bonded force compute kernel
    nonbonded = gmx.ForceCompute(xyz, topology)

    # Create operation to invoke constraints kernel
    constraints = gmx.Constraints(xyz, topology)

    # Create operation to invoke electrostatics kernel
    pme = gmx.Electrostatics(xyz, **params)

    # Operation to sum forces for integrator
    forces = gmx.SumForces(nonbonded, constraints, pme)

    # Operation to perform integration, updating data handles and state
    state = gmx.Integrate(xyz, v, forces)

    # Operation to perform simulation loop to advance to a given time step
    output = gmx.RunUpTo(state, current_step, gmx.Step(1000))

    with gmx.initialize(**runtime_params) as session:
        # Create an operation that updates to the next timestep
        for _ in range(10):
            session.eval(output)
            print("Step: {}, electrostatic energy: {}".format(
                    session.eval(current_step),
                    session.eval(pme).energy))
