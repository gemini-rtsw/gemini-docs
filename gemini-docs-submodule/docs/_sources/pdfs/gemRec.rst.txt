Created:June 25, 1996 Modified:February 3, 1997
===============================================

+--------------+
| **Gemini**   |
|              |
| **Controls** |
|              |
| **Group**    |
|              |
| **Report**   |
+--------------+

..

   Gemini Record Reference Manual

Bret Goodrich and Andy Foster
=============================

   SPE-C-G0070/02

   **This document decribes the EPICS records created by the Gemini
   Project.**

 1.0 Introduction....................................................................................3
=====================================================================================================

1.1 Purpose
..............................................................................................................3

1.2
Scope..................................................................................................................3

   1.3
   References..........................................................................................................3
   1.4
   Revisions............................................................................................................3

 2.0 apply - Apply Record.....................................................................4
==============================================================================================

   2.1 Field
   Summary...................................................................................................4
   2.2 Field
   Descriptions..............................................................................................5

2.3 Record Support
Routines...................................................................................5

2.4 Record Processing
.............................................................................................6

   2.5 Device Support
   ..................................................................................................6
   2.6 CapFast
   ..............................................................................................................6

 3.0 CAD - Command Action Directive Record .................................7
============================================================================

   3.1 Field
   Summary...................................................................................................8
   3.2 Field
   Descriptions..............................................................................................9

3.3 Record Support
Routines.................................................................................10

3.4 Record Processing
...........................................................................................11

   3.5 Device Support
   ................................................................................................12
   3.6 CapFast
   ............................................................................................................12

 4.0 CAR - Command Action Response Record...............................13
=========================================================================

   4.1 Field
   Summary.................................................................................................14
   4.2 Field
   Descriptions............................................................................................14

4.3 Record Support
Routines.................................................................................15

4.4 Record Processing
...........................................................................................16

Gemini Record Reference Manual SPE-C-G0070/02 1 of 49 Table Of Contents
-----------------------------------------------------------------------

   4.5 Device
   Support.................................................................................................16
   4.6 CapFast
   ............................................................................................................16

 5.0 SIR - Status Information Record................................................17
=====================================================================================

   5.1 Field
   Summary.................................................................................................18
   5.2 Field
   Descriptions............................................................................................19

5.3 Record Support
Routines.................................................................................20

5.4 Record
Processing............................................................................................21

   5.5 Device
   Support.................................................................................................21
   5.6 CapFast
   ............................................................................................................21

 6.0 lutout - Lookup Table Output Record .......................................22
=================================================================================

6.1 Field
Summary.................................................................................................23

6.2 Field
Description..............................................................................................23

6.3 Record Support
Routines.................................................................................25

6.4 Record
Processing............................................................................................25

6.5 Device
Support.................................................................................................26

   6.6 CapFast
   ............................................................................................................26
   6.7 Future
   Enhancements.......................................................................................26

 7.0 lutin - Lookup Table Input Record ............................................27
====================================================================================

7.1 Field
Summary.................................................................................................27

7.2 Field
Description..............................................................................................28

7.3 Record Support
Routines.................................................................................29

7.4 Record
Processing............................................................................................30

7.5 Device
Support.................................................................................................30

   7.6 CapFast
   ............................................................................................................30
   7.7 Future
   Enhancements.......................................................................................31

 8.0 mosub - Multiple Output Subroutine Record ...........................32
===========================================================================

8.1
Introduction......................................................................................................32

   8.2 Field
   Summary.................................................................................................33
   8.3 Field
   Descriptions............................................................................................36
   8.4 Record Support
   Routines.................................................................................37

 9.0 GenSub - The General Subroutine Record ...............................39
============================================================================

9.1
Introduction......................................................................................................39

   9.2 Field
   Summary.................................................................................................40
   9.3 Field
   Descriptions............................................................................................44

9.4 Record Support
Routines.................................................................................45

   9.5 Use of the ‘GenSub’
   Record............................................................................47
   9.6 Use of User Defined
   Structures........................................................................47

9.7 Dynamically Changing the User Routine called during Record
Processing ...49

   **Introduction**

1.0 Introduction
----------------

 1.1 Purpose
~~~~~~~~~~~

   This document describes the EPICS records created by the Gemini 8M
   Telescopes Project for use in its telescope and instrument control
   databases.

 1.2 Scope
~~~~~~~~~

This document defines the interface to only those records created by and
for the Gemini Project. For a complete list of the standard EPICS
records, and a description of the field summary tables, refer to the
*EPICS Input Output Controller Record Reference Manual* [1].

 1.3 References
~~~~~~~~~~~~~~

1. *EPICS Input Output Controller Record Reference Manual*, Janet B.
   Anderson and Martin R. Kraimer, Argonne National Laboratory, Dec 1,
   1994.

2. *ICD 1b — The Baseline Attribute/Value Interface* (gscg.grp.024/04),
   Kim Gillies, Steve Wampler, Bret Goodrich.

3. *EPICS Lookup Table Records* (gscg.bdg.003.lut/1), Bret Goodrich.

4. *The ‘mosub’ EPICS Record Reference Manual*, Andy Foster.

5. *The ‘genSub’ EPICS Record Reference Manual*, Andy Foster.

 1.4 Revisions
~~~~~~~~~~~~~

1. Version 01, 8 November, 1996. Document created.

2. Version 02, February 3, 1997, Updated genSub section.

2.0 apply - Apply Record
------------------------

   The apply record executes data links to other records. Its primary
   purpose is to process CAD records in a fixed order, and return the
   results of processing those records. There may be up to eight sets of
   links to other records. The links in each set pass the directive
   field (DIR to OUTA through OUTH) and client ID field (CLID to OCLA
   through OCLH), and receive the result value (INPA through INPH to
   VAL) and error message (INMA through INMH to MESS).

   The apply record accepts the same directives as the CAD, namely:
   MARK, CLEAR, PRESET, START, and STOP. Writing a value to the
   directive field (DIR) starts the processing of the record and
   subsequent processing of all attached records. Writing the START
   directive forces the PRESET directive to be sent to all links before
   the START directive is sent. This insures that all CAD records linked
   to the apply record have valid arguments.

   Values returned through the INPx links are inspected by the apply
   record for non-zero results. If any link returns a non-zero value,
   the associated INMx link is read. The error value and error message
   are copied to the VAL and MESS fields, monitors are posted, and the
   processing halts. No further links are processed once an error has
   been returned.

   The use of the apply record is required for all principal systems
   databases, including the TCS, CICS, and all instruments. There must
   be one, and only one, top level apply record in the database,
   although there may be cascaded apply records. All principal systems
   CAD records must be linked to the apply record and must be processed
   through this record. Links from the apply record outputs may go to
   records other than CAD records, such as calc, sub, or mosub records.

 2.1 Field Summary
~~~~~~~~~~~~~~~~~

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | P     | *PP** |
|       |       |       | ial** |       |       | roc** |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       |       |       | *     |       |
|       |       |       |       |       |       | *Moni |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| VAL   | LONG  | No    | 0     | Yes   | No    | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| DIR   | RECC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| CLID  | LONG  | No    | 0     | Yes   | Yes   | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MESS  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OMSS  | S     | No    | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTx  | OU    | No    | 0     | No    | No    | No    | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OCLx  | OU    | No    | 0     | No    | No    | No    | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPx  | I     | No    | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INMx  | I     | No    | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

 2.2 Field Descriptions
~~~~~~~~~~~~~~~~~~~~~~

+----------+--------------------+------------------------------------+
| **Name** | **Summary**        | **Description**                    |
+==========+====================+====================================+
| VAL      | Value              | This is the return value from the  |
|          |                    | input links. If any link returns a |
|          |                    | non-zero, processing stops and the |
|          |                    | last value is returned.            |
+----------+--------------------+------------------------------------+
| DIR      | Directive          | This value of this field is passed |
|          |                    | to all OUTx output links. If the   |
|          |                    | directive is START, the directive  |
|          |                    | PRESET is first passed to all      |
|          |                    | output links.                      |
+----------+--------------------+------------------------------------+
| CLID     | Client ID          | This number is incremented every   |
|          |                    | time a directive is loaded. The    |
|          |                    | value is passed to all OCLx output |
|          |                    | links.                             |
+----------+--------------------+------------------------------------+
| MESS     | Message            | This is the return message from an |
|          |                    | INMx input link. If the return     |
|          |                    | value is 0, this field is empty.   |
|          |                    | Otherwise, it reads the error      |
|          |                    | message from the INMx link.        |
+----------+--------------------+------------------------------------+
| OMSS     | Old Message        | This is the old message string.    |
+----------+--------------------+------------------------------------+
| OUTx     | Output             | There are eight output links       |
|          |                    | OUTA-OUTH which pass the value of  |
|          | directive link     | the DIR field to a record field.   |
+----------+--------------------+------------------------------------+
| OCLx     | Output client      | There are eight output links       |
|          |                    | OUMA-OUMH which pass the value of  |
|          | ID link            | the CLID field to a record field.  |
+----------+--------------------+------------------------------------+
| INPx     | Input result link  | There are eight input links        |
|          |                    | INPA-INPH which read a value from  |
|          |                    | a record field. A non-zero value   |
|          |                    | halts the processing sequence.     |
+----------+--------------------+------------------------------------+
| INMx     | Input message link | There are eight input links        |
|          |                    | INMA-INMH which read a value from  |
|          |                    | a record field. The link is read   |
|          |                    | only for the corresponding INPx    |
|          |                    | link which returned a non-zero     |
|          |                    | value.                             |
+----------+--------------------+------------------------------------+

 2.3 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

 2.3.1 init_record
^^^^^^^^^^^^^^^^^

   This routine initializes the apply record. All OUTx links are forced
   to be process passive; all OUMx, INPx, and INMx links are forced to
   be non-process passive.

**2.3.2 process**

   See the next section.

**2.3.3 get_value**

   This routine fills the values of struct valueDes so that they refer
   to VAL.

 2.3.4 get_enum_str
^^^^^^^^^^^^^^^^^^

   This routine converts the long integer values 0 through 4 into the
   strings “MARK”, “CLEAR”, PRESET”, “START”, and “STOP”, respectively.

**2.3.5 get_enum_strs**

   This routine returns all five of the above strings.

**2.3.6 put_enum_str**

   This routine converts the above strings into the long integer values
   0 through 4.

 2.4 Record Processing
~~~~~~~~~~~~~~~~~~~~~

   This routine processes the record whenever requested. Processing will
   occur whenever a value is written to the DIR field.

-  All MARK directives are ignored and processing exits.

-  The return message field is cleared.

-  If the directive is START:

..

   **—**\ increment the client ID,

   **—**\ recursively call this procedure with PRESET, **—**\ exit if an
   error occurred during PRESET.

-  for each existing set of links A-H:

..

   **—**\ send CLID and DIR to OCLx and OUTx links,

   **—**\ get VAL from INPx link,

   **—**\ if VAL is non-zero, get MESS from OUMx link and stop looping

-  post monitor on VAL field.

-  if VAL is non-zero and MESS is different than OMSS:

..

   **—**\ post monitor on MESS field.

**2.5 Device Support**

   There is no device support available.

 2.6 CapFast
~~~~~~~~~~~

   There is one CapFast symbol for the Apply record.

**FIGURE 1.** CapFast *eapply* symbol

   .

======= ===== ======
   SLNK apply FLNK
              
           .  
              
           .  
======= ===== ======
   DIR           VAL
   CLID       MESS
   INPA       OUTA
   INMA       OCLA
   INPB       OUTB
   INMB       OCLB
   INPC       OUTC
   INMC       OCLC
   INPD       OUTD
   INMD       OCLD
   INPE       OUTE
   INME       OCLE
   INPF       OUTF
   INMF       OCLF
   INPG       OUTG
   INMG       OCLG
   INPH       OUTH
   INMH       OCLH
\             
======= ===== ======

..

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   .

3.0 CAD - Command Action Directive Record
-----------------------------------------

   The CAD record initiates processing of Gemini commands. Attributes of
   the command are either loaded into fields A through T, or read from
   input links INPA through INPT. Processing begins when a directive is
   received in the DIR field. Subroutine calls may be made during
   initialization and processing. Each directive also has an associated
   link which is processed when the directive is received. A return
   value from the processing subroutine is returned in the VAL field.

   There are five valid directives: MARK, CLEAR, PRESET, START, and
   STOP. The MARK directive forces the CAD state machine into state 1.
   This directive is also executed through special processing if any of
   fields A through T are modified. The CLEAR directive forces the state
   machine into state 0, clearing any prior mark or preset. The PRESET
   directive moves the state machine from state 1 to state 2. The START
   directive either moves the state machine from state 2 to state 0, or
   executes a PRESET directive then moves from state 2 to state 0. In
   all cases except PRESET, START, and STOP in state 0 the processing
   subroutine SNAM is called and the corresponding directive link is
   processed.

   The CAD record can be in one of three states, given by the value of
   the MARK field. In state 0, the CAD record is considered to be
   cleared. In state 1, it is considered to be marked as ready to
   preset. In state 2, it is considered to be preset and ready to
   activate. Figure 1 shows the state diagram for the CAD record, with
   the edges identified as possible directive commands.

   (2) PRESET call and link processed first.

.. _field-summary-1:

3.1 Field Summary
~~~~~~~~~~~~~~~~~

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| VAL   | LONG  | No    | 0     | Yes   | No    | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SNAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SADR  | LONG  | No    | 0     | No    | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STYP  | SHORT | No    | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| DIR   | RECC  | Yes   | 1     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ICID  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MESS  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| CTYP  | SHORT | Yes   | 2     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| PREC  | SHORT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MLNK  | FW    | Yes   | 0     | No    | No    | No    | No    |
|       | DLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| CLNK  | FW    | Yes   | 0     | No    | No    | No    | No    |
|       | DLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| PLNK  | FW    | Yes   | 0     | No    | No    | No    | No    |
|       | DLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STLK  | FW    | Yes   | 0     | No    | No    | No    | No    |
|       | DLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SPLK  | FW    | Yes   | 0     | No    | No    | No    | No    |
|       | DLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OCID  | LONG  | No    | 0     | Yes   | Yes   | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OSIM  | RECC  | No    | None  | Yes   | Yes   | Yes   | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NARG  | SHORT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MARK  | SHORT | No    | 0     | Yes   | Yes   | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ERSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIOL  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SVAL  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIML  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIMM  | RECC  | No    | None  | Yes   | Yes   | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIMS  | GBLC  | No    | 0     | Yes   | Yes   | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPx  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTx  | OU    | Yes   | 0     | No    | No    | No    | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| A - T | S     | No    | 0     | Yes   | Yes   | No    | Spec  |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALx  | NOA   | Yes   | 0     | Yes   | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVx  | GBLC  | No    | S     | Yes   | No    | No    | No    |
|       | HOICE |       | tring |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

.. _field-descriptions-1:

 3.2 Field Descriptions
~~~~~~~~~~~~~~~~~~~~~~

+----------+------------------------+----------------------------+
| **Name** | **Summary**            | **Description**            |
+==========+========================+============================+
| VAL      | Return Error           | The return value is set    |
|          |                        | within the user-supplied   |
|          | Code                   | processing subroutine.     |
|          |                        | Conventionally, a return   |
|          |                        | value of zero indicates    |
|          |                        | success, while a non-zero  |
|          |                        | value shows an error has   |
|          |                        | occurred.                  |
+----------+------------------------+----------------------------+
| SNAM     | Subroutine             | The name of a VxWorks      |
|          |                        | subroutine to execute      |
|          | Name                   | during processing.         |
+----------+------------------------+----------------------------+
| SADR     | Subroutine             | The internal               |
|          |                        | representation of the      |
|          | Address                | subroutine address.        |
+----------+------------------------+----------------------------+
| STYP     | Subroutine symbol type | Not used.                  |
+----------+------------------------+----------------------------+
| INAM     | Init Routine           | The name of a VxWorks      |
|          |                        | subroutine to execute      |
|          | Name                   | during initialization.     |
+----------+------------------------+----------------------------+
| DIR      | CAD Directive          | The directive to execute.  |
|          |                        | This may be one of the     |
|          |                        | following enumerated list  |
|          |                        | values: MARK, CLEAR,       |
|          |                        | PRESET, START, or STOP.    |
+----------+------------------------+----------------------------+
| ICID     | Client ID (In)         | An integer value to be     |
|          |                        | associated with the        |
|          |                        | current command.           |
+----------+------------------------+----------------------------+
| MESS     | Message                | A return message from the  |
|          |                        | CAD. This string will be   |
|          |                        | empty if the return value  |
|          |                        | is zero.                   |
+----------+------------------------+----------------------------+
| OMSS     | Old Message            | The previous message.      |
+----------+------------------------+----------------------------+
| CTYP     | Number of              | This value should be set   |
|          |                        | to the maximum number of   |
|          | CAD Args               | arguments a CAD record     |
|          |                        | will use. The value is     |
|          |                        | usually set by the         |
|          |                        | selected CapFast symbol.   |
+----------+------------------------+----------------------------+
| PREC     | Display                | This value is used for the |
|          |                        | precision of               |
|          | Precision              | double-precision outputs.  |
+----------+------------------------+----------------------------+
| MLNK     | Mark Link              | If the directive is MARK,  |
|          |                        | this forward link is       |
|          |                        | processed.                 |
+----------+------------------------+----------------------------+
| CLNK     | Clear Link             | If the directive is CLEAR, |
|          |                        | this forward link is       |
|          |                        | processed.                 |
+----------+------------------------+----------------------------+
| PLNK     | Preset Link            | If the directive is        |
|          |                        | PRESET, this forward link  |
|          |                        | is processed.              |
+----------+------------------------+----------------------------+
| STLK     | Start Link             | If the directive is START, |
|          |                        | this forward link is       |
|          |                        | processed.                 |
+----------+------------------------+----------------------------+
| SPLK     | Stop Link              | If the directive is STOP,  |
|          |                        | this forward link is       |
|          |                        | processed.                 |
+----------+------------------------+----------------------------+
| OCID     | Client ID (Out)        | The input client ID is     |
|          |                        | sent out this field.       |
+----------+------------------------+----------------------------+
| OSIM     | Simulation             | The simulation mode is     |
|          |                        | sent out this field.       |
|          | Mode (Out)             |                            |
+----------+------------------------+----------------------------+
| NARG     | No. Inputs used        | The actual number of       |
|          |                        | arguments used is set in   |
|          |                        | this field.                |
+----------+------------------------+----------------------------+
| MARK     | Is Record Preset?      | This field shows the       |
|          |                        | current state of the CAD.  |
|          |                        | It can be zero, indicating |
|          |                        | no MARK has been done;     |
|          |                        | one, showing a MARK; or    |
|          |                        | two, showing a PRESET has  |
|          |                        | been done.                 |
+----------+------------------------+----------------------------+
| ERSV     | Error Alarm            | The severity of an alarm.  |
|          |                        |                            |
|          | Severity               |                            |
+----------+------------------------+----------------------------+
| SIOL     | Simulation             | Simulation mode variables. |
|          |                        | Refer to reference [1],    |
|          | Error Link             | chapter 3.                 |
+----------+------------------------+----------------------------+
| SVAL     | Simulation             |                            |
|          |                        |                            |
|          | Error                  |                            |
+----------+------------------------+----------------------------+

+----------+-----------------+---------------------------------------+
| **Name** | **Summary**     | **Description**                       |
+==========+=================+=======================================+
| SIML     | Simulation      |                                       |
|          |                 |                                       |
|          | Mode Link       |                                       |
+----------+-----------------+---------------------------------------+
| SIMM     | Simulation      |                                       |
|          |                 |                                       |
|          | Mode            |                                       |
+----------+-----------------+---------------------------------------+
| SIMS     | Simulation      | The value for the simulated alarm.    |
|          |                 |                                       |
|          | Mode Alarm      |                                       |
|          |                 |                                       |
|          | Severity        |                                       |
+----------+-----------------+---------------------------------------+
| INPx     | Input Link A-T  | The 20 input links for the arguments  |
|          |                 | to the record.                        |
+----------+-----------------+---------------------------------------+
| OUTx     | Output Link A-  | The 20 outputs are sent across these  |
|          |                 | links.                                |
|          | T               |                                       |
+----------+-----------------+---------------------------------------+
| A - T    | Value of Input  | The 20 string input arguments.        |
|          |                 |                                       |
|          | A-T             |                                       |
+----------+-----------------+---------------------------------------+
| VALx     | Value of Output | The 20 output values.                 |
|          |                 |                                       |
|          | A-T             |                                       |
+----------+-----------------+---------------------------------------+
| FTVx     | Type of Value   | The types for the 20 output values.   |
|          |                 | The type may be one of                |
|          | A-T             |                                       |
|          |                 | STRING, LONG, or DOUBLE.              |
+----------+-----------------+---------------------------------------+

.. _record-support-routines-1:

 3.3 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-1:

 3.3.1 init_record
^^^^^^^^^^^^^^^^^

   During the first initialization pass, the output types are determined
   from the FTVx fields and appropriate space is created for the VALx
   fields. During the second pass, input and output links are
   initialized, the initialization routine is called, and the processing
   subroutine is readied. The directive field is set to CLEAR, and the
   mark field is set to zero.

**3.3.2 process**

   See the next section.

 3.3.3 special
^^^^^^^^^^^^^

   The special processing is called when a value is put to one of the
   twenty fields A through T. The mark flag is set to one.

**3.3.4 get_value**

   This routine fills the values of struct valueDes so that they refer
   to VAL.

**3.3.5 get_precision**

   This routine retrieves PREC.

.. _get_enum_str-1:

 3.3.6 get_enum_str
^^^^^^^^^^^^^^^^^^

   This routine converts the long integer values 0 through 4 into the
   strings “MARK”, “CLEAR”, PRESET”, “START”, and “STOP”, respectively.

**3.3.7 get_enum_strs**

   This routine returns all five of the above strings.

**3.3.8 put_enum_str**

   This routine converts the above strings into the long integer values
   0 through 4.

.. _record-processing-1:

 3.4 Record Processing
~~~~~~~~~~~~~~~~~~~~~

   Record processing is very dependent upon the directive given to
   process and the value of the mark field in the state machine. The
   algorithm is:

-  If PRESET, START, or STOP with MARK==0, then return

-  If simulation, process simulation links.

-  Process all input links.

-  Process requested directive (see next sections for details).

-  Enforce rule that MESS is empty if VAL is 0.

-  Put the values on the output links.

-  Raise monitors on fields VAL, MESS, OSIM, OCID, MARK.

-  Process directive link (MLNK, CLNK, PLNK, STLK, SPLK).

-  Process forward link.

 3.4.1 Mark Directive Processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  Call user subroutine, return value in VAL.

-  Copy ICID to OCID.

-  Set mark field to 1.

 3.4.2 Clear Directive Processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  Call user subroutine, return value in VAL.

-  Copy ICID to OCID.

-  Set mark field to 0.

 3.4.3 Preset Directive Processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  Call user subroutine, return value in VAL.

-  Copy ICID to OCID.

-  Set mark field to 2.

 3.4.4 Start Directive Processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  If mark field is 1:

..

   **—**\ set directive to PRESET.

   **—**\ Call user subroutine, return value in VAL.

   **—**\ Copy ICID to OCID.

   **—**\ Process PLNK link.

   **—**\ Set mark field to 2.

   **—**\ Put the values on the output links.

   **—**\ Raise monitors.

   **—**\ set directive to START.

-  Call user subroutine, return value in VAL.

-  Copy ICID to OCID.

-  Set mark field to 0.

 3.4.5 Stop Directive Processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   **•** If mark field is not 0:

   **—**\ Call user subroutine, return value in VAL.

   **—**\ Copy ICID to OCID.

   **—**\ Set mark field to 0.

**3.5 Device Support**

   There is no device support available.

.. _capfast-1:

 3.6 CapFast
~~~~~~~~~~~

   There are four CapFast symbols for the CAD record.

**FIGURE 3.** CapFast *ecad2, ecad4, ecad8, and ecad20* symbols

   .

====== === ======
   DIR cad    VAL
====== === ======
ICID       MESS
SIML       MARK
SIOL       OCID
   A       OSIM
\          ODIR
\          VALA
INPA       OUTA
   B       VALB
INPB       OUTB
   C       VALC
INPC       OUTC
   D       VALD
INPD       OUTD
   E       VALE
INPE       OUTE
   F       VALF
INPF       OUTF
   G       VALG
INPG       OUTG
   H       VALH
INPH       OUTH
   I       VALI
INPI       OUTI
   J       VALJ
INPJ       OUTJ
   K       VALK
INPK       OUTK
   L       VALL
INPL       OUTL
   M       VALM
INPM       OUTM
   N       VALN
INPN       OUTN
   O       VALO
INPO       OUTO
   P       VALP
INPP       OUTP
   Q       VALQ
INPQ       OUTQ
   R       VALR
INPR       OUTR
   S       VALS
INPS       OUTS
   T       VALT
INPT       OUTT
SLNK       FLNK
\          MLNK
\          CLNK
\          PLNK
\          STLK
SDIS       SPLK
\          
====== === ======

====== === ======
   DIR cad    VAL
====== === ======
ICID       MESS
SIML       MARK
SIOL       OCID
   A       OSIM
\          ODIR
\          VALA
INPA       OUTA
   B       VALB
INPB       OUTB
   C       VALC
INPC       OUTC
   D       VALD
INPD       OUTD
   E       VALE
INPE       OUTE
   F       VALF
INPF       OUTF
   G       VALG
INPG       OUTG
   H       VALH
INPH       OUTH
SLNK       FLNK
\          MLNK
\          CLNK
\          PLNK
\          STLK
SDIS       SPLK
\          
====== === ======

====== === ======
   DIR cad    VAL
====== === ======
ICID       MESS
SIML       MARK
SIOL       OCID
A          OSIM
\          ODIR
\          VALA
INPA       OUTA
B          VALB
INPB       OUTB
C          VALC
INPC       OUTC
D          VALD
INPD       OUTD
SLNK       FLNK
\          MLNK
\          CLNK
\          PLNK
\          STLK
SDIS       SPLK
\          
====== === ======

====== === ======
   DIR cad    VAL
====== === ======
ICID       MESS
SIML       MARK
SIOL       OCID
   A       OSIM
\          ODIR
\          VALA
INPA       OUTA
   B       VALB
INPB       OUTB
SLNK       FLNK
\          MLNK
\          CLNK
\          PLNK
\          STLK
SDIS       SPLK
\          
====== === ======

..

   NPPNMSNPPNMSNPPNMSNPPNMS NPPNMSNPPNMSNPPNMSNPPNMS

NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMS

NPPNMS NPPNMSNPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMS

NPPNMSNPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMSNPPNMS

NPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMS

NPPNMS NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMS

NPPNMSNPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMS

NPPNMS NPPNMSNPPNMS

   NPPNMS

NPPNMS NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMSNPPNMS

   NPPNMS

   NPPNMS

   NPPNMS

   NPPNMS

   NPPNMSNPPNMS

   .

4.0 CAR - Command Action Response Record
----------------------------------------

   The CAR record provides information about the state of a particular
   action occurring within a database. Possible states of the CAR record
   are UNAVAILABLE, IDLE, BUSY, PAUSED, and ERR. The allowable
   transitions between the states are illustrated in the figure below.

.. _field-summary-2:

 4.1 Field Summary
~~~~~~~~~~~~~~~~~

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| VAL   | RECC  | Yes   | Idle  | Yes   | No    | Yes   | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| CLID  | LONG  | No    | 0     | Yes   | No    | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OMSS  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OERR  | LONG  | No    | 0     | Yes   | Yes   | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| IVAL  | LONG  | No    | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ICID  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| IMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| IERR  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| AVAL  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MVAL  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ACID  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MCID  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| AMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| AERR  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MERR  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ERSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIOL  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SVAL  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIML  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIMM  | RECC  | No    | 0     | Yes   | Yes   | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIMS  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

.. _field-descriptions-2:

 4.2 Field Descriptions
~~~~~~~~~~~~~~~~~~~~~~

+----------+------------------+--------------------------------------+
| **Name** | **Summary**      | **Description**                      |
+==========+==================+======================================+
| VAL      | State            | Current state                        |
+----------+------------------+--------------------------------------+
| CLID     | Client ID        | Value of the latest client ID        |
+----------+------------------+--------------------------------------+
| OMSS     | Message (out)    | Output message                       |
+----------+------------------+--------------------------------------+
| OERR     | Error Code (out) | Output error code                    |
+----------+------------------+--------------------------------------+
| IVAL     | State (in)       | Input state transition               |
+----------+------------------+--------------------------------------+
| ICID     | Client ID (in)   | Link to client ID                    |
+----------+------------------+--------------------------------------+
| IMSS     | Message (in)     | Input message                        |
+----------+------------------+--------------------------------------+
| IERR     | Error Code (in)  | Input error code                     |
+----------+------------------+--------------------------------------+
| AVAL     | Last State       | The last state value which was       |
|          |                  | archived                             |
|          | Archived         |                                      |
+----------+------------------+--------------------------------------+
| **Name** | **Summary**      | **Description**                      |
+----------+------------------+--------------------------------------+
| MVAL     | Last State       | The last state value which generated |
|          |                  | a monitor event                      |
|          | Monitored        |                                      |
+----------+------------------+--------------------------------------+
| ACID     | Last Client ID   | The last Client ID which generated   |
|          |                  | an archive event                     |
|          | Archived         |                                      |
+----------+------------------+--------------------------------------+
| MCID     | Last Client ID   | The last Client ID which generated a |
|          |                  | monitor event                        |
|          | Monitored        |                                      |
+----------+------------------+--------------------------------------+
| AMSS     | Last Message     | The last message which generated an  |
|          |                  | archive event                        |
|          | Archived         |                                      |
+----------+------------------+--------------------------------------+
| MMSS     | Last Message     | The last message which generated a   |
|          |                  | monitor event                        |
|          | Monitored        |                                      |
+----------+------------------+--------------------------------------+
| AERR     | Last Error Code  | The last error code which generated  |
|          |                  | an archive event                     |
|          | Archived         |                                      |
+----------+------------------+--------------------------------------+
| MERR     | Last Error Code  | The last error code which generated  |
|          |                  | a monitor event                      |
|          | Monitored        |                                      |
+----------+------------------+--------------------------------------+
| ERSV     | Error Alarm      | The severity code of the error alarm |
|          |                  |                                      |
|          | Severity         |                                      |
+----------+------------------+--------------------------------------+
| SIOL     | Simulation       | Simulation mode variables. Refer to  |
|          |                  | reference [1], chapter 3.            |
|          | Error Link       |                                      |
+----------+------------------+--------------------------------------+
| SVAL     | Simulation       |                                      |
|          |                  |                                      |
|          | Error            |                                      |
+----------+------------------+--------------------------------------+
| SIML     | Simulation       |                                      |
|          |                  |                                      |
|          | Mode Link        |                                      |
+----------+------------------+--------------------------------------+
| SIMM     | Simulation       |                                      |
|          |                  |                                      |
|          | Mode             |                                      |
+----------+------------------+--------------------------------------+
| SIMS     | Simulation       |                                      |
|          |                  |                                      |
|          | Mode Alarm       |                                      |
|          |                  |                                      |
|          | Severity         |                                      |
+----------+------------------+--------------------------------------+

.. _record-support-routines-2:

 4.3 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-2:

 4.3.1 init_record
^^^^^^^^^^^^^^^^^

   This routine sets the current state to IDLE and clears the input
   message and error code. Any simulation modes or links are also
   initialized.

**4.3.2 process**

   See the next section.

**4.3.3 get_value**

   This routine fills the values of struct valueDes so that they refer
   to VAL.

.. _get_enum_str-2:

 4.3.4 get_enum_str
^^^^^^^^^^^^^^^^^^

   This routine converts the long integer values 0 through 5 into the
   strings “UNAVAILABLE”, “IDLE”, “PAUSED”, “ERR”, “BUSY”, and “UNKNOWN”
   respectively.

**4.3.5 get_enum_strs**

   This routine returns all of the above strings.

**4.3.6 put_enum_str**

   This routine converts the state strings into values 0 through 5.

.. _record-processing-2:

 4.4 Record Processing
~~~~~~~~~~~~~~~~~~~~~

   Record processing begins when a value is written into the IVAL field.
   The client ID is read from the ICID link and put into CLID. The input
   value is used to transition the CAR state machine and the input
   message and input error code are placed into the output message and
   output error code fields. If the state machine transitions to the
   ERROR state an alarm is generated.

Monitors are posted if the output value is different than the last
output value or the client ID is different than the old client ID.
Monitors are posted on the VAL, CLID, OMSS, and OERR fields.

**4.5 Device Support**

   There is no device support available.

.. _capfast-2:

 4.6 CapFast
~~~~~~~~~~~

   There is one CapFast symbol for the CAR record.

**FIGURE 5.** CapFast *ecars* symbol .

==== === ======
IVAL car    VAL
==== === ======
ICID     CLID
IMSS     OMSS
IERR     OERR
SIML     FLNK
SIOL     
SLNK     
SDIS     
\        
==== === ======

..

   NPPNMS

   NPPNMS

   NPPNMS

   NPPNMS

   .

5.0 SIR - Status Information Record
-----------------------------------

   The Status Information Record provides a standard information-passing
   mechanism between Gemini principle systems. The SIR records for any
   Gemini system are expected to be combined into a separate database
   called the Status Alarm Database (SAD) and loaded into the SAD IOC.

SIR records combine three important parts of Gemini EPICS status
reporting: a status value, status message, and alarms. The status value
is read through the INP link, the status message is placed into the IMSS
link. Alarms are propagated to the SIR record by the input link.

There are three valid output data types: long integer, double precision,
and string. A subroutine supplied by the user can be attached to the
SNAM field and called during record processing. This subroutine can
convert the input, raise additional alarms, or enhance the output
message string.

   A full description of the SIR record’s function is expected in the
   FDSC field. This field can hold a 40 character string which is used
   by the OCS when displaying the SIR value.

.. _field-summary-3:

 5.1 Field Summary
~~~~~~~~~~~~~~~~~

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| INP   | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| IMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FDSC  | S     | Yes   | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVL  | GBLC  | Yes   | 0     | Yes   | No    | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| EGU   | S     | Yes   | units | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VAL   | VOID  | No    | Null  | Yes   | Yes   | Yes   | Yes   |
|       | \*    |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OMSS  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SNAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SADR  | LONG  | No    | 0     | No    | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STYP  | SHORT | No    | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| PREC  | SHORT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| AVAL  | VOID  | No    | 0     | Yes   | Yes   | No    | No    |
|       | \*    |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MVAL  | VOID  | No    | 0     | Yes   | Yes   | No    | No    |
|       | \*    |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| AMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MMSS  | S     | No    | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LALM  | D     | No    | 0     | XXX   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HIHI  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LOLO  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HIGH  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LOW   | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| BRSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HHSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LLSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HSV   | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LSV   | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HYST  | D     | Yes   | 0     | XXX   | Yes   | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ADEL  | D     | Yes   | 0     | XXX   | Yes   | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MDEL  | D     | Yes   | 0     | XXX   | Yes   | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIOL  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SVAL  | S     | No    | Null  | Yes   | Yes   | No    | Yes   |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIML  | I     | Yes   | 0     | No    | No    | No    | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIMM  | RECC  | No    | 0     | Yes   | Yes   | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SIMS  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | No    |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

.. _field-descriptions-3:

 5.2 Field Descriptions
~~~~~~~~~~~~~~~~~~~~~~

+----------+------------------------+----------------------------+
| **Name** | **Summary**            | **Description**            |
+==========+========================+============================+
| INP      | Input Link             | A CA link to the value to  |
|          |                        | be reported. This value    |
|          |                        | must match the type        |
|          |                        | declared in FTVL.          |
+----------+------------------------+----------------------------+
| IMSS     | Message In             | The input message.         |
+----------+------------------------+----------------------------+
| FDSC     | Full Description       | A 40 character description |
|          |                        | of the SIR record          |
+----------+------------------------+----------------------------+
| FTVL     | Type of value          | The data type of the input |
|          |                        | link. May be either LONG,  |
|          |                        | DOUBLE, or STRING.         |
+----------+------------------------+----------------------------+
| EGU      | Engineering            | The units of the value.    |
|          |                        |                            |
|          | Units                  |                            |
+----------+------------------------+----------------------------+
| VAL      | Value Out              | The input value converted  |
|          |                        | to a string.               |
+----------+------------------------+----------------------------+
| OMSS     | Message Out            | The output message.        |
+----------+------------------------+----------------------------+
| SNAM     | Subroutine             | The VxWorks name of a      |
|          |                        | subroutine to perform      |
|          | Name                   | additional conversion      |
|          |                        | between the input and      |
|          |                        | output values.             |
+----------+------------------------+----------------------------+
| SADR     | Subroutine             | The internal               |
|          |                        | representation of the      |
|          | Address                | subroutine address.        |
+----------+------------------------+----------------------------+
| STYP     | Subroutine symbol type | Not used.                  |
+----------+------------------------+----------------------------+
| PREC     | Display                | This value is used for the |
|          |                        | precision of               |
|          | Precision              | double-precision output.   |
+----------+------------------------+----------------------------+
| AVAL     | Last Value             | This value is used to      |
|          |                        | determine if the new value |
|          | Archived               | needs to generate an       |
|          |                        | archive monitor.           |
+----------+------------------------+----------------------------+
| MVAL     | Last Value             | This value is used to      |
|          |                        | determine if the new value |
|          | Monitored              | needs to generate a        |
|          |                        | monitor.                   |
+----------+------------------------+----------------------------+
| AMSS     | Last Message           | This string used to        |
|          |                        | determine if the new       |
|          | Archived               | message string needs to    |
|          |                        | generate an archive        |
|          |                        | monitor                    |
+----------+------------------------+----------------------------+
| MMSS     | Last Message           | This string used to        |
|          |                        | determine if the new       |
|          | Monitored              | message string needs to    |
|          |                        | generate a monitor.        |
+----------+------------------------+----------------------------+
| LALM     | Last Value             | This value saves the last  |
|          |                        | value that caused an       |
|          | Alarmed                | alarm.                     |
+----------+------------------------+----------------------------+
| HIHI     | Hihi Alarm             | This sets the HI-HI alarm  |
|          |                        | limit.                     |
|          | Limit                  |                            |
+----------+------------------------+----------------------------+
| LOLO     | Lolo Alarm             | This sets the LOW-LOW      |
|          |                        | alarm limit.               |
|          | Limit                  |                            |
+----------+------------------------+----------------------------+
| HIGH     | High Alarm             | This sets the HIGH alarm   |
|          |                        | limit.                     |
|          | Limit                  |                            |
+----------+------------------------+----------------------------+
| LOW      | Low Alarm              | This sets the low alarm    |
|          |                        | limit.                     |
|          | Limit                  |                            |
+----------+------------------------+----------------------------+
| BRSV     | Bad Sub Return         | This alarm is set if the   |
|          |                        | user subroutine returns a  |
|          | Severity               | non-zero value.            |
+----------+------------------------+----------------------------+
| HHSV     | Hihi Severity          | This alarm is set if the   |
|          |                        | HIGH-HIGH value is         |
|          |                        | reached.                   |
+----------+------------------------+----------------------------+
| LLSV     | Lolo Severity          | This alarm is set if the   |
|          |                        | LOW-LOW value is reached.  |
+----------+------------------------+----------------------------+
| HSV      | High Severity          | This alarm is set if the   |
|          |                        | HIGH value is reached.     |
+----------+------------------------+----------------------------+
| LSV      | Low Severity           | This alarm is set if the   |
|          |                        | LOW value is reached.      |
+----------+------------------------+----------------------------+
| **Name** | **Summary**            | **Description**            |
+----------+------------------------+----------------------------+
| HYST     | Alarm                  | This sets the hysteresis   |
|          |                        | range for alarm reporting. |
|          | Deadband               |                            |
+----------+------------------------+----------------------------+
| ADEL     | Archive                | This set the archive       |
|          |                        | hysteresis range.          |
|          | Deadband               |                            |
+----------+------------------------+----------------------------+
| MDEL     | Monitor                | This sets the monitor      |
|          |                        | hysteresis range.          |
|          | Deadband               |                            |
+----------+------------------------+----------------------------+
| SIOL     | Simulation             | In simulation mode this    |
|          |                        | link value is read and     |
|          | Value Link             | returned in                |
|          |                        |                            |
|          |                        | VAL.                       |
+----------+------------------------+----------------------------+
| SVAL     | Simulation             | The simulation value is    |
|          |                        | stored here.               |
|          | Value                  |                            |
+----------+------------------------+----------------------------+
| SIML     | Simulation             | This link determines if    |
|          |                        | the record is in           |
|          | Mode Link              | simulation mode or not.    |
+----------+------------------------+----------------------------+
| SIMM     | Simulation             | The simulation mode is     |
|          |                        | stored here.               |
|          | Mode                   |                            |
+----------+------------------------+----------------------------+
| SIMS     | Simulation             | This is the value of the   |
|          |                        | simulation mode alarm      |
|          | Mode Alarm             | severity.                  |
|          |                        |                            |
|          | Severity               |                            |
+----------+------------------------+----------------------------+

.. _record-support-routines-3:

 5.3 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-3:

 5.3.1 init_record
^^^^^^^^^^^^^^^^^

   On the first pass, space is allocated for the VAL, SVAL, MVAL, AVAL
   fields, based upon the type of FTVL field (DBF_STRING, DBF_DOUBLE, or
   DBF_LONG). On the second pass, the simulation mode is checked and if
   true, the simulation mode and input link are set. The address of the
   process subroutine (SNAM) is found.

**5.3.2 process**

   See the next section.

**5.3.3 get_value**

   This routine fills the values of struct valueDes so that they refer
   to VAL.

**5.3.4 get_precision**

   This routine retrieves PREC.

**5.3.5 get_units**

   This routine returns EGU.

 5.3.6 cvt_dbaddr
^^^^^^^^^^^^^^^^

   This routine converts the VAL field to either string, double
   precision, or long integer based upon the value of FTVL.

 5.3.7 get_alarm_double
^^^^^^^^^^^^^^^^^^^^^^

   This routine sets the following values:

   upper_alarm_limit = HIHI upper_warning_limit = HIGH
   lower_warning_limit = LOW lower_alarm_limit = LOLO

.. _record-processing-3:

 5.4 Record Processing
~~~~~~~~~~~~~~~~~~~~~

   In simulation mode, the simulation value is fetched from the link.
   Otherwise the values is fetched from the INP link. The user
   subroutine (SNAM) is executed and the return value is checked. For
   return values of zero, the input message IMSS is copied to the output
   message OMSS, the record is time stamped, alarms and monitors are
   checked, and the forward link is processed.

   An alarm is set if the value is outside the preset range limits
   (HIHI, HIGH, LOW, LOLO) and outside of the hysteresis range (HYST).

   Logging and archive monitors are raised on VAL and OMSS if their
   value changes. For double precision and long integer values of VAL
   the change must be greater than the hysteresis ranges (MDEL and
   ADEL).

**5.5 Device Support**

   No device support is available.

.. _capfast-3:

 5.6 CapFast
~~~~~~~~~~~

   There is one CapFast symbol for the SIR record.

**FIGURE 6.** CapFast *esirs* symbol

   .

====== ====== ======
   INP    sir FLNK
====== ====== ======
IMSS             VAL
SIML          OMSS
SIOL          
SLNK          
SDIS          
\             
====== ====== ======

..

   NPP NMS

   NPPNMS

   NPPNMS

   NPPNMS

   .

6.0 lutout - Lookup Table Output Record
---------------------------------------

   The lutout record converts a character string tag into up to four
   output values. The output values may be either string,
   double-precision, or long integer, and are selected through the FTVA,
   FTVB, FTVC, and FTVD fields. The conversion is performed on the value
   found in the lookup table for the appropriate output, thus if the
   conversion is to a long integer type the lookup table value should be
   an ASCII string representation of a long integer. Conversion of
   string values consists of copying the lookup table value to the
   output field. A character string tag may have outputs with mixed
   types, however each output field’s type is fixed at initialization.

   The lutout record may selectively send values to the output fields by
   using the SELB field. This field is a bit mask for the enabled output
   fields. If the bit mask is 1, then only the VALA field will be
   written, if it is 3, then both VALA and VALB will be written. A value
   of 15 will write to all four output fields. The number of fields
   successfully written will be placed in the NVAL field. This value is
   the intersection of the bits set in SELB and the number of values in
   a tag’s lookup table entry. The SEVR and STAT alarm fields are set to
   INVALID and SOFT if an input configuration string is not recognized

   The lookup table can be reread from the file by writing a one to the
   LOAD field.

A lutout record can reference another lutout or lutin record’s lookup
table by making a connection between its LLNK field and the other
record’s LTBL field. The record can now find a tag in either its lookup
table or the table from the other record. This feature can only work
within an IOC; it will not work across channel access. *[This feature is
not yet implemented].*

   More information and examples can be found in the lutout manual[3].

**6.1 Field Summary**

+-----------+-----------+-------------+------------+------------+-------------+---------+
| **Field** | **Type**  | **Initial** | **Access** | **Modify** | **Monitor** | **PP**  |
+===========+===========+=============+============+============+=============+=========+
| VAL       | STRING    | Null        | Yes        | Yes        | Yes         | Yes     |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OVAL      | STRING    | Null        | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FDIR      | STRING    | Null        | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FNAM      | STRING    | Null        | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| NVAL      | LONG      | 0           | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| ONVL      | LONG      | 0           | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| SELB      | LONG      | 15          | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| LOAD      | LONG      | 0           | Yes        | Yes        | No          | Special |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| LTBL      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| LLNK      | INLINK    | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| PREC      | LONG      | 2           | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALA      | NOACCESS  | 0           | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALB      | NOACCESS  | 0           | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALC      | NOACCESS  | 0           | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALD      | NOACCESS  | 0           | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDA      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDB      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDC      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDD      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OUTA      | OUTLINK   | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OUTB      | OUTLINK   | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OUTC      | OUTLINK   | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OUTD      | OUTLINK   | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVA      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVB      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVC      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVD      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+

 6.2 Field Description
~~~~~~~~~~~~~~~~~~~~~

**TABLE 1.** Lutout Record Field Description

+----------+--------------+------------------------------------------+
| **Name** | **Summary**  | **Description**                          |
+==========+==============+==========================================+
| VAL      | Input string | An ASCII string which corresponds to a   |
|          |              | tag entry in the lookup table.           |
+----------+--------------+------------------------------------------+
| OVAL     | Old tag      | The previous value.                      |
+----------+--------------+------------------------------------------+

+----------+----------------------------+----------------------------+
| **Name** | **Summary**                | **Description**            |
+==========+============================+============================+
| FDIR     | Initialization file        | The name of the directory  |
|          | directory                  | of the lookup table.       |
+----------+----------------------------+----------------------------+
| FNAM     | Initialization file name   | The name of the file for   |
|          |                            | the lookup table.          |
+----------+----------------------------+----------------------------+
| NVAL     | Number of outputs          | This is the number of      |
|          |                            | values found in the lookup |
|          |                            | table for the appropriate  |
|          |                            | input tag. It is between 0 |
|          |                            | and 4.                     |
+----------+----------------------------+----------------------------+
| ONVL     | Old number                 | The previous value of      |
|          |                            | NVAL. Used for triggering  |
|          |                            | monitors.                  |
+----------+----------------------------+----------------------------+
| SELB     | Selection Bits             | This is a bit mask for the |
|          |                            | values which are to be     |
|          |                            | converted. If SELB is 1,   |
|          |                            | only VALA will be written; |
|          |                            | if 3, VALA and VALB will   |
|          |                            | be written. A value of 15  |
|          |                            | will write all values.     |
+----------+----------------------------+----------------------------+
| LOAD     | Reload configuration file  | Any nonzero value written  |
|          |                            | to this field will force   |
|          |                            | the reloading of the       |
|          |                            | lookup table from the      |
|          |                            | configuration file defined |
|          |                            | by                         |
|          |                            |                            |
|          |                            | FDIR and FNAM.             |
+----------+----------------------------+----------------------------+
| LTBL     | Lookup Table               | This is the location where |
|          |                            | the conversion table is    |
|          |                            | stored in memory.          |
+----------+----------------------------+----------------------------+
| LLNK     | Lookup Table               | The link to another        |
|          |                            | record’s lookup table      |
|          | Link                       | (LTBL). This link only     |
|          |                            | works within an IOC.       |
+----------+----------------------------+----------------------------+
| PREC     | Precision                  | This is the level of       |
|          |                            | precision given for        |
|          |                            | double-precision           |
|          |                            | conversion.                |
+----------+----------------------------+----------------------------+
| VALA     | Output value               | The lookup table values    |
|          |                            | are put on this port. A    |
|          |                            | monitor event is sent if   |
|          |                            | the value changed.         |
+----------+----------------------------+----------------------------+
| VALB     | Output value               | The lookup table values    |
|          |                            | are put on this port. A    |
|          |                            | monitor event is sent if   |
|          |                            | the value changed.         |
+----------+----------------------------+----------------------------+
| VALC     | Output value               | The lookup table values    |
|          |                            | are put on this port. A    |
|          |                            | monitor event is sent if   |
|          |                            | the value changed.         |
+----------+----------------------------+----------------------------+
| VALD     | Output value               | The lookup table values    |
|          |                            | are put on this port. A    |
|          |                            | monitor event is sent if   |
|          |                            | the value changed.         |
+----------+----------------------------+----------------------------+
| OLDA     | Old value                  | The old value. Used to     |
|          |                            | trigger monitors.          |
+----------+----------------------------+----------------------------+
| OLDB     | Old value                  | The old value. Used to     |
|          |                            | trigger monitors.          |
+----------+----------------------------+----------------------------+
| OLDC     | Old value                  | The old value. Used to     |
|          |                            | trigger monitors.          |
+----------+----------------------------+----------------------------+
| OLDD     | Old value                  | The old value. Used to     |
|          |                            | trigger monitors.          |
+----------+----------------------------+----------------------------+
| OUTA     | Output link                | The lookup table values    |
|          |                            | are sent out these links.  |
+----------+----------------------------+----------------------------+
| OUTB     | Output link                | The lookup table values    |
|          |                            | are sent out these links.  |
+----------+----------------------------+----------------------------+
| OUTC     | Output link                | The lookup table values    |
|          |                            | are sent out these links.  |
+----------+----------------------------+----------------------------+
| OUTD     | Output link                | The lookup table values    |
|          |                            | are sent out these links.  |
+----------+----------------------------+----------------------------+
| FTVA     | Output value type          | May be one of STRING,      |
|          |                            | DOUBLE, or LONG. This      |
|          |                            | field                      |
|          |                            |                            |
|          |                            | forces the conversion of   |
|          |                            | the appropriate table      |
|          |                            | value to the correct type. |
+----------+----------------------------+----------------------------+
| FTVB     | Output value type          | May be one of STRING,      |
|          |                            | DOUBLE, or LONG. This      |
|          |                            | field                      |
|          |                            |                            |
|          |                            | forces the conversion of   |
|          |                            | the appropriate table      |
|          |                            | value to the correct type. |
+----------+----------------------------+----------------------------+
| **Name** | **Summary**                | **Description**            |
+----------+----------------------------+----------------------------+
| FTVC     | Output value type          | May be one of STRING,      |
|          |                            | DOUBLE, or LONG. This      |
|          |                            | field                      |
|          |                            |                            |
|          |                            | forces the conversion of   |
|          |                            | the appropriate table      |
|          |                            | value to the correct type. |
+----------+----------------------------+----------------------------+
| FTVD     | Output value type          | May be one of STRING,      |
|          |                            | DOUBLE, or LONG. This      |
|          |                            | field                      |
|          |                            |                            |
|          |                            | forces the conversion of   |
|          |                            | the appropriate table      |
|          |                            | value to the correct type. |
+----------+----------------------------+----------------------------+

.. _record-support-routines-4:

 6.3 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-4:

 6.3.1 init_record
^^^^^^^^^^^^^^^^^

   During the first initialization pass the ASCII configuration file is
   read and stored in LTBL. Space is allocated for the VAL[A-D] and
   OLD[A-D] fields. On the second pass, device support initialization is
   performed.

**6.3.2 process**

   See next section.

.. _special-1:

 6.3.3 special
^^^^^^^^^^^^^

   If the LOAD field is nonzero, the existing lookup table is cleared
   and a new table is read from the file described by the FDIR and FNAM
   fields.

**6.3.4 get_value**

   This routine fills in the values of struct valueDes so that they
   refer to VAL.

**6.3.5 get_precision**

   This routine returns the value of PREC.

**6.3.6 get_enum_strs**

   All tags are returned by this routine.

.. _cvt_dbaddr-1:

 6.3.7 cvt_dbaddr
^^^^^^^^^^^^^^^^

   This routine converts the output strings into the appropriate data
   type defined by FTV[A-D].

.. _record-processing-4:

 6.4 Record Processing
~~~~~~~~~~~~~~~~~~~~~

   The processing algorithm performs the following steps:

1. Checks to see the appropriate device support module exists. If it
      doesn’t, an error message is issued and processing is terminated
      with the PACT field set to TRUE.

2. The device support write function is called. The return status value
      is set from the return value of this function.

3. If the device support did not complete processing, the PACT field is
      left TRUE and processing terminates. It is assumed the device
      support callback will reenter the processing routine and complete
      at a later time.

4. The processing flag is set to TRUE.

5. Check for alarms. If the NVAL field is equal to zero, the SOFT_ALARM
      is set to INVALID.

6. Check for monitors. If any value in the fields VAL, NVAL, VALA, VALB,
      VALC, VALD has changed a monitor event is posted for that field.

7. Process the forward link.

8. Set PACT to FALSE and exit.

**6.5 Device Support**

   The only device currently supported is Soft Channel.

.. _capfast-4:

 6.6 CapFast
~~~~~~~~~~~

There are two CapFast symbols for the lutout record: *elutouts.sym* and
*elutout.sym*. These symbol are included in the epics3.12.2Gem.4 and
later distributions. The symbols are shown in Figure 7 on page 26.

**FIGURE 7.** CapFast *elutouts* and *elutout* symbols

   .

   .

 6.7 Future Enhancements
~~~~~~~~~~~~~~~~~~~~~~~

   Some future enhancements the lutout record may be:

-  Access of LTBL across a network.

-  Support for more data types.

-  Per-item additions to the lookup table.

-  Better return of the list of tags.

7.0 lutin - Lookup Table Input Record
-------------------------------------

   The lutin record converts up to four input values into a character
   string. The input values can be any of string, double-precision, or
   long integer data types, determined from the FTVA, FTVB. FTVC, and
   FTVD fields. The conversion type must match the value found in the
   lookup table, thus if the type is a long integer, the corresponding
   lookup table value must also be an integer. For integer and
   double-precision values, the value is matched to within the low and
   high tolerances given in the lookup table. This feature supports any
   hysteresis or jitter associated with the sampled value. The low and
   high tolerances can be individually adjusted to accommodate nonlinear
   or preloaded systems.

   The SELB field allows only the specified input fields to be used in
   the conversion. If the selection bit mask is 1, the first
   configuration that matches only the VALA field is returned. If the
   mask is 3, the VALA and VALB fields are the only ones tested. For a
   mask of 15, all input fields are used. The NVAL field returns the
   number of fields that were successfully matched. An NVAL field of
   zero will generate a SOFT alarm of INVALID.

   The lookup table can be reread from the file by writing a one to the
   LOAD field.

   A lutin record can reference another lutout or lutin record’s lookup
   table by making a connection between its LLNK field and the other
   record’s LTBL field. The record can now find a tag in either its
   lookup table or the table from the other record. This feature can
   only work within an IOC; it will not work across channel access.
   [*This feature is not yet implemented].*

   More information and examples can be found in the lutin manual[3].

.. _field-summary-4:

 7.1 Field Summary
~~~~~~~~~~~~~~~~~

**TABLE 2.** Lutin Record Field Summary

+-----------+-----------+-------------+------------+------------+-------------+---------+
| **Field** | **Type**  | **Initial** | **Access** | **Modify** | **Monitor** | **PP**  |
+===========+===========+=============+============+============+=============+=========+
| VAL       | STRING    | Null        | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OVAL      | STRING    | Null        | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| NVAL      | LONG      | 0           | Yes        | No         | Yes         | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| ONVL      | LONG      | 0           | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| SELB      | LONG      | 15          | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| PREC      | LONG      | 2           | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FDIR      | STRING    | Null        | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FNAM      | STRING    | Null        | Yes        | Yes        | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| LTBL      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| LLNK      | INLINK    | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| LOAD      | LONG      | 0           | Yes        | Yes        | No          | Special |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| INPA      | INLINK    | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| **Field** | **Type**  | **Initial** | **Access** | **Modify** | **Monitor** | **PP**  |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| INPB      | INLINK    | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| INPC      | INLINK    | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| INPD      | INLINK    | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALA      | NOACCESS  | 0           | Yes        | Yes        | Yes         | Yes     |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALB      | NOACCESS  | 0           | Yes        | Yes        | Yes         | Yes     |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALC      | NOACCESS  | 0           | Yes        | Yes        | Yes         | Yes     |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| VALD      | NOACCESS  | 0           | Yes        | Yes        | Yes         | Yes     |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDA      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDB      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDC      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| OLDD      | NOACCESS  | 0           | No         | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVA      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVB      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVC      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+
| FTVD      | GBLCHOICE | STRING      | Yes        | No         | No          | No      |
+-----------+-----------+-------------+------------+------------+-------------+---------+

.. _field-description-1:

 7.2 Field Description
~~~~~~~~~~~~~~~~~~~~~

**TABLE 3.** Lutin Record Field Description

+----------+---------------------+-----------------------------------+
| **Name** | **Summary**         | **Description**                   |
+==========+=====================+===================================+
| VAL      | Result              | The character string of the       |
|          |                     | configuration that matches the    |
|          |                     | inputs.                           |
+----------+---------------------+-----------------------------------+
| OVAL     | Old Result          | The old VAL. Used to trigger      |
|          |                     | monitors.                         |
+----------+---------------------+-----------------------------------+
| NVAL     | Result Code         | The number of values that were    |
|          |                     | used in the match.                |
+----------+---------------------+-----------------------------------+
| ONVL     | Old Result Code     | The old NVAL. Used to trigger     |
|          |                     | monitors.                         |
+----------+---------------------+-----------------------------------+
| SELB     | Selection Bits      | The bit mask of the input values  |
|          |                     | to use for testing.               |
+----------+---------------------+-----------------------------------+
| PREC     | Precision           | The precision of double-precision |
|          |                     | values.                           |
+----------+---------------------+-----------------------------------+
| FDIR     | Init File Directory | The name of the directory of the  |
|          |                     | ASCII configuration file.         |
+----------+---------------------+-----------------------------------+
| FNAM     | Init File Name      | The name of the file of the ASCII |
|          |                     | configuration file.               |
+----------+---------------------+-----------------------------------+
| LTBL     | Lookup Table        | The address of the storage of the |
|          |                     | lookup table.                     |
+----------+---------------------+-----------------------------------+
| LLNK     | Lookup Table        | A link to another record’s lookup |
|          |                     | table.                            |
|          | Link                |                                   |
+----------+---------------------+-----------------------------------+
| LOAD     | Reload Table        | Any nonzero value written to this |
|          |                     | field will force the reloading of |
|          |                     | the lookup table from the         |
|          |                     | configuration file defined by     |
|          |                     |                                   |
|          |                     | FDIR and FNAM.                    |
+----------+---------------------+-----------------------------------+
| INPA     | Input               | The input link.                   |
+----------+---------------------+-----------------------------------+
| INPB     | Input               | The input link.                   |
+----------+---------------------+-----------------------------------+
| INPC     | Input               | The input link.                   |
+----------+---------------------+-----------------------------------+
| **Name** | **Summary**         | **Description**                   |
+----------+---------------------+-----------------------------------+
| INPD     | Input               | The input link.                   |
+----------+---------------------+-----------------------------------+
| VALA     | Value of Input      | The input value.                  |
+----------+---------------------+-----------------------------------+
| VALB     | Value of Input      | The input value.                  |
+----------+---------------------+-----------------------------------+
| VALC     | Value of Input      | The input value.                  |
+----------+---------------------+-----------------------------------+
| VALD     | Value of Input      | The input value.                  |
+----------+---------------------+-----------------------------------+
| OLDA     | Old Value of        | The old value. Used to trigger    |
|          |                     | monitors.                         |
|          | Input               |                                   |
+----------+---------------------+-----------------------------------+
| OLDB     | Old Value of        | The old value. Used to trigger    |
|          |                     | monitors.                         |
|          | Input               |                                   |
+----------+---------------------+-----------------------------------+
| OLDC     | Old Value of        | The old value. Used to trigger    |
|          |                     | monitors.                         |
|          | Input               |                                   |
+----------+---------------------+-----------------------------------+
| OLDD     | Old Value of        | The old value. Used to trigger    |
|          |                     | monitors.                         |
|          | Input               |                                   |
+----------+---------------------+-----------------------------------+
| FTVA     | Type of Value       | The data type of the input value. |
|          |                     | May be either STRING, DOUBLE, or  |
|          |                     | LONG.                             |
+----------+---------------------+-----------------------------------+
| FTVB     | Type of Value       | The data type of the input value. |
|          |                     | May be either STRING, DOUBLE, or  |
|          |                     | LONG.                             |
+----------+---------------------+-----------------------------------+
| FTVC     | Type of Value       | The data type of the input value. |
|          |                     | May be either STRING, DOUBLE, or  |
|          |                     | LONG.                             |
+----------+---------------------+-----------------------------------+
| FTVD     | Type of Value       | The data type of the input value. |
|          |                     | May be either STRING, DOUBLE, or  |
|          |                     | LONG.                             |
+----------+---------------------+-----------------------------------+

.. _record-support-routines-5:

 7.3 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-5:

 7.3.1 init_record
^^^^^^^^^^^^^^^^^

   During the first initialization pass the ASCII configuration file is
   read and stored in LTBL. Space is allocated for the VAL[A-D] and
   OLD[A-D] fields. On the second pass, device support initialization is
   performed.

**7.3.2 process**

   See next section.

.. _special-2:

 7.3.3 special
^^^^^^^^^^^^^

   If the LOAD field is nonzero, the existing lookup table is cleared
   and a new table is read from the file described by the FDIR and FNAM
   fields.

**7.3.4 get_value**

   This routine fills in the values of struct valueDes so that they
   refer to VAL.

**7.3.5 get_precision**

   This routine returns the value of PREC.

**7.3.6 get_enum_strs**

   All tags are returned by this routine.

.. _cvt_dbaddr-2:

 7.3.7 cvt_dbaddr
^^^^^^^^^^^^^^^^

   This routine converts the input strings into the appropriate data
   type defined by FTV[A-D].

.. _record-processing-5:

 7.4 Record Processing
~~~~~~~~~~~~~~~~~~~~~

   The processing algorithm performs the following steps:

1. Checks to see the appropriate device support module exists. If it
   doesn’t, an error message is issued and processing is terminated with
   the PACT field set to TRUE.

2. The device support read function is called. The return status value
   is set from the return value of this function.

3. If the device support did not complete processing, the PACT field is
   left TRUE and processing terminates. It is assumed the device support
   callback will reenter the processing routine and complete at a later
   time.

4. The processing flag is set to TRUE.

5. Check for alarms. If the NVAL field is equal to zero, SOFT_ALARM is
   set to INVALID.

6. Check for monitors. If any value in the fields VAL, NVAL, VALA, VALB,
   VALC, VALD has changed a monitor event is posted for that field.

7. Process the forward link.

8. Set PACT to FALSE and exit.

**7.5 Device Support**

   The only device currently supported is Soft Channel.

.. _capfast-5:

 7.6 CapFast
~~~~~~~~~~~

   There are two CapFast symbols for the lutin record: *elutins.sym* and
   *elutin.sym*. These symbols are part of the epics3.12.2Gem.4 and
   later distributions. The symbols are shown in Figure 8 on page 30.

**FIGURE 8.** CapFast *elutins* and *elutin* symbols\ :sub:`.`

== ============================================
\  ======= ================ =========== =======
      SELB    lutin            FLNK     
                                        
              Soft Channel              
   ======= ================ =========== =======
      FLD0                     FLD2     
      FLD1 LUT in           record         FLD3
      VALA                                 VAL
   \          SCAN:Passive  FDIR:       
                                        
              PHAS:0        FNAM:       
                                        
              EVNT:0        SELB:15     
                                        
              PINI:NO       FTVA:STRING 
                                        
              PRIO:LOW      FTVB:STRING 
                                        
              PREC:2        FTVC:STRING 
                                        
              DISV:1        FTVD:STRING 
                                        
              DISS:NO_ALARM             
      INPA                                 NVAL
      VALB                                 LTBL
      INPB                              
      VALC                              
      INPC                              
      VALD                              
      INPD                              
      LOAD                              
      LLNK                              
      SLNK                              
   \                                    
   ======= ================ =========== =======
== ============================================

..

   .

.. _future-enhancements-1:

 7.7 Future Enhancements
~~~~~~~~~~~~~~~~~~~~~~~

   Some future enhancements the lutin record may be:

-  Access of LTBL across a network.

-  Support for more data types.

-  Per-item additions to the lookup table.

-  Better return of the list of tags.

8.0 mosub - Multiple Output Subroutine Record
---------------------------------------------

   The material presented here is taken from [4].

.. _introduction-1:

8.1 Introduction
~~~~~~~~~~~~~~~~

   The Multiple Output Subroutine record was developed for two reasons.
   Firstly, to provide a record which has more than one output field and
   secondly, as a record which can handle the transfer of ‘strings’
   across the database. Traditional EPICS records only have one output
   value. This is very restricting when dealing with an application
   which needs to transfer large amounts of data between records and
   leads to unnecessarily complicated database schematics. It is also
   true that with the ‘standard’ set of EPICS records, there is no way
   of passing a ‘string’ between two records. For example, there are
   ‘stringin’ and ‘stringout’ records, but the problem is, how do you
   get the string into the record in the first place? The Multiple
   Output Subroutine record solves both of these problems by adding
   extra functionality to the original EPICS ‘sub’ record.

.. _field-summary-5:

 8.2 Field Summary
~~~~~~~~~~~~~~~~~

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| VAL   | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SNAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SADR  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STYP  | SHORT | No    | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPA  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPB  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPC  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPD  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPE  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPF  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPG  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPH  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPI  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPJ  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPK  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPL  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPM  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTA  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTB  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTC  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTD  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTE  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTF  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUT1  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUT2  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUT3  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUT4  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUT5  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUT6  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| EGU   | S     | Yes   | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HOPR  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LOPR  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HIHI  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LOLO  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HIGH  | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| LOW   | FLOAT | Yes   | 0     | Yes   | Yes   | No    | Yes   |
+-------+-------+-------+-------+-------+-------+-------+-------+
| PREC  | SHORT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| BRSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HHSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LLSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HSV   | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LSV   | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| HYST  | D     | Yes   | 0     | Yes   | Yes   | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ADEL  | D     | Yes   | 0     | Yes   | Yes   | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MDEL  | D     | Yes   | 0     | Yes   | Yes   | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| A     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| B     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| C     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| D     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| E     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| F     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| G     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| H     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| I     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| J     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| K     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| L     | D     | No    | 0     | Yes   | Yes   | Yes   | Yes   |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| M     | S     | No    | Null  | Yes   | Yes   | Yes   | Yes   |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALA  | D     | No    | 0     | Yes   | Yes   | Yes   | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALB  | D     | No    | 0     | Yes   | Yes   | Yes   | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALC  | D     | No    | 0     | Yes   | Yes   | Yes   | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALD  | D     | No    | 0     | Yes   | Yes   | Yes   | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALE  | D     | No    | 0     | Yes   | Yes   | Yes   | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALF  | D     | No    | 0     | Yes   | Yes   | Yes   | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STR1  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STR2  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STR3  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STR4  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STR5  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STR6  | S     | No    | Null  | Yes   | Yes   | Yes   | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LA    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LB    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LC    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| LD    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LE    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LF    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LG    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LH    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LI    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LJ    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LK    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LL    | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LM    | S     | No    | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LVA   | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LVB   | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LVC   | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LVD   | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LVE   | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LVF   | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LS1   | S     | No    | 0     | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LS2   | S     | No    | 0     | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LS3   | S     | No    | 0     | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LS4   | S     | No    | 0     | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LS5   | S     | No    | 0     | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LS6   | S     | No    | 0     | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LALM  | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ALST  | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| MLST  | D     | No    | 0     | Yes   | No    | No    | No    |
|       | OUBLE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

.. _field-descriptions-4:

8.3 Field Descriptions
~~~~~~~~~~~~~~~~~~~~~~

+-----------+---------------------------+---------------------------+
| **Name**  | **Summary**               | **Description**           |
+===========+===========================+===========================+
| VAL       | Value Field               | This field is not used.   |
+-----------+---------------------------+---------------------------+
| INAM      | Initialisation            | This is the name of the   |
|           |                           | initialisation routine to |
|           | Routine                   | be called once, at        |
|           |                           | iocInit.                  |
+-----------+---------------------------+---------------------------+
| SNAM      | Process Routine           | This is the name of the   |
|           |                           | routine to be called when |
|           |                           | the record processes.     |
+-----------+---------------------------+---------------------------+
| SADR      | Subroutine                | Filled in by record       |
|           |                           | processing.               |
|           | Address                   |                           |
+-----------+---------------------------+---------------------------+
| STYP      | Subroutine                | Filled in by record       |
|           |                           | processing.               |
|           | Symbol Type               |                           |
+-----------+---------------------------+---------------------------+
| INPA,..., | Input Link A,...,         | The input links from      |
|           |                           | where the values of       |
| INPM      | Input Link M              | A,...,M are fetched       |
|           |                           | during record processing. |
+-----------+---------------------------+---------------------------+
| OUTA,..., | Output Link A,..          | The output links on which |
|           |                           | the DOUBLE values located |
| OUTF      | Output Link F             | at VALA,...,VALF are      |
|           |                           | placed during record      |
|           |                           | processing.               |
+-----------+---------------------------+---------------------------+
| OUT1,..., | Output Link 1,..          | The output links on which |
|           |                           | the STRINGS located at    |
| OUT6      | Output Link 6             |                           |
|           |                           | VAL1,...,VAL6 are placed  |
|           |                           | during record processing. |
+-----------+---------------------------+---------------------------+
| EGU       | Engineering               | ASCII string describing   |
|           |                           | Engineering units. This   |
|           | Units                     | field is used by record   |
|           |                           | support to supply a units |
|           |                           | description string when   |
|           |                           | *get_units* is called.    |
+-----------+---------------------------+---------------------------+
| HOPR      | High Operating            | These fields determine    |
|           |                           | the upper and lower       |
|           | Range                     | display limits for        |
|           |                           | graphical displays and    |
|           |                           | the upper and lower       |
|           |                           | control limits for        |
|           |                           | control displays. The     |
|           |                           | fields are used in the    |
|           |                           | record support routines:  |
|           |                           | *get_graphic_double* and  |
|           |                           | *get_control_double*.     |
+-----------+---------------------------+---------------------------+
| LOPR      | Low Operating             |                           |
|           |                           |                           |
|           | Range                     |                           |
+-----------+---------------------------+---------------------------+
| HIHI      | Hihi Alarm                | These fields specify the  |
|           |                           | alarm limits and          |
|           | Limit                     | severities. Note that     |
|           |                           | they are used as range    |
|           |                           | limits for checks against |
|           |                           | the VAL field. Since the  |
|           |                           | VAL field is not used by  |
|           |                           | this record, these fields |
|           |                           | are redundant.            |
+-----------+---------------------------+---------------------------+
| LOLO      | Lolo Alarm                |                           |
|           |                           |                           |
|           | Limit                     |                           |
+-----------+---------------------------+---------------------------+
| HIGH      | High Alarm                |                           |
|           |                           |                           |
|           | Limit                     |                           |
+-----------+---------------------------+---------------------------+
| LOW       | Low Alarm                 |                           |
|           |                           |                           |
|           | Limit                     |                           |
+-----------+---------------------------+---------------------------+
| BRSV      | Severity for a subroutine |                           |
|           | return value less than 0. |                           |
+-----------+---------------------------+---------------------------+
| HHSV      | Severity for a Hihi       |                           |
|           | Alarm.                    |                           |
+-----------+---------------------------+---------------------------+
| LLSV      | Severity for a Lolo       |                           |
|           | Alarm.                    |                           |
+-----------+---------------------------+---------------------------+
| HSV       | Severity for a High       |                           |
|           | Alarm.                    |                           |
+-----------+---------------------------+---------------------------+
| LSV       | Severity for a            |                           |
|           |                           |                           |
|           | Low Alarm                 |                           |
+-----------+---------------------------+---------------------------+
| **Name**  | **Summary**               | **Description**           |
+-----------+---------------------------+---------------------------+
| HYST      | Alarm                     | These parameters specify  |
|           |                           | hysteresis factors for    |
|           | Deadband                  | raising alarms and        |
|           |                           | posting logging and       |
|           |                           | monitor events for the    |
|           |                           | VAL field. As above,      |
|           |                           | since VAL is not used by  |
|           |                           | this record, these fields |
|           |                           | are redundant.            |
+-----------+---------------------------+---------------------------+
| ADEL      | Archive                   |                           |
|           |                           |                           |
|           | Deadband                  |                           |
+-----------+---------------------------+---------------------------+
| MDEL      | Monitor                   |                           |
|           |                           |                           |
|           | Deadband                  |                           |
+-----------+---------------------------+---------------------------+
| A,...,L   | A,...,L                   | The input fields which    |
|           |                           | hold the DOUBLE values    |
|           |                           | fetched in across the     |
|           |                           | input links               |
|           |                           | INPA,...,INPL.            |
+-----------+---------------------------+---------------------------+
| M         | M                         | The input field which     |
|           |                           | holds the STRING value    |
|           |                           | fetched in across the     |
|           |                           | input link INPM.          |
+-----------+---------------------------+---------------------------+
| VALA,..., | VALA,..., VALF            | These fields can hold any |
|           |                           | DOUBLE value that the     |
| VALF      |                           | user desires. They can be |
|           |                           | set from within the       |
|           |                           | subroutine called at      |
|           |                           | process time. The DOUBLES |
|           |                           | are placed on the output  |
|           |                           | links:                    |
|           |                           |                           |
|           |                           | OUTA,...,OUTF when the    |
|           |                           | record processes.         |
+-----------+---------------------------+---------------------------+
| STR1,..., | STR1,...,STR6             | These fields can hold any |
|           |                           | STRING value that the     |
| STR6      |                           | user desires. They can be |
|           |                           | set from within the       |
|           |                           | subroutine called at      |
|           |                           | process time. The STRINGS |
|           |                           | are placed on the output  |
|           |                           | links:                    |
|           |                           |                           |
|           |                           | OUT1,...,OUT6 when the    |
|           |                           | record processes.         |
+-----------+---------------------------+---------------------------+
| LA,...,LL | Last A,.., Last L         | Previous input DOUBLE     |
|           |                           | values. These fields are  |
|           |                           | used to decide when to    |
|           |                           | trigger monitors on       |
|           |                           | A,...,L.                  |
+-----------+---------------------------+---------------------------+
| LM        | Last M                    | Previous input STRING     |
|           |                           | value. This field is used |
|           |                           | to decide when to trigger |
|           |                           | a monitor on M.           |
+-----------+---------------------------+---------------------------+
| LVA,...,  | Last VALA,...,            | Previous output DOUBLE    |
|           |                           | values. These fields are  |
| LVF       | Last VALF                 | used to decide when to    |
|           |                           | trigger monitors on       |
|           |                           | VALA,...,VALF.            |
+-----------+---------------------------+---------------------------+
| LS1,...,  | Last STR1,...,            | Previous output STRING    |
|           |                           | values. These fields are  |
| LS6       | Last STR6                 | used to decide when to    |
|           |                           | trigger monitors on       |
|           |                           | STR1,...,STR6.            |
+-----------+---------------------------+---------------------------+
| LALM      | Last Alarm                | These fields are used to  |
|           |                           | implement the hysteresis  |
|           | Monitor                   | factors for monitors.     |
|           |                           | Again, since VAL is not   |
|           | Trigger Value             | used by this record,      |
|           |                           | these fields are          |
|           |                           | redundant.                |
+-----------+---------------------------+---------------------------+
| ALST      | Last Archiver Monitor     |                           |
|           |                           |                           |
|           | Trigger Value             |                           |
+-----------+---------------------------+---------------------------+
| MLST      | Last Value                |                           |
|           |                           |                           |
|           | Change                    |                           |
|           |                           |                           |
|           | Monitor                   |                           |
|           |                           |                           |
|           | Trigger Value             |                           |
+-----------+---------------------------+---------------------------+

.. _record-support-routines-6:

 8.4 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-6:

 8.4.1 init_record
^^^^^^^^^^^^^^^^^

   The VAL field is set equal to 0. For each constant input link to a
   DOUBLE value, the corresponding field is initialised with the
   constant value. For each input link that is of type PV_LINK, a
   channel access link is created. The STRING input field ‘M’ is
   initialised as a blank string and a channel access link is created
   for INPM. For each output DOUBLE and STRING link, a channel access
   link is created.

   The user initialisation routine, whose name is specified in INAM, is
   located and called. Note that the record assumes that an
   initialisation routine will always be specified. The record fails if
   no routine name is given.

   The routine specified in SNAM is located and its address and type are
   stored in SADR and STYP. These are used to call the routine when the
   record processes.

8.4.2 process
^^^^^^^^^^^^^

   The record can be made to process by writing to any of the input
   fields A-M. The process routine implements the following algorithm:

-  If PACT is FALSE, set PACT = TRUE and process the input links.

-  Call the routine specified in SNAM.

-  Process the output links.

-  Get the time stamp for this processing.

-  Check for alarms. Alarms are irrelevant in this record because the
   VAL field is not used.

-  Post events for value changes in the input fields and the output
   fields.

-  Process the record on the end of the forward link.

-  Set PACT = FALSE.

**8.4.3 get_value**

   Fills in the values of the *valueDes* structure so that they refer to
   VAL.

**8.4.4 get_units**

   Retrieves the Engineering Units string.

**8.4.5 get_precision**

   Retrieves the value of the PREC field.

8.4.6 get_graphic_double
^^^^^^^^^^^^^^^^^^^^^^^^

   Sets the upper and lower display limits for a field. If the field is
   one of A-D, LA-LD, VALA-VALF, HIHI, HIGH, LOW or LOLO, the limits are
   set to HOPR and LOPR.

8.4.7 get_control_double
^^^^^^^^^^^^^^^^^^^^^^^^

   Sets the upper and lower control limits for a field. If the field is
   one of A-D, LA-LD, VAL, HIHI, HIGH, LOW or LOLO, the limits are set
   to HOPR and LOPR.

.. _get_alarm_double-1:

8.4.8 get_alarm_double
^^^^^^^^^^^^^^^^^^^^^^

   Sets the following values:

   upper_alarm_limit = HIHI upper_warning_limit = HIGH
   lower_warning_limit = LOW lower_alarm_limit = LOLO

9.0 GenSub - The General Subroutine Record
------------------------------------------

.. _introduction-2:

 9.1 Introduction
~~~~~~~~~~~~~~~~

   This record, known as ‘GenSub’, has been designed as a replacement
   for the standard EPICS subroutine record. It allows the easy passage
   of arrays, scalars and user defined structures between records
   existing within the same database and between those which exist in
   separate IOC’s. The advantage of using arrays when transferring data
   between IOC’s, rather than a set of values from individual records,
   is that Channel Access guarantees to write the whole array with one
   *ca_put*. The atomicity of this operation insures that data at the
   receiving end is consistent at any given time. This is important in
   many applications. The current Channel Access limit for the amount of
   data which can be transferred with a single *ca_put* is 16kB.

   Other features of the ‘GenSub’ record include the following:

-  Up to 10 input fields.

-  Up to 10 output fields.

-  The types of both input and output fields are completely configurable
   by the user.

-  The routine to be called at process time can be changed dynamically
   after the database has been loaded. The name of the routine can
   either be fetched in over a link from another record or written
   directly into the SNAM field.

-  The user can configure the record to decide when events will be
   posted for the output fields. This can be: Never, Always or just when
   any element of an array changes value.

-  The VAL field holds the value returned from the routine called during
   record processing.

.. _field-summary-6:

9.2 Field Summary
~~~~~~~~~~~~~~~~~

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| VAL   | LONG  | No    | 0     | Yes   | Yes   | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVAL  | LONG  | No    | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SADR  | LONG  | No    | 0     | Yes   | No    | Yes   | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OSAD  | LONG  | No    | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| LFLG  | RECC  | Yes   | I     | Yes   | Yes   | No    | No    |
|       | HOICE |       | gnore |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| EFLG  | RECC  | Yes   | A     | Yes   | Yes   | No    | No    |
|       | HOICE |       | lways |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SUBL  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| SNAM  | S     | Yes   | Null  | Yes   | Yes   | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| ONAM  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| STYP  | SHORT | No    | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| BRSV  | GBLC  | Yes   | 0     | Yes   | Yes   | No    | Yes   |
|       | HOICE |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| PREC  | SHORT | Yes   | 0     | Yes   | Yes   | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPA  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPB  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPC  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPD  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPE  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPF  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPG  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPH  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPI  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| INPJ  | I     | Yes   | 0     | No    | No    | N/A   | No    |
|       | NLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFA   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFB   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFC   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFD   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFE   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFF   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFG   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFH   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFI   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFJ   | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| A     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| B     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| C     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| D     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| E     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| F     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| G     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| H     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| I     | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| J     | NOA   | No    | 0     | No    | Yes   | No    | Yes   |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTA   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTB   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTC   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTD   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTE   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTF   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTG   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTH   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTI   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTJ   | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOA   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOB   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOC   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOD   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOE   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOF   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOG   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOH   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOI   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOJ   | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTA  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTB  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTC  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTD  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTE  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTF  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTG  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTH  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTI  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OUTJ  | OU    | Yes   | 0     | No    | No    | N/A   | No    |
|       | TLINK |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVA  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| UFVB  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVC  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVD  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVE  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVF  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVG  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVH  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVI  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| UFVJ  | S     | Yes   | Null  | Yes   | No    | No    | No    |
|       | TRING |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALA  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALB  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALC  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALD  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALE  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALF  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALG  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALH  | NOA   | No    | 0     | No    | Yes   | Y     | No    |
|       | CCESS |       |       |       |       | es/No |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALI  | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| VALJ  | NOA   | No    | 0     | No    | Yes   | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLA  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLB  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLC  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLD  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLE  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLF  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLG  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLH  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLI  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| OVLJ  | NOA   | No    | 0     | No    | No    | No    | No    |
|       | CCESS |       |       |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVA  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVB  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVC  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVD  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVE  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVF  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVG  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVH  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| FTVI  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

+-------+-------+-------+-------+-------+-------+-------+-------+
| **Fi  | **T   | **    | *     | **Acc | **Mod | **Rec | *     |
| eld** | ype** | DCT** | *Init | ess** | ify** | Proc  | *PP** |
|       |       |       | ial** |       |       | Moni  |       |
|       |       |       |       |       |       | tor** |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| FTVJ  | GBLC  | Yes   | D     | Yes   | No    | No    | No    |
|       | HOICE |       | ouble |       |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVA  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVB  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVC  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVD  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVE  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVF  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVG  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVH  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVI  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| NOVJ  | ULONG | Yes   | 1     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVA  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVB  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVC  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVD  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVE  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVF  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVG  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVH  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVI  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+
| TOVJ  | ULONG | Yes   | 0     | Yes   | No    | No    | No    |
+-------+-------+-------+-------+-------+-------+-------+-------+

.. _field-descriptions-5:

9.3 Field Descriptions
~~~~~~~~~~~~~~~~~~~~~~

+-----------+---------------------------+---------------------------+
| **Name**  | **Summary**               | **Description**           |
+===========+===========================+===========================+
| VAL       | Value returned from       | This field holds the      |
|           | process routine           | value returned from the   |
|           |                           | user defined process      |
|           |                           | routine.                  |
+-----------+---------------------------+---------------------------+
| OVAL      | Old VAL                   | Previous VAL, used to     |
|           |                           | decide when to post       |
|           |                           | events.                   |
+-----------+---------------------------+---------------------------+
| SADR      | Subroutine                | The address of the        |
|           |                           | routine called at process |
|           | Address                   | time.                     |
+-----------+---------------------------+---------------------------+
| OSAD      | Old SADR                  | Previous SADR, used to    |
|           |                           | decide when to post       |
|           |                           | events.                   |
+-----------+---------------------------+---------------------------+
| LFLG      | Link Flag                 | Tells the record whether  |
|           |                           | to read or ignore the     |
|           |                           | SUBL link. If the value   |
|           |                           | is READ, then the name of |
|           |                           | the subroutine to be      |
|           |                           | called at process time is |
|           |                           | read from SUBL. If the    |
|           |                           | value is IGNORE, the name |
|           |                           | of the subroutine is that |
|           |                           | currently held in         |
|           |                           |                           |
|           |                           | SNAM.                     |
+-----------+---------------------------+---------------------------+
| EFLG      | Event Flag                | Tells the record when to  |
|           |                           | post events on the output |
|           |                           | fields VALA,...,VALJ. If  |
|           |                           | the value is NEVER,       |
|           |                           | events are never posted.  |
|           |                           | If the value is ALWAYS,   |
|           |                           | events are posted         |
|           |                           | everytime the record      |
|           |                           | processes. If the value   |
|           |                           | is ON CHANGE, events are  |
|           |                           | posted when any element   |
|           |                           | of an array changes       |
|           |                           | value.                    |
|           |                           |                           |
|           |                           | Archiving and Value       |
|           |                           | Change events are posted  |
|           |                           | in each case.             |
+-----------+---------------------------+---------------------------+
| SUBL      | Subroutine Link           | Where to get the          |
|           |                           | subroutine name from.     |
+-----------+---------------------------+---------------------------+
| INAM      | Initialisation            | This is the name of the   |
|           |                           | initialisation routine to |
|           | Routine                   | be called once, at        |
|           |                           | iocInit.                  |
+-----------+---------------------------+---------------------------+
| SNAM      | Process Routine           | This is the name of the   |
|           |                           | routine to be called when |
|           |                           | the record processes.     |
|           |                           | Note, this can be         |
|           |                           | overwritten by the SUBL   |
|           |                           | link, if LFLG is set to   |
|           |                           | READ.                     |
+-----------+---------------------------+---------------------------+
| ONAM      | Process Routine           | Old process subroutine    |
|           |                           | name.                     |
+-----------+---------------------------+---------------------------+
| STYP      | Subroutine                | Filled in by record       |
|           |                           | processing.               |
|           | Symbol Type               |                           |
+-----------+---------------------------+---------------------------+
| BRSV      | Severity for a subroutine | Specifies the Alarm       |
|           | return value less than 0. | severity.                 |
+-----------+---------------------------+---------------------------+
| PREC      | Display                   | Specifies the number of   |
|           |                           | decimal places with which |
|           | Precision                 | to display the values of  |
|           |                           | the fields VALA,...,VALJ. |
+-----------+---------------------------+---------------------------+
| INPA,..., | Input Link A,...,         | The input links from      |
|           |                           | where the values of       |
| INPJ      | Input Link J              | A,...,J are fetched       |
|           |                           | during record processing. |
+-----------+---------------------------+---------------------------+
| UFA,...,  | User Function A           | These are the names of    |
|           |                           | functions which return    |
| UFJ       | User Function J           | the sizes of any user     |
|           |                           | defined structures to be  |
|           |                           | received in the input     |
|           |                           | fields A,...,J.           |
+-----------+---------------------------+---------------------------+
| A,...,J   | Input Fields              | The input fields which    |
|           |                           | hold the scalar values or |
|           |                           | arrays fetched in across  |
|           |                           | the input links           |
|           |                           | INPA,...,INPJ.            |
+-----------+---------------------------+---------------------------+
| FTA,...,  | Field Type of A           | Field types of the input  |
|           |                           | values. These can be      |
| FTJ       | Field Type of J           | CHAR, STRING, DOUBLE,     |
|           |                           | LONG, etc.                |
+-----------+---------------------------+---------------------------+
| **Name**  | **Summary**               | **Description**           |
+-----------+---------------------------+---------------------------+
| NOA...,   | Number of                 | The number of elements in |
|           |                           | each input field. Default |
| NOJ       | elements in A,..          | is 1 (scalar value). An   |
|           |                           | array is specified by     |
|           | Number of elements in J   | setting this field to     |
|           |                           | greater than 1.           |
+-----------+---------------------------+---------------------------+
| OUTA,..., | Output Link A,..          | The output links on which |
|           |                           | the scalars or arrays     |
| OUTJ      | Output Link J             | located at VALA,...,VALJ  |
|           |                           | are placed during record  |
|           |                           | processing.               |
+-----------+---------------------------+---------------------------+
| UFVA,..., | User Function VALA,...,   | These are the names of    |
|           |                           | functions which return    |
| UFVJ      | User Function             | the sizes of any user     |
|           |                           | defined structures which  |
|           | VALJ                      | are to passed out of the  |
|           |                           | record from the fields    |
|           |                           | VALA,...,VALJ.            |
+-----------+---------------------------+---------------------------+
| VALA,..,  | Output Fields             | The output fields which   |
|           |                           | hold the scalar values or |
| VALJ      |                           | arrays pushed out across  |
|           |                           | the output links          |
|           |                           | OUTA,...,OUTJ.            |
+-----------+---------------------------+---------------------------+
| FTVA,..., | Field Type of             | Field types of the output |
|           |                           | values. These can be      |
| FTVJ      | VALA                      | CHAR, STRING, DOUBLE,     |
|           |                           | LONG, etc.                |
|           | Field Type of             |                           |
|           |                           |                           |
|           | VALJ                      |                           |
+-----------+---------------------------+---------------------------+
| OVLA,..,  | Previous                  | The previous values of    |
|           |                           | the outputs. These are    |
| OVLJ      | Outputs                   | used to decide when to    |
|           |                           | post events if EFLG is    |
|           |                           | set to ON CHANGE.         |
+-----------+---------------------------+---------------------------+
| NOVA,..., | Number of elements in     | The number of elements in |
|           |                           | each output field.        |
| NOVJ      | VALA                      | Default is 1 (scalar      |
|           |                           | value). An array is       |
|           | Number of elements in     | specified by setting this |
|           |                           | field to greater than 1.  |
|           | VALJ                      |                           |
+-----------+---------------------------+---------------------------+
| TOVA,..., | Total Number of bytes in  | The total number of bytes |
|           | VALA                      | in each output field.     |
| TOVJ      |                           | These are used internally |
|           | Total Number of bytes in  | by record processing and  |
|           | VALJ                      | do not concern the user.  |
+-----------+---------------------------+---------------------------+

.. _record-support-routines-7:

 9.4 Record Support Routines
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _init_record-7:

 9.4.1 init_record
^^^^^^^^^^^^^^^^^

   This routine is called twice at *iocInit*. On the first call it does
   the following:

-  Look for any user functions defined in the fields UFA-UFJ and
   UFVA-UFVJ. If they have been defined, call them to get the size of
   the structure which is to passed across the link. If they are not
   defined, no routine is called.

-  Calloc sufficient space to hold the number of input scalars and/or
   arrays defined by the settings of the fields FTA-FTJ and NOA-NOJ. If
   a user function has been defined, calloc the space required by the
   multiple of the number of elements and size returned from the user
   function.

-  Calloc sufficient space to hold the number of output scalars and/or
   arrays defined by the settings of the fields FTVA-FTVJ and NOVA-NOVJ.
   If a user function has been defined, calloc the space required by the
   multiple of the number of elements and size returned from the user
   function. For the output fields, also calloc space to hold the
   previous value of a field. This is required when the decision is made
   on whether or not to post events.

..

   On the second call, it does the following:

-  Create the SUBL link.

-  Create each input link.

-  Create each output link.

-  If the field INAM is set, look-up the address of the routine and call
   it.

-  If the field LFLG is set to IGNORE and SNAM is defined, look-up the
   address of the process routine.

.. _process-1:

9.4.2 process
^^^^^^^^^^^^^

   This routine implements the following algorithm:

-  Set PACT to TRUE.

-  If the field LFLG is set to READ, get the subroutine name from the
   SUBL link. If the name is not NULL and it is not the same as the
   previous subroutine name, lookup the subroutine address. Set the old
   subroutine name, ONAM, equal to the current name, SNAM.

-  Fetch the values from the input links.

-  Call the routine specified by SNAM.

-  Set VAL equal to the return value from the routine specified by SNAM.

-  Place the output values on the output links.

-  Get the time of processing and put it into the timestamp field.

-  If the subroutine address has changed, post a change-of-value event
   and a log event for the SADR field. If VAL has changed, post a
   change-of value and log event for this field. If EFLG is set to
   ALWAYS, post change-of-value and log events for every output field.
   If EFLG is set to ON CHANGE, post change-of-value and log events for
   every output field which has changed. In the case of an array, an
   event will be posted if any single element of the array has changed.
   If EFLG is set to NEVER, no change-of-value or log events are posted
   for the output fields.

-  Process the record on the end of the forward link, if one exists.

-  Set PACT to FALSE.

**9.4.3 get_value**

   Fills in the values of struct *valueDes* so that they refer to VAL.

9.4.4 get_precision
^^^^^^^^^^^^^^^^^^^

   Sets the display precision to the value of PREC for any of the output
   fields VALA,..., VALJ. This routine could be called for any of these
   fields.

.. _cvt_dbaddr-3:

 9.4.5 cvt_dbaddr
^^^^^^^^^^^^^^^^

   The purpose of this routine is to fill in the struct *dbAddr* for the
   field of the record for which it has been called. Typically, the
   number of elements in the field, the field type and the size of the
   field will be set in this routine. For arrays, this record support
   routine is essential.

 9.4.6 get_array_info
^^^^^^^^^^^^^^^^^^^^

   This routine returns the current number of elements and the offset of
   the first value for an array. For this record, the offset field is
   always 0.

**9.4.7 put_array_info**

   This routine is called after new values have been placed in an array.

.. _special-3:

 9.4.8 special
^^^^^^^^^^^^^

   This routine is called whenever the SNAM field changes. It is called
   twice, once before the change and once after. On the first call, the
   routine simply returns. On the second call, after SNAM has changed,
   it implements the following algorithm:

-  If LFLG is set to IGNORE and SNAM is not NULL, then look-up the
   address of the routine specified by SNAM. Set the SADR field equal to
   the subroutine address.

-  Post change-of-value and log events for the SADR field, if this has
   changed.

 9.5 Use of the ‘GenSub’ Record
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   Two ‘GenSub’ records can be used to transfer data between one
   another. These records can be located in the same IOC or in separate
   IOC’s. The data can be a scalar of any type, an array, a user defined
   structure or an array of user defined structures.

   Let us name the ‘GenSub’ record which is sending data, record A, and
   the ‘GenSub’ record which is receiving the data, record B. There are
   some fields which must be set-up correctly in each record before the
   data transfer can occur without error. The output field types and the
   number of elements in the output fields of record A must match the
   input field types and number of elements in the input fields of
   record B. Thus, combining the two records is rather like a jigsaw.
   Let us take an example.

   If 5 doubles are to be passed from the VALA field of record A to the
   A field of record B, then the following settings are necessary:

   Record A should have: FTVA = DOUBLE, NOVA = 5.

   Record B should have: FTA = DOUBLE, NOA = 5.

 9.6 Use of User Defined Structures
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   It is possible for the user to define a structure which is to be
   passed between two ‘GenSub’ records. As an example, let us imagine
   that the following structure needs to be transferred between the VALB
   field of record A and the B field of record B:

   *struct pinfo {*

   *int age;*

   *char name[128];*

   *char posn[128]; double salary; };*

   The user must supply a function which will return the size of this
   structure. An example, and template for such a function is given
   below:

   *#include <vxWorks.h>*

   *#include <types.h>*

   *#include <time.h>*

   *#include <stdlib.h>*

   *#include <stdioLib.h>*

   *#include <dbEvent.h>*

   *#include <dbDefs.h>*

   *#include <dbCommon.h>*

   *#include <recSup.h>*

   *#include <GenSubRecord.h> #include <pinfo.h>*

   *long setup( struct GenSubRecord \*pgsub )*

   *{*

   *return( sizeof(struct pinfo) ); }*

   The user should set the following fields:

   Record A: UFVB:setup

   FTVB:CHAR

   NOVB: 1

   Record B: UFB: setup

   FTB: CHAR NOB: 1

   These settings indicate that a single structure, of the size of what
   is returned from *setup,* will be passed from VALB of record A to B
   of record B. Inside the process routine called from record B, the
   user should cast the B field as a pointer to the structure, thus:

   s\ *truct pinfo \*ex; ex = (struct pinfo \*)pgsub->b;*

   The elements of the structure then become available to the routine.
   Note that the user is responsible for ensuring that the two ‘GenSub’
   records, which may be in separate IOC’s, share identical layouts of
   the structure.

   It is also worth pointing out here, that because we are packing the
   structure into a stream of characters, character arrays within the
   structure are not limited to the 40 character limit imposed on
   strings for normal record fields. In this example, we have used
   character arrays dimensioned to 128.

   The total size of the structure must be less than 16kB. Internal
   ‘GenSub’ record code checks for this.

 9.7 Dynamically Changing the User Routine called during Record Processing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   The ‘GenSub’ record allows the user to dynamically change which
   routine is called when the record processes. This can be done in two
   ways:

-  The LFLG field can be set to READ so that the name of the routine is
   read from the SUBL link. Thus, whatever is feeding this link can
   change the name of the routine before the ‘GenSub’ record is
   processed. In this case, the record looks in the symbol table for the
   symbol name whenever the name of routine fetched from the link
   changes.

-  The LFLG field can be set to IGNORE. In this case, the rotine called
   during record processing is that specified in the SNAM property
   field. Under these conditions, the SNAM field can be changed by a
   Channel Access write to the SNAM field. Thus, during development
   work, when it is often required to run a modified version of the
   routine, it will no longer be a requirement to reboot the IOC and
   reload the database. A new routine will be called during record
   processing if the routine is loaded with the vxWorks *ld* command,
   and *cau* is used to put the name of the routine into the record’s
   SNAM field. After the SNAM field has been changed, the record
   automatically looks up the symbol name in the symbol table. Note
   that, if the same routine name is used, this is not a problem. The
   record finds the latest version of the code which has been loaded.
   Obviously, one needs to take care of the amount of memory which is
   used, if no *unld* command is ever used.
