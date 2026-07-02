---
layout: page
title: Doppler‑SiMpLE - Doppler‑Enhanced LiDAR SLAM (Honours Thesis)
description: Proposed and implemented a scan‑stitching SLAM algorithm that integrates per‑point Doppler velocity from LiDAR to improve localization in geometrically-deficient environments (e.g., tunnels). Implemented in C++ with MATLAB for prototyping/analysis. Focus on sensor fusion, algorithm design, and performance evaluation.
img: assets/img/slam-lidar-thesis/doppler-simple-reward.png
importance: 3
category: mechatronics
---

## Overview

Honours thesis (BEng, Mechatronics, UQ) supervised by Dr. Tyson Phillips. The project set out to answer a single question: can consecutive LiDAR scans be stitched together using per-point Doppler velocity, rather than by comparing one scan against another?

Scan-to-scan registration is the process of aligning overlapping LiDAR point clouds to estimate how a platform has moved. It underpins the front-end of most LiDAR SLAM systems. The problem is that it relies on unique geometric features being visible between consecutive scans. In geometrically deficient environments such as tunnels and freeways, there is very little distinguishing structure to match against, and registration degrades.

Doppler-enabled LiDAR sensors (such as the Aeva Aeries II) return a per-point radial velocity alongside the usual range measurement. This velocity is independent of the surrounding geometry. The thesis proposed Doppler-SiMpLE, a registration algorithm that rewards a pose estimate for making predicted per-point velocities match the measured per-point velocities from the sensor, and evaluated it against the existing SiMpLE algorithm across eight real-world sequences.

The result: Doppler-SiMpLE performed similarly, and in geometrically deficient environments often better, than SiMpLE, while relying only on the current scan rather than a stored map of previous scans.

---

## Aim

To establish if scan-stitching can be performed using per-point velocity information, rather than scan-to-scan comparison.

### Scope

| In-scope                                                    | Out-of-scope                                             |
| ----------------------------------------------------------- | -------------------------------------------------------- |
| Front-end, sensor-dependent processing                      | Back-end, sensor-independent processing                  |
| Same-source LiDAR point-cloud registration                  | Cross-source point-cloud registration                    |
| Incorporating per-point velocity into a reward-based metric | Integrating Doppler-SiMpLE and SiMpLE into one algorithm |

---

## Background

For a platform to be autonomous, it needs to build a map of its environment and locate itself within that map so it can make motion and control decisions. This is the Simultaneous Localisation and Mapping (SLAM) problem, and it is difficult because localisation needs a map and building the map needs a pose estimate, so the two are coupled.

A common approach uses LiDAR to capture the environment as point cloud data. Because LiDAR has a limited range, individual scans have to be registered (stitched) together to build a complete map and recover the platform trajectory. The SiMpLE algorithm (Simple Mapping and Localisation Evaluation) is a high-performing, front-end, LiDAR-only odometry method with only four configurable parameters. Its weakness is that it struggles in geometrically deficient environments.

Rather than adding algorithmic complexity to fix this, the thesis added a new measurement: Doppler per-point velocity, which does not depend on environment geometry.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/slam-lidar-thesis/aeva-aeries-ii.png" title="Aeva Aeries II LiDAR" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Doppler-enabled LiDAR sensor: the Aeva Aeries II (FMCW)
</div>

---

## Theoretical Foundation

### ICP, a cost method

The Iterative Closest Point (ICP) method, developed by Besl and McKay in 1992, is the core approach to point cloud registration. ICP estimates the transformation that best aligns a new scan to an existing one by repeatedly finding the closest point correspondences and minimising the error between them until the error falls below a threshold. Point-to-point ICP minimises Euclidean distance between correspondences; point-to-plane ICP minimises the projection of that distance onto the surface normal at the target point.

The limitation of ICP is that it is error-based: it assumes every correspondence is exact. In real datasets this is never true because of noise, outliers and partial overlap, so error accumulates in the transformation estimate.

### SiMpLE, a reward method

SiMpLE takes a reward-based approach instead. It works in three steps: spatially subsample the input scan, register that reduced scan against the local map, then use the resulting pose estimate to update the local map for the next scan. Rather than minimising error, SiMpLE rewards a transformation for placing new points close to previously mapped points, using a zero-mean Gaussian reward. This relaxes the assumption that correspondences are exact and makes SiMpLE more robust to noise and unsegmented point clouds.

SiMpLE is a significant improvement on ICP, but like most registration methods it still depends on unique geometric features, so it struggles where those features are scarce.

---

## Proposed Method: Doppler-SiMpLE

Doppler-SiMpLE keeps the reward-based structure of SiMpLE but changes what is being rewarded. Instead of rewarding points for being close to previously mapped points, it rewards a pose hypothesis for making the predicted per-point Doppler velocity match the measured per-point Doppler velocity from the sensor.

The predicted velocity for each point is derived using the approach from DICP (Doppler ICP by Hexsel et al.). Given an initial pose hypothesis (seed), the vehicle linear and angular velocities are approximated from the state vector over the sampling period. These are transformed into the LiDAR frame using the vehicle-to-LiDAR calibration. The LiDAR velocity is then projected onto the direction vector from the LiDAR to each measured point to produce a predicted per-point velocity. The difference between this prediction and the measured velocity feeds the same zero-mean Gaussian reward used by SiMpLE.

<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/slam-lidar-thesis/velocity-schematic.png" title="Per-point velocity estimation schematic" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Estimating the per-point Doppler velocity. The LiDAR velocity is projected onto the direction vector from the LiDAR to each measured point to obtain the predicted per-point velocity.
</div>

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/slam-lidar-thesis/doppler-simple-reward.png" title="Doppler-SiMpLE reward metric" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The proposed Doppler-SiMpLE metric. For each point, the difference between the predicted and measured per-point velocity is scored by a zero-mean Gaussian reward, and the pose that maximises the total reward is selected.
</div>

Because the velocity comparison happens entirely within the current scan, Doppler-SiMpLE has two useful properties that SiMpLE does not:

- It is independent of the geometry of the observed environment, since velocity does not rely on distinctive structure.
- It does not require a stored map of previous scans to register against, only the current scan.

It also inherits the robustness of the reward-based metric, so it handles moving objects without the outlier pruning that error-based methods typically need.

---

## Implementation

The full Doppler-SiMpLE pipeline was implemented in both C++ and MATLAB. C++ was used for the performance-oriented implementation and MATLAB for prototyping and analysis. The registration uses a KD-tree for nearest-neighbour search, an objective function that scores a pose hypothesis by summing per-point rewards, and a search heuristic that uses an optimisation solver (such as Gauss-Newton, Quasi-Newton or Nelder-Mead) to find the pose that maximises the total reward.

For a like-for-like comparison, the original SiMpLE was restructured into a scan-to-scan version by removing its local map update step and returning the pose estimate from the previous scan to the current scan directly. The Aeva dataset ground truth poses, given in the world frame, were converted to the scan-to-scan frame so all three (Doppler-SiMpLE, SiMpLE and ground truth) could be compared on equal terms.

The companion C++ code is available at https://github.com/NadiaRegli/doppler-simple

---

## Dataset

Evaluation used the Aeva dataset, a benchmark of eight real-world sequences captured with a Doppler-enabled Aeva Aeries I FMCW LiDAR (120 degree horizontal field of view, 30 degree vertical, up to 300 m range, 10 Hz sampling). Sequences 00 to 03 are short and geometrically deficient (tunnels and a freeway); sequences 04 to 07 are longer highways and a parkway with moderate geometric structure.

| Seq. | Location                      | Length [m] | Frames | Geometric Structure |
| ---- | ----------------------------- | ---------- | ------ | ------------------- |
| 00   | Baker-Barry Tunnel (Empty)    | 860        | 837    | Poor                |
| 01   | Baker-Barry Tunnel (Vehicles) | 907        | 658    | Poor                |
| 02   | Robin Williams Tunnel         | 689        | 301    | Poor                |
| 03   | Brisbane Lagoon Freeway       | 4942       | 1762   | Poor                |
| 04   | Ontario Highway 7             | 8876       | 6343   | Moderate            |
| 05   | Ontario Highway 407           | 7836       | 4734   | Moderate            |
| 06   | Don Valley Parkway            | 10310      | 5083   | Moderate            |
| 07   | Ontario Highway 427           | 7238       | 4012   | Moderate            |

---

## Results

### Velocity estimation

The first step was to confirm the predicted per-point velocity could be estimated accurately. Across every sequence, regardless of environment, geometric structure, vehicle speed or length, the difference between measured and predicted velocity was approximately zero, aside from genuinely moving objects such as other vehicles. Those moving objects are not a problem, since the reward-based metric does not assume correspondences are exact. This validated using the predicted velocity inside Doppler-SiMpLE.

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/slam-lidar-thesis/velocity-estimation.png" title="Velocity estimation performance" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Measured per-point velocity (left) compared with predicted per-point velocity (right) and the difference between them (bottom) for a representative scan in each sequence. The difference is minimal aside from moving objects.
</div>

### Registration performance

In the geometrically deficient sequences (00 to 03), Doppler-SiMpLE clearly outperformed SiMpLE in the translational component of the scan-to-scan pose, closely following the ground truth where SiMpLE lost track due to the lack of geometric features. The rotational component was weaker: Doppler-SiMpLE did not track rotation as well, likely because small errors between measured and predicted velocity allow several pose hypotheses with slightly different rotations to earn the same maximum reward.

In sequences 01 and 02, large spikes appeared at the start where the vehicle was already moving quickly. Seeding the solver with zero when the platform is not stationary places the seed too far from the solution, so it fails to converge initially. The takeaway was that a zero initial seed is only appropriate when the vehicle starts stationary.

In the moderate-structure sequences (04 to 07), SiMpLE improved substantially in translation, while Doppler-SiMpLE stayed at a similar level to its geometrically-deficient performance. This shows Doppler-SiMpLE is insensitive to declining geometric structure, whereas SiMpLE's performance is tightly coupled to it. In the presence of moving vehicles there was no observable change to Doppler-SiMpLE, confirming the reward-based metric is not disturbed by moving objects.

### Trajectory comparison

Over full world-frame trajectories, both methods drifted in geometrically deficient environments, driven mostly by rotational error. Sequence 02 was illustrative: Doppler-SiMpLE recovered the correct translational length of the trajectory but spiralled away as rotational error accumulated. In moderate environments SiMpLE produced the more accurate overall trajectory. The significant point is that Doppler-SiMpLE remained comparable to SiMpLE while using only current-scan velocity, without matching against a stored map.

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/slam-lidar-thesis/trajectory-comparison.png" title="Trajectory comparison" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Ground truth trajectory (green) against Doppler-SiMpLE (blue) and SiMpLE (red) pose estimates in the world frame for all eight Aeva dataset sequences.
</div>

### Objective function seed analysis

Several seeds were tested. Ground truth as a seed confirmed the algorithm behaves as expected in translation but still leaves rotation ambiguous. A constant velocity model performed poorly. The best practical choice was the previous pose estimate for the translational component (zero for the very first scan), with the rotational component left at zero, since there is little roll-pitch-yaw movement across the Aeva sequences and feeding an incorrect rotational estimate only accumulates drift.

### Sensitivity analysis

Like SiMpLE, Doppler-SiMpLE has only four configuration parameters, and all proved insensitive to deviation, which makes the algorithm practical across varying environments and point cloud densities:

- Registration reward, sigma-reward: insensitive across 0.1 to 1000, best when set near the input scan subsampling radius.
- Local map subsampling, r-map: completely insensitive; set greater than r-new to limit the effect of the LiDAR scan pattern.
- Input scan subsampling, r-new: insensitive, marginally best around 0.1; smaller values give a more accurate representation of the environment.
- Convergence tolerance, cT: smaller is generally better but too small causes non-convergence; around 1e-5 is appropriate.

### Computational performance

Doppler-SiMpLE was slightly faster than the scan-to-scan version of SiMpLE for shorter sequences (00 to 04), and slightly slower for the longer sequences (05 to 07). The slowdown on long sequences comes from using the previous pose estimate as the seed, which accumulates rotational error and makes convergence harder as drift grows. Overall the computational cost was comparable to SiMpLE, so including per-point velocity remains real-time applicable.

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/slam-lidar-thesis/computational-time.png" title="Average time per scan comparison" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Average computational time per scan for Doppler-SiMpLE (blue) and SiMpLE (red) across sequences 00 to 07. Doppler-SiMpLE is faster on shorter sequences and slower on longer ones due to accumulated drift.
</div>

---

## Outcomes and Reflection

The project met its aim. It confirmed that per-point Doppler velocity can be accurately estimated, then showed that a reward-based algorithm built on that velocity can perform scan-to-scan registration similarly to, and in geometrically deficient environments often better than, SiMpLE, without ever matching against previous scans.

The main limitation is rotation. Doppler-SiMpLE improves translational pose estimation in poor-geometry environments but struggles with the rotational component, which then limits full trajectory accuracy. This points naturally to future work: pairing Doppler-SiMpLE with a method like SiMpLE that is strong in rotation but needs support in translation, integrated through a weighted objective function in the same way DICP combines a Doppler error function with a geometric one.

---

## Key Skills and Learnings

- Sensor fusion and algorithm design, incorporating a new measurement type (Doppler velocity) into an existing registration framework
- Point cloud registration theory, including ICP variants (point-to-point, point-to-plane) and reward-based registration
- Mathematical derivation of a predicted per-point velocity model from vehicle and LiDAR frame kinematics
- Implementation in both C++ (KD-tree nearest-neighbour search, objective function, optimisation solvers) and MATLAB
- Experimental design, benchmarking against a baseline across eight sequences with parameter, seed and computational analysis
- Working with real-world benchmark datasets and converting between world-frame and scan-to-scan representations
- Technical writing and research communication under supervision

---

## Technologies Used

`C++` · `MATLAB` · `LiDAR` · `Doppler / FMCW Sensing` · `Point Cloud Registration` · `SLAM` · `KD-Trees` · `Optimisation Solvers (Gauss-Newton, Quasi-Newton, Nelder-Mead)` · `Aeva Dataset`
