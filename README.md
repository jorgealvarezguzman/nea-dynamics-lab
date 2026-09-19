# nea-dynamics-lab

Build an interactive **Near-Earth Asteroid Dynamics Lab** as one self-contained HTML file, with embedded CSS and JavaScript, no external libraries, assets, or network requests. Make it look like an elegant museum exhibit: a dark starfield, restrained colours, readable typography, thin fading trails, and a softly glowing Sun. Start running immediately.

The purpose is to investigate how gravity, direct solar radiation pressure, and thermal recoil change an asteroid’s orbit and rotation. Clearly distinguish a numerical demonstration, a comparison between models, and validation against observations.

1. **Create the physical scene**

Show a top-down projection of a genuinely three-dimensional simulation, with the Sun, eight planets, the Moon, and an irregular rotating asteroid. Frame the inner Solar System initially, with a separate close-up showing the asteroid’s shape, spin axis, illuminated facets, temperature distribution, and force arrows.

Use documented physical constants and deterministic, approximate initial conditions. Label the default system as a synthetic demonstration rather than an accurate ephemeris. Include a “Geographos-inspired” preset with an elongated synthetic shape, an approximately 5.223-hour rotation period, and approximate orbital elements. Identify which properties are assumed.

Use SI units internally. Keep body sizes and force-arrow lengths enlarged only for display, with their visual scale identified.

2. **Implement actual dynamics**

Propagate the massive bodies with mutual Newtonian point-mass gravity. All comparison asteroids must share the same planetary trajectories and behave as test particles.

Propagate asteroid translation, orientation, angular velocity, and temperature from their governing equations. Never animate the main simulation using prescribed ellipses.

Use a consistent reference frame throughout. If asteroid integration uses heliocentric coordinates, include both direct and indirect third-body acceleration terms. Compute forces, torque lever arms, inertia, and orbital elements in correctly defined frames.

Use an adaptive integrator appropriate for coupled, non-conservative dynamics, such as Dormand–Prince 5(4), with separate error scales for position, velocity, rotation, and temperature. Keep physics integration independent of rendering speed. Reduce achieved playback speed when necessary instead of silently compromising accuracy.

3. **Model direct and thermal radiation separately**

Represent the asteroid with a small, closed, convex, irregular triangular mesh. Calculate its centre of mass and inertia consistently with its geometry and assumed uniform density.

For direct radiation, calculate each illuminated facet’s force using solar flux, projected area, and absorption, diffuse-reflection, and specular-reflection coefficients. Require those coefficients to sum to one. Calculate torque by summing each facet’s moment about the centre of mass. Apply inverse-square variation of solar flux.

For thermal recoil, implement an explicitly labelled simplified thermal model. A suitable browser-scale approximation is a lumped surface temperature for each facet:

$$
C_A\frac{dT}{dt}
=
\alpha I(r)\max(0,\mathbf n\cdot\mathbf s)
-
\epsilon\sigma T^4,
$$

where \(C_A\) is heat capacity per unit area, \(\alpha\) is absorptivity, \(I(r)\) is incident solar flux, and \(\mathbf s\) points toward the Sun.

For Lambertian thermal emission, calculate each facet’s recoil:

$$
\mathbf F_{\mathrm{thermal}}
=
-\frac{2}{3c}\epsilon\sigma A T^4\mathbf n.
$$

Describe this as a finite-heat-capacity approximation, not a full subsurface-conduction model. Initialise temperatures through a documented warm-up procedure.

Sum thermal forces and torques directly. Do not add another empirical Yarkovsky acceleration or total YORP torque on top of contributions already represented. Explain that reflected-light torque and thermal-emission torque both contribute to rotational evolution.

4. **Run a genuine factorial comparison**

Propagate eight asteroid copies simultaneously, starting from identical position, velocity, orientation, angular velocity, shape, material properties, and initial temperatures.

Use three binary factors:

* **P:** Earth and Moon gravitational perturbations.
* **S:** Direct solar radiation force and torque.
* **T:** Thermal-emission recoil force and torque.

Run all combinations: 000, 001, 010, 011, 100, 101, 110, and 111. Keep solar gravity and the remaining planetary gravitational contributions identical across runs.

Use 111 as the most complete implemented comparison model, without calling it ground truth. Switching P off must remove the complete Earth/Moon contribution from the asteroid equations while preserving the shared planetary trajectories.

For a thermal-only diagnostic, keep solar heating active while disabling direct photon momentum transfer. Explain that these switches isolate mathematical contributions; some combinations are artificial diagnostic experiments.

5. **Make small differences visible honestly**

Draw the selected asteroid trajectories overlaid in the main view. Add a separate magnified displacement view relative to run 111, with an adjustable, prominently labelled magnification factor.

Never increase physical forces merely to make trajectories visibly separate.

Below the scene, show a live comparison table containing each run’s enabled effects, elapsed simulated time, position difference, velocity difference, semi-major-axis difference, orbital-plane difference, spin-rate difference, and relative attitude angle.

Compare orientations using the angle between rotations, accounting for the equivalence of \(q\) and \(-q\). Quaternion norm is a numerical diagnostic, not an attitude-change measurement.

Show time-series plots for selected residuals and distinguish periodic orbital variations from fitted secular trends.

6. **Expose interactions and omission effects**

For a selected signed output, show the effect of toggling each factor while holding the other factors fixed.

For example, at a fixed setting of P, calculate:

$$
I_{ST}=y_{11}-y_{10}-y_{01}+y_{00}.
$$

Use a signed quantity such as displacement along a common reference orbit. Explain that a nonzero interaction means the combined response differs from the sum of the separate responses.

Do not convert these differences into universal percentages of “importance.” Rankings apply only to the selected asteroid, interval, observable, and parameter assumptions.

7. **Include numerical verification**

Provide a “Run verification” button with actual pass, fail, or unresolved results for:

* A Sun-only Kepler orbit: energy, angular momentum, orbital elements, and measured orbital period against analytical expectations.
* An ideal reflecting plate: radiation force against \(F=2IA/c\) at normal incidence.
* A prescribed force and lever arm: torque magnitude and direction.
* Torque-free rigid-body rotation: rotational energy and inertial angular momentum.
* Timestep/tolerance refinement: changes in the reported observables.
* Surface-mesh refinement on the same underlying shape.

Show errors and acceptance thresholds. Never generate success indicators without executing the checks. Mark effects as numerically unresolved when their magnitude is comparable to the estimated numerical error.

Display conservation diagnostics where conservation is expected. Label asteroid energy changes under planetary forcing and radiation as energy changes, rather than automatically treating them as integration drift.

8. **Provide useful controls**

Let me click a body to follow it, drag to pan, scroll to zoom, pause, reset, step forward, and change playback speed. Target roughly 30–60 seconds per asteroid orbit when numerical accuracy and hardware permit.

Let me select visible comparison runs and edit asteroid size, density, spin period, pole direction, optical coefficients, emissivity, and surface heat capacity. Parameter changes must restart every comparison from matched initial conditions.

Include an ordinary-orbit preset, a synthetic Earth-encounter preset, and a small-asteroid preset. Identify synthetic encounter geometry clearly.

Allow CSV export of results and JSON export of the complete configuration, constants, initial conditions, numerical settings, and model assumptions.

9. **State the scope clearly**

Include a compact methods panel describing the implemented equations and approximations. Identify omitted physics, including relativistic corrections, non-spherical planetary gravity, gravity-gradient torques, detailed conduction, and surface self-heating.

Do not claim to reproduce Geographos observations, JPL Horizons, impact probabilities, or a universally dominant perturbation hierarchy. Display observational validation as “not performed” unless an independent reference dataset has actually been supplied and compared.

Deliver the complete working HTML, with understandable code and no placeholder charts, fabricated measurements, or simulated verification results.
