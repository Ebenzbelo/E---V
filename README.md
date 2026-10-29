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

### Feedback Control (with Simscape and Simulink)
After the model of the hydroelectric generator is designed, the effects of different physical parameters can be explored. For example, how much power the system generates when the reservoir is only half full or the effect of pipe diameter on the rotation rates of the wheel and generator can be measured. More than using physical models to explore these input/output relationships, an understanding of the system dynamics to design a control strategy can be used.

Feedback control is a common strategy used to achieve a desired output, for example, to maintain a specific operating speed. To control the generator speed using feedback control, one would: measure the actual speed, compare it to the desired speed, and use information about that error to decide how much water to send to the water wheel. 

The input/output relationship of a physical system being controlled is called a plant. It is important to note that Simscape is a powerful tool for plant modeling. Simulink, on the other hand, is an ideal tool for implementing feedback control. Feedback control systems in Simulink are laid out the same way as they are depicted in schematics, making them easy to implement and understand. The standard Simulink library even contains implementations of the most common type of controller: a proportional, integral, derivative (PID) Controller.

<img width="753" height="162" alt="Feedback Control" src="https://github.com/user-attachments/assets/9daf7c24-d90d-45ba-8fe5-3501463d43fb" />

The integration of a plant model in Simscape and a control system in Simulink is straightforward. After identifying the plant inputs and outputs, the Simulink-PS Converter and the PS-Simulink Converter blocks are used to create Simulink signals. Then a control loop using standard Simulink blocks is built.

<img width="761" height="124" alt="PS - Simulink connector" src="https://github.com/user-attachments/assets/7d809fa6-26ad-4674-853a-41e712cca9e4" />


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

Once the fluid circuit that divides a pressure-driven flow into two streams and the electromechanical system in which a rotating wheel drives a generator are constructed, they are combined using a converter that connects the Isothermal Liquids domain to the Mechanical Rotational domain.

The Rotational Mechanical Converter (IL) block provides an interface between the pressure of liquid connected to its A port and the torque across its R and C ports. In other words, the torque about the rotating shaft is equal to the pressure drop [N/m2] multiplied by the volume of fluid moved per rotation [m3/rad].
This relationship between fluid pressure and resultant torque is an idealized approximation of how a water wheel works.

Now, to complete the hydroelectric power model, the outlet of one of the pipes is used as the pressure source for the Rotational Mechanical Converter (IL) block. After connecting the fluid model to the electromechanical system, the entire model will be a single physical network. Therefore, only one Solver Configuration block is needed.

To implement a feedback control loop around the hydroelectric generator model, both an input and an output from the plant are needed. The output, which serves as the control objective, is the generator's rotation rate. A sensor which turns the Simscape block value into a Simulink signal is used.
The plant input is the control signal. A valve is added to control how much water flows to the water wheel.
The Local Restriction (IL) block (Isothermal Liquid > Elements), represents a restriction of variable area. A physical signal input controls the open area. This block can be used to model a valve or orifice.

The creation of a control system requires a reference signal (the desired speed), an error calculation, and a controller. Following the blocks from the Simulink libraries:

<img width="387" height="192" alt="Table" src="https://github.com/user-attachments/assets/b8d9013e-a871-4892-9c7f-ab934acce3e4" />

A PI controller is used to manage the generator speed by specifying the valve opening.

To complete the control loop, it is necessary to connect the controller output to the plant input and the plant output to the error calculation.

The simulation runs, but the Diagnostic Viewer reports warnings.These warnings indicate an algebraic loop. An algebraic loop is a circular dependency where a block's input is needed in order to compute a block's output in a given time step.
 
In this example, the model still runs, but this is not true for all models. It is good practice to break algebraic loops when possible. A simple way to break an algebraic loop is by introducing a delay.



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
A Pipe (IL) block (Isothermal Liquids > Elements) and a second Reservoir (IL) block are added.
In the Pipe (IL) block, set the Pipe length parameter to 20 m, the Cross-sectional area parameter to pi*(0.5/2)^2 m^2, and the Hydraulic diameter parameter to 0.5 m.
The inlet of the pipe, port A, is connected to the Pressure Source block while the outlet, port B, is connected to the new Reservoir (IL) block.

Then, the Reservoir (IL) block is deleted at the outlet of the Pipe (IL) block, to create two new copies of the Pipe (IL) block and connect them to the outlet of the original pipe such that the flow is split between them. Two new Reservoir (IL) blocks are added and connected to the open ends of the pipes.

<img width="850" height="365" alt="Step (3)" src="https://github.com/user-attachments/assets/d22aa16b-6f09-4917-bf03-c0c8446d9438" />

STEP 5: The fluid and electromechanical domains need to be connected.
First, the Ideal Torque Source and PS Constant blocks are deleted from the rotational portion of the system.
A Rotational Mechanical Converter (IL) block (Isothermal Liquid > Elements) is added to the model and the rotational mechanical ports (R and C) are connected in place of the Ideal Torque Source block.

In the Rotational Mechanical Converter (IL) block, the Interface volume displacement parameter is set to vol_displacement m^3/rad.

<img width="1279" height="515" alt="Step (4)" src="https://github.com/user-attachments/assets/ab133f55-2a21-4e20-b9c8-bc88d48e4412" />

STEP 6:
Now, one of the Reservoir (IL) blocks connected to one of the outlet pipes in the fluid domain is deleted and that pipe is connected to the Rotational Mechanical Converter (IL) block.
 
Hence, one of the two Solver Configuration blocks is deleted.
<img width="1108" height="493" alt="Fluid and Electromechanical Domains" src="https://github.com/user-attachments/assets/262f75b9-11e4-4321-958c-f92c3dfb4c81" />

STEP 7:
Ideal Rotational Motion Sensor block (Mechanical > Mechanical Sensors) is added and the sensor is connected so that it measures the rotational motion of the generator as a positive value.
Then, a PS-Simulink Converter is added and connected to the angular velocity port, W, of the sensor.

Now, the Pipe block connected to the Rotational Mechanical Converter block is replaced with a Local Restriction (IL) block.
The Minimum restriction area parameter is set to 0.05 m^2 and the Maximum restriction area parameter to 1 m^2.
A Simulink-PS Converter block is added and connected to the physical signal input port (AR) of the Local Restriction block.


<img width="1177" height="534" alt="PS - Simulink converter simulation" src="https://github.com/user-attachments/assets/16a27780-c6a6-4191-87bb-fffdca276d00" /> <img width="1489" height="627" alt="Screenshot (969)" src="https://github.com/user-attachments/assets/9cc43bca-e61a-48b7-8aca-4df92f7fcb7d" />

STEP 8:
The Constant, Sum and Discrete PID Controller blocks are added to the model.
The Constant value parameter is set to 500.
The Sum block's List of signs parameter is changed to |+- (the | symbol helps arrange the ports.) 
Then, the Constant block is connected to the add port (+) of the Sum block, and the Discrete PID Controller block is connected to the output port of the Sum block.

<img width="434" height="77" alt="Control system" src="https://github.com/user-attachments/assets/857b9415-330d-4994-874a-f12ab320d6c4" />

The Block Parameters dialog for the Discrete PID Controller block is open and the Controller parameter is set to PI.
The Sample time parameter is set to 0.05.
These parameters are changed in the Controller parameters section:
Proportional (P): 1e-6
Integral (I): 5e-3

<img width="1385" height="586" alt="Control setting" src="https://github.com/user-attachments/assets/469bb565-2501-4073-8eb1-a5b1b457f988" />

STEP 9:
The output of the Discrete PID Controller block is connected to the Local Restriction block via the Simulink-PS Converter block. Then, the angular velocity measurement is connected to the negative port of the Sum block.

<img width="1042" height="452" alt="STEP (5)" src="https://github.com/user-attachments/assets/410cb95b-6e9d-4317-ad24-31cdfaf843db" />

A Unit Delay block (Simulink > Discrete) is added. The block is connected between the angular velocity measurement and the - port of the Sum block to break the algebraic loop.

<img width="866" height="485" alt="STEP (6)" src="https://github.com/user-attachments/assets/8635c244-9c1e-4f08-9ddb-f75a5a7ef936" />


































