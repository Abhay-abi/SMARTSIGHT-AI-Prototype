# SMARTSIGHT AI – AI-Based Driver Drowsiness Detection System

**Educational Prototype for RC Vehicle (Model/Simulation Only)**

> ⚠️ **CRITICAL SAFETY NOTICE**: This is a research/educational prototype for small RC model vehicles only. Do NOT connect this system to any real automobile's steering, throttle, braking, CAN bus, or safety-critical systems.

---

## 📋 System Overview

SMARTSIGHT AI is a complete educational prototype combining:

- **Smart Glasses** with eye/drowsiness detection
- **ESP32-based wireless communication** (ESP-NOW)
- **AI Safe-Side Decision Engine** for emergency stopping
- **Map/GPS location analysis**
- **Obstacle detection sensors**
- **Controlled vehicle deceleration and steering**
- **Indicator control and hazard lights**

### Complete Sequence Flow

```
Driver Drowsy
    ↓
Eye Closed ≥ 1 second
    ↓
Smart Glasses Warning (Buzzer + Vibration)
    ↓
Drowsiness Confirmed
    ↓
Emergency Signal to Vehicle
    ↓
Acquire GPS Location
    ↓
Obtain Map/Road Information
    ↓
Analyze Left & Right Sides
    ↓
AI: Calculate Safety Scores
    ↓
Select Safest Side
    ↓
Activate LEFT/RIGHT Indicator
    ↓
Check Local Obstacles
    ↓
Gradual Side Movement (Differential Motor Control)
    ↓
Gradual Speed Reduction
    ↓
Vehicle Stops at Safe Location
    ↓
Hazard Indicators ON
    ↓
Remain Stopped (Manual Reset Required)
```

---

## 🔧 Hardware Components

### Smart Glasses Unit (ESP32 #1)
- **ESP32 DevKit V1**
- **Digital IR Eye Sensor** (HIGH = open, LOW = closed)
- **Buzzer** (Drowsiness warning)
- **Vibration Motor** (Tactile feedback)

### Vehicle Unit (ESP32 #2)
- **ESP32 DevKit V1**
- **Motor Driver Module** (L298N or similar)
- **2 × DC Geared Motors** (Left & Right wheels)
- **3 × HC-SR04 Ultrasonic Sensors** (Front, Left, Right)
- **2 × Indicator LEDs** (Left, Right)
- **GPS/GNSS Module** (NEO-6M or simulated)
- **RC Vehicle Chassis**
- **Suitable Battery** (LiPo or similar)

### Optional Components
- OLED Display (Real-time status)
- IMU (Gyroscope + Accelerometer)
- Wheel Encoders (Speed verification)
- Raspberry Pi / PC (AI processing)
- Camera (Environmental analysis)

---

## 📁 Repository Structure

```
SMARTSIGHT-AI-Prototype/
├── README.md                          # This file
├── SYSTEM_ARCHITECTURE.md             # Detailed architecture
├── CIRCUIT_DIAGRAM.txt                # Circuit schematic (text format)
├── PINOUT_CONFIGURATION.md            # Complete pin mapping
├── WIRING_TABLE.md                    # Detailed wiring guide
│
├── firmware/
│   ├── ESP32_Glasses.ino              # Smart glasses controller
│   ├── ESP32_Vehicle.ino              # Vehicle motor controller
│   ├── ESP_NOW_Communication.h        # ESP-NOW protocol library
│   └── Sensor_Libraries.h             # Sensor helper functions
│
├── ai_engine/
│   ├── SafeSideDecision.py            # Safe-side AI algorithm
│   ├── MapAnalysis.py                 # Road/map analysis
│   ├── GPSHandler.py                  # GPS/location processing
│   ├── ObstacleDetection.py           # Sensor obstacle logic
│   └── StateManager.py                # Overall system state machine
│
├── simulation/
│   ├── SimulationCore.py              # Main simulation engine
│   ├── MapEnvironment.py              # Simulated road/map
│   ├── VehiclePhysics.py              # Vehicle movement dynamics
│   ├── SensorSimulation.py            # Simulated sensor data
│   └── VisualizationEngine.py         # Real-time visualization
│
├── scenarios/
│   ├── Scenario_A_AwakeDriver.py      # Test: Normal driving
│   ├── Scenario_B_ShortEyeClosure.py  # Test: 0.5s eye closure
│   ├── Scenario_C_Drowsiness.py       # Test: >1s eye closure
│   ├── Scenario_D_LeftSafer.py        # Test: Left side selected
│   ├── Scenario_E_RightSafer.py       # Test: Right side selected
│   ├── Scenario_F_ObstacleDetection.py# Test: Obstacle during maneuver
│   ├── Scenario_G_BothBlockedEdge.py  # Test: Both sides unsafe
│   └── Scenario_H_GPSFailure.py       # Test: GPS/map failure
│
├── tests/
│   ├── test_SafeSideDecision.py       # AI algorithm tests
│   ├── test_MotorControl.py           # Motor PWM tests
│   ├── test_SensorFusion.py           # Sensor integration tests
│   └── test_StateTransitions.py       # State machine tests
│
├── docs/
│   ├── DETAILED_SPECIFICATIONS.md     # Complete tech specs
│   ├── AI_ALGORITHM_EXPLANATION.md    # Safe-side scoring logic
│   ├── SAFETY_RULES.md                # Fail-safe behavior
│   ├── TESTING_GUIDE.md               # How to test each scenario
│   └── TROUBLESHOOTING.md             # Common issues & fixes
│
└── config/
    ├── system_config.json             # System parameters
    └── calibration.json               # Sensor calibration data
```

---

## ⚡ Quick Start

### 1. Hardware Setup
- Wire ESP32 devices according to `PINOUT_CONFIGURATION.md`
- Install motor driver and sensors on RC vehicle
- Assemble smart glasses unit
- Connect GPS module

### 2. Firmware Upload
```bash
# Install Arduino IDE
# Add ESP32 boards: https://github.com/espressif/arduino-esp32
# Upload ESP32_Glasses.ino to glasses ESP32
# Upload ESP32_Vehicle.ino to vehicle ESP32
```

### 3. Run Simulation
```bash
cd simulation/
python SimulationCore.py
```

### 4. Test Scenarios
```bash
cd scenarios/
python Scenario_C_Drowsiness.py  # Example: Test drowsiness detection
```

---

## 🧠 AI Safe-Side Decision Engine

### Scoring Algorithm

```
SAFETY_SCORE = (AVAILABILITY × 0.25) 
             + (SHOULDER_QUALITY × 0.20) 
             + (OBSTACLE_CLEARANCE × 0.25) 
             + (ROAD_ACCESSIBILITY × 0.15) 
             - (BARRIER_PENALTY × 0.10) 
             - (OBSTACLE_PENALTY × 0.05)

Example:
  LEFT_SCORE = 82/100  → Safe shoulder, clear path
  RIGHT_SCORE = 47/100 → Obstacle detected, barrier present
  
  DECISION: SELECT LEFT
  REASON: "Clear shoulder available; obstacle on right side"
```

### Decision Inputs
- Current GPS position (latitude, longitude)
- Current heading (0-360°)
- Current speed (km/h)
- Road geometry from map
- Left/Right availability status
- Obstacle distances (front, left, right)
- Road width
- Traffic information (if available)
- Restricted area data

### Decision Outputs
- **Selected Side**: LEFT or RIGHT
- **Safety Score**: Numerical score per side
- **Reasoning**: Human-readable explanation
- **Confidence Level**: High/Medium/Low
- **Action Commands**: Motor PWM, indicator state, deceleration rate

---

## 🎮 Simulation Scenarios

| Scenario | Test Case | Expected Behavior |
|----------|-----------|-------------------|
| **A** | Driver awake, normal driving | No action; vehicle continues |
| **B** | Eye closed 0.5 seconds | Blink detected; no emergency |
| **C** | Eye closed >1 second | Drowsiness confirmed; emergency activated |
| **D** | Left side safer | LEFT indicator ON; move left |
| **E** | Right side safer | RIGHT indicator ON; move right |
| **F** | Obstacle detected during maneuver | Recalculate; switch sides if needed |
| **G** | Both sides blocked | Stop immediately; hazard lights ON |
| **H** | GPS/map data unavailable | Fail-safe stop; no map-based decisions |

---

## 🛡️ Safety Features

### Eye Detection Logic
- **0–0.7s**: Normal closure / blink (no action)
- **0.7–1.0s**: Buzzer + Vibration warning (alert driver)
- **≥1.0s**: Drowsiness confirmed; emergency protocol activated

### Obstacle Safety Thresholds
- **> 100 cm**: Safe (proceed normally)
- **50–100 cm**: Caution (reduce speed)
- **20–50 cm**: Slow down (increase obstacle awareness)
- **< 20 cm**: Emergency stop (halt vehicle)

### Fail-Safe Behaviors
- ✅ GPS failure → Do NOT attempt map-based movement
- ✅ Map data missing → Stop or maintain current path
- ✅ Sensor failure → Do NOT select unsafe side
- ✅ Wireless loss → Stop model vehicle safely
- ✅ Both sides unsafe → Stop in current position
- ✅ Obstacle during maneuver → Stop/recalculate immediately
- ✅ No valid AI decision → STOP

---

## 📊 System Architecture Layers

```
┌─────────────────────────────────────────┐
│     AI Decision Engine (Python/RPi)     │
│  • Map Analysis                         │
│  • Safe-Side Scoring                    │
│  • Obstacle Fusion                      │
└──────────────────┬──────────────────────┘
                   │
        ESP-NOW & Serial Communication
                   │
┌──────────────────┴──────────────────────┐
│     Vehicle ESP32 Controller             │
│  • Motor Control (PWM)                  │
│  • Indicator Management                 │
│  • Real-time Sensor Fusion              │
│  • Emergency State Machine              │
└──────────────────┬──────────────────────┘
                   │
     ┌─────────────┴─────────────┐
     ▼                           ▼
┌─────────────┐           ┌─────────────┐
│   Motor     │           │  Indicators │
│   Driver    │           │  & Sensors  │
└─────────────┘           └─────────────┘
     │                           │
     ▼                           ▼
  [Wheels]              [LEDs, HC-SR04s]
```

---

## 📝 Pin Configuration Summary

### ESP32 Glasses Unit
| Component | GPIO | Notes |
|-----------|------|-------|
| Eye Sensor | GPIO 27 | Digital input (HIGH=open, LOW=closed) |
| Buzzer | GPIO 25 | Digital output |
| Vibration Motor Driver | GPIO 26 | PWM output |

### ESP32 Vehicle Unit
| Component | GPIO | Notes |
|-----------|------|-------|
| Motor Left (IN1, IN2, ENA) | 18, 19, 23 | PWM for speed control |
| Motor Right (IN3, IN4, ENB) | 21, 22, 5 | PWM for speed control |
| Left Indicator | GPIO 16 | Digital output |
| Right Indicator | GPIO 17 | Digital output |
| Front Ultrasonic Trigger | GPIO 14 | Digital output |
| Front Ultrasonic Echo | GPIO 12 | Digital input |
| Left Ultrasonic Trigger | GPIO 13 | Digital output |
| Left Ultrasonic Echo | GPIO 15 | Digital input |
| Right Ultrasonic Trigger | GPIO 32 | Digital output |
| Right Ultrasonic Echo | GPIO 33 | Digital input |
| GPS RX | GPIO 3 (RX2) | Serial input |
| GPS TX | GPIO 1 (TX2) | Serial output |

---

## 🚀 Running the Complete System

### Step 1: Upload Firmware
```bash
# Using Arduino IDE
1. Install ESP32 board support
2. Open firmware/ESP32_Glasses.ino
3. Select Board: "ESP32 Dev Module"
4. Select COM port for glasses unit
5. Click Upload
6. Repeat for ESP32_Vehicle.ino on vehicle unit
```

### Step 2: Start Simulation
```bash
cd simulation/
python SimulationCore.py --mode=visual
```

### Step 3: Run Test Scenario
```bash
cd scenarios/
python Scenario_C_Drowsiness.py
```

### Example Output
```
================================
SMARTSIGHT AI - Session Start
================================

[00:00] Vehicle initialized
[00:02] Driver eyes: OPEN
[00:05] Driver eyes: OPEN
[00:08] Eyes closing...
[00:09] EYE CLOSURE DURATION: 0.5s - Normal blink detected
[00:15] Eyes closing...
[00:16] EYE CLOSURE DURATION: 2.1s - DROWSINESS DETECTED ⚠️

================================
EMERGENCY PROTOCOL ACTIVATED
================================

[00:16] GPS Location: 15.8497°N, 74.4977°E
[00:16] Current Heading: 90° (East)
[00:16] Current Speed: 35 km/h
[00:17] Map environment loaded

================================
SAFE-SIDE ANALYSIS
================================

LEFT SIDE ANALYSIS:
  • Shoulder availability: EXCELLENT
  • Obstacle distance: 150 cm
  • Road accessibility: HIGH
  LEFT SAFETY SCORE: 82/100

RIGHT SIDE ANALYSIS:
  • Obstacle detected: Car parked
  • Shoulder availability: LIMITED
  • Road accessibility: MEDIUM
  RIGHT SAFETY SCORE: 47/100

DECISION: SELECT LEFT ✓
REASONING: Clear shoulder available; obstacle on right side

[00:17] LEFT INDICATOR: ON
[00:17] Checking local obstacles...
[00:17] Front obstacle distance: 180 cm - CLEAR
[00:17] Left obstacle distance: 240 cm - CLEAR

[00:18] Beginning controlled side movement
[00:18] Left motor PWM: 120, Right motor PWM: 160
[00:19] Reducing speed...
[00:20] Left motor PWM: 100, Right motor PWM: 130
[00:21] Approaching safe area...
[00:22] Left motor PWM: 80, Right motor PWM: 110
[00:25] Deceleration phase...
[00:30] Vehicle speed: 10 km/h
[00:35] Left motor PWM: 20, Right motor PWM: 20
[00:40] VEHICLE STOPPED

================================
SAFE STOP COMPLETED
================================

Final Position: 15.8503°N, 74.4978°E
Location Status: ON LEFT SHOULDER
Hazard Indicators: FLASHING
System Status: WAITING FOR MANUAL RESET
```

---

## 📚 Documentation Files

Each document provides deep technical details:

- **SYSTEM_ARCHITECTURE.md** - Complete system design
- **DETAILED_SPECIFICATIONS.md** - Technical parameters
- **AI_ALGORITHM_EXPLANATION.md** - Safe-side scoring math
- **SAFETY_RULES.md** - Fail-safe protocols
- **TESTING_GUIDE.md** - How to validate each component
- **PINOUT_CONFIGURATION.md** - Exact GPIO mapping
- **WIRING_TABLE.md** - Detailed connection guide

---

## 🔬 For Engineering Colleges

This prototype is designed for:
- **Senior Capstone Projects**
- **Embedded Systems Coursework**
- **Robotics & Autonomous Systems**
- **AI/ML in Automotive Applications**
- **Real-Time Systems Design**
- **IoT & Wireless Communication**

The complete codebase, schematics, and simulation provide a professional demonstration of:
- ESP32 embedded programming
- Wireless communication protocols
- Sensor fusion algorithms
- AI decision-making systems
- Motor control electronics
- Real-time state management
- Fail-safe system design

---

## ⚖️ Legal & Safety Disclaimer

**THIS IS AN EDUCATIONAL PROTOTYPE ONLY**

- ✅ Designed for small RC model vehicles
- ✅ For research and educational purposes
- ✅ Suitable for college demonstrations
- ❌ DO NOT connect to real vehicles
- ❌ DO NOT use for actual driving assistance
- ❌ NOT approved for road use
- ❌ NOT a replacement for actual safety systems

---

## 📞 Support & Questions

For issues, questions, or improvements, open a GitHub issue or discussion.

---

**Version:** 1.0 | **Last Updated:** 2026 | **Status:** Complete Prototype Ready for Educational Use

---

## 📖 Next Steps

1. Review **SYSTEM_ARCHITECTURE.md** for complete design overview
2. Check **PINOUT_CONFIGURATION.md** for GPIO mappings
3. Upload firmware from **firmware/** directory
4. Run simulation from **simulation/** directory
5. Execute test scenarios from **scenarios/** directory
6. Review **TESTING_GUIDE.md** for validation procedures

🎓 **Happy Learning & Building!**
