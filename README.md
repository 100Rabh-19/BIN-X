# Smart Waste Sorter

An automated waste segregation system progressing from an Arduino-based proof of concept to a camera-assisted system using embedded control, sensors, and a motorized sorting mechanism.

**Development status:** Basic prototype documented; advanced system in design. The hardware list and costs below are planning estimates based on the current BOM. They are not confirmation that every component has been purchased, integrated, or tested.

## 1. Project Overview

The project aims to detect incoming waste, identify its category, and route it into one of four collection compartments:

- Wet waste
- Dry waste
- Metal waste
- Plastic waste

The development is organized into two stages.

### Stage 1 — Existing Basic Prototype

The existing prototype uses an Arduino-compatible board, an IR sensor, a proximity/inductive sensor, a moisture sensor, a stepper motor and driver, a servo, and a buzzer. The controller reads the sensors and operates the sorting mechanism using fixed motion sequences.

The prototype demonstrates sensor-driven detection and mechanical actuation. Its moisture threshold is a simple heuristic; it does not provide robust four-class waste classification.

### Stage 2 — Advanced System Under Development

The proposed advanced system separates high-level computing from real-time control:

- **Raspberry Pi 5 (4 GB):** camera capture, local computer-vision/AI inference, application logic, logging, and user interface.
- **Raspberry Pi Camera Module 3 Wide:** captures images of incoming waste.
- **ESP32-S3 DevKitC-1:** reads supported sensors and runs the actuator state machine, homing, and fault handling.
- **Sensors:** inlet detection, metal detection, homing, and load-cell measurement.
- **Motion system:** stepper motor and driver for the sorter, with servos for the gate/drop door.
- **Mechanical assembly:** outer body, frame, four collection compartments, chamber, sorting mechanism, access gate, and chute.
- **Power and safety:** separate regulated rails as required, electrical protection, emergency stop, and service-door interlock.
- **Vision interface:** chamber lighting and an IPS HDMI display.
- **Optional connectivity:** a cellular module may be added if remote monitoring is retained in the final design. Confirm whether it is included in the latest BOM before procurement.

## 2. System Architecture

```text
Waste item
    |
    v
Inlet item sensor ----------------------> ESP32-S3
    |                                         |
    v                                         | Sensor status
Camera Module 3 Wide                          |
    |                                         |
    v                                         |
Raspberry Pi 5 (4 GB)                          |
    |                                         |
    | Image classification / destination      |
    +------------ command -------------------->
                                              |
                                              v
                                    ESP32-S3 state machine
                                              |
                                  +-----------+-----------+
                                  |                       |
                                  v                       v
                           Stepper driver             Servo(s)
                                  |                       |
                                  +-----------+-----------+
                                              |
                                              v
                                  Sorting plate / gate / chute
                                              |
                                              v
                                    Four collection bins
                                              |
                                              v
                                   Load-cell measurement
                                              |
                                              v
                                  Logging / display / report
```

This is a conceptual architecture, not a finalized wiring diagram. The Pi-to-ESP32 communication interface and protocol must be selected and documented. Emergency-stop and access-door safety functions must not depend solely on the Pi, AI model, or network connection.

## 3. Hardware Bill of Materials

The following table transcribes the current BOM and its listed costs in Indian rupees. Prices are estimates and should be reconfirmed with suppliers before ordering.

### Mechanical

| Component | Estimated cost |
|---|---:|
| Outer body | ₹9,000 |
| Frame | ₹3,500 |
| Compartments (4) | ₹1,800 |
| Gate, drop door, and sorter | ₹5,000 |
| Chamber interior and window | ₹3,000 |
| Sorter bearing | ₹1,200 |
| Seals and fasteners | ₹1,500 |
| **Mechanical subtotal** | **₹25,000** |

### Power and Safety

| Component | Estimated cost |
|---|---:|
| Main power supply | ₹1,499 |
| Pi power supply | ₹1,235 |
| DC-DC converter, servo rail | ₹900 |
| DC-DC converter, logic | ₹250 |
| Protection | ₹1,800 |
| Emergency stop and safety relay | ₹6,500 |
| Service-door interlock | ₹1,200 |
| Indicators | ₹700 |
| Electrical enclosure and wiring set | ₹5,000 |
| **Power and Safety subtotal** | **₹19,084** |

### Sensing

| Component | Estimated cost |
|---|---:|
| Inlet item sensor | ₹3,500 |
| Metal sensor | ₹767 |
| Homing sensor (sorter) | ₹450 |
| Load cell, inspection chamber | ₹100 |
| Load-cell amplifier | ₹119 |
| **Sensing subtotal** | **₹4,936** |

### Compute and Control

| Component | Estimated cost |
|---|---:|
| Raspberry Pi 5 (4 GB) | ₹12,000 |
| Pi 5 camera cable | ₹395 |
| Pi 5 active cooler | ₹488 |
| ESP32-S3-DevKitC-1 | ₹1,003 |
| Storage | ₹700 |
| Interface PCB | ₹2,500 |
| 24 V input module | ₹550 |
| Output module | ₹450 |
| **Compute and Control subtotal** | **₹18,086** |

### Motion

| Component | Estimated cost |
|---|---:|
| Sorting stepper motor | ₹1,699 |
| Stepper driver | ₹2,699 |
| Gate and drop-door servos | ₹4,200 |
| **Motion subtotal** | **₹8,598** |

### Vision

| Component | Estimated cost |
|---|---:|
| Raspberry Pi Camera Module 3 Wide | ₹3,599 |
| Chamber lighting | ₹800 |
| IPS HDMI display | ₹7,499 |
| **Vision subtotal** | **₹11,898** |

### Estimated Cost Summary

| Category | Estimated subtotal |
|---|---:|
| Mechanical | ₹25,000 |
| Power and Safety | ₹19,084 |
| Sensing | ₹4,936 |
| Compute and Control | ₹18,086 |
| Motion | ₹8,598 |
| Vision | ₹11,898 |
| **Estimated grand total** | **₹87,602** |

The grand total is the sum of the category subtotals above. It excludes any unlisted shipping, taxes, tools, fabrication changes, replacement parts, or contingency. Validate quantities, supplier prices, and exact part numbers before treating this as a procurement budget.

## 4. Hardware Roles and Integration Notes

| Hardware | Intended role | Details to verify |
|---|---|---|
| Raspberry Pi 5 (4 GB) | Image processing, AI inference, logging, UI | Power supply, thermal management, storage, software stack |
| Camera Module 3 Wide | Captures incoming item images | Camera cable/connector, mounting, lighting, focus and field of view |
| ESP32-S3 DevKitC-1 | Sensor and actuator controller | Exact board revision, pin assignment, logic levels |
| Inlet item sensor | Detects item arrival | Exact model, sensing range, output type and voltage |
| Metal sensor | Identifies metal presence | Sensor technology, detection distance, target size and mounting |
| Homing sensor | Establishes sorter reference position | Normally-open/closed behavior, voltage and repeatability |
| Load cell and amplifier | Measures supported load | Load-cell capacity, number of cells, HX711/module details, mechanical load path and calibration |
| Stepper motor and driver | Rotates sorter mechanism | Motor current, torque, supply voltage, driver carrier and cooling |
| Gate/drop-door servos | Opens or moves release mechanism | Model, torque, stall current, travel and mechanical linkage |
| Power supplies and DC-DC converters | Provide required rails | Rail voltage, continuous/peak current, efficiency, thermal derating |
| Emergency stop and safety relay | Provides a hardware safety path | Contact ratings, actuator power interruption, reset behavior |
| Service-door interlock | Inhibits hazardous motion when opened | Switch type and integration with the safety path |
| IPS HDMI display | Local status and user interface | Exact model, power draw and mounting |
| Chamber lighting | Consistent image illumination | Brightness, heat, power, and glare control |

## 5. Schematics and Electrical Design

**Schematic status: not yet finalized.** This repository does not claim that a complete, verified, editable schematic is included. Add the native schematic source and exported PDFs/images when the electrical design is complete.

The final schematic package should include:

1. **System overview:** Pi, ESP32-S3, sensors, motion drivers, power rails, display, and communication interfaces.
2. **Power distribution:** input supply, protection, DC-DC converters, rail ratings, grounding, and connector pinouts.
3. **Pi and ESP32 interface:** chosen communication link, signal direction, voltage compatibility, connector details, and protocol.
4. **Sensor interfaces:** inlet sensor, metal sensor, homing sensor, load-cell amplifier, and any retained environmental sensors.
5. **Motion control:** stepper driver, motor connector, servo power distribution, limit/homing signals, and fault inputs.
6. **Safety circuit:** emergency stop, service-door interlock, actuator power interruption, and manual reset requirements.
7. **Connector and pin schedule:** connector reference, pin number, signal name, voltage, wire details, and destination.
8. **Test points:** named rails and key signals for commissioning and fault-finding.

Before power-on, verify every module's datasheet and pinout, supply voltage, logic-level compatibility, current demand, connector ratings, fuse selection, wire gauge, grounding, thermal limits, and mechanical clearances. Do not connect a 5 V output directly to an ESP32-S3 GPIO; its GPIO operates at 3.3 V logic. Motors must be driven by suitable driver hardware, not directly from controller pins.

The emergency stop must not rely only on firmware. Keep hazardous AC mains wiring enclosed and separate from low-voltage logic. Have the power and safety design reviewed before operating the complete machine.

## 6. Preliminary Mechanical Specifications

These are starting estimates based on the concept CAD views, not measured dimensions from a native CAD assembly.

| Parameter | Preliminary target | Status |
|---|---|---|
| Overall height | Approximately 850 mm | Estimate; confirm in CAD |
| Outer diameter / width | Approximately 500 mm | Estimate; confirm in CAD |
| Empty mass | Approximately 12–20 kg; 15 kg initial target | Estimate; replace with CAD mass properties and a scale measurement |
| Number of collection compartments | 4 | Design intent |
| Combined bin capacity | Approximately 35–50 L | Estimate; verify internal geometry |
| Individual bin capacity | Approximately 8–12 L | Estimate; depends on layout |
| Access opening | Approximately 300 × 400 mm | Preliminary; verify service access |
| Sorting plate diameter | Approximately 300–380 mm | Preliminary; verify mechanism |

### Suggested Materials

| Part | Candidate material | Preliminary guidance |
|---|---|---|
| Outer body | Powder-coated galvanized steel or aluminium sheet | Around 1.5 mm starting point; validate stiffness and fabrication |
| Frame | Aluminium extrusion/angle or steel frame | Around 20 × 20 mm aluminium profile as an initial layout |
| Removable bins | HDPE or PP | Washable, corrosion-resistant purchased bins |
| Sorting plate | SS304 stainless steel | Around 2–3 mm starting point; validate stiffness and actuator torque |
| Base | Steel or reinforced aluminium | Size for stability and the loaded center of gravity |
| Gate and chute | Stainless steel, galvanized steel, or aluminium | Select based on wear, moisture, and cleaning needs |
| Seals | Compatible rubber gasket | Select for the opening and cleaning conditions |
| Electronics enclosure | Suitable enclosed electrical enclosure | Protect against dust, accidental liquid ingress, and contact |

The final material thicknesses and dimensions require mechanical checks for maximum item mass, impact, deflection, fastener loading, corrosion, cleaning, stability, and pinch-point guarding. Confirm that each bin's load is transferred correctly if bin weight is measured.

### CAD Deliverables to Add

- Editable native CAD assembly and source parts
- STEP assembly export
- Dimensioned general arrangement
- Exploded assembly view
- Fabrication drawings and sheet-metal details
- Mounting-hole and connector cutout drawings
- Material and fastener BOM
- CAD mass-properties report
- Photographs of the fabricated assembly, once built

Place these files in `mechanical/cad/`. Concept renders should be labelled as concept renders, not as-built photographs.

## 7. Software and Firmware Plan

### Existing prototype firmware

The Arduino sketch is stored at:

`firmware/existing_prototype/smart_waste_sorter_basic.ino`

The current sketch reads the proximity sensor on D6, IR sensor on D5, moisture sensor on A0, and buzzer on D12. It controls the servo on D7 and uses the `CheapStepper` library with pins 11, 10, 9, and 8.

The code uses fixed delays and fixed motion angles. It should be treated as prototype firmware. The local `int soil` declaration shadows the global variable, and the moisture-to-percentage mapping is not calibrated. Review the actual driver board and wiring against the installed motor before reuse.

### Advanced software plan

- **Raspberry Pi application:** image capture, classifier inference, confidence thresholding, event logging, and display/dashboard.
- **ESP32 firmware:** sensor polling, motion state machine, homing, timeout checks, actuator control, and fault reporting.
- **Communication protocol:** specify message format, command validation, acknowledgement, timeout, and recovery behavior.
- **Data logging:** timestamp, predicted class, confidence, selected destination, measured weight (if available), and success/fault outcome.

Suggested operating states include `IDLE`, `ITEM_DETECTED`, `CLASSIFYING`, `ROUTING`, `VERIFYING`, `LOGGING`, and fault states such as `ESTOP`, `DOOR_OPEN`, `HOMING_FAILED`, `MOTION_TIMEOUT`, `SENSOR_FAULT`, `COMMUNICATION_LOST`, and `UNKNOWN_CLASS`.

## 8. Testing and Validation

Record actual results rather than presenting targets as measured performance.

| Test | Record |
|---|---|
| Power-on and rail stability | Rail voltage, current, temperature |
| Inlet and sensor repeatability | Detection rate, missed events, false triggers |
| Metal detection | Target material, size, orientation, distance, success rate |
| Homing | Success rate and position variation |
| Waste classification | Test samples, confusion matrix, confidence |
| Sorting performance | Correct-sort rate, throughput, jams |
| Load-cell calibration | Reference weights and measurement error |
| Emergency stop | Response during each motion state |
| Service-door interlock | Whether motion is inhibited when open |
| Communication loss | Fault response and recovery behavior |
| Sustained operation | Temperatures, errors, and failure rate |
| Bin removal and cleaning | Access, spills, and serviceability |

## 9. Current Open Items

- Confirm exact part numbers, supplier links, datasheets, quantities, and final costs.
- Confirm the main supply voltage and calculate the full per-rail power budget, including motor and servo peak/stall current.
- Confirm the inlet and metal sensors and validate detection range in the actual mechanical layout.
- Confirm load-cell quantity, capacity, mounting geometry, and amplifier module.
- Confirm stepper motor, driver carrier, servo models, and required torque.
- Finalize the native schematic and run electrical-rule checks.
- Finalize the Pi-to-ESP32 interface and message protocol.
- Add the native CAD assembly, STEP export, and dimensioned fabrication drawings.
- Decide whether cellular connectivity and any environmental/gas sensors remain in scope.
- Measure actual dimensions, mass, classification accuracy, throughput, and power consumption.

## 10. Repository Structure

```text
smart-waste-sorter/
├── README.md
├── docs/
├── hardware/
│   ├── bom/
│   └── architecture/
├── mechanical/
│   └── cad/
├── firmware/
│   ├── existing_prototype/
│   └── advanced_system/
├── images/
└── testing/
```

## 11. Development Principle

The advanced design will be documented as it is verified. Provisional specifications, estimates, and untested concepts are labelled accordingly. A component listed in the BOM is not considered integrated or validated until its part number, wiring, and test results have been recorded.
