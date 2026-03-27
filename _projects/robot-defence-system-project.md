---
layout: page
title: Laser Bot - Autonomous Arena Defence Robot
description: Autonomous mobile robot that navigates to an arena centre and engages IR targets with a laser pointer. Responsible for full PCB design and wiring, including schematic capture, component selection, footprint creation, design rule compliance, Gerber file generation, continuity testing, and soldering, using Altium Designer.
img: assets/img/laser-bot-project/full-assembly-bot.JPEG
importance: 4
category: mechatronics
---

## Project Overview

This report documents my individual contribution to the METR2800 group project: the design and fabrication of an autonomous mobile robot capable of navigating to the centre of an arena, locating an infrared (IR) target, and engaging it by firing a laser pointer. My role was the design, layout, fabrication, and wiring of the printed circuit board (PCB) that served as the central electrical hub of the robot.
The robot integrated DC motor control, IR-based laser turret sensing and actuation, ultrasonic distance sensing, and an ATmega328P microcontroller, all of which were interconnected through the PCB I designed.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include video.liquid path="assets/video/bot-operation.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true muted=true %}
    </div>
</div>
<div class="caption">
    Demo of the laser bot operation - the robot navigates to the center of the arena and fires laser at IR target.
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/full-assembly-bot.JPEG" title="Full Assembly of the Laser Bot" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Full Assembly of the Laser Bot
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/actual-main-pcb-1.JPEG" title="Main PCB" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/actual-main-pcb-2.JPEG" title="Main PCB with components soldered" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Main PCB
</div>

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/actual-turret-pcb.JPEG" title="Turret PCB" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Turret PCB
</div>

## Individual Contribution
### Scope of Work

My contribution encompassed the full PCB design pipeline, from schematic capture through to physical assembly and verification. Specifically, this included:
- Self-directed learning of Altium Designer through approximately 20 hours of tutorial content, applied directly to the project schematic and layout.
- Integration of the motor control circuit and IR turret circuit, supplied by other team members, into a unified schematic, incorporating all power regulation requirements.
- Creation of all component schematic symbols and PCB footprints from scratch, cross-referenced against manufacturer datasheets to ensure dimensional accuracy.
- PCB layout and routing with consideration for optimal component placement, design rule compliance, signal integrity (ground plane, capacitor crosstalk avoidance), and manufacturing constraints.
- Generation and submission of Gerber X2 output files to the UQ PCB manufacturer.
- Post-fabrication continuity testing using a multimeter and iterative debugging of routing errors.
- PCB soldering and final wiring of all external components and sensors to the board.

### Key Design Decisions

The following design decisions were made to optimise the electrical performance, physical layout, and manufacturability of the PCB:

| Design Decision | Justification | Technical Basis |
| --- | --- | --- |
| Split into two PCBs (main + turret) | Reduced wire run length from turret components and simplified routing on the main board. | Original single-PCB design required 11 long wires from the turret. Splitting the design to a dedicated turret PCB reduced these to short, grouped connections via a 10-way male header. |
| M3 mounting holes on all PCB corners | Enabled stable mechanical fastening to the robot chassis. | M3 screws selected as the smallest readily available fastener size; M2 screws are difficult to source. |
| Male headers for sensor and inter-PCB connections | Reduced PCB footprint and improved routing neatness. | Male headers (6mm x 6mm) are significantly smaller than two-way terminal blocks (10mm x 8.3mm), freeing routing space. |
| Terminal blocks retained for DC motors and power supply | Higher current and voltage demands required more robust connections than male headers. | DC motors draw a maximum stall current of 2.1A; power supply operates at 12V DC, both exceeding what male headers are rated for. |
| Ground plane used for all ground connections | Minimised electrical noise and interference caused by ground loops. | A contiguous ground plane forces the return current path directly beneath each trace, lowering loop inductance and reducing radiated noise. |
| PCB dimensions: main 70mm x 80mm; turret 29mm x 29mm | Constrained by the existing chassis and 3D-printed turret head dimensions. | Turret head internal cavity: 30mm x 30mm; chassis footprint: approximately 100mm x 100mm. |
| All pins and components labelled on silkscreen | Eliminated the need to cross-reference the schematic during physical assembly and wiring. | Increased PCB area from the design revision provided sufficient room for clear silkscreen labels on all connections. |
| Manufacturing design rules adhered to throughout | Ensured the board could be manufactured at standard cost without speciality processes. | Min track/space: 6mil/6mil; 2 copper layers; 1oz copper; green solder mask; min drill 0.3mm; min annular ring 0.6mm. |

---

## Design Results

The final design consisted of two PCBs: a main board housing the ATmega328P microcontroller, motor driver IC, voltage regulators, and all peripheral connectors; and a turret board accommodating the four SFH310RA phototransistors, their pull-down resistors, and the XC4490 laser module.
The PCB was manufactured by the UQ PCB facility and underwent continuity testing upon arrival. An initial routing error on the voltage regulator was identified and corrected in a revised board revision. The final board operated correctly, with all motor, turret, and sensor connections verified under functional test conditions.
One discovered issue during integration was that the servo motor library conflicted with the ATmega328P’s PWM output on pins 9 and 10, preventing motor speed control. This was assessed as non-critical, as the final project specification did not require variable speed, and the robot performed correctly at fixed speed.

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/main_pcb_schematic.jpg" title="Main PCB Schematic" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Main PCB Schematic
</div>

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/main_pcb_design.JPG" title="Main PCB Design" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Main PCB Design
</div>

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/turret_pcb_schematic.jpg" title="Turret PCB Schematic" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Turret PCB Schematic
</div>

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/laser-bot-project/turret_pcb_design.JPG" title="Turret PCB Design" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Turret PCB Design
</div>

## Learning and Reflections
### Technical Skills Acquired

This project represented my first experience with end-to-end PCB design. The key technical skills developed include:
- Proficiency in Altium Designer - including schematic capture, footprint creation, PCB layout, design rule checking (DRC), and Gerber file generation.
- Practical knowledge of PCB manufacturing constraints and how to design to a manufacturer’s specification.
- Hands-on soldering skills, reinforced through the course’s soldering tutorials.
- Applied electrical engineering knowledge including voltage regulation circuit design, power budgeting, and signal integrity considerations such as ground plane design and decoupling capacitor placement.
- Debugging methodology - using continuity testing to trace and resolve routing errors on a fabricated board.

### Reflections on the Design Process

A key learning was the importance of verifying all input specifications before committing to a design. A discrepancy in the motor current requirements, which was not identified until PCB testing, required a board revision to correct the voltage regulator output capacitor values. This reinforced the value of thorough upstream design review at the component specification stage.
Additionally, the project highlighted the impact of component placement decisions on system-level performance. Positioning the 16MHz crystal oscillator in close proximity to the ATmega328P, and placing all connectors on the board perimeter, both contributed meaningfully to signal quality and ease of assembly.

### Reflections on Course Structure

The most significant learning outcome of this course was gaining practical, industry-relevant experience with professional PCB design tooling. Altium Designer is widely used in the electronics industry, and developing working proficiency with it, including creating custom schematic symbols and footprints from datasheets, was a valuable and transferable outcome. The course would benefit from structured guided practicals covering the full design workflow, allowing all students to develop breadth of knowledge beyond their individual assigned domain.

## References
- Altium Designer. (2020, October 25). Preventing Ground Loops in Your PCB Design. Retrieved from Altium: https://resources.altium.com/p/preventing-ground-loops-your-pcb-design

- Bossche, P. V. (2005). SUBAT: An assessment of sustainable battery technology. Journal of Power Sources, 5–6.

