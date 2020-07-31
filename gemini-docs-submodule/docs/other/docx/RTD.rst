Read The Docs is a free and complete document hosting service.\ |image0|

Concept of Operations

Use RTD as the primary documentation service for real time documentation
========================================================================

RTD would provide the following benefits 
----------------------------------------

-  Host real time documentation on a public website

-  Complete search facility for all hosted documentation

-  Version control

-  Format conversions

-  Unified appearance

Potential documents that could be hosted on RTD
-----------------------------------------------

-  ADE Documentation

-  ADE 2 Documentation

-  Use Cases and requirements for ADE systems (Google docs)

-  SW Development standards (DMT)

-  CCB Functional Requirements

-  Release notes

-  VME boot/info/settings

-  Instructions/How-Tos

Steps to use RTD for real time documentation
============================================

1. .. rubric:: Migrate existing real time documents to RTD
      :name: migrate-existing-real-time-documents-to-rtd

   a. Compile complete list of documents

   b. Convert documents

   c. Verify conversions

   d. Add to repos

   e. Publish to RTD

2. .. rubric:: Using RTD for new real time documents
      :name: using-rtd-for-new-real-time-documents

   a. Learn to write rst

   b. Write documentation

   c. Add to repos

   d. Publish to RTD

Gemini RTD Experiments

Read The Docs Workflow 
======================

Figure 1

.. _section-1:

RTD Experiment Team
===================

5 engineers 40 hours

Conversion Examples
===================

Gemini ADE conversion from PDF to rst and published on RTD
----------------------------------------------------------

|image1|

Gemini Record Reference Manual
------------------------------

|image2|

Results
=======

PDF
---

Most PDF conversions are fine without any modifications and can be
directly uploaded to RTD. However, many technical drawings and graphics
in PDFs do not convert properly and would either need to be manually
redrawn. Another option would be to link PDFs that do not convert
properly.

WikiHTML
--------

Wiki/HTML conversions generally work without issues. Some minor
formatting issues did arise from the Wikis that required manual fixes.
Tables of contents did not always convert properly.

Google Docs and Word documents
------------------------------

Google Docs and Word (docx) documents generally converted without any
issues. Graphics and images occasionally need to be fixed.

Feedback from engineers
=======================

Initial resistance to learning new system

General positive feedback

Comments to use RTD tools and hosting locally

Using Read The Docs with existing Gemini Documents

Training
========

RTD uses rst markup language. Rst is relatively easy to use, however,
rst is not intuitive. A short training for engineers using RTD would
save time in the long run. A 1 to 2 hour introduction to rst and RTD
would be a good start.

Searching
=========

RTD provides a standardized hosting and search environment. Gemini will
still have documentations located in different locations, however,

Collaboration
=============

Collaboration on Google docs is hard to beat. docs is a better option
for live documents.

Time Saving
===========

Implementation Estimate

Read The Docs work unit estimates

+-------------+-------------+-------------+----------+-------------+
| Unit        | Description | Time        | Quantity | Estimate    |
|             |             | (hours)     |          | (hours)     |
+=============+=============+=============+==========+=============+
| Cr          | Create a    | 0.1         | 10       | 1           |
| eate/update | new Git or  |             |          |             |
| repo        | SVN repo to |             |          |             |
|             | hold docs   |             |          |             |
|             | or add docs |             |          |             |
|             | to an       |             |          |             |
|             | existing    |             |          |             |
|             | repo.       |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| List/find   |             | 8           | 1        | 8           |
| all real    |             |             |          |             |
| time docs   |             |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Convert pdf | Convert PDF | 0.25        | 100      | 25          |
|             | docs using  |             |          |             |
|             | pandoc and  |             |          |             |
|             | Word. Check |             |          |             |
|             | docs for    |             |          |             |
|             | accurate    |             |          |             |
|             | and usable  |             |          |             |
|             | conversion. |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Convert doc | Convert     | 0.1         | 100      | 10          |
|             | Good Docs   |             |          |             |
|             | using       |             |          |             |
|             | pandoc.     |             |          |             |
|             | Check docs  |             |          |             |
|             | for         |             |          |             |
|             | accurate    |             |          |             |
|             | and usable  |             |          |             |
|             | conversion. |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Convert     | Convert     | 0.15        | 100      | 15          |
| Wiki/HTML   | Wiki/HTML   |             |          |             |
|             | docs using  |             |          |             |
|             | pandoc.     |             |          |             |
|             | Check docs  |             |          |             |
|             | for         |             |          |             |
|             | accurate    |             |          |             |
|             | and usable  |             |          |             |
|             | conversion. |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Write       | Write new   | 0.01        | 100      | 1           |
| documents   | rst         |             |          |             |
|             | documents.  |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Create      | Create      | 16          | 1        | 16          |
| automation  | scripts to  |             |          |             |
| scripts     | au          |             |          |             |
|             | tomatically |             |          |             |
|             | convert     |             |          |             |
|             | large       |             |          |             |
|             | resources   |             |          |             |
|             | of          |             |          |             |
|             | documents.  |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Publish     | Push docs   | 0.01        | 10       | 0.1         |
|             | to RTD and  |             |          |             |
|             | check for   |             |          |             |
|             | finished    |             |          |             |
|             | build.      |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Create      | Create      | 16          | 1        | 16          |
| Training    | i           |             |          |             |
| Material    | nstructions |             |          |             |
|             | and how-tos |             |          |             |
|             | for basic   |             |          |             |
|             | rst and RTD |             |          |             |
|             | use cases   |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Misc.       | Meetings,   | 8           | 1        | 8           |
| adm         | mail,       |             |          |             |
| inistration | or          |             |          |             |
|             | ganization, |             |          |             |
|             | com         |             |          |             |
|             | munication, |             |          |             |
|             | etc.        |             |          |             |
+-------------+-------------+-------------+----------+-------------+
| Contingency |             | 20          | 1        | 20          |
+-------------+-------------+-------------+----------+-------------+
| Imp         | 120.1       |             |          |             |
| lementation |             |             |          |             |
| Estimate    |             |             |          |             |
| (hours)     |             |             |          |             |
+-------------+-------------+-------------+----------+-------------+

|image3|

.. |image0| image:: media/media/image9.png
   :width: 6.35938in
   :height: 5.62281in
.. |image1| image:: media/media/image7.png
   :width: 8.2777in
   :height: 4.04184in
.. |image2| image:: media/media/image5.png
   :width: 8.27604in
   :height: 6.35983in
.. |image3| image:: media/media/image2.png
   :width: 6.5in
   :height: 4.01389in
