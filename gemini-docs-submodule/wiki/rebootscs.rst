Reboot SCS
==========

From SWG

Jump to: `navigation <#column-one>`__, `search <#searchInput>`__

-  Flatten the mirror. On SCS DM screen Master->System Status. Beam A,
   XYZ till should be close to the same values. To flatten, go to the
   Master->Move page and set all values to zero. The two numbers at the
   bottom (XY position) should have the values in the, Positioner "TCS
   coords" on the System Status page (upper rightish) copied over.

-  Mirror control and Vibration Loop should be, "OFF". Turn these off on
   Master->Control(Primitive Commands) DM page. Use VEND and MEND to
   turn off).

-  After the reboot, be sure the followers all go white (around -8000,
   somewhere).

-  As a courtesy for whoever uses the telescope after you, put the
   baffles back to where they were before you reboot.

-  You can use dbpf m2:iocStats:SysReset 1 to reboot from the command
   line.

|SCS Move.png|

|SCS Primitive.png|

|SCS Status.png|

Retrieved from
"http://swg.wikis-internal.gemini.edu/index.php?title=Reboot_SCS&oldid=20024"

Views
'''''

-  `Page </index.php/Reboot_SCS>`__
-  `Discussion </index.php?title=Talk:Reboot_SCS&action=edit&redlink=1>`__
-  `Edit </index.php?title=Reboot_SCS&action=edit>`__
-  `History </index.php?title=Reboot_SCS&action=history>`__

Personal tools
''''''''''''''

-  `Log in / create
   account </index.php?title=Special:UserLogin&returnto=Reboot+SCS>`__

Navigation
''''''''''

-  `Main page </index.php/Main_Page>`__
-  `Community portal </index.php/SWG:Community_portal>`__
-  `Current events </index.php/SWG:Current_events>`__
-  `Recent changes </index.php/Special:RecentChanges>`__
-  `Random page </index.php/Special:Random>`__
-  `Help </index.php/Help:Contents>`__

Search
''''''

 

Toolbox
'''''''

-  `What links here </index.php/Special:WhatLinksHere/Reboot_SCS>`__
-  `Related
   changes </index.php/Special:RecentChangesLinked/Reboot_SCS>`__
-  `Special pages </index.php/Special:SpecialPages>`__
-  `Printable version </index.php?title=Reboot_SCS&printable=yes>`__
-  `Permanent link </index.php?title=Reboot_SCS&oldid=20024>`__

|Powered by MediaWiki|

-  This page was last modified on 5 March 2020, at 09:54.
-  This page has been accessed 50 times.
-  `Privacy policy </index.php/SWG:Privacy_policy>`__
-  `About SWG </index.php/SWG:About>`__
-  `Disclaimers </index.php/SWG:General_disclaimer>`__

.. |SCS Move.png| image:: media/rId25.png
   :width: 5.83333in
   :height: 6.11209in
   :target: /index.php/File:SCS_Move.png
.. |SCS Primitive.png| image:: media/rId27.png
   :width: 5.83333in
   :height: 3.54976in
   :target: /index.php/File:SCS_Primitive.png
.. |SCS Status.png| image:: media/rId29.png
   :width: 5.83333in
   :height: 5.05731in
   :target: /index.php/File:SCS_Status.png
.. |Powered by MediaWiki| image:: media/rId81.png
   :width: 0.91667in
   :height: 0.32292in
   :target: //www.mediawiki.org/
