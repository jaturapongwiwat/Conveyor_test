Automated Sorting & Production Line

Status: Test Version (v0.1) — Ladder Logic only. 

-----------------------------------------

Overview

This project simulates an industrial conveyor line that detects incoming products and sorts them into GOOD and REJECT. The control logic is built as a Step Sequence (State Machine) in Mitsubishi GX Works3 Ladder Logic, using Global Labels throughout instead of raw device addresses.

At this stage, only the PLC ladder logic has been written and tested in offline simulation. No external systems (Factory I/O, SCADA, Database) are connected yet.

Product Flow
START
  → Conveyor Run
  → Detect Product
  → Classify (Good / Reject)
  → Move to Sorting Position
  → Sort (Pass-through GOOD / Push REJECT)
  → Exit Sensor Confirms
  → Count
  → Cycle Reset → back to waiting for next product
Step Sequence (State Machine)

The core logic is driven by a step sequence rather than flat ladder logic, to keep the program easy to read and debug:

-----------------------------------------

STEP_IDLE

Condition: Start pressed + System Ready

Next: STEP_CONVEYOR

STEP_CONVEYOR

Condition: Product detected

Next: STEP_PRODUCT_DETECT

STEP_PRODUCT_DETECT

Condition: Classified + move timer elapsed

Next: STEP_MOVE_TO_SORT

STEP_MOVE_TO_SORT (Branching)

If STEP_SORT_GOOD:

Condition: Good exit sensor

Next: STEP_COMPLETE

If STEP_SORT_REJECT:

Condition: Reject exit sensor

Next: STEP_COMPLETE

STEP_COMPLETE

Condition: cycle reset timer

Next: Loop back to STEP_CONVEYOR

-----------------------------------------

Each step is implemented with SET/RST instructions rather than plain OUT, so the state stays latched until its exit condition is explicitly met — this avoids steps dropping out mid-cycle and makes the sequence behave as a true state machine.

What's Included in This Version
Full I/O list and Global Label mapping
Step Sequence ladder logic (Start/Stop, Auto/Manual, Emergency, Sorting, Counters, Alarms, Stack Light)
Interlock & safety logic (Emergency Stop, Fault handling, Auto/Manual mutual lockout)
Verified in GX Works3 offline simulation
Not Yet Implemented
Factory I/O connection (machine/sensor simulation)
SCADA monitoring (Ignition)
Production and alarm data logging to database