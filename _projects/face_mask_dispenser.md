---
layout: page
title: Contactless Mask Dispenser
description: A contactless face‑mask dispenser integrating sensors, motorised coil dispensing, and embedded controllers. The metal coil  mechanism was designed in Autodesk Inventor and the frame fabricated from metal with laser‑cut clear panels; system includes power management, motor control firmware, sensor integration, LED status indicators, and a leak‑proof sealed enclosure.
img: assets/img/mask-dispenser-project/full-assembly.JPG
importance: 5
category: mechatronics
---

## Project Overview

A mechatronic system designed to dispense 50 individual, uncontaminated face masks contactlessly, with full aerosol protection during standby, dispensing, and restocking operations. The dispenser fits within a compact BXP3 enclosure (400 x 200 x 180 mm) and is suitable for wall mounting in high-traffic environments.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include video.liquid path="assets/video/mask-dispenser-demo.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true muted=true %}
    </div>
</div>
<div class="caption">
    Figure: Demonstration of the contactless mask dispenser.
</div>

## My role

I was responsible for the **Actuation Subsystem**, which encompassed the delivery rod design, motor selection and characterisation, motor mounting, and restocking mechanism. I also contributed to the housing CAD, frame and plate machining, and assisted team members across other subsystems.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/project-functional-decomposition.JPG" title="Project Functional Decomposition" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Project Functional Decomposition. Red nodes indicate my primary responsibilities; orange nodes indicate areas where I provided support.
</div>

## Design Process Overview

The actuation subsystem was broken into discrete, sequentially addressable steps, each with identified challenges and cross-subsystem interfaces. This systematic approach ensured setbacks were anticipated early and design decisions were traceable and justified.

| Step  | Task                                                    | Key Challenge                                      |
| ----- | ------------------------------------------------------- | -------------------------------------------------- |
| 1     | Mask measurement and rod orientation selection          | Fitting mask loops within size constraints         |
| 2     | Proof of concept prototype                              | Validating feasibility before procuring components |
| 3-5   | Iterative rod design (3D printed, coil, machined metal) | Tolerances, surface finish, mask alignment         |
| 6     | Rod selection based on testing                          | Reliability vs. capacity trade-off                 |
| 7     | Motor selection via torque analysis                     | Balancing torque, size, and cost                   |
| 8     | Motor mount design and manufacture                      | Structural rigidity and vibration minimisation     |
| 9-10  | Restocking mechanism design and assembly                | Ease of use within housing constraints             |
| 11-12 | Full system testing                                     | Code integration, fault recovery                   |

---

## Delivery Rod Design

### Constraints

The delivery rod was oriented along the 200 mm dimension of the enclosure to maximise mask capacity. Key design constraints included:

- Minimum thread pitch of 4 mm to accommodate mask ear loops
- Maximum length of 200 mm (excluding motor, mount, and wall thickness)
- Mounting height of at least 380 mm to allow masks to hang freely
- Maximum diameter of 20 mm for roof clearance
- Smooth surface finish to prevent mask snagging

### Iteration 1: Proof of Concept

A skewer-and-string mockup was used to validate the core dispensing concept before any components were procured or fabricated.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/poc.JPG" title="Proof of Concept" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Initial rough proof of concept built from a skewer and string.
</div>

### Iteration 2: 3D Printed Rods

A range of 3D printed rods were designed with varying thread profiles (triangular, buttress, square, and U-shaped) to identify the geometry most conducive to smooth, fault-free mask dispensing. Prior to printing full rods, a set of test shaft mounting holes at incremental sizes was printed to characterise the dimensional tolerances of the 3D printer and prevent material waste.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/rod-shaft-sizes.JPG" title="Rod Shaft Test Holes" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: 3D printed test pieces used to calibrate shaft hole tolerances.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/rods.JPG" title="3D Printed Rod Iterations" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Multiple thread profile iterations (triangular, buttress, square, U-shaped) printed and tested.
</div>

Testing confirmed that the **U-shaped thread** produced the fewest dispensing faults. However, the inherent surface roughness of FDM 3D printing remained a source of occasional mask snagging.

### Iteration 3: Metal Coil Rod

A metal coil was investigated as an alternative to address the surface finish limitations of 3D printed rods. Two coil pitch and length configurations were tested, each attached to the motor shaft via a custom 3D printed adapter. A 3D printed drill guide was used to ensure accurate hole placement in the shaft attachment.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/metal-coil-attachmen.JPG" title="Metal Coil Attachment" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: 3D Printed shaft attachment for the metal coil.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/metal-coil.JPG" title="Metal Coil" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Two coil rod assembly iterations.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/drill-guide.JPG" title="Drill Guide" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: 3D printed guide used to accurately drill holes into the coil shaft attachment
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/coil-motor-assembly.JPG" title="Coil Motor Assembly" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Coil assembled onto the 28BYJ-48 motor.
</div>

The coil offered significantly improved reliability over the 3D printed rods. However, its reduced capacity (requiring 3 restocks to dispense all 50 masks) and the tendency for masks to sit at an angle on the coil (risking contact with internal components) were identified as key drawbacks.

### Iteration 4: Machined Aluminium Rod (Final Design)

The final design combined the strengths of the previous two iterations: the smooth surface finish of metal and the U-shaped thread geometry that keeps masks parallel to the dispenser wall. The rod was manufactured using a **CNC mill and lathe**, with the thread spanning the full rod length for ease of machining. Two parallel holes were drilled at one end to accept grub screws for secure attachment to the motor shaft flats.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/aluminum-rod.JPG" title="Aluminium Rod" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Machined aluminium U-shaped delivery rod.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/metal-rod-assembly.JPG" title="Metal Rod Assembly" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Aluminium rod assembled onto motor.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/metal-rod.JPG" title="Metal Rod" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Full length view of the final aluminium delivery rod.
</div>

**Results:** Zero dispensing faults in testing, masks sat parallel to the wall, and 25 masks fit on the rod, requiring only 2 restocks to dispense all 50 masks.

---

## Motor Selection

### Torque Analysis

The required motor torque was determined by analysing the frictional forces acting on the delivery rod. The mask ear loops are made from Spandex (polyurethane), which has a coefficient of friction of mu = 0.2. With 25 masks at 2.5 g each, the total load on the rod was 0.61 N. Using a free body diagram and static equilibrium:

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/delivery-rod-fbd.JPG" title="Delivery Rod FBD" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Free body diagram of the delivery rod under full mask load.
</div>

- Reaction force at support B: By = 0.08296 N
- Required friction force to overcome: F = 0.2 x 0.08296 = **16.59 mN**

### Motor Type Selection

A stepper motor was selected over servo and brushless DC motor alternatives based on the application requirements: precise position control, repeatability, low additional circuitry, low to medium acceleration, and low cost.

| Motor Type    | Advantages                                               | Disadvantages                                   |
| ------------- | -------------------------------------------------------- | ----------------------------------------------- |
| Stepper Motor | High precision, high holding torque, no encoder required | Higher heat, low-to-medium acceleration         |
| Servo Motor   | High speed, high torque, high dynamic response           | Requires encoder, more expensive                |
| Brushless DC  | High efficiency, minimised EMI                           | Requires encoder and complex driver electronics |

### Motor Comparison: NEMA17 vs 28BYJ-48

Two stepper motors were evaluated based on availability, size, torque, driver requirements, and mounting features.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/nema17.JPG" title="NEMA17" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: NEMA17 stepper motor
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/28BYJ-48.JPG" title="28BYJ-48" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: 28BYJ-48 stepper motor.
</div>

The **28BYJ-48** was selected for its compact form factor (42 x 32 x 30 mm), which allowed for a longer delivery rod and therefore greater mask capacity per restock. Key specifications:

- Type: 4-phase, 5-wire unipolar stepper motor
- Rated voltage: 5-12 V (compatible with all other system components)
- Operating current: 240 mA (motor), 500 mA (driver)
- Holding torque: **34.4 mN.m** (exceeds the 16.59 mN frictional requirement)
- Shaft: Dual flats for grub screw attachment
- Driver: ULN2003 (34 x 33 x 11 mm, supplied with motor)

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/ULN2003.JPG" title="ULN2003" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: ULN2003 stepper motor driver board.
</div>

The same motor and driver combination was used for both the delivery rod and the dispenser door actuator.

---

## Motor Mount Design

The motor mount was 3D printed in PLA, selected over CNC machining due to its lower setup complexity for this application. CNC milling was reserved for components requiring tighter tolerances and greater structural robustness, such as the delivery rod itself.

| Manufacturing Method | Tolerance (mm)     | Min. Layer Thickness (mm) | Max. Build Volume (mm) |
| -------------------- | ------------------ | ------------------------- | ---------------------- |
| CNC Milling          | +/- 0.025 to 0.125 | 0.01 (cutting depth)      | 2000 x 800 x 1000      |
| Industrial FDM       | +/- 0.5            | 0.8 to 1.0                | 900 x 600 x 900        |

Two mount iterations were developed:

**Iteration 1: NEMA17 Mount** - Designed and fabricated first to validate the mounting approach while the final motor selection was still pending.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/NEMA17-mount.JPG" title="NEMA17 Mount" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: First motor mount iteration, designed for the NEMA17.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/nema17-mount-cad.JPG" title="NEMA17 Mount CAD" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: CAD model of the NEMA17 motor mount.
</div>

**Iteration 2: 28BYJ-48 Mount (Final)** - Redesigned to fit the smaller selected motor. Upon testing, the mount held the motor securely with minimal vibration, enabling accurate and repeatable mask dispensing.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/28BYJ-48-mount.JPG" title="28BYJ-48 Mount" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: Final motor mount for the 28BYJ-48, 3D printed in PLA.
</div>

---

## Restocking Mechanism

The restocking mechanism was designed to match the thread geometry of the delivery rod so that masks could be pre-loaded onto the key and transferred onto the rod in a single motion.

**Iteration 1: Loading Key (Final Design)**

A simple 3D printed key with teeth matching the rod thread pitch. The user loads masks onto the key, inserts it into the dispenser, loops the mask ear loops over the rod, and withdraws the key downward. Its simplicity made it fast and intuitive to use.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/loading-key.JPG" title="Loading Key" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: 3D printed mask loading key.
</div>

**Iteration 2: Loading Gripper**

A dual-key gripper with mismatched opposing teeth, held closed by a rubber band. The intent was to allow the user to grip and hold masks securely during loading. However, testing revealed the rubber band lacked sufficient elastic force to hold all masks when fully loaded, and the overall mechanism was more difficult to operate than the simple key.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mask-dispenser-project/loading-gripper.JPG" title="Loading Gripper" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure: 3D printed loading gripper (second iteration, not selected).
</div>

The first iteration was selected as the final design based on its simplicity, reliability, and ease of use.

---

## Outcomes and Reflection

The actuation subsystem met all project requirements: 50 masks were dispensed across 2 restocks, with zero faults recorded during the final system test. The aluminium rod design, motor selection, and restocking mechanism all performed as intended.

Two issues emerged during the final demonstration:

1. **Mask snagging:** Under certain loading conditions, masks caught between the dispenser wall and those behind them on the rod. This was not observed in prior testing and could be addressed in future iterations through slightly larger mask spacing or a marginally shorter rod.
2. **User error:** The dispenser door was inadvertently left open during demonstration, allowing aerosol spray to enter the enclosure. While the masks were not contaminated, the dispenser required cleaning within the 20-minute demonstration window. This highlighted a need for a door-open indicator or interlock in future designs.

---

## Skills Demonstrated

- Iterative mechanical design and prototyping (3D printing, CNC milling, lathe machining)
- Engineering analysis: statics, friction, and torque calculations
- Motor selection and electromechanical system integration
- Manufacturing process selection and justification
- Cross-subsystem design coordination within a team environment
- Technical documentation and design traceability

---
