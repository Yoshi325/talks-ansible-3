===========
Ansible 300
===========

-------------------------
Getting in over our heads
-------------------------

:Author: Charles L. Yost
:Date: 2015-12

.. role:: small-code(code)
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


Disclosure
==========

This talk is full of examples. I did not create them. I found them while researching. Proper credit for them can be found at the end in the "Credits" section.


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
  :small-code:`ansible-playbook playbook.yml --tags "always"`

.. class:: fragment current-visible collapsable-fragment

  **For selecting via meta:  ‘tagged’, ‘untagged’ and ‘all’**
  :small-code:`ansible-playbook playbook.yml --tags "tagged"`
  :small-code:`ansible-playbook playbook.yml --tags "untagged"`
  :small-code:`ansible-playbook playbook.yml --tags "all"`

.. class:: fragment current-visible collapsable-fragment

  **For including multiple tags:**
  :small-code:`ansible-playbook playbook.yml --tags "tag1,tag2"`

.. class:: fragment current-visible collapsable-fragment

  **For excluding tags:**
  :small-code:`ansible-playbook playbook.yml --skip-tags=tag3`


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
  :small-code:`ansible-playbook playbook.yml --start-at-task="task1"`

.. class:: fragment current-visible collapsable-fragment

  **To perform a syntax check on your playbook:**
  :small-code:`ansible-playbook playbook.yml --syntax-check`

.. class:: fragment current-visible collapsable-fragment

  **To step though the tasks in your playbook:**

  ``ansible-playbook playbook.yml --step``

.. class:: notes

    step prompts for confirmation before running a task


The ansible command
===================

.. code::

  ansible host-pattern   \
  --sudo --ask-sudo-pass \
  --module-name shell    \
  --args="ufw status verbose"


The playbook
============

**Include tasks from another file**

.. class:: fragment current-visible collapsable-fragment

  ``  - include: tasks/more_tasks.yml``

.. class:: fragment current-visible collapsable-fragment

  | Be sure to only include a list of tasks.
  | It is not another playbook.
  | But it also requires a document separator ``---``.

.. class:: fragment current-visible collapsable-fragment

  You can even go so far as to include variables:

  :small-code:`- include: tasks/more_tasks.yml var1=testing`


Formatting 3 Ways
=================

.. class:: fragment current-visible collapsable-fragment

  .. code::

    - name: Copy Phergie shell script into place.
      template: src="templates/phergie.sh.j2" dest="/home/{{ phergie_user }}/phergie.sh" owner="{{ phergie_user }}" group="{{ phergie_user }}" mode=0755

.. class:: fragment current-visible collapsable-fragment

  .. code::

    - name: Copy Phergie shell script into place.
      template:
        src: "templates/phergie.sh.j2"
        dest: "/home/{{ phergie_user }}/phergie.sh"
        owner: "{{ phergie_user }}"
        group: "{{ phergie_user }}"
        mode: 0755

.. class:: fragment current-visible collapsable-fragment

  .. code::

    - name: Copy Phergie shell script into place.
      template: >
        src="templates/phergie.sh.j2"
        dest="/home/{{ phergie_user }}/phergie.sh"
        wner="{{ phergie_user }}"
        roup="{{ phergie_user }}"
        mode=0755

.. class:: notes

  The last way can actually be used with anything that needs to wrap.

----


Recursive Syntax Checking
-------------------------

.. code::

  find ./playbooks -name '*.yml' -depth 1 \
  | xargs -n1                             \
      ansible-playbook                    \
          --syntax-check                  \
          --list-tasks                    \
          -i tests/ansible_hosts

----


Heavy Shell Action
==================

.. class:: fragment current-visible collapsable-fragment

  .. code::

    - name: local action math
      local_action: >
        shell {{ IPOctet }}=$(
        echo "{{ ServerIPRange|int }}
        /{{ epcrange|int }}
        +{{ IPOctet|int }}"
        | bc
        )
      with_sequence: start=1 end=4
      register: result
      ignore_errors: yes

.. class:: fragment current-visible collapsable-fragment

  You can iterate over these results using result.stdout_lines:

  .. code::

    - name: iterate results
      local_action: debug msg={{item}}
      with_items: result.stdout_lines

----


Lining them up,
---------------
and knocking them down
----------------------

.. code::

  - name: set PHP-FPM parameters
    lineinfile:
      dest: /etc/php-fpm.conf
      regexp: "^{{ item.param }}"
      insertafter: "^;{{ item.param }}"
      line: "{{ item.param }} = {{ item.value }}"
    with_items:
      - { param: 'error_log', value: '/var/log/php-fpm/error.log' }
      - { param: 'log_level', value: 'error' }
      - { param: 'emergency_restart_threshold', value: '10' }

----


Dynamic Inventory
=================

.. class:: fragment current-visible collapsable-fragment

  Dynamic Inventory allows you to pass a script into Ansible's commands, which it uses to obtain a json blob with the inventory.

.. class:: fragment current-visible collapsable-fragment

  Scripts are provided for:

  +-----------------------+-----------------------+
  | Cobbler               | Amazon EC2            |
  | BSD Jails             | DigitalOcean          |
  | Google Compute Engine | Linode                |
  | OpenShift             | OpenStack Nova        |
  | Red Hat's SpaceWalk   | Vagrant               |
  | Zabbix                |                       |
  +-----------------------+-----------------------+

.. class:: fragment current-visible collapsable-fragment

  | But you can write one yourself.
  | Say to pull from Active Directory.

.. class:: fragment current-visible collapsable-fragment

  The output of your script needs to look like this:

  .. code:: json

    {
    "webservers"  : [ "host2.example.com", "host3.example.com" ],
    "databases"   : {
        "hosts"   : [ "host1.example.com", "host2.example.com" ],
         "vars"   : {
            "a"   : true
        }
    },
    "atlanta"     : {
        "hosts"   : [ "host1.example.com", "host4.example.com"],
        "vars"    : {
            "b"   : false
        },
        "children": [ "marietta", "5points" ]
    },
    "marietta"    : [ "host6.example.com" ],
    "5points"     : [ "host7.example.com" ]
    }

Modules
=======

----

Core Modules
------------

.. class:: fragment current-visible collapsable-fragment

  | The debug module print statements during execution,
  | which can include variables.

  ``  - debug: msg="System {{ inventory_hostname }}``

.. class:: fragment current-visible collapsable-fragment

  | The accelerate module can increase
  | communication throughput to clients.
  | (Communications are still encrypted.)
  | It is used by adding the following to an ansible play:

  ``accelrate: true``

.. class:: fragment current-visible collapsable-fragment

  | The fail module abandons the progress with a custom message.
  | It is most useful when combined with "when"

  .. code::

      - fail: msg="The system may not be provisioned."
        when: cmdb_status != "to-be-staged"

.. class:: fragment current-visible collapsable-fragment

  | The pause module temporarily stops exectuion of a playbook.
  | It can require an amount of time passing, or user input.

  .. code::

      # Time Based:
      - pause: minutes=5
      - pause: seconds=30
      # User Input:
      - pause: prompt="Check fo org.foo.FooOverload exception"


COWSAY Easter Egg
=================
If cowsay is installed, Ansible takes it upon itself to make your day happier when running playbooks. If you decide that you would like to work in a professional cow-free environment, you can either uninstall cowsay, or set an environment variable:

export ANSIBLE_NOCOWS=1

https://support.ansible.com/hc/en-us/articles/201957877-How-do-I-disable-cowsay-



Resources and Credits
=====================

- https://gist.github.com/marktheunissen/2979474
- http://stackoverflow.com/questions/23945201/how-to-run-only-one-task-in-ansible-playbook
- http://docs.ansible.com/ansible/playbooks_tags.html
- http://stackoverflow.com/a/25452182
- http://sparanoid.com/note/ansible-advanced-lineinfile/
- https://servercheck.in/blog/yaml-best-practices-ansible-playbooks-tasks
- https://raymii.org/s/tutorials/Ansible_-_Playbook_Testing.html
- http://docs.ansible.com/ansible/intro_dynamic_inventory.html
- http://docs.ansible.com/ansible/developing_inventory.html
- http://docs.ansible.com/ansible/debug_module.html
- http://docs.ansible.com/ansible/accelerate_module.html
- http://docs.ansible.com/ansible/fail_module.html
- http://docs.ansible.com/ansible/pause_module.html
