# E---V
## Objective
This is aimed at Multidomain Modeling of Hydroelectric Power Generation.
In this system, flowing water causes the wheel to rotate which, in turn, spins the generator that creates electricity.
## Overview
To build large systems such as robots and vehicles, models that include multiple domains are needed. The Simpscape library contains blocks to model many common physical domains and a transducer is needed to convert energy from one domain to another. In this case, an idealized lossless conversion (Rotational Electromechanical Converter) between two physical domains is implemented. The energy conversion is bi-directional. In Simscape, the rotational electromechanical converter block can be used to represent a DC motor or as an electric generator.
Multiple domains can be converted into a single physical network using a converter. 
In this project, there are three physical domains: 
- fluid,
- mechanical
- and electrical.

And there are two energy conversions: 
- fluid to mechanical: to spin the wheel
- and mechanical to electrical: to generate electricity.\

<img width="1420" height="873" alt="Hydroelectricity" src="https://github.com/user-attachments/assets/114957d7-e427-43d5-b9c3-326a836427e2" />

Gravity driving the flow of water, the water spinning the wheel, and the wheel turning the generator are captured in the complete model. 
It's necessary to start with small portions of the model, troubleshoot them, and then add more complexity. The mechanical-to-electrical conversions is first considered, then the fluid energy by an Ideal Torque Source block is represented, which acts as a placeholder for the fluid system.
