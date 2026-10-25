  
661

# **ARINC661Forge Pro**

v1.0.0  
ARINC 661-8 CDS PROTOCOL ENGINE ACTIVE Run Normative Audit

## **Cockpit Display System Config**

Application ID / Layer ID:  
Target Widget ID / Type: Widget \#101: A661\_PUSH\_BUTTON (Autopilot Master) Widget \#102: A661\_EDIT\_BOX\_NUMERIC (Target Altitude) Widget \#103: A661\_ARC\_DIAL (Airspeed Indicator)  
Parameter ID & New Value: A661\_VISIBLE (1 \= True) A661\_ENABLE (1 \= True) A661\_VALUE (Integer / Float) A661\_LABEL\_STRING (Text)  
Send Parameter (UA → CDS) Simulate Event (CDS → UA)

BLOCK HEADER ALIGNMENT  
32 bits  
✓ ARINC 661 Compliant

ACTIVE COCKPIT LAYER  
\#10  
✓ Flight Control DU

COMMAND FRAMING  
0x5111  
✓ A661\_SET\_PARAMETER

### 

10-Point Normative ARINC 661-8 Compliance Scorecard

### 

Runtime Protocol Binary Block & Dissector

