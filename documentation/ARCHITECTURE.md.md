# ARINC Specification 661-8 Technical Architecture & Specification

## 1\. Executive Summary & Protocol Scope

ARINC Specification 661 defines the architecture and communication protocol between Cockpit Display Systems (CDS) and airborne User Applications (UA). Standardized for modern glass cockpits (Airbus A380, A350, Boeing 787), ARINC 661 completely decouples cockpit graphical rendering from mission flight logic.

### Key Architectural Concepts:

1. **Separation of Presentation & Logic**:  
   - The Cockpit Display System (CDS) renders the graphics and manages pilot interactions (pointing devices, knobs, buttons).  
   - The User Application (UA) contains flight management and navigation algorithms, interacting with the CDS solely via runtime parameter messages.  
2. **Definition Files (DF)**:  
   - A pre-compiled binary file describing the static hierarchical layout of display layers, containers, and interactive widgets (`A661_PUSH_BUTTON`, `A661_EDIT_BOX_NUMERIC`, `A661_ARC_DIAL`).  
3. **Runtime Protocol Blocks**:  
   - Communication packets packaged within 32-bit aligned blocks beginning with `A661_BEGIN_BLOCK` (\$0xB0B0\$).  
   - UA-to-CDS: `A661_CMD_SET_PARAMETER` (\$0x5111\$) dynamically modifies widget state (visibility, label, numerical value).  
   - CDS-to-UA: `A661_NOTIFY_WIDGET_EVENT` (\$0x5222\$) delivers pilot interaction events (selection, text entry, drag).  
4. **Safety & DO-178C DAL Isolation**:  
   - Isolates flight-critical user applications (DAL-A) from display software, preventing display unit software crashes from affecting flight control computers.