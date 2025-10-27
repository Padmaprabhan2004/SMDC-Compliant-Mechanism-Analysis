# Dynamic Simulation of a Bio‑Inspired Inchworm with Soft Pneumatic Actuators (SPA) in ABAQUS




## About

* Multi‑chamber SPA geometry designed in SolidWorks and exported as STEP/IGES.
* Ogden hyperelastic material model for realistic large‑strain behavior.
* Chamfered pressurizing end tip for improved ground contact and directional bias.
* Surface‑to‑surface contact for ground interaction and **self‑contact** between chambers.
* Periodic pressure actuation at **50 kPa** implemented via a user subroutine (e.g., `DLOAD`/`VDFIELD` approach or Abaqus UMAT/DLOAD combination).
* Dynamic, nonlinear, large deformation analysis (ABAQUS/Explicit recommended).

---
## Use the Visualization module to open `.odb` after running the job and inspect:

   * Time history of deformation
   * Contact force evolution
   * Reaction forces and center‑of‑mass displacement

---

## Material — Ogden Model

The Ogden model captures large nonlinear elastic deformation typical of silicone or elastomeric materials used for SPAs. Include an `*HYPERELASTIC, OG˙DEN` card in the ABAQUS input with coefficients determined from experimental curve fitting. Example card (replace coefficients with fitted values):

```
*Material, name=Silicone_Ogden
*Hyperelastic, Ogden, N=3
Hyperelastic properties
mu1=0.9
alpha1=7.5
D1=0.005
```

Add density and damping as required:

```
*Density of surface is 7800 g/cm^3
*Density of SPA is 1.049E-09g/cm^3
```

---

## Contact & Boundary Conditions

* **Ground contact:** surface‑to‑surface contact between the chamfered tip (slave) and a rigid ground (master). Use a friction coefficient consistent with your material/ground (specify in input or CAE).
* **Self‑contact:** enable surface‑to‑surface contact between adjacent SPA chambers (small sliding, finite friction).
* **Constraints:** appropriate tie or connector constraints for any rigid components (if present) and reference points for applying pressure/monitoring motion.

Suggested ABAQUS keywords / settings:

```
*CONTACT PAIR, interaction=SPA_ground_contact
*SURFACE BEHAVIOR, FRICTION
<friction coefficient>
*CONTACT CONTROLS

*STEP : Static GENERAL, NLgeom ON, 
stabilization was used with energy fraction 1E-4
```

---

## Pressure Actuation (50 kPa, periodic)

Pressure is applied as a time‑varying load (e.g., amplitude curve or via a subroutine). The subroutine enables complex periodic waveforms, phase shifting for different chambers, or feedback logic.

A simple periodic pressure function used by `pressure_subroutine.for` could be:

[ p(t) = 50,
\text{kPa} \times 0.5\left(1 - \cos\left(2\pi\nfrac{t}{T}\right)\right) ]

Where `T` is the pressure period. The subroutine can map this scalar pressure to the chamber faces or internal cavity elements.
---

## Results 

* Cyclic elongation and contraction of SPA chambers.
* Directional locomotion achieved due to asymmetric chamfered tip contact and frictional interactions.
* Contact force time histories show staggered gripping and release phases between front and rear contact surfaces.


---
