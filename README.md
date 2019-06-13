# Python_for_Administerators
Hey I'm Biswajeet panda, Here you will understand how python is used for admins.

# Making system administration easier (and more fun)

                                       (First published September 04, 2007)
                                                                                          
                                                                                          
Adopt Python to manage UNIX® systems while incorporating concepts of good program
design. Python is an easy-to-learn, open source scripting language that lets system
administrators do their job more quickly. It can also make tasks more fun.


## Introduction
As a system administrator, you run across numerous challenges and problems. Managing users, disk space, processes, devices, and backups can cause many system administrators to lose their hair, good humor, or sanity. Shell scripts can help, but they often have frustrating limitations. This is where a full-featured scripting language, such as Python, can turn a tedious task into an easy and, dare I say it, fun one.

The examples in this article demonstrate different Python features that you can put to practical use. If you work through them, you'll be well on your way to understanding the power of Python.


## About modules
A module is an important Python concept. Basically, a module is a resource you import in order to use it. This process is comparable to taking a piece of paper out of a file cabinet and putting it on your desk, ready for use. You import modules using the import command, which appears at the top of each of the example programs. Modules are available for database connectivity, network programming, operating system services, and hundreds of other useful areas.

## Put Python to work
### Why Python?

Python has many features to recommend it:
```
* It's free, being open source.
* It's easy to learn. Many users find its syntax much more English-like than other scripting languages.
 It's mature. Python has been around a long time, which means its code is stable, lots of modules add functionality, and robust documentation is available on the Web.
```

Python is a full-featured, robust programming language and, as such, it has tons of features.
Learning it could be a task of epic proportions. However, remember that many Python features,
such as the GUI toolkits, are of limited value to system administrators. That's why this article uses
specific examples: They demonstrate the skills you need to effectively write Python scripts to
manage systems.

## Notes on the examples
* Each example includes a try: and an except: with a surrounding block of code. This is an implementation of rudimentary error handling. Python has extensive support for handling all types of exceptions but, for the purposes of these example programs, I've kept it simple.
* These examples were run on Python 2.5 running on a Linux® box, but they should work on any UNIX®/Linux machine.
* You'll undoubtedly think of ways these scripts can be improved. This is good! The nature of Python scripts is that they can be easily modified and customized without needing to recompile code.

## Example 1: Search for files and show permissions in a friendly format
The first example program (see Listing 1) searches for files that match a pattern (based on user
input) and displays the results to the screen, along with the permissions assigned to the particular
files. At first, you might think this program doesn't do much more than execute a find command;
however, it displays results in a customized way, and your options for displaying this enhanced find
are limitless. The example shows you how to take a system command and make it better (or at
least more customized).

The script basically performs three tasks:
1. Get the search pattern from the user.
2. Perform the search.
3. Present the results to the user.

In writing the script, constantly ask yourself this question, "Which task is this code supporting?" Asking yourself this question can increase the focus of your work and efficiency

### Listing 1. Search for files and list results with file permissions
```
import stat, sys, os, string, commands

#Getting search pattern from user and assigning it to a list

try:
	#run a 'find' command and assign results to a variable

	pattern = raw_input("Enter the file pattern to search for:\n")
	commandString = "find " + pattern
	commandOutput = commands.getoutput(commandString)
	findResults = string.split(commandOutput, "\n")
	#output find results, along with permissions
	print "Files:"
	print commandOutput
	print "================================"
	
	for file in findResults :
		mode=stat.S_IMODE(os.lstat(file)[stat.ST_MODE])
		print "\nPermissions for file ", file, ":"
			
			for level in "USR", "GRP", "OTH":
				
				for perm in "R", "W", "X":
					
					if mode & getattr(stat,"S_I"+perm+level):
						print level, " has ", perm, " permission"
					
					else:
						print level, " does NOT have ", perm, " permission"

except:
	print "There was a problem - check the message above"
```

The program follows these steps:
1. Ask the user for a search pattern (lines 7-9).
2. Print a listing of files found (lines 12-14).
3. Using the stat module, get permissions for each file found and display them to the screen
(lines 15-23).

When the program is run, the output looks like that shown in Listing 2.

### Listing 2. Output of the first example

```
$ python example1.py
Enter the file pattern to search for:
j*.py
FILES FOUND FOR PATTERN j*.py :
jim.py
jim2.py
================================
Permissions for file jim.py :
USR R
USR W
USR X
GRP -
GRP -
GRP -
OTH -
OTH -
OTH -
Permissions for file jim2.py :
USR R
USR W
USR X
GRP R
GRP -
GRP X
OTH R
OTH -
OTH X

```
## Example 2: Perform operations on a tar archive that is based on menu selection

The previous example prompted the user for a search pattern to use. Another way to get information from the user is through a command-line argument. The program in Listing 3 shows how to do that in Python: The code takes a tar filename as a command-line argument and then prompts the user with several options.

This example also shows a new way to attack the problem. The first example used the command module to run the find command and capture the output. This approach can be clumsy and isn't very "Pythonic." This example uses the tarfile module to open a tar file, which has the advantage of allowing you to use Python attributes and methods as you manipulate the file. With many modules provided by Python, you can do things that can't be done through the command line.

This is a good example of implementing a menu system in Python. The program performs different actions based on your selection:
* If you press 1, the program prompts you for the file name in the archive to extract the current directory to and then extracts the file.
* If you press 2, the program prompts you for the file name and then displays the file information.
* If you press 3, the program lists all the files in the archive.

### Listing 3. Perform actions on a tar archive based on your menu selection

```
import tarfile, sys
try:
	#open tarfile
	tar = tarfile.open(sys.argv[1], "r:tar")
 	#present menu and get selection
 	selection = raw_input("Enter\n\
 	1 to extract a file\n\
 	2 to display information on a file in the archive\n\
 	3 to list all the files in the archive\n\n")
 	#perform actions based on selection above
 	if selection == "1":
		 filename = raw_input("enter the filename to extract: ")
		 tar.extract(filename)
 	elif selection == "2":
 		filename = raw_input("enter the filename to inspect: ")
 		for tarinfo in tar:
 			if tarinfo.name == filename:
				 print "\n\
				 Filename:\t\t", tarinfo.name, "\n\
				 Size:\t\t", tarinfo.size, "bytes\n\
	elif selection == "3":
 		print tar.list(verbose=True)
except:
 	print "There was a problem running the program"

```

The program follows these steps:
1. Open the tar file (line 5).
2. Present the menu and get the user selection (lines 8-11).
3. If you press 1 (lines 14-16), extract a file from the archive.
4. If you press 2 (lines 17-23), present information about a selected file.
5. If you press 3 (lines 24-25), present information about all the files in the archive.

The output is shown in Listing 4.

### Listing 4. User menu for second example

```
$ python example2.py jimstar.tar
Enter
1 to extract a file
2 to display information on a file in the archive
3 to list all the files in the archive
```

## Example 3: Check for a running process and show information in a friendly format

One of the most important duties of a system administrator is checking on running processes. The script in Listing 5 gives you some ideas. The program takes advantage of UNIX's ability to run a grep command on output generated by a command, which lets you automatically narrow the data Python has to parse.

This program also uses the string module. Get to know this module—you'll use it often.

### Listing 5. Display information on a running process in a friendly format

```
import commands, os, string
program = raw_input("Enter the name of the program to check: ")
try:
 	#perform a ps command and assign results to a list
 	output = commands.getoutput("ps -f|grep " + program)
 	proginfo = string.split(output)
 	#display results
 	print "\n\
 	Full path:\t\t", proginfo[5], "\n\
 	Owner:\t\t\t", proginfo[0], "\n\
 	Process ID:\t\t", proginfo[1], "\n\
 	Parent process ID:\t", proginfo[2], "\n\
 	Time started:\t\t", proginfo[4]
except:
 	print "There was a problem with the program."
```

The program follows these steps:
1. Get the name of a process to check and assign it to a variable (line 3).
2. Run the ps command and assign the results to a list (lines 7-8).
3. Display detailed information about the process with English terms (lines 11-16).
The output is shown in Listing 6.

### Listing 6. Output of the third example

```
$ python example3.py
Enter the name of the program to check: xterm
 	Full path: /usr/bin/xterm
 	Owner: knowltoj
 	Process ID: 3220
 	Parent process ID: 4308
 	Time started: 16:51:46
```

## Example 4: Check userids and passwords for policy compliance

Managing security is a critical part of the job for any system administrator. Python makes this job easier, as the last example illustrates. The program in Listing 7 uses the pwd module to access the password database. It checks userids and passwords for security policy compliance (in this case, that userids are at least six characters long and passwords are at least eight characters long).

There are two caveats:
* This program works only if you have full rights to /etc/passwd.
* If you use shadow passwords, this script won't work (however, Python 2.5 does have a spwd module that does the job).

### Listing 7. Check userids and passwords for compliance with security policy

```
import pwd
#initialize counters
erroruser = []
errorpass = []
#get password database
passwd_db = pwd.getpwall()
try:
	#check each user and password for validity
 	for entry in passwd_db:
 		username = entry[0]
 		password = entry [1]
 		if len(username) < 6:
 			erroruser.append(username)
 		if len(password) < 8:
 			errorpass.append(username)
 		#print results to screen
 		print "The following users have an invalid userid (less than six characters):"
 		for item in erroruser:
			print item
 		print "\nThe following users have invalid password(less than eight characters):"
 		for item in errorpass:
 			print item
except:
	print "There was a problem running the script."
	
```

The program follows these steps:
1. Initialize the counter lists (lines 4-5).
2. Open the password database and assign data to a list (line 8).
3. Check users and passwords for validity (lines 12-18).
4. Output invalid users and passwords (lines 21-26).
The output is shown in Listing 8.

### Listing 8. Output of the fourth example
```
$ python example4.py
The following users have an invalid userid (less than six characters):
Guest
The following users have invalid password(less than eight characters):
Guest
johnsmith
joewilson
suejones

```

Other uses for scripts
You can use Python in a number of ways to manage systems. One of the best things you can do is analyze your work, determine which tasks you perform repeatedly, and explore whether Python modules are available to help you with those tasks—almost certainly they are.

Some specific areas where Python can be of great help are as follows:
* Managing servers:Checks patch levels for a particular application across a set of servers and updates them automatically.
* Logging: Sends an e-mail automatically if a particular type of error shows up in the syslog.
* Networking: Makes a Telnet connection to a server and monitors the status of the connection.
* Testing Web applications: Uses freely available tools to emulate a Web browser and verifies Web application functionality and performance.

These are just a few examples—I'm sure you can add useful ideas of your own. 

# Summary

With its easy-to-learn language; its ability to handle files, processes, strings, and numbers; and its almost endless array of helper modules, Python is a scripting language that looks like it was made for system administrators. It's a valuable tool for any system administrator's toolbox

# Thanks for bearing till this.
# - Biswajeet panda
