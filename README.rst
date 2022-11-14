Infrastructure Tutorial
#######################

Abstract
  This tutorial shows the infrastructure I use to work with remote computers. The goal of the methods and tools shown here is to automate repetetive tasks, avoid common pitfals, and to gain awareness of fairshare in case there are multiple users. This tutorial is not meant to be complete. It is just a summary of the methods and tools I learned to use over the time.

Text-files
==========
Most configurations on your local and remote computer are written in text-files.
I often run text-editors without a graphical-user-interface, but directly in the terminal. When you share text-files with others, who might use different editors on different operating-systems, you must be aware of pitfalls regarding newlines and whitespaces.

Newlines
--------
A big pitfall with text-files are the multiple ways to indicate that a newline is starting.
The text-editors on your local machine might use a different newline indicator than your remote machine.
Most common in linux is the ``\n`` newline indicator. 

Whitespaces
-----------
There are multiple whitespaces [``' '``, ``\t``, ``\eol``, ... ] which might be undistinguishable in some text-editors but that might effect your programs that read the text-file. To avoid surprises I usually make my text-editors show these explicitly.

Text-editors and viewers that I use
-----------------------------------
In general, set your text-editor to not change anything on its own.
Do not make your text-editor replace control-characters by default.
E.g. replacing spaces with tabs in code.
This creates massive diffs in version-control which obfuscate your intendet modification to the code. 

``vim``
^^^^^^^
A common editor. It can be customized using the text-file ``~/.vimrc``. This is my ``~/.vimrc``:
::
    syntax on
    set nu
    highlight ExtraWhitespace ctermbg=red guibg=red
    match ExtraWhitespace /\s\+$/
    set list
    set listchars=eol:⏎,tab:␉·,trail:␠,nbsp:⎵

It makes vim show the linenumbers and makes vim show whitespaces explicitly.

``emacs``
^^^^^^^^^
Another common editor. But for some reason I do not use it much, but you might want to give it a try.

``less``
^^^^^^^^
A viewer. No editing. It just prints a text-file in the terminal. Very useful when you want to copy some part of a text-file to paste it somewhere else. I also use ``less`` to look into ``FITS``-files because the headers are text.

Here is an example with my terminal set to 80-columns calling ``less ./Prod5-South-20deg-AverageAz-14MSTs37SSTs.1800s-v0.1.fits``

::

    ...            
    TELESCOP= 'CTA     '           / Name of telescope                              
    INSTRUME= 'Southern Array'     / Instrument                                     
    AUTHOR  = 'G.Maier '           / Author                                         
    ORIGIN  = 'CTAO    '           / Origin                                         
    DATE    = '2021-07-29T12:49:25' / File creation date (YYYY-MM-DDThh:mm:ss UTC)  
    HDUDOC  = 'https://github.com/open-gamma-ray-astro/gamma-astro-data-formats'    
    HDUVERS = '0.2     '           / HDU version                                    
    HDUCLASS= 'GADF    '           / HDUCLASS                                       
    HDUCLAS1= 'RESPONSE'           / HDUCLAS1                                       
    HDUCLAS2= 'EFF_AREA'           / HDUCLAS2                                       
    HDUCLAS3= 'FULL-ENCLOSURE'     / HDUCLAS3                                       
    HDUCLAS4= 'AEFF_2D '           / HDUCLAS4                                       
    END 
    ...


``hexdump``
^^^^^^^^^^^
A viewer. No editing. Very powerful for debugging. Use it with the ``-c`` option to see each character including the control-characters, such as ``\n`` explicitly.

``wc``
^^^^^^
A counter. I use it to count e.g. the number of lines in a textfile using the ``-l`` option. It prints the number of lines in the terminal.


Your terminal
=============


Communicating with a remote computer
====================================

copying files with ``rsync -a``
----------------------------

Screen
======
Usually when you connect to a remote machine and send it a command via ``ssh``, the remote will no longer execute your command when the connection to your local machine ends.
The program ``screen`` is one possible way to start a command on the remote machine, log off from the remote, and have your command keep on executing.
Further, ``screeen`` allows you to 'meet' and interact with another person who logs into the same useraccount on the remote.
You can see what the person types in the terminal in real-time.
This can be very useful for operating a remote instrument, such as a telescope, while you also have a voice-chat with the other person.

Configure ``screen`` using the ``~/.screenrc``:

::

    #startup_message off
    defscrollback 4096
    defutf8 on

    bind + resize +1
    bind - resize -1

    shell bash
    shelltitle "$ |bash"
    hardstatus alwayslastline " [%0c] %-w%{= BW}%50>%n %t%{-}%+w %= %h "

    chdir /home/user
    screen -t "home"

    chdir /home/user/kit
    screen -t "kit"

    screen -t "run"

    screen -t "git"

    chdir /space/l8/super/user
    screen -t "out"

    chdir /space/l8/super/user/.backup
    screen -t "backup"


job-queues
==========
Job-queues are powerful to do parallel computing on a compute-cluster.
They also allow to fair-share resources with other users.
You need to accept that there are many different and incompatible job-queues.
For this reason it is very important that the specific needs of your job-queue do not spread across your entire code, but are concentrated in small spot.
This way you, and your colleagues have a realistic chance to make the stuff run on a different compute-cluster with a different job-queue.

batch-tools
-----------
There exist batch-tools which help with using job-queues.
Often batch-tools abstract away from your specific job-queue and might support different flavours out of the box.
But you have to make sure that the tool respects the fair-share.
Some tools assume that you are the sole user and thus will always keep jobs alive waiting for your workload.
Your colleages will not like this. In all scientific clusters I know of you are expected to end jobs when the computation is done.

Tools for the ``sun-grid-engine`` that do respect fair-share:

- queue_map_reduce-library_ for python. Minimal, can only do emberrasingly simple parallel computes. For me, this is enough.
- ???make for python. Versatile, but needs infrastructure to setup and to learn a 'makefile' like script language.


qstat
-----
Note that the  sun-grid-engine's ``qstat`` can return machine readable output using the ``-xml`` option.
With this you can inspect and organize your jobs on your own when you have more complicated demands than emberrasingly simple parallel computes. There is a python-parser for qstat_. 


qlogin
------
Not all clusters have ``qlogin``. It allows you to loin to a worker-node. This can be very useful for debuging to explore the environment present on the worker-node to find out why some of your programs do not run the way they do when you login into the gateway.


cron
=====
The program ``cron`` can be used to execute your commands on the remote in certain time-intervalls without the need for you to log into the remote. I use ``cron`` e.g. to do automatic backups of my ``github.com`` repositories to our institute's storage.
It is important to know that ``cron`` will not load your ``~/.bashrc`` or any other settings of your environment when it executes your command. 

My configuration in ``~/.crontab.txt``

::

        43 0 * * 0 /home/user/anaconda3/bin/python3.7 /home/user/.github_backup.py my-organization /bulk/user/.backup/%Y-%m-%d_%H-%M-%S_my-organization.tar >> /home/user/.github_backup.log 2>&1


Network-file-system
===================
File-systems are extraordinary powerful. So let's make use of them.

map and reduce in parallel computing
------------------------------------
I use the filesystem extensively to do the ``map`` and the ``reduce`` in my parallel computing on a compute-cluster with job-queues.

atomic move
-----------
Let's say you got a job/command running on the remote which is writing an output-file named ``./result.csv``.
Let us assume that your job just started to write the output-file. Writing takes some time, it is not atomic. Now let us assume further that your job dies right now before it finnished writing the output-file.
Now there exists a file in the filesystem correctly named ``./result.csv`` which is incomplete.
When you are not lucky, the next program will read ``./result.csv``, will not realize that it is incomplete, and will give you a wrong result.
Sounds unlikely? No. This is everyday business on a fair-share-cluster.

The solution is to make an atomic ``mv`` move in the filesystem.
The idea is simply to not write a file named ``./result.csv`` but to write a file named ``./result.csv.incomplete``.
And after it is written you perform an atomic move ``mv ./result.csv.incomplete ./result.csv``.

So far so good. But in case this final move does not happen in the same directory, the move might not be atomic in a network-file-system.

::

    user@remote:~$ mv ./source/result.csv.incomplete ./destination/result.csv

In your network-file-system ``./source`` and ``./destination`` might cross hidden boundaries of the hardware which prevent the atomic move.
In a network-file-system you first have to copy

::

    user@remote:~$ rsync -a ./source/result.csv.incomplete ./destination/result.csv.incomplete

and then finally perform the atomic move

::

    user@remote:~$ mv ./source/result.csv.incomplete ./destination/result.csv

To abstract this away, I made a tiny ``python`` network-file-system-library_ to have a safe ``nfs.copy()`` and ``nfs.move()``.


Tape-archive
============
The tape-archive ``.tar`` is one of my personal superheros when it comes to computing. Just like a filesystem it allows you to structure your data and set up a hirachy of your choice that helps you to organize. But unlike a directory in your filesystem it has the advantage that it is more portable and can easier be moved to other machines. Further it might help you to reduce the number of objects in your filesystem by writing many files into one ``tar``.


.. _network-file-system-library: https://github.com/cherenkov-plenoscope/network_file_system

.. _Dask: https://docs.dask.org/en/latest/

.. _pyABC.sge: https://pyabc.readthedocs.io/en/latest/api_sge.html

.. _ipyparallel: https://ipyparallel.readthedocs.io/en/latest/index.html

.. _qstat: https://pypi.org/project/qstat/

.. _queue_map_reduce-library: https://github.com/cherenkov-plenoscope/queue_map_reduce
