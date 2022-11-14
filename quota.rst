Quota
=====

Your storage-space might be restricted by a quota.
For example there is usually a quota applied to your ```/home/user``` directory.
Check your quota when you experience errors related to writing files.

The quota limits both:
- Total size
- Number of objects

Call
```bash
user@lfs1.mpi-hd.mpg.de:/home/user$ /usr/local/bin/nquota
```

and see your quota
```bash
samba1.mpi-hd.mpg.de file system usage for user user:

FS                                   USED    QUOTA  OBJUSED OBJQUOTA
us0                                 9.83G      15G     167K     300K

FS       = filesystem name
USED     = disk space used
QUOTA    = disk quota
OBJUSED  = number of objects used (files+ACL entries)
OBJQUOTA = object quota
           K=1024, M=1024^2, G=1024^3, T=1024^4
```

You might be able to negotiate a raise to the quota's limits with our admins.
For example I was granted a higher limit for the number of objects.

