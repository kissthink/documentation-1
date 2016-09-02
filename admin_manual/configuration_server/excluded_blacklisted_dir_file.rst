Excluding Directories and Blacklisting Files
============================================

Definitions of terms
--------------------

:**Blacklisted**:
  Files that may harm the ownCloud environment like a foreign ``.htaccess`` file
:**Excluded**:
  Directories that are excluded from being processed, like snapshot directories

Both types are defined in config.php and are when set - not scanned, not viewed, not synced, can not be created or renamed (or deleted) or accessed via direct path input from a file explorer. Even, when a filepath is entered manually via a file explorer and contains at any place an excluded directory name, the path cannot be accessed.

For setup example details please see ``config.sample.php``

Blacklisted Files
-----------------

By default, ownCloud blacklists the file ``.htaccess`` to secure the running instance which is important when using Apache as webserver. A foreign ``.htaccess`` file could overwrite rules defined by ownCloud. There is no explicit need to enter the file name ``.htaccess`` as parameter to the ``blacklisted_files`` array in config.php. But you can add more blacklisted file names if necessary.

Excluded Directories
--------------------

**Reason for excluding directories:**

1. Enterprise Storage Systems or special filesystems like ZFS are capable of snapshots. These snapshots are directories with a particular name and keep point in time views/copies of the data. Usually these snapshot directories are present in each directory
2. Compared to the filesystem which is read/write, snapshot directories are by design read only
3. There is no common naming for these directories and most likely will never be. NetApp uses ``.snapshot`` and ``~snapshot``, EMC eg ``.ckpt``, HDS eg ``.latest`` and ``~latest``, the ZFS filesystem uses ``.zfs`` and so on.
4. Viewing and scanning of these directories does not make any sense as these directories are used to ease backup, restore and cloning
5. Directories defined by their names which are part of the filesystem mounted, but must not be accessible via ownCloud

**Example:**

If you have a snapshot capable storage or filesystem where snapshots are enabled and presented to clients, each directory will contain a "special" visible directory named eg .snapshot. Depending on the system, you may find underneath a list of snapshots taken and in the next lower level the complete set of files and directories which were present when the snapshot was created. In most systems, this mechanism is true in all directory levels:

.. code-block:: bash

   /.snapshot
	/nightly.0
		/home
		/dat
		/pictures
		file_1
		file_2
	/nightly.1
		/home
		/dat
		/pictures
		file_1
		file_2
	/nightly.2
		/home
		/dat
		/pictures
		file_1
		file_2
	...
   /home
   /dat
   /pictures
   file_1
   file_2
   ...

**Quantitative contemplation:**

If you have a filesystem mounted with 200,000 files and directories and 15 snapshots in rotation, you would now scan and process 200,000 elements plus 200,000 x 15 = 3,000,000 elements additionally. These additional 3,000,000 elements, 15 times more than the original quanity, would also be available for viewing and synchronisation. Because this is a big and unnecessary overhead, most times confusing to clients, further processing can be eliminated by using excluded directories.
