Real-time Upgrade Common Code Base

Functional Requirements and Test Plan

Matt Rippa

Document Version 1.0

March 2016

References

[1] `EPICS Record Reference
Manual <https://wiki-ext.aps.anl.gov/epics/index.php/RRM_3-14>`__

[2] `EPICS Application Developer’s
Guide <https://drive.google.com/open?id=0B4C51TY6UtsQc1lfOUdUdVk3SUk>`__

[3] `Bancomm635VME User's
Guide <http://www.microsemi.com/document-portal/doc_view/133468-bc635vme-user-s-guide>`__

[4] `Xycom-240 Digital
I/O <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQUkk5Szk3NkU1dTQ/view?usp=sharing>`__

[5] `Xycom-566 Analog
Input <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQenNNWndvd3QyVGc/view?usp=sharing>`__

[6] `PMAC User
Manual <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQMFRUMVFpa0hBNFk/view?usp=sharing>`__

[7] `PMAC Software Reference
Manual <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQb1NibjdERVVHS0k/view?usp=sharing>`__

[8] `PMAC Hardware Reference
Manual <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQUlVkaHR2Tm9xQjQ/view?usp=sharing>`__

[9] `PMAC Dual-Ported
Ram <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQdkdxb0Z1WkJZUG8/view?usp=sharing>`__

[10] `PMAC Quick Reference
Guide <https://drive.google.com/a/gemini.edu/file/d/0B4C51TY6UtsQc2Z2bl9PUEpVUGs/view?usp=sharing>`__

[11] VMIC VME 5588 Reflective Memory

1 Introduction
==============

The Real-time Upgrade Project involves upgrading the operating system,
CPU board support packages and related VME board drivers for telescope
facility systems. The Common Code-Base (CCB) elements are primitive
software components which are used to design and implement the telescope
facility systems such as the Telescope, Mount and Primary Control
Systems.

In most cases the CCB elements have undergone significant refactoring to
be compatible with the EPICS Operating System Independent (OSI)
libraries. As such, the requirements and testing planned for each CCB
component is reviewed here in this document.

-  Section 2 of this document describes the common code base
      requirements for basic functionality of each system.

-  Section 3 of this document describes the test plan for each document.

2 Component Requirements 
========================

2.1 IOC-Stats (aka devIocStats)
-------------------------------

IOC-Stats is an EPICS community library providing common system
information such as CPU load, memory consumption and other resources
such as file handles. As this is the replacement library for the vxworks
based VxStats, the requirements are that each system can include the
IOC-Stats library into the build and load the database and records at
startup.

2.1.1 Specific Requirements for iocStats at Gemini
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.1.1.1 REQ-101-01 Largest free memory block
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the size of largest free block.

2.1.1.2 REQ-101-02 CPU Usage
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the estimated percent CPU usage by the tasks.

2.1.1.3 REQ-101-03 Suspended Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the number of suspended tasks

2.1.1.4 REQ-101-04 File Descriptors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the number of file descriptors currently in use

2.1.1.5 REQ-101-05 CA Clients
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the number of current CA clients

2.1.1.6 REQ-101-06 CA connections
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the number of current CA connections

2.1.1.7 REQ-101-07 Number of Records
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall report the number of records on the IOC

2.1.1.8 REQ-101-08 Restart control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The IOC shall provide the utility of rebooting itself by processing a
Subroutine Record with the SNAM= rebootProc.

This is provided by the $(ioc):SysReset record in the iocStats module.

2.1.2 Snapshot of EPICS IOC-Stats running on RTEMS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This looks out of context as-is… move to the IOC-Stats introduction.

|ioc_stats_rtems.jpg|

See the `iocStats
website <http://www.slac.stanford.edu/grp/ssrl/spear/epics/site/devIocStats/>`__
for full history and documentation.

2.2 Sequencer and State Notation Code (aka SNCSEQ)
--------------------------------------------------

The Sequencer is a community library providing a compiler for the State
Notation Language (SNC). The Sequencer is executed at runtime to load
State Sets. The user manual and package history can be found at
http://www-csr.bessy.de/control/SoftDist/sequencer/index.html

2.2.1 Gemini Specific Requirements for the Sequencer and SNC
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.2.1.1 REQ-102-01 Sequencer Demo Program
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

An IOC shall be capable of running the demo.st sequencer program
provided in the `test harness <#epics-test-harness-1>`__. This program
ramps an epics analog input value from 0 to 10 volts and back to 0 volts
before repeating the ramp. When the voltage goes above a certain high
limit (say 5.5 volts), a different epics record (call it a light bulb)
changes state from a 0 to 1. The process repeats and as the ramp voltage
continues to hit 10 volts and reverse, the light bulb record turns back
to a 0 when the voltage drop below a lower limit (say 5.0 volts).

2.2.1.2 REQ-102-02 Legacy State Notation Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All legacy state notation code shall be capable of being compiled and
running with the currently selected versions of SNCSEQ.

2.3 Gemini Records (geminiRec)
------------------------------

The geminiRec libraries provide the implementation for EPICS records
used exclusively at Gemini.

================ =========== =======
Records Type     Description Used By
================ =========== =======
genSubRecord                 
applyRecord                  
cadRecord                    
carRecord                    
sirRecord                    
cmdTimeoutRecord             
loadRecord                   
lutinRecord                  
lutoutRecord                 
statusRecord                 
================ =========== =======

2.3.1 Specific Requirements for geminiRec support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.3.1.1 REQ-103-01 genSubRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The genSubRecord shall support all legacy genSub records at Gemini.

2.3.1.2 REQ-103-02 applyRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The applyRecord shall support all legacy apply records at Gemini.

2.3.1.3 REQ-103-03 cadRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The cadRecord shall support all legacy CAD records at Gemini.

2.3.1.4 REQ-103-04 carRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The carRecord shall support all legacy CAR records at Gemini.

2.3.1.5 REQ-103-05 sirRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The sirRecord shall support all legacy SIR records at Gemini.

2.3.1.6 REQ-103-06 cmdTimeoutRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The cmdTimeoutRecord shall support all legacy cmdTimeout records at
Gemini.

2.3.1.7 REQ-103-07 loadRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The loadRecord shall support all legacy load records at Gemini.

2.3.1.8 REQ-103-08 lutinRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The lutinRecord shall support all legacy lutin records at Gemini.

2.3.1.9 REQ-103-09 lutoutRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The lutoutRecord shall support all legacy lutout records at Gemini.

2.3.1.10 REQ-103-10 statusRecord
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The statusRecord shall support all legacy status records at Gemini.

2.4 pvload
----------

The Process Variable Load support library is a vendor supported library.

2.4.1 Specific Requirements for pvload at Gemini
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.4.1.1 REQ-104-01 Identity Record
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All IOC’s shall advertise their software release version in an EPICS
stringin record named, “<ioc_name>:identity”. The value of the identity
record shall be equal to the release number in the production directory,
i.e., “/gem_sw/prod/<EPICS_RELEASE>/ioc/<ioc_name>/<release_number>”,
and reflected in the <ioc_name>App/config/identity.pv pvload file.

2.4.1.2 REQ-104-02 Legacy pvload files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Legacy pvload files shall be processed at boot time with no errors and
function in the same manner as legacy systems.

2.5 Bancomm 
-----------

The bancomm support library provides both driver and device support for
EPICS. The driver layer gives read and write access to the 635/637 board
registers whereas the device layer implements epics record specific
implementation. Since this driver was porting for OSI compatibility, a
thorough set of functional testing will be carried out.

2.5.1 Database Definition File (bancomm.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Bancomm database definition file defines the EPICS device and driver
requirements.

   variable( altIntCounter, int )

   variable( bcIntCounter, int )

   device(ai, VME_IO, devAiBc635, "Bancomm 635")

   device(ao, VME_IO, devAoBc635, "Bancomm 635")

   device(stringin, VME_IO, devSiBc635, "Bancomm 635")

   device(stringout, VME_IO, devSoBc635, "Bancomm 635")

   driver(drvBc635)

2.5.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following device support routines are required for the Bancomm
635/637 board.

1. devAiBc635 -- Provide Analog Input support for VME_IO including Scan
      I/O Interrupt support. Calls
      `bcIntEnable() <#kix.rrnoni7xhdye>`__.

2. devAoBc635 -- Provide Analog Output support for VME_IO Passive
      Scanning only.

3. devSiBc635 -- Provide String Input support for VME_IO including Scan
      I/O Interrupt support. Calls
      `bcIntEnable() <#kix.rrnoni7xhdye>`__.

4. devSoBc635 -- Provide String Output support for VME_IO Passive
      scanning only.

2.5.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The bancomm 635/637 driver provides low-level access to the boards time
registers to acquire the current time as provided by either GPS or NTP.
The driver shall provide the following routines for access to external
support libraries such as timelib.:

   extern void BCconfigure(const int, const int, const int, const int,
   const int);

   extern int `bc635IntEnable <#kix.rrnoni7xhdye>`__\ (const unsigned
   short, const char \*);

   extern int bc635_read(double \*);

   extern int bc635_write(const unsigned short, const double);

   extern void bcIntConnect(void (*isrproc)(const int n));

   extern void bcIntDisconnect(void);

   extern int bcSendTfp(char \*);

   extern void bcSetRTC(void);

   extern int bcSetEpoch (const int);

   extern int bcRegsToTime (double \*, unsigned char \*);

   extern int bc635_report (int);

   extern int bc635_init (void);

   extern int bcTestCard( void );

   extern int bcGetGpsLeap( void );

+----------------+----------------+----------------+----------------+
| **Function**   | **Used By**    | **Meaning**    | **Return       |
|                |                |                | value**        |
+================+================+================+================+
| BCconfigure    | Timelib:       | Configure the  | N/A            |
|                |                | Bancomm time   |                |
|                | t              | interface.     |                |
|                | imeClockInit() | Define startup |                |
|                |                | configuration  |                |
|                |                | options for    |                |
|                |                | the Bancomm    |                |
|                |                | time card to   |                |
|                |                | be called      |                |
|                |                | direct from    |                |
|                |                | the RTEMS      |                |
|                |                | shell or       |                |
|                |                | startup file   |                |
|                |                | before         |                |
|                |                | iocInit. (In   |                |
|                |                | practice, this |                |
|                |                | is called by   |                |
|                |                | ti             |                |
|                |                | meClockInit(), |                |
|                |                | which is       |                |
|                |                | called in the  |                |
|                |                | startup file   |                |
|                |                | before         |                |
|                |                | iocInit).      |                |
+----------------+----------------+----------------+----------------+
| bc635IntEnable | Drv635:        | Enable         | OK,ERROR       |
|                | BCconfigure()  | interrupts on  |                |
|                |                | the Bancomm    |                |
|                | Dev635:        | 635 card.      |                |
|                |                |                |                |
|                | initRecordAi() |                |                |
|                |                |                |                |
|                | initRecordSi() |                |                |
+----------------+----------------+----------------+----------------+
| bc635_read     | Timelib        | Read time --   | OK,ERROR       |
|                |                | Read the       |                |
|                | timeInit()     | current time   |                |
|                |                | registers and  |                |
|                |                | return the     |                |
|                |                | time as the    |                |
|                |                | number of      |                |
|                |                | seconds (type  |                |
|                |                | double value). |                |
+----------------+----------------+----------------+----------------+
| bc635_write    | Dev635:        | - Set time     | OK,ERROR if    |
|                |                | coincidence    | not signal 3   |
|                | write_ao(),    | strobe -- For  |                |
|                | write_so()     | signal number  |                |
|                |                | 3 (set TOD     |                |
|                |                | interrupt)     |                |
|                |                | write out to   |                |
|                |                | the Bancomm as |                |
|                |                | follows :      |                |
|                |                |                |                |
|                |                | Set Time Of    |                |
|                |                | Day for time   |                |
|                |                | coincidence    |                |
|                |                | interrupt.     |                |
|                |                | Input value is |                |
|                |                | time for the   |                |
|                |                | interrupt      |                |
|                |                | (seconds since |                |
|                |                | midnight).     |                |
+----------------+----------------+----------------+----------------+
| bcIntConnect   | Not used       | Register a     | N/A            |
|                |                | user defined   |                |
|                |                | interrupt      |                |
|                |                | service        |                |
|                |                | routine to be  |                |
|                |                | called on      |                |
|                |                | every Bancomm  |                |
|                |                | periodic       |                |
|                |                | interrupt.     |                |
+----------------+----------------+----------------+----------------+
| b              | Not used       | Disconnects a  | N/A            |
| cIntDisconnect |                | previously     |                |
|                |                | defined user   |                |
|                |                | interrupt      |                |
|                |                | routine for    |                |
|                |                | handling       |                |
|                |                | Bancomm        |                |
|                |                | periodic       |                |
|                |                | interrupts.    |                |
+----------------+----------------+----------------+----------------+
| bcSendTfp      | Drv635:        | Configure      | OK, or ERROR   |
|                |                | 635/637 for    | on timeout.    |
|                | EPICS Device   | specific modes |                |
|                | Support        | (i.e., Master, |                |
|                | “init()” call. | Slave,         |                |
|                |                | LeapSeconds    |                |
|                |                | (on|off), or   |                |
|                |                | bcoffset       |                |
|                |                | control)       |                |
|                |                |                |                |
|                |                | Send a string  |                |
|                |                | to the bc635   |                |
|                |                | output FIFO by |                |
|                |                | writing        |                |
|                |                | successive     |                |
|                |                | bytes to the   |                |
|                |                | fifo address.  |                |
|                |                | This routine   |                |
|                |                | delimits the   |                |
|                |                | supplied       |                |
|                |                | string with    |                |
|                |                | <SOH>...<ETB>. |                |
|                |                | The Bancomm    |                |
|                |                | ACK register   |                |
|                |                | is set to 0x95 |                |
|                |                | to cause TFP   |                |
|                |                | to take        |                |
|                |                | action, then   |                |
|                |                | wait for TFP   |                |
|                |                | acknowledge    |                |
|                |                | bit set        |                |
|                |                | (0x01).        |                |
+----------------+----------------+----------------+----------------+
| bcSetRTC       | Not used       | Set            | N/A            |
|                |                | Battery-backed |                |
|                |                | clock --Load   |                |
|                |                | the Bancomm    |                |
|                |                | real-time      |                |
|                |                | clock (RTC)    |                |
|                |                | using the      |                |
|                |                | current NTP    |                |
|                |                | time           |                |
+----------------+----------------+----------------+----------------+
| bcSetEpoch     | Drv635:        | Set epoch year | OK             |
|                |                | -- calculate   |                |
|                | Only used      | the start of   | Needs          |
|                | internally     | year epoch     | Protection     |
|                |                | (wrt 1970)     | from ERROR     |
|                |                | using mktime,  |                |
|                |                | given year.    |                |
|                |                | Set the value  |                |
|                |                | in the global  |                |
|                |                | var            |                |
|                |                | bcYearEpoch.   |                |
+----------------+----------------+----------------+----------------+
| bcRegsToTime   | Drv635:        | - Convert bc   |                |
|                |                | Time or Event  |                |
|                | Only used      | regs to time   |                |
|                | internally     | -- Takes a mem |                |
|                |                | copy of the    |                |
|                |                | EVENT or TIME  |                |
|                |                | regs and       |                |
|                |                | returns the    |                |
|                |                | time as the    |                |
|                |                | number of      |                |
|                |                | seconds (type  |                |
|                |                | double value). |                |
|                |                |                |                |
|                |                | \* RETURNS:    |                |
|                |                |                |                |
|                |                | OK or errors   |                |
|                |                | (no Bancomm or |                |
|                |                | Bancomm status |                |
|                |                | bit field)     |                |
+----------------+----------------+----------------+----------------+
| bc635_report   | Drv635:        | - Report       | OK             |
|                |                | Bancomm        |                |
|                | EPICS Device   | 635/637        |                |
|                | Support        | status. This   |                |
|                | “report()”     | is called from |                |
|                | call.          | the use of     |                |
|                |                | dbior drvBc635 |                |
|                |                | (i             |                |
|                |                | nterest_level) |                |
|                |                | .              |                |
+----------------+----------------+----------------+----------------+
| bc635_init     | EPICS iocInit  | - EPICS DRIVER | OK, ERROR      |
|                |                | INIT           |                |
|                |                | --Initialize   |                |
|                |                | bc635/637 --   |                |
|                |                | called from    |                |
|                |                | iocInit        |                |
+----------------+----------------+----------------+----------------+
| bcTestCard     | Drv635         | EPICS iocInit  | OK if card     |
|                |                | - EPICS DRIVER | present, ERROR |
|                |                | INIT           | if absent      |
|                |                | --Initialize   |                |
|                |                | bc635/637 --   |                |
|                |                | called from    |                |
|                |                | iocInit        |                |
+----------------+----------------+----------------+----------------+
| bcGetGpsLeap   | Drv635:        | - Return the   | Integer number |
|                | bc635_init()   | current value  | of seconds.    |
|                |                | of GPS leap    |                |
|                |                | seconds.       |                |
|                |                |                |                |
|                |                | The current    |                |
|                |                | value of GPS   |                |
|                |                | leap seconds   |                |
|                |                | is obtained    |                |
|                |                | from a Bancomm |                |
|                |                | 637 card by    |                |
|                |                | sending an O2  |                |
|                |                | packet request |                |
|                |                | to the TFP.    |                |
|                |                | The value      |                |
|                |                | returned is    |                |
|                |                | zero on a      |                |
|                |                | bc635          |                |
|                |                | (non-GPS)      |                |
|                |                | card.          |                |
+----------------+----------------+----------------+----------------+

Further, the driver shall register itself with the epics generalTime
libraries adding the Bancomm driver to the list of available Time
Providers.

2.5.4 OSI Specific Challenges 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Bancomm board is required to stop and start the operating system
clock and provide it’s own tick pulse. See
`timeClockInit() <#fts0i87foetw>`__ for the description of changing the
system clock tick during startup scripts for different systems. Eg., the
MCS sets the tick resolution to 80 ticks per second, and the WFS’s and
SCS set the tick resolution to 200 ticks per second. The OSI layer of
epics base does not implement all VxWorks calls. We found that
controlling the system clock (as required by the Bancomm driver) needed
to use operating system dependent calls.

2.5.5 epicsGeneralTime
~~~~~~~~~~~~~~~~~~~~~~

The generalTime framework provides a mechanism for several time
providers to be present within the system. There

are two types of provider, one type for the current time and one type
for providing Time Event times. Each time

provider has a priority, and installed providers are queried in priority
order whenever a time is requested, until one

returns successfully. Thus there is a fallback from higher priority
providers (smaller value of priority) to lower priority

providers (larger value of priority) if the higher priority ones fail.
Each architecture has a “last resort” provider,

installed at priority 999, usually based on the system clock, which is
used in the absence of any other provider.

Targets running vxWorks and RTEMS have an NTP provider installed at
priority 100.

The Bancomm driver will register itself as the highest priority time
provider with priority 20. See specific requirements below.

2.5.6 Bancomm Board Jumper and Mode Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The jumper locations for the Rev. A through Rev. F TFP versions are
shown in Figure 2-2. The Rev. G and up along with the P100004 version
jumpers are shown in Figure 2-3. The jumper blocks are not drawn to
scale in order to make the numbers more visible. It may be helpful to
refer to the schematic diagrams to obtain a clearer idea of the function
of each jumper option.

-  JP1

..

   With the jumper in the 1-2 position the TFP is configured to use DC
   level shift input timecode. In the 3-4 or open position the TFP is
   configured to use modulated timecode.

-  JP2 (GPS Option)

..

   In the 1-2 position the TFP is configured to use a single ended 1pps
   GPS input. In the 3-4 position the TFP is configured to use a
   differential 1pps GPS input.

-  JP3 (GPS Option)

..

   In the 1-2 position the TFP is configured to use the ACUTIME Smart
   Antenna or SV-6 as the GPS sensor. In the 3-4 position the TFP is
   configured to use the TANS as the GPS sensor. The ACUTIME, SV-6, and
   TANS are GPS sensor options that are available from Datum, Inc. This
   jumper is not present on the P100004 model boards.

-  JP4

..

   The jumpers in the JP4 group are designed to be moved as a pair.
   Positions 3-4 and 5-6 define one configuration, and positions 1-2 and
   7-8 define a second configuration. In the default configuration the
   TFP is configured with an auxiliary RS-422 output. In the second
   configuration the TFP is configured ina daisy-chain mode (the RS-422
   input is jumpered to the RS-422 output). This jumper set is intended
   to be used in a digital synchronization mode. At the present time
   this mode has not been implemented. This jumper is not present on the
   P100004 model boards.

-  JP5

..

   In the 1-2 position this jumper places a “100Ω” load between the
   RS-422 input lines. In the 3-4 position the “100Ω” load is bypassed.
   When the TFP is the terminal device on an RS-422 daisy chain the load
   should be used. When the TFP is not at the end of the chain the load
   should be omitted.

-  JP6

..

   In the 1-2 position this jumper places GROUND on P2 pin C12. In the
   2-3 position the 1, 5, 10MHz clock is driven out of P2 pin C12. On
   the model P100004 boards, this jumper is implemented as a 2x2 pin
   block. A shunt on pins 2 and 4 enables the 10MHz output on P2 pin
   C12. A shunt on pins 1 and 2 disables the output by grounding P2 pin
   C12.

Operational Jumper Settings for GPS Locking on 637

==
\    
\    
==

Operational Jumper Settings for GPS Locking on 635

==
\    
\    
==

2.5.7 EEPROM Version Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For successful use of the 637 boards Post May 3rd, 2015, the EEPROM must
be upgrade to version XXXX.

2.5.8 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.5.8.1 REQ-105-01 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Bancomm support library shall be capable of executing the
bc635_init() routine without error by calling iocInit().

2.5.8.2 REQ-105-02 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvBc635( <interest_level>) shall display the
state of all data channels respective for that card. See
`bc635_report <#8mo1uwbcs4cn>`__.

2.5.8.3 REQ-105-03 Analog Input Record Support (Scan I/O Interrupt)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Bancomm support library shall be capable of initializing and
processing an EPICS Analog Input record with its DTYP set to drvBc635
and Scan I/O Interrupt support.

2.5.8.4 REQ-105-04 Analog Output Record Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Bancomm support library shall be capable of initializing and
processing an EPICS Analog Output record with its DTYP set to drvBc635.

2.5.8.5 REQ-105-05 String Input Record Support (Scan I/O Interrupt)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Bancomm support library shall be capable of initializing and
processing an EPICS String Input record with its DTYP set to drvBc635
and Scan I/O Interrupt support.

2.5.8.6 REQ-105-06 String Output Record Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Bancomm support library shall be capable of initializing and
processing an EPICS String Output support with its DTYP set to drvBc635.

2.5.8.7 REQ-105-06 epicsGeneralTime Time Provider
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Bancomm driver shall register itself with the epicsGeneralTime
framework as the highest time provider. This shall be evident at any
time by calling `generalTimeReport() <#9s27o4jsqqhh>`__ on the IOC.

2.6 Timelib
-----------

The timelib interfaces with the Bancomm driver and the EPICS generalTime
library to provide specific time products such as TAI timestamps and
dates to facility telescope systems.

.. _specific-requirements-1:

2.6.1 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The timelib requirements are very extensive and it is not practical (at
this writing) to itemize every function here. Further, this would yield
a low return on the investment. The timelib itself has not been
refactored for OSI system calls. All the timelib calls are standard C
calls and don’t invoke RTOS utilities such as semaphores, message queues
and threads. However, there are two key functions which will be tested
against. The timeNow() returns the raw time from the Gemini TimeBus (the
Bancomm) and the `timeClockInit() <#fts0i87foetw>`__ is used to
initialize the timelib with the Bancomm and EPICS at startup.

+-----------------+-----------------+-----------------+-------------+
| **Function**    | **Usedby**      | **Meaning**     | **Returns** |
+=================+=================+=================+=============+
| timeNow()       | All telescope   | -  Read the     | OK          |
|                 | systems         |       current   |             |
|                 |                 |       raw time  |             |
|                 |                 |                 |             |
|                 |                 | -  Returned:    |             |
|                 |                 |                 |             |
|                 |                 | ..              |             |
|                 |                 |                 |             |
|                 |                 |    (SI seconds  |             |
|                 |                 |    since TAI    |             |
|                 |                 |    1970 January |             |
|                 |                 |    1.0)         |             |
+-----------------+-----------------+-----------------+-------------+
| timeClockInit() | All telescope   | -  Initialise   | OK          |
|                 | systems         |       the clock |             |
|                 |                 |       used by   |             |
|                 |                 |       the       |             |
|                 |                 |       Gemini    |             |
|                 |                 |       time      |             |
|                 |                 |       system    |             |
|                 |                 |                 |             |
|                 |                 | -  This routine |             |
|                 |                 |       must be   |             |
|                 |                 |       called    |             |
|                 |                 |       prior to  |             |
|                 |                 |       starting  |             |
|                 |                 |       EPICS.    |             |
+-----------------+-----------------+-----------------+-------------+

2.6.1.1 REQ-106-01 timeNow
^^^^^^^^^^^^^^^^^^^^^^^^^^

The timelib shall return TAI time provided with a call to timeNow().

2.6.1.2 REQ-106-02 timeClockInit
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The timelib shall initialize and configure the Bancomm board with a call
to `timeClockInit() <#fts0i87foetw>`__. This routine is essential to the
correct startup of the Gemini time system. It must be called prior to
the startup of EPICS since it calls BCconfigure() . Since EPICS is not
running when this routine is called it uses private memory to store
whether the time system is to be simulated and whether this IOC is the
time bus master. These stored values can be used later by EPICS code.
The master flag is set to 1 if the IOC in which this code is running is
to be the time bus master i.e. it has a bc637 card installed. The
simulate flag is set to 1 if time is to be simulated i.e time is to be
driven by an onboard real time clock rather than a Bancomm card.

2.7 Slalib
----------

The slalib requirements are very extensive and it is not practical (at
this writing) to itemize every function here. Further, this would yield
a low return on the investment. The slalib itself has not been
refactored for OSI system calls. All the slalib calls are standard C
calls and don’t invoke RTOS utilities such as semaphores, message queues
and threads.

.. _specific-requirements-2:

2.7.1 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Although there are no EPICS OSI specific requirements, the following
requirements are defined for compatibility with the build environment
and systems depending on slalib.

2.7.1.1 REQ-107-01 Slalib ADE Compatibility
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The slalib library shall be compatible with the Application Development
Environment (ADE ), and build without errors or warnings.

2.7.1.2 REQ-107-02 Slalib Available for Linking
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The slalib library shall be available for linking and testing with other
CCB components.

2.8 Astlib
----------

The Astlib provides astrometric math functions to the telescope systems.
The astlib requirements are very extensive and it is not practical (at
this writing) to itemize every function here. Further, this would yield
a low return on the investment. The astlib itself has not been
refactored for OSI system calls. All the astlib calls are standard C
calls and don’t invoke RTOS utilities such as semaphores, message queues
and threads.

.. _specific-requirements-3:

2.8.1 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Although there are no EPICS OSI specific requirements, the following
requirements are defined for compatibility with the build environment
and system and systems depending on astlib.

2.8.1.1 REQ-108-01 Astlib ADE Compatibility
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The astlib library shall be compatible with the Application Development
Environment (ADE ), and build without errors or warnings.

2.8.1.2 REQ-108-02 Astlib Available for Linking
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The astlib library shall be available for linking and testing with other
CCB components.

2.9 Asyn
--------

The Asyn module is a community support module.

.. _specific-requirements-4:

2.9.1 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following Gemini specific requirements have been defined for the
Asyn library.

2.9.1.1 REQ-109-01 Asyn ADE Compatibility
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Asyn library shall be compatible with the Application Development
Environment (ADE ), and and build without errors or warnings.

2.9.1.2 REQ-109-01 Asyn Available for Linking
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Asyn library shall be available for linking and testing with other
CCB components.

2.10 StreamDevice
-----------------

The StreamDevice module is a community support module.

.. _specific-requirements-5:

2.10.1 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following Gemini specific requirements have been defined for the
Stream Device library.

2.10.1.1 REQ-110-01 Stream Device ADE Compatibility
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Stream Device library shall be compatible with the Application
Development Environment (ADE ), and build without errors or warnings.

2.10.1.2 REQ-110-01 Stream Device Available for Linking
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Stream Device library shall be available for linking and testing
with other CCB components.

2.11 Xycom-240
--------------

The Xycom-240 board provides digital Input/Output to the EPICS device
layer. The driver support code

2.11.1 Database Definition File (xy240.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

device(bi,VME_IO, devBiXy240,"XYCOM-240")

device(bo,VME_IO, devBoXy240,"XYCOM-240")

device(mbbi,VME_IO, devMbbiXy240,"XYCOM-240")

device(mbbo,VME_IO, devMbboXy240,"XYCOM-240")

driver(drvXy240)

registrar(drvXy240RegisterCommands)

.. _device-support-requirements-1:

2.11.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following device support routines are required for the Xycom-240
board.

1. devBiXy240 -- Provide binary input support using VMEIO for EPICS
      records with Interrupt I/O scanning.

2. devBoXy240 --Provide binary output support using VMEIO for EPICS
      records with Passive scanning.

3. devMbbiXy240 --Provide multi-bit binary input support using VMEIO for
      EPICS records with Interrupt I/O scanning.

4. devMbboXy240 --Provide multi-bit binary output support using VMEIO
      for EPICS records with Passive scanning.

.. _driver-support-requirements-1:

2.11.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Xycom 240 driver provides access to configure and control the
digital input and output of the Xycom 240 board. The EPICS device
support requires the following routines.

long `xy240_init() <#xzk5k3gvyl81>`__;

long `xy240_getioscanpvt <#gdxmvykxu38i>`__\ (short card, IOSCANPVT
\*scanpvt);

long `xy240_bi_driver <#f81n9yfdojo1>`__\ (short card, epicsUInt32 mask,
epicsUInt32 \*prval);

long `xy240_bo_read <#1kp9y58juwsp>`__\ (short card, epicsUInt32 mask,
epicsUInt32 \*prval);

long `xy240_bo_driver <#yqbi6rd2unr0>`__\ (short card, epicsUInt32 val,
epicsUInt32 mask);

long `xy240_io_report <#gpn1f63hw5id>`__\ (int level);

+-----------------+-----------------+-----------------+-------------+
| **Function**    | **Usedby**      | **Meaning**     | **Returns** |
+=================+=================+=================+=============+
| xy240_init      | EPICS iocInit() | -  Initialize   | OK, ERROR   |
|                 |                 |       the       |             |
|                 |                 |       XYCOM-240 |             |
|                 |                 |       on the    |             |
|                 |                 |       VME bus.  |             |
|                 |                 |                 |             |
|                 |                 | -  Turn Front   |             |
|                 |                 |       panel LED |             |
|                 |                 |       green on, |             |
|                 |                 |       red off   |             |
|                 |                 |                 |             |
|                 |                 | -  Clear        |             |
|                 |                 |       interrupt |             |
|                 |                 |       input     |             |
|                 |                 |       register  |             |
|                 |                 |       latch     |             |
|                 |                 |                 |             |
|                 |                 | -  Configure    |             |
|                 |                 |       JK1       |             |
|                 |                 |       (ports    |             |
|                 |                 |       0-3) as   |             |
|                 |                 |       input     |             |
|                 |                 |                 |             |
|                 |                 | -  Configure    |             |
|                 |                 |       JK2       |             |
|                 |                 |       (ports    |             |
|                 |                 |       4-7) as   |             |
|                 |                 |       output    |             |
|                 |                 |                 |             |
|                 |                 | -  Start        |             |
|                 |                 |                 |             |
|                 |                 |     epicsThread |             |
|                 |                 |       for       |             |
|                 |                 |       dio_scan  |             |
|                 |                 |       routine   |             |
+-----------------+-----------------+-----------------+-------------+
| xy2             | devXy240:       | Implement Scan  | OK,         |
| 40_getioscanpvt | bi_ioinfo()     | I/O Interrupt   |             |
|                 |                 | support for     | ERROR       |
|                 | mbbi_ioinfo()   | binary input    |             |
|                 |                 | and multibit    |             |
|                 |                 | binary input    |             |
|                 |                 | records.        |             |
+-----------------+-----------------+-----------------+-------------+
| xy240_bi_driver | devXy240:       | Interface to    | OK, ERROR   |
|                 |                 | read the binary |             |
|                 | read_bi(),      | input registers |             |
|                 | read_mbbi()     | of the 240      |             |
|                 |                 | board.          |             |
+-----------------+-----------------+-----------------+-------------+
| xy240_bo_read   | devXy240:       | Interface to    | OK,         |
|                 |                 | read the state  |             |
|                 | init_bo(),      | of the binary   | ERROR       |
|                 | init_mbbo(),    | output          |             |
|                 |                 | registers of    |             |
|                 | write_mbbo()    | the 240 board.  |             |
+-----------------+-----------------+-----------------+-------------+
| xy240_bo_driver | devXy240:       | Interface to    | OK,         |
|                 |                 | write to the    |             |
|                 | write_bo(),     | binary output   | ERROR       |
|                 |                 | registers of    |             |
|                 | write_mbbo()    | the 240 board.  |             |
+-----------------+-----------------+-----------------+-------------+
| xy240_io_report | EPICS dbior()   | Report to show  | OK, ERROR   |
|                 |                 | the state of    |             |
|                 |                 | all 8 data      |             |
|                 |                 | ports of data   |             |
|                 |                 | 240 board       |             |
+-----------------+-----------------+-----------------+-------------+
| drvXy240Config  | EPICS IOC Shell | Optional        | OK, ERROR   |
|                 |                 | configure       |             |
|                 |                 | function to be  |             |
|                 |                 | called before   |             |
|                 |                 | iocInit() for   |             |
|                 |                 | using any valid |             |
|                 |                 | base address.   |             |
+-----------------+-----------------+-----------------+-------------+

.. _specific-requirements-6:

2.11.4 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.11.4.1 Signal to Port Mapping
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The XYCOM 240 board provides VME bus systems with 80 channels of
TTL-level input or output. The EPICS implementation uses a VMEIO
protocol to map Signals to Ports. This driver specifically maps the
first 4 ports (0,1,2 and 3) as inputs and the last 4 ports (4, 5, 6 and
7) as output. The following VMEIO Signal to XYCOM-240 port mapping is
defined.

+---------+---------+---------+---------+---------+---------+---------+
| **VMEIO | *       | *       | **Data  | **Inte  | **Flag  | **G     |
| S       | *Port** | *Jack** | Pins**  | rrupt** | Output  | round** |
| ignal** |         |         |         |         | Pin**   |         |
|         |         |         |         | **Input |         | *       |
|         |         |         |         | Pin**   |         | *Pins** |
+=========+=========+=========+=========+=========+=========+=========+
| **I     |         |         |         |         |         |         |
| nputs** |         |         |         |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S24 to  | 0       | JK1     | 1 to 8  | 9       | 10      | 11, 12  |
| S31     |         |         |         |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S16 to  | 1       | JK1     | 13 to   | 21      | 22      | 23, 24  |
| S23     |         |         | 20      |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S8 to   | 2       | JK1     | 25 to   | 33      | 34      | 35, 36  |
| S15     |         |         | 32      |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S0 to   | 3       | JK1     | 37 to   | 45      | 46      | 47, 48, |
| S7      |         |         | 44      |         |         | 49, 50  |
+---------+---------+---------+---------+---------+---------+---------+
| **Ou    |         |         |         |         |         |         |
| tputs** |         |         |         |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S24 to  | 4       | JK2     | 1 to 8  | 9       | 10      | 11, 12  |
| S31     |         |         |         |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S16 to  | 5       | JK2     | 13 to   | 21      | 22      | 23, 24  |
| S23     |         |         | 20      |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S8 to   | 6       | JK2     | 25 to   | 33      | 34      | 35,36   |
| S15     |         |         | 32      |         |         |         |
+---------+---------+---------+---------+---------+---------+---------+
| S0 to   | 7       | JK2     | 37 to   | 45      | 46      | 47, 48, |
| S7      |         |         | 44      |         |         | 49, 50  |
+---------+---------+---------+---------+---------+---------+---------+

The VMEIO specification is defined in the EPICS Application Developer's
Guide and the EPICS Record Reference Manual. The EPICS fields INP and
OUT shall specify the format **#C<n> S<k>** , where *n* is the XYCOM
Card Number in the system and *k* is the Signal number as defined above
from 0 to 31.

2.11.4.2 REQ-111-01 XYCOM-240 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The XYCOM 240 support library shall be capable of executing the
`xy240_init() <#xzk5k3gvyl81>`__ routine without error by calling
iocInit().

2.11.4.3 REQ-111-02 XYCOM-240 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvXycom240( <interest_level>) shall display the
state of all data channels respective for that card. See
`xy240_io_report <#gpn1f63hw5id>`__.

2.11.4.4 REQ-111-03 XYCOM-240 Binary Output Record Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The XYCOM 240 support library shall be capable of initializing and
processing an EPICS binary output record with its DTYP field set to
‘XYCOM-240’. Writing a 1 or 0 to the VAL field of this record will
result in the respective output bit being set or reset on the XYCOM 240.
The respective output shall be defined by the output signals S0 through
S31 as defined in `2.2.4.1 Signal to Port Mapping <#111md58bbqio>`__.

There shall be no errors reported during the processing of the record.

2.11.4.5 REQ-111-04 XYCOM-240 Binary Input Record Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The XYCOM 240 support library shall be capable of initializing and
processing an EPICS binary input record with its DTYP field set to
‘XYCOM-240’. Reading the VAL field of this record shall show the state
of the input bit on the XYCOM 240. The respective input shall be defined
by the input signals S0 through S31 as defined in `2.2.4.1 Signal to
Port Mapping <#111md58bbqio>`__.

There shall be no errors reported during the processing of this record.

2.11.4.6 REQ-111-05 XYCOM-240 Multibit Binary Input Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The XYCOM 240 support library shall be capable of initializing and
processing an EPICS multibit binary input (MBBI) record with its DTYP
field set to ‘XYCOM-240’. Reading the VAL field of this record shall
show the state of up to 4 input bits on the XYCOM 240. The respective
input shall be defined by the input signals S0 through S31 as defined in
`2.2.4.1 Signal to Port Mapping <#111md58bbqio>`__. The number of input
bits is defined by the NOBT field of the record.

For example, if INP is defined as “#C0 S24”, and NOBT is defined as 4,
the MBBI record shall equal the state of `Port 0 <#2zb6z33yfo6u>`__ pins
1 to 4. The 4 bits yield the 16 possible states of the MBBI record.

2.11.4.7 REQ-111-06 XYCOM-240 Multibit Binary Output Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The XYCOM 240 support library shall be capable of initializing and
processing an EPICS multibit binary output record (MBBO) with its DTYP
field set to ‘XYCOM-240’. Writing to the VAL field of this record shall
configure up to 4 output bits on the XYCOM 240. The respective output
shall be defined by the output signals S0 through S31 as defined in
`2.2.4.1 Signal to Port Mapping <#111md58bbqio>`__. The number of output
bits is defined by the NOBT field of the record.

For example, if OUT is defined as “#C0 S24”, and NOBT is defined as 4,
the MBBO record shall equal the state of `Port 4 <#qnqr6bxvypgv>`__ pins
37 to 40. The 4 bits yield the 16 possible states of the MBBO record.

2.11.4.8 REQ-111-07 XYCOM-240 Binary Input Support (Interrupt Scanning)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is the same as
`REQ-111-04 <#req-111-04-xycom-240-binary-input-record-support>`__ with
the inclusion of supporting interrupt scanning. The SCAN field of the
record shall support Interrupt I/O.

2.11.4.9 REQ-111-08 XYCOM-240 Multibit Binary Input Support (Interrupt Scanning)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is the same as
`REQ-111-05 <#req-111-05-xycom-240-multibit-binary-input-support>`__
with the inclusion of supporting interrupt scanning. The SCAN field of
the record shall support Interrupt I/O.

2.12 Xycom-566
--------------

The Xycom-566 board provides 16 differential analog input channels to
the EPICS device layer. The device and driver support code implement
three modes: Single-Ended, Differential Scanned mode and Differential
Latched. In practice at Gemini, only differential scanned mode is used.

2.12.1 Database Definition File (xy566.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   device(ai,VME_IO, devAiXy566Se,"XYCOM-566 SE Scanned")

   device(ai,VME_IO, devAiXy566Di,"XYCOM-566 Dif Scanned")

   device(ai,VME_IO, devAiXy566DiL,"XYCOM-566 Dif Latched")

   device(waveform,VME_IO, devWfXy566Sc,"XYCOM-566 Single Channel")

   driver(drvXy566)

   registrar(xy566RegisterCommands)

.. _device-support-requirements-2:

2.12.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.12.2.1 Linear Conversions provided by the Xycom 566 device support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Users must define EGUF and EGUL for the max and min engineering units
that the card can measure. The driver will use those values to figure
out ESLO (the slope) and EOFF (the offset). Record support then
multiples the raw value by ESLO, then adds EOFF to get the VAL in
engineering units. See the EPICS Record Reference Manual for more of
those details.

2.12.2.2 Differential Scanned Mode at Initialization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The driver at Gemini is initialized with the following register
configurations.

2.12.2.2.1 XY566 Settings
'''''''''''''''''''''''''

+-----------------------+-----------------------+--------------------+
| **Setting**           | **Register**          | **Register Value** |
+=======================+=======================+====================+
| Reset the counter     | CSR                   | 0x8000             |
| interrupt             |                       |                    |
+-----------------------+-----------------------+--------------------+
| Reset the sequence    | CSR                   | 0x2000             |
| interrupt             |                       |                    |
+-----------------------+-----------------------+--------------------+
| Reset the trigger     | CSR                   | 0x0800             |
| clock interrupt       |                       |                    |
+-----------------------+-----------------------+--------------------+
| enable the sequence   | CSR                   | 0x0100             |
| controller            |                       |                    |
+-----------------------+-----------------------+--------------------+
| Read values into      | CSR                   | 0x0040             |
| first 32 words on     |                       |                    |
| each read             |                       |                    |
+-----------------------+-----------------------+--------------------+
| Read in sequential    | CSR (bit 0x0020 == 0) | 0x0000             |
| mode                  |                       |                    |
+-----------------------+-----------------------+--------------------+
| Leds green-on red-off | CSR                   | 0x0003             |
+-----------------------+-----------------------+--------------------+
| Latch the first bunch | SOFT_START            | 0x00               |
| of data, then start   |                       |                    |
| continuous scan mode. |                       |                    |
+-----------------------+-----------------------+--------------------+

2.12.2.2.2 AMD 9513A System Timing Controller Settings
''''''''''''''''''''''''''''''''''''''''''''''''''''''

Successive writes into the STC Control and Data Registers are made to
configure the A/D conversion modes and timing characteristics of the
Xycom-566. The initialize routine will always make the same
configuration with the driver designed as-is. See the following table
and the Am9513A Manual for programming details.

+----------------------+----------------------+----------------------+
| **Setting**          | **Register**         | **AMD 9513A          |
|                      |                      | RegisterValue**      |
+======================+======================+======================+
| **Initialize the     |                      |                      |
| Am9513 on the Xycom  |                      |                      |
| 566**                |                      |                      |
+----------------------+----------------------+----------------------+
| Master Reset         | STC Control          | 0xffff               |
+----------------------+----------------------+----------------------+
| Disarm all counters  | STC Control          | 0xff5f               |
+----------------------+----------------------+----------------------+
| 16-bit mode          | STC Control          | 0xffef               |
+----------------------+----------------------+----------------------+
| **Master Mode        |                      |                      |
| Register Setup**     |                      |                      |
+----------------------+----------------------+----------------------+
| Select Master Mode   | STC Control          | 0xff17               |
| Register             |                      |                      |
+----------------------+----------------------+----------------------+
| 16 bit, divide by 4  | STC Data             | 0x2200               |
+----------------------+----------------------+----------------------+
| **Counter 2 is used  |                      |                      |
| to set the time      |                      |                      |
| between sequences**  |                      |                      |
+----------------------+----------------------+----------------------+
| Select Counter 2     | STC Control          | 0xff02               |
+----------------------+----------------------+----------------------+
| Counter 2 TC Toggle  | STC Data             | 0x0b02               |
| Mode                 |                      |                      |
+----------------------+----------------------+----------------------+
| Counter 2 TC Output  | STC Control          | 0xffea               |
| High                 |                      |                      |
+----------------------+----------------------+----------------------+
| **Counter 4 is used  |                      |                      |
| as time between      |                      |                      |
| sequence elements**  |                      |                      |
+----------------------+----------------------+----------------------+
| Select Counter 4     | STC Control          | 0xff04               |
+----------------------+----------------------+----------------------+
| Counter 4 TC Toggle  | STC Data             | 0x0b02               |
| Mode                 |                      |                      |
+----------------------+----------------------+----------------------+
| Counter 4 TC Output  | STC Control          | 0xffec               |
| High                 |                      |                      |
+----------------------+----------------------+----------------------+
| **Counter 5 is used  |                      |                      |
| as an event          |                      |                      |
| counter**            |                      |                      |
+----------------------+----------------------+----------------------+
| Select Counter 5     | STC Control          | 0xff05               |
+----------------------+----------------------+----------------------+
| Counter 5 TC Toggle  | STC Data             | 0x0b02               |
| Mode                 |                      |                      |
+----------------------+----------------------+----------------------+
| Counter 5 TC Output  | STC Control          | 0xffed               |
| High                 |                      |                      |
+----------------------+----------------------+----------------------+
| **Set time between   |                      |                      |
| sequences**          |                      |                      |
+----------------------+----------------------+----------------------+
| Select Counter 4     | STC Control          | 0xff04               |
| (again)              |                      |                      |
+----------------------+----------------------+----------------------+
| See 9513A manual     | STC Data (unknown    | 0x9525               |
|                      | setting)             |                      |
+----------------------+----------------------+----------------------+
| Downcount value      | STC DATA             | 0x0014               |
+----------------------+----------------------+----------------------+
| Load and Arm Counter | STC Control          | 0xff68               |
| 4                    |                      |                      |
+----------------------+----------------------+----------------------+
| Select Counter 5     | STC Control          | 0xff05               |
| (again)              |                      |                      |
+----------------------+----------------------+----------------------+
| See 9513A manual     | STC Data (unknown    | 0x97ad               |
|                      | setting)             |                      |
+----------------------+----------------------+----------------------+
| Downcount value      | STC Data             | 0x0100               |
+----------------------+----------------------+----------------------+
| Load and Arm Counter | STC Control          | 0xff70               |
| 5                    |                      |                      |
+----------------------+----------------------+----------------------+

.. _driver-support-requirements-2:

2.12.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Xycom-566 driver provides access to configure and control the analog
input of the Xycom 566 board. The EPICS device support requires the
following routines.

long xy566_init (void);

long xy566_report(int level);

int ai_xy566_getioscanpvt(unsigned short card, IOSCANPVT \*scanpvt);

int ai_xy566_driver (short card, short chan, unsigned int type, unsigned
short \*prval );

int xy566_driver (unsigned short slot, void (*pcbroutine)(void \*), void
\*parg );

void xy566_set_gain(int card, int val);

int xy566SEConfig (unsigned int ncards, unsigned int nchannels, unsigned
int base, unsigned int memory);

int xy566DIConfig (unsigned int ncards, unsigned int nchannels, unsigned
int base, unsigned int memory);

int xy566DILConfig(unsigned int ncards, unsigned int nchannels, unsigned
int base, unsigned int memory);

int xy566WFConfig (unsigned int ncards, unsigned int nchannels, unsigned
int base, unsigned int memory,

   unsigned int armaddr, unsigned int vector);

+----------------+----------------+----------------+----------------+
| **Function**   | **Usedby**     | **Meaning**    | **Returns**    |
+================+================+================+================+
| xy566_init     | EPICS          | -  Allocate    | OK             |
|                | iocInit()      |       memory   |                |
|                |                |       for      |                |
|                |                |       xy566    |                |
|                |                |                |                |
|                |                |      registers |                |
|                |                |                |                |
|                |                | -  Allocate    |                |
|                |                |       memory   |                |
|                |                |       for Data |                |
|                |                |       RAM      |                |
|                |                |                |                |
|                |                | -  reset the   |                |
|                |                |       Xycom    |                |
|                |                |       566      |                |
|                |                |       board    |                |
|                |                |                |                |
|                |                | -  initialize  |                |
|                |                |       the      |                |
|                |                |       Am9513   |                |
|                |                |       chip on  |                |
|                |                |       the      |                |
|                |                |       Xycom566 |                |
|                |                |                |                |
|                |                | -  Reset the   |                |
|                |                |       counter, |                |
|                |                |       sequence |                |
|                |                |       and      |                |
|                |                |       trigger  |                |
|                |                |       clock    |                |
|                |                |                |                |
|                |                |     interrupts |                |
|                |                |                |                |
|                |                | -  Enable the  |                |
|                |                |       sequence |                |
|                |                |                |                |
|                |                |     controller |                |
+----------------+----------------+----------------+----------------+
| ai_xy56        | d              | Configure      | OK             |
| 6_getioscanpvt | evAiXy566DiL.c | interrupts and |                |
|                |                | callback for   |                |
|                |                | I/O Interrupt  |                |
|                |                | scanning.      |                |
+----------------+----------------+----------------+----------------+
| a              | devAiXy566Di.c | Configure the  | OK, -1 Card    |
| i_xy566_driver |                | card and read  | Range, -2      |
|                | d              | the raw data   | Channel Range, |
|                | evAiXy566DiL.c | from 566 RAM.  | -3 Mode not    |
|                |                |                | supported.     |
|                | devAiXy566Se.c |                |                |
+----------------+----------------+----------------+----------------+
| xy566_driver   | devWfXy566.c   | Configure and  | OK, -1 No      |
|                |                | arm a waveform | card, -2       |
|                |                | built from raw | Callback       |
|                |                | data in 566    | already in     |
|                |                | RAM.           | use.           |
+----------------+----------------+----------------+----------------+
| xy566_report   | EPICS dbior()  | Report to show | OK, ERROR      |
|                |                | the state of   |                |
|                |                | all channels.  |                |
+----------------+----------------+----------------+----------------+
| xy566_set_gain | IOC Shell at   | This has to be | N/A            |
|                | startup        | called BEFORE  |                |
|                |                | any of the the |                |
|                |                | Configure      |                |
|                |                | routines below |                |
|                |                | are called for |                |
|                |                | each card.     |                |
|                |                | Valid values   |                |
|                |                | are 1,2,5,10.  |                |
+----------------+----------------+----------------+----------------+
| xy566SEConfig  | Single Ended   | Configure use  | OK             |
|                | Mode           | of the 566 for |                |
|                |                | Single Ended   |                |
|                |                | mode. Call     |                |
|                |                | BEFORE         |                |
|                |                | iocInit().     |                |
+----------------+----------------+----------------+----------------+
| xy566DIConfig  | Differential   | Configure use  | OK             |
|                | Mode           | of the 566 for |                |
|                |                | Differential   |                |
|                |                | mode. Call     |                |
|                |                | BEFORE         |                |
|                |                | iocInit().     |                |
+----------------+----------------+----------------+----------------+
| xy566DILConfig | Differential   | Configure use  | OK             |
|                | Latched        | of the 566 for |                |
|                |                | Differential   |                |
|                |                | Latched mode.  |                |
|                |                | Call BEFORE    |                |
|                |                | iocInit().     |                |
+----------------+----------------+----------------+----------------+
| xy566WFConfig  | WaveForm       | Configure use  | OK             |
|                | Support        | of the 566 for |                |
|                |                | Waveform mode. |                |
|                |                | Call BEFORE    |                |
|                |                | iocInit().     |                |
+----------------+----------------+----------------+----------------+

.. _specific-requirements-7:

2.12.4 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.12.4.1 VMEIO Signal Pinout
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 board provides the VME bus with 32-Single ended or 16
differential channels of analog input. The EPICS implementation uses a
VMEIO protocol to map Signals to channels. The following VMEIO Signal to
Xycom-566 pinouts are defined.

=================== ======================= =======================
**JK1**             **Single Ended**        **Differential**
                                            
**Front panel pin** **VMEIO Signal**        **VMEIO Signal**
=================== ======================= =======================
1                   0                       0 LO
2                   8                       0 HI
3                   GND                     GND
4                   9                       1 HI
5                   1                       1 LO
6                   GND                     GND
7                   2                       2 LO
8                   10                      2 HI
9                   GND                     GND
10                  11                      3 HI
11                  3                       3 LO
12                  GND                     GND
13                  4                       4 LO
14                  12                      4 HI
15                  GND                     GND
16                  5                       5 HI
17                  13                      5 LO
18                  GND                     GND
19                  6                       6 LO
20                  14                      6 HI
21                  GND                     GND
22                  15                      7 HI
23                  7                       7 LO
24                  GND                     GND
25                  16                      8 LO
26                  24                      8 HI
27                  GND                     GND
28                  25                      9 HI
29                  17                      9 LO
30                  GND                     GND
31                  18                      10 LO
32                  26                      10 HI
33                  GND                     GND
34                  27                      11 HI
35                  19                      11 LO
36                  GND                     GND
37                  20                      12 LO
38                  28                      12 HI
39                  GND                     GND
40                  29                      12 HI
41                  21                      12 LO
42                  GND                     GND
43                  22                      14 LO
44                  30                      14 HI
45                  GND                     GND
46                  31                      15 HI
47                  23                      15 LO
48                  GND                     GND
49                  GDN (Ext. trigger, PDI) GDN (Ext. trigger, PDI)
50                  Ext. Trigger            Ext. Trigger
=================== ======================= =======================

The VMEIO specification is defined in the EPICS Application Developer's
Guide and the EPICS Record Reference Manual. The EPICS field INP shall
specify the format **#C<n> S<k>** , where *n* is the Xycom Card Number
in the system and *k* is the Signal number as defined above from 0 to
31. See `3.13.1 Test Setup and
Prerequisites <#test-setup-and-prerequisites-10>`__ .

2.12.4.2 REQ-112-01 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 support library shall be capable of executing the
`xy566_init() <#q70cgd44ilou>`__ routine without error by calling
iocInit().

2.12.4.3 REQ-112-02 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvXy566( <interest_level>) shall display the
state of all data channels respective for that card. See
`xy566_report <#234l2buazs8q>`__.

2.12.4.4 REQ-112-03 xy566DIConfig
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 driver shall provide a configure routine to be called on
the iocShell at startup. The configure routine xy566DIConfig() will set
the base address of the board and the data address of the memory
consistent with the configuration of the 566 board.

2.12.4.5 REQ-112-04 Analog Input Record Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 driver shall be capable of initializing and processing an
EPICS analog input record with its DTYP field set to ‘XYCOM-566 Dif
Scanned’. Reading the VAL field of this record shall show the state of
the input channel on the XYCOM 566.

The respective input shall be defined by the input signals S0 through
S31 as defined in 2.2.4.1 Signal to Port Mapping.

2.12.4.6 REQ-112-05 Gain Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 uses a 32-element on-board Gain RAM to store a gain factor
for each analog input channel. One of three gain ranges is selected via
jumper option at the time the module is installed (See the `Xycom-556
Manual Section
2.7.5 <https://drive.google.com/open?id=0B4C51TY6UtsQenNNWndvd3QyVGc>`__).

Regardless of which gain range jumper is set on the board, the Xycom-566
driver shall provide a function available to the IOC shell to set the
gain factor. The legacy driver is currently designed to set all analog
channels on the board to the same gain factor. See
`xy566_set_gain() <#yh3nl2t6iaii>`__. This function must be called prior
to iocInit().

The Gain is user programmable in the startup script by calling
xy566_set_gain(cardnumber, gain_entry ) , where cardnumber is the number
of the 566 board starting at zero and gain_entry is the element number
of the four available gain levels (0 - 3). With Jumper J7 set for gain
range X1, the levels are (1, 2, 5, 10) respectively. This means that the
MCS tachometers have unity gain and the motor currents have a gain of 2
as shown in the `MCS System
Note <https://drive.google.com/open?id=11SiQmNp9DEFfp89ijOznZefrOQr9GnBmJq9qmW9DxLs>`__
regarding the Xycom 566.

2.12.4.7 REQ-112-06 xy566SEConfig
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 driver shall provide a configure routine for **Single
Ended** operation to be called on the iocShell at startup. The configure
routine xy566SEConfig() will set the base address of the board and the
data address of the memory consistent with the configuration of the 566
board.

2.12.4.8 REQ-112-07 xy566DILConfig
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 driver shall provide a configure routine for
**Differential Input Latched** operation to be called on the iocShell at
startup. The configure routine xy566DILConfig() will set the base
address of the board and the data address of the memory consistent with
the configuration of the 566 board.

**2.12.4.9 REQ-112-08 xy566WFConfig**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Xycom-566 driver shall provide a configure routine for **Wave Form**
operation to be called on the iocShell at startup. The configure routine
xy566WFConfig() will set the base address of the board and the data
address of the memory consistent with the configuration of the 566
board.

2.12.4.10 REQ-112-09 XYCOM-566 Two Card Support 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The driver shall support two (2) cards. All requirements REQ-112-01
through REQ-112-08 shall be supported on either of two XYCOM-566 cards.
EPICS input and output channels shall be specified as Card 0 or 1 and
Signals 0 through 49. The schematic design tools such as TDCT shall
specify the input or output links as “#C<0|1>பS<0...49>”.

2.13 PMAC
---------

The Delta Tau Data Systems, Inc. Programmable Multi-Axis Controller
(PMAC) is a family of high performance servo motion controllers capable
of commanding up to eight axes of motion simultaneously with a high
level of sophistication. Motorola’s DSP56001 is the CPU for PMAC, and it
handles all the calculations for all eight axes.

PMAC is configured for a particular application by choice of the
hardware set (through options and accessories), configuration of
parameters, and the writing of motion and PLC programs. Each PMAC
possesses firmware capable of controlling eight axes. The eight axes can
be associated all together for completely coordinated motion; each axis
can be put in its own coordinate system for eight completely independent
operations; any intermediate arrangement of axes into coordinate systems
is also possible.

The PMAC CPU communicates with the axes through specially designed
custom gate array ICs, referred to as DSPGATES. Each of these ICs can
handle four analog output channels, four encoders as input, and four
analog-derived inputs from accessory boards. One PMAC can utilize from
one to four of these gate array ICs, so specifying the hardware
configuration amounts to counting the numbers and types of inputs and
outputs. Up to 16 PMAC modules ay be ganged together with complete
synchronization, for a total of 128 axes.

2.13.1 Database Definition File (pmacDev.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

device(ai,VME_IO,devPmacMbxAi,"PMAC-VME ASCII")

device(ai,VME_IO,devPmacRamAi,"PMAC-VME DPRAM")

device(ao,VME_IO,devPmacMbxAo,"PMAC-VME ASCII")

device(ao,VME_IO,devPmacRamAo,"PMAC-VME DPRAM")

device(bi,VME_IO,devPmacMbxBi,"PMAC-VME ASCII")

device(bi,VME_IO,devPmacRamBi,"PMAC-VME DPRAM")

device(bo,VME_IO,devPmacMbxBo,"PMAC-VME ASCII")

device(bo,VME_IO,devPmacRamBo,"PMAC-VME DPRAM")

device(event,VME_IO,devPmacRamEvent,"PMAC-VME DPRAM")

device(longin,VME_IO,devPmacMbxLi,"PMAC-VME ASCII")

device(longin,VME_IO,devPmacRamLi,"PMAC-VME DPRAM")

device(longout,VME_IO,devPmacMbxLo,"PMAC-VME ASCII")

device(longout,VME_IO,devPmacRamLo,"PMAC-VME DPRAM")

device(mbbi,VME_IO,devPmacMbxMbbi,"PMAC-VME ASCII")

device(mbbi,VME_IO,devPmacRamMbbi,"PMAC-VME DPRAM")

device(mbbo,VME_IO,devPmacMbxMbbo,"PMAC-VME ASCII")

device(mbbo,VME_IO,devPmacRamMbbo,"PMAC-VME DPRAM")

device(stringin,VME_IO,devPmacMbxSi,"PMAC-VME ASCII")

device(stringout,VME_IO,devPmacMbxSo,"PMAC-VME ASCII")

device(load,VME_IO,devPmacMbxLoad,"PMAC-VME ASCII")

device(status,VME_IO,devPmacMbxStatus,"PMAC-VME ASCII")

device(status,VME_IO,devPmacRamStatus,"PMAC-VME DPRAM")

.. _device-support-requirements-3:

2.13.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following device support routines are required for the PMAC:

1.  devPmacMbxAi -- Provide Analog Input support for the PMAC Mailbox
       access.

2.  devPmacRamAi -- ProvideAnalog Input support for the PMAC DPRam
       access.

3.  devPmacMbxAo -- Provide Analog Output support for the PMAC Mailbox
       access.

4.  devPmacRamAo -- Provide Analog Output support for the PMAC DPRam
       access.

5.  devPmacMbxBi -- Provide Binary Input support for the PMAC Mailbox
       access.

6.  devPmacRamBi -- Provide Binary Input support for the PMAC DPRam
       access.

7.  devPmacMbxBo -- Provide Binary Output support for the PMAC Mailbox
       access.

8.  devPmacRamBo -- Provide Binary Output support for the PMAC DPRam
       access.

9.  devPmacRamEvent -- Provide Event records support for the PMAC DPRam
       access.

10. devPmacMbxLi -- Provide Longin device support for the PMAC Mailbox
       access.

11. devPmacRamLi -- Provide Longin device support for the PMAC DPRam
       access.

12. devPmacMbxLo -- Provide Longout device support for the PMAC Mailbox
       access.

13. devPmacRamLo -- Provide Longout device support for the PMAC DPRam
       access.

14. devPmacMbxMbbi -- Provide Multibit Binary Input device support for
       the PMAC Mailbox access.

15. devPmacRamMbbi -- Provide Multibit Binary Input device support for
       the PMAC DPRam access.

16. devPmacMbxMbbo -- Provide Multibit Binary Output device support for
       the PMAC Mailbox access.

17. devPmacRamMbbo -- Provide Multibit Binary Output device support for
       the PMAC DPRam access.

18. devPmacMbxSi -- Provide Stringin record device support for the PMAC
       Mailbox access.

19. devPmacMbxSo -- Provide Stringout record device support for the PMAC
       Mailbox access.

20. devPmacMbxLoad -- Provide Load record device support for the PMAC
       Mailbox access.

21. devPmacMbxStatus -- Provide Status record device support for the
       PMAC Mailbox access.

22. devPmacRamStatus -- Provide Status record device support for the
       PMAC DPRam access.

.. _driver-support-requirements-3:

2.13.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following driver functions are the key tasks running on a loaded
PMAC driver and represent all aspects of the required functionality at
Gemini. These tasks are structured into two type of support: Mailbox
communications and Dual Port Ram communications. Dual Port memory has
several different regions with different data types. Further, the PMAC
has internal scan clocks making certain types of DPRam available at
different phases of the processing cycle. For this reason, there are
several different EPICS Scan Threads dealing with the different DPRam
types.

long pmacConfig(int cardNumber, unsigned long addrBase, unsigned long
addrDpram,

   unsigned int irqVector, unsigned int irqLevel);

PMAC_LOCAL long drvPmac_report(int level);

PMAC_LOCAL long drvPmac_init(void);

PMAC_LOCAL long drvPmacStartup(void);

void drvPmacMbxScan (PMAC_MBX_IO \* pMbxIo);

void drvPmacFldScan (PMAC_MBX_IO \* pMbxIo);

PMAC_LOCAL long drvPmacRamGetData(PMAC_RAM_IO \*);

PMAC_LOCAL long drvPmacRamPutData(PMAC_RAM_IO \*);

long drvPmacDpramRequest(short, short, char \*, void (*)(), void \*,
PMAC_RAM_IO \**, int);

EPICSTHREADFUNC drvPmacMbxTask(PMAC_CARD \*pCard);

EPICSTHREADFUNC drvPmacSvoTask(void \*);

EPICSTHREADFUNC drvPmacBkgTask(void \*);

EPICSTHREADFUNC drvPmacVarTask(void \*);

EPICSTHREADFUNC drvPmacOpnTask(void \*);

EPICSTHREADFUNC drvPmacAscTask(void \*);

EPICSTHREADFUNC drvPmacFldTask(void \*);

long drvPmacSvoRead(int);

long drvPmacBkgRead(int);

long drvPmacVarRead(int);

long drvPmacOpnRead(int);

+-----------------+-----------------+-----------------+-------------+
| **Function**    | **Usedby**      | **Meaning**     | **Returns** |
+=================+=================+=================+=============+
| pmacConfig      | IOC Shell       | Configure VME   | OK, ERROR   |
|                 | Startup         | for PMAC with:  |             |
|                 |                 |                 |             |
|                 |                 | -  cardNumber   |             |
|                 |                 |       in the    |             |
|                 |                 |       crate     |             |
|                 |                 |                 |             |
|                 |                 | -  addrBase     |             |
|                 |                 |                 |             |
|                 |                 | -  addrDpram,   |             |
|                 |                 |                 |             |
|                 |                 | -  irqVector    |             |
|                 |                 |                 |             |
|                 |                 | -  irqLevel     |             |
|                 |                 |                 |             |
|                 |                 | All Values Must |             |
|                 |                 | Agree with      |             |
|                 |                 |                 |             |
|                 |                 | X:$0783 -       |             |
|                 |                 | X:$078C         |             |
|                 |                 |                 |             |
|                 |                 | Example for 2   |             |
|                 |                 | cards:          |             |
|                 |                 |                 |             |
|                 |                 | pmacConfig(0,   |             |
|                 |                 | 0x7FA000,       |             |
|                 |                 | 0x700000, 0xa1, |             |
|                 |                 | 6)              |             |
|                 |                 |                 |             |
|                 |                 | pmacConfig(1,   |             |
|                 |                 | 0x7FA200,       |             |
|                 |                 | 0x704000, 0xa5, |             |
|                 |                 | 6)              |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmac_report  | IOC Shell       | Register        | OK, ERROR   |
|                 |                 | function with   |             |
|                 |                 | EPICS dbior().  |             |
|                 |                 | Report shows    |             |
|                 |                 | the state of    |             |
|                 |                 | all initialized |             |
|                 |                 | PMAC boards and |             |
|                 |                 | their           |             |
|                 |                 | configuration.  |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmac_init    | EPICS iocInit() | Calls           | OK, ERROR   |
|                 |                 | pmacVmeInit().  |             |
|                 |                 | Initializes     |             |
|                 |                 | PMAC-VME        |             |
|                 |                 | Hardware        |             |
|                 |                 | Configuration   |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacStartup  | devPmacMbx.c:   | Startup the     | OK, ERROR   |
|                 |                 | PMAC driver by  |             |
|                 | de              | initializing    |             |
|                 | vPmacMbx_init() | all scan        |             |
|                 |                 | threads.        |             |
|                 | devPmacRam.c:   |                 |             |
|                 |                 |                 |             |
|                 | de              |                 |             |
|                 | vPmacRam_init() |                 |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacMbxScan  | devPmacMbx.c:   | Put PMAC MBX    | N/A         |
|                 | r/w             | request on      |             |
|                 |                 | ASCII           |             |
|                 | devP            | communications  |             |
|                 | macMbxAi_read() | queue. The      |             |
|                 |                 | queue is        |             |
|                 | devP            | implemented     |             |
|                 | macMbxBi_read() | with an         |             |
|                 |                 | ep              |             |
|                 | devP            | icsRingPointer. |             |
|                 | macMbxLi_read() | So we push the  |             |
|                 |                 | data with       |             |
|                 | devPma          | epicsRingPoin   |             |
|                 | cMbxMbbi_read() | terPush(&data), |             |
|                 |                 | then release    |             |
|                 | devPmacM        | the Scan        |             |
|                 | bxStatus_read() | Mailbox         |             |
|                 |                 | semaphore by    |             |
|                 | devPm           | calling         |             |
|                 | acMbxAo_write() | epicsEventS     |             |
|                 |                 | igal(mbxSemID). |             |
|                 | devPm           |                 |             |
|                 | acMbxBo_write() | See `2.13.5.1   |             |
|                 |                 | Mailbox         |             |
|                 | devPm           | Thread <#mai    |             |
|                 | acMbxLo_write() | lbox-thread>`__ |             |
|                 |                 |                 |             |
|                 | devPmac         |                 |             |
|                 | MbxMbbo_write() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macMbxSi_read() |                 |             |
|                 |                 |                 |             |
|                 | devPm           |                 |             |
|                 | acMbxSo_write() |                 |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacFldScan  | devPmacMbx.c:   | The Load record | N/A         |
|                 |                 | adds the file   |             |
|                 | Load record     | request on to   |             |
|                 |                 | the scanFld     |             |
|                 | devPma          | queue. The      |             |
|                 | cMbxLoad_proc() | corresponding   |             |
|                 |                 | tak parses and  |             |
|                 |                 | sends new PMAC  |             |
|                 |                 | programs to the |             |
|                 |                 | board.          |             |
|                 |                 |                 |             |
|                 |                 | Implements PMAC |             |
|                 |                 | Load Record     |             |
|                 |                 | support by      |             |
|                 |                 | setting up the  |             |
|                 |                 | pRec->dnv and   |             |
|                 |                 | pRec->upv with  |             |
|                 |                 | the command and |             |
|                 |                 | response        |             |
|                 |                 | respectively.   |             |
|                 |                 | In this case    |             |
|                 |                 | the command is  |             |
|                 |                 | the filename to |             |
|                 |                 | download to the |             |
|                 |                 | PMAC.           |             |
|                 |                 |                 |             |
|                 |                 | See `2.13.5.3   |             |
|                 |                 | File Download   |             |
|                 |                 | Thr             |             |
|                 |                 | ead <#file-down |             |
|                 |                 | load-thread>`__ |             |
+-----------------+-----------------+-----------------+-------------+
| dr              | drvPmac.c:      | Read data from  | N/A         |
| vPmacRamGetData |                 | PMAC DPRAM      |             |
|                 | d               |                 |             |
|                 | rvPmacVarRead() |                 |             |
|                 |                 |                 |             |
|                 | d               |                 |             |
|                 | rvPmacBkgRead() |                 |             |
|                 |                 |                 |             |
|                 | d               |                 |             |
|                 | rvPmacSvoRead() |                 |             |
+-----------------+-----------------+-----------------+-------------+
| dr              | devPmacRam.c:   | Write data to   | N/A         |
| vPmacRamPutData | r/w             | PMAC DPRAM      |             |
|                 |                 |                 |             |
|                 | devPm           |                 |             |
|                 | acRamAo_write() |                 |             |
|                 |                 |                 |             |
|                 | devPm           |                 |             |
|                 | acRamBo_write() |                 |             |
|                 |                 |                 |             |
|                 | devPm           |                 |             |
|                 | acRamLo_write() |                 |             |
|                 |                 |                 |             |
|                 | devPmac         |                 |             |
|                 | RamMbbo_write() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamSi_read() |                 |             |
|                 |                 |                 |             |
|                 | devPm           |                 |             |
|                 | acRamSo_write() |                 |             |
+-----------------+-----------------+-----------------+-------------+
| drvP            | devPmacRam.c:   | Add PMAC DPRAM  | N/A         |
| macDpramRequest |                 | address to scan |             |
|                 | devP            | list            |             |
|                 | macRamAi_init() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamBi_init() |                 |             |
|                 |                 |                 |             |
|                 | devPmac         |                 |             |
|                 | RamEvent_init() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamLi_init() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamLi_init() |                 |             |
|                 |                 |                 |             |
|                 | devPmacR        |                 |             |
|                 | amStatus_init() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamAo_init() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamBo_init() |                 |             |
|                 |                 |                 |             |
|                 | devP            |                 |             |
|                 | macRamLo_init() |                 |             |
|                 |                 |                 |             |
|                 | devPma          |                 |             |
|                 | cRamMbbo_init() |                 |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacSvoRead  | drvPmacSvoTask  | Read Servo      | N/A         |
|                 |                 | Fixed Buffer    |             |
|                 |                 | data            |             |
|                 |                 |                 |             |
|                 |                 | See `2.13.5.2.1 |             |
|                 |                 | Servo Fixed     |             |
|                 |                 | Data Scan       |             |
|                 |                 | Thread <#servo- |             |
|                 |                 | data-reporting- |             |
|                 |                 | scan-thread>`__ |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacBkgRead  | drvPmacBkgTask  | Read Background | N/A         |
|                 |                 | Fixed Data      |             |
|                 |                 | Buffer          |             |
|                 |                 |                 |             |
|                 |                 | See `2.13.5.2.2 |             |
|                 |                 | Background      |             |
|                 |                 | Fixed Data Scan |             |
|                 |                 | Thread <#ba     |             |
|                 |                 | ckground-fixed- |             |
|                 |                 | data-reporting- |             |
|                 |                 | scan-thread>`__ |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacVarRead  | drvPmacVarTask  | Read Background | N/A         |
|                 |                 | Variable Data   |             |
|                 |                 | Buffer          |             |
|                 |                 |                 |             |
|                 |                 | See `2.13.5.2.3 |             |
|                 |                 | Backgroung      |             |
|                 |                 | Variable Data   |             |
|                 |                 | Scan            |             |
|                 |                 | Thread <#backg  |             |
|                 |                 | round-variable- |             |
|                 |                 | data-reporting- |             |
|                 |                 | scan-thread>`__ |             |
+-----------------+-----------------+-----------------+-------------+
| drvPmacOpnRead  | drvPmacOpnTask  | Read            | N/A         |
|                 |                 | Dual-Ported Ram |             |
|                 |                 | from the Open   |             |
|                 |                 | area            |             |
|                 |                 |                 |             |
|                 |                 | See `2.13.5.2.4 |             |
|                 |                 | Open Area of    |             |
|                 |                 | Dual Ported Ram |             |
|                 |                 | Scan            |             |
|                 |                 | Thread <        |             |
|                 |                 | #open-area-of-d |             |
|                 |                 | ual-ported-ram- |             |
|                 |                 | scan-thread>`__ |             |
+-----------------+-----------------+-----------------+-------------+
|                 |                 |                 | N/A         |
+-----------------+-----------------+-----------------+-------------+

2.13.4 VME Interrupts Request Vector and Handlers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+-----------------------+-------------------+-----------------------+
| Vector base from      | Handler           | Comment               |
| pmacConfig()          |                   |                       |
+=======================+===================+=======================+
| baseIrqVector -1      | pmacMbxReceiptISR | ISR for mailbox       |
|                       |                   | receipt acknowledge   |
+-----------------------+-------------------+-----------------------+
| baseIrqVector         | pmacMbxReadmeISR  | ISR for mailbox       |
|                       |                   | message arrival       |
+-----------------------+-------------------+-----------------------+
| baseIrqVector +1      | pmacAscInISR      | ISR for ASCII         |
|                       |                   | Host-Input Buffer     |
|                       |                   | Ready                 |
+-----------------------+-------------------+-----------------------+
| baseIrqVector +2      | pmacGatBufferISR  | ISR for DPRAM Data    |
|                       |                   | Gathering Buffer      |
+-----------------------+-------------------+-----------------------+

2.13.5 PMAC Data Scan Threads
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The PMAC driver has dedicated threads running to scan for updates to the
Mailbox to the Dual-Ported Ram..

2.13.5.1 Mailbox Thread
^^^^^^^^^^^^^^^^^^^^^^^

The Mailbox scan thread manages all mailbox requests from EPICS records.
If any of the mailbox related records (listed above in
`drvPmacMbxScan() <#46vp227yje8g>`__ section) process, this causes the
drvPmacMbxScan() thread to run. The Mailbox Request is then added to the
mailbox queue and processed on the VME backplane.

2.13.5.2 Dual Port Memory Automatic Functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The mapping of memory addresses between the host computer on one side,
and PMAC's address space on the other side, is quite simple. Using this
memory is a matter of matching the addresses on both sides. To PMAC,
DPRAM simply appears as extra memory in the range $D000 to $DFFF, which
can be thought of as 4K of double (48-bit) words or 8K of single
(24-bit) words, in both X and Y memory (remember, X and Y memory in PMAC
are 24-bit locations.).

PMAC provides many facilities for using the dual-ported RAM (DPRAM) to
pass information back and forth between the host computer and PMAC.
These facilities are comprised of the following functions:

• DPRAM Control Panel Function (Host to PMAC) DP-Ram Range $D000- $D008

• DPRAM Servo Data Reporting Function (PMAC to Host) DP-Ram Range $D009-
$D089

• DPRAM Background Fixed Data Reporting Function (PMAC to Host) DP-Ram
Range $D08A- $D18A

• DPRAM Background Variable Data Reporting Function (PMAC to Host)
DP-Ram Range $D200-$DFFF

• DPRAM ASCII Communications Buffer (Bi-directional) DP-Ram Range
$D200-$DFFF

• DPRAM Binary Rotary Program Buffer (Host to PMAC) DP-Ram Range
$D200-$DFFF

• DPRAM Data Gathering Buffer (PMAC to Host -- already existing) DP-Ram
Range $D200-$DFFF

• DPRAM <CONTROL-W> ASCII Command Function (Host to PMAC -- superseded
by DPRAM

ASCII Communications)

In addition to these automatic functions, the user is free to access
otherwise unused registers in the DPRAM through the use of M-variables
on the PMAC side, and through pointer variables on the host side, for
sending data either way between the host and PMAC.

2.13.5.2.1 Servo Data Reporting Scan Thread
'''''''''''''''''''''''''''''''''''''''''''

PMAC can provide key motor servo data to the DPRAM where it can be
accessed easily and quickly by the host computer. PMAC copies data from
key motor registers into fixed registers in the DPRAM. The copying is
attempted every I19 servo cycles, and is done for motors 1 through *n*,
where *n* is determined by variable I59. Setting I48 to 1 and issuing
the GATHER command enables this feature.

The Servo Fixed Data Area of Dual Port Ram is scanned for data every 100
ms with a priority of 45.

2.13.5.2.2 Background Fixed Data Reporting Scan Thread
''''''''''''''''''''''''''''''''''''''''''''''''''''''

PMAC can provide information of interest of the host computer through
the DPRAM as a background task. This feature is enabled if I49=1. If
this feature is enabled, each time PMAC executes its housekeeping tasks
in background, which it does between each scan of each PLC program, it
will update the buffer in DPRAM if the host has read the previous
contents of the buffer. The information in this buffer is data in PMAC
that is updated less often than once per servo cycle.

The Backgroung Fixed Data Area of Dual Port Ram is scanned for data
every 50 ms with a priority of 44.

2.13.5.2.3 Background Variable Data Reporting Scan Thread
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''

The Background Variable Data Read Buffer allows the user to have up to
128 user-specified PMAC

registers copied into DPRAM during the background cycle. This function
is controlled by I55. The buffer

has two modes of operation, single user and multi-user. The default mode
is the single user mode. It is

active when bit 8 of the control word (Y:$D1FA) is set to zero.
Multi-user mode is active when bit 8 of

the control word (Y:$D1FA) is set to one.

The Background Variable Data Area of Dual Port Ram is scanned for data
every 50 ms with a priority of 44.

2.13.5.2.4 Open Area of Dual Ported Ram Scan Thread
'''''''''''''''''''''''''''''''''''''''''''''''''''

The open area for dual port ram is in the range $DF00 through $DFFF. The
Open Area of Dual Port Ram is scanned for data every 25 ms with a
priority of 43. An EPICS input record with DTYP=PMAC-VME DPRAM whose
hardware input specifies a DPRAM address in the Open Area utilizes this
thread for I/O Interrupt scanning. This thread can be tested by
verifying proper operation of such a record with SCAN=I/O Intr.

2.13.5.3 File Download Thread
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is used for the EPICS Load record to download new files to the
PMAC. The File I/O is managed with a semaphore and queue. This
functionality is currently unused at Gemini.

2.13.5.4 DPRAM Gather Reader/Write Threads
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

PMAC’s data gathering function can create a rotary buffer in DPRAM, so
that the host computer can pick up the data as it is being gathered.
This function was implemented before V1.14, and is described in the
User's Manual for PMAC. The data-gathering buffer in DPRAM must start at
address $D200. The Data Gathering functions are currently unused at
Gemini

The Gather Read and Write tasks are synchronized with semaphores
scanGatReadSem and scanGetWriteSem. The read task controls the master
process calling epicsThreadSleep() every 20 ms.

.. _specific-requirements-8:

2.13.6 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.13.6.1 REQ-113-01 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of executing the
`drvPmac_init() <#nxjktyxxeqn4>`__ routine without error by calling

iocInit().

2.13.6.2 REQ-113-02 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvPmac <interest_level> shall display the state
of all data channels respective for each initialized card. See
`drvPmac_report() <#hwpksar39c5t>`__.

2.13.6.3 REQ-113-03 Analog Input Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS analog input record with its DTYP field set to PMAC-VME ASCII.
This is not used at Gemini.

2.13.6.4 REQ-113-04 Analog Output Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS analog output record with its DTYP field set to PMAC-VME ASCII.
This is not used at Gemini.

2.13.6.5 REQ-113-05 Binary Input Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS binary input record with its DTYP field set to PMAC-VME ASCII.
This is not used at Gemini.

2.13.6.6 REQ-113-06 Binary Output Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS binary output record with its DTYP field set to PMAC-VME ASCII.
This is not used at Gemini.

2.13.6.7 REQ-113-07 Long Input Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS long input record with its DTYP field set to PMAC-VME ASCII.
This is not used at Gemini.

2.13.6.8 REQ-113-08 Long Output Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS long output record with its DTYP field set to PMAC-VME ASCII.
This is not used at Gemini.

2.13.6.9 REQ-113-09 Multibit Binary Input Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS mbbi record with its DTYP field set to PMAC-VME ASCII. This is
not used at Gemini.

2.13.6.10 REQ-113-10 Multibit Binary Output Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS mbbo record with its DTYP field set to PMAC-VME ASCII. This is
not used at Gemini.

2.13.6.11 REQ-113-11 String Input Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS string input record with its DTYP field set to PMAC-VME ASCII.

2.13.6.12 REQ-113-12 String Output Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS string output record with its DTYP field set to PMAC-VME ASCII.

2.13.6.13 REQ-113-13 Load Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS load record with its DTYP field set to PMAC-VME ASCII. This is
currently not in use at Gemini.

2.13.6.14 REQ-113-14 Status Record Support for PMAC-VME ASCII
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS status record with its DTYP field set to PMAC-VME ASCII. This
is not used at Gemini.

2.13.6.15 REQ-113-15 Analog Input Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS analog input record with its DTYP field set to PMAC-VME DPRAM.

2.13.6.16 REQ-113-16 Analog Output Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS analog output record with its DTYP field set to PMAC-VME DPRAM.

2.13.6.17 REQ-113-17 Binary Input Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS binary input record with its DTYP field set to PMAC-VME DPRAM.
This is not used at Gemini.

2.13.6.18 REQ-113-18 Binary Output Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS binary output record with its DTYP field set to PMAC-VME DPRAM.
This is not used at Gemini.

2.13.6.19 REQ-113-19 Event Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS event record with its DTYP field set to PMAC-VME DPRAM. This is
not used at Gemini.

2.13.6.20 REQ-113-20 Long Input Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS long input record with its DTYP field set to PMAC-VME DPRAM.
This is not used at Gemini.

2.13.6.21 REQ-113-21 Long Output Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS long output record with its DTYP field set to PMAC-VME DPRAM.
This is not used at Gemini.

2.13.6.22 REQ-113-22 Multibit Binary Input Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS mbbi record with its DTYP field set to PMAC-VME DPRAM. This is
not used at Gemini.

2.13.6.23 REQ-113-23 Multibit Binary Output Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS mbbo record with its DTYP field set to PMAC-VME DPRAM. This is
not used at Gemini.

2.13.6.24 REQ-113-24 Status Record Support for PMAC-VME DPRAM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of initializing and processing
an EPICS status record with its DTYP field set to PMAC-VME DPRAM.

2.13.6.25 REQ-113-25 Two Card Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PMAC support library shall be capable of configuring and
initializing two VME PMAC boards. All scan tasks shall be running and
not suspended after iocInit() completes. Each PMAC task running shall
have the respective card number appended to the name of the task.

2.13.6.26 REQ-113-26 Continuous Data Stream One card
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A continuous sequence of floating point numbers shall be capable of
passing from EPICS records into dual ported memory filling a PMAC
internal ring buffer. (This needs more definition -- what function of
the driver implements this?)

2.13.6.27 REQ-113-27 Two Continuous Data Streams Two cards
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Two continuous sequences of floating point numbers shall be capable of
passing from two EPICS records into dual ported memory filling a PMAC
internal ring buffer on two different PMAC boards on the same VME crate.

2.13.6.28 REQ-113-28 Servo Fixed Data Thread
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Data values deposited into the Servo Fixed Data area of DPRAM shall be
processed without error.

2.13.6.29 REQ-113-29 Background Fixed Data Thread
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Data values deposited into the Background Fixed Data area of DPRAM shall
be processed without error.

2.13.6.30 REQ-113-30 Background Variable Data Thread
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Data values deposited into the Background Variable Data area of DPRAM
shall be processed without error.

2.13.6.31 REQ-113-31 Open Variable Data Thread
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Data values deposited into the Open area of DPRAM shall be processed
without error.

2.13.6.32 REQ-113-32 File Download Thread
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

File download data shall be capable of being downloaded to the PMAC
without error. This is currently not in use at Gemini.

2.13.6.33 REQ-113-33 Gather Data Read/Write Threads
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Data Gathering buffer thread shall be processed without error. This
is currently not in use at Gemini.

2.14 OMS VME44
--------------

The VME44 intelligent motor controller can manage 4 axes of motion and
monitor each associated incremental encoder in one slot of a VME bus
computer. It meets the VME C.1 specification for D08(0) slaves and can
be plugged directly into the backplane of these machines. It can manage
coordinated or independent motion of each of the four axes
simultaneously. The controller can also generate constant velocity
profiles with circular interpolation for cutting and machining type
operation.

The VME44 utilizes 68000 microprocessor to control direction of motion,
acceleration, deceleration and velocity of an associated motor. In
response to command from the host computer, the VME44 controller will
calculate the optimum velocity profile to reach the desired destination
in the minimum time while conforming to the programmed velocity and
acceleration parameters.

Commands may be sent to the VME44 by simple I/O commands using virtually
any language on the host VME computer. These controller are easily
programmed with ASCII character string. For a typical motion
requirements of 1,000,000 pulses at 400,000 pulses/sec and an
acceleration of 500,000 pulses /sec2 the following string would be input
from the host computer to the VME44:

VL400000 AC500000 MR1000000 GO

2.14.1 Database Definition File (oms44Dev.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

device(oms44,VME_IO,devOMS44,"OMS 8/44")

.. _device-support-requirements-4:

2.14.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following device support routines are required for the OMS44 motor
controller:

1. configureDrive: Set motor velocity, acceleration and base velocity

2. controlMotion: Start, stop or abort a motion

3. controlPower: Enable or disable the motor power.

4. devInit: Initialize the device control record support module

5. devInitRec: Initialize an instance of deviceControl record support

6. omsScanTask: Monitor device motion

7. setDelay: Re-process the calling record after a given interval

8. setPosition: Load the motor position counter with given value

.. _driver-support-requirements-4:

2.14.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following driver functions are the key tasks running on a loaded
OMS44 driver and represent all aspects of the required functionality at
Gemini.

long drvOmsVmeMotorPosition(int card, int axis, long \*position, long
\*encoder);

long drvOmsVmeMotorState (int card, int axis, int \*lowLimit, int
\*highLimit,

   int \*homeSwitch, int \*axisDone);

int drvOmsVmeGetCardType (int card);

void drvOmsVmeGetErrorMessage (char \*message);

long drvOmsVmeInit();

long drvOmsVmeReadCard (int card, char \*buffer, int timeout);

long drvOmsVmeWriteCard (int card, char \*buffer);

long drvOmsVmeWriteMotor (int card, int axis, char \*buffer);

+--------------------+--------+--------------------+---------------+
| Function           | Usedby | Meaning            | Returns       |
+====================+========+====================+===============+
| drvO               |        | Request current    | OK, ERROR     |
| msVmeMotorPosition |        | encoder and        |               |
|                    |        | position from a    |               |
|                    |        | given axes.        |               |
|                    |        |                    |               |
|                    |        | Parameters:        |               |
|                    |        |                    |               |
|                    |        | -  card: card      |               |
|                    |        |       number to be |               |
|                    |        |       queried      |               |
|                    |        |                    |               |
|                    |        | -  axis: axis      |               |
|                    |        |       number to be |               |
|                    |        |       queried      |               |
|                    |        |                    |               |
|                    |        | -  position:       |               |
|                    |        |       current step |               |
|                    |        |       position     |               |
|                    |        |                    |               |
|                    |        | -  counter:        |               |
|                    |        |       current      |               |
|                    |        |       encoder      |               |
|                    |        |       value        |               |
+--------------------+--------+--------------------+---------------+
| d                  |        | Request current    | OK, ERROR     |
| rvOmsVmeMotorState |        | switch status for  |               |
|                    |        | a given axes.      |               |
|                    |        |                    |               |
|                    |        | Parameters:        |               |
|                    |        |                    |               |
|                    |        | -  card: card to   |               |
|                    |        |       query        |               |
|                    |        |                    |               |
|                    |        | -  axis: axis to   |               |
|                    |        |       query        |               |
|                    |        |                    |               |
|                    |        | -  lowLimit: state |               |
|                    |        |       of low limit |               |
|                    |        |       switch       |               |
|                    |        |                    |               |
|                    |        | -  highLimit:      |               |
|                    |        |       state of     |               |
|                    |        |       high limit   |               |
|                    |        |       switch       |               |
|                    |        |                    |               |
|                    |        | -  homeSwitch:     |               |
|                    |        |       state of     |               |
|                    |        |       home switch  |               |
|                    |        |                    |               |
|                    |        | -  axisDone: state |               |
|                    |        |       of axis done |               |
|                    |        |       bit          |               |
+--------------------+--------+--------------------+---------------+
| dr                 |        | Return the OMS     | OK, card type |
| vOmsVmeGetCardType |        | card type.         |               |
|                    |        |                    |               |
|                    |        | Parameter:         |               |
|                    |        |                    |               |
|                    |        | -  card: card to   |               |
|                    |        |       query        |               |
+--------------------+--------+--------------------+---------------+
| drvOms             |        | Recover current    | OK            |
| VmeGetErrorMessage |        | error message.     |               |
|                    |        |                    |               |
|                    |        | Parameter:         |               |
|                    |        |                    |               |
|                    |        | -  Message: error  |               |
|                    |        |       message      |               |
+--------------------+--------+--------------------+---------------+
| drvOmsVmeInit      |        | Locate and         | OK, ERROR     |
|                    |        | initialize all OMS |               |
|                    |        | Vme boards         |               |
+--------------------+--------+--------------------+---------------+
| drvOmsVmeReadCard  |        | Recover data read  | OK, ERROR     |
|                    |        | from an OMS VME    |               |
|                    |        | card.              |               |
|                    |        |                    |               |
|                    |        | Parameters:        |               |
|                    |        |                    |               |
|                    |        | -  card: card to   |               |
|                    |        |       read from    |               |
|                    |        |                    |               |
|                    |        | -  Buffer: string  |               |
|                    |        |       returned by  |               |
|                    |        |       card         |               |
|                    |        |                    |               |
|                    |        | -  Timeout:        |               |
|                    |        |       maximum time |               |
|                    |        |       to wait for  |               |
|                    |        |       response     |               |
|                    |        |       (sys ticks)  |               |
+--------------------+--------+--------------------+---------------+
| drvOmsVmeWriteCard |        | Write a message to | OK, ERROR     |
|                    |        | an OMS VME card.   |               |
|                    |        |                    |               |
|                    |        | Parameters:        |               |
|                    |        |                    |               |
|                    |        | -  card: card to   |               |
|                    |        |       read from    |               |
|                    |        |                    |               |
|                    |        | -  Buffer: string  |               |
|                    |        |       returned by  |               |
|                    |        |       card         |               |
+--------------------+--------+--------------------+---------------+
| d                  |        | Write a message to | OK, ERROR     |
| rvOmsVmeWriteMotor |        | a specific OMS VME |               |
|                    |        | axis.              |               |
|                    |        |                    |               |
|                    |        | Parameters:        |               |
|                    |        |                    |               |
|                    |        | -  card: card      |               |
|                    |        |       number to    |               |
|                    |        |       write to     |               |
|                    |        |                    |               |
|                    |        | -  axis: axis on   |               |
|                    |        |       the card to  |               |
|                    |        |       get the      |               |
|                    |        |       message      |               |
|                    |        |                    |               |
|                    |        | -  Buffer: message |               |
|                    |        |       to be sent   |               |
|                    |        |       to axis      |               |
+--------------------+--------+--------------------+---------------+

.. _vme-interrupts-request-vector-and-handlers-1:

2.14.4 VME Interrupts Request Vector and Handlers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+--------------------------+--------------+--------------------------+
| Vector base from         | Handler      | Comment                  |
| drvOmsVmeInit()          |              |                          |
+==========================+==============+==========================+
| DRV_OMS_VME_VECTOR       | drvOmsVmeIsr | ISR for OMS VME motion   |
|                          |              | control cards            |
+--------------------------+--------------+--------------------------+

2.14.5 OMS44 Data Scan Threads
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The OMS44 driver has dedicated thread (omsScanTask). It runs through the
list of motors that have deviceControl records controlling them and
manages their operation

.. _specific-requirements-9:

2.14.6 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.14.6.1 REQ-114-01 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The OMS44 support library shall be capable of executing the
drvOmsVmeInit() routine without error by calling iocInit().

2.14.6.2 REQ-114-02 OMS44 Motor Record Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The OMS44 support library shall be capable of initializing and
processing an EPICS Motor record with its DTYP “OMS 8/44”.

2.15 CAN Bus
------------

This section describes the software interface to the TEWS TIP810
Industry-Pack Module for EPICS. This software is an IPAC Module Driver
and uses the services of the drvIpac Industry Pack Driver to interface
to the IPAC Carrier Board.

The software provides an interface to higher level software which, other
than device initialisation, is not specific to the TIP810 but could be
used with a CANbus driver for a different hardware interface. The
interface to the higher level software is provided in two header files:
*canBus.h* contains all of the generic CANbus definitions and
declarations, while *drvTip810.h* incorporates the additional
declarations and definitions which are specific to the TIP810 module.
The routines which are specific to the TIP810 or meant for use from the
RTEMS shell are described individually in this section, also the generic
CANbus interface routines are described below.

The TEWS TIP810 IP module contains a Philips pca82c200 stand-alone
CAN-controller chip which performs all of the CANbus interfacing
functions. The interface to this chip is defined in a separate header
file *pca82c200.h* which declares the register interface structure and
the bit-patterns for the various on-chip registers.

The CAN-controller chip does not contain the necessary interrupt vector
logic required by the IndustryPack interface however, so the TIP810
module implements this in additional logic, including a register to hold
the vector number to be used.

.

2.15.1 Database Definition File (devTip810.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

device(ai,INST_IO,devAiCan,"CANbus")

device(ao,INST_IO,devAoCan,"CANbus")

device(bi,INST_IO,devBiCan,"CANbus")

device(bo,INST_IO,devBoCan,"CANbus")

device(mbbi,INST_IO,devMbbiCan,"CANbus")

device(mbbo,INST_IO,devMbboCan,"CANbus")

devTip810.dbd:device(bi,INST_IO,devBiTip810,"Tip810")

device(stringin, INST_IO,devSiWiener,"CANbus")

device(mbbiDirect,INST_IO,devMbbiDirectCan,"CANbus")

device(mbboDirect,INST_IO,devMbboDirectCan,"CANbus")

.. _device-support-requirements-5:

2.15.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These are the device support provided with the TEWS TIP810 Industry-Pack
CANbus driver under EPICS.

Device Support routines have been provided for the following record
types:

-  Analogue Records (ai, ao)

-  Binary Records (bi, bo)

-  Multi-Bit Binary Records (mbbi, mbbo)

-  Multi-Bit Binary Direct Records (mbbiDirect, mbboDirect)

The device support behaviour for these record types is substantially the
same.

Also available is the ability to check the status of the Tip810 CANbus
interface, using:

-  Binary Input Records to access the TIP810 module status (bi)

The support for this record type is significantly different to the
others

1.  devAiCan -- Provide Analog Input support for the CANbus access.

2.  devAoCan -- Provide Analog Output support for the CANbus access.

3.  devBiCan -- Provide Binary Input support for the CANbus access.

4.  devBoCan -- Provide Binary Output support for the CANbus access.

5.  devMbbiCan -- Provide Multi Bits Binary Input support for the CANbus
       access.

6.  devMbboCan -- Provide Multi Bits Binary Output support for the
       CANbus access.

7.  devSiWiener -- Provide String Input support for the CANbus access.

8.  devMbbiDirectCan -- Provide Multi Bits Binary Input Direct support
       for the CANbus access.

9.  devMbboDirectCan -- Provide Multi Bits Binary Output Direct support
       for the CANbus access.

10. devBiTip810 -- Provide Binary Input support for the Tip810 access.

.. _driver-support-requirements-5:

2.15.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This section describes the software interface to the TEWS TIP810
Industry-Pack Module for EPICS. This software is an IPAC Module Driver
and uses the services of the drvIpac Industry Pack Driver to interface
to the IPAC Carrier Board.

The following routines provided by this driver are described in detail
below:

epicsShareFunc int t810Create(char \*busName, int card, int slot, int
irqNum, int busRate);

epicsShareFunc void t810Shutdown(void \*dummy);

epicsShareFunc int t810Initialise(void);

epicsShareFunc int t810Report(int page);

epicsShareFunc int t810Status(canBusID_t busID);

int canTest (char \*pbusName, canID_t identifier, int rtr, int length,
char \*data);

int canOpen (char \*busName, canBusID_t \*pbusID);

int canIoParse (char \*canString, canIo_t \*pcanIo);

int canWrite (canBusID_t busID, const canMessage_t \*pmessage, int
timeout);

int canMessage (canBusID_t busID, canID_t identifier, canMsgCallback_t
\*pcallback, void \*pprivate);

int canMsgDelete (canBusID_t busID, canID_t identifier, canMsgCallback_t
\*pcallback, void \*pprivate);

int canSignal (canBusID_t busID, canSigCallback_t \*pcallback, void
\*pprivate);

int canBusReset (char \*busName);

int canBusStop (char \*busName);

int canBusReset (char \*busName);

int canRead (canBusID_t busID, canMessage_t \*pmessage, double timeout);

+----------------+-----------------+-----------------+-------------+
| **Function**   | **Usedby**      | **Meaning**     | **Returns** |
+================+=================+=================+=============+
| t810Create     | IOC Shell       | Configure VME   | OK, ERROR   |
|                | Startup         | for IPAC with:  |             |
|                |                 |                 |             |
|                |                 | -  pbusName:    |             |
|                |                 |       unique    |             |
|                |                 |       CAN Bus   |             |
|                |                 |       Id        |             |
|                |                 |                 |             |
|                |                 | -  card: IPAC   |             |
|                |                 |       driver    |             |
|                |                 |       carrier   |             |
|                |                 |                 |             |
|                |                 | -  slot: IPAC   |             |
|                |                 |       driver    |             |
|                |                 |       slot      |             |
|                |                 |                 |             |
|                |                 | -  irqNum:      |             |
|                |                 |       Interrupt |             |
|                |                 |       vector    |             |
|                |                 |       number    |             |
|                |                 |                 |             |
|                |                 | -  busRate: CAN |             |
|                |                 |       bus rate  |             |
|                |                 |                 |             |
|                |                 |      Kbits/sec. |             |
+----------------+-----------------+-----------------+-------------+
| t810Shutdown   |                 | Shutdown        | void        |
|                |                 | routine, resets |             |
|                |                 | all devices to  |             |
|                |                 | stop            |             |
|                |                 | interrupts.     |             |
+----------------+-----------------+-----------------+-------------+
| t810Initialise |                 | Initialise      | OK, ERROR   |
|                |                 | driver and all  |             |
|                |                 | registered      |             |
|                |                 | hardware.       |             |
+----------------+-----------------+-----------------+-------------+
| t810Report     | IOC Shell       | Register        | OK, ERROR   |
|                |                 | function with   |             |
|                |                 | EPICS dbior().  |             |
|                |                 | Report shows    |             |
|                |                 | the state of    |             |
|                |                 | all initialized |             |
|                |                 | IPAC boards and |             |
|                |                 | their           |             |
|                |                 | configuration.  |             |
+----------------+-----------------+-----------------+-------------+
| t810Status     |                 |                 |             |
+----------------+-----------------+-----------------+-------------+
| canTest        | IOC Shell       | Test routine,   | OK, ERROR   |
|                |                 | sends a single  |             |
|                |                 | test message to |             |
|                |                 | the named       |             |
|                |                 | CANbus.         |             |
+----------------+-----------------+-----------------+-------------+
| canOpen        |                 | Return device   | OK, ERROR   |
|                |                 | pointer for     |             |
|                |                 | given CAN bus   |             |
|                |                 | name.           |             |
+----------------+-----------------+-----------------+-------------+
| canIoParse     |                 | Parse a CAN     | OK, ERROR   |
|                |                 | address string  |             |
|                |                 | into a canIo_t  |             |
|                |                 | structure       |             |
+----------------+-----------------+-----------------+-------------+
| canWrite       |                 | Writes a        | OK, ERROR   |
|                |                 | message to the  |             |
|                |                 | given CANbus    |             |
+----------------+-----------------+-----------------+-------------+
| canMessage     |                 | Register CAN    | OK, ERROR   |
|                |                 | message         |             |
|                |                 | call-back       |             |
+----------------+-----------------+-----------------+-------------+
| canMsgDelete   |                 | Delete CAN      | OK, ERROR   |
|                |                 | message         |             |
|                |                 | call-back       |             |
+----------------+-----------------+-----------------+-------------+
| canSignal      |                 | Register CAN    | OK, ERROR   |
|                |                 | error signal    |             |
|                |                 | call-back       |             |
+----------------+-----------------+-----------------+-------------+
| canBusReset    | IOC Shell       | Reset CAN chip  | OK, ERROR   |
|                |                 | and message and |             |
|                |                 | error counters. |             |
+----------------+-----------------+-----------------+-------------+
| canBusStop     | IOC Shell       | Stop CAN        | OK, ERROR   |
|                |                 | interface.      |             |
+----------------+-----------------+-----------------+-------------+
| canBusRestart  | IOC Shell       | Restart a       | OK, ERROR   |
|                |                 | stopped CAN     |             |
|                |                 | interface.      |             |
+----------------+-----------------+-----------------+-------------+
| canRead        |                 | Send Remote     | OK, ERROR   |
|                |                 | Transmission    |             |
|                |                 | Request and     |             |
|                |                 | wait for reply. |             |
+----------------+-----------------+-----------------+-------------+

.. _vme-interrupts-request-vector-and-handlers-2:

2.15.4 VME Interrupts Request Vector and Handlers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+-------------------------------+---------+-------------------------------------+
| Vector base from t810Create() | Handler | Comment                             |
+===============================+=========+=====================================+
| baseIrqVector=50              | t810ISR | ISR for CAN bus receipt acknowledge |
+-------------------------------+---------+-------------------------------------+

.. _specific-requirements-10:

2.15.5 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.15.5.1 REQ-115-01 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of executing the t810Create
routine without error by calling

iocInit().

2.15.5.2 REQ-115-02 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior t810Report <interest_level> shall display the
state of all data channels respective for each initialized card.

2.15.5.3 REQ-115-03 Analog Input Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS analog input record with its DTYP field set to
CANbus.

2.15.5.4 REQ-115-04 Analog Output Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS analog output record with its DTYP field set to
CANbus.

2.15.5.5 REQ-115-05 Binary Input Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS binary input record with its DTYP field set to
CANbus.

2.15.5.6 REQ-115-06 Binary Output Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS binary output record with its DTYP field set to
CANbus.

2.15.5.7 REQ-115-07 Multibit Binary Input Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS mbbi record with its DTYP field set to CANbus.

2.15.5.8 REQ-115-08 Multibit Binary Output Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS mbbo record with its DTYP field set to CANbus.

2.15.5.9 REQ-115-09 Multibit Binary Input Direct Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS mbbi direct record with its DTYP field set to
CANbus.

2.15.5.10 REQ-115-10 Multibit Binary Output Direct Record Support for CANbus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS mbbo direct record with its DTYP field set to
CANbus.

2.15.5.11 REQ-115-11 Binary Input Record Support for Tip810
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of initializing and
processing an EPICS binary input record with its DTYP field set to
Tip810.

2.15.5.12 REQ-115-12 Two Card Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The TIP810 support library shall be capable of configuring and
initializing two tip810 boards. All scan tasks shall be running and not
suspended after iocInit() completes. Each TIP810 task running shall have
the respective card number appended to the name of the task.

2.16 drvSerial
--------------

drvSerial is an EPICS driver that can be used to send and receive binary
data through a serial port. The notes on how it is used can be found
`here <http://swg.wikis-internal.gemini.edu/index.php/DrvSerial_Notes>`__.

2.16.1 Database Definition File (drvSerial.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

driver(drvSerial)

.. _device-support-requirements-6:

2.16.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

No device support is provided by the drvSerial driver.

2.16.3 Driver Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~

The EPICS driver support requires the following routines:

+----------------------------------------------------------------------+
| long drvSerialInit (void);                                           |
|                                                                      |
| long drvSerialCreateLink(const char \*pName, drvSerialParseInput     |
| \*pParser, void \*pAppPrivate,                                       |
|                                                                      |
| drvSerialLinkId \*pId);                                              |
|                                                                      |
| long drvSerialAttachLink(const char \*pName, drvSerialParseInput     |
| \*pParser, void \**ppAppPrivate);                                    |
|                                                                      |
| long drvSerialSendRequest(drvSerialLinkId id, drvSerialPriority pri, |
| const drvSerialRequest \* pReq) ;                                    |
|                                                                      |
| long drvSerialNextResponse(drvSerialLinkId id, drvSerialResponse \*  |
| pResponse);                                                          |
|                                                                      |
| long drvSerialReport(int level);                                     |
+----------------------------------------------------------------------+

+----------------+----------------+----------------+----------------+
| Function       | Usedby         | Meaning        | Returns        |
+================+================+================+================+
| drvSerialInit  | EPICS          | -  Create hash | S_drvSerial_OK |
|                | iocInit()      |       table    |                |
|                |                |       for the  | S_db_noMemory  |
|                |                |       file     |                |
|                |                |       name     |                |
|                |                |       strings  |                |
|                |                |       and      |                |
|                |                |                |                |
|                |                |     associated |                |
|                |                |       MUTEX    |                |
|                |                |                |                |
|                |                | -  Create an   |                |
|                |                |       EPICS    |                |
|                |                |       “bucket” |                |
|                |                |       for,     |                |
|                |                |                |                |
|                |                |      integers, |                |
|                |                |                |                |
|                |                |      pointers, |                |
|                |                |       and      |                |
|                |                |       strings. |                |
+----------------+----------------+----------------+----------------+
| drvSe          | Device support | -  Create and  | S_drvSerial_OK |
| rialCreateLink |                |       open new | S_dr           |
|                |                |       serial   | vSerial_noInit |
|                |                |       link     | S_drvS         |
|                |                |                | erial_noParser |
|                |                | -  Spawn read  |                |
|                |                |       and      | S_dr           |
|                |                |       write    | vSerial_linkIn |
|                |                |       tasks    |                |
|                |                |                | S_dev_noMemory |
|                |                |                |                |
|                |                |                | (-1)           |
+----------------+----------------+----------------+----------------+
| drvSe          | Device support | Attach to      | S_drvSerial_OK |
| rialAttachLink |                | already open   | S_drvS         |
|                |                | link           | erial_noParser |
|                |                |                | S_drvSe        |
|                |                |                | rial_linkInUse |
|                |                |                | S_drvSeria     |
|                |                |                | l_noneAttached |
+----------------+----------------+----------------+----------------+
| drvSer         | Device support | Send data      | S_drvSerial_OK |
| ialSendRequest |                |                | S_drvS         |
|                |                |                | erial_linkDown |
|                |                |                | S_drvSer       |
|                |                |                | ial_invalidArg |
|                |                |                | S_drvSe        |
|                |                |                | rial_queueFull |
|                |                |                | S_dev_noMemory |
|                |                |                |                |
|                |                |                | (-1)           |
+----------------+----------------+----------------+----------------+
| drvSeri        | Device support | Receive data   | S_drvSerial_OK |
| alNextResponse |                |                | S_drv          |
|                |                |                | Serial_noEntry |
|                |                |                |                |
|                |                |                | (-1)           |
+----------------+----------------+----------------+----------------+
| d              | EPICS dbior()  | Report to show | S_drvSerial_OK |
| rvSerialReport |                | the active     |                |
|                |                | serial queues. |                |
+----------------+----------------+----------------+----------------+

.. _specific-requirements-11:

2.16.4 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.16.4.1 REQ-116-01 drvSerial Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The drvSerial driver shall be capable of executing the drvSerianInit()
routine without error by calling iocInit().

2.16.4.2 REQ-116-02 drvSerial Attaching to an Existing Connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The drvSerial driver shall be able to attach to an already open
connection to a serial port and return no errors. Attaching to a
non-existent connection should return an error.

2.16.4.3 REQ-116-03 drvSerial Opening a New Connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The drvSerial driver shall be capable of connecting to an existing
serial port and return without errors. Connecting to a non-existent port
should return an error.

2.16.4.4 REQ-116-04 drvSerial Sending a Message (Request)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The drvSerial driver shall be able to send a message through an open
connection (new or attached) without errors. Sending a message to an
invalid connection shall return an error. The driver send routine shall
not block. Sending more than a 100 messages without other process
removing them from the queue shall return an error.

2.16.4.5 REQ-116-05 drvSerial Receiving a Response
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The drvSerial driver should be able to receive a response from an open
connection without errors. Trying to receive a response from an invalid
connection should return an error. The driver receiving routine shall
return an error if there are no messages pending in the queue (shall not
block)

2.16.4.6 REQ-116-06 drvSerial EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvSerial( <interest_level>) shall display the
state of all open links. See drvSerialReport().

2.17 AbDf1
----------

The Allen-Bradley PLC-5 Programmable Logic Controllers (PLC) provide a
proven approach for industrial control. PLC-5 processors are high-speed,
single-slot processors used for control and information processing. They
are designed for larger sequential and regulatory control applications
with specialized I/O requirements and/or the need to coordinate with
other processors and devices. Ladder logic, the PLC’s programming
language represents a program by a graphical diagram based on the
circuit diagrams of relay logic hardware. PLC-5 are programmed using a
Windows program called RSLogix5. The PLC communicates using an
asynchronous byte-oriented protocol that is used to communicate with
most Allen Bradley RS232 interface modules, called DF1. It works over
half duplex and full duplex modes of communication. The AbDf1 driver
implements the DF1 protocol and uses uses drvSerial to talk to serial
lines.

Note that the mbbi or mbbo records are supported in the code, but since
they are not used by in the ECS or GIS, they were not included in the
requirements. Also, the support for the ecsMotor record device support
was not included because the goal is to remove this record when the ECS
is ported to use the CCB..

2.17.1 Database Definition File (AbDf1.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Allen Bradley DF1 Device Support

device( ai, INST_IO, devAiAbDf1, "AB DF1 serial" )

device( ao, INST_IO, devAoAbDf1, "AB DF1 serial" )

device( bi, INST_IO, devBiAbDf1, "AB DF1 serial" )

device( bo, INST_IO, devBoAbDf1, "AB DF1 serial" )

device( mbbi, INST_IO, devMbbiAbDf1, "AB DF1 serial" )

device( mbbo, INST_IO, devMbboAbDf1, "AB DF1 serial" )

# Variables that can be set from the IOC shell

variable(drvAbDf1SrcStationNumber, int)

variable(drvAbDf1DefaultScanPeriod_mS, double)

variable(drvAbDf1Debug, int)

variable(drvAbDf1MaxOutstandingRequest, int)

# Allen Bradley Driver Support

driver(drvAbDf1)

# register the IOC shell commands

registrar(drvAbDf1CommandsRegister)

.. _device-support-requirements-7:

2.17.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following device support routines are required to communicate with
the PLC-5 using analog input, analog output, binary input, binary
output, multi bit binary input, and multi bit binary output records. We
use the generic support routines only:

1. devAiAbDf1 - Provide analog input support for the DF1 protocol

2. devAoAbDf1 - Provide analog output support for the DF1 protocol

3. devBiAbDf1 - Provide binary input support for the DF1 protocol

4. devBoAbDf1- Provide binary input support for the DF1 protocol

.. _driver-support-requirements-6:

2.17.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.17.3.1 Functions

The following driver functions are called by the device support
routines. Only a subset of them provide aspects of functionality
required at Gemini.

abDf1ElemIO \*drvAbDf1NewElemIO (void);

void drvAbDf1InitiateAll(void);

epicsInt32 drvAbDf1SetupIO (const struct link \*pLink, abDf1ElemIO
\*pDevIO, unsigned \*pBitNo);

void drvAbDf1SetupScan (const int cmd, abDf1ElemIO \*pElemIO);

epicsInt32 drvAbDf1InitiateWrite (abDf1ElemIO \*pDevElemIO)

epicsInt32 drvAbDf1ReadWord (abDf1ElemIO \*pDevElemIO, epicsUInt16
\*pVal);

epicsInt32 drvAbDf1ReadBitString (abDf1ElemIO \*pDevElemIO, epicsUInt16
mask, epicsUInt16 \*pVal);

epicsInt32 drvAbDf1ReadReal (abDf1ElemIO \*pDevElemIO, float \*pVal);

+----------------+----------------+----------------+----------------+
| Function       | Usedby         | Meaning        | Returns        |
+================+================+================+================+
| drv            | Device support | Allocate and   | Structure      |
| AbDf1NewElemIO | record         | clear an       | pointer        |
|                | i              | element IO     |                |
|                | nitialization. | structure.     | NULL           |
|                |                | Driver embeds  |                |
|                |                | the element IO |                |
|                |                | structure      |                |
|                |                | within a       |                |
|                |                | larger driver  |                |
|                |                | specific       |                |
|                |                | structure.     |                |
+----------------+----------------+----------------+----------------+
| drvAb          | Device support | Called after   | void           |
| Df1InitiateAll | i              | all records    |                |
|                | nitialization. | have been      |                |
|                |                | initialized to |                |
|                |                | initiate       |                |
|                |                | scanning on    |                |
|                |                | all            |                |
|                |                | links/cards.   |                |
+----------------+----------------+----------------+----------------+
| d              | Device support | Called by the  | S_drvAbD       |
| rvAbDf1SetupIO | i              | device support | f1_badAddrType |
|                | nitialization. | "Init Record"  |                |
|                |                | routine to     | S_drvAbDf1     |
|                |                | parse the      | _badNodeNumber |
|                |                | hardware       |                |
|                |                | address        | S_dr           |
|                |                | information    | vAbDf1_badFile |
|                |                | and initialize |                |
|                |                | the element IO | S_drvAb        |
|                |                | structure.     | Df1_badElement |
|                |                |                |                |
|                |                |                | S_drvAbDf      |
|                |                |                | 1_badBitNumber |
|                |                |                |                |
|                |                |                | S_drvAbDf1     |
|                |                |                | _badSubelement |
|                |                |                |                |
|                |                |                | S_drvAbDf1_OK  |
+----------------+----------------+----------------+----------------+
| drv            | Device support | Called by the  | void           |
| AbDf1SetupScan | get i/o        | device support |                |
|                | interrupt info | "Init Record"  |                |
|                | routine.       | routine to     |                |
|                |                | parse the      |                |
|                |                | hardware       |                |
|                |                | address        |                |
|                |                | information    |                |
|                |                | and initialize |                |
|                |                | the element IO |                |
|                |                | structure.     |                |
+----------------+----------------+----------------+----------------+
| drvAbDf        | Device support | Initiates a    | S_drvAbD       |
| 1InitiateWrite | write routine. | write          | f1_uknDataType |
|                |                | operation.     |                |
|                |                | Integer words, | S_drv          |
|                |                | bit strings,   | AbDf1_noMemory |
|                |                | and floating   |                |
|                |                | point          | S_drvAbDf1     |
|                |                | currently      | _notSubscribed |
|                |                | supported.     |                |
|                |                |                | S_drvAbDf1_a   |
|                |                |                | syncCompletion |
|                |                |                |                |
|                |                |                | S_drv          |
|                |                |                | AbDf1_mutexTMO |
|                |                |                |                |
|                |                |                | S_df1_BadAddr  |
|                |                |                |                |
|                |                |                | S_df1_EOF      |
|                |                |                |                |
|                |                |                | S_df1_BadType  |
|                |                |                |                |
|                |                |                | S_drvAbDf1_OK  |
+----------------+----------------+----------------+----------------+
| dr             | Device support | Reads current  | S_drvAbDf1     |
| vAbDf1ReadWord | read routine.  | value from the | _notSubscribed |
|                |                | cache. Returns |                |
|                |                | unsigned word  | S_drv          |
|                |                | value.         | AbDf1_mutexTMO |
|                |                |                |                |
|                |                |                | S_df1_BadAddr  |
|                |                |                |                |
|                |                |                | S_df1_EOF      |
|                |                |                |                |
|                |                |                | S_df1_BadType  |
|                |                |                |                |
|                |                |                | S_drvAbDf1_OK  |
+----------------+----------------+----------------+----------------+
| drvAbDf        | Device support | Reads current  | S_drvAbDf1     |
| 1ReadBitString | binary input   | value from the | _notSubscribed |
|                | routine.       | cache. Returns |                |
|                |                | bit string.    | S_drv          |
|                |                |                | AbDf1_badParam |
|                |                |                |                |
|                |                |                | S_drv          |
|                |                |                | AbDf1_mutexTMO |
|                |                |                |                |
|                |                |                | S_df1_BadAddr  |
|                |                |                |                |
|                |                |                | S_df1_EOF      |
|                |                |                |                |
|                |                |                | S_df1_BadType  |
|                |                |                |                |
|                |                |                | S_drvAbDf1_OK  |
+----------------+----------------+----------------+----------------+
| dr             | Device support | Reads current  | S_drvAbDf1     |
| vAbDf1ReadReal | analog input   | value from the | _notSubscribed |
|                | routine        | cache. Returns |                |
|                |                | IEEE           | S_drv          |
|                |                | si             | AbDf1_badParam |
|                |                | ngle-precision |                |
|                |                | floating point | S_drv          |
|                |                | value.         | AbDf1_mutexTMO |
|                |                |                |                |
|                |                |                | S_df1_BadAddr  |
|                |                |                |                |
|                |                |                | S_df1_EOF      |
|                |                |                |                |
|                |                |                | S_df1_BadType  |
|                |                |                |                |
|                |                |                | S_drvAbDf1_OK  |
+----------------+----------------+----------------+----------------+

2.17.3.1 Global Variables
^^^^^^^^^^^^^^^^^^^^^^^^^

The following variables can be used to configure and/or change the
behaviour of the driver.

+----------------+----------------+----------------+----------------+
| Variable       | Usedby         | Meaning        | Data Type      |
+================+================+================+================+
| drvAbDf1Sr     | EPICS startup  | The            | Unsigned       |
| cStationNumber | script         | Allen-Bradley  | integer        |
|                |                | node (station) |                |
|                |                | number for the | (default 012)  |
|                |                | host that is   |                |
|                |                | running this   |                |
|                |                | code. Should   |                |
|                |                | be defined     |                |
|                |                | before calling |                |
|                |                | iocInit()      |                |
+----------------+----------------+----------------+----------------+
| drvAbDf1Defaul | EPICS startup  | The number of  | Double         |
| tScanPeriod_mS | script         | milliseconds   |                |
|                |                | between scans. | (default       |
|                |                | Should be      | 100.0)         |
|                |                | defined before |                |
|                |                | calling        |                |
|                |                | iocInit().     |                |
+----------------+----------------+----------------+----------------+
| d              | EPICS shell    | The maximum    | Unsigned       |
| rvAbDf1MaxOuts |                | outstanding    | integer        |
| tandingRequest |                | requests (the  |                |
|                |                | maximum        | (default 16)   |
|                |                | simultaneous   |                |
|                |                | unanswered     |                |
|                |                | requests       |                |
|                |                | allowed).      |                |
|                |                | Should be      |                |
|                |                | changed        |                |
|                |                | occasionally.  |                |
+----------------+----------------+----------------+----------------+
| drvAbDf1Debug  | EPICS shell    | Turn on/off    | Unsigned       |
|                |                | debug          | integer        |
|                |                | messages.      | (default 0)    |
|                |                | Increasing     |                |
|                |                | detail with    |                |
|                |                | increasing     |                |
|                |                | value.         |                |
+----------------+----------------+----------------+----------------+

.. _vme-interrupts-request-vector-and-handlers-3:

2.17.4 VME Interrupts Request Vector and Handlers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

No interrupt handlers are used in this driver.

2.17.5 PLC Scan Threads
~~~~~~~~~~~~~~~~~~~~~~~

The PLC driver has one dedicated thread per PLC to scan for updates in
the PLC memory. The threads are in charge of processing all responses
that are pending in drvSerial.

.. _specific-requirements-12:

2.17.6 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.17.6.1 REQ-117-01 Initialize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PLC support library shall be capable of executing the
drvAbDf1InitiateAll() routine without error by calling

iocInit().

2.17.6.2 REQ-117-02 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvAbDf1 <interest_level> shall display the
state of all data channels respective for each initialized card (in
VxWorks it doesn’t print anything).

2.17.6.3 REQ-117-03 Analog Input Record Support for AB DF1 serial(SCAN I/O Intr)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The AbDf1 support library shall be capable of initializing and
processing an EPICS analog input record with its DTYP field set to AB
DF1 serial. SCAN= I/O Intr.

2.17.6.4 REQ-117-04 Analog Output Record Support for AB DF1 serial
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The AbDf1 support library shall be capable of initializing and
processing an EPICS analog output record with its DTYP field set to AB
DF1 serial.

2.17.6.5 REQ-117-05 Binary Input Record Support for AB DF1 serial(SCAN I/O Intr)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The AbDf1 support library shall be capable of initializing and
processing an EPICS binary input record with its DTYP field set to AB
DF1 serial. SCAN= I/O Intr.

2.17.6.6 REQ-117-06 Binary Output Record Support for AB DF1 serial
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The AbDf1 support library shall be capable of initializing and
processing an EPICS binary output record with its DTYP field set to AB
DF1 serial.

2.17.6.7 REQ-117-25 Two PLC Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The AbDf1 support library shall be capable of communicate with two
Allen-Bradley PLC’s connected to two separate serial ports. All scan
tasks shall be running and not suspended after iocInit() completes. Each
AbDf1 task running shall have the name “AB DF1”.

2.18 tcslib
-----------

The tcslib requirements are very extensive and it is not practical (at
this writing) to itemize every function here. Further, this would yield
a low return on the investment. The tcslib itself has not been
refactored for OSI system calls. All the tcslib calls are standard C
calls and don’t invoke RTOS utilities such as semaphores, message queues
and threads.

.. _specific-requirements-13:

2.18.1 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Although there are no EPICS OSI specific requirements, the following
requirements are defined for compatibility with the build environment
and systems depending on slalib.

2.18.1.1 REQ-118-01 tcslib ADE Compatibility
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The tcslib library shall be compatible with the Application Development
Environment (ADE ), and build without errors or warnings.

2.18.1.2 REQ-118-02 tcslib Available for Linking
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The tcslib library shall be available for linking and testing with other
CCB components.

.. _section-1:

2.19 VMIC-5588
--------------

VMIVME-5588 is a high-performance, daisy-chained VME-to-VME network.
Data is transferred by writing to on-board global RAM. The data is
automatically sent to the location in memory on all Reflective Memory
boards on the network.

VMIC’s VMIVME-5588 Reflective Memory interface allows data to be shared
between up to 256 independent systems (nodes) at rates up to 29.5
Mbyte/s. Each Reflective Memory board may be configured with 256 Kbyte
to 16 Mbyte of on-board SRAM. The local SRAM provides fast Read access
times to stored data. Writes are stored in local SRAM and broadcast over
a high-speed data path to other Reflective Memory nodes. The transfer of
data between nodes is software transparent, so no I/O overhead is
required. Transmit and Receive FIFOs buffer data during peak data rates
to optimize

The Reflective Memory also allows interrupts to one or more nodes by
writing to a byte register. These interrupt (three levels, each user
definable) signals may be used to synchronize a system process, or used
to follow any data. The interrupt always follows the data to ensure the
reception of the data before the interrupt is acknowledged. The
VMIVME-5588 requires no initialization unless interrupts are being used.
If interrupts are used, vectors and interrupt levels must be written to
on-board registers and the interrupts enabled.

2.19.1 Database Definition File (vmi5588.dbd)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   | device( bi, INST_IO, devBiVmi5588, "VMI5588 Synchro Bus" )
   | driver(drvVmi5588)
   | registrar(drvVmi5588RegisterCommands)
   | variable(intr0cnt, int)
   | variable(intr1cnt, int)
   | variable(intr2cnt, int)
   | variable(intr3cnt, int)
   | variable(latestIrqNum, int)

.. _device-support-requirements-8:

2.19.2 Device Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

devBiVmi5588 --Provide binary input support using INST_IO for EPICS
records with Interrupt I/O scanning.

.. _driver-support-requirements-7:

2.19.3 Driver Support Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The vmi-5588 driver provides contains support routines for the VMIC
VMIVME5588 Reflective

Memory card, for use both from EPICS and from a C subroutine. The EPICS
device support requires the following routines.

long vmi5588_init(void);

long vmi5588_report (int level);

LOCAL void vmi5588_reboot (void \*p);

void vmi5588_intr(void \*p);

long rmIntConnect(int irqNumber, void (*proutine)(int));

long rmIntDisconnect(int irqNumber);

long rmIntSend(int irqNumber,int nodeId);

long rmNodeId(void);

unsigned long rmStatus(long reset);

void vmi5588_pageISR(int rmNodeId);

long vmi5588_pageInit(short rmPage);

void vmi5588_pvtInit(struct rmpvt \**ppdpvt, short rmPage,short
rmOffset,struct rm_data \*prmData);

long vmi5588_getIoscanpvt(struct rmpvt \*pdpvt, IOSCANPVT \*pscanpvt);

long vmi5588_trigger(short rmPage);

void \*rmPageMemBase(void);

+----------------+----------------+----------------+----------------+
| **Function**   | **Usedby**     | **Meaning**    | **Returns**    |
+================+================+================+================+
| vmi5588_init   | EPICS          | -  registers   | OK, or         |
|                | iocInit()      |       the card | S_dev_???      |
|                |                |       with the | errors.        |
|                |                |       devLib   |                |
|                |                |       address  |                |
|                |                |       routines |                |
|                |                |                |                |
|                |                | -  makes sure  |                |
|                |                |       that a   |                |
|                |                |       VMIC5588 |                |
|                |                |       card is  |                |
|                |                |       present  |                |
|                |                |       at the   |                |
|                |                |       given    |                |
|                |                |       location |                |
|                |                |                |                |
|                |                | -  enable      |                |
|                |                |       VMEbus   |                |
|                |                |                |                |
|                |                |      interrupt |                |
|                |                |       level    |                |
|                |                |       needed   |                |
|                |                |       by this  |                |
|                |                |       card     |                |
|                |                |                |                |
|                |                | -  initialise  |                |
|                |                |       the card |                |
|                |                |                |                |
|                |                |   interrupters |                |
|                |                |                |                |
|                |                | -  turn any    |                |
|                |                |                |                |
|                |                |     interrupts |                |
|                |                |       off if   |                |
|                |                |       we do a  |                |
|                |                |       reboot   |                |
|                |                |                |                |
|                |                | -  turn off    |                |
|                |                |       the FAIL |                |
|                |                |       LED      |                |
+----------------+----------------+----------------+----------------+
| vmi5588_report |                | -  driver      | OK, or         |
|                |                |       status   | S_dev_NoInit   |
|                |                |       report   | if driver not  |
|                |                |                | initialised.   |
|                |                |      function, |                |
|                |                |       which    |                |
|                |                |       displays |                |
|                |                |       the      |                |
|                |                |       current  |                |
|                |                |       status   |                |
|                |                |       of the   |                |
|                |                |       hardware |                |
|                |                |       when     |                |
|                |                |       called   |                |
+----------------+----------------+----------------+----------------+
| vmi5588_reboot | epicsAtExit    | reboot Hook by |                |
|                |                | vmi5588_init,  |                |
|                | devAiXy566Di.c | and is         |                |
|                |                | responsible    |                |
|                | d              | for turning    |                |
|                | evAiXy566DiL.c | off RM         |                |
|                |                | interrupts.    |                |
|                | devAiXy566Se.c |                |                |
+----------------+----------------+----------------+----------------+
| vmi5588_intr   | rmINtConnect   | All RM         |                |
|                |                | interrupts     |                |
|                |                | pass through   |                |
|                |                | this routine,  |                |
|                |                | which reads    |                |
|                |                | the RM source  |                |
|                |                | ID and calls   |                |
|                |                | the connected  |                |
|                |                | routine for    |                |
|                |                | this channel.  |                |
|                |                |                |                |
|                |                | .              |                |
+----------------+----------------+----------------+----------------+
| rmIntConnect   | EPICS dbior()  | Connect a C    | OK, or         |
|                |                | routine up to  | S_dev_???      |
|                |                | an RM          | errors.        |
|                |                | Interrupt.     |                |
|                |                |                |                |
|                |                | -  register an |                |
|                |                |                |                |
|                |                |      interrupt |                |
|                |                |       routine  |                |
|                |                |       with the |                |
|                |                |       RM       |                |
|                |                |       driver   |                |
|                |                |       layer    |                |
|                |                |                |                |
|                |                | -  initialises |                |
|                |                |       the BIM  |                |
|                |                |                |                |
|                |                |      registers |                |
|                |                |       on the   |                |
|                |                |       5588     |                |
|                |                |       card to  |                |
|                |                |       enable   |                |
|                |                |                |                |
|                |                |     interrupts |                |
|                |                |       for the  |                |
|                |                |       given RM |                |
|                |                |                |                |
|                |                |      interrupt |                |
|                |                |       channel  |                |
|                |                |       number.  |                |
+----------------+----------------+----------------+----------------+
| r              | IOC Shell at   | -  disables RM | N/A            |
| mIntDisconnect | startup        |                |                |
|                |                |     interrupts |                |
|                |                |       on the   |                |
|                |                |       given    |                |
|                |                |       channel  |                |
|                |                |       number   |                |
|                |                |                |                |
|                |                | -  marks the   |                |
|                |                |       channel  |                |
|                |                |       as       |                |
|                |                |       unused.  |                |
+----------------+----------------+----------------+----------------+
| rmIntSend      |                | causes an      | OK, or         |
|                |                | interrupt to   | S_dev_???      |
|                |                | be sent out on |                |
|                |                | the RM bus,    |                |
|                |                | using the      |                |
|                |                | given channel  |                |
|                |                | number.        |                |
+----------------+----------------+----------------+----------------+
| rmNodeId       |                | Used to find   | vmi5588 Node   |
|                |                | the value of   | number (in the |
|                |                | the RM node ID | range 0 to     |
|                |                | setting on the | 255), or       |
|                |                | VMIC5588 card. | S_dev_NoInit.  |
+----------------+----------------+----------------+----------------+
| rmStatus       | devBiVmi5588.c | This routine   | Status from    |
|                |                | reads various  | the VMIC558    |
|                |                | status bits    | card (a value  |
|                |                | from the 5588  | in the range 0 |
|                |                | card and       | to 0xffff), or |
|                |                | returns a      |                |
|                |                | composite of   | S_dev_NoInit.  |
|                |                | the            |                |
|                |                | interesting    |                |
|                |                | bits.          |                |
+----------------+----------------+----------------+----------------+
| v              | Called by an   | causes records |                |
| mi5588_pageISR | incoming I/O   | in modified    |                |
|                | Interrupt on   | pages which    |                |
|                | channel 1      | are set to I/O |                |
|                |                | Interrupt      |                |
|                |                | scanning to be |                |
|                |                | processed.     |                |
+----------------+----------------+----------------+----------------+
| vm             |                | -  Initialises | OK, or         |
| i5588_pageInit |                |       the page | S_dev_??? if   |
|                |                |                | can't connect  |
|                |                |      structure | interrupt      |
|                |                |       used for |                |
|                |                |       incoming |                |
|                |                |       I/O      |                |
|                |                |                |                |
|                |                |    Interrupts. |                |
|                |                |                |                |
|                |                | -  No action   |                |
|                |                |       if page  |                |
|                |                |       has      |                |
|                |                |       already  |                |
|                |                |       been     |                |
|                |                |                |                |
|                |                |   initialised. |                |
+----------------+----------------+----------------+----------------+
| v              |                | Allocates      |                |
| mi5588_pvtInit |                | storage for    |                |
|                |                | and            |                |
|                |                | initialises    |                |
|                |                | private record |                |
|                |                | data           |                |
+----------------+----------------+----------------+----------------+
| vmi558         |                | Returns the    | OK, or         |
| 8_getIoscanpvt |                | IOSCANPVT      | S_dev_internal |
|                |                | token for a    | if no entry    |
|                |                | record given   | found          |
|                |                | its dpvt       |                |
|                |                | structure.     |                |
+----------------+----------------+----------------+----------------+
| v              |                | Sends a        | OK, or         |
| mi5588_trigger |                | broadcast      | S_dev_???      |
|                |                | interrupt to   |                |
|                |                | trigger I/O    |                |
|                |                | interrupt      |                |
|                |                | processing for |                |
|                |                | all RM pages   |                |
|                |                | on given page  |                |
|                |                | number.        |                |
+----------------+----------------+----------------+----------------+
| rmPageMemBase  |                | Function to    |                |
|                |                | tell us where  |                |
|                |                | the Reflective |                |
|                |                | memory is      |                |
|                |                | located        |                |
+----------------+----------------+----------------+----------------+

2.19.3.1 Reflected Memory Status Bits
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The rmStatus routine plays a primary role in the device and driver
support for the Vmi5588, capturing the state of the board registers and
populating individual EPICS Binary Input records with the result every
second. This is all passive scanning.

The rmStatus routine reads various status bits from the 5588 card and
returns a composite of the interesting bits. The input parameter is a
bit mask which is used to reset those status bits which are writable --
if rmStatus is called with the parameters RM_RESYNC, RM_BADXFER or
RM_NORING then the relevant status bit or bits will be cleared if they
are currently set. To clear all status bits, use a parameter value of
0xffff. The returned status value is not affected by the input
parameter.

The RM_NORING bit functions slightly differently to the others. Whenever
rmStatus is called with the the bit RM_NORING set the current value of
the status bit is returned and the bit cleared as normal, but the test
location in the reflected memory is also written which results in a data
packet being transmitted around the ring. Any calls to rmStatus which
occur before this packet (or any other packets which originated at this
node) has been received will return RM_NORING, but the ring transition
time should only be a few microseconds at worst.

| RETURNS:
| Status from the VMIC5588 card (a value in the range 0 to 0xffff), or
  S_dev_NoInit.
| The following symbols are defined in the header file vmi5588.h and
  provide a bit mask for the return state. The bit will be set if the
  condition described below is true, so zero means Good status.
| RM_IRQ1 Interrupt 1 Pending
| RM_IRQ2 Interrupt 2 Pending
| RM_IRQ3 Interrupt 3 Pending
| RM_NOSIG No Input Signal
| RM_NOSYNC Input PLL unsynchronized
| RM_RESYNC PLL Recently unsynchronized
| RM_NORING Fibre Ring broken
| RM_BADXFR Single Transfer Error
| RM_TXHALF Transmit FIFO Half-full
| RM_RXHALF Receive FIFO Half-full
| EXAMPLE:
| -> rmStatus 0x40
| value = 64 = 0x44 = '@'
| -> rmStatus
| value = 4 = 0x4

.. _specific-requirements-14:

2.19.4 Specific Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2.19.4.1 REQ-119-01 vmi5588_init
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The vmi-5588 support library shall be capable of executing the
vmi5588_init() routine without error by calling iocInit().

2.19.4.2 REQ-119-02 Vmi5588 EPICS Report
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The EPICS routine dbior drvVmi5588 <interest_level>) shall display the
state of all data channels respective for that card. See vmi5588_report.

2.19.4.3 REQ-119-03 Binary Input Device Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The vmi5588 support library shall be capable of initializing and
processing an EPICS binary input record with its DTYP field set to
‘VMI5588 Synchro Bus’.

2.19.4.4 REQ-119-04 Reflected Memory Status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The vmi5588 support library shall be capable of populating all “RM
Status” binary input records defined in the Reflected Memory Status Bits
section above.

2.19.4.5 REQ-119-05 VME Node to PCI (MSDOS) Node Data Integrity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The vmi5588 support library shall be capable of executing a Ping-Pong
test between a VME node and a PCI node running MSDOS (like the M2TS
CEM).

PING PONG DETAILS...

2.19.4.6 REQ-119-06 VME Node to VME Node Data Integrity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The vmi5588 support library shall be capable of executing a Ping-Pong
test between two VME systems.

PING PONG DETAILS...

.. _section-2:

3 Test Plan
===========

The following test plans depend on a functioning ADE system capable of
building binaries for both the MVME2700 (RTEMS-mvme2307) and MVME6100
(RTEMS-beatnik) bsp’s. The following configurations are required for
each board.

3.1 Linux Host Setup
--------------------

1. Open a Linux terminal and configure the EPICS Channel Access to
      communicate with your IOC.

..

   For HBF:

+----------------------------------------+
| $ export EPICS_CA_ADDR_LIST 10.1.2.171 |
+----------------------------------------+

..

   For SBF:

+---------------------------------+
| $ export EPICS_CA_ADDR_LIST 172 |
|                                 |
| .16.2.23                        |
+---------------------------------+

3.2 CPU Board Setup
-------------------

+--------------------------+------------------------------------------+
| Mvme2700 Setup Procedure | 1. Register your test IOC with the ADE   |
|                          |       Redirector. For this example we’ll |
|                          |       use the gemtest1-MK as our test    |
|                          |       IOC. Make sure your IOC has a      |
|                          |       registered IP address. For this    |
|                          |       setup we’ll use gemtest1-MK with   |
|                          |       an IP address of 10.1.2.174. The   |
|                          |       tftp server is hbfrtdev-lv1 and    |
|                          |       has an IP address of 10.1.71.11.   |
|                          |                                          |
|                          | +------------------------------------+   |
|                          | | $ configure-ioc -t work -a         |   |
|                          | | RTEMS-mvme2307 gemtest1-MK         |   |
|                          | +------------------------------------+   |
|                          |                                          |
|                          | 2. Setup your mvme2700 with the          |
|                          |       following board level              |
|                          |       configurations                     |
|                          |                                          |
|                          | +------------------------------------+   |
|                          | | PPC1-Bug>niot                      |   |
|                          | |                                    |   |
|                          | | Controller LUN =00?                |   |
|                          | |                                    |   |
|                          | | Device LUN =00?                    |   |
|                          | |                                    |   |
|                          | | Node Control Memory Address        |   |
|                          | | =07F9E000?                         |   |
|                          | |                                    |   |
|                          | | Client IP Address =10.1.2.174?     |   |
|                          | |                                    |   |
|                          | | Server IP Address =10.1.71.11?     |   |
|                          | |                                    |   |
|                          | | Subnet IP Address Mask             |   |
|                          | | =255.255.255.0?                    |   |
|                          | |                                    |   |
|                          | | Broadcast IP Address =10.1.2.255?  |   |
|                          | |                                    |   |
|                          | | Gateway IP Address =10.1.2.1?      |   |
|                          | |                                    |   |
|                          | | Boot File Name ("NULL" for None)   |   |
|                          | | =/g                                |   |
|                          | | em_sw/prod/redirector/gemtest1-MK? |   |
|                          | |                                    |   |
|                          | | Argument File Name ("NULL" for     |   |
|                          | | None)                              |   |
|                          | | =10.1.71.11:/gem_s                 |   |
|                          | | w:prod/redirector/gemtest1-MK.cmd? |   |
|                          | |                                    |   |
|                          | | Boot File Load Address =001F0000?  |   |
|                          | |                                    |   |
|                          | | Boot File Execution Address        |   |
|                          | | =001F0000?                         |   |
|                          | |                                    |   |
|                          | | Boot File Execution Delay          |   |
|                          | | =00000000?                         |   |
|                          | |                                    |   |
|                          | | Boot File Length =00000000?        |   |
|                          | |                                    |   |
|                          | | Boot File Byte Offset =00000000?   |   |
|                          | |                                    |   |
|                          | | BOOTP/RARP Request Retry =05?      |   |
|                          | |                                    |   |
|                          | | TFTP/ARP Request Retry =05?        |   |
|                          | |                                    |   |
|                          | | Trace Character Buffer Address     |   |
|                          | | =00000000?                         |   |
|                          | |                                    |   |
|                          | | BOOTP/RARP Request Control:        |   |
|                          | | Always/When-Needed (A/W)=W?        |   |
|                          | |                                    |   |
|                          | | BOOTP/RARP Reply Update Control:   |   |
|                          | | Yes/No (Y/N) =Y?                   |   |
|                          | |                                    |   |
|                          | | Update Non-Volatile RAM (Y/N)? Y   |   |
|                          | +------------------------------------+   |
+--------------------------+------------------------------------------+

+---------------------------+-----------------------------------------+
| Mvme-6100 Setup Procedure | 1. Register your ioc application        |
|                           |                                         |
|                           | +-----------------------------------+   |
|                           | | $ configure-ioc -t work -a        |   |
|                           | | RTEMS-beatnik gemtest1-MK         |   |
|                           | +-----------------------------------+   |
|                           |                                         |
|                           | 2. Setup your mvme6100 with the         |
|                           |       following board level             |
|                           |       configurations                    |
|                           |                                         |
|                           | ..                                      |
|                           |                                         |
|                           |    TBD...                               |
|                           |                                         |
|                           | +-----------------------------------+   |
|                           | | MVME6100> gevEdit                 |   |
|                           | | mot-/dev/enet0-file               |   |
|                           | | mot-/dev                          |   |
|                           | | /enet0-file=ioc/labvme4/test.boot |   |
|                           | |                                   |   |
|                           | | (Blank line terminates input.)    |   |
|                           | |                                   |   |
|                           | | ioc/labvme4/PGTEST-CP-IOC.boot    |   |
|                           | |                                   |   |
|                           | | Update Global Environment Area of |   |
|                           | | NVRAM (Y/N)? y                    |   |
|                           | |                                   |   |
|                           | | MVME6100> gevEdit epics-nfsmount  |   |
|                           | |                                   |   |
|                           | | epics-nfsm                        |   |
|                           | | ount=172.16.71.11:/gemini:/gemini |   |
|                           | |                                   |   |
|                           | | (Blank line terminates input.)    |   |
|                           | |                                   |   |
|                           | | 172.16.71.11:/home:/home          |   |
|                           | |                                   |   |
|                           | | Update Global Environment Area of |   |
|                           | | NVRAM (Y/N)? y                    |   |
|                           | |                                   |   |
|                           | | MVME6100> gevEdit epics-script    |   |
|                           | |                                   |   |
|                           | | epics-script                      |   |
|                           | | =/gemini/ioc/labvme4/epics/st.cmd |   |
|                           | |                                   |   |
|                           | | (Blank line terminates input.)    |   |
|                           | |                                   |   |
|                           | | /gem_sw/work                      |   |
|                           | |                                   |   |
|                           | | Update Global Environment Area of |   |
|                           | | NVRAM (Y/N)? y                    |   |
|                           | +-----------------------------------+   |
+---------------------------+-----------------------------------------+

3.3 IOC-Stats
-------------

This module tests the functional requirements of the EPICS iocStats
support library.

3.3.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are no peripheral hardware tests for this module. You only need to
prepare a CPU board as described in `3.2 CPU Board
Setup <#cpu-board-setup>`__.

3.3.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

The iocStats libraries include epics records which can be loaded during
startup. The modular framework of databases can be viewed in
iocStats/iocAdmin/Db/. The iocAdminRTEMS.substitutions will load the
following template files with a top-level macro for $(IOC) :

-  ioc.template Records showing stats about IOC CPU load, records,
      files, CA clients, etc.

-  iocGeneralTime.template Records showing stats about IOC GeneralTime
      providers

-  iocRTOS.template Records showing stats about IOC Free Memory Blocks

-  iocRTEMSOnly.template Records showing stats about RTEMS Workspace
      memory

-  iocsEnvVar.template Records showing stats about IOC Environment
      Variables

-  iocCluster.template. Records showing stats about IOC System Clusters

3.3.2.1 iocAdmin.substitutions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The iocAdminRTEMS.substitutions file generates the iocAdmin.db file at
build time.

+----------------------------------------------------------------------+
| #==========                                                          |
| ==================================================================== |
|                                                                      |
| #                                                                    |
|                                                                      |
| # Abs: IOC Administration Records for RTEMS IOCs                     |
|                                                                      |
| #                                                                    |
|                                                                      |
| # Name: iocAdminRTEMS.substitutions                                  |
|                                                                      |
| #                                                                    |
|                                                                      |
| #==========                                                          |
| ==================================================================== |
|                                                                      |
| #                                                                    |
|                                                                      |
| file ioc.template                                                    |
|                                                                      |
| {                                                                    |
|                                                                      |
| pattern { IOCNAME , TODFORMAT }                                      |
|                                                                      |
| { $(IOC) , "%m/%d/%Y %H:%M:%S" }                                     |
|                                                                      |
| }                                                                    |
|                                                                      |
| file iocGeneralTime.template                                         |
|                                                                      |
| {                                                                    |
|                                                                      |
| pattern { IOCNAME }                                                  |
|                                                                      |
| { $(IOC) }                                                           |
|                                                                      |
| }                                                                    |
|                                                                      |
| file iocRTOS.template                                                |
|                                                                      |
| {                                                                    |
|                                                                      |
| pattern { IOCNAME , SYS_MBUF_FLNK }                                  |
|                                                                      |
| { $(IOC) , $(IOC):CLUST_1_0_0}                                       |
|                                                                      |
| }                                                                    |
|                                                                      |
| file iocRTEMSOnly.template                                           |
|                                                                      |
| {                                                                    |
|                                                                      |
| pattern { IOCNAME }                                                  |
|                                                                      |
| { $(IOC) }                                                           |
|                                                                      |
| }                                                                    |
|                                                                      |
| file iocEnvVar.template                                              |
|                                                                      |
| {                                                                    |
|                                                                      |
| pattern { IOCNAME, ENVNAME , ENVVAR , ENVTYPE}                       |
|                                                                      |
| { $(IOC) , CA_ADDR_LIST , EPICS_CA_ADDR_LIST , epics }               |
|                                                                      |
| { $(IOC) , CA_CONN_TIME , EPICS_CA_CONN_TMO , epics }                |
|                                                                      |
| { $(IOC) , CA_AUTO_ADDR , EPICS_CA_AUTO_ADDR_LIST , epics }          |
|                                                                      |
| { $(IOC) , CA_RPTR_PORT , EPICS_CA_REPEATER_PORT , epics }           |
|                                                                      |
| { $(IOC) , CA_SRVR_PORT , EPICS_CA_SERVER_PORT , epics }             |
|                                                                      |
| { $(IOC) , CA_MAX_ARRAY , EPICS_CA_MAX_ARRAY_BYTES , epics }         |
|                                                                      |
| { $(IOC) , CA_SRCH_TIME , EPICS_CA_MAX_SEARCH_PERIOD , epics }       |
|                                                                      |
| { $(IOC) , CA_BEAC_TIME , EPICS_CA_BEACON_PERIOD , epics }           |
|                                                                      |
| { $(IOC) , TIMEZONE , EPICS_TIMEZONE , epics }                       |
|                                                                      |
| { $(IOC) , TS_NTP_INET , EPICS_TS_NTP_INET , epics }                 |
|                                                                      |
| { $(IOC) , IOC_LOG_PORT , EPICS_IOC_LOG_PORT , epics }               |
|                                                                      |
| { $(IOC) , IOC_LOG_INET , EPICS_IOC_LOG_INET , epics }               |
|                                                                      |
| }                                                                    |
|                                                                      |
| file iocCluster.template                                             |
|                                                                      |
| {                                                                    |
|                                                                      |
| # Pool Size                                                          |
|                                                                      |
| pattern { IOCNAME , P , S , TYPE , FLNK }                            |
|                                                                      |
| # System Pool                                                        |
|                                                                      |
| { $(IOC) , 1 , 0 , SYS , $(IOC):CLUST_1_1_0 }                        |
|                                                                      |
| { $(IOC) , 1 , 1 , SYS , "" }                                        |
|                                                                      |
| }                                                                    |
+----------------------------------------------------------------------+

3.3.2.2 Include the iocAdminRTEMS.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In your test IOC, include the iocAdminRTEMS database. Edit the
iocTestApp/Db/Makefile:

+------------------------+
| DB += iocAdminRTEMS.db |
+------------------------+

3.3.2.3 Include the devIocStats and iocAdmin libraries in your build
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In your testIOC, include the iocAdmin libraries. Edit the
iocTestApp/src/Makefile:

+---------------------------------+
| gemtest1-MK_DBD += iocAdmin.dbd |
|                                 |
| ...                             |
|                                 |
| gemtest1-MK_LIBS += devIocStats |
+---------------------------------+

3.3.2.4 Startup File
^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK                    |
|                                                                      |
| cd ${INSTALL}                                                        |
|                                                                      |
| #Set the Timezone to UTC                                             |
|                                                                      |
| zoneset("UTC0")                                                      |
|                                                                      |
| epicsEnvSet("ENGINEER","Your Name Goes Here")                        |
|                                                                      |
| epicsEnvSet("LOCATION","HBF")                                        |
|                                                                      |
| ## Register all support components                                   |
|                                                                      |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")                                |
|                                                                      |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)                      |
|                                                                      |
| dbLoadRecords("db/iocAdminRTEMS.db",                                 |
| "IOC=gemtest1-MK,TODFORMAT=%m/%d/%Y %H:%M:%S")                       |
|                                                                      |
| iocInit                                                              |
+----------------------------------------------------------------------+

3.3.2.5 Rebuild IOC
^^^^^^^^^^^^^^^^^^^

+--------------------------------------------------+
| Run ‘make distclean; make’ in ioc top directory. |
+--------------------------------------------------+

3.3.3 IOC Diagnostic Screens

For all iocStats requirements, you can use the MEDM screen below. Run
the gem-iocstats <ioc_target> diagnostic screen like this.

+-------------------------------+
| $ gem-iocstats.py gemtest1-MK |
+-------------------------------+

|ioc_stats.png|

.. _req-101-01-largest-free-memory-block-1:

3.3.3 REQ-101-01 Largest free memory block
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | value of the Largest free memory block is valid. |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure that the value is reported.               |
+------------------+--------------------------------------------------+

.. _req-101-02-cpu-usage-1:

3.3.4 REQ-101-02 CPU Usage
~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | value of the CPU Usage is valid.                 |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure that the value is reported.               |
+------------------+--------------------------------------------------+

.. _req-101-03-suspended-tasks-1:

3.3.5 REQ-101-03 Suspended Tasks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | value of the Suspended Tasks is valid.           |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure that the value is reported.               |
+------------------+--------------------------------------------------+

.. _req-101-04-file-descriptors-1:

3.3.6 REQ-101-04 File Descriptors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | value of the File Descriptors is valid.          |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure that the value is reported.               |
+------------------+--------------------------------------------------+

.. _req-101-05-ca-clients-1:

3.3.7 REQ-101-05 CA Clients
~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | number of CA Clients is valid.                   |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure that the value is reported.               |
+------------------+--------------------------------------------------+

.. _req-101-06-ca-connections-1:

3.3.8 REQ-101-06 CA Connections
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | value of the CA Connections.                     |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure that the value is reported.               |
+------------------+--------------------------------------------------+

.. _req-101-07-number-of-records-1:

3.3.9 REQ-101-07 Number of Records
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, check the      |
|                  | value of the Number of records.                  |
+------------------+--------------------------------------------------+
| Success Criteria | .Ensure that the value is reported.              |
+------------------+--------------------------------------------------+

3.3.10 REQ-101-09 Restart Control
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.3.1 Test Setup and                         |
|                  |                                                  |
|                  | Prerequisites <#test-setup-and-prerequisites>`__ |
|                  |                                                  |
|                  | 2. `3.3.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness>`__           |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__                 |
+==================+==================================================+
| Procedure        | Using the IOC Diagnostic screens, execute a      |
|                  | Reboot.                                          |
+------------------+--------------------------------------------------+
| Success Criteria | Ensure the Reboot is successful.                 |
+------------------+--------------------------------------------------+

3.4 Sequencer
-------------

This module tests the functional requirements for the EPICS Sequencer.

.. _test-setup-and-prerequisites-1:

3.4.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are no peripheral hardware tests for this module. You only need to
prepare a CPU board as described in `3.2 CPU Board
Setup <#cpu-board-setup>`__.

.. _section-3:

.. _epics-test-harness-1:

3.4.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

To test the requirements for the EPICS Sequencer, there are two tests.
The demo program verifies a simple state set can be processed.
Additionally, a legacy Gemini program will be tested. For the demo
program, load the demo.db and the demoLib into the IOC test bench.

3.4.2.1 Add the demo.db 
^^^^^^^^^^^^^^^^^^^^^^^

Edit the gemtest1App/Db/Makefile:

+---------------+
| DB += demo.db |
+---------------+

3.4.2.2 Add the demoLib
^^^^^^^^^^^^^^^^^^^^^^^

Edit the gemtest1App/Db/Makefile:

+-----------------------------+
| gemtest1-MK_DBD += demo.dbd |
|                             |
| #SNCSEQ                     |
|                             |
| gemtest1-MK_LIBS += demoLib |
|                             |
| gemtest1-MK_LIBS += seq pv  |
+-----------------------------+

3.4.2.3 Startup script
^^^^^^^^^^^^^^^^^^^^^^

Edit the startup script to load the records and start the demo program.
Note that this builds on the `iocStats Test
Harness <#epics-test-harness>`__.

+----------------------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK                    |
|                                                                      |
| cd ${INSTALL}                                                        |
|                                                                      |
| #Setup timezone and user info for iocsStats                          |
|                                                                      |
| zoneset("UTC0")                                                      |
|                                                                      |
| epicsEnvSet("ENGINEER","Matt Rippa")                                 |
|                                                                      |
| epicsEnvSet("LOCATION","HBF")                                        |
|                                                                      |
| ## Register all support components                                   |
|                                                                      |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")                                |
|                                                                      |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)                      |
|                                                                      |
| ## Load record instances                                             |
|                                                                      |
| dbLoadRecords("db/iocAdminRTEMS.db",                                 |
| "IOC=gemtest1-MK,TODFORMAT=%m/%d/%Y %H:%M:%S")                       |
|                                                                      |
| dbLoadRecords("db/demo.db", "PREFIX=snctest")                        |
|                                                                      |
| iocInit                                                              |
|                                                                      |
| ## Start any sequence programs                                       |
|                                                                      |
| seq(&demo, "prefix=snctest")                                         |
+----------------------------------------------------------------------+

3.4.3 Diagnostic Screens
~~~~~~~~~~~~~~~~~~~~~~~~

The sequencer demo can be run to see the live state of the demo program.

+------------------------------------------------------------------------+
| $/gem_sw/prod/R3.14.12.4/support/seq/2-2-5/bin/linux-x86_64/seqdemo.py |
+------------------------------------------------------------------------+

|SequencerDemo.png|

.. _req-102-01-sequencer-demo-program-1:

3.4.4 REQ-102-01 Sequencer Demo Program
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.4.1 Test Setup and                         |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-1>`__ |
|                  |                                                  |
|                  | 2. `3.4.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-1>`__         |
|                  |                                                  |
|                  | 3. `3.4.3 Diagnostic                             |
|                  |       Screens <#diagnostic-screens>`__           |
+==================+==================================================+
| Procedure        | 1. Run the IOC as shown in the prerequisites.    |
|                  |                                                  |
|                  | 2. Using the Diagnostic Screens check the demo   |
|                  |       program is running.                        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The demo program is running with the voltage  |
|                  |       ramping up and down. The light turns on    |
|                  |       and off as the voltage goes above and      |
|                  |       below the defined limits.                  |
+------------------+--------------------------------------------------+

.. _req-102-02-legacy-state-notation-code-1:

3.4.5 REQ-102-02 Legacy State Notation Code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.4.1 Test Setup and                         |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-1>`__ |
|                  |                                                  |
|                  | 2. `3.4.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-1>`__         |
|                  |                                                  |
|                  | 3. `3.4.3 Diagnostic                             |
|                  |       Screens <#diagnostic-screens>`__           |
+==================+==================================================+
| Procedure        | 1. For each legacy system, load and run the      |
|                  |       sequencer code.                            |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The legacy system code will function as       |
|                  |       before. Because this test relies on the    |
|                  |       upgraded systems, it must be executed      |
|                  |       incrementally. It may only be possible to  |
|                  |       verify this with summit testing.           |
+------------------+--------------------------------------------------+

3.5 Gemini Records
------------------

.. _test-setup-and-prerequisites-2:

3.5.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are no peripheral hardware tests for this module. You only need to
prepare a CPU board as described in `3.2 CPU Board
Setup <#cpu-board-setup>`__.

.. _epics-test-harness-2:

3.5.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

3.5.2.1 Add the testTop.db
^^^^^^^^^^^^^^^^^^^^^^^^^^

+------------------+
| DB += testTop.db |
+------------------+

3.5.2.2 Add the libraries
^^^^^^^^^^^^^^^^^^^^^^^^^

We need to include libraries for geminiRecords, tcslib and
testGeminiRecords in the build.

+---------------------------------------+
| gemtest1-MK_DBD += geminiRecords.dbd  |
|                                       |
| gemtest1-MK_DBD += tcslib.dbd         |
|                                       |
| gemtest1-MK_DBD += testGeminiRecs.dbd |
|                                       |
| ...                                   |
|                                       |
| gemtest1-MK_LIBS += testGeminiRecs    |
|                                       |
| gemtest1-MK_LIBS += tcslib            |
|                                       |
| gemtest1-MK_LIBS += geminiRecords     |
|                                       |
| gemtest1-MK_LIBS += slalib            |
+---------------------------------------+

.. _startup-script-1:

3.5.2.3 Startup script
^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------------------------+
| cd /gem_sw/work/R3.14.12.4/ioc/gemtest1/MK                           |
|                                                                      |
| zoneset("UTC0")                                                      |
|                                                                      |
| epicsEnvSet("ENGINEER","Matt Rippa")                                 |
|                                                                      |
| epicsEnvSet("LOCATION","HBF")                                        |
|                                                                      |
| ## Register all support components                                   |
|                                                                      |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")                                |
|                                                                      |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)                      |
|                                                                      |
| ## Load record instances                                             |
|                                                                      |
| dbLoadRecords("db/iocAdminRTEMS.db",                                 |
| "IOC=gemtest1-MK,TODFORMAT=%m/%d/%Y %H:%M:%S")                       |
|                                                                      |
| dbLoadRecords("db/testTop.db", "test=grt:")                          |
|                                                                      |
| iocInit                                                              |
|                                                                      |
| ## Start any sequence programs                                       |
|                                                                      |
| #seq(demo, "prefix=snctest")                                         |
+----------------------------------------------------------------------+

.. _diagnostic-screens-1:

3.5.3 Diagnostic Screens
~~~~~~~~~~~~~~~~~~~~~~~~

The Gemini Records Test screen can be run to test the functionality of
all records under the Gemini Records library.

Example script for work package:

+----------------------------------------------------------------------+
| $                                                                    |
| /gem_sw/                                                             |
| work/R3.14.12.4/support/geminiRec/bin/linux-x86_64/gemRecordsTest.sh |
+----------------------------------------------------------------------+

Example script for production package:

+----------------------------------------------------------------------+
| $                                                                    |
| /gem_sw/prod/R                                                       |
| 3.14.12.4/support/geminiRec/4-1-4/bin/linux-x86_64/gemRecordsTest.sh |
+----------------------------------------------------------------------+

|GeminiRecordsTest.png|

.. _req-103-01-gensubrecord-1:

3.5.4 REQ-103-01 gensubRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminals each configured |
|                  |       as described in `3.1 Linux Host            |
|                  |       Setup <#linux-host-setup>`__.              |
|                  |                                                  |
|                  | 2. `3.5.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-2>`__         |
|                  |                                                  |
|                  | 3. `3.5.3 Diagnostic                             |
|                  |       Screens <#diagnostic-screens-1>`__ for     |
|                  |       Gemini Test Records                        |
|                  |                                                  |
|                  | 4. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__ for iocStats    |
+==================+==================================================+
| Procedure        | 1. Boot the IOC , start the Linux terminals and  |
|                  |       the test screens as described in the       |
|                  |       prerequisites.                             |
|                  |                                                  |
|                  | 2. With the Gemini Records Test Libraries loaded |
|                  |       there will be roughly 1000+ channels       |
|                  |       connected. View and confirm this on the    |
|                  |       iocStats screen.                           |
|                  |                                                  |
|                  | 3. For the gensubRecord test there is an airmass |
|                  |       calculation record in the testObserve      |
|                  |       schematic. View the general record health  |
|                  |       with the dbpr command using no interest    |
|                  |       level argument.                            |
|                  |                                                  |
|                  | +------------------------------------------+     |
|                  | | 10.1.2.171> dbpr grt:observe:calcAirmass |     |
|                  | +------------------------------------------+     |
|                  |                                                  |
|                  | 4. Run the dbpr command with an interest level   |
|                  |       of 10 and check the results and the SNAM   |
|                  |       field. The SNAM field shows the subroutine |
|                  |       which executes when the record processes.  |
|                  |       The TIME field shows valid timestamp if    |
|                  |       the record has processed.                  |
|                  |                                                  |
|                  | +---------------------------------------------+  |
|                  | | 10.1.2.171> dbpr grt:observe:calcAirmass 10 |  |
|                  | +---------------------------------------------+  |
|                  |                                                  |
|                  | 5. In linux terminal 1, add a camonitor to the   |
|                  |       airMassNow record in the testObserve       |
|                  |       schematic. The airMassNow is updated every |
|                  |       1 second based on the zenith distance of   |
|                  |       the telescope (INPB of the calcAirmass     |
|                  |       gensub).                                   |
|                  |                                                  |
|                  | +--------------------------------------+         |
|                  | | 10.1.2.171> camonitor grt:airMassNow |         |
|                  | +--------------------------------------+         |
|                  |                                                  |
|                  | 6. You’ll see the airMassNow record update when  |
|                  |       the elevation has updated. In your         |
|                  |       terminal #2 update the elevation position. |
|                  |       Try successively updating the elevation to |
|                  |       confirm the gensub record processes at     |
|                  |       most 1 time per second.                    |
|                  |                                                  |
|                  | +-----------------------------------------+      |
|                  | | 10.1.2.171> caput grtMC:elCurrentPos 60 |      |
|                  | +-----------------------------------------+      |
+------------------+--------------------------------------------------+
| Success Criteria | 1. From procedure step 3, view and confirm the   |
|                  |       calcAirmass record is in good health. It’s |
|                  |       possible the record has not processed yet  |
|                  |       and the SEVR field will show INVALID.      |
|                  |                                                  |
|                  | 2. Check all the inputs, outputs and the SNAM    |
|                  |       field from step 4. The SEVR will say       |
|                  |       NO_ALARM.                                  |
|                  |                                                  |
|                  | 3. With linux terminal inputs verify the gensub  |
|                  |       record processes. You can do this by       |
|                  |       watching updates to the camonitor screen.  |
|                  |       As the elevation values change, the        |
|                  |       monitor on the airMassNow will update      |
|                  |       demonstrating the gensubRecord is          |
|                  |       functioning.                               |
+------------------+--------------------------------------------------+

.. _req-103-02-applyrecord-1:

3.5.5 REQ-103-02 applyRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminals each configured |
|                  |       as described in `3.1 Linux Host            |
|                  |       Setup <#linux-host-setup>`__.              |
|                  |                                                  |
|                  | 2. `3.5.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-2>`__         |
|                  |                                                  |
|                  | 3. `3.5.3 Diagnostic                             |
|                  |       Screens <#diagnostic-screens-1>`__ for     |
|                  |       Gemini Records Test Screen (GRTS)          |
|                  |                                                  |
|                  | 4. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__ for iocStats    |
|                  |                                                  |
|                  | 5. Xycom is a dependency...                      |
+==================+==================================================+
| Procedure        | 1. Client ID Field (CLID)                        |
|                  |                                                  |
|                  |    a. The CLID is incremented every time a       |
|                  |          directive is loaded. There are a series |
|                  |          of Apply records starting with the      |
|                  |          “Master” Apply record in the testApply  |
|                  |          schematic .The top-level “Master” apply |
|                  |          record can be controlled from the GRTS, |
|                  |          shown as an APPLY button. Put a         |
|                  |          camonitor on the CLID field of all      |
|                  |          apply records. Each time the “Master”   |
|                  |          Apply record is processed the directive |
|                  |          is load for all Apply and CAD records   |
|                  |          in the processing chain.                |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | $ camonitor grt:apply.CLID                 |   |
|                  | | grt:seq:apply.CLID grt:seq:apply1.CLID     |   |
|                  | | grt:seq:apply2.CLID grt:seq:apply3.CLID    |   |
|                  | | grt:seq:apply4.CLID                        |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | b. Process the Apply sequence by MARKing any one |
|                  |       command on the GRTS, then click the Apply  |
|                  |       button.                                    |
|                  |                                                  |
|                  | c. Mark any number of commands on the GRTS, then |
|                  |       click on the CLEAR button.                 |
|                  |                                                  |
|                  | 2. Message Field (MESS) (**Requires XYCOM        |
|                  |       currently**)                               |
|                  |                                                  |
|                  |    a. This is the return message from processing |
|                  |          an Apply directive. If the return is 0, |
|                  |          this field is empty. Otherwise, it      |
|                  |          reads the error message. Put a monitor  |
|                  |          on the MESS field of all apply records. |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | $ camonitor grt:apply.MESS                 |   |
|                  | | grt:seq:apply.MESS grt:seq:apply1.MESS     |   |
|                  | | grt:seq:apply2.MESS grt:seq:apply3.MESS    |   |
|                  | | grt:seq:apply4.MESS                        |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | b. Set the Xycom interlock to the INTERLOCKED    |
|                  |       state by setting the Xycom 240 inputs. For |
|                  |       this you need the Xycom 240 installed.     |
|                  |       (See `Xycom 240                            |
|                  |                                                  |
|                  |    Setup <#test-setup-and-prerequisites-10>`__). |
|                  |       Connect all Xycom output data ports        |
|                  |       4,5,6,7 on JK/2 to all input data ports    |
|                  |       0,1,2,3 on JK/1. Use a 50-pin breakout     |
|                  |       with ribbon cables to make it easier, or   |
|                  |       just the ribbon cable.                     |
|                  |                                                  |
|                  | ..                                               |
|                  |                                                  |
|                  |    NB. By set the output bits on port 4, you     |
|                  |    drive the input on port 0 (i.e., the state of |
|                  |    S24 and S25 determine the interlock. You need |
|                  |    1 for INTERLOCK and 2 for CLEAR) .            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | ioc> xy240_dio_out(0,4,1) |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | c. With the interlock set, the first CAD         |
|                  |       subroutine in the chain will be REJECTED   |
|                  |       and this halts all further processing.     |
|                  |       Process the Apply sequence by MARKing any  |
|                  |       one command on the GRTS. Then click the    |
|                  |       Apply button.                              |
|                  |                                                  |
|                  | d. With the interlock in a CLEAR state, the      |
|                  |       processing is not REJECTED. Clear the      |
|                  |       interlock by writing a 2 back to the Xycom |
|                  |       S24, S25.                                  |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | ioc> xy240_dio_out(0,4,2) |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | e. Process the Apply sequence by MARKing any one |
|                  |       command on the GRTS, then click the Apply  |
|                  |       button.                                    |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Step 1.a should show the CLID for all apply   |
|                  |       records. They should all be the same       |
|                  |       integer.                                   |
|                  |                                                  |
|                  | 2. Step 1b. should show all CLID’s increment by  |
|                  |       1. Some apply records may processes        |
|                  |       multiple times, but the CLID should        |
|                  |       increment by 1 and only 1 for all records. |
|                  |                                                  |
|                  | 3. Step 1c. should clear the MARK bit on all     |
|                  |       commands. The apply records will all       |
|                  |       process, yet the CLID’s will NOT           |
|                  |       increment.                                 |
|                  |                                                  |
|                  | 4. Step 2a. should show no message returned in   |
|                  |       any apply records when the interlock is in |
|                  |       a CLEAR state.                             |
|                  |                                                  |
|                  | 5. After step 2b. and 2c., the message **“GIS    |
|                  |       Interlock present”** should be printed on  |
|                  |       the camonitor terminal for the top level   |
|                  |       appy record.                               |
|                  |                                                  |
|                  | 6. After clearing the interlock and issuing the  |
|                  |       apply on any number of marked command,     |
|                  |       there should be no messages returned and   |
|                  |       all commands should process.               |
+------------------+--------------------------------------------------+

.. _req-103-03-cadrecord-1:

3.5.6 REQ-103-03 cadRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminals each configured |
|                  |       as described in `3.1 Linux Host            |
|                  |       Setup <#linux-host-setup>`__.              |
|                  |                                                  |
|                  | 2. `3.5.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-2>`__         |
|                  |                                                  |
|                  | 3. `3.5.3 Diagnostic                             |
|                  |       Screens <#diagnostic-screens-1>`__ for     |
|                  |       Gemini Records Test Screen (GRTS)          |
|                  |                                                  |
|                  | 4. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__ for iocStats    |
+==================+==================================================+
| Procedure        | 1. The Apply record in the previous section is   |
|                  |       really brokering the processing of a       |
|                  |       sequence CAD records. That’s a sequence of |
|                  |       Command Action Directives. A CAD record    |
|                  |       will process its command with a subroutine |
|                  |       (defined in the SNAM field) and possibly   |
|                  |       reject the command if error conditions are |
|                  |       encountered. For example, the              |
|                  |       testConfigBegin CAD record is the first    |
|                  |       record in the testSequenceCommands         |
|                  |       schematic. If the interlock (discussed in  |
|                  |       the previous section ) is in the INTERLOCK |
|                  |       state, the testConfigBegin() is rejected   |
|                  |       and the corresponding MESS is returned to  |
|                  |       the Apply record. Process any command in   |
|                  |       the GRTS to see the states printed in the  |
|                  |       ioc terminal.                              |
+------------------+--------------------------------------------------+
| Success Criteria | 1. By using the GRTS to process commands and     |
|                  |       using the interlock to invalidate the      |
|                  |       processing, you should see the respective  |
|                  |       messages on the IOC console. This verifies |
|                  |       the CAD subroutine is working and printing |
|                  |       each directive.                            |
+------------------+--------------------------------------------------+

.. _req-103-04-carrecord-1:

3.5.7 REQ-103-04 carRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminals each configured |
|                  |       as described in `3.1 Linux Host            |
|                  |       Setup <#linux-host-setup>`__.              |
|                  |                                                  |
|                  | 2. `3.5.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-2>`__         |
|                  |                                                  |
|                  | 3. `3.5.3 Diagnostic                             |
|                  |       Screens <#diagnostic-screens-1>`__ for     |
|                  |       Gemini Records Test Screen (GRTS)          |
|                  |                                                  |
|                  | 4. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__ for iocStats    |
|                  |                                                  |
|                  | 5. Xycom-240 set for no interlocking: in IOC     |
|                  |       console, type ‘xy240_dio_out(0,4,2)’       |
+==================+==================================================+
| Procedure        | 1. Mark one of the commands in the OBSERVE       |
|                  |       section on the GRTS, then press the APPLY  |
|                  |       button.                                    |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the state of OBSERVE on the GRTS  |
|                  |       changes from IDLE to BUSY, then back to    |
|                  |       IDLE.                                      |
+------------------+--------------------------------------------------+

.. _req-103-05-sirrecord-1:

3.5.8 REQ-103-05 sirRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminals each configured |
|                  |       as described in `3.1 Linux Host            |
|                  |       Setup <#linux-host-setup>`__.              |
|                  |                                                  |
|                  | 2. `3.5.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-2>`__         |
|                  |                                                  |
|                  | 3. `3.3.3 IOC Diagnostic                         |
|                  |       Screens <#_rfm0gwxetqz>`__ for iocStats    |
|                  |                                                  |
|                  | 4. Xycom-240 set for no interlocking: in IOC     |
|                  |       console, type ‘xy240_dio_out(0,4,2)’       |
+==================+==================================================+
| Procedure        | 1. In first Linux host terminal, run ‘camonitor  |
|                  |       grt:SAD:interlockSadState                  |
|                  |       grt:SAD:interlockSadState.OMSS’            |
|                  |                                                  |
|                  | 2. In second Linux host terminal run ‘caput      |
|                  |       grt:SAD:interlockSadState.IMSS “test       |
|                  |       message” ’                                 |
|                  |                                                  |
|                  | 3. In IOC console, type ‘xy240_dio_out(0,4,1)’   |
+------------------+--------------------------------------------------+
| Success Criteria |                                                  |
+------------------+--------------------------------------------------+

.. _req-103-06-cmdtimeoutrecord-1:

3.5.9 REQ-103-06 cmdTimeoutRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This record is not described in the Gemini Records Reference Manual, and
no examples exist in the test harness database. Is it used anywhere?

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _req-103-07-loadrecord-1:

3.5.10 REQ-103-07 loadRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Note: This record is intended to used to load programs into PMAC cards,
but is not actually used by Gemini.

We will not test this record at this time.

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _req-103-08-lutinrecord-1:

3.5.11 REQ-103-08 lutinRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _req-103-09-lutoutrecord-1:

3.5.12 REQ-103-09 lutoutRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _req-103-10-statusrecord-1:

3.5.13 REQ-103-10 statusRecord
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _pvload-1:

3.6 Pvload
----------

.. _test-setup-and-prerequisites-3:

3.6.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are no peripheral hardware tests for this module. You only need to
prepare a CPU board as described in `3.2 CPU Board
Setup <#cpu-board-setup>`__.

.. _epics-test-harness-3:

3.6.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

The pvload library does not have a native test harness. However, all
IOC’s will contain an identity record in the <ioc>App/Db directory which
is to be maintained with the corresponding <ioc>App/config/identity.pv
file. Below you can see how the record is defined with substitutions and
template files.

+-------------------------------------------------+
| [gemtest1-MKApp/Db]> cat identity.substitutions |
|                                                 |
| file identity.template                          |
|                                                 |
| {                                               |
|                                                 |
| pattern { IOCNAME }                             |
|                                                 |
| { $(IOC) }                                      |
|                                                 |
| }                                               |
+-------------------------------------------------+

+--------------------------------------------+
| [gemtest1-MKApp/Db]> cat identity.template |
|                                            |
| record(stringin, "$(IOCNAME):identity") {  |
|                                            |
| field(DESC, "Version of this IOC")         |
|                                            |
| field(DTYP, "Soft Channel")                |
|                                            |
| field(PINI, "YES")                         |
|                                            |
| }                                          |
+--------------------------------------------+

3.6.2.1 Add the identity.db to gemtest1-MKApp/Db/Makefile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------+
| DEPSCHS = $(wildcard ../*.sch) |
|                                |
| ...                            |
|                                |
| DB += identity.db              |
+--------------------------------+

3.6.2.2 Add the library to gemtest1-MKApp/src/Makefile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------+
| gemtest1-MK_DBD += pvload.dbd |
|                               |
| ...                           |
|                               |
| gemtest1-MK_LIBS += pvload    |
+-------------------------------+

.. _startup-script-2:

3.6.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK  |
|                                                    |
| cd ${INSTALL}                                      |
|                                                    |
| zoneset("UTC0")                                    |
|                                                    |
| epicsEnvSet("ENGINEER","<YOUR NAME>")              |
|                                                    |
| epicsEnvSet("LOCATION","HBF")                      |
|                                                    |
| ## Register all support components                 |
|                                                    |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")              |
|                                                    |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)    |
|                                                    |
| dbLoadRecords("db/identity.db", "IOC=gemtest1-MK") |
|                                                    |
| iocInit                                            |
|                                                    |
| pvload "./data/identity.pv", "IOC=gemtest1-MK:"    |
+----------------------------------------------------+

.. _req-104-01-identity-record-1:

3.6.3 REQ-104-01 Identity Record
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.6.1 Test Setup and                         |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-3>`__ |
|                  |                                                  |
|                  | 2. `3.6.2 EPICS Test                             |
|                  |       Harness <#epics-test-harness-3>`__         |
+==================+==================================================+
| Procedure        | 1. Boot the test IOC and load the identity PV    |
|                  |       file as described in the prerequisites.    |
|                  |                                                  |
|                  | 2. Print out the identity record attributes with |
|                  |       dbpr.                                      |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC boots without error and loads the     |
|                  |       identity.pv file                           |
|                  |                                                  |
|                  | 2. Confirm the value defined in the identity.pv  |
|                  |       file agrees with the VAL field of the      |
|                  |       record shown below in red.                 |
|                  |                                                  |
|                  | +---------------------------------------------+  |
|                  | | 10.1.2.171> dbpr gemtest1-MK:identity       |  |
|                  | |                                             |  |
|                  | | ASG: DESC: Version of this IOC DISA: 0      |  |
|                  | |                                             |  |
|                  | | DISP: 0 DISV: 1 NAME: gemtest1-MK:identity  |  |
|                  | |                                             |  |
|                  | | SEVR: NO_ALARM STAT: NO_ALARM SVAL: TPRO: 0 |  |
|                  | |                                             |  |
|                  | | VAL: 1-1                                    |  |
|                  | +---------------------------------------------+  |
+------------------+--------------------------------------------------+

.. _req-104-02-legacy-pvload-files-1:

3.6.4 REQ-104-02 Legacy pvload files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The requirement to support legacy pvload files must be tested
incrementally for each telescope system.

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Prepare all hardware and boot IOC including   |
|                  |       legacy pv files.                           |
+==================+==================================================+
| Procedure        | 1. Boot the test IOC and load the legacy PV      |
|                  |       file.                                      |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The legacy pvload file successfully populates |
|                  |       all records.                               |
+------------------+--------------------------------------------------+

.. _bancomm-1:

3.7 Bancomm
-----------

.. _test-setup-and-prerequisites-4:

3.7.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Setup your test Bancomm 635 or 637 with the proper jumper and switch
setting to define the base address. The base address for Gemini boards
will be 0x4000 . Base address selection for the VMEbus requires the
setting of switch SW1 (A6 through A13) and SW2 (A14 and A15). So, for
the 0x4000 we want 0100 0000 00 for the A15 through A6 respectively.

================ ========= =============== ===============
Name             Hex value Switch SW1/pins Swithc SW2/pins
================ ========= =============== ===============
Base Address     0x4000    01              0000 0000
Supervisory Mode ???                       
Interrupt Level                            
================ ========= =============== ===============

.. _epics-test-harness-4:

3.7.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

The bancomm driver is equipped with a set of EPICS records which can be
loaded during startup and used for testing. The database can be viewed
in bancommApp/Db/bctest.sch. Open in tdct like this:

+----------------------------------------------------+
| [bancommApp/Db]>tdct -cfg ./tdct.cfg -i bctest.sch |
+----------------------------------------------------+

3.7.2.1 Add the bctest.db
^^^^^^^^^^^^^^^^^^^^^^^^^

+------------------------------------------------------+
| # Create and install (or just install) into <top>/db |
|                                                      |
| # databases, templates, substitutions like this      |
|                                                      |
| #TDCT_CFG = ../tdct.cfg                              |
|                                                      |
| DEPSCHS = $(wildcard ../*.sch)                       |
|                                                      |
| DB += bctest.db                                      |
|                                                      |
| DB += iocAdminRTEMS.db                               |
+------------------------------------------------------+

.. _add-the-libraries-1:

3.7.2.2 Add the libraries
^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------------------------+
| ###DBD_FILE########Bancomm-Section############   |
|                                                  |
| gemtest1-MK_DBD += bancomm.dbd                   |
|                                                  |
| ##############################################   |
|                                                  |
| ###DBD_FILE#######IOCSTATS-Section############   |
|                                                  |
| gemtest1-MK_DBD += iocAdmin.dbd                  |
|                                                  |
| ##############################################   |
|                                                  |
| ###LIB##########BANCOMM-Section################# |
|                                                  |
| gemtest1-MK_LIBS += bancomm                      |
|                                                  |
| ##############################################   |
|                                                  |
| ###LIB#######IOCSTATS-Section#################   |
|                                                  |
| gemtest1-MK_LIBS += devIocStats                  |
|                                                  |
| ##############################################   |
+--------------------------------------------------+

.. _startup-script-3:

3.7.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK                    |
|                                                                      |
| cd ${INSTALL}                                                        |
|                                                                      |
| zoneset("UTC0")                                                      |
|                                                                      |
| epicsEnvSet("ENGINEER","<YOUR NAME>")                                |
|                                                                      |
| epicsEnvSet("LOCATION","HBF")                                        |
|                                                                      |
| ## Register all support components                                   |
|                                                                      |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)                      |
|                                                                      |
| # Configure the time system                                          |
|                                                                      |
| # Master ? Simulate ? intPerSecond, intPerTick, timeOffset           |
| (microsec)                                                           |
|                                                                      |
| BCconfigure (0, 1, 80, 1, 0)                                         |
|                                                                      |
| ## Load record instances                                             |
|                                                                      |
| dbLoadRecords("db/bctest.db", “ top=gemtest1-MK”)                    |
|                                                                      |
| dbLoadRecords("db/iocAdminRTEMS.db",                                 |
| "IOC=gemtest1-MK,TODFORMAT=%m/%d/%Y %H:%M:%S")                       |
|                                                                      |
| iocInit                                                              |
+----------------------------------------------------------------------+

.. _req-105-01-initialize-1:

3.7.3 REQ-105-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are setup  |
|                  |       as described in\ `3.2 CPU Board            |
|                  |       Setup <#cpu-board-setup>`__.               |
|                  |                                                  |
|                  | 2. Ensure that your Bancomm board is setup as    |
|                  |       described in\ `3.7.1 Test Setup and        |
|                  |       Pre                                        |
|                  | requisites <#test-setup-and-prerequisites-4>`__. |
|                  |                                                  |
|                  | 3. Prepare your test IOC for the Bancomm as      |
|                  |       described in `3.7.2 EPICS Test             |
|                  |       Harness <#epics-test-harness-4>`__.        |
|                  |                                                  |
|                  | 4. Additionally, prepare your IOC to use the     |
|                  |       IOCstats libraries and as described in     |
|                  |       `3.3 IOC-Stats <#ioc-stats>`__.            |
+==================+==================================================+
| Procedure        | 1. Boot the IOC and confirm the startup script   |
|                  |       fully executes.                            |
|                  |                                                  |
|                  | 2. Run the ‘date’ command on the IOC.            |
|                  |                                                  |
|                  | 3. Run the ‘generalTimeReport 10’ command on the |
|                  |       IOC                                        |
|                  |                                                  |
|                  | 4. Run the iocstats diagnostic screen from a     |
|                  |       Linux terminal.                            |
|                  |                                                  |
|                  | +------------------------------------+           |
|                  | | linux$ gem-iocstats.py gemtest1-MK |           |
|                  | +------------------------------------+           |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The BCconfigure() will have initialized the   |
|                  |       Bancomm driver with no reported errors.    |
|                  |                                                  |
|                  | 3. The EPICS iocInit() will have run with no     |
|                  |       errors.                                    |
|                  |                                                  |
|                  | 4. The date command will show the correct        |
|                  |       current date and a time of day             |
|                  |       corresponding to the numbers on the front  |
|                  |       panel of the Bancomm board.                |
|                  |                                                  |
|                  | 5. The generalTimeReport will show all           |
|                  |       registered EPICS time providers, the first |
|                  |       of which shall be the Bancomm driver and   |
|                  |       will be the highest priority.              |
|                  |                                                  |
|                  | 6. The Bancomm driver will be reported as the    |
|                  |       current time provider for the IOC.         |
|                  |                                                  |
|                  | +---------------------------------------------+  |
|                  | | 10.1.2.171> generalTimeReport 10            |  |
|                  | |                                             |  |
|                  | | Backwards time errors prevented 410 times.  |  |
|                  | |                                             |  |
|                  | | Current Time Providers:                     |  |
|                  | |                                             |  |
|                  | | "bc635", priority = 20                      |  |
|                  | |                                             |  |
|                  | | Current Time is 2016-01-01 00:01:54.375971. |  |
|                  | |                                             |  |
|                  | | "NTP", priority = 100                       |  |
|                  | |                                             |  |
|                  | | Current Time is 2016-05-11 20:35:09.862574. |  |
|                  | |                                             |  |
|                  | | "OS Clock", priority = 999                  |  |
|                  | |                                             |  |
|                  | | Current Time is 2001-01-01 00:00:29.652200. |  |
|                  | |                                             |  |
|                  | | Event Time Providers:                       |  |
|                  | |                                             |  |
|                  | | No Providers registered.                    |  |
|                  | +---------------------------------------------+  |
|                  |                                                  |
|                  | 7. The iocStats diagnostic screen shall show the |
|                  |       same information as above and the          |
|                  |       heartbeat and all update fields will be    |
|                  |       green and active.                          |
+------------------+--------------------------------------------------+

.. _req-105-02-epics-report-1:

3.7.4 REQ-105-02 EPICS Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.7.3 REQ-105-01 Bancomm                     |
|                  |       Initialize <#req-105-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. This test is executed as part of the          |
|                  |       initialize test above.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. When the Bancomm board initializes it         |
|                  |       registers a report routine with EPICS. The |
|                  |       warning is currently acceptable.           |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvBc635 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvBc635                           |   |
|                  | |                                            |   |
|                  | | Warning: bspExtMemProbe kills real-time    |   |
|                  | | performance.                               |   |
|                  | |                                            |   |
|                  | | use only during driver initialization      |   |
|                  | |                                            |   |
|                  | | clear the 'bspExtVerbosity' variable to    |   |
|                  | | silence this warning                       |   |
|                  | |                                            |   |
|                  | | Bancomm 635 board at address 0x4000        |   |
|                  | |                                            |   |
|                  | | TFP is flywheeling (not locked)            |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.7.5 REQ-105-03 Analog Input Record (Scan I/O Inter Support)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open a Linux terminal as described in `3.1    |
|                  |       Linux Host Setup <#linux-host-setup>`__    |
|                  |                                                  |
|                  | 2. `3.7.3 REQ-105-01 Bancomm                     |
|                  |       Initialize <#req-105-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. After the IOC has boot successfully, verify   |
|                  |       the Interrupts are enabled by viewing the  |
|                  |       console messages.                          |
|                  |                                                  |
|                  | 2. In order to enable the TOD interrupt enter in |
|                  |       the linux terminal set the number of       |
|                  |       seconds in the day by writing to the       |
|                  |       timesecs record. The TOD record will       |
|                  |       process with your input and update the     |
|                  |       strobe registers as a result. The value    |
|                  |       should be to something between 0.0 and     |
|                  |       86399.999                                  |
|                  |                                                  |
|                  | +----------------------------------------+       |
|                  | | $ caput gemtest1-MK:timesecs 82360.251 |       |
|                  | +----------------------------------------+       |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The TOD interrupt Record will receive an      |
|                  |       interrupt when the defined number of       |
|                  |       second of the day happens.                 |
+------------------+--------------------------------------------------+

3.7.6 REQ-105-04 Analog Output Record
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open a Linux terminal as described in `3.1    |
|                  |       Linux Host Setup <#linux-host-setup>`__    |
|                  |                                                  |
|                  | 2. `3.7.3 REQ-105-01 Bancomm                     |
|                  |       Initialize <#req-105-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. After the IOC has boot successfully, verify   |
|                  |       the Interrupts are enabled by viewing the  |
|                  |       console messages.                          |
|                  |                                                  |
|                  | 2. In the linux terminal set the number of       |
|                  |       seconds in the day by writing to the       |
|                  |       timesecs record. The TOD record will       |
|                  |       process with your input and update the     |
|                  |       strobe registers as a result. The value    |
|                  |       should be to something between 0.0 and     |
|                  |       86399.999                                  |
|                  |                                                  |
|                  | +----------------------------------------+       |
|                  | | $ caput gemtest1-MK:timesecs 82360.251 |       |
|                  | +----------------------------------------+       |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Both external event interrupts and time of    |
|                  |       day interrupts are enabled (shown below in |
|                  |       red).                                      |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | …                                          |   |
|                  | |                                            |   |
|                  | | iocInit                                    |   |
|                  | |                                            |   |
|                  | | Starting iocInit                           |   |
|                  | |                                            |   |
|                  | | ##################################         |   |
|                  | | ########################################## |   |
|                  | |                                            |   |
|                  | | ## EPICS R3.14.12.4 $Date: Mon 2013-12-16  |   |
|                  | | 15:51:45 -0600$                            |   |
|                  | |                                            |   |
|                  | | ## EPICS Base built Oct 24 2014            |   |
|                  | |                                            |   |
|                  | | ##################################         |   |
|                  | | ########################################## |   |
|                  | |                                            |   |
|                  | | BC635 driver : external event interrupts   |   |
|                  | | enabled                                    |   |
|                  | |                                            |   |
|                  | | BC635 driver : time of day interrupts      |   |
|                  | | enabled                                    |   |
|                  | |                                            |   |
|                  | | iocRun: All initialization complete        |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 2. As a result of inserting a new time in the    |
|                  |       strobe registers, you can see the          |
|                  |       TODinterrupt                               |
+------------------+--------------------------------------------------+

3.7.7 REQ-105-05 String Input Record (Scan I/O Inter Support)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open a Linux terminal as described in `3.1    |
|                  |       Linux Host Setup <#linux-host-setup>`__    |
|                  |                                                  |
|                  | 2. `3.7.3 REQ-105-01 Bancomm                     |
|                  |       Initialize <#req-105-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. After the IOC has boot successfully, verify   |
|                  |       the Interrupts are enabled by viewing the  |
|                  |       console messages.                          |
|                  |                                                  |
|                  | 2. Change the SCAN I/O Intr to Passive because a |
|                  |       system exception happens using I/O         |
|                  |       interrupt..                                |
|                  |                                                  |
|                  | 3. Query the value in the string input EPICS     |
|                  |       channel rtd9timestamp to verify the time   |
|                  |       read from the Bancomm card.                |
|                  |                                                  |
|                  | +------------------------------------------+     |
|                  | | $ caget rtd9timestamp                    |     |
|                  | |                                          |     |
|                  | | rtd9timestamp 2016/09/08 15:46:54.020072 |     |
|                  | +------------------------------------------+     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the time matches with the time    |
|                  |       displayed in the Bancom card.              |
+------------------+--------------------------------------------------+

3.7.8 REQ-105-06 String Output Record
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open a Linux terminal as described in `3.1    |
|                  |       Linux Host Setup <#linux-host-setup>`__    |
|                  |                                                  |
|                  | 2. `3.7.3 REQ-105-01 Bancomm                     |
|                  |       Initialize <#req-105-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. This record is not used at Gemini             |
+------------------+--------------------------------------------------+
| Success Criteria | 1. N/A                                           |
+------------------+--------------------------------------------------+

.. _timelib-1:

3.8 Timelib
-----------

.. _test-setup-and-prerequisites-5:

3.8.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _epics-test-harness-5:

3.8.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

The Timelib driver does not need any EPICS record to be loaded at
startup. This test configures a Bancomm card calling the timeClockInit
function and uses the timeTest program for the timenow function call.

.. _add-the-libraries-2:

3.8.2.1 Add the libraries
^^^^^^^^^^^^^^^^^^^^^^^^^

+------------------------------------+
| labvme9-sbf-ioc_DBD = timelib.dbd  |
|                                    |
| …….                                |
|                                    |
| labvme9-sbf-ioc_LIBS = timelib     |
|                                    |
| labvme9-sbf-ioc_SRCS += testtime.c |
|                                    |
| DBD += testtime.dbd                |
+------------------------------------+

.. _startup-script-4:

3.8.2.2 Startup Script
^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme9-sbf-ioc                |
|                                                                      |
| cd ${INSTALL}                                                        |
|                                                                      |
| ## Register all support components                                   |
|                                                                      |
| dbLoadDatabase("dbd/labvme9-sbf-ioc.dbd")                            |
|                                                                      |
| dbLoadDatabase("dbd/testtime.dbd")                                   |
|                                                                      |
| labvme9_sbf_ioc_registerRecordDeviceDriver(pdbbase)                  |
|                                                                      |
| # Configure the time system                                          |
|                                                                      |
| timeClockInit (1, 0, 60, 1, 0)                                       |
|                                                                      |
| ## Load record instances                                             |
|                                                                      |
| dbLoadRecords("db/iocAdminRTEMS.db"," IOC=rtd9,TODFORMAT=%m/%d/%Y    |
| %H:%M:%S")                                                           |
|                                                                      |
| iocInit                                                              |
+----------------------------------------------------------------------+

.. _section-4:

.. _section-5:

.. _req-106-01-timenow-1:

3.8.3 REQ-106-01 timeNow
~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `Test Setup and                               |
|                  |       Prerequisites <#epics-test-harness-5>`__   |
|                  |                                                  |
|                  | 2. `EPICS Test                                   |
|                  |       harness <#epics-test-harness-5>`__         |
+==================+==================================================+
| Procedure        | 1. The IOC must show no errors at the startup    |
|                  |                                                  |
|                  | 2. Enter the help shell command and verify the   |
|                  |       testTimestamps command is available in the |
|                  |       list of shell commands. This confirms the  |
|                  |       command is successfully initialized and    |
|                  |       ready to be used.                          |
|                  |                                                  |
|                  | 3. Enter the command:                            |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | $ testTimestamps                           |   |
|                  | |                                            |   |
|                  | | Time now as double in testTimestamps() =   |   |
|                  | | 1473340592.470013                          |   |
|                  | |                                            |   |
|                  | | Time now as hmsf in testTimestamps() =     |   |
|                  | | 13:16:31.4700                              |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the time now as hmsf format       |
|                  |       matches the time displayed in the Bancomm  |
|                  |       card.                                      |
+------------------+--------------------------------------------------+

.. _req-106-02-timeclockinit-1:

3.8.4 REQ-106-02 timeClockInit
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `Test Setup and                               |
|                  |       Prerequisites <#epics-test-harness-5>`__   |
|                  |                                                  |
|                  | 2. `EPICS Test                                   |
|                  |       harness <#epics-test-harness-5>`__         |
+==================+==================================================+
| Procedure        | 1. The IOC must show no errors at the startup    |
|                  |                                                  |
|                  | 2. Check the VME startup messages when the       |
|                  |       timeClockInit function is executed.        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the Bancomm card is configured    |
|                  |       properly at startup.                       |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | # Configure the time system                |   |
|                  | |                                            |   |
|                  | | # Master ? Simulate ? intPerSecond,        |   |
|                  | | intPerTick, timeOffset (microsec)          |   |
|                  | |                                            |   |
|                  | | timeClockInit (1, 0, 60, 1, 0)             |   |
|                  | |                                            |   |
|                  | | BC635 setup: initial clock tick frequency: |   |
|                  | | 50                                         |   |
|                  | |                                            |   |
|                  | | BC635 Time Provider registered             |   |
|                  | |                                            |   |
|                  | | priority = 20                              |   |
|                  | |                                            |   |
|                  | | Flywheeling (not locked to reference)      |   |
|                  | |                                            |   |
|                  | | Last successful sync was at 1990-01-01     |   |
|                  | | 00:00:01.000000                            |   |
|                  | |                                            |   |
|                  | | Bancomm interrupt frequency set to 60.001  |   |
|                  | | Hz                                         |   |
|                  | |                                            |   |
|                  | | System clock frequency = 60.001 Hz         |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

.. _slalib-1:

3.9 Slalib
----------

.. _test-setup-and-prerequisites-6:

3.9.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _epics-test-harness-6:

3.9.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~

The Slalib library does not need any EPICS record that can be loaded
during startup or for testing purposes. It must used with other CCB
components or new modules.

3.9.2.1 Add the Slalib DBD file and library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------+
| labvme8-CP-ioc_DBD += slalib.dbd |
|                                  |
| ….                               |
|                                  |
| labvme8-CP-ioc_LIBS += slalib    |
+----------------------------------+

.. _startup-script-5:

3.9.2.2 Startup Script
^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme8-CP-ioc    |
|                                                         |
| cd ${INSTALL}                                           |
|                                                         |
| ## Register all support components                      |
|                                                         |
| dbLoadDatabase("dbd/labvme8-CP-ioc.dbd")                |
|                                                         |
| labvme8_CP_ioc_registerRecordDeviceDriver(pdbbase)      |
|                                                         |
| ## Load record instances                                |
|                                                         |
| #dbLoadTemplate("db/labvme8-CP-ioc.substitutions")      |
|                                                         |
| #dbLoadRecords("db/labvme8-CP-ioc.db", "user=username") |
|                                                         |
| iocInit                                                 |
|                                                         |
| ## Start any sequence programs                          |
|                                                         |
| #seq(sncxxx, "user=username")                           |
+---------------------------------------------------------+

3.9.2.3 Build and Install
^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------------+
|    $ cd labvme8/CP                  |
|                                     |
|    $ make distclean uninstall; make |
+-------------------------------------+

.. _req-107-01-slalib-ade-compatibility-1:

3.9.3 REQ-107-01 Slalib ADE Compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+---------------+-----------------------------------------------------+
| Prerequisites | 1. `3.9.1 Test Setup and                            |
|               |                                                     |
|               |  Prerequisites <#test-setup-and-prerequisites-6>`__ |
+===============+=====================================================+
| Procedure     | 1. Edit configure/RELEASE to put in correct paths   |
|               |       for dependencies:                             |
|               |                                                     |
|               | WORKSUP = /gem_sw/work/R3.14.12.4/support           |
|               |                                                     |
|               | PRODSUP = /gem_sw/prod/R3.14.12.4/support           |
|               |                                                     |
|               | SLALIB=$(PRODSUP)/slalib/1-9-5                      |
|               |                                                     |
|               | 2. Add dependencies to                              |
|               |       labvme8/CP/labvme8-CP-iocApp/src/Makefile     |
|               |                                                     |
|               | labvme8-CP-ioc_DBD += slalib.dbd                    |
|               |                                                     |
|               | labvme8-CP-ioc_LIBS += slalib                       |
|               |                                                     |
|               | 3. `Build and Install the                           |
|               |       module. <#build-and-install>`__               |
+---------------+-----------------------------------------------------+
|               | 1. The Slalib library shall be compatible with the  |
|               |       Application Development Environment (ADE ),   |
|               |       and build without errors or warnings.         |
+---------------+-----------------------------------------------------+

.. _req-107-02-slalib-available-for-linking-1:

3.9.4 REQ-107-02 Slalib Available for Linking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.9.1 Test Setup and                         |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-6>`__ |
+==================+==================================================+
| Procedure        | 1. Edit configure/RELEASE to put in correct      |
|                  |       paths for all library dependencies:        |
|                  |                                                  |
|                  | WORKSUP = /gem_sw/work/R3.14.12.4/support        |
|                  |                                                  |
|                  | PRODSUP = /gem_sw/prod/R3.14.12.4/support        |
|                  |                                                  |
|                  | IOCSTATS=$(PRODSUP)/iocStats/3-1-14-2            |
|                  |                                                  |
|                  | SNCSEQ=$(PRODSUP)/seq/2-2-3-1                    |
|                  |                                                  |
|                  | GEMREC=$(PRODSUP)/geminiRec/4-0-1                |
|                  |                                                  |
|                  | PVLOAD=$(PRODSUP)/pvload/1-1-1                   |
|                  |                                                  |
|                  | SLALIB=$(PRODSUP)/slalib/1-9-5                   |
|                  |                                                  |
|                  | 2. Add dependencies to                           |
|                  |       labvme8/CP/labvme8-CP-iocApp/src/Makefile  |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += base.dbd                   |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += iocAdmin.dbd               |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += geminiRecords.dbd          |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += pvload.dbd                 |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += slalib.dbd                 |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += devIocStats               |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += seq pv                    |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += geminiRecords             |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += pvload                    |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += slalib                    |
|                  |                                                  |
|                  | 3. `Build and Install the                        |
|                  |       module. <#build-and-install>`__            |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The slalib library shall be available for     |
|                  |       linking and testing with other CCB         |
|                  |       components.                                |
+------------------+--------------------------------------------------+

.. _astlib-1:

3.10 Astlib
-----------

.. _test-setup-and-prerequisites-7:

3.10.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _epics-test-harness-7:

3.10.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The Astlib library does not need any EPICS record that can be loaded
during startup or for testing purposes. It must used with other CCB
components or new modules.

3.10.2.1 Add the Astlib DBD file and library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------+
| labvme8-CP-ioc_DBD += astlib.dbd |
|                                  |
| ….                               |
|                                  |
| labvme8-CP-ioc_LIBS += astlib    |
+----------------------------------+

.. _startup-script-6:

3.10.2.2 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme8-CP-ioc    |
|                                                         |
| cd ${INSTALL}                                           |
|                                                         |
| ## Register all support components                      |
|                                                         |
| dbLoadDatabase("dbd/labvme8-CP-ioc.dbd")                |
|                                                         |
| labvme8_CP_ioc_registerRecordDeviceDriver(pdbbase)      |
|                                                         |
| ## Load record instances                                |
|                                                         |
| #dbLoadTemplate("db/labvme8-CP-ioc.substitutions")      |
|                                                         |
| #dbLoadRecords("db/labvme8-CP-ioc.db", "user=username") |
|                                                         |
| iocInit                                                 |
|                                                         |
| ## Start any sequence programs                          |
|                                                         |
| #seq(sncxxx, "user=username")                           |
+---------------------------------------------------------+

.. _build-and-install-1:

3.10.2.3 Build and Install
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------------+
|    $ cd labvme8/CP                  |
|                                     |
|    $ make distclean uninstall; make |
+-------------------------------------+

3.9.3 REQ-107-01 Astlib ADE Compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+---------------+-----------------------------------------------------+
| Prerequisites | 1. `3.10.1 Test Setup and                           |
|               |                                                     |
|               |  Prerequisites <#test-setup-and-prerequisites-7>`__ |
+===============+=====================================================+
| Procedure     | 1. Edit configure/RELEASE to put in correct paths   |
|               |       for dependencies:                             |
|               |                                                     |
|               | WORKSUP = /gem_sw/work/R3.14.12.4/support           |
|               |                                                     |
|               | PRODSUP = /gem_sw/prod/R3.14.12.4/support           |
|               |                                                     |
|               | ASTLIB=$(PRODSUP)/astlib/1-6-2                      |
|               |                                                     |
|               | 2. Add dependencies to                              |
|               |       labvme8/CP/labvme8-CP-iocApp/src/Makefile     |
|               |                                                     |
|               | labvme8-CP-ioc_DBD += astlib.dbd                    |
|               |                                                     |
|               | labvme8-CP-ioc_LIBS += astlib                       |
|               |                                                     |
|               | 3. `Build and Install the                           |
|               |       module. <#build-and-install-1>`__             |
+---------------+-----------------------------------------------------+
|               | 1. The Astlib library shall be compatible with the  |
|               |       Application Development Environment (ADE ),   |
|               |       and build without errors or warnings.         |
+---------------+-----------------------------------------------------+

3.10.4 REQ-107-02 Astlib Available for Linking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.10.1 Test Setup and                        |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-7>`__ |
+==================+==================================================+
| Procedure        | 1. Edit configure/RELEASE to put in correct      |
|                  |       paths for all library dependencies:        |
|                  |                                                  |
|                  | WORKSUP = /gem_sw/work/R3.14.12.4/support        |
|                  |                                                  |
|                  | PRODSUP = /gem_sw/prod/R3.14.12.4/support        |
|                  |                                                  |
|                  | IOCSTATS=$(PRODSUP)/iocStats/3-1-14-2            |
|                  |                                                  |
|                  | SNCSEQ=$(PRODSUP)/seq/2-2-3-1                    |
|                  |                                                  |
|                  | GEMREC=$(PRODSUP)/geminiRec/4-0-1                |
|                  |                                                  |
|                  | PVLOAD=$(PRODSUP)/pvload/1-1-1                   |
|                  |                                                  |
|                  | ASTLIB=$(PRODSUP)/astlib/1-6-2                   |
|                  |                                                  |
|                  | 2. Add dependencies to                           |
|                  |       labvme8/CP/labvme8-CP-iocApp/src/Makefile  |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += base.dbd                   |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += iocAdmin.dbd               |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += geminiRecords.dbd          |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += pvload.dbd                 |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += astlib.dbd                 |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += devIocStats               |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += seq pv                    |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += geminiRecords             |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += pvload                    |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += astlib                    |
|                  |                                                  |
|                  | 3. `Build and Install the                        |
|                  |       module. <#build-and-install-1>`__          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The astlib library shall be available for     |
|                  |       linking and testing with other CCB         |
|                  |       components.                                |
+------------------+--------------------------------------------------+

.. _asyn-1:

3.11 Asyn
---------

.. _test-setup-and-prerequisites-8:

3.11.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _epics-test-harness-8:

3.11.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

Th ASYN driver is an EPICS community package which has been tested and
probed to work properIy with RTEMS. The ASYN driver validation consists
on check their ADE compatibility and the availability to be linked to
other CCB components.

3.11.2.1 Add the ASYN DBD file and library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------+
| labvme8-CP-ioc_DBD += asyn.dbd |
|                                |
| ….                             |
|                                |
| labvme8-CP-ioc_LIBS += asyn    |
+--------------------------------+

.. _startup-script-7:

3.11.2.2 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme8-CP-ioc    |
|                                                         |
| cd ${INSTALL}                                           |
|                                                         |
| ## Register all support components                      |
|                                                         |
| dbLoadDatabase("dbd/labvme8-CP-ioc.dbd")                |
|                                                         |
| labvme8_CP_ioc_registerRecordDeviceDriver(pdbbase)      |
|                                                         |
| ## Load record instances                                |
|                                                         |
| #dbLoadTemplate("db/labvme8-CP-ioc.substitutions")      |
|                                                         |
| #dbLoadRecords("db/labvme8-CP-ioc.db", "user=username") |
|                                                         |
| iocInit                                                 |
|                                                         |
| ## Start any sequence programs                          |
|                                                         |
| #seq(sncxxx, "user=username")                           |
+---------------------------------------------------------+

.. _build-and-install-2:

3.11.2.3 Build and Install
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------------+
|    $ cd labvme8/CP                  |
|                                     |
|    $ make distclean uninstall; make |
+-------------------------------------+

.. _req-109-01-asyn-ade-compatibility-1:

3.11.3 REQ-109-01 ASYN ADE Compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+---------------+-----------------------------------------------------+
| Prerequisites | 1. `3.11.1 Test Setup and                           |
|               |                                                     |
|               |  Prerequisites <#test-setup-and-prerequisites-8>`__ |
+===============+=====================================================+
| Procedure     | 1. Edit configure/RELEASE to put in correct paths   |
|               |       for dependencies:                             |
|               |                                                     |
|               | WORKSUP = /gem_sw/work/R3.14.12.4/support           |
|               |                                                     |
|               | PRODSUP = /gem_sw/prod/R3.14.12.4/support           |
|               |                                                     |
|               | ASYN=$(PRODSUP)/asyn/4-29-1-0                       |
|               |                                                     |
|               | 2. Add dependencies to                              |
|               |       labvme8/CP/labvme8-CP-iocApp/src/Makefile     |
|               |                                                     |
|               | labvme8-CP-ioc_DBD += asyn.dbd                      |
|               |                                                     |
|               | labvme8-CP-ioc_LIBS += asyn                         |
|               |                                                     |
|               | 3. `Build and Install the                           |
|               |       module <#build-and-install-2>`__.             |
+---------------+-----------------------------------------------------+
|               | 1. The ASYN driver shall be compatible with the     |
|               |       Application Development Environment (ADE ),   |
|               |       and build without errors or warnings.         |
+---------------+-----------------------------------------------------+

3.11.4 REQ-109-02 ASYN Available for Linking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.11.1 Test Setup and                        |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-8>`__ |
+==================+==================================================+
| Procedure        | 1. Edit configure/RELEASE to put in correct      |
|                  |       paths for all library dependencies:        |
|                  |                                                  |
|                  | WORKSUP = /gem_sw/work/R3.14.12.4/support        |
|                  |                                                  |
|                  | PRODSUP = /gem_sw/prod/R3.14.12.4/support        |
|                  |                                                  |
|                  | IOCSTATS=$(PRODSUP)/iocStats/3-1-14-2            |
|                  |                                                  |
|                  | SNCSEQ=$(PRODSUP)/seq/2-2-3-1                    |
|                  |                                                  |
|                  | GEMREC=$(PRODSUP)/geminiRec/4-0-1                |
|                  |                                                  |
|                  | PVLOAD=$(PRODSUP)/pvload/1-1-1                   |
|                  |                                                  |
|                  | ASYN=$(PRODSUP)/4-29-1-0                         |
|                  |                                                  |
|                  | 2. Add dependencies to                           |
|                  |       labvme8/CP/labvme8-CP-iocApp/src/Makefile  |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += base.dbd                   |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += iocAdmin.dbd               |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += geminiRecords.dbd          |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += pvload.dbd                 |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += asyn.dbd                   |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += devIocStats               |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += seq pv                    |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += geminiRecords             |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += pvload                    |
|                  |                                                  |
|                  | labvme8-CP-ioc_LIBS += asyn                      |
|                  |                                                  |
|                  | 3. `Build and Install the                        |
|                  |       module. <#build-and-install-1>`__          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The ASYN driver shall be available for        |
|                  |       linking and testing with other CCB         |
|                  |       components.                                |
+------------------+--------------------------------------------------+

.. _streamdevice-1:

3.12 StreamDevice
-----------------

.. _test-setup-and-prerequisites-9:

3.12.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _epics-test-harness-9:

3.12.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The Stream Device driver is an EPICS community package which has been
tested and probed to work properly with RTEMS. The Stream Device driver
validation consists on check their ADE compatibility and the
availability to be linked to other CCB components.

3.12.2.1 Add the StreamSDevice DBD file and library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------------+
| labvme9-sbf-ioc_DBD += stream.dbd |
|                                   |
| ….                                |
|                                   |
| labvme9-sbf-ioc_LIBS += stream    |
+-----------------------------------+

.. _startup-script-8:

3.12.2.2 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme9-sbf-ioc    |
|                                                          |
| cd ${INSTALL}                                            |
|                                                          |
| ## Register all support components                       |
|                                                          |
| dbLoadDatabase("dbd/labvme8-CP-ioc.dbd")                 |
|                                                          |
| labvme9-sbf_ioc_registerRecordDeviceDriver(pdbbase)      |
|                                                          |
| ## Load record instances                                 |
|                                                          |
| #dbLoadTemplate("db/labvme9-sbf-ioc.substitutions")      |
|                                                          |
| #dbLoadRecords("db/labvme9-sbf-ioc.db", "user=username") |
|                                                          |
| iocInit                                                  |
|                                                          |
| ## Start any sequence programs                           |
|                                                          |
| #seq(sncxxx, "user=username")                            |
+----------------------------------------------------------+

.. _build-and-install-3:

3.12.2.3 Build and Install
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------------+
|    $ cd labvme9/sbf               |
|                                   |
|    $ make distclean uninstall all |
+-----------------------------------+

.. _section-6:

.. _req-110-01-stream-device-ade-compatibility-1:

3.12.3 REQ-110-01 Stream Device ADE Compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+---------------+-----------------------------------------------------+
| Prerequisites | 1. `3.12.1 Test Setup and                           |
|               |                                                     |
|               |  Prerequisites <#test-setup-and-prerequisites-9>`__ |
+===============+=====================================================+
| Procedure     | 1. Edit configure/RELEASE to put in correct paths   |
|               |       for dependencies:                             |
|               |                                                     |
|               | WORKSUP = /gem_sw/work/R3.14.12.4/support           |
|               |                                                     |
|               | PRODSUP = /gem_sw/prod/R3.14.12.4/support           |
|               |                                                     |
|               | SNCSEQ=$(PRODSUP)/seq/2-2-3 ls                      |
|               |                                                     |
|               | ASYN=$(PRODSUP)/asyn/4-29-1                         |
|               |                                                     |
|               | CALC=$(PRODSUP)/calc/3-6-1-1                        |
|               |                                                     |
|               | PCRE=$(PRODSUP)/pcre/8-38-1                         |
|               |                                                     |
|               | STREAM=$(PRODSUP)/StreamDevice/2-6-1                |
|               |                                                     |
|               | 2. Add dependencies to                              |
|               |       labvme8/CP/labvme8-CP-iocApp/src/Makefile     |
|               |                                                     |
|               | labvme9-sbf-ioc_DBD += stream.dbd                   |
|               |                                                     |
|               | labvme9-sbf-ioc_LIBS += stream                      |
|               |                                                     |
|               | 3. `Build and Install the                           |
|               |       module. <#build-and-install-3>`__             |
+---------------+-----------------------------------------------------+
|               | 1. The Stream Device driver shall be compatible     |
|               |       with the Application Development Environment  |
|               |       (ADE), and build without errors or warnings.  |
+---------------+-----------------------------------------------------+

3.12.4 REQ-110-02 Stream Device Available for Linking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.12.1 Test Setup and                        |
|                  |       Pr                                         |
|                  | erequisites <#test-setup-and-prerequisites-9>`__ |
+==================+==================================================+
| Procedure        | 1. Edit configure/RELEASE to put in correct      |
|                  |       paths for all library dependencies:        |
|                  |                                                  |
|                  | WORKSUP = /gem_sw/work/R3.14.12.4/support        |
|                  |                                                  |
|                  | PRODSUP = /gem_sw/prod/R3.14.12.4/support        |
|                  |                                                  |
|                  | IOCSTATS=$(PRODSUP)/iocStats/3-1-14-2            |
|                  |                                                  |
|                  | SNCSEQ=$(PRODSUP)/seq/2-2-3-1                    |
|                  |                                                  |
|                  | GEMREC=$(PRODSUP)/geminiRec/4-0-1                |
|                  |                                                  |
|                  | PVLOAD=$(PRODSUP)/pvload/1-1-1                   |
|                  |                                                  |
|                  | ASYN=$(PRODSUP)/asyn/4-29-1                      |
|                  |                                                  |
|                  | CALC=$(PRODSUP)/calc/3-6-1-1                     |
|                  |                                                  |
|                  | PCRE=$(PRODSUP)/pcre/8-38-1                      |
|                  |                                                  |
|                  | STREAM=$(PRODSUP)/StreamDevice/2-6-1             |
|                  |                                                  |
|                  | 2. Add dependencies to                           |
|                  |       labvme8/CP/labvme8-CP-iocApp/src/Makefile  |
|                  |                                                  |
|                  | labvme9-sbf-ioc_DBD += base.dbd                  |
|                  |                                                  |
|                  | labvme9-sbf-ioc_DBD += iocAdmin.dbd              |
|                  |                                                  |
|                  | labvme9-sbf-ioc_DBD += geminiRecords.dbd         |
|                  |                                                  |
|                  | labvme9-sbf-ioc_DBD += pvload.dbd                |
|                  |                                                  |
|                  | labvme8-CP-ioc_DBD += stream.dbd                 |
|                  |                                                  |
|                  | labvme9-sbf-ioc_LIBS += devIocStats              |
|                  |                                                  |
|                  | labvme9-sbf-ioc_LIBS += seq pv                   |
|                  |                                                  |
|                  | labvme9-sbf-ioc_LIBS += geminiRecords            |
|                  |                                                  |
|                  | labvme9-sbf-ioc_LIBS += pvload                   |
|                  |                                                  |
|                  | labvme9-sbf-ioc_LIBS += stream                   |
|                  |                                                  |
|                  | 3. `Build and Install the                        |
|                  |       module. <#build-and-install-3>`__          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The Stream Device driver shall be available   |
|                  |       for linking and testing with other CCB     |
|                  |       components.                                |
+------------------+--------------------------------------------------+

.. _section-7:

.. _xycom-240-1:

3.13 Xycom-240
--------------

.. _test-setup-and-prerequisites-10:

3.13.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Setup your test Xycom 240 with the proper jumper and switch settings to
define the base address. This test will use 0xD000 as the base address
so the S2 (switches 1-6) should be set as: 0,0,1,0,1,1 (where 0 is
closed and 1 is open). S2 (switches 7-8) define the
supervisor/non-privileged mode and address space selection. For this
test we use Supervisory OR Non-Privileged mode and Short I/O Access
Operation. Therefore, S2 (switches 7-8) should be set to 0,0 (both
closed).

================ ========= ============ ====================
Name             Hex Value Switch /pins Switch S2 (pins 1-6)
================ ========= ============ ====================
Base Address     0xD000    S2/ 1-6      0,0,1,0,1,1
Supervisory Mode 0x00      S2/ 7-8      0,0
Interrupt Level            S3 ...       
================ ========= ============ ====================

.. _epics-test-harness-10:

3.13.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The XYCOM-240 driver is equipped with a set of epics records which can
be loaded during startup. The database can be viewed in
xy240App/Db/xycom240Test.sch. Make sure your SYS_TDCT_PATHS is defined
like this,

+----------------------------------------------------------------------+
| [xy240App/Db]>echo $SYS_TDCT_PATHS                                   |
|                                                                      |
| /gem_sw/epics/R3.14.12.4/exte                                        |
| nsions/src/tdct/sym:/gem_sw/prod/R3.14.12.4/support/geminiRec/4-0/db |
+----------------------------------------------------------------------+

and use the tdct like this:

+--------------------------------------------------------+
| [xy240App/Db]>tdct -cfg ./tdct.cfg -i xycom240Test.sch |
+--------------------------------------------------------+

The test harness record names have been designed with specific tests in
mind:

+-----------+-----------+--------+-----------+-----------+------+
| Name      | De        | Signal | Xycom-240 | SCAN      | NOBT |
|           | scription |        | JK/Pin    |           |      |
+===========+===========+========+===========+===========+======+
| x         | Binary    | S0     | JK2/37    | Passive   | N/A  |
| y240:s0bo | Output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| x         | Binary    | S1     | JK2/38    | Passive   | N/A  |
| y240:s1bo | Output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| x         | Binary    | S2     | JK2/39    | Passive   | N/A  |
| y240:s2bo | Output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| x         | Binary    | S3     | JK2/40    | Passive   | N/A  |
| y240:s3bo | Output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy2       | Multi-bit | S0     | JK2/37    | Passive   | 4    |
| 40:s0mbbo | binary    |        | ,38,39,40 |           |      |
|           | output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy2       | Multi-bit | S4     | JK2/41    | Passive   | 4    |
| 40:s4mbbo | binary    |        | ,42,43,44 |           |      |
|           | output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy2       | Multi-bit | S8     | JK2/25    | Passive   | 4    |
| 40:s8mbbo | binary    |        | ,26,27,28 |           |      |
|           | output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy24      | Multi-bit | S12    | JK2/29    | Passive   | 4    |
| 0:s12mbbo | binary    |        | ,30,31,32 |           |      |
|           | output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy24      | Multi-bit | S16    | JK2/13    | Passive   | 4    |
| 0:s16mbbo | binary    |        | ,14,15,16 |           |      |
|           | output    |        |           |           |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy        | Binary    | S24    | JK1/1     | I/O       | N/A  |
| 240:s24bi | Input     |        |           | Interrupt |      |
+-----------+-----------+--------+-----------+-----------+------+
| xy        | Binary    | S25    | JK1/2     | I/O       | N/A  |
| 240:s25bi | Input     |        |           | Interrupt |      |
+-----------+-----------+--------+-----------+-----------+------+

3.13.2.1 Add the xycom240Test.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to, create a test ioc in the ADE environment, Eg.,

+-----------------------------------------+
| $gem-start-new-module.py -i gemtest1/MK |
+-----------------------------------------+

2. Edit your configure/RELEASE to include the XYCOM module from either
      WORK or PROD:

+---------------------------------------------+
| ...                                         |
|                                             |
| XYCOM=/gem_sw/work/R3.14.12.4/support/xycom |
+---------------------------------------------+

3. Find your gemtest1-MKApp/Db/Makefile and edit your Makefile to
      include the test harness as described in `3.2.1 EPICS Test
      Harness <#epics-test-harness-10>`__.

+-----------------------+
| DB += xycom240Test.db |
+-----------------------+

3.13.2.2 Add the xycom240 Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and libraries in your
gemtest1-MKApp/src/Makefile:

+------------------------------+
| gemtest1-MK_DBD += xy240.dbd |
|                              |
| ...                          |
|                              |
| gemtest1-MK_LIBS += xy240    |
+------------------------------+

.. _startup-script-9:

3.13.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

3.13.2.4 Edit your startup source file iocBoot/ioclab4vme-hbf/stgemtest1-MK.src to reflect a minimal test for the Xycom 240. You want to load your database definition (dbd), configure the Xycom 240, load the test harness records we imported and call iocInit().
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK |
|                                                   |
| cd ${INSTALL}                                     |
|                                                   |
| ## Register all support components                |
|                                                   |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")             |
|                                                   |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)   |
|                                                   |
| #Configure the Xycom 240 for our base address     |
|                                                   |
| drvXy240Config(1, 32, 0xd000)                     |
|                                                   |
| ## Load record instances                          |
|                                                   |
| #dbLoadRecords("db/xycom240Test.db")              |
|                                                   |
| iocInit                                           |
+---------------------------------------------------+

.. _build-and-install-4:

3.13.2.5 Build and install
^^^^^^^^^^^^^^^^^^^^^^^^^^

Build the test ioc:

+----------------------------------+
| $ cd gemtest1-MK/hbf             |
|                                  |
| $ make distclean uninstall; make |
+----------------------------------+

3.13.3 REQ-111-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are setup  |
|                  |       as described in `3.2 CPU Board             |
|                  |       Setup <#cpu-board-setup>`__.               |
|                  |                                                  |
|                  | 2. Ensure that your Xycom-240 board is setup as  |
|                  |       described in `3.13.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites <#test-setup-and-prerequisites-10>`__. |
|                  |                                                  |
|                  | 3. Prepare your test IOC for the Xycom Tests as  |
|                  |       described in `3.13.2 EPICS Test            |
|                  |       Harness <#epics-test-harness-10>`__.       |
+==================+==================================================+
| Procedure        | 1. Boot the prepared IOC and confirm the startup |
|                  |       script fully executes.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the Xycom-240 driver as a |
|                  |       result.                                    |
|                  |                                                  |
|                  | 3. The Front-Panel LED will turn from RED to     |
|                  |       GREEN upon successful initialization.      |
|                  |       **NB. This is a necessary but NOT a        |
|                  |       sufficient condition for a successful      |
|                  |       initialization.**                          |
|                  |                                                  |
|                  | 4. Run epicsThreadShowAll and verify the         |
|                  |       dio_scan task is running. This confirms    |
|                  |       the driver is successfully initialized and |
|                  |       the driver is emulating interrupt          |
|                  |       scanning.                                  |
|                  |                                                  |
|                  | +--------------------------------+               |
|                  | | 10.1.2.171> epicsThreadShowAll |               |
|                  | |                                |               |
|                  | | ...                            |               |
|                  | |                                |               |
|                  | | 0a010010 50 149 Delay dio_scan |               |
|                  | |                                |               |
|                  | | ...                            |               |
|                  | +--------------------------------+               |
|                  |                                                  |
|                  | 4. When the board initializes all inputs are set |
|                  |       to 1 and all outputs are set to 0. Verify  |
|                  |       this is the state by running the report    |
|                  |       for this driver.                           |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | | XY240 BINARY OUT CHANNELS:                 |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.13.4 REQ-111-02 EPICS Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.13.3 REQ-111-01                            |
|                  |       Initialize <#req-111-01-initialize>`__     |
+==================+==================================================+
| Procedure        | 1. This test is executed as part of the          |
|                  |       initialize test above.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. When the board initializes all inputs are set |
|                  |       to 1 and all outputs are set to 0. Verify  |
|                  |       this is the state by running the report    |
|                  |       for this driver.                           |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | | XY240 BINARY OUT CHANNELS:                 |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.13.5 REQ-111-03 Binary Output Support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.3.3 REQ-101-01                             |
|                  |       Initialize <#req-111-01-initialize>`__     |
|                  |                                                  |
|                  | 3. Configure a DMM to measure the output voltage |
|                  |       for signal S0. Refer to `2.2.4.1 Signal to |
|                  |       Port Mapping <#signal-to-port-mapping>`__  |
|                  |       for a Xycom-240 front-panel pinouts.       |
+==================+==================================================+
| Procedure        | 1. Reboot the IOC to reinitialize all eight I/O  |
|                  |       ports to the default values. All input     |
|                  |       ports 0-3 should read 1 and all output     |
|                  |       ports 4-7 should read 0.                   |
|                  |                                                  |
|                  | 2. Populate the xy240:S0bo record with a 1       |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ caput -n xy240:s0bo 1 |                      |
|                  | +-------------------------+                      |
|                  |                                                  |
|                  | 3. Populate the xy240:S0bo record with a 0       |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ caput -n xy240:s0bo 0 |                      |
|                  | +-------------------------+                      |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step one above report the Xycom 240     |
|                  |       channel status and verify the output       |
|                  |       channel 0 reports a 1 value as shown below |
|                  |       in red:.                                   |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 2. After step two above, use the DMM as          |
|                  |       configured in the prerequisites to verify  |
|                  |       a TTL level 1 (approximately 5 volts).     |
|                  |                                                  |
|                  | 3. After step two above report the Xycom 240     |
|                  |       channel status and verify output channel 0 |
|                  |       reads a 0 value, as shown below in red.    |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 4. After step three above, use the DMM as        |
|                  |       configured in the prerequisites to verify  |
|                  |       a TTL level 0 (approximately 0 volts).     |
+------------------+--------------------------------------------------+

3.13.6 REQ-111-04 Binary Input Support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.3.3 REQ-101-01                             |
|                  |       Initialize <#req-111-01-initialize>`__     |
|                  |                                                  |
|                  | 3. Configure a power supply to inject a 0-5 volt |
|                  |       input voltage for signal S0. Refer to      |
|                  |       `2.2.4.1 Signal to Port                    |
|                  |       Mapping <#signal-to-port-mapping>`__ for a |
|                  |       Xycom-240 front-panel pinouts.             |
+==================+==================================================+
| Procedure        | 1. Reboot the IOC to reinitialize all eight I/O  |
|                  |       ports to the default values. All input     |
|                  |       ports 0-3 should read 1 and all output     |
|                  |       ports 4-7 should read 0.                   |
|                  |                                                  |
|                  | 2. Set the power supply input voltage to 0       |
|                  |       volts.                                     |
|                  |                                                  |
|                  | 3. Set the power supply input voltage to 5       |
|                  |       volts.                                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step 2 above query the value of the     |
|                  |       input record xy240:s0bi and verify that it |
|                  |       is 0 by processing the record.             |
|                  |                                                  |
|                  | +-----------------------+                        |
|                  | | $ caget -n xy240:s0bi |                        |
|                  | |                       |                        |
|                  | | xy240:s0bi 0          |                        |
|                  | +-----------------------+                        |
|                  |                                                  |
|                  | 2. After step 2 in the procedure above, and      |
|                  |       after you process the record with caget,   |
|                  |       report the Xycom 240 channel status and    |
|                  |       verify output channel 0 reads a 0 value.   |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3. After step 3 in the procedure section above,  |
|                  |       process the input record xy240:s0bi and    |
|                  |       verify that it is 1.                       |
|                  |                                                  |
|                  | +-----------------------+                        |
|                  | | $ caget -n xy240:s0bi |                        |
|                  | |                       |                        |
|                  | | xy240:s0bi 1          |                        |
|                  | +-----------------------+                        |
|                  |                                                  |
|                  | 4. After step 3 in the procedure section above,  |
|                  |       report the Xycom 240 channel status and    |
|                  |       verify output channel 0 reads a 1 value.   |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.13.7 REQ-111-05 Multibit Binary Input Support (Passive Scanning)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.3.3 REQ-101-01                             |
|                  |       Initialize <#req-111-01-initialize>`__     |
|                  |                                                  |
|                  | 3. Configure a **dual channel** power supply to  |
|                  |       apply a 0-5 volt input for signals S24 and |
|                  |       S25 independently. Refer to `2.2.4.1       |
|                  |       Signal to Port                             |
|                  |       Mapping <#signal-to-port-mapping>`__ for a |
|                  |       Xycom-240 front-panel pinouts.             |
|                  |                                                  |
|                  | 4. Note for this test, xy240:s24mbbi is Passive  |
|                  |       with NOBT=2, so S24 and S25 are read and   |
|                  |       determine the final value for this record. |
+==================+==================================================+
| Procedure        | 1. Make sure the power supply is off and reboot  |
|                  |       the IOC to reinitialize all eight I/O      |
|                  |       ports to the default values. All input     |
|                  |       ports 0-3 should read 1 and all output     |
|                  |       ports 4-7 should read 0.                   |
|                  |                                                  |
|                  | 2. Test for 0x0. Set both channels of the power  |
|                  |       supply input voltage to 0 volts each.      |
|                  |       Check the S24 and S25 input values are     |
|                  |       both 0.                                    |
|                  |                                                  |
|                  | 3. Test for 0x1. Set the S25 channel on the      |
|                  |       power supply voltage to 5 volts, and leave |
|                  |       the S24 channel to 0 volts. Check the      |
|                  |       input values for S24 and S25 show 0 and 1  |
|                  |       respectively.                              |
|                  |                                                  |
|                  | 4. Test for 0x2. Set the S25 channel on the      |
|                  |       power supply voltage to 0 volts, and set   |
|                  |       the S24 channel to 5 volts. Check the      |
|                  |       input values for S24 and S25 show 1 and 0  |
|                  |       respectively.                              |
|                  |                                                  |
|                  | 5. Test for 0x3. Set the S25 channel on the      |
|                  |       power supply voltage to 5 volts, and set   |
|                  |       the S24 channel to 5 volts. Check the      |
|                  |       input values for S24 and S25 show 1 and 1  |
|                  |       respectively.                              |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step 2 above query the value of the     |
|                  |       input record xy240:s4mbbi and verify that  |
|                  |       it is 0 by processing the record.          |
|                  |                                                  |
|                  | +--------------------------+                     |
|                  | | $ caget -n xy240:s24mbbi |                     |
|                  | |                          |                     |
|                  | | xy240:s24mbbi 0          |                     |
|                  | +--------------------------+                     |
|                  |                                                  |
|                  | 2. After step 2 in the procedure above, and      |
|                  |       after you process the record with caget,   |
|                  |       report the Xycom 240 channel status and    |
|                  |       verify channel S24 and S25 read a 0 value  |
|                  |       as shown below in red.                     |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3. After step 2 above query the value of the     |
|                  |       input record xy240:s24mbbi and verify that |
|                  |       it is 0x1 by processing the record.        |
|                  |                                                  |
|                  | +--------------------------+                     |
|                  | | $ caget -n xy240:s24mbbi |                     |
|                  | |                          |                     |
|                  | | xy240:s24mbbi 1          |                     |
|                  | +--------------------------+                     |
|                  |                                                  |
|                  | 4. After step 3 in the procedure above, and      |
|                  |       after you process the record with caget,   |
|                  |       report the Xycom 240 channel status and    |
|                  |       verify that it is a 0x1 . S24 should       |
|                  |       report 0 and S25 should report a 1.        |
|                  |                                                  |
|                  | 5. After step 4 in the procedure section above,  |
|                  |       process the input record xy240:s24mbbi and |
|                  |       verify that it is a 0x2 .                  |
|                  |                                                  |
|                  | +--------------------------+                     |
|                  | | $ caget -n xy240:s24mbbi |                     |
|                  | |                          |                     |
|                  | | xy240:s24mbbi 2          |                     |
|                  | +--------------------------+                     |
|                  |                                                  |
|                  | 6. After step 4 in the procedure section above,  |
|                  |       and after you process the record with      |
|                  |       caget, report the Xycom 240 channel status |
|                  |       and verify that it is a 0x2 . S24 should   |
|                  |       report 1 and S25 should report a 0.        |
|                  |                                                  |
|                  | 7. After step 5 in the procedure section above,  |
|                  |       process the input record xy240:s24mbbi and |
|                  |       verify that it is a 0x3.                   |
|                  |                                                  |
|                  | +--------------------------+                     |
|                  | | $ caget -n xy240:s24mbbi |                     |
|                  | |                          |                     |
|                  | | xy240:s24mbbi 3          |                     |
|                  | +--------------------------+                     |
|                  |                                                  |
|                  | 8. After step 5 in the procedure section above,  |
|                  |       and after you process the record with      |
|                  |       caget, report the Xycom 240 channel status |
|                  |       and verify that it is a 0x3 . S24 should   |
|                  |       report 1 and S25 should report a 1.        |
+------------------+--------------------------------------------------+

3.13.8 REQ-111-06 Multibit Binary Output Support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.3.3 REQ-101-01                             |
|                  |       Initialize <#req-111-01-initialize>`__     |
|                  |                                                  |
|                  | 3. Configure a DMM to measure the output voltage |
|                  |       for signal S0, S1, S2 and S3. Refer to     |
|                  |       `2.2.4.1 Signal to Port                    |
|                  |       Mapping <#signal-to-port-mapping>`__ for a |
|                  |       Xycom-240 front-panel pinouts.             |
+==================+==================================================+
| Procedure        | 1. Reboot the IOC to reinitialize all eight I/O  |
|                  |       ports to the default values. All input     |
|                  |       ports 0-3 should read 1 and all output     |
|                  |       ports 4-7 should read 0.                   |
|                  |                                                  |
|                  | 2. The xy240:s0mbbo record has the NOBT=4 so     |
|                  |       S0,S1,S2 and S3 are set when this record   |
|                  |       processes. Populate the xy240:s0mbbo       |
|                  |       record with a 15 (0xF) to set all channels |
|                  |       to 1.                                      |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 15 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 3. Test 0xE. Populate the xy240:s0mbbo record    |
|                  |       with a 14 (0xE)                            |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 14 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 4. Test 0xD. Populate the xy240:s0mbbo record    |
|                  |       with a 13 (0xD)                            |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 13 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 5. Test 0xC. Populate the xy240:s0mbbo record    |
|                  |       with a 12 (0xC)                            |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 12 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 6. Test 0xB. Populate the xy240:s0mbbo record    |
|                  |       with a 11 (0xB)                            |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 11 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 7. Test 0xA. Populate the xy240:s0mbbo record    |
|                  |       with a 10 (0xA)                            |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 10 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 8. Test 0x9. Populate the xy240:s0mbbo record    |
|                  |       with a 9 (0x9)                             |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 9 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 9. Test 0x8. Populate the xy240:s0mbbo record    |
|                  |       with a 8 (0x8)                             |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 8 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 10. Test 0x7. Populate the xy240:s0mbbo record   |
|                  |        with a 7 (0x7)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 7 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 11. Test 0x6. Populate the xy240:s0mbbo record   |
|                  |        with a 6 (0x6)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 6 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 12. Test 0x5. Populate the xy240:s0mbbo record   |
|                  |        with a 5 (0x5)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 5 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 13. Test 0x4. Populate the xy240:s0mbbo record   |
|                  |        with a 4 (0x4)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 4 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 14. Test 0x3. Populate the xy240:s0mbbo record   |
|                  |        with a 3 (0x3)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 3 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 15. Test 0x2. Populate the xy240:s0mbbo record   |
|                  |        with a 2 (0x2)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 2 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 16. Test 0x1. Populate the xy240:s0mbbo record   |
|                  |        with a 1 (0x1)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 1 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 17. Test 0x0. Populate the xy240:s0mbbo record   |
|                  |        with a 0 (0x0)                            |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 0 |                    |
|                  | +---------------------------+                    |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step two in the procedure above, report |
|                  |       the Xycom 240 channel status and verify    |
|                  |       the output channel 0,1,2 and 3 all report  |
|                  |       1’s as values, as shown below in red.      |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 2. After step two in the procedure above, verify |
|                  |       the DMM reports a TTL level 1              |
|                  |       (approximately 5 volts) or TTL 0           |
|                  |       (approximately 0 volts) for the respective |
|                  |       output channels on the board.              |
|                  |                                                  |
|                  | 3. After step three in the procedure above,      |
|                  |       report the Xycom 240 channel status and    |
|                  |       verify output channel S0, S1 S2 and S3     |
|                  |       report the correct bit pattern for the     |
|                  |       respective test in question. RECALL, the   |
|                  |       most significant bit is Channel 3!         |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = ? Chan 1 = ? Chan 2 = ? Chan 3 =  |   |
|                  | | ?                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 4. After step three in the procedure above,      |
|                  |       verify the DMM reports a TTL level 1       |
|                  |       (approximately 5 volts) or TTL 0           |
|                  |       (approximately 0 volts) for the respective |
|                  |       output channels on the board. RECALL, the  |
|                  |       most significant bit is Channel 3!         |
|                  |                                                  |
|                  | 5. Repeat steps 3 and 4 for the remaining steps  |
|                  |       (through step 17) in the procedure.        |
|                  |       Confirm all bit patterns.                  |
+------------------+--------------------------------------------------+

3.13.9 REQ-111-07 Binary Input Support Interrupt Scanning
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+-------------------+-------------------------------------------------+
| Prerequisites     | 1. Open two Linux host terminals each           |
|                   |       configured as described in `3.1 Linux     |
|                   |       Host Setup <#linux-host-setup>`__.        |
|                   |                                                 |
|                   | 2. Ensure IOC is initialized as described in    |
|                   |       `3.3.3 REQ-101-01                         |
|                   |       Initialize <#req-111-01-initialize>`__.   |
|                   |                                                 |
|                   | 3. Connect all output data ports 4,5,6,7 on     |
|                   |       JK/2 to all input data ports 0,1,2,3 on   |
|                   |       JK/1.                                     |
|                   |                                                 |
|                   | 4. Refer to `2.2.4.1 Signal to Port             |
|                   |       Mapping <#signal-to-port-mapping>`__ for  |
|                   |       a Xycom-240 front-panel pinouts.          |
+===================+=================================================+
| Procedure         | 1. Reboot the IOC to reinitialize all eight I/O |
|                   |       ports to the default values.              |
|                   |       **NB.**\ *When the driver initializes,    |
|                   |       all input ports are set to 1 (TTL 5       |
|                   |       volts), and all output ports are set to 0 |
|                   |       (TTL 0 volts). Yet, all the input records |
|                   |       loaded are configured as SCAN I/O         |
|                   |       Interrupt, and since the outputs are      |
|                   |       default to zero (and you connected all    |
|                   |       the outputs to the input channels), these |
|                   |       output values shall exist on all input    |
|                   |       records (channels) that are processed.    |
|                   |       See the*\ `3.3.2 EPICS Test               |
|                   |       Harness <#epics-test-harness-10>`__\ *for |
|                   |       the Xycom-240 and confirm these records   |
|                   |       and have been set to zero immediately     |
|                   |       after initialize.* Confirm the success    |
|                   |       criteria below in step 1.                 |
|                   |                                                 |
|                   | 2. To get the bi input to process, we drive the |
|                   |       connected output channels. Then, confirm  |
|                   |       the proper state of the input channel .   |
|                   |       To make this easier, set your first Linux |
|                   |       terminal to monitor the input record and  |
|                   |       drive your outputs in the second          |
|                   |       terminal.                                 |
|                   |                                                 |
|                   | ..                                              |
|                   |                                                 |
|                   |    Terminal 1:                                  |
|                   |                                                 |
|                   | +---------------------------+                   |
|                   | | $ camonitor -n xy240:s0bi |                   |
|                   | +---------------------------+                   |
|                   |                                                 |
|                   | ..                                              |
|                   |                                                 |
|                   |    Terminal 2:                                  |
|                   |                                                 |
|                   | +-------------------------+                     |
|                   | | $ caput -n xy240:s0bo 1 |                     |
|                   | +-------------------------+                     |
|                   |                                                 |
|                   | 3. Return the output channel to zero by driving |
|                   |       xy240:s0bo to zero. Then, confirm the     |
|                   |       proper state of the input channels by     |
|                   |       watching your monitor terminal.           |
|                   |                                                 |
|                   | +-------------------------+                     |
|                   | | $ caput -n xy240:s1bo 0 |                     |
|                   | +-------------------------+                     |
|                   |                                                 |
|                   | 4. Repeat steps 2 and 3 for the following       |
|                   |       channels: xy240:s1bo.                     |
+-------------------+-------------------------------------------------+
| Success Criterial | 1. Confirm the state after iocInit() has set    |
|                   |       all connected input records with I/O      |
|                   |       interrupt channels to zero. Eg.,          |
|                   |       xy240:s0bi shall process due to the       |
|                   |       changing value from default 1 to 0.       |
|                   |                                                 |
|                   | 2. For steps 2,3 and 4 in the procedure,        |
|                   |       confirm the correct channel states at     |
|                   |       each step using the dbior drvXy240 1      |
|                   |       command.                                  |
+-------------------+-------------------------------------------------+

3.13.10 REQ-111-08 Multibit Binary Input Support (Interrupt Scanning)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminals each configured |
|                  |       as described in `3.1 Linux Host            |
|                  |       Setup <#linux-host-setup>`__.              |
|                  |                                                  |
|                  | 2. Ensure IOC is initialized as described in     |
|                  |       `3.3.3 REQ-101-01                          |
|                  |       Initialize <#req-111-01-initialize>`__.    |
|                  |                                                  |
|                  | 3. Connect all output data ports 4,5,6,7 on JK/2 |
|                  |       to all input data ports 0,1,2,3 on JK/1.   |
|                  |                                                  |
|                  | 4. Refer to `2.2.4.1 Signal to Port              |
|                  |       Mapping <#signal-to-port-mapping>`__ for a |
|                  |       Xycom-240 front-panel pinouts.             |
+==================+==================================================+
| Procedure        | 1. Reboot the IOC to reinitialize all eight I/O  |
|                  |       ports to the default values.               |
|                  |       **NB.**\ *When the driver initializes, all |
|                  |       input ports are set to 1 (TTL 5 volts),    |
|                  |       and all out ports are set to 0 (TTL 0      |
|                  |       volts). Yet, all the MBBI records loaded   |
|                  |       are configured as SCAN I/O Interrupt, and  |
|                  |       since the outputs are default to zero (and |
|                  |       you connected all the outputs to the input |
|                  |       channels), these output values shall exist |
|                  |       on all input records (channels) that are   |
|                  |       processed. See the*\ `3.3.2 EPICS Test     |
|                  |       Harness <#epics-test-harness-10>`__\ *for  |
|                  |       the Xycom-240 and confirm these MBBI       |
|                  |       records and the adjacent NOBT number of    |
|                  |       bits have been set to zero immediately     |
|                  |       after initialize.* Confirm the success     |
|                  |       criteria below in step 1.                  |
|                  |                                                  |
|                  | 2. To get the mbbi inputs to process, we drive   |
|                  |       the connected output channels. Drive the 4 |
|                  |       output signals S0-S3 to all ones by        |
|                  |       writing a 15 (0xF) into record             |
|                  |       xy240:s0mbbo . Then, confirm the proper    |
|                  |       state of the input channels. To make this  |
|                  |       easier, set your first Linux terminal to   |
|                  |       monitor the input record and drive your    |
|                  |       outputs in the second terminal.            |
|                  |                                                  |
|                  | ..                                               |
|                  |                                                  |
|                  |    Terminal 1:                                   |
|                  |                                                  |
|                  | +-----------------------------+                  |
|                  | | $ camonitor -n xy240:s0mbbi |                  |
|                  | +-----------------------------+                  |
|                  |                                                  |
|                  | ..                                               |
|                  |                                                  |
|                  |    Terminal 2:                                   |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | $ caput -n xy240:s0mbbo 15 |                   |
|                  | +----------------------------+                   |
|                  |                                                  |
|                  | 3. Return the S0-S3 channels to zero by driving  |
|                  |       xy240:s0mbbo to zero. Then, confirm the    |
|                  |       proper state of the input channels.        |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput -n xy240:s0mbbo 0 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 4. Repeat steps 2 and 3 for the following        |
|                  |       channels: xy240:s4mbbo, xy240:s8mbbo,      |
|                  |       xy240:s12mbbo, xy240:s16mbbo.              |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Confirm the state after iocInit() has set all |
|                  |       connected MBBI records with I/O interrupt  |
|                  |       channels to zero. Eg., xy240:s0mbbi shall  |
|                  |       process due to the changing value from     |
|                  |       default 1 to 0. Since NOBT =4, the         |
|                  |       adjacent 3 bits S1,S2 and S3 are also set  |
|                  |       to 0. This pattern is repeated for         |
|                  |       xy240:s4mbbi, xy240:s8mbbi, xy240:s12mbbi  |
|                  |       and xy240:s16mbbi.                         |
|                  |                                                  |
|                  | 2. Since the test harness does not include       |
|                  |       records for xy240:s20mbbi, xy240:s24mbbi,  |
|                  |       and xy240:s28mbbi, these final 12 input    |
|                  |       channels shall remain as the default of 1. |
|                  |                                                  |
|                  | 3. For steps 2,3 and 4 in the procedure, confirm |
|                  |       the correct channel states at each step    |
|                  |       using the dbior drvXy240 1 command.        |
+------------------+--------------------------------------------------+

3.13.11 REQ-111-09 Two Card Support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. Configure a second Xycom-240 board with the   |
|                  |       base address set appropriately.            |
|                  |                                                  |
|                  | 3. `3.3.3 REQ-101-01                             |
|                  |       Initialize <#req-111-01-initialize>`__     |
|                  |                                                  |
|                  | 4. Configure a DMM to measure the output voltage |
|                  |       for CARD 1 (second Xycom-240) signal S0.   |
|                  |       Refer to `2.2.4.1 Signal to Port           |
|                  |       Mapping <#signal-to-port-mapping>`__ for a |
|                  |       Xycom-240 front-panel pinouts.             |
|                  |                                                  |
|                  | 5. Connect the same output from S0 of Card 1 to  |
|                  |       the input S0 of Card1.                     |
+==================+==================================================+
| Procedure        | 1. Reboot the IOC to reinitialize all eight I/O  |
|                  |       ports to the default values. For CARD 1.   |
|                  |       all input ports 0-3 should read 1 (EXCEPT  |
|                  |       S0) and all output ports 4-7 should read   |
|                  |       0.                                         |
|                  |                                                  |
|                  | 2. Populate the xy240:S0bocard1 record with a 1  |
|                  |                                                  |
|                  | +------------------------------+                 |
|                  | | $ caput -n xy240:s0bocard1 1 |                 |
|                  | +------------------------------+                 |
|                  |                                                  |
|                  | 3. Populate the xy240:S0bocard1 record with a 0  |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ caput -n xy240:s0bo 0 |                      |
|                  | +-------------------------+                      |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Confirm a default state on both boards with   |
|                  |       the power supply off and the IOC just      |
|                  |       rebooted. Use the report functionality.    |
|                  |                                                  |
|                  | 2. After step **two** above report the Xycom-240 |
|                  |       channel status and verify the output       |
|                  |       channel 0 reports a 1 value. BOTH CARDS    |
|                  |       SHALL BE REPORTED.                         |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 1                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3. After step two above, verify the DMM reports  |
|                  |       a TTL level 1 (approximately 5 volts).     |
|                  |                                                  |
|                  | 4. After step three above report the Xycom-240   |
|                  |       channel status and verify output channel 0 |
|                  |       reads a 0 value.                           |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy240 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy240                           |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 0                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 1 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | |    XY240 BINARY OUT CHANNELS:              |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | |                                            |   |
|                  | | B*: XY240: card 1                          |   |
|                  | |                                            |   |
|                  | | XY240 BINARY IN CHANNELS:                  |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 1 Chan 2 = 1 Chan 3 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 1 Chan 5 = 1 Chan 6 = 1 Chan 7 =  |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 1 Chan 9 = 1 Chan 10 = 1 Chan 11  |   |
|                  | | = 1                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 1 Chan 13 = 1 Chan 14 = 1 Chan   |   |
|                  | | 15 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 1 Chan 17 = 1 Chan 18 = 1 Chan   |   |
|                  | | 19 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 1 Chan 21 = 1 Chan 22 = 1 Chan   |   |
|                  | | 23 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 1 Chan 25 = 1 Chan 26 = 1 Chan   |   |
|                  | | 27 = 1                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 1 Chan 29 = 1 Chan 30 = 1 Chan   |   |
|                  | | 31 = 1                                     |   |
|                  | |                                            |   |
|                  | | XY240 BINARY OUT CHANNELS:                 |   |
|                  | |                                            |   |
|                  | | Chan 0 = 0 Chan 1 = 0 Chan 2 = 0 Chan 3 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 4 = 0 Chan 5 = 0 Chan 6 = 0 Chan 7 =  |   |
|                  | | 0                                          |   |
|                  | |                                            |   |
|                  | | Chan 8 = 0 Chan 9 = 0 Chan 10 = 0 Chan 11  |   |
|                  | | = 0                                        |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0 Chan 13 = 0 Chan 14 = 0 Chan   |   |
|                  | | 15 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 16 = 0 Chan 17 = 0 Chan 18 = 0 Chan   |   |
|                  | | 19 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 20 = 0 Chan 21 = 0 Chan 22 = 0 Chan   |   |
|                  | | 23 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 24 = 0 Chan 25 = 0 Chan 26 = 0 Chan   |   |
|                  | | 27 = 0                                     |   |
|                  | |                                            |   |
|                  | | Chan 28 = 0 Chan 29 = 0 Chan 30 = 0 Chan   |   |
|                  | | 31 = 0                                     |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 5. After step three above, verify the DMM        |
|                  |       reports a TTL level 0 (approximately 0     |
|                  |       volts).                                    |
+------------------+--------------------------------------------------+

.. _xycom-566-1:

3.14 Xycom-566
--------------

.. _test-setup-and-prerequisites-11:

3.14.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Setup your test Xycom 566 with the proper jumper and switch settings to
define the base address. This test will use 0xC000 as the base address
so the S3 (switches 1-6 ) should be set as: 1,1,0,0,0,0 (where 0 is
closed and 1 is open). S2 (switches 7-8) define the
supervisor/non-privileged mode and address space selection. For this
test we use Supervisory OR Non-Privileged mode and Short I/O Access
Operation. Therefore, S3 (switches 7-8) should be set to 0,0 (both
closed).

+------------------+------------------+--------------+--------------+
| Name             | Hex Value        | Switch /pins | Switch Value |
+==================+==================+==============+==============+
| Switches         |                  |              |              |
+------------------+------------------+--------------+--------------+
| Interrupt Level  |                  | S1/1-3       | 110          |
| 3                |                  |              |              |
+------------------+------------------+--------------+--------------+
| Data Ram Address | 0xA000000        | S2/1-8       | 10100000     |
+------------------+------------------+--------------+--------------+
| Base Address,    | 0xC000           | S3/ 1-6      | 110000       |
| A16,             |                  |              |              |
| non-privileged   |                  |              |              |
+------------------+------------------+--------------+--------------+
| Supervisory Mode | 0x00             | S3/ 7-8      | 0,0          |
+------------------+------------------+--------------+--------------+
| Jumpers          |                  |              |              |
+------------------+------------------+--------------+--------------+
| IACK Daisy Chain | J1=B, J2=B       |              |              |
| Mode             |                  |              |              |
+------------------+------------------+--------------+--------------+
| Short I/O        | J3=B             |              |              |
+------------------+------------------+--------------+--------------+
| Bipolar, +/- 10  | J8=B, J10=B      |              |              |
| volt input       |                  |              |              |
+------------------+------------------+--------------+--------------+
| Offset binary,   | J9=B,            |              |              |
| A/D data format  | J14=J15=out      |              |              |
+------------------+------------------+--------------+--------------+

.. _epics-test-harness-11:

3.14.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The XYCOM-566 driver is equipped with a set of epics records which can
be loaded during startup. The database can be viewed in
xy566App/Db/xycom566Test.sch. Make sure your SYS_TDCT_PATHS is defined
like this,

+----------------------------------------------------------------------+
| [xy566App/Db]>echo $SYS_TDCT_PATHS                                   |
|                                                                      |
| /gem_sw/epics/R3.14.12.4/exte                                        |
| nsions/src/tdct/sym:/gem_sw/prod/R3.14.12.4/support/geminiRec/4-0/db |
+----------------------------------------------------------------------+

and use the tdct like this:

+--------------------------------------------------------+
| [xy566App/Db]>tdct -cfg ./tdct.cfg -i xycom566Test.sch |
+--------------------------------------------------------+

The test harness record names have been designed with specific tests in
mind based on the MCS Requirements for the use of the Xycom 566. See the
`MCS System
Note <https://drive.google.com/open?id=11SiQmNp9DEFfp89ijOznZefrOQr9GnBmJq9qmW9DxLs>`__
regarding the Xycom 566. There is only a single analog input record
running with Differential Scanned setup.

+----------+----------+--------+----------+----------+----------+
| Name     | Des      | Signal | X        | SCAN     | MDEL     |
|          | cription |        | ycom-240 |          |          |
|          |          |        | J        |          |          |
|          |          |        | K/Pin(s) |          |          |
+==========+==========+========+==========+==========+==========+
| xy5      | Analog   | S14    | J        | .1       | -1       |
| 66:aiS14 | Input    |        | K1/43,44 | second   |          |
|          |          |        | (diffe   |          | Trigger  |
|          |          |        | rential) |          | monitor  |
|          |          |        |          |          | every    |
|          |          |        |          |          | scan     |
+----------+----------+--------+----------+----------+----------+

3.14.2.1 Add the xycom566Test.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to, create a test ioc in the ADE environment, Eg.,

+-------------------------------------------+
| $gem-start-new-module.p￼￼y -i gemtest1/MK |
+-------------------------------------------+

2. The XYCOM module provides drivers and device support for both the 240
      and 566. Edit your configure/RELEASE to include the XYCOM module
      from either WORK or PROD:

+---------------------------------------------+
| ...                                         |
|                                             |
| XYCOM=/gem_sw/work/R3.14.12.4/support/xycom |
+---------------------------------------------+

3. Find your gemtest1-MKApp/Db/Makefile and edit your Makefile to
      include the test harness as described in `3.2.1 EPICS Test
      Harness <#epics-test-harness-10>`__.

+-----------------------+
| DB += xycom566Test.db |
+-----------------------+

3.14.2.2 Add the Xycom 566 DBD file and library 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and libraries in your
gemtest1-MKApp/src/Makefile:

+------------------------------+
| gemtest1-MK_DBD += xy566.dbd |
|                              |
| ...                          |
|                              |
| gemtest1-MK_LIBS += xy566    |
+------------------------------+

.. _startup-script-10:

3.14.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK            |
|                                                              |
| cd ${INSTALL}                                                |
|                                                              |
| zoneset("UTC0")                                              |
|                                                              |
| epicsEnvSet("ENGINEER","<YOUR NAME>")                        |
|                                                              |
| epicsEnvSet("LOCATION","HBF")                                |
|                                                              |
| ## Register all support components                           |
|                                                              |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")                        |
|                                                              |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)              |
|                                                              |
| #Configure the Xycom 240 for our base address                |
|                                                              |
| xy566_set_gain(0,0); #set card 0 to gain_entry 0 --> gain=1  |
|                                                              |
| #xy566_set_gain(0,1); #set card 0 to gain_entry 1 --> gain=2 |
|                                                              |
| xy566DIConfig(1, 16, 0xc000, 0xa00000)                       |
|                                                              |
| ## Load record instances                                     |
|                                                              |
| dbLoadRecords("db/xycom566Test.db")                          |
|                                                              |
| iocInit                                                      |
+--------------------------------------------------------------+

.. _build-and-install-5:

3.14.2.4 Build and install
^^^^^^^^^^^^^^^^^^^^^^^^^^

Build the test ioc:

+----------------------------------+
| $ cd gemtest1-MK/hbf             |
|                                  |
| $ make distclean uninstall; make |
+----------------------------------+

.. _req-112-01-initialize-1:

3.14.3 REQ-112-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are setup  |
|                  |       as described in `3.2 CPU Board             |
|                  |       Setup <#cpu-board-setup>`__.               |
|                  |                                                  |
|                  | 2. Ensure that your Xycom-566 board is setup as  |
|                  |       described in `3.14.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites <#test-setup-and-prerequisites-11>`__. |
|                  |                                                  |
|                  | 3. Prepare your test IOC for the Xycom Tests as  |
|                  |       described in `3.14.2 EPICS Test            |
|                  |       Harness <#epics-test-harness-11>`__.       |
+==================+==================================================+
| Procedure        | 1. Boot the prepared IOC and confirm the startup |
|                  |       script fully executes.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the Xycom-566 driver as a |
|                  |       result.                                    |
|                  |                                                  |
|                  | 3. The driver configures all the boards’         |
|                  |       internal registers during the INIT         |
|                  |       process, and as a final step sets the      |
|                  |       front-Panel LED from RED to GREEN upon     |
|                  |       successful initialization.                 |
+------------------+--------------------------------------------------+

.. _req-112-02-epics-report-1:

3.14.4 REQ-112-02 EPICS Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.14.3 REQ-112-01                            |
|                  |       Initialize <#req-112-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. After the board successfully initializes, run |
|                  |       the report for the Xycom 566.              |
|                  |                                                  |
|                  | +------------------------------+                 |
|                  | | 10.1.2.171> dbior drvXy566 1 |                 |
|                  | +------------------------------+                 |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The console will report the raw hex values    |
|                  |       for all 16 differential channels.          |
|                  |                                                  |
|                  | 2.                                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.171> dbior drvXy566 1               |   |
|                  | |                                            |   |
|                  | | Driver: drvXy566                           |   |
|                  | |                                            |   |
|                  | | AI: XY566-DI: card 0                       |   |
|                  | |                                            |   |
|                  | | type = 1                                   |   |
|                  | |                                            |   |
|                  | | type0_nchans = 16                          |   |
|                  | |                                            |   |
|                  | | type1_nchans = 32                          |   |
|                  | |                                            |   |
|                  | | type2_nchans = 16                          |   |
|                  | |                                            |   |
|                  | | Raw values:                                |   |
|                  | |                                            |   |
|                  | | Chan 00 = 0x0816 Chan 01 = 0x0814 Chan 02  |   |
|                  | | = 0x0813 Chan 03 = 0x080d                  |   |
|                  | |                                            |   |
|                  | | Chan 04 = 0x080a Chan 05 = 0x0807 Chan 06  |   |
|                  | | = 0x0805 Chan 07 = 0x0800                  |   |
|                  | |                                            |   |
|                  | | Chan 08 = 0x080b Chan 09 = 0x0805 Chan 10  |   |
|                  | | = 0x0802 Chan 11 = 0x07f6                  |   |
|                  | |                                            |   |
|                  | | Chan 12 = 0x07ef Chan 13 = 0x07e4 Chan 14  |   |
|                  | | = 0x0668 Chan 15 = 0x0804                  |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3.                                               |
+------------------+--------------------------------------------------+

.. _req-112-03-xy566diconfig-1:

3.14.5 REQ-112-03 xy566DIConfig
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.14.3 REQ-112-01                            |
|                  |       Initialize <#req-112-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. Following the initialize procedure above, the |
|                  |       board is configured using the              |
|                  |       xy566DIConfig() routine before iocInit().  |
|                  |                                                  |
|                  | 2. Ensure the startup script completes and       |
|                  |       iocInit() runs without error.              |
+------------------+--------------------------------------------------+
| Success Criteria | 1. This requirement is successfully passes if    |
|                  |       the initialize test passes.                |
+------------------+--------------------------------------------------+

3.14.6 REQ-112-04 Analog Input Record
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.14.3 REQ-112-01                            |
|                  |       Initialize <#req-112-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. Configure a 2-channel signal generator (SG)   |
|                  |       for differential outputs and connect to    |
|                  |       Channel 14 of the Xycom566. Tie the low    |
|                  |       outputs together, one high signal to pin   |
|                  |       43 and the other high signal to pin 44.    |
|                  |       This is described in `2.12.4.1 VMEIO       |
|                  |       Signal Pinout <#vmeio-signal-pinout>`__.   |
|                  |                                                  |
|                  | 2. Configure a scope to monitor and verify the   |
|                  |       output signal from the signal generator.   |
|                  |                                                  |
|                  | 3. Since the differential signal is twice the    |
|                  |       voltage at the input on channel 14, set    |
|                  |       the SG to +/- 5 volt square wave to see    |
|                  |       the full range of +/-10 volts.             |
|                  |                                                  |
|                  | 4. Set the frequency to 1 Hz.                    |
|                  |                                                  |
|                  | 5. Confirm your gain is set correct for unity    |
|                  |       gain in the startup script.                |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The scope will see 2 times the voltage of the |
|                  |       SG if connected to either pins 43 or 44    |
|                  |       and ground on pin 45. This is correct. We  |
|                  |       have +/-10 volts in this case.             |
|                  |                                                  |
|                  | 2.                                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | >camonitor xy566:aiS14                     |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.334238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.434238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.534238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.634238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.734238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.834238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.934238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.034238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.134238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.234238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.334238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.434238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.534238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.634238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.734238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.834238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.934238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:26.034238     |   |
|                  | | -9.99023                                   |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:26.134238 -10 |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3.                                               |
+------------------+--------------------------------------------------+

.. _req-112-05-gain-control-1:

3.14.7 REQ-112-05 Gain Control
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.14.6 REQ-112-04 Analog Input               |
|                  |                                                  |
|                  |      Record <#req-112-04-analog-input-record>`__ |
+==================+==================================================+
| Procedure        | 1. Follow the procedure for REQ-112-04 but set   |
|                  |       the gain_setting to entry 1 which is a     |
|                  |       gain of 2. This is commented out in the    |
|                  |       startup script above.                      |
|                  |                                                  |
|                  | 2. Set the SG amplitude to ¼ the full range, so  |
|                  |       2.5 Vpp.                                   |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The differential signal is doubled showing    |
|                  |       +/- 5 Vpp on the scope.                    |
|                  |                                                  |
|                  | 2. The epics record sees the gain of 2 and shows |
|                  |       +/- 10 Vpp.                                |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | >camonitor xy566:aiS14                     |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.334238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.434238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.534238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.634238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.734238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.834238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.934238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.034238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.134238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.234238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.334238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.434238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.534238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.634238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.734238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.834238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.934238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:26.034238     |   |
|                  | | -9.99023                                   |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:26.134238 -10 |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

.. _req-112-06-xy566seconfig-1:

3.14.8 REQ-112-06 xy566SEConfig
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ============================
Prerequisites    WAIVED as not used at Gemini
================ ============================
Procedure        
Success Criteria 
================ ============================

.. _req-112-07-xy566dilconfig-1:

3.14.9 REQ-112-07 xy566DILConfig
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ============================
Prerequisites    WAIVED as not used at Gemini
================ ============================
Procedure        
Success Criteria 
================ ============================

.. _req-112-08-xy566wfconfig-1:

3.14.10 REQ-112-08 xy566WFConfig
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ============================
Prerequisites    WAIVED as not used at Gemini
================ ============================
Procedure        
Success Criteria 
================ ============================

3.14.11 REQ-112-09 Dual Card Analog Input Record
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.1 Linux Host Setup <#linux-host-setup>`__  |
|                  |                                                  |
|                  | 2. `3.14.3 REQ-112-01                            |
|                  |       Initialize <#req-112-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. Configure a 2-channel signal generator (SG)   |
|                  |       for differential outputs and connect to    |
|                  |       Channel 14 of the Xycom566. Tie the low    |
|                  |       outputs together, one high signal to pin   |
|                  |       43 and the other high signal to pin 44.    |
|                  |       This is described in `2.12.4.1 VMEIO       |
|                  |       Signal Pinout <#vmeio-signal-pinout>`__.   |
|                  |                                                  |
|                  | 2. Configure a scope to monitor and verify the   |
|                  |       output signal from the signal generator.   |
|                  |                                                  |
|                  | 3. Since the differential signal is twice the    |
|                  |       voltage at the input on channel 14, set    |
|                  |       the SG to +/- 5 volt square wave to see    |
|                  |       the full range of +/-10 volts.             |
|                  |                                                  |
|                  | 4. Set the frequency to 1 Hz.                    |
|                  |                                                  |
|                  | 5. Confirm your gain is set correct for unity    |
|                  |       gain in the startup script.                |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The scope will see 2 times the voltage of the |
|                  |       SG if connected to either pins 43 or 44    |
|                  |       and ground on pin 45. This is correct. We  |
|                  |       have +/-10 volts in this case.             |
|                  |                                                  |
|                  | 2.                                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | >camonitor xy566:aiS14                     |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.334238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.434238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.534238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.634238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.734238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.834238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:24.934238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.034238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.134238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.234238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.334238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.434238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.534238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.634238 10  |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.734238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.834238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:25.934238 -10 |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:26.034238     |   |
|                  | | -9.99023                                   |   |
|                  | |                                            |   |
|                  | | xy566:aiS14 2016-08-25 17:37:26.134238 -10 |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3.                                               |
+------------------+--------------------------------------------------+

.. _section-8:

.. _section-9:

.. _section-10:

.. _pmac-1:

3.15 PMAC
---------

.. _test-setup-and-prerequisites-12:

3.15.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Set up your test PMAC-VME card for proper VME address space and base
addresses. This must be done through the serial port connection on front
panel connector J4. See application note #106A in the PMAC VME
Addressing document for the procedure. For this test, set the PMAC
mailbox address space to non-privileged access, A24 space at base
address 0x7FA000, DPRAM base address to 0x700000, interrupt level 7 and
IRQ vector to 0xA1.

.. _epics-test-harness-12:

3.15.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The pmac driver is equipped with a set of EPICS records which can be
loaded during startup and used for testing. The database can be viewed
in pmaclibApp/Db/pmactest.sch. Open in tdct like this:

+------------------------------------------------------+
| [pmaclibApp/Db]>tdct -cfg ./tdct.cfg -i pmactest.sch |
+------------------------------------------------------+

3.15.2.1 Add the pmactest.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to, create a test ioc in the ADE environment, Eg.,

+-----------------------------------------+
| $gem-start-new-module.py -i gemtest1/MK |
+-----------------------------------------+

2. Edit your configure/RELEASE to include the PMAC module from either
      WORK or PROD:

+----------------------------------------------+
| PMAC=/gem_sw/work/R3.14.12.4/support/pmaclib |
+----------------------------------------------+

3. Find your gemtest1-MKApp/Db/Makefile and edit your Makefile to
      include the test harness as described in `3.2.1 EPICS Test
      Harness <https://docs.google.com/document/d/1riqBByUvcI34ubQWQzHFLtfkV3DXafrONYEkJmi0Z6k/edit#heading=h.61tktv79xmlv>`__.

+-------------------+
| DB += pmactest.db |
+-------------------+

3.15.2.2 Add the pmaclib DBD file and Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and libraries in your
gemtest1-MKApp/src/Makefile:

+--------------------------------+
| gemtest1-MK_DBD += pmaclib.dbd |
|                                |
| …                              |
|                                |
| gemtest1-MK_LIBS += pmaclib    |
+--------------------------------+

.. _startup-script-11:

3.15.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK |
|                                                   |
| cd ${INSTALL}                                     |
|                                                   |
| zoneset("UTC0")                                   |
|                                                   |
| epicsEnvSet("ENGINEER","<YOUR NAME>")             |
|                                                   |
| epicsEnvSet("LOCATION","HBF")                     |
|                                                   |
| ## Register all support components                |
|                                                   |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")             |
|                                                   |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)   |
|                                                   |
| ...                                               |
|                                                   |
| iocInit                                           |
+---------------------------------------------------+

.. _req-113-01-initialize-1:

3.15.3 REQ-113-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are set up |
|                  |       as described in `3.2 CPU Board             |
|                  |       Setup. <#cpu-board-setup>`__               |
|                  |                                                  |
|                  | 2. Ensure that your PMAC-VME board is set up as  |
|                  |       described in `3.15.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-12>`__ |
|                  |                                                  |
|                  | 3. Preparre your test IOC for the PMAC tests as  |
|                  |       described in `3.15.2 EPICS Test            |
|                  |       Harness. <#epics-test-harness-12>`__       |
+==================+==================================================+
| Procedure        | 1. Boot the prepared IOC and confirm the startup |
|                  |       script fully executes.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IIOC must show no errors on startup.      |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the PMAC-VME driver as a  |
|                  |       result.                                    |
|                  |                                                  |
|                  | 3.                                               |
+------------------+--------------------------------------------------+

.. _req-113-02-epics-report-1:

3.15.4 REQ 113-02 EPICS Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.15.3 REQ-113-01                            |
|                  |       Initialize <#req-113-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. After the board successfully initializes, run |
|                  |       the driver report for the PMAC module:     |
|                  |                                                  |
|                  | +-----------------------------+                  |
|                  | | 10.1.2.171> dbior drvPmac 1 |                  |
|                  | +-----------------------------+                  |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The console will report the number of cards   |
|                  |       configured and the state of 17 operating   |
|                  |       parameters.                                |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | Driver: drvPmac                            |   |
|                  | |                                            |   |
|                  | | PMAC-VME driver: @(#) drvPmac.c 1.7        |   |
|                  | | 97/05/06                                   |   |
|                  | |                                            |   |
|                  | | Number of cards configured = 1.            |   |
|                  | |                                            |   |
|                  | | card = 0 ctlr = 0 present = 0 enabled = 0  |   |
|                  | |                                            |   |
|                  | | enabledMbx = 1 enabledAsc = 0 enabledRam = |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | enabledSvo = 1 enabledBkg = 1 enabledVar = |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | enabledOpn = 1 enabledFld = 1 enabledGat = |   |
|                  | | 1                                          |   |
|                  | |                                            |   |
|                  | | numSvoIo = 0 numBkgIo = 0 numVarIo = 0     |   |
|                  | | numOpnIo = 8                               |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

.. _req-113-03-analog-input-record-support-for-pmac-vme-ascii-1:

3.15.5 REQ 113-03 Analog Input Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ====================
Prerequisites    
================ ====================
Procedure        This test is waived.
Success Criteria 
================ ====================

.. _req-113-04-analog-output-record-support-for-pmac-vme-ascii-1:

3.15.6 REQ 113-04 Analog Output Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-05-binary-input-record-support-for-pmac-vme-ascii-1:

3.15.7 REQ 113-05 Binary Input Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-06-binary-output-record-support-for-pmac-vme-ascii-1:

3.15.8 REQ 113-06 Binary Output Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-07-long-input-record-support-for-pmac-vme-ascii-1:

3.15.9 REQ 113-07 Long Input Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-08-long-output-record-support-for-pmac-vme-ascii-1:

3.15.10 REQ 113-08 Long Output Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-09-multibit-binary-input-record-support-for-pmac-vme-ascii-1:

3.15.11 REQ 113-09 Multibit Binary Input Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-10-multibit-binary-output-record-support-for-pmac-vme-ascii-1:

3.15.12 REQ 113-10 Multibit Binary Output Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-11-string-input-record-support-for-pmac-vme-ascii-1:

3.15.13 REQ 113-11 String Input Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open a Linux host terminal, configured as     |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup. <#linux-host-setup>`__              |
+==================+==================================================+
| Procedure        | This will test two different variations of the   |
|                  | string input record:                             |
|                  |                                                  |
|                  | 1. | For the VME signal S1 variation:            |
|                  |       | In the Linux host window type:           |
|                  |                                                  |
|                  | +------------------------+                       |
|                  | | $ caget pmac0:mbxSiVer |                       |
|                  | +------------------------+                       |
|                  |                                                  |
|                  | 2. | For the VME signal S2 variation:            |
|                  |       | In the Linux host window type:           |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ caput pmac0:mbxSi ver |                      |
|                  | | $ caget pmac0:mbxsi     |                      |
|                  | +-------------------------+                      |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that for each variation of the         |
|                  |       stringin record tested, the caget command  |
|                  |       returns the correct firmware version of    |
|                  |       the PMAC-VME card.                         |
+------------------+--------------------------------------------------+

.. _req-113-12-string-output-record-support-for-pmac-vme-ascii-1:

3.15.14 REQ 113-12 String Output Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminal, configured as   |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup. <#linux-host-setup>`__              |
|                  |                                                  |
|                  | 2. In the first terminal window, use camonitor   |
|                  |       to monitor the values of pmac0:dprAi,      |
|                  |       pmac0:mbxSoSig1 and pmac0:mbxSoSig0:       |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | $ camonitor pmac0:dprAi pmac0:mbxSoSig1    |   |
|                  | | pmac0:mbxSoSig0                            |   |
|                  | +--------------------------------------------+   |
+==================+==================================================+
| Procedure        | This will test two different variations of the   |
|                  | stringoutput record:                             |
|                  |                                                  |
|                  | 1. | For the VME signal S1 variation:            |
|                  |       | In the second Linux host window type:    |
|                  |                                                  |
|                  | +-----------------------------------------+      |
|                  | | $ caput pmac0:mbxSoSig1 ‘M100->F:$DFFE’ |      |
|                  | | $ caput pmac0:mbxSoSig1 ‘M100=3.14’     |      |
|                  | | $ caput pmac0:mbxSoSig1 M100            |      |
|                  | +-----------------------------------------+      |
|                  |                                                  |
|                  | 2. | For the VME non-signal S1 variation:        |
|                  |       | In the second Linux host window type:    |
|                  |                                                  |
|                  | +-------------------------------------+          |
|                  | | $ caput pmac0:mbxSoSig0 ‘M100=6.28’ |          |
|                  | | $ caput pmac0:mbxSoSig0 M100        |          |
|                  | +-------------------------------------+          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step 1 of the procedure verify that     |
|                  |       camonitor in the first Linux terminal      |
|                  |       window reports the values of pmac0:Ai and  |
|                  |       pmac0:mbxSoSig1 as follows:                |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | pmac0:dprAi 2016-08-23 17:03:46.400063     |   |
|                  | | 3.14                                       |   |
|                  | |                                            |   |
|                  | | pmac0:mbxSoSig1 2016-08-23 17:03:46.442391 |   |
|                  | |                                            |   |
|                  | | pmac0:mbxSoSig1 2016-08-23 17:04:32.459209 |   |
|                  | | 3.14                                       |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 2. After step 2 of the procedure verify that     |
|                  |       camonitor reports the values of pmac0:Ai   |
|                  |       and pmac0:mbxSoSig0 as follows:            |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | pmac0:dprAi 2016-08-23 17:08:12.487568     |   |
|                  | | 6.28                                       |   |
|                  | |                                            |   |
|                  | | pmac0:mbxSoSig0 2016-08-23 17:08:20.824495 |   |
|                  | | m100                                       |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

.. _req-113-13-load-record-support-for-pmac-vme-ascii-1:

3.15.15 REQ 113-13 Load Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-14-status-record-support-for-pmac-vme-ascii-1:

3.15.16 REQ 113-14 Status Record Support for PMAC-VME ASCII
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-15-analog-input-record-support-for-pmac-vme-dpram-1:

3.15.17 REQ 113-15 Analog Input Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | .. rubric:: `3.15.14 REQ                         |
|                  |    113-12 <#req-113-12-strin                     |
|                  | g-output-record-support-for-pmac-vme-ascii-1>`__ |
|                  |    String Output Record Support for PMAC-VME     |
|                  |    ASCII                                         |
|                  |    :name: req-113-12-s                           |
|                  | tring-output-record-support-for-pmac-vme-ascii-2 |
+==================+==================================================+
| Procedure        | 1. None                                          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Successful completion of `3.15.14 REQ         |
|                  |       113-12 <#req-113-12-strin                  |
|                  | g-output-record-support-for-pmac-vme-ascii-1>`__ |
|                  |       String Output Record Support for PMAC-VME  |
|                  |       ASCII                                      |
+------------------+--------------------------------------------------+

.. _req-113-16-analog-output-record-support-for-pmac-vme-dpram-1:

3.15.18 REQ 113-16 Analog Output Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | `3.15.17 REQ 113-15 Analog Input Record Support  |
|                  | for PMAC-VME                                     |
|                  | DPRAM <#req-113-15-anal                          |
|                  | og-input-record-support-for-pmac-vme-dpram-1>`__ |
+==================+==================================================+
| Procedure        | None                                             |
+------------------+--------------------------------------------------+
| Success Criteria | Successful completion of `3.15.17 REQ 113-15     |
|                  | Analog Input Record Support for PMAC-VME         |
|                  | DPRAM <#req-113-15-anal                          |
|                  | og-input-record-support-for-pmac-vme-dpram-1>`__ |
+------------------+--------------------------------------------------+

.. _req-113-17-binary-input-record-support-for-pmac-vme-dpram-1:

3.15.19 REQ 113-17 Binary Input Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-18-binary-output-record-support-for-pmac-vme-dpram-1:

3.15.20 REQ 113-18 Binary Output Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-19-event-record-support-for-pmac-vme-dpram-1:

3.15.21 REQ 113-19 Event Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-20-long-input-record-support-for-pmac-vme-dpram-1:

3.15.22 REQ 113-20 Long Input Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-21-long-output-record-support-for-pmac-vme-dpram-1:

3.15.23 REQ 113-21 Long Output Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-22-multibit-binary-input-record-support-for-pmac-vme-dpram-1:

3.15.24 REQ 113-22 Multibit Binary Input Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-23-multibit-binary-output-record-support-for-pmac-vme-dpram-1:

3.15.25 REQ 113-23 Multibit Binary Output Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ===============================================
Prerequisites    NOTE: This type of record is not used at Gemini
================ ===============================================
Procedure        This test is waived.
Success Criteria 
================ ===============================================

.. _req-113-24-status-record-support-for-pmac-vme-dpram-1:

3.15.26 REQ 113-24 Status Record Support for PMAC-VME DPRAM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open 2 Linux host terminals, configured as    |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup <#linux-host-setup>`__               |
|                  |                                                  |
|                  | 2. In one terminal, start the pmac status record |
|                  |       monitor display screen:                    |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | $                                          |   |
|                  | | /gem                                       |   |
|                  | | _sw/prod/R3.14.12.4/support/pmaclib/{ver}/ |   |
|                  | | bin/linux-x86_64/pmaclibTest.sh            |   |
|                  | +============================================+   |
|                  | | |pmacStatusRecMonitor.png|                 |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | ￼                                                |
+==================+==================================================+
| Procedure        | 1. In the second terminal, issue the following   |
|                  |       command:                                   |
|                  |                                                  |
|                  | +--------------------------------+               |
|                  | | $ caput pmac0:dprLo 2147483647 |               |
|                  | +--------------------------------+               |
|                  |                                                  |
|                  | 2. Again, In the second terminal, issue the      |
|                  |       following command:                         |
|                  |                                                  |
|                  | +------------------------------+                 |
|                  | | $ caput pmac0:dprLo 11184810 |                 |
|                  | +------------------------------+                 |
|                  |                                                  |
|                  | 3. Again, In the second terminal, issue the      |
|                  |       following command:                         |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | caput pmac0:dprLo 5592405 |                    |
|                  | +---------------------------+                    |
|                  |                                                  |
|                  | 4. Again, In the second terminal, issue the      |
|                  |       following command:                         |
|                  |                                                  |
|                  | +---------------------+                          |
|                  | | caput pmac0:dprLo 0 |                          |
|                  | +---------------------+                          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step 1 of the procedure, verify that    |
|                  |       the status record monitor screen indicates |
|                  |       all bits 0 - 23 are green.                 |
|                  |                                                  |
|                  | 2. After step 2 of the procedure verify that     |
|                  |       same screen indicates only the odd         |
|                  |       numbered bits up to bit 23 are green.      |
|                  |                                                  |
|                  | 3. After step 3 of the procedure, verify that    |
|                  |       same screen indicates only the even        |
|                  |       numbered bits up to bit 22 are green.      |
|                  |                                                  |
|                  | 4. After step 4 of the procedure, verify that    |
|                  |       same screen indicates none of the bits are |
|                  |       green.                                     |
|                  |                                                  |
|                  | 5. Verify that for each step of the procedure    |
|                  |       the status record monitor screen does not  |
|                  |       indicate any of the bits 24 - 31 become    |
|                  |       green.                                     |
+------------------+--------------------------------------------------+

.. _req-113-25-two-card-support-1:

3.15.27 REQ 113-25 Two Card Support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | This will test basic functionality of two PMAC   |
|                  | cards on one CPU                                 |
|                  |                                                  |
|                  | Details incomplete                               |
+==================+==================================================+
| Procedure        |                                                  |
+------------------+--------------------------------------------------+
| Success Criteria |                                                  |
+------------------+--------------------------------------------------+

3.15.28 REQ 113-26 Continuous Data Stream 1 card
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open 3 Linux host terminals, configured as    |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup <#linux-host-setup>`__               |
|                  |                                                  |
|                  | 2. In the first terminal, issue the following    |
|                  |       command:                                   |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | $ camonitor pmac0:azM60 pmac0:azM61 …      |   |
|                  | | pmac0:azM79                                |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3. In the second terminal, issue the following   |
|                  |       command:                                   |
|                  |                                                  |
|                  | +----------------------------------------------+ |
|                  | | $ camonitor pmac0:M90 pmac0:M91 … pmac0:M109 | |
|                  | +----------------------------------------------+ |
+==================+==================================================+
| Procedure        | 1. In the first terminal, issue the following    |
|                  |       command:                                   |
|                  |                                                  |
|                  | +----------------------------------------+       |
|                  | | $ caput pmac0:Tracking.SCAN “1 second” |       |
|                  | +----------------------------------------+       |
|                  |                                                  |
|                  | 2. Wait around 10 seconds and issue the          |
|                  |       following command:                         |
|                  |                                                  |
|                  | +---------------------------------------+        |
|                  | | $ caput pmac0:Tracking.SCAN “Passive” |        |
|                  | +---------------------------------------+        |
|                  |                                                  |
|                  | 3. In the first terminal, issue the following    |
|                  |       command:                                   |
|                  |                                                  |
|                  | +------------------------------------------+     |
|                  | | $ caput pmac0:Tracking.SCAN “.01 second” |     |
|                  | +------------------------------------------+     |
|                  |                                                  |
|                  | 4. Wait around 10 seconds and issue the          |
|                  |       following command:                         |
|                  |                                                  |
|                  | +---------------------------------------+        |
|                  | | $ caput pmac0:Tracking.SCAN “Passive” |        |
|                  | +---------------------------------------+        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step 2 of the procedure, verify that    |
|                  |       the timestamp difference between           |
|                  |       pmac0:azM60 and pmac0:M90 is 0.01 second   |
|                  |       or less.                                   |
|                  |                                                  |
|                  | 2. Verify that the timestamp difference between  |
|                  |       two correlatives instances of pmac0:M90 os |
|                  |       1 second. Also the value for pmac0:azM60   |
|                  |       and pmac0:M90 must be the same.            |
|                  |                                                  |
|                  | 3. After step 3 of the procedure, verify that    |
|                  |       the timestamp difference between           |
|                  |       pmac0:azM60 and pmac0:M90 is 0.01 second   |
|                  |       or less.                                   |
|                  |                                                  |
|                  | 4. Verify that the timestamp difference between  |
|                  |       two correlatives instances of pmac0:M90 os |
|                  |       .01 second. Also the value for pmac0:azM60 |
|                  |       and pmac0:M90 must be the same.            |
+------------------+--------------------------------------------------+

3.15.29 REQ 113-27 Two Continuous Data Streams 2 cards
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

3.15.30 REQ 113-28 Servo Data Reporting Thread
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminal, configured as   |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup. <#linux-host-setup>`__              |
|                  |                                                  |
|                  | 2. In the first terminal window, use camonitor   |
|                  |       to monitor the value of pmac0:aiOpn        |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ camonitor pmac0:aiSvo |                      |
|                  | +-------------------------+                      |
+==================+==================================================+
| Procedure        | 1. In the second Linux host terminal, command    |
|                  |       the value of pmac0:aoOpn:                  |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | $ caput pmac0:aoSvo 1.414 |                    |
|                  | +---------------------------+                    |
+------------------+--------------------------------------------------+
| Success Criteria | 1. 1Verify that camonitor in the first Linux     |
|                  |       host terminal reports the correct value    |
|                  |       for pmac0:aiSvo:                           |
|                  |                                                  |
|                  | +----------------------------------------------+ |
|                  | | pmac0:aiSvo 2016-08-26 14:01:49.050103 1.414 | |
|                  | +----------------------------------------------+ |
+------------------+--------------------------------------------------+

3.15.31 REQ 113-29 Background Fixed Data Reporting Thread
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminal, configured as   |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup. <#linux-host-setup>`__              |
|                  |                                                  |
|                  | 2. In the first terminal window, use camonitor   |
|                  |       to monitor the value of pmac0:aiOpn        |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ camonitor pmac0:aiBfd |                      |
|                  | +-------------------------+                      |
+==================+==================================================+
| Procedure        |                                                  |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that camonitor in the first Linux host |
|                  |       terminal reports the correct value for     |
|                  |       pmac0:aiBfd:                               |
|                  |                                                  |
|                  | +----------------------------------------------+ |
|                  | | pmac0:aiBfd 2016-08-26 14:01:49.050103 98.62 | |
|                  | +----------------------------------------------+ |
+------------------+--------------------------------------------------+

3.15.32 REQ 113-30 Background Variable Data Reporting Thread
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Open two Linux host terminal, configured as   |
|                  |       described in `3.1 Linux Host               |
|                  |       Setup. <#linux-host-setup>`__              |
|                  |                                                  |
|                  | 2. In the first terminal window, use camonitor   |
|                  |       to monitor the value of pmac0:aiBvd        |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ camonitor pmac0:aiBvd |                      |
|                  | +-------------------------+                      |
+==================+==================================================+
| Procedure        | 1. In the second Linux host terminal, command    |
|                  |       the value of pmac0:mbxSoSig1               |
|                  |                                                  |
|                  | +------------------+                             |
|                  | | $ caput pmac0:mb |                             |
|                  | +------------------+                             |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that camonitor in the first Linux host |
|                  |       terminal reports the correct value for     |
|                  |       pmac0:aiBvd:                               |
|                  |                                                  |
|                  | +----------------------------------------------+ |
|                  | | pmac0:aiBvd 2016-09-06 15:38:21.975589 12345 | |
|                  | +----------------------------------------------+ |
+------------------+--------------------------------------------------+

3.15.33 REQ 113-31 Open Area Data Thread
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | Open two Linux host terminal, configured as      |
|                  | described in `3.1 Linux Host                     |
|                  | Setup. <#linux-host-setup>`__                    |
|                  |                                                  |
|                  | In the first terminal window, use camonitor to   |
|                  | monitor the value of pmac0:aiOpn                 |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | $ camonitor pmac0:aiOpn |                      |
|                  | +-------------------------+                      |
+==================+==================================================+
| Procedure        | 2. In the second Linux host terminal, command    |
|                  |       the value of pmac0:aoOpn:                  |
|                  |                                                  |
|                  | +-----------------------------+                  |
|                  | | $ caput pmac0:aoOpn 3.14159 |                  |
|                  | +-----------------------------+                  |
+------------------+--------------------------------------------------+
| Success Criteria | 2. Verify that camonitor in the first Linux host |
|                  |       terminal reports the correct value for     |
|                  |       pmac0:aiOpn:                               |
|                  |                                                  |
|                  | +-                                               |
|                  | -----------------------------------------------+ |
|                  | |                                                |
|                  | pmac0:aiOpn 2016-08-26 14:01:49.050103 3.14159 | |
|                  | +-                                               |
|                  | -----------------------------------------------+ |
+------------------+--------------------------------------------------+

.. _req-113-32-file-download-thread-1:

3.15.34 REQ 113-32 File Download Thread
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ====================
Prerequisites    
================ ====================
Procedure        This test is waived.
Success Criteria 
================ ====================

.. _req-113-33-gather-data-readwrite-threads-1:

3.15.35 REQ 113-33 Gather Data Read/Write Threads
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ====================
Prerequisites    
================ ====================
Procedure        This test is waived.
Success Criteria 
================ ====================

3.16 OMS44
----------

.. _test-setup-and-prerequisites-13:

3.16.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Setup your test OMS VME44 with the proper jumper and switch settings to
define the base address. This test will use the factory default address
of 0xFF80,jumpers for address line A15, A14, A13, A12, A11, A10, A9 and
A8 on J25 and A7 on J26 should be absent, selecting 1 for those lines.
Jumpers should be placed on A6, A7 and A4 on J26, selecting a 0 for
those lines. Alternatively, for GS testing, address 0xFC00 (Jumpers on
A7, A8 & A9) could be used, avoiding a later change when live testing.
The OMS44 allows Short Non-Previleged Access setting a jumper on lines
AM1 and AM4 to decode them when they are low, no jumper should be on AM0
and AM5 so they will be decoded when high.

+--------------------+-------------+----------+--------------------+
| Name               | Hex Value   | Switches | Switch Value       |
+====================+=============+==========+====================+
| Interrupt Level 5  | 0xEF/0xB    | J15/J35  | 11101111/1011      |
+--------------------+-------------+----------+--------------------+
| Limit Polarity     | 0x0         | J24      | 0000               |
| Selection          |             |          |                    |
+--------------------+-------------+----------+--------------------+
| Base Address, A16, | 0xFF80      | J25/J26  | 11111111/10000011  |
| non-privileged     |             |          |                    |
|                    | (0xFC00 GS) |          | (                  |
|                    |             |          | 11111110/00000011) |
+--------------------+-------------+----------+--------------------+
| Encoder Bias       | 0xF         | J11      | 1111               |
| Selection (T & Z   |             |          |                    |
| Axes)              |             |          |                    |
+--------------------+-------------+----------+--------------------+
| Encoder Bias       | 0xF         | J14      | 1111               |
| Selection (X & Y   |             |          |                    |
| Axes)              |             |          |                    |
+--------------------+-------------+----------+--------------------+
| I/O and Pull Up    | 0xFC        | J32      | 11111100           |
| Select             |             |          |                    |
+--------------------+-------------+----------+--------------------+

You will also need to setup a test-bench in order to test hardware I/O.
The following should be the setup:

Hardware needed

-  Bread Board

-  VME Backboard cable w/64Pin connector

-  Oscilloscope with passive 10x probes

-  Resistor 300 Ohm

-  Switch 2 positions

-  Wires

Setup

-  Using the bread-board, make the following connections

=================== ===================
64DIN Connector Pin Bread Board
=================== ===================
21a                 Resistor Terminal A
21c                 Resistor Terminal B
23a                 Switch Terminal 1
24c                 Switch Terminal 2
=================== ===================

+-----------------------------+
| |oms44_testbench.png|       |
+=============================+
| Fig: OMS44 Motor Test Bench |
+-----------------------------+

-  Connect the 64 pin VME cable to backplane connector P2 of the OMS44
      slot

-  Connect Channel 1 of the oscilloscope to the connection point
      Resistor B.

.. _epics-test-harness-13:

3.16.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The OMS44 driver is equipped with an epics records which can be loaded
during startup. The database can be viewed in oms44App/Db/oms44Test.sch.
Make sure your SYS_TDCT_PATHS is defined like this,

+----------------------------------------------------------------------+
| [xy566App/Db]>echo $SYS_TDCT_PATHS                                   |
|                                                                      |
| /gem_sw/epics/R3.14.12.4/exte                                        |
| nsions/src/tdct/sym:/gem_sw/prod/R3.14.12.4/support/geminiRec/4-0/db |
+----------------------------------------------------------------------+

and use the tdct like this:

+-----------------------------------------------------+
| [xy566App/Db]>tdct -cfg ./tdct.cfg -i oms44Test.sch |
+-----------------------------------------------------+

The test harness motor record

=========== ============ ======= ====
Name        Description    SCAN    NOBT
=========== ============ ======= ====
$(top)motor Motor Record   Passive N/A
=========== ============ ======= ====

3.16.2.1 Add the oms44Test.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to, create a test ioc in the ADE environment, Eg.,

+-------------------------------------------+
|    $gem-start-new-module.py -i labvme8/CP |
+-------------------------------------------+

2. The OMS44 module provides driver and device support for the OMS VME44
      card. Edit your configure/RELEASE to include the OMS44 module from
      PROD:

+----------------------------------------------+
|    PRODSUP = /gem_sw/prod/R3.14.12.4/support |
|                                              |
|    …                                         |
|                                              |
|    OMS44=$(PRODSUP)/oms44/1-0-1              |
+----------------------------------------------+

3. Find your labvme8-CP-App/Db/Makefile and edit your Makefile to
      include the test harness as described in `3.2.1 EPICS Test
      Harness <https://docs.google.com/document/d/18Dh69MtvqhNOmubMdDTA2BO2wkRWw3m_DF5OXEk58cc/edit#heading=h.61tktv79xmlv>`__.

+-----------------------+
|    DB += oms44Test.db |
+-----------------------+

3.16.2.2 Add the OMS44 DBD file and library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and libraries in your
gemtest1-MKApp/src/Makefile:

+---------------------------------+
| labvme8-CP-ioc_DBD += oms44.dbd |
|                                 |
| ...                             |
|                                 |
| labvme8-CP-ioc_LIBS += oms44    |
+---------------------------------+

.. _startup-script-12:

3.16.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme8-CP-ioc |
|                                                      |
| cd ${INSTALL}                                        |
|                                                      |
| zoneset("UTC0")                                      |
|                                                      |
| epicsEnvSet("ENGINEER","<YOUR NAME>")                |
|                                                      |
| epicsEnvSet("LOCATION","HBF")                        |
|                                                      |
| ## Register all support components                   |
|                                                      |
| dbLoadDatabase("dbd/labvme8-CP-ioc.dbd")             |
|                                                      |
| labvme8-CP-ioc_registerRecordDeviceDriver(pdbbase)   |
|                                                      |
| ## Load record instances                             |
|                                                      |
| dbLoadRecords("db/oms44Test.db")                     |
|                                                      |
| iocInit                                              |
+------------------------------------------------------+

.. _build-and-install-6:

3.16.2.4 Build and install
^^^^^^^^^^^^^^^^^^^^^^^^^^

Build the test ioc:

+-------------------------------------+
|    $ cd labvme8/CP                  |
|                                     |
|    $ make distclean uninstall; make |
+-------------------------------------+

.. _req-114-01-initialize-1:

3.16.3 REQ-114-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are setup  |
|                  |       as described in `3.2 CPU Board             |
|                  |       Setup <https://docs.goo                    |
|                  | gle.com/document/d/18Dh69MtvqhNOmubMdDTA2BO2wkRW |
|                  | w3m_DF5OXEk58cc/edit#heading=h.ipoe1u10cu8i>`__. |
|                  |                                                  |
|                  | 2. Ensure that your OMS VME44 board is setup as  |
|                  |       described in 3.16.1 Test Setup and         |
|                  |       Prerequisites.                             |
|                  |                                                  |
|                  | 3. Prepare your test IOC for the OMS44 Tests as  |
|                  |       described in 3.16.2 EPICS Test Harness.    |
+==================+==================================================+
| Procedure        | 1. Boot the prepared IOC and confirm the startup |
|                  |       script fully executes.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the oms44 driver as a     |
|                  |       result.                                    |
+------------------+--------------------------------------------------+

.. _section-11:

.. _section-12:

.. _req-114-02-oms44-motor-record-support-1:

3.16.4 REQ-114-02 OMS44 Motor Record Support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. 3.16.3 REQ-114-01 Initialize                  |
+==================+==================================================+
| Procedure        | 1. After the board successfully initializes, set |
|                  |       the EPICS_CA_ADDR_LIST to the IOC IP.      |
|                  |       Using the export linux command:            |
|                  |                                                  |
|                  | +-----------------------------------------+      |
|                  | |    > export EPICS_CA_ADDR_LIST <IOC IP> |      |
|                  | +-----------------------------------------+      |
|                  |                                                  |
|                  | 1. Start the GCAL dm-screen on any console       |
|                  |       machine                                    |
|                  |                                                  |
|                  | +-----------+                                    |
|                  | |    > gcal |                                    |
|                  | +-----------+                                    |
|                  |                                                  |
|                  | 1. Open the Filter Device Control dm screen.     |
|                  |       Click on the Engineering screen button and |
|                  |       select Filter Device Control option menu.  |
|                  |       Use the leftmost mouse button              |
|                  |                                                  |
|                  | .                                                |
|                  |                                                  |
|                  | 1. Select the INIT command by clicking the INIT  |
|                  |       button in the MODE menu. Then, click the   |
|                  |       GO button                                  |
|                  |                                                  |
|                  | 1. Select the INDEX command by clicking the      |
|                  |       INDEX button in the MODE menu. Then, click |
|                  |       the GO button                              |
|                  |                                                  |
|                  |    a. Close switch, then open                    |
|                  |                                                  |
|                  |    b. Repeat to complete index                   |
|                  |                                                  |
|                  | 1. Select the MOVE command by clicking the INIT  |
|                  |       button in the MODE menu. Enter a position  |
|                  |       in the target position field. Then, click  |
|                  |       the GO button.                             |
|                  |                                                  |
|                  | 1. Select the PARK command by clicking the PARK  |
|                  |       button in the MODE menu. Then, click the   |
|                  |       GO button                                  |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The mechanism initializes without any         |
|                  |       problem.                                   |
|                  |                                                  |
|                  | 2. The mechanism indexes triggered by the switch |
|                  |                                                  |
|                  | 3. During index, move and park, the oscilloscope |
|                  |       should display the pulse signal outputed   |
|                  |       by the OMS card                            |
+------------------+--------------------------------------------------+

.. _section-13:

.. _section-14:

3.17 IPAC/tip810
----------------

3.17.1 REQ-115-01
~~~~~~~~~~~~~~~~~

3.17.2 REQ-115-02
~~~~~~~~~~~~~~~~~

.. _drvserial-1:

3.18 drvSerial
--------------

.. _test-setup-and-prerequisites-14:

3.18.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Set up a VME crate running EPICS. Insert a loopback plug into the second
serial port of the IOC .

.. _epics-test-harness-14:

3.18.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The drvSerial driver does not need a database to run the tests. The test
programs will be loaded on a VME crate running EPICS and executed from
the EPICS shell.

3.18.2.1 Add the C code
^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to, create a test ioc in the ADE environment, Eg.,

+--------------------------------------------+
|    $gem-start-new-module.py -i gemtest1/CP |
+--------------------------------------------+

1. The drvSerial module provides driver and device support for the OMS
      VME44 card. Edit your configure/RELEASE to include the drvSerial
      module from either WORK or PROD:

+-------------------------------------------------------------+
|    SERIAL = /gem_sw/prod/R3.14.12.4/support/drvSerial/2-0-4 |
+-------------------------------------------------------------+

3.18.2.2 Add the drvSerial and drvSerialTest DBD files and libraries
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and libraries in your
gemtest1-CPApp/src/Makefile:

+--------------------------------------+
| gemtest1-CP_DBD += drvSerial.dbd     |
|                                      |
| gemtest1-CP_DBD += drvSerialTest.dbd |
|                                      |
| ...                                  |
|                                      |
| gemtest1-CP_LIBS += drvSerial        |
|                                      |
| gemtest1-CP_LIBS += drvSerialTest    |
+--------------------------------------+

.. _startup-script-13:

3.18.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-CP |
|                                                   |
| cd ${INSTALL}                                     |
|                                                   |
| zoneset("UTC0")                                   |
|                                                   |
| epicsEnvSet("ENGINEER","<YOUR NAME>")             |
|                                                   |
| epicsEnvSet("LOCATION","HBF")                     |
|                                                   |
| ## Register all support components                |
|                                                   |
| gemtest1_CP_registerRecordDeviceDriver(pdbbase)   |
|                                                   |
| iocInit                                           |
+---------------------------------------------------+

.. _build-and-install-7:

3.18.2.4 Build and install
^^^^^^^^^^^^^^^^^^^^^^^^^^

Build the test ioc:

+-------------------------------------+
|    $ cd gemtest1/CP                 |
|                                     |
|    $ make distclean uninstall; make |
+-------------------------------------+

3.18.3 TST-116-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are setup  |
|                  |       as described in the CPU Board Setup.       |
|                  |                                                  |
|                  | 2. Ensure that your serial port is setup as      |
|                  |       described in Test Setup and Prerequisites. |
|                  |                                                  |
|                  | 3. Prepare your test IOC for the drvSerial Tests |
|                  |       as described in the EPICS Test Harness.    |
+==================+==================================================+
| Procedure        | 1. Boot the prepared IOC and confirm the startup |
|                  |       script fully executes.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the drvSerial driver as a |
|                  |       result.                                    |
+------------------+--------------------------------------------------+

.. _section-15:

3.18.4 TST-116-02 drvSerial Attach Link
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. REQ-116-01 Initialize                         |
+==================+==================================================+
| Procedure        | 1. After the IOC has boot successfully, verify   |
|                  |       that there are no errors reported at       |
|                  |       iocInit.                                   |
|                  |                                                  |
|                  | 2. Run the drvSerialTestAttach routine from the  |
|                  |       EPICS shell:                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | prompt> drvSerialTestAttachLink /dev/ttyS1 |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3. Run the drvSerialTestCreateLink routine from  |
|                  |       the EPICS shell                            |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | prompt> drvSerialTestVreateLink /dev/ttyS1 |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 4. Run the drvSerialTestAttachLink routine again |
|                  |       from the EPICS shell.                      |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | prompt> drvSerialTestAttachLink /dev/ttyS1 |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+
| Success Criteria | 1. After step 2 of the test procedure, the       |
|                  |       following message shall be printed on the  |
|                  |       console:                                   |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | drvSerialAttachLink(): No link exists to   |   |
|                  | | which to attach                            |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 2. After step 3 of the test procedure, the       |
|                  |       following message shall be printed on the  |
|                  |       console:                                   |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | drvSerialAttachLink(): attached to link on |   |
|                  | | port /dev/ttyS1                            |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.18.5 TST-116-03 drvSerial Create Link
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `TST-116-02 drvSerial Attach                  |
|                  |                                                  |
|                  |      Link <#tst-116-02-drvserial-attach-link>`__ |
+==================+==================================================+
| Procedure        | 1. (None)                                        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Successful completion of TEST-116-02          |
+------------------+--------------------------------------------------+

3.18.6 TST-116-04 Send Message
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `TST-116-02                                   |
|                  |       drvSerialA                                 |
|                  | ttachLink <#tst-116-02-drvserial-attach-link>`__ |
+==================+==================================================+
| Procedure        | 1. Run the drvSerialTestSend routine from the    |
|                  |       IPICS shell:                               |
|                  |                                                  |
|                  | +---------------------------+                    |
|                  | | prompt> drvSerialTestSend |                    |
|                  | +---------------------------+                    |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the following messages are        |
|                  |       printed on the console:                    |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | drvSerialTestSend(): sizeof(req)=268,      |   |
|                  | | sizeof(req.buf)=256                        |   |
|                  | |                                            |   |
|                  | | ----                                       |   |
|                  | |                                            |   |
|                  | | drvSerialTestPrintBuffer (serialTestSend)  |   |
|                  | |                                            |   |
|                  | | | [A print out of the random buffer data,  |   |
|                  | |   with lines like:                         |   |
|                  | | | [87] [f3] [ :] [ +] [06] [da] [99] [dd]  |   |
|                  | |   …                                        |   |
|                  | | | …                                        |   |
|                  | |                                            |   |
|                  | | … [f2] [ p] [b2] [97] [ J] [da]            |   |
|                  | |                                            |   |
|                  | | ]                                          |   |
|                  | |                                            |   |
|                  | | 172.16.2.23> sendRequest: nwritten=255     |   |
|                  | |                                            |   |
|                  | | drvSerialTestParser() called, bufCount =   |   |
|                  | | 255                                        |   |
|                  | |                                            |   |
|                  | | 172.16.2.23> sendRequest: nwritten=255     |   |
|                  | |                                            |   |
|                  | | drvSerialTestParser() called, bufCount =   |   |
|                  | | 255                                        |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.18.7 TST-116-05 Receive Response
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `TST-116-04 Send Message <#_itgb3wi5s82f>`__  |
+==================+==================================================+
| Procedure        | 1. Run the drvSerialTestReceive routine from the |
|                  |       EPICS shell                                |
|                  |                                                  |
|                  | +---------------------------------+              |
|                  | |    prompt> drvSerialTestReceive |              |
|                  | +---------------------------------+              |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify the following messages are printed on  |
|                  |       the console:                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | ----                                       |   |
|                  | |                                            |   |
|                  | | drvSerialTestPrintBuffer                   |   |
|                  | | (serialTestReceive)                        |   |
|                  | |                                            |   |
|                  | | | [A print out of the random buffer data,  |   |
|                  | |   with lines like:                         |   |
|                  | | | [87] [f3] [ :] [ +] [06] [da] [99] [dd]  |   |
|                  | |   …                                        |   |
|                  | | | …                                        |   |
|                  | |                                            |   |
|                  | | … [f2] [ p] [b2] [97] [ J] [da]            |   |
|                  | |                                            |   |
|                  | | ]                                          |   |
|                  | |                                            |   |
|                  | | bufCount=255                               |   |
|                  | |                                            |   |
|                  | | Buffers agree                              |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

3.18.8 TST-116-06 drvSerial Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `TST-116-05 Receive                           |
|                  |       Response <#tst-116-05-receive-response>`__ |
+==================+==================================================+
| Procedure        | 1. Run the i/o report routine on the IOC         |
|                  |       console:                                   |
|                  |                                                  |
|                  | +----------------------------+                   |
|                  | | prompt> dbior drvSerial 10 |                   |
|                  | +----------------------------+                   |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify the following lines are printed on the |
|                  |       IOC console:                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | Driver: drvSerial                          |   |
|                  | |                                            |   |
|                  | | Device Name =/dev/ttyS1                    |   |
|                  | |                                            |   |
|                  | | Request Queue                              |   |
|                  | |                                            |   |
|                  | | Pri=0 Pending=0 Reserved=0                 |   |
|                  | |                                            |   |
|                  | | Pri=1 Pending=0 Reserved=0                 |   |
|                  | |                                            |   |
|                  | | Pri=2 Pending=0 Reserved=0                 |   |
|                  | |                                            |   |
|                  | | Response Q cnt=0                           |   |
|                  | |                                            |   |
|                  | | mutexSem epicsMutexId 0x2d9c48 source      |   |
|                  | | ../drvSerial.c line 577                    |   |
|                  | |                                            |   |
|                  | | 1a010b97 Not Held                          |   |
|                  | |                                            |   |
|                  | | writeQueueSem 1a010b96 Held by:0a01002c    |   |
|                  | | (SerialWT) Nest count:1                    |   |
|                  | |                                            |   |
|                  | | readQueueSem 1a010b95 Held by:0a010001     |   |
|                  | | (_main_) Nest count:1                      |   |
|                  | |                                            |   |
|                  | | Bucket entries in use = 1 bytes in use =   |   |
|                  | | 1060                                       |   |
|                  | |                                            |   |
|                  | | Bucket entries/hash id - mean = 0.003906   |   |
|                  | | std dev = 0.062378 max = 1                 |   |
|                  | +--------------------------------------------+   |
+------------------+--------------------------------------------------+

.. _section-16:

.. _abdf1-1:

3.19 AbDf1
----------

.. _test-setup-and-prerequisites-15:

3.19.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This test requires an Allen-Bradley PLC-5 Programmable Logic Controllers
(PLC) up and running. This PLC communicates using an asynchronous
byte-oriented protocol that is used to communicate with most Allen
Bradley RS232 interface modules, called DF1. It works over half duplex
and full duplex modes of communication. The AbDf1 driver implements the
DF1 protocol and uses drvSerial to talk to serial lines.

.. _epics-test-harness-15:

3.19.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The Allen-Bradley driver is equipped with a set of EPICS records which
can be loaded during startup and used for testing. The database can be
viewed in labvme-cp-iocApp/Db/abdf1test.sch. Open in tdct like this:

+-------------------------------------------------------------+
| [labvme-cp-iocApp/Db]>tdct -cfg ./tdct.cfg -i abdf1test.sch |
+-------------------------------------------------------------+

3.19.2.1 Add the abdf1test.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to create a test ioc in the ADE environment, Eg.

+------------------------------------------+
|    $gem-start-new-module.py -i labvme/cp |
+------------------------------------------+

2. Edit your configure/RELEASE to include the Allen-Bradley module from
      either WORK or PROD:

+------------------------------------------------------+
|    ABDF1=/gem_sw/prod/R3.14.12.4/support/AbDf1/2-0-1 |
|                                                      |
|    DRVSERIAL=$(PRODSUP)/drvSerial/2-0-5              |
+------------------------------------------------------+

3. Find your labvme-cp-iocApp/Db/ and edit your Makefile to include the
      Allen-Bradley test database:

+-----------------------+
|    DB += AbDf1test.db |
+-----------------------+

3.19.2.2 Add the AbDf1 DBD file and Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and library in your
labvme-cp-iocApp/src/Makefile:

+------------------------------------+
| labvme-cp-ioc_DBD += drvSerial.dbd |
|                                    |
| labvme-cp-ioc_DBD += AbDf1.dbd     |
|                                    |
| …                                  |
|                                    |
| labvme-cp-ioc_LIB += drvSerial     |
|                                    |
| labvme-cp-ioc_LIB += AbDf1         |
+------------------------------------+

.. _section-17:

.. _startup-script-14:

3.19.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+------------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/labvme-cp-ioc  |
|                                                      |
| cd ${INSTALL}                                        |
|                                                      |
| zoneset("UTC0")                                      |
|                                                      |
| epicsEnvSet("ENGINEER","<YOUR NAME>")                |
|                                                      |
| epicsEnvSet("LOCATION","CP")                         |
|                                                      |
| ## Register all support components                   |
|                                                      |
| dbLoadDatabase("dbd/labvme-cp-ioc.dbd")              |
|                                                      |
| labvme_cp_ioc_registerRecordDeviceDriver(pdbbase)    |
|                                                      |
| …                                                    |
|                                                      |
| ## Load record instances                             |
|                                                      |
| dbLoadRecords("db/abdf1test.db", "user=<your name>") |
|                                                      |
| var drvAbDf1SrcStationNumber 11                      |
|                                                      |
| var drvAbDf1Debug 2                                  |
|                                                      |
| iocInit                                              |
+------------------------------------------------------+

.. _req-117-01-initialize-1:

3.19.3 REQ-117-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your Allen-Bradley is set up as   |
|                  |       described in `3.19.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-15>`__ |
|                  |                                                  |
|                  | 2. Prepare your test IOC for the Allen-Bradley   |
|                  |       tests as described in `3.19.2 EPICS Test   |
|                  |       Harness <#epics-test-harness-15>`__.       |
+==================+==================================================+
| Procedure        | 1. Boot the prepared IOC and confirm the startup |
|                  |       script fully executes.                     |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the Allen-Bradley Driver  |
|                  |       and the serial driver as a result.         |
+------------------+--------------------------------------------------+

.. _req-117-02-epics-report-1:

3.19.4 REQ 117-02 EPICS Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.19.3                                       |
|                  |       Initialize <#req-117-01-initialize-1>`__   |
+==================+==================================================+
| Procedure        | 1. After the board successfully initializes, run |
|                  |       the AbDf1 driver report:                   |
|                  |                                                  |
|                  | +-----------------------------------+            |
|                  | |    labvme> dbior drvAbDf1Report 1 |            |
|                  | +-----------------------------------+            |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The console will report the number of cards   |
|                  |       configured and the state of the driver     |
|                  |                                                  |
|                  | +-------------------------+                      |
|                  | | Add output from command |                      |
|                  | +-------------------------+                      |
+------------------+--------------------------------------------------+

3.19.5 REQ-117-03 Analog Input Record Support for AB DF1 Serial (SCAN I/O Intr)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your Allen-Bradley is set up as   |
|                  |       described in `3.19.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-15>`__ |
|                  |                                                  |
|                  | 2. Prepare your test IOC for the Allen-Bradley   |
|                  |       tests as described in `3.19.2 EPICS Test   |
|                  |       Harness <#epics-test-harness-15>`__.       |
|                  |                                                  |
|                  | 3. Open a Linux host terminal                    |
+==================+==================================================+
| Procedure        | 1. In the Linux host terminal, run camonitor on  |
|                  |       the Analog Input record:                   |
|                  |                                                  |
|                  | +--------------------+                           |
|                  | | $ camonitor plc:ai |                           |
|                  | +--------------------+                           |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the camonitor is showing the      |
|                  |       value of plc:ai is being incremented every |
|                  |       half second.                               |
+------------------+--------------------------------------------------+

.. _req-117-04-analog-output-record-support-for-ab-df1-serial-1:

3.19.6 REQ-117-04 Analog Output Record Support for AB DF1 Serial
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your Allen-Bradley is set up as   |
|                  |       described in `3.19.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-15>`__ |
|                  |                                                  |
|                  | 2. Prepare your test IOC for the Allen-Bradley   |
|                  |       tests as described in `3.19.2 EPICS Test   |
|                  |       Harness <#epics-test-harness-15>`__.       |
|                  |                                                  |
|                  | 3. Open a Linux host terminal                    |
+==================+==================================================+
| Procedure        | 1. In the Linux host terminal, run camonitor on  |
|                  |       the Analog Input record:                   |
|                  |                                                  |
|                  | +-----------------------+                        |
|                  | |    $ camonitor plc:ao |                        |
|                  | +-----------------------+                        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the camonitor is showing the      |
|                  |       value of plc:ao is being incremented every |
|                  |       half second.                               |
+------------------+--------------------------------------------------+

3.19.7 REQ-117-05 Binary Output Record Support for AB DF1 Serial(SCAN I/O Intr)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your Allen-Bradley is set up as   |
|                  |       described in `3.19.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-15>`__ |
|                  |                                                  |
|                  | 2. Prepare your test IOC for the Allen-Bradley   |
|                  |       tests as described in `3.19.2 EPICS Test   |
|                  |       Harness <#epics-test-harness-15>`__.       |
|                  |                                                  |
|                  | 3. Open a Linux host terminal                    |
+==================+==================================================+
| Procedure        | 1. In the Linux host terminal, populate the      |
|                  |       output record using the caput command:     |
|                  |                                                  |
|                  | +---------------------+                          |
|                  | |    $ caput plc:bo 0 |                          |
|                  | +---------------------+                          |
|                  |                                                  |
|                  | 2. run camonitor on the binary Input record:     |
|                  |                                                  |
|                  | +-----------------------+                        |
|                  | |    $ camonitor plc:bi |                        |
|                  | |                       |                        |
|                  | |    camonitor plc:bi 0 |                        |
|                  | +-----------------------+                        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the binary input (plc:bi) channel |
|                  |       is set to 0                                |
+------------------+--------------------------------------------------+

3.19.8 REQ-117-06 Binary Input Record Support for AB DF1 Serial
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your Allen-Bradley is set up as   |
|                  |       described in `3.19.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-15>`__ |
|                  |                                                  |
|                  | 2. Prepare your test IOC for the Allen-Bradley   |
|                  |       tests as described in `3.19.2 EPICS Test   |
|                  |       Harness <#epics-test-harness-15>`__.       |
|                  |                                                  |
|                  | 3. Open a Linux host terminal                    |
+==================+==================================================+
| Procedure        | 1. In the Linux host terminal, run camonitor on  |
|                  |       the binary Input record:                   |
|                  |                                                  |
|                  | +-----------------------+                        |
|                  | |    $ camonitor plc:bi |                        |
|                  | +-----------------------+                        |
|                  |                                                  |
|                  | 2. Populate the output record using the caput    |
|                  |       command:                                   |
|                  |                                                  |
|                  | +---------------------+                          |
|                  | |    $ caput plc:bo 1 |                          |
|                  | +---------------------+                          |
+------------------+--------------------------------------------------+
| Success Criteria | 1. Verify that the binary input (plc:bi) channel |
|                  |       is set to 1                                |
+------------------+--------------------------------------------------+

3.19.9 REQ-117-07 Two PLC Support 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your Allen-Bradley is set up as   |
|                  |       described in `3.19.1 Test Setup and        |
|                  |       Prer                                       |
|                  | equisites. <#test-setup-and-prerequisites-15>`__ |
|                  |                                                  |
|                  | 2. Prepare your test IOC for the Allen-Bradley   |
|                  |       tests as described in `3.19.2 EPICS Test   |
|                  |       Harness <#epics-test-harness-15>`__.       |
+==================+==================================================+
| Procedure        |                                                  |
+------------------+--------------------------------------------------+
| Success Criteria |                                                  |
+------------------+--------------------------------------------------+

.. _section-18:

.. _section-19:

.. _tcslib-1:

**3.20 tcslib**
---------------

.. _test-setup-and-prerequisites-16:

3.20.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _epics-test-harness-16:

3.20.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

This module will be tested using the Gemini Records Test Harness
Database.

3.20.2.1 Add the …...db
^^^^^^^^^^^^^^^^^^^^^^^

+---+
|   |
+---+

.. _add-the-libraries-3:

3.20.2.2 Add the libraries
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------+
| gemtest1-CP_DBD += tcslib.dbd |
|                               |
| ...                           |
|                               |
| gemtest1-CP_LIBS += tcslib    |
+-------------------------------+

.. _startup-script-15:

3.20.2.3 Startup Script
^^^^^^^^^^^^^^^^^^^^^^^

+---------------------------------------------------+
| $(LINUX_ONLY)#!${INSTALL}/bin/${ARCH}/gemtest1-MK |
|                                                   |
| cd ${INSTALL}                                     |
|                                                   |
| zoneset("UTC0")                                   |
|                                                   |
| epicsEnvSet("ENGINEER","<YOUR NAME>")             |
|                                                   |
| epicsEnvSet("LOCATION","HBF")                     |
|                                                   |
| ## Register all support components                |
|                                                   |
| dbLoadDatabase("dbd/gemtest1-MK.dbd")             |
|                                                   |
| gemtest1_MK_registerRecordDeviceDriver(pdbbase)   |
|                                                   |
| ...                                               |
|                                                   |
| iocInit                                           |
+---------------------------------------------------+

3.20.3 REQ-118-01 ADE Compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    |                                                  |
+==================+==================================================+
| Procedure        | cd ~/work//support/tcslib                        |
|                  |                                                  |
|                  | \* Clean to start from fresh                     |
|                  |                                                  |
|                  | make distclean                                   |
|                  |                                                  |
|                  | 1. Make sure the configuration is fine           |
|                  | (configure/RELEASE)                              |
|                  |                                                  |
|                  | cd configure                                     |
|                  |                                                  |
|                  | cat RELEASE                                      |
|                  |                                                  |
|                  | Should report the following:                     |
|                  |                                                  |
|                  | SLA                                              |
|                  | LIB=/gem_sw/prod/R3.14.12.4/support/slalib/1-9-5 |
|                  |                                                  |
|                  | AST                                              |
|                  | LIB=/gem_sw/prod/R3.14.12.4/support/astlib/1-6-2 |
|                  |                                                  |
|                  | GEMREC                                           |
|                  | =/gem_sw/prod/R3.14.12.4/support/geminiRec/4-0-3 |
|                  |                                                  |
|                  | ...                                              |
|                  |                                                  |
|                  | EPICS_BASE=/gem_sw/epics/R3.14.12.4/base         |
|                  |                                                  |
|                  | ...                                              |
|                  |                                                  |
|                  | 2. Build tcslib                                  |
|                  |                                                  |
|                  | cd ..                                            |
|                  |                                                  |
|                  | make                                             |
+------------------+--------------------------------------------------+
| Success Criteria | The tcslib library shall be compatible with the  |
|                  | Application Development Environment (ADE ), and  |
|                  | build without errors or warnings.                |
+------------------+--------------------------------------------------+

3.20.4 REQ-118-02 ADE Available for Linking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    |                                                  |
+==================+==================================================+
| Procedure        | 1.Switch to an IOC directory                     |
|                  |                                                  |
|                  | For example:                                     |
|                  |                                                  |
|                  | cd ~/work/ioc/sbf/                               |
|                  |                                                  |
|                  | 2. Check that the RELEASE loads all the          |
|                  | libraries (including tcslib). You'll need to     |
|                  | specify the library versions if they are in      |
|                  | production.                                      |
|                  |                                                  |
|                  | cd configure                                     |
|                  |                                                  |
|                  | cat RELEASE                                      |
|                  |                                                  |
|                  | ...                                              |
|                  |                                                  |
|                  | SLALIB=$(PRODSUP)/slalib/1-9-5                   |
|                  |                                                  |
|                  | ASTLIB=$(PRODSUP)/astlib/1-6-2                   |
|                  |                                                  |
|                  | GEMREC=$(PRODSUP)/geminiRec/4-0-3                |
|                  |                                                  |
|                  | TCSLIB=$(PRODSUP)/tcslib/1-0-1                   |
|                  |                                                  |
|                  | ...                                              |
|                  |                                                  |
|                  | 3. Switch to the src directory and make sure the |
|                  | library is included in the makefile              |
|                  |                                                  |
|                  | cd ../labvme3-sbf-iocApp/src                     |
|                  |                                                  |
|                  | cat Makefile                                     |
|                  |                                                  |
|                  | ...                                              |
|                  |                                                  |
|                  | labvme3-sbf-ioc_DBD += tcslib.dbd                |
|                  |                                                  |
|                  | ...                                              |
|                  |                                                  |
|                  | 4. Recompile                                     |
|                  |                                                  |
|                  | cd ~/work/ioc/sbf                                |
|                  |                                                  |
|                  | make distclean                                   |
|                  |                                                  |
|                  | make                                             |
+------------------+--------------------------------------------------+
| Success Criteria | The tcslib library shall be available for        |
|                  | linking and testing with other CCB components.   |
+------------------+--------------------------------------------------+

3.21 VMI5588 Refelective Memory
-------------------------------

.. _test-setup-and-prerequisites-17:

3.21.1 Test Setup and Prerequisites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Setup your test Vmi5588 with the proper jumper and switch settings to
define the base address and memory

/\* default base addr, mem size, interrupt vector and level \*/

#define RM_VME_BASE 0xA00000 /\* A24 Address space \*/

#define RM_VME_SIZE 0x040000 /\* 256 Kbytes long \*/

#define RM_INT_VECTOR 0xb0 /\* 4 vectors used, b0 to b3 \*/

#define RM_INT_LEVEL 6 /\* interrupts are urgent \*/

1. DESCRIBE JUMPERS AND SWITCHES HERE

2. POINT OUT DIFFERENCES FOR DMA and NON-DMA BOARDS

.. _epics-test-harness-17:

3.21.2 EPICS Test Harness
~~~~~~~~~~~~~~~~~~~~~~~~~

The Vmi5588 driver is equipped with a set of epics records which can be
loaded during startup. The database can be viewed in
vmi5588App/Db/vmi5588Test.sch. Make sure your SYS_TDCT_PATHS is defined
like this,

+----------------------------------------------------------------------+
| [vmi5588App/Db]>echo $SYS_TDCT_PATHS                                 |
|                                                                      |
| /gem_sw/epics/R3.14.12.4/exte                                        |
| nsions/src/tdct/sym:/gem_sw/prod/R3.14.12.4/support/geminiRec/4-0/db |
+----------------------------------------------------------------------+

and use the tdct like this:

+---------------------------------------------------------+
| [vmi5588App/Db]>tdct -cfg ./tdct.cfg -i vmi5588Test.sch |
+---------------------------------------------------------+

The test harness records to verify are the reflective memory status
registers defined in the

============== =========== ====== ========
Name           Description Signal SCAN
============== =========== ====== ========
vmi5588:ring                      1 second
vmi5588:txfifo                    1 second
vmi5588:input                     1 second
vmi5588:pll                       1 second
vmi5588:int3                      1 second
vmi5588:int2                      1 second
vmi5588:int1                      1 second
vmi5588:sync                      1 second
vmi5588:crc                       1 second
vmi5588:rxfifo                    1 second
============== =========== ====== ========

3.21.2.1 Add the vmi5588Test.db
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. If you need to, create a test ioc in the ADE environment, Eg.,

+-------------------------------------------+
| $gem-start-new-module.p￼￼y -i gemtest1/MK |
+-------------------------------------------+

2. The XYCOM module provides drivers and device support for both the 240
      and 566. Edit your configure/RELEASE to include the XYCOM module
      from either WORK or PROD:

+-------------------------------------------------+
| ...                                             |
|                                                 |
| VMI5588=/gem_sw/work/R3.14.12.4/support/vmi5588 |
+-------------------------------------------------+

3. Find your gemtest1-MKApp/Db/Makefile and edit your Makefile to
      include the test harness as described in `3.2.1 EPICS Test
      Harness <#epics-test-harness-10>`__.

+----------------------+
| DB += vmi5588Test.db |
+----------------------+

3.21.2.2 Add the Vmi5588 DBD file and library 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Include the following dbd and libraries in your
gemtest1-MKApp/src/Makefile:

+---------------------------------+
| gemtest1-MK_DBD += vmi5588.dbd  |
|                                 |
| gemtest1-MK_DBD += pingpong.dbd |
|                                 |
| …                               |
|                                 |
| gemtest1-MK_LIBS += vmi5588     |
|                                 |
| gemtest1-MK_LIBS += pingpong    |
+---------------------------------+

3.21.2.3 tartup Script
^^^^^^^^^^^^^^^^^^^^^^

+----------------------------------------------------------------------+
| 1. The IOC must show no errors on startup.                           |
|                                                                      |
| 2. The EPICS iocInit() will be running and will have initialized the |
|       Xycom-566 driver as a result.                                  |
|                                                                      |
| 3. The driver configures all the boards’ internal registers during   |
|       the INIT process, and as a final step sets the front-Panel LED |
|       from RED to GREEN upon successful initialization.              |
+----------------------------------------------------------------------+

.. _build-and-install-8:

3.21.2.4 Build and install
^^^^^^^^^^^^^^^^^^^^^^^^^^

Build the test ioc:

+----------------------------------+
| $ cd gemtest1-MK/hbf             |
|                                  |
| $ make distclean uninstall; make |
+----------------------------------+

3.21.3 REQ-119-01 Initialize
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. Ensure that your IOC and CPU board are setup  |
|                  |       as described in `3.2 CPU Board             |
|                  |       Setup <#cpu-board-setup>`__.               |
|                  |                                                  |
|                  | 2. For this series of tests you will need either |
|                  |       two VME crates or 1 VME crate and 1 CEM.   |
|                  |       Each physical system must be configured    |
|                  |       with a Vmi5588.                            |
|                  |                                                  |
|                  | 3. Configure the board ID’s for the Vmi5588 to   |
|                  |       be VME_0=0, VME_1=1, and CEM=1. In this    |
|                  |       configuration you can test VME_0 as the    |
|                  |       initiator and VME_1 or the CEM as the      |
|                  |       receiver of the Ping Pong tests in below.  |
|                  |                                                  |
|                  | 4. Connect the fiber appropriately between       |
|                  |       systems.                                   |
+==================+==================================================+
| Procedure        | 1. Boot **both** prepared systems and confirm    |
|                  |       the startup scripts fully executes.        |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The IOC must show no errors on startup.       |
|                  |                                                  |
|                  | 2. The EPICS iocInit() will be running and will  |
|                  |       have initialized the Xycom-566 driver as a |
|                  |       result.                                    |
|                  |                                                  |
|                  | 3. The driver configures all the boards’         |
|                  |       internal registers during the INIT         |
|                  |       process, and as a final step sets the      |
|                  |       front-Panel LED from RED to GREEN upon     |
|                  |       successful initialization.                 |
+------------------+--------------------------------------------------+

3.21.4 REQ-119-02 EPICS Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------------+--------------------------------------------------+
| Prerequisites    | 1. `3.21.3 REQ-119-01                            |
|                  |       Initialize <#req-119-01-initialize>`__     |
+==================+==================================================+
| Procedure        | 1. After the board successfully initializes, run |
|                  |       the report for the Xycom 566.              |
|                  |                                                  |
|                  | +--------------------------------+               |
|                  | | 10.1.2.171> dbior drvVmi5588 2 |               |
|                  | +--------------------------------+               |
+------------------+--------------------------------------------------+
| Success Criteria | 1. The console will report the full status of    |
|                  |       the board.                                 |
|                  |                                                  |
|                  | 2.                                               |
|                  |                                                  |
|                  | +--------------------------------------------+   |
|                  | | 10.1.2.172> dbior drvVmi5588 2             |   |
|                  | | Driver: drvVmi5588                         |   |
|                  | | vmi5588: RM node 0x00, status 0x40, max 0  |   |
|                  | | retries                                    |   |
|                  | | pad1:0xff                                  |   |
|                  | | BID:0x4b                                   |   |
|                  | | IRS:0x40                                   |   |
|                  | | pad2:0xff                                  |   |
|                  | | NID:0x00                                   |   |
|                  | | CSR:0x65                                   |   |
|                  | | CMD:0xff01                                 |   |
|                  | | pad3:0x0xdfa00008                          |   |
|                  | | interrupt control registers at 0xdfa00020  |   |
|                  | | vector registers at 0xdfa00030             |   |
|                  | | test address = 0xdfa00040                  |   |
|                  | | pad6 address = 0xdfa00044                  |   |
|                  | | page flags at 0xdfa00100                   |   |
|                  | | pad7 address = 0xdfa002fe                  |   |
|                  | | pad8 address = 0xdfa00300                  |   |
|                  | | mem starts at 0xdfa00400                   |   |
|                  | | Receiver: Input signal good, PLL Locked,   |   |
|                  | | Recent Sync loss                           |   |
|                  | | Jumpers: Fast mode, Transfer Error         |   |
|                  | | Interrupt Enabled                          |   |
|                  | | Status: Fibre ring Intact, Fail LED Off    |   |
|                  | | FIFOs: Transmitter Empty, Receiver <50%    |   |
|                  | | Full                                       |   |
|                  | | Int's: (irs=0x40)                          |   |
|                  | | Int 00: Not in use, Level 0 disabled,      |   |
|                  | | vector 0xb0 (icr=0x00); routine = 0x0      |   |
|                  | | Int 01: Not in use, Level 0 disabled,      |   |
|                  | | vector 0xb1 (icr=0x00); routine = 0x0      |   |
|                  | | Int 02: Not in use, Level 6 enabled, Auto  |   |
|                  | | clear, vector 0xb2 (icr=0x1e); routine =   |   |
|                  | | 0x0                                        |   |
|                  | | Int 03: Not in use, Level 0 disabled,      |   |
|                  | | vector 0xb3 (icr=0x00); routine = 0x0      |   |
|                  | +--------------------------------------------+   |
|                  |                                                  |
|                  | 3. The interrupt “Int 02” is enabled and         |
|                  |       icr=0x1e on this IOC. This can be manually |
|                  |       set by running the command “prep (2)”. Run |
|                  |       that command and check the report again.   |
|                  |                                                  |
|                  | 4. Ensure both physical systems are setup,       |
|                  |       initialized and show a successful report.  |
+------------------+--------------------------------------------------+

.. _req-119-03-binary-input-device-support-1:

3.21.5 REQ-119-03 Binary Input Device Support 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================ ==================
Prerequisites    1. Run the Vmi5588
================ ==================
Procedure        
Success Criteria 
================ ==================

.. _req-119-04-reflected-memory-status-1:

3.21.6 REQ-119-04 Reflected Memory Status
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _req-119-05-vme-node-to-pci-msdos-node-data-integrity-1:

3.21.7 REQ-119-05 VME Node to PCI (MSDOS) Node Data Integrity 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. _req-119-06-vme-node-to-vme-node-data-integrity-1:

3.21.8 REQ-119-06 VME Node to VME Node Data Integrity
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================
Prerequisites    
================
Procedure        
Success Criteria 
================

.. |ioc_stats_rtems.jpg| image:: ./media/media/image1.jpg
   :width: 3.35417in
   :height: 5.74449in
.. |ioc_stats.png| image:: ./media/media/image6.png
   :width: 4.61831in
   :height: 6.57639in
.. |SequencerDemo.png| image:: ./media/media/image3.png
   :width: 4.61979in
   :height: 4.09208in
.. |GeminiRecordsTest.png| image:: ./media/media/image2.png
   :width: 6.5in
   :height: 3.31944in
.. |pmacStatusRecMonitor.png| image:: ./media/media/image4.png
   :width: 4.38021in
   :height: 1.72917in
.. |oms44_testbench.png| image:: ./media/media/image5.png
   :width: 6.34375in
   :height: 4.65278in
