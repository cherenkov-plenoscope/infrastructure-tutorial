Infrastructure Tutorial
#######################

Abstract
  This tutorial shows the infrastructure I use to work with remote computers. The goal of the methods and tools shown here is to automate repetetive tasks, avoid common pitfals, and to gain awareness of fairshare in case there are multiple users. This tutorial is not meant to be complete. It is just a summary of the methods and tools I learned to use over the time.

Text-files
==========
Most configurations on your local and remote computer are written in text-files.
It is important that you are familiar with text-editors that run without a graphical-user-interface (GUI) in an x-server, but directly in the terminal. When you share text-files with others, who might use other editors, you must be aware of pitfalls regarding newlines and whitespaces.

Newlines
--------
A big pitfall with text-files are the multiple ways to indicate that a newline is starting.
The text-editors on your local machine might use a different newline indicator than your remote machine.
Most common in the linux is the ``\n`` newline indicator. 

Whitespaces
-----------
Be aware that there a multiple whitespaces [``' '``, ``\t``, ``\eol``, ... ] which might be undistinguishable in some editors and viewers but that might effect your programs that read the text-file. To avoid surprises I usually make my text-editors show these explicitly.

Tip for version-control
-----------------------
Set your text-editor to not change anything on its own.
Do not make your text-editor replace control-characters by default.
E.g. replacing spaces with tabs in code.
This creates massive diffs which obfuscate your intendet modification to the code. 

Text-editors and viewers that I use
-----------------------------------

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
A viewer. No editing. It just prints a text-file in the terminal. I often use ``less`` to look into ``FITS`` files because the headers are text. You have to adjust the number of colums of your terminal to get a good look into a ``FITS``-header.

Here is an example with my terminal set to 80-columns calling ``less ./Prod5-South-20deg-AverageAz-14MSTs37SSTs.1800s-v0.1.fits``

::

    XTENSION= 'BINTABLE'           / binary table extension                         
    BITPIX  =                    8 / 8-bit bytes                                    
    NAXIS   =                    2 / 2-dimensional binary table                     
    NAXIS1  =                 1392 / width of table in bytes                        
    NAXIS2  =                    1 / number of rows in table                        
    PCOUNT  =                    0 / size of special data area                      
    GCOUNT  =                    1 / one data group (required keyword)              
    TFIELDS =                    5 / number of fields in each row                   
    TTYPE1  = 'ENERG_LO'           / label for field   1                            
    TFORM1  = '42E     '           / data format of field: 4-byte REAL              
    TUNIT1  = 'TeV     '           / physical unit of field                         
    TTYPE2  = 'ENERG_HI'           / label for field   2                            
    TFORM2  = '42E     '           / data format of field: 4-byte REAL              
    TUNIT2  = 'TeV     '           / physical unit of field                         
    TTYPE3  = 'THETA_LO'           / label for field   3                            
    TFORM3  = '6E      '           / data format of field: 4-byte REAL              
    TUNIT3  = 'deg     '           / physical unit of field                         
    TTYPE4  = 'THETA_HI'           / label for field   4                            
    TFORM4  = '6E      '           / data format of field: 4-byte REAL              
    TUNIT4  = 'deg     '           / physical unit of field                         
    TTYPE5  = 'EFFAREA '           / label for field   5                            
    TFORM5  = '252E    '           / data format of field: 4-byte REAL              
    TUNIT5  = 'm**2    '           / physical unit of field                         
    EXTNAME = 'EFFECTIVE AREA'     / name of this binary table extension            
    TDIM5   = '(42,6)  '           / size of the multidimensional array             
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

    some


job-queues
==========
Job-queues are powerful tools to allow parallel computing on a compute-cluster while respecting fair-share with other users.

batch-tools
-----------
There exist tools which help with using job-queues, but you have to make sure that the tool respects the fair-share.
Some tools assume that you are the sole user and thus will always keep jobs alive waiting for your workload.
Your colleages will not like this. In all scientific clusters I know of you are expected to end jobs when the computation is done.

Tools for the ``sun-grid-engine`` that do respect fair-share:

- queue_map_reduce-library_ for python. Minimal, can only do emberrasingly simple parallel computes. For me, this is enough.
- ???make for python. Versatile, but needs infrastructure to setup and to learn a 'makefile' like script language.


qstat
-----
Note that the  sun-grid-engine's ``qstat`` can return machine readable output using the ``-xml`` option.
With this you can inspect and organize your jobs on your own when you have more complicated demands than emberrasingly simple parallel computes. There is a python-parser for qstat_. 


chron
=====
The program chron can be used to execute your commands on the remote in certain time-intervalls without the need for you to log into the remote. I use ``chron`` e.g. to do automatic backups of my ``github.com`` repositories to our institute's storage.
It is important to know that ``chron`` will not load your ``~/.bashrc`` or any other settings of your environment when it executes your command.


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

::

    import uuid
    import os
    import shutil
    import errno


    def copy(src, dst):
        """
        Atomic copy.
        """
        copy_id = uuid.uuid4().__str__()
        tmp_dst = "{:s}.{:s}.tmp".format(dst, copy_id)
        try:
            shutil.copytree(src, tmp_dst)
        except OSError as exc:
            if exc.errno == errno.ENOTDIR:
                shutil.copy2(src, tmp_dst)
            else:
                raise
        os.rename(tmp_dst, dst)


    def move(src, dst):
        """
        Atomic move across seperate file-system.
        """
        try:
            os.rename(src, dst)
        except OSError as err:
            if err.errno == errno.EXDEV:
                copy(src, dst)
                os.unlink(src)
            else:
                raise



.. _network-file-system-library: https://github.com/cherenkov-plenoscope/starter_kit/blob/main/plenoirf/plenoirf/network_file_system.py

.. _Dask: https://docs.dask.org/en/latest/

.. _pyABC.sge: https://pyabc.readthedocs.io/en/latest/api_sge.html

.. _ipyparallel: https://ipyparallel.readthedocs.io/en/latest/index.html

.. _qstat: https://pypi.org/project/qstat/

.. _queue_map_reduce-library: https://github.com/cherenkov-plenoscope/queue_map_reduce
