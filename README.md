# nea-dynamics-lab

The deliverable is index.html at the repo root.

What it does

- Integrates Sun, eight planets and the Moon with mutual point-mass gravity in a barycentric frame, plus eight asteroid test-particle copies in the same state vector, so all runs share one planetary trajectory and one step sequence.
- Each copy carries position, velocity, quaternion, body angular velocity and 320 facet temperatures. Everything is propagated by an adaptive Dormand–Prince 5(4) with separate error scales per state class. Playback speed drops when the machine cannot keep up; tolerances never do.
- Direct radiation and thermal recoil are separate facet sums with the exact formulas you specified, including the documented spin-averaged warm-up. No empirical Yarkovsky or YORP term is added on top.
- The 2³ factorial (P, S, T) runs live. Heating stays on in every run, and the T-only combinations are labelled as diagnostic decompositions.
- Views: top-down main scene with click-to-follow, pan and zoom; a close-up with temperature-coloured facets, spin axis, Sun direction and autoscaled force arrows with printed scales; a displacement view with a prominent power-of-ten magnification that never alters forces.
- Live comparison table, signed factor effects and interactions, residual time series with least-squares trend versus periodic residual, CSV and JSON export, and a methods panel listing omitted physics. Observational validation is stated as not performed.

Verification results, measured in the browser

┌────────────────────────────────────┬──────┬────────┐
│               Group                │ Rows │ Result │
├────────────────────────────────────┼──────┼────────┤
│ Sun-only Kepler orbit              │ 6    │ pass   │
├────────────────────────────────────┼──────┼────────┤
│ Ideal plates and thermal recoil    │ 8    │ pass   │
├────────────────────────────────────┼──────┼────────┤
│ Prescribed force and lever arm     │ 4    │ pass   │
├────────────────────────────────────┼──────┼────────┤
│ Torque-free rigid body             │ 4    │ pass   │
├────────────────────────────────────┼──────┼────────┤
│ Tolerance and step refinement      │ 9    │ pass   │
├────────────────────────────────────┼──────┼────────┤
│ Mesh refinement 80/320/1280 facets │ 6    │ pass   │
└────────────────────────────────────┴──────┴────────┘

Verdicts are computed at click time from measured errors against printed thresholds. Small angles use atan2 forms so nano-radian attitude and plane differences are not roundoff artefacts. Rows within
three times the estimated numerical noise floor are marked unresolved.

Things to know

- Measured speed in headless Chrome and Node: roughly 20 to 50 seconds per asteroid orbthe target. The 1280-facet option is about four times slower.
- Preset properties that are assumed rather than documented are listed on screen and in the JSON export. The synthetic encounter reaches about 0.02 AU from Earth near day 149, and the on-screen note
  says the geometry was designed.
- Permanently shadowed facets have no equilibrium in a conduction-free model and cool slowly; the warm-up panel says so.
- I did not commit. The README still holds the original brief; I left it untouched since it.

# Prompt
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

# Context
**Controlled perturbation comparisons are appropriate, but I would refine the design and substantially qualify my earlier assessment.** A factorial experiment can reveal how model components affect predictions. It cannot, by itself, establish that those predictions are physically accurate.

My recommendation is to combine **numerical verification, targeted perturbation comparisons, and independent validation**.

First, the terminology needs correcting. A full factorial design evaluates every combination of the selected factors. Three binary switches require \(2^3=8\) runs. In my original table, **A, B, C and E already form a two-factor factorial experiment**, provided E uses the same Earth/Moon gravity model as C. D introduces a gravity-model refinement, while F adds several effects together. The complete six-case list is therefore a mixed comparison design, rather than a full factorial design. [NIST’s definition](https://www.itl.nist.gov/div898/handbook/pri/section3/pri333.htm)

More consequentially, rereading the thesis exposed issues that should be resolved **before interpreting differences between perturbation models**.

* **The Sun-only baseline needs verification.** Figure 6.6(a), printed page 50, shows its semi-major axis varying approximately from 1.2455 to 1.2462 AU—an excursion of roughly 100,000 km. Under the stated two-body equations, that quantity should remain constant apart from numerical error. This calls for checking the integration and the calculation of orbital elements. 
* **The reported force and orbital changes appear inconsistent.** Table 6.4 gives a mass of \(6.2081\times10^{12}\) kg, while Figure 6.9 shows SRP forces of order tens of newtons. My angular-momentum scaling check gives an orbital-plane change of order \(10^{-8}\) radians over the plotted 1.6 years, whereas Figure 6.6(c) shows changes of order \(10^{-1}\) radians. If that graph represents conventional inertial orbital inclination, these results need reconciliation. A reference-frame, unit, integration, or plotting issue could explain the discrepancy; the PDF alone cannot identify its cause. 
* **The rotation experiment starts from rest.** Table 6.3 sets angular velocity to zero. That is useful for demonstrating that a torque generates rotation, but actual Geographos rotates approximately every **5.22 hours**. Its existing spin substantially changes the illumination history and averaging of torques. [Geographos physical-characterisation study](https://arxiv.org/html/1407.2127v1)

Consequently, I would replace “the SRP side was reasonably well validated” with: **the thesis demonstrated an implementation and provided a limited force benchmark, but the accuracy of the coupled orbital and rotational predictions remains insufficiently established.** The broad predominance hypothesis was not demonstrated.

The comparison programme I would recommend is the following.

**Start by establishing a trustworthy numerical baseline.** Recover the analytical Kepler orbit and check conservation of energy and angular momentum. Verify simple radiation-force and torque cases independently. Then tighten integration tolerances and refine the surface discretisation until the resulting changes are comfortably smaller than the perturbations being measured. Otherwise, adding more physics can obscure existing errors.

**Use a realistic gravitational reference for scientific comparisons.** Let \(G\) include the Sun, planets and Moon, with additional corrections selected according to the required accuracy. Earth and Moon should not be assumed to be the leading gravitational perturbers simply because those are the bodies included in the experiment. High-precision asteroid studies include the other planets, and sometimes massive asteroids and relativistic corrections. [Del Vigna et al., force model](https://arxiv.org/pdf/1805.05947)

Then define radiation components explicitly:

* \(S\): momentum transfer from incident and reflected sunlight, including force and torque.
* \(T\): recoil from thermal emission, including force and torque.

A useful initial factorial experiment is:

| Run | Gravitational reference \(G\) | Direct radiation \(S\) | Thermal recoil \(T\) |
| --- | ----------------------------: | ---------------------: | -------------------: |
| 00  |                      Included |                    Off |                  Off |
| 10  |                      Included |                     On |                  Off |
| 01  |                      Included |                    Off |                   On |
| 11  |                      Included |                     On |                   On |

The thermal-only run is a **computational diagnostic**: sunlight still heats the asteroid, while the direct momentum-transfer term is disabled. It is not a physically realisable asteroid.

For a signed observable \(y\), such as displacement along a common reference orbit at a specified time, calculate:

$$
I_{ST}=y_{11}-y_{10}-y_{01}+y_{00}.
$$

This measures whether the combined response differs from the sum of the separate responses. Such interactions can arise because changing attitude changes illumination, which changes subsequent forces and torques. Using signed components helps avoid apparent interactions introduced merely by taking a vector norm.

There is also a correction to my original “add Yarkovsky/YORP” recommendation: **YORP includes reflected-light torque as well as thermal-emission torque.** Adding a complete YORP prescription on top of an SRP torque model could count part of the same physics twice. The implementation should account for each momentum-transfer contribution once. [Yarkovsky and YORP review](https://arxiv.org/abs/1502.01249)

**Test Earth/Moon importance and gravity refinements separately.** Starting from the verified reference model, remove Earth’s contribution, Moon’s contribution, and other relevant planetary contributions in controlled runs. These comparisons measure the consequences of omitting each contribution for the selected asteroid and interval.

Compare point-mass gravity against point-mass gravity **plus non-spherical corrections** in an additional, nested experiment. Spherical harmonics are a refinement of a body’s gravity field; their importance must be assessed against encounter geometry and the error budget. They should not automatically receive priority over other omitted physics. The 2014 Bennu analysis provides a useful example of explicitly testing model refinements and omissions. [Chesley et al.](https://arxiv.org/pdf/1402.5573)

If interactions between Earth/Moon gravity, direct radiation and thermal recoil are themselves the research question, then use **all eight combinations of those three switches**, holding the remaining background model fixed. Grouping Earth and Moon together would still prevent conclusions about their individual importance.

**Choose the outputs before ranking the perturbations.** Different outputs can produce different rankings.

| Research question           | Appropriate outputs                                                               |
| --------------------------- | --------------------------------------------------------------------------------- |
| Orbital prediction          | Position and velocity residuals; radial, along-track and cross-track displacement |
| Long-term orbital evolution | Orbit-averaged semi-major-axis drift, separated from periodic variations          |
| Rotational evolution        | Spin-rate change, pole direction and relative attitude angle                      |
| Close encounter prediction  | Encounter timing, closest-approach distance and encounter-plane displacement      |

My earlier suggestion of “attitude change in quaternion norm” was incorrect. **Unit quaternions should retain unit norm regardless of orientation.** Their norm is a numerical consistency check; relative rotation angle measures attitude change.

A nonzero perturbation also does not automatically imply sustained secular drift. Periodic changes need to be distinguished from changes that accumulate across successive orbits.

Finally, **simulation comparison and observational validation require different procedures**:

* For perturbation attribution, hold initial conditions and physical parameters fixed.
* For observational validation, fit each candidate model fairly to the observations, account for parameter uncertainty, and preferably assess predictions against observations withheld from fitting.

Horizons is valuable for an independent propagation benchmark, provided epochs, reference frames, time scales, units and model assumptions match. Its ephemerides are fitted model predictions with uncertainties, however, rather than direct observations. An orbital match also does not validate an attitude model; that requires rotational observations such as light curves. [JPL Horizons documentation](https://ssd.jpl.nasa.gov/horizons/manual.html)

One historical correction matters: thermal effects were relevant **already in 2014**. YORP had been detected on Geographos in 2008. A complete thermophysical solver need not have been mandatory for your MSc scope, but excluding those effects required limiting the hypothesis accordingly. [Ďurech et al., 2008](https://astro.troja.mff.cuni.cz/davok/papers/geographos_yorp_08.pdf)

I would therefore reformulate the investigation around **whether a specified set of perturbations meets stated orbit and attitude accuracy requirements for defined asteroids and time intervals**. Repeat the comparisons across plausible shape, spin, density and thermal-property uncertainties. That produces a defensible result about when each model component is necessary—and gives the factorial experiment a clear role within the validation process.
