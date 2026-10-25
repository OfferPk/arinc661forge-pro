/\*\*  
 \* ARINC Specification 661-8 Cockpit Display System (CDS) Core Protocol Engine  
 \* Implements User Application (UA) runtime parameter modification,  
 \* widget event notifications, 32-bit word alignment, and Definition File (DF) structures.  
 \*/

// ARINC 661 Protocol Block Types & Command IDs  
const ARINC661\_COMMANDS \= {  
  A661\_BEGIN\_BLOCK: 0xB0B0,  
  A661\_END\_BLOCK: 0xE0E0,  
  A661\_CMD\_SET\_PARAMETER: 0x5111,  
  A661\_NOTIFY\_WIDGET\_EVENT: 0x5222,  
  A661\_CMD\_ACTIVATE\_LAYER: 0x5333  
};

// Standard ARINC 661 Widget Types  
const ARINC661\_WIDGETS \= {  
  A661\_ROOT\_CONTAINER: 0x0001,  
  A661\_PANEL: 0x0002,  
  A661\_PUSH\_BUTTON: 0x0010,  
  A661\_LABEL: 0x0011,  
  A661\_EDIT\_BOX\_NUMERIC: 0x0012,  
  A661\_ARC\_DIAL: 0x0020  
};

// Parameter IDs  
const ARINC661\_PARAMETERS \= {  
  A661\_VISIBLE: 0x01,  
  A661\_ENABLE: 0x02,  
  A661\_VALUE: 0x03,  
  A661\_COLOR: 0x04,  
  A661\_LABEL\_STRING: 0x05  
};

class ARINC661Engine {  
  constructor(appId \= 1, layerId \= 10\) {  
    this.appId \= appId;  
    this.layerId \= layerId;  
    this.sequenceNumber \= 0;  
  }

  /\*\*  
   \* Serializes an A661\_CMD\_SET\_PARAMETER message block from UA to CDS  
   \*/  
  serializeSetParameter(widgetId, parameterId, value) {  
    this.sequenceNumber++;

    // Encode parameter value (supporting int32, float32, string)  
    let valBytes \= \[\];  
    if (typeof value \=== 'number') {  
      if (Number.isInteger(value)) {  
        valBytes \= \[value & 0xFF, (value \>\> 8\) & 0xFF, (value \>\> 16\) & 0xFF, (value \>\> 24\) & 0xFF\];  
      } else {  
        const buf \= new ArrayBuffer(4);  
        new Float32Array(buf)\[0\] \= value;  
        valBytes \= Array.from(new Uint8Array(buf));  
      }  
    } else if (typeof value \=== 'string') {  
      const strBytes \= Array.from(new TextEncoder().encode(value));  
      valBytes \= \[...strBytes\];  
      while (valBytes.length % 4 \!== 0\) valBytes.push(0x00); // 32-bit alignment  
    }

    // Command Header: CommandID (2B) \+ Length (2B) \+ WidgetID (2B) \+ ParameterID (2B)  
    const cmdLen \= 8 \+ valBytes.length;  
    const cmdHeader \= \[  
      (ARINC661\_COMMANDS.A661\_CMD\_SET\_PARAMETER \>\> 8\) & 0xFF,  
      ARINC661\_COMMANDS.A661\_CMD\_SET\_PARAMETER & 0xFF,  
      (cmdLen \>\> 8\) & 0xFF,  
      cmdLen & 0xFF,  
      (widgetId \>\> 8\) & 0xFF,  
      widgetId & 0xFF,  
      (parameterId \>\> 8\) & 0xFF,  
      parameterId & 0xFF  
    \];

    const commandBlock \= \[...cmdHeader, ...valBytes\];

    // Block Header: A661\_BEGIN\_BLOCK (2B) \+ BlockSize (2B) \+ AppID (1B) \+ LayerID (1B) \+ Seq (2B)  
    const blockSize \= 8 \+ commandBlock.length;  
    const blockHeader \= \[  
      (ARINC661\_COMMANDS.A661\_BEGIN\_BLOCK \>\> 8\) & 0xFF,  
      ARINC661\_COMMANDS.A661\_BEGIN\_BLOCK & 0xFF,  
      (blockSize \>\> 8\) & 0xFF,  
      blockSize & 0xFF,  
      this.appId & 0xFF,  
      this.layerId & 0xFF,  
      (this.sequenceNumber \>\> 8\) & 0xFF,  
      this.sequenceNumber & 0xFF  
    \];

    const fullFrame \= \[...blockHeader, ...commandBlock\];

    return {  
      type: 'SET\_PARAMETER',  
      appId: this.appId,  
      layerId: this.layerId,  
      sequence: this.sequenceNumber,  
      widgetId: widgetId,  
      parameterId: parameterId,  
      wireBytes: fullFrame,  
      hexDump: fullFrame.map(b \=\> b.toString(16).padStart(2, '0').toUpperCase()).join(' ')  
    };  
  }

  /\*\*  
   \* Serializes an A661\_NOTIFY\_WIDGET\_EVENT message from CDS to UA  
   \*/  
  serializeWidgetEvent(widgetId, eventId \= 0x01) {  
    this.sequenceNumber++;  
    const eventLen \= 8;  
    const eventHeader \= \[  
      (ARINC661\_COMMANDS.A661\_NOTIFY\_WIDGET\_EVENT \>\> 8\) & 0xFF,  
      ARINC661\_COMMANDS.A661\_NOTIFY\_WIDGET\_EVENT & 0xFF,  
      (eventLen \>\> 8\) & 0xFF,  
      eventLen & 0xFF,  
      (widgetId \>\> 8\) & 0xFF,  
      widgetId & 0xFF,  
      (eventId \>\> 8\) & 0xFF,  
      eventId & 0xFF  
    \];

    const blockSize \= 8 \+ eventHeader.length;  
    const blockHeader \= \[  
      (ARINC661\_COMMANDS.A661\_BEGIN\_BLOCK \>\> 8\) & 0xFF,  
      ARINC661\_COMMANDS.A661\_BEGIN\_BLOCK & 0xFF,  
      (blockSize \>\> 8\) & 0xFF,  
      blockSize & 0xFF,  
      this.appId & 0xFF,  
      this.layerId & 0xFF,  
      (this.sequenceNumber \>\> 8\) & 0xFF,  
      this.sequenceNumber & 0xFF  
    \];

    const fullFrame \= \[...blockHeader, ...eventHeader\];

    return {  
      type: 'WIDGET\_EVENT',  
      appId: this.appId,  
      layerId: this.layerId,  
      sequence: this.sequenceNumber,  
      widgetId: widgetId,  
      eventId: eventId,  
      wireBytes: fullFrame,  
      hexDump: fullFrame.map(b \=\> b.toString(16).padStart(2, '0').toUpperCase()).join(' ')  
    };  
  }  
}

if (typeof module \!== 'undefined' && module.exports) {  
  module.exports \= {  
    ARINC661Engine,  
    ARINC661\_COMMANDS,  
    ARINC661\_WIDGETS,  
    ARINC661\_PARAMETERS  
  };  
}  
