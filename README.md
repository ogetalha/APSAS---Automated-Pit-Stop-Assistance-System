# APSAS - Automated Pit Stop Assistance System

#### still a work in progress!

---------------------------------------------------------------------------------

Formula 1 might be one of the most enthrilling sports of today with its high-performance cars and high-stakes races, yet is notoriously unsafe for drivers that drive these cars under immense pressure.

This system and its 3 main sub-systems aim to make a specific part of an F1 race, namely the Pit Stop, more efficient and safer for the drivers, the pit crew and any other person on the pit lane. 

However, it ensures that the spirit of the competition remains intact and does not intend to unfairly benefit or handicap any driver or their respective teams. 

## The individual systems are as follows: 
### 1. WWES – Wheel, Wing, and Jack Engagement System
WWES manages the physical and mechanical aspects of a pitstop, including tire servicing,
aerodynamic adjustments, and the automated lifting and lowering of the vehicle. This subsystem is
designed to reduce reliance on manual signaling, improve timing consistency, and lower the number of
crew members directly exposed to moving parts.

Subsystems: 
1. Wheel Operations Control 
2. Wing Adjustment Management 
3. Auto-Jack Control Subsystem

Key Functions:
- Tracks crew using RFID and computer vision
- Monitors tool engagement and technician posture to determine task progression
- Detects and manages aerodynamic adjustments
- Raises and lowers the vehicle using laser-detected auto-jack triggers
- Controls a visual floor signal (red/green light) to indicate pit release authorization

### 2. CRPDS – Crew Readiness and Position Detection System
CRPDS safeguards human involvement in the pit stop process by ensuring crew members are where
they should be, when they should be, and are in safe postures before release authorization is
considered.
This subsystem balances real-time positional awareness with role-based logic. It cross-references
crew assignments with allowed zones, posture detection, and timing thresholds.

Technologies Employed:
- Redundant RFID tagging (embedded in gloves, vests, or helmets)
- Real-time computer vision (PoseNet, YOLO) for human pose and zone validation
- Defined pit box zoning (Z1–Z5) to categorize danger and safety areas

Operational Flow:
1. Car enters pit box; crew approach monitored
2. Crew tags are tracked against assigned zones
3. Vision system detects pose and task posture
4. If any crew is detected in restricted zones, or their tag is missing, release is blocked
5. Release is only authorized when all crew are confirmed to have completed their roles and retreated
to safe zones.

Requirements Overview:
- Zone correctness must be verified before and after task execution
- Posture and time windows are required to infer task completion
- Redundancy in detection ensures safety even during RFID dropouts

### 3. PLMS – Pit Lane Monitoring System
The PLMS component of APSAS exists to monitor the wider dynamic environment around the pit box.
While WWES and CRPDS focus on internal operations, PLMS watches the pit lane for high-speed traffic
that may pose a threat during release.
This system operates using computer vision to detect and classify approaching vehicles, assess their
trajectory, and determine whether conditions are safe for release.

Core Functions:
- Detect rearward and lateral pit lane traffic via rear-facing cameras
- Use real-time visual tracking to estimate approach speed and direction
- Classify the severity of potential conflicts (safe, caution, block)
- Integrate with APSAS logic to override release when unsafe

Decision Logic:
- If any vehicle is approaching above a critical speed within a defined proximity, the system withholds
the green light
- Only when no threats are detected and all internal systems report ready does PLMS
contribute a
"
safe to release
"
signal

Requirements Overview:
- Vision system must maintain accuracy under varied lighting and weather conditions
- Threat classification logic must operate within tight latency windows
- System must react in under 50ms to any sudden threat emergence

## Cross-System Readiness Integration
APSAS uses a simple boolean AND logic to integrate signals from all three subsystems.
Release is only allowed when all systems report their respective
"READY"
status.
ifWWES_READY && CRPDS_READY && PLMS_READY:
Signal Release = GREEN
else:
Signal Release = RED
