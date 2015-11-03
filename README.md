# ansvif
A Not So Very Intelligent Fuzzer.

Compliation
  You can compile the code with:
  $ make && make test
  
  If you would like to try out the example code, you can compile faulty.c with:
  $ gcc faulty.c -o faulty


Using the example code:
  You can point the memory back at address \xff\x05\x40\x00\x00\x00\x00\x00 (the
  subroutine containing the code that spawns bash) with:
  $ ./faulty -a $(perl -e 'print "A"x24;print "\x00\xff\x05\x40\x00\x00\x00\x00\x00"')
  The address may be a little different under your distro, check gdb if you really
  want to try it out.  The code above /should/ drop you at a bash prompt.
  If the code is set to a mode where all are able to execute as another user, it will
  attempt to spawn a root shell.


Useage:
  ./find_suid /usr/bin/ /bin/ /sbin/
  ./ansvif -[tm] [template manpage] -c /path/to/executable -b buffersize

  Examples:
  $ echo "Marshall" ./ansvif -t examples/template -c ./faulty -b 64
  $ ./ansvif -m mount -c /usr/sbin/mount -e examples/template_env -x examples/template_other -f 8 -b 2048
  $ ./ansvif -t examples/blank -F tmp/tmphtml -x examples/htmltags -c /usr/bin/iceweasel -b 128 -A "file:///home/username/src/ansvif/tmp/tmphtml"  -f 2 -n -R "sleep 3 && killall iceweasel" -S ">"

Options:
  -t This file should hold line by line command arguments as shown in the example file.
  -e This file should hold line by line environment variables as shown in the example
     file.  You can usually get these by doing something like:
     $ strings /bin/mount | perl -ne 'print if /[A-Z]=$/' > mount_envs
  -c Specifies the command path.
  -p Specifies the manpage location (as an integer, usually 1 or 8)
  -m Specifies the commands manpage.
  -D Dumps whats found in the manpage.
  -f Number of threads to use.  Default is 2.
  -b Specifies the buffer size to fuzz with.  256-2048 Is usually sufficient.
  -r Uses only random garbage data.  (note: this is buggy)
  -o Writes output to log file.
  -z Randomize the buffer size from 1 to what is specified by -b.
  -x Other junk to put in.  Usernames and such can go here.
  -S Seperator between options.
  -v Verbose.
  -T Timeout for threads.
  -L Unpriviledged user to run as if root.
  -A Always put whats after this in the command to run.
  -F File to feed into the program that -x along with normal fuzzing data will be put in.
  -n Never use random data in the fuzz.
  -R Run this command after each fuzz.
  -d Debug data.
  -h Shows the help page.

Recommendations:
  DO NOT RUN THIS CODE IN A PRODUCTION ENVIRONMENT!
  If you try setting faulty.c's output to suid(0) then PLEASE do it in a virtual machine
  or atleast a machine that you don't care about.
  Other than that, just play around and have fun!


Notes:
  As for now, this code is mostly still in an experimental state, and may not work
  correctly.