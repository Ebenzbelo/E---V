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

### Fluidic Circuit
When there is a working electromechanical system, the ideal torque source is replaced with the torque imposed by the water hitting the paddles of the wheel.
Fluid flows are often described as fluidic circuits. In fact, there are direct analogies between many fluid and electrical elements. For example, pressure in a fluid system acts like voltage in an electrical system. A pressure differential across a pipe causes fluid to flow from high to low pressure, just like a voltage across a resistor drives a current from high to low voltage.

<img width="801" height="146" alt="Illustration" src="https://github.com/user-attachments/assets/b4f16737-f486-4e3e-a49a-87d6af63fa7e" />

The flow from the reservoir to the water wheel is a fluid circuit with a pressure source and resistive elements. The pressure source is the hydrostatic pressure at the bottom of the reservoir.
Hydrostatic pressure is the pressure that results from the weight of the fluid. At the surface of a body of water, there is only atmospheric pressure—the weight of the air in the atmosphere. At the bottom of a body of water, there is also the weight of all the water. That pressure difference is ΔP=ρgh, where g is the acceleration of gravity and ρ is the fluid density.

The resistive elements in your circuit are the pipes and valves that control the flow. In this simple model, there is one pipe that directs water to the wheel, and another pipe that directs the water to an overflow area. A valve controls how much water can flow from the reservoir to the water wheel, and the remaining water is diverted to the overflow pipe. Both the surface of the reservoir and the outlet of the overflow pipe are at atmospheric pressure.

## METHODOLOGY
Generators operate more efficiently when running at high rotation rates, but the water wheel spins at relatively low speed. To increase the efficiency of the generator, you can add a gear box.

The Rotational Electromechanical Converter block is in the Electrical > Electrical Elements library.
This block relates torque and angular velocity to current and voltage by using a constant of proportionality, K.
τ=K∗I
V=K∗ω
When an electrical source drives this block, the block represents an idealized DC motor. When a torque or angular velocity drives the block, it represents an idealized generator.

Now the rotation of the water wheel is coupled to the rotation of the generator. Like other rotational elements, both the R and C ports of the Rotational Electromechanical Converter block must be connected to your physical network.
 
In this design, the converter represents a generator that has a stationary casing (the stator). Represent this by connecting the C port of the Rotational Electromechanical Converter block to a fixed point.

The generator needs to provide power to a load, such as a battery. A resistor is used to represent a generic load.

STEP 1: 
The damped rotational system is designed with values on the blocks, set as Rotational Damper Block (Damping coefficient = 10 N*m/(rad/s)), Inertia Block (Inertia = 1915 kg*m^2), PS Constant (Constant = 50 N*m). 
A Gear Box block (Mechanical > Mechanisms) is added to the model and the Inertia block is branched to the input port of the Gear Box block with the Gear ratio changed to 0.01.
A Rotational Electromechanical Converter block is added to the model. Then, the R port of the converter is connected to the O port of the Gear Box block. The Constant of proportionality K parameter is set to 0.2 V*s/rad.

<img width="781" height="574" alt="step" src="https://github.com/user-attachments/assets/2c058ca2-4094-40c7-bf36-342c29af794f" />

STEP 2: 
Another second Mechanical Rotational Reference s added to the model. Connect it to the C port of the Rotational Electromechanical Converter block.
A Resistor block is added to the model and connected it to the positive (+) port of the Rotational Electromechanical Converter block. The Resistance parameter is set to 100 Ohm.
 
The Electrical Reference block is added to ground the electrical portion of the model.

<img width="690" height="372" alt="Next Step" src="https://github.com/user-attachments/assets/cc535134-9113-424f-9c31-72b4716b61e8" />




