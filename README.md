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

Like other domains, modeling in the Isothermal Liquid domain requires both a Solver Configuration block and a domain-specific reference—in this case, a Reservoir (IL) block. The Reservoir (IL) block represents an infinite volume of liquid at the specified pressure. The default pressure is atmospheric.
In addition, the properties of the liquid are specified in the network using the Isothermal Liquid Properties (IL) block. The default liquid is water.

The Pressure Source (IL) block (Isothermal Liquids > Sources) can be used to specify a pressure differential between ports A and B, and the pressure can be specified by an external physical signal or a constant value entered as a block parameter. The block icon changes to indicate this.
A positive value causes the pressure at port B to be greater than port A. In this system, the pressure difference is constant, due to hydrostatic pressure, PB − PA =ρgh.

A pressure differential drives water flow from high pressure to low pressure. In this simple fluid circuit, water flows through a pipe driven by hydrostatic pressure (ρgh).

In the hydroelectric power model, some of the water leaving the pipe goes to the water wheel, and some is diverted to the overflow reservoir.
First, a fluid circuit that splits the flow between the two outlet pipes is built.

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

STEP 3: For the fluid domain, 
A Reservoir (IL) block (Isothermal Liquid > Elements) and an Isothermal Liquid Properties (IL) block (Isothermal Liquid > Utilities) are added to your model.
A Pressure Source (IL) block is added to your model. The Source type parameter is changed to Constant and set the Pressure differential parameter to rho*g*h_res Pa. Connect port A to the Reservoir (IL) block.

<img width="837" height="608" alt="Step (2)" src="https://github.com/user-attachments/assets/ae1845f2-1a17-40c2-abce-f2b25f2eb57a" />

STEP 4: 
Add a Pipe (IL) block (Isothermal Liquids > Elements) and a second Reservoir (IL) block.
In the Pipe (IL) block, set the Pipe length parameter to 20 m, the Cross-sectional area parameter to pi*(0.5/2)^2 m^2, and the Hydraulic diameter parameter to 0.5 m.
Connect the inlet of the pipe, port A, to the Pressure Source block. Connect the outlet, port B, to the new Reservoir (IL) block.

Then, the Reservoir (IL) block is deleted at the outlet of the Pipe (IL) block, to create two new copies of the Pipe (IL) block and connect them to the outlet of the original pipe such that the flow is split between them. Two new Reservoir (IL) blocks are added and connected to the open ends of the pipes.

<img width="850" height="365" alt="Step (3)" src="https://github.com/user-attachments/assets/d22aa16b-6f09-4917-bf03-c0c8446d9438" />









