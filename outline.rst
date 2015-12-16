===========
Ansible (3)
===========

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
``ansible-playbook playbook.yml [OPTIONS]``

.. class:: notes

  playbook.yml goes right after the command
  a less often encountered way of handing this
  in my experiance, you can also put the playbook at the end

The tags option
===============

.. class:: fragment current-visible collapsable-fragment

  .. raw:: html

    For running a specific part or parts of a playbook, <br/>
    rather than the whole thing.

.. class:: fragment current-visible collapsable-fragment

  Both plays and tasks support the “tags:” attribute.

.. class:: fragment current-visible collapsable-fragment

  **For overriding tag selection: always**
  ``ansible-playbook playbook.yml --tags "always"``

.. class:: fragment current-visible collapsable-fragment

  **For selecting via meta:  ‘tagged’, ‘untagged’ and ‘all’**
  ``ansible-playbook playbook.yml --tags "tagged"``
  ``ansible-playbook playbook.yml --tags "untagged"``
  ``ansible-playbook playbook.yml --tags "all"``

.. class:: fragment current-visible collapsable-fragment

  **For including multiple tags:**
  ``ansible-playbook playbook.yml --tags "tag1,tag2"``

.. class:: fragment current-visible collapsable-fragment

  **For excluding tags:**
  ``ansible-playbook playbook.yml --skip-tags=tag3``


The list options
================

.. class:: fragment current-visible collapsable-fragment

  For listing out parts of the playbook. It is very useful to check your playbook and hosts files before using them.

.. class:: fragment current-visible collapsable-fragment

  **For listing hosts:**
  ``ansible-playbook playbook.yml --list-hosts``

.. class:: fragment current-visible collapsable-fragment

  **For listing all available tags:**
  ``ansible-playbook playbook.yml --list-tags``

.. class:: fragment current-visible collapsable-fragment

  **For listing all tasks that would be executed:**
  ``ansible-playbook playbook.yml --list-tasks``


The task options
================

.. class:: fragment current-visible collapsable-fragment

  **To start your playbook at a task (by name):**
  ``ansible-playbook playbook.yml --start-at-task="task1"``

.. class:: fragment current-visible collapsable-fragment

  **To perform a syntax check on your playbook:**
  ``ansible-playbook playbook.yml --syntax-check``

.. class:: fragment current-visible collapsable-fragment

  **To step though the tasks in your playbook:**

  ``ansible-playbook playbook.yml --step``

.. class:: notes

    step prompts for confirmation before running a task


The ansible command
===================

``ansible php --sudo --ask-sudo-pass -m shell -a "ufw status verbose"``

The playbook
============


**Include tasks from another file:**
``  - include: tasks/more_tasks.yml``
Be sure to only include a list of tasks. It is not another playbook. But it also requires a document separator ``---``

You can even go so far as to include variables:
``  - include: tasks/more_tasks.yml var1=testing``


Heavy Shell Action
==================

.. code:: yaml
  - name: local action math
    local_action: shell {{ IPOctet }}=$(echo {{ ServerIPRange|int }}/{{ epcrange|int }}+{{ IPOctet|int }}" | bc)
    with_sequence: start=1 end=4
    register: result
    ignore_errors: yes

You can iterate over these results using result.stdout_lines:

- name: iterate results
  local_action: debug msg={{item}}
  with_items: result.stdout_lines

http://stackoverflow.com/a/25452182

Dynamic Inventory
=================

http://docs.ansible.com/ansible/intro_dynamic_inventory.html

Modules
=======

----

Core Modules
------------
http://docs.ansible.com/ansible/debug_module.html
Prints statements during execution, which can include variables.
``  - debug: msg="System {{ inventory_hostname }}``



Formatting 3 Ways
=================

  - name: Copy Phergie shell script into place.
    template: src="templates/phergie.sh.j2" dest="/home/{{ phergie_user }}/phergie.sh" owner="{{ phergie_user }}" group="{{ phergie_user }}" mode=0755

  - name: Copy Phergie shell script into place.
    template:
      src: "templates/phergie.sh.j2"
      dest: "/home/{{ phergie_user }}/phergie.sh"
      owner: "{{ phergie_user }}"
      group: "{{ phergie_user }}"
      mode: 0755

  - name: Copy Phergie shell script into place.
    template: >
      src="templates/phergie.sh.j2"
      dest="/home/{{ phergie_user }}/phergie.sh"
      wner="{{ phergie_user }}"
      roup="{{ phergie_user }}"
      mode=0755

.. class:: notes
  The last way can actually be used with anything that needs to wrap.

https://servercheck.in/blog/yaml-best-practices-ansible-playbooks-tasks

Recursive Syntax Checking
=========================

find ./playbooks -name '*.yml' -depth 1 | xargs -n1  ansible-playbook --syntax-check --list-tasks -i tests/ansible_hosts

https://raymii.org/s/tutorials/Ansible_-_Playbook_Testing.html


COWSAY Easter Egg
=================
If cowsay is installed, Ansible takes it upon itself to make your day happier when running playbooks. If you decide that you would like to work in a professional cow-free environment, you can either uninstall cowsay, or set an environment variable:

export ANSIBLE_NOCOWS=1

https://support.ansible.com/hc/en-us/articles/201957877-How-do-I-disable-cowsay-



Resources and Credits
=====================

https://gist.github.com/marktheunissen/2979474
http://stackoverflow.com/questions/23945201/how-to-run-only-one-task-in-ansible-playbook
http://docs.ansible.com/ansible/playbooks_tags.html
