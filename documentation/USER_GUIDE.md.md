# ARINC661Forge Pro User Guide & Developer Manual

## 1\. Quickstart Guide

1. Launch `index.html` in your browser.  
2. In the "Cockpit Display System Config" panel, select Application ID (e.g. `1`) and Layer ID (e.g. `10`).  
3. Select the target Widget ID (e.g. `Widget #101: A661_PUSH_BUTTON`).  
4. Select the target Parameter ID and input a new value (e.g. `A661_VALUE` \= `250`).  
5. Click **Send Parameter (UA \-\> CDS)** to serialize an `A661_CMD_SET_PARAMETER` binary block with 32-bit word alignment and inspect the hex dissector.  
6. Click **Simulate Event (CDS \-\> UA)** to generate a simulated pilot button click event (`A661_NOTIFY_WIDGET_EVENT`).  
7. Click **Run Normative Audit** to evaluate all 10 ARINC 661-8 protocol compliance checks.

## 2\. Standalone Headless Python CLI

Run automated regression testing and compliance verification:

python3 arinc661-cli.py \--audit \--app 1 \--layer 10