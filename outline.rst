=======
Ansible
=======

-------------------------
Getting in over our heads
-------------------------

:Author: Charles L. Yost
:Date: 2015-11

----

Description
===========

A 45 minute deep dive into Ansible. The inner workings, all the possiblities, and how to really get into some trouble with it. Jam-packed with examples, this talk is intended to be a living cookbook/cheatsheet for when Ansible has you over a barrel, or it's your turn in one.


Speaker Bio
===========

Charles Yost is currently a Security Developer at Binary Defense Systems. He has worked in the IT industry for over 10 years in a wide variety of roles including: Printer Technician, VoIP  Systems Administrator, .Net Developer, and Web Developer. Throughout life his number one passion has been learning new skills. He can often be found researching a topic, attempting to keep up with the quickly evolving field of technology. Charles enjoys teaching and talking to others about technology. He is a member of NEOISF, and attends as many InfoSec conferences as he can justify with his wife.


Contact
=======

Twitter: @CHARLESLYOST

GitHub: Yoshi325

| This Talk:
| https://github.com/Yoshi325/talks-ansible-3


"Watch that first step, it's a doozy!"
======================================

.. figure:: images/66C538AB-7D9C-4791-99D8-F359978DBABD.jpg

The ansible-playbook command
============================


The tags feature
================
For running a specific part of a playbook, rather than the whole thing. Both plays and tasks support the “tags:” attribute.

For overriding tag selection: always
------------------------------------

``ansible-playbook playbook.yml --tags "always"``

For selecting via meta:  ‘tagged’, ‘untagged’ and ‘all’
-------------------------------------------------------

``ansible-playbook playbook.yml --tags "tagged"``

``ansible-playbook playbook.yml --tags "untagged"``

``ansible-playbook playbook.yml --tags "all"``

For including multiple tags:
----------------------------

``ansible-playbook playbook.yml --tags "configuration,packages"``

For excluding tags:
-------------------

"only run plays and tasks whose tags do not match these values"
```ansible-playbook playbook.yml --skip-tags=SKIP_TAGS```






The list options
================
"outputs a list of matching hosts; does not execute anything else"
``ansible-playbook playbook.yml --list-hosts``

"list all available tags"
``ansible-playbook playbook.yml --list-tags``

"list all tasks that would be executed"
``ansible-playbook playbook.yml --list-tasks``


The start task at option
========================

"start the playbook at the task matching this name"

ansible-playbook playbook.yml --start-at-task=START_AT


The syntax check option
=======================

"perform a syntax check on the playbook, but do not execute it"

ansible-playbook playbook.yml --syntax-check


The step option
===============

"one-step-at-a-time: confirm each task before running"
ansible-playbook playbook.yml --step








Resources and Credits
=====================

https://gist.github.com/marktheunissen/2979474
http://stackoverflow.com/questions/23945201/how-to-run-only-one-task-in-ansible-playbook
http://docs.ansible.com/ansible/playbooks_tags.html
