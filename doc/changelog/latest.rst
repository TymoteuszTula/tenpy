[latest]
========

Release Notes
-------------
TODO: Summarize the most important changes

Many thanks to Wilhelm Kadow for great work on the mixed real- and momentum-space representation for models!

Changelog
---------

Backwards incompatible changes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- change default separator for :func:`tenpy.tools.misc.get_recursive`, :func:`~tenpy.tools.misc.set_recursive`, :func:`~tenpy.tools.misc.update_recursive`, and
  :func:`~tenpy.tools.misc.flatten` to ``'.'`` instead of ``'/'``. 
  Also, :func:`~tenpy.tools.misc.get_recursive` now supports a `default` argument.
- replace the `orthogonal_to` option of :class:`tenpy.algorithms.mps_common.Sweep` by an `orthogonal_to` keyword argument for the class and it's :meth:`~tenpy.algorithms.mps_common.Sweep.init_env`.
- deprecated options:
  - `sweep_0` for sweep classes; use `sweeps` in the `resume_data` instead.
- Require context-manager-style setup for Simulation classes.
- Replace the `SingleSiteMixer` and `TwoSiteMixer` with the :class:`~tenpy.algorithms.dmrg.SubspaceExpansion`; major
  rewriting of the mixer code.
  Further, we now reactivate/reset the :class:`~tenpy.algorithms.dmrg.Mixer` whenever growing the bond dimension due to `chi_list` in DMRG.
  The new option :cfg:option:`DMRGEngine.chi_list_reactivates_mixer` allows to disable this.
- Renamed the `simulation_class_name` argument/parameter to `simulation_class` of :func:`~tenpy.simulations.simulation.run_simulation` 
  for more consistency with remaining simulation parameters.


Added
^^^^^
- :class:`tenpy.models.mixed_xk.MixedXKLattice` and :class:`tenpy.models.mixed_xk.MixedXKModel` for models in mixed real- and momentum space representation on a cylinder.
- :func:`tenpy.simulations.simulation.run_sequential_simulations`
- :meth:`tenpy.networks.mps.MPSEnvironment.init_LP` and :meth:`~tenpy.networks.mps.MPSEnvironment.init_RP`, and
  :meth:`tenpy.networks.mpo.MPOEnvironment.init_LP` and :meth:`~tenpy.networks.mpo.MPOEnvironment.init_RP` additionally
  support the argument `start_env_sites`, which can now be part of the `init_env_data`.
  This allows to converge MPO environments from scratch, given only the MPO and MPS, with a power-method.
  The :class:`tenpy.networks.mpo.MPOTransferMatrix` was added for converging environments from scratch with Arnoldi.
- Caching with the :mod:`tenpy.tools.cache.DictCache`, :mod:`tenpy.tools.cache.PickleCache`, and :mod:`tenpy.tools.cache.Hdf5Cache` to reduce memory requirements.
  In that context, the keyword argument `cache` was added to the :class:`~tenpy.algorithms.algorithm.Algorithm` base class.
- :meth:`tenpy.networks.mps.MPS.sample_measurements`.
- equality test for :class:`~tenpy.linalg.charges.LegCharge`.
- MPS with segment boundaries now keep track of unitaries at the boundaries in :attr:`~tenpy.networks.mps.MPS.segment_boundaries`.
- :class:`~tenpy.simulations.simulation.OrthogonalExcitations` simulation class for finding excited states with DMRG.
- :cfg:option:`Simulation.group_sites` for the simultion class.
- `extract_segment` method for model, lattice and MPS/MPO classes.
- :class:`~tenpy.algorithms.mpo_evolution.TimeDependentExpMPOEvolution` for time-dependent hamiltonians.
- :func:`tenpy.tools.misc.merge_recursive` to merge nested parameter dictionaries.


Changed
^^^^^^^
- :func:`tenpy.tools.misc.find_subclass` now directly raises an error if no subclass with that name is found.
- Renamed the `logging_params` to `log_params`.
- :func:`tenpy.simulations.measurement.correlation_length` now supports a `unit` keyword.
  If it is not given explicitly, a warning is raised.
- :func:`tenpy.networks.mps.MPS.canonical_form` now supports an argument `envs_to_update` to allow keeping
  MPS/MPOEnvironments consistent.
- keyword argument `sequential_simulations` for :meth:`tenpy.algorithms.algorithm.Algorithm.get_resume_data`.
- Allow to select the distribution of the generated `U` for the :class:`~tenpy.algorithms.tebd.RandomUnitaryEvolution`.
  For randomly perturbing an MPS in :meth:`~tenpy.networks.mps.InitialStateBuilder.randomized` or the new
  :meth:`~tenpy.networks.mps.MPS.perturb`, select the distribution suitable to preserve the dtype of the MPS.
- Rewriting of the :class:`~tenpy.algorithms.dmrg.DensityMatrixMixer`.
- Generalize `conserve` argument of the :class:`~tenpy.networks.site.Site` subclasses, e.g. :class:`~tenpy.networks.site.SpinHalfSite`,
  to handle `None` and `False` the same way as a string ``'None'``. The string is the new, documented way to disable charge
  conservation.


Fixed
^^^^^
- (!) Missing ``+ h.c.`` in :meth:`tenpy.networks.mpo.MPOEnvironment.full_contraction` when `H.explicit_plus_hc` was True.
  This caused wrong energies being reported during DMRG when `explicit_plus_hc` was used.
- (!) The DMRG mixers didn't respect/handle the `explicit_plus_hc` of MPOs correctly. This might have lead to an
  insufficient subspace expandsion.
- Always return `psi` in the :meth:`tenpy.algorithms.algorithm.Algorithm.get_resume_data`.
  Optionally, this can be a copy, e.g. if `psi.canonical_form` needs to be called at the end of the algorithm, which would render the environments invalid.
- Use logging in simulation only after calling :func:`~tenpy.tools.misc.setup_logging`.
- :issue:`99` and :issue:`113` by allowing to either reinitialize the environment from scratch, 
  and/or to updating the environments in psi.canonical_form().
- :issue:`137` additional error check for :meth:`~tenpy.networks.mps.MPS.from_lat_product_state` with
  :class:`~tenpy.models.lattice.HelicalLattice`.
