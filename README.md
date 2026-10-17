# Cantilever Beam Bending: Experiment vs Closed-Form vs FEA vs SolidWorks

This project validates three ways of predicting how a thin aluminum cantilever beam deflects under a tip load, using a physical test as the baseline.

The four methods compared:
1. **Experiment:** a physical beam loaded in steps, with tip deflection measured directly.
2. **Closed-form analysis:** an Euler–Bernoulli beam theory solution derived by hand.
3. **Finite element analysis:** a two-element beam model built by hand and solved in MATLAB.
4. **Commercial package:** a SolidWorks Simulation static study of the same beam.

**Result:** all three predictions agreed closely, and the experiment came within **5%** of them.

📄 **[Read the full report](Beam_Bending_Report.pdf)**

---

## Beam properties
| Property | Value |
|---|---|
| Material | Aluminum 1060 |
| Cantilevered length, L | 29.6 cm |
| Width, w | 15.24 cm |
| Thickness, t | 0.635 mm |
| Mass | 71.7 g |
| I<sub>xx</sub> (bending axis) | 3.252 × 10⁻¹² m⁴ |
| I<sub>yy</sub> | 2.949 × 10⁻⁸ m⁴ |

---

## Part 1: Experimental testing
1. Weighed the beam and the loading nuts on a scale.
2. Drilled a ¼ in hole near the free end to hang the load.
3. Clamped the opposite end to a table with two hand clamps to create the fixed support.
4. Marked the unloaded beam position on a wall.
5. Hung nuts on a string through the hole in steps of 8 nuts, for five load steps from 0.36 N to 1.80 N, and measured tip deflection with a ruler at each step.
6. Removed the nuts 8 at a time and recorded deflection on the way down, to build a **loading–unloading hysteresis plot**. The beam returned to its original position once unloaded, which confirmed elastic behavior and the fixed-end boundary condition.

## Part 2: Closed-form analysis (Euler–Bernoulli)
1. Found the centroid and the second moments of area. The section is symmetric, so I<sub>xy</sub> = 0.
2. Started from the governing equation, EI·u'''' = −mg, which accounts for the beam's own weight.
3. Integrated four times, applying a boundary condition at each step: S(L) = −F, M(L) = 0, u'(0) = 0, and u(0) = 0.
4. This gives the deflection equation:

   EI·u(z) = mg·z⁴/(24L) − mg·z³/6 − F·z³/6 + FL·z²/2 − mgL·z²/4

5. Evaluated it at z = L for each experimental load.

## Part 3: Finite element analysis (hand-built)
1. Split the beam into **two beam elements** with three nodes. Each node has one translational and one rotational degree of freedom, for 6 DOF in total.
2. Built the 4×4 stiffness matrix for each element and assembled them into the 6×6 global stiffness matrix.
3. Built the load vector. It includes the beam's self-weight as a distributed load, c = ρAg, plus the tip point load F.
4. Applied the fixed-end constraint (q₁ = θ₁ = 0) to reduce the system to 4×4.
5. Solved **F = Kq** in MATLAB for the tip deflection q₃.

## Part 4: SolidWorks Simulation
1. Modeled the beam in SolidWorks to the experimental dimensions.
2. Assigned **Aluminum 1060** material properties.
3. Applied a **Fixed Geometry** constraint at the clamped end.
4. Loaded the model with gravity (self-weight) and a tip point load, matching each of the five experimental load cases.
5. Ran a linear static study and read the tip displacement from the contour plots.

## Part 5: Results and comparison
Tip deflection from the two hand methods:

| Tip load F (N) | Closed-form u(L) (m) | Hand FEA q₃ (m) |
|---|---|---|
| 0.361 | 0.0240 | 0.0248 |
| 0.721 | 0.0379 | 0.0387 |
| 1.082 | 0.0518 | 0.0526 |
| 1.443 | 0.0657 | 0.0665 |
| 1.803 | 0.0796 | 0.0804 |

- The closed-form, hand FEA, and SolidWorks results are nearly identical.
- The experiment follows the same linear trend, with a maximum error **under 5%**.

**Sources of error:**
- The clamps couldn't create a perfectly fixed boundary. Some rotation and twist remained at the support.
- Reading deflection against wall marks with a ruler limits precision.
- Small variations in the beam itself may have caused local stiffness differences.

**Takeaway:** for slender beams under small deflections, closed-form theory, a coarse two-element FEA model, and commercial FEA all predict the response accurately. In practice, the limiting factor is how faithfully the test fixture reproduces the ideal boundary condition.

---

## Files
| File | Description |
|---|---|
| `Beam_Bending_Report.pdf` | Full report: procedure, derivations, FEA matrices, SolidWorks setup, plots, and error analysis. |
| `CAD_FEA/ECproject.SLDPRT` | SolidWorks model of the beam, with the Simulation studies saved inside it. |
| `CAD_FEA/ECproject-Static 2.CWR` | SolidWorks Simulation results for static study 2. |
| `CAD_FEA/ECproject-Static 3.CWR` | SolidWorks Simulation results for static study 3 (17,502 nodes, 8,563 elements). |
| `CAD_FEA/ECproject-Static 3.LOG`, `.MFC`, `.DF1`, `.DF2`, `.gsz` | Solver log and auxiliary files for study 3. |

## Viewing the FEA results
1. Keep all the `CAD_FEA` files together in one folder.
2. Open `ECproject.SLDPRT` in SolidWorks.
3. Turn on the add-in: **Tools → Add-Ins → SolidWorks Simulation**.
4. Click the **Static 2** or **Static 3** study tab at the bottom of the window to see the displacement and stress plots.

## Tools
SolidWorks Simulation · MATLAB · Euler–Bernoulli beam theory · finite element method (beam elements)
