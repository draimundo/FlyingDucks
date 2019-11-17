# Flying Ducks: A modern solution to fast IoT devices deployment

## 1. Introduction and objectives
Project-Owl[^powl] is a smart open-source and cheap solution to reestablish a basic communication infrastructure in regions hit by a disaster. 

Briefly, interconnected IoT devices called `Ducks`[^duck] provide ad-hoc WiFi coverage locally, allowing civilians to connect and send information on their situation, location and needs. Every 5 `Ducks` are then connected to interconnected to a `MamaDuck` or `PapaDuck`, which in turn create a LoRAWan network, finally connected to an OWL, or central management system. 

This system has been field tested recently, in Puerto Rico[^pr] and Houston, TX [^htx]. These field tests in ideal conditions showed multiple limitations of the system.

In the field tests, which happened in ideal (non disastrous) conditions, the ducks were delivered by car, and even the quickest deployment (Houston), took 90 minutes to place 40 ducks. Moreover, because the ducks are placed at a low level, the network error rate has been as high as 50% in Puerto Rico, and the area covered in Houston (by 40 ducks) was only of a square mile.

Our project aims to alleviate the following problematics:
- Heavy human involvement needed for placement;
- The zone has to be accessible;
- Connectivity in urban/accidented areas.

Additionally the system can provide:
- Supplementary information about civilian locations, by adding a small hardware/software GSM detection component to the network.
- Live monitoring capabilities (air temperature, oxygen concentration, water level, etc) using additional sensors, which could be used to anticipate the extension of the disaster.

## 2. Concept

1. The operator defines a zone where the mesh has to be created;
2. An algorithm computes where the antennas should be placed;
3. Drones deliver boxes containing ducks, and possibly hardware for the extended capabilities;
4. Balloons attached to the boxes are inflated by emptying helium cartridges into them (same principle as lifejackets). The balloons are attached to the boxes by a long (~50m) thin metal wire, which also acts as an antenna.

| ![volcano.jpg](https://i.imgur.com/HQkDdNC.png) | 
|:--:| 
| *Typical steps of a Flying Ducks deployment* |


Flying Ducks can offer:
- usage **anytime**, **anywhere**;
- a **fast** network deployment, with minimal operator exposure;
- **modular capabilities**:
     - IoT devices for **network restablishement** (Project Owl)
     - Sensors for **disaster prediction and monitoring** (temperature, humidity, ...);
     - GSM detection system, to get a **more precise idea of the civilian positions**.

## 3. Parts of the system
### 1. Ducks
The ducks create the LoRa mesh network, allowing communication between the different nodes and the HQ, as well as civilian access to the network through WiFi. The basic duck configuration has the following specs:[^duck]

- **Range:** 400 meters;
- **Throughput:** up to 11'000 bits/sec **enables simple applications with small packets size**;
- **Autonomy**: **about 96 hours** (1000mAh battery);
- **Weight**: **less than 50 grams**;
- **Price**: **less than 30 dollars** (Mainly for the LoRa board[^lb], probably way less if it is mass produced / adapted to the specific situation).


### 2. Balloon
To get good connectivity between ducks a balloon can be used to lift a 50m copper wire. For maximum lift the balloon has a kite shape and hence does use additional wind to keep the antenna upright.

It was estimated that 0.04m³ (~7g helium) can lift approximately 20 gram whereas a 50m AWG 32 copper wire weighs less than 20 gram. For a resilient connection to the balloon an additional string is used. To illustrate how a the balloon looks like a image is shown below.

| ![](https://i.imgur.com/xcjI3mc.jpg) |
|:--:| 
| *Representation of a duck balloon* |

In the example picture a balloon is illustrated which was actually prototyped as a rescue balloon (see [Rescue Me Balloon](https://www.kickstarter.com/projects/651668573/rescue-me-balloon/)).

To estimate the weight of the balloon the weight of a 16gramm CO2 cartridge was used (~60 gram). Since the helium is 10 times lighter the full cartridge will weigh about 50 gram. Furthermore balloon is estimated to weigh 50 grams.

Overall the total weight is shown in the following table:
| Part  |      weight     |
|----------|:-------------:|
| helium & cartridge |  50g
| balloon & valve |  90g
| copper wire | 20g
| Antenna module | 50g 
| **Total** | **210g**


### 3. HQ
The headquarter (HQ) is designed as a basis not only for the operators, but also for the different implemented systems. Built in a safe zone, it will generally integrate the Owl (master link of the Project-Owl duck system), and processing power to process any supplementary data (such as GSM intensities or captor data). 

The processing power needed at the HQ is higher than for the nodes, but should be runnable on any laptop. Autonomy of the HQ has to be considered, but isn't that problematic as the safe zone may still be powered, or a generator could be used to supply electricity.

In an advanced version, the whole solution could be integrated in a container or palletized, easy to deliver by helicopter or cargo plane during a supply drop.


### 4. UI

IBM's emerging technology **Node Red** offers a fast and modulable deployment.

#### Zone Delimitation

The "delimitation zone" view enables the user to define a zone where a LoRAWan network needs to be established by simply dragging lines on the map.


| ![](https://i.imgur.com/tabyMMK.jpg) | 
|:--:| 
| *Designable danger zone* |



#### Mission controller
This is the dashboard where online data of the working drone fleet and the deployed antennas are displayed.


| ![](https://i.imgur.com/iZ7TNen.png) | 
|:--:| 
| *Mission dashboard* |

#### Civilian localization via GSM detection

The HQ maps discovered IMEI number with decibel intensities. This enables the user to detect locations of injured people with the help of triangulization principle.

|![](https://i.imgur.com/0mt10SI.jpg) |
|:--:| 
| *Triangulation map* |




    
### 5. GSM detector
*Note that this part still needs further design validation and implementation, as no open-source technologies for this exist to this day. This section only aims to prove its plausibility in this context.*

The GSM detector works on the fact that powered on mobile phones always emit signals, even if not connected to a network (to try to connect). Using the copper wire as a detector, the signal can be filtered to isolate a specific frequency allocation band (e.g 	880.0 – 915.0MHz for E-GSM-900). Using a low-power DSP, the IMEI of a specific phone can be extracted from the signal and transmitted to the HQ.

While the intensity of emitted signals is relatively low, and the detection power is limited by the low power-constraint, the proximity of different ducks (400m, as specified before) makes this easily realizable, as a cell tower has a 26.9km operation radius[^cellradius].

The current consumption of this detection system can be compensated by adding supplementary battery capacity (26g for 1000mAh[^battr], included in the final dimensioning).

Basing on (at least) three perceived intensities for a specific IMEI, the HQ can triangulate the position of a cell phone with relatively high precision (depending on the detector resolution/precision). This data could then be used to orient the rescue teams more precisely, for example.

### 6. Drones
Unlike the deployed components, the drones are chosen for their performance (also in difficult conditions), their autonomy and reliability, while the price is considered as a secondary objective.

**Considered use-case**:
1) fly 15 minutes to target (>~ 10 km);
2) fly back 10 minutes (>~ 10 km, dropped payload);
3) 20 minutes drop-off time;
4) 4-5kg payload (~20 boxes at 250g)


As an example, the specs of a high-performance multicopter from Digital Eagle[^de]:
| SK-62 Hexacopter Security  |          |
|----------|:-------------:|
| Cruising speed |  5-10 m/s (>18km/h)
| Flight duration | 40-60 min  
| Max payload | 6 kg
| Price | ~10k$
| Year | 2018

Another possibility would be to use a fixed-wing as the ones developped by wingcopter. The specification of the Wingcopter 178 Heavy Lift [^wingcopter] are shown below.
| Wingcopter 178 Heavy Lift  |          |
|----------|:-------------:|
| Max speed hover mode |  50km/h
| Max speed fixed wing mode |  150km/h
| Range with 4kg |  85km
| Wind resistance | 20m/s 
| Max payload | 6 kg
| Year | 2018

Drones with this kind of payload could deliver up to 25 boxes per trip, quickly populating the map.

## 4. Conclusion
This project was inspired by the winner of last year's IBM Codeathon, namely Project Owl. With Project Owl it is possible to establish a working network by placing small access points (ducks) over an area. This network can then be used to gather data and communicate during a catastrophe where infrastructure can be damaged or unavailable.

At this point project "Flying Duck" takes OWL up one step. During a disaster it is not always possible/safe to distribute the ducks by hand in the affected area. Therefore a concept was elaborated to deliver ducks at the desired spots by using drones. With the proposed solution, the access points can be nearly freely distributed over the area since the antenna is lifted by a balloon to achieve good connectivity independently of the environment. This facilitates the task of gathering first data on the heavily affected area. A proposed improvement is the detection of GSM signals to get a more precise picture of where civilians are situated.

Overall, the proposed solution would bring many benefits to many different disaster scenarios like earthquakes, floodings, avalanches or wildfires for example.

[^1]: http://www.project-owl.com/Making_A_DuckLink_September2019_ProjectOWL.pdf
[^pr]: http://www.project-owl.com/ProjectOWL_FieldTestOne_PuertoRico_April2019.pdf
[^htx]: http://www.project-owl.com/ProjectOWL_FieldTestTwo_Houston_Report_August2019.pdf
[^powl]: https://github.com/Project-Owl/
[^duck]: https://github.com/Project-Owl/duck
[^lb]:https://www.amazon.com/MakerFocus-Development-Bluetooth-0-96inch-Display/dp/B076MSLFC9/ref=sr_1_3?th=1
[^cellradius]:http://www.itu.int/ITU-D/tech/events/2003/slovenia2003/Presentations/Day%203/3.3.1_Chandler.pdf
[^battr]:https://www.sparkfun.com/products/13813
[^de]:http://www.digitaleagle-uav.com/products.html
[^wingcopter]: https://wingcopter.com/

