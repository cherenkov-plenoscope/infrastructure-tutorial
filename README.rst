Infrastructure Tutorial
#######################

Abstract
  This tutorial shows the infrastructure I use to work with remote computers. The goal of the methods and tools shown here is to automate repetetive tasks, avoid common pitfals, and to gain awareness of fairshare in case there are multiple users. This tutorial is not meant to be complete. It is just a summary of the methods and tools I learned to use over the time.

Text-files
==========
Most configurations on your local and remote computer are written in text-files.
It is important that you are familiar with text-editors that run without a graphical-user-interface (GUI) in an x-server, but directly in the terminal. Especially when you share text-files with others, who might use other editors, you must be aware of pitfalls regarding newlines and whitespaces.

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

Tools I use
-----------

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
A viewer. No editing. It just prints a text-file in the terminal. I often use ``less`` to look into ``FITS`` files because the headers in FITS files are just text. You just have to adjust the number of colums of your terminal to get a good look into a ``FITS``-header.

``hexdump``
^^^^^^^^^^^
A viewer. No editing. Very powerful for debugging. Use it with the ``-c`` option to see each character including the control-characters, such as ``\n`` explicitly.
* ``wc`` A counter. I use it to count the number of lines in a textfile using the ``-l`` option. It prints the number of lines in the terminal.


Communicating with a remote computer
====================================


