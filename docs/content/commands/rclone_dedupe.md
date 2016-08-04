---
date: 2016-08-04T21:37:09+01:00
title: "rclone dedupe"
slug: rclone_dedupe
url: /commands/rclone_dedupe/
---
## rclone dedupe

Interactively find duplicate files delete/rename them.

### Synopsis



By default `dedup` interactively finds duplicate files and offers to
delete all but one or rename them to be different. Only useful with
Google Drive which can have duplicate file names.

The `dedupe` command will delete all but one of any identical (same
md5sum) files it finds without confirmation.  This means that for most
duplicated files the `dedupe` command will not be interactive.  You
can use `--dry-run` to see what would happen without doing anything.

Here is an example run.

Before - with duplicates

    $ rclone lsl drive:dupes
      6048320 2016-03-05 16:23:16.798000000 one.txt
      6048320 2016-03-05 16:23:11.775000000 one.txt
       564374 2016-03-05 16:23:06.731000000 one.txt
      6048320 2016-03-05 16:18:26.092000000 one.txt
      6048320 2016-03-05 16:22:46.185000000 two.txt
      1744073 2016-03-05 16:22:38.104000000 two.txt
       564374 2016-03-05 16:22:52.118000000 two.txt

Now the `dedupe` session

    $ rclone dedupe drive:dupes
    2016/03/05 16:24:37 Google drive root 'dupes': Looking for duplicates using interactive mode.
    one.txt: Found 4 duplicates - deleting identical copies
    one.txt: Deleting 2/3 identical duplicates (md5sum "1eedaa9fe86fd4b8632e2ac549403b36")
    one.txt: 2 duplicates remain
      1:      6048320 bytes, 2016-03-05 16:23:16.798000000, md5sum 1eedaa9fe86fd4b8632e2ac549403b36
      2:       564374 bytes, 2016-03-05 16:23:06.731000000, md5sum 7594e7dc9fc28f727c42ee3e0749de81
    s) Skip and do nothing
    k) Keep just one (choose which in next step)
    r) Rename all to be different (by changing file.jpg to file-1.jpg)
    s/k/r> k
    Enter the number of the file to keep> 1
    one.txt: Deleted 1 extra copies
    two.txt: Found 3 duplicates - deleting identical copies
    two.txt: 3 duplicates remain
      1:       564374 bytes, 2016-03-05 16:22:52.118000000, md5sum 7594e7dc9fc28f727c42ee3e0749de81
      2:      6048320 bytes, 2016-03-05 16:22:46.185000000, md5sum 1eedaa9fe86fd4b8632e2ac549403b36
      3:      1744073 bytes, 2016-03-05 16:22:38.104000000, md5sum 851957f7fb6f0bc4ce76be966d336802
    s) Skip and do nothing
    k) Keep just one (choose which in next step)
    r) Rename all to be different (by changing file.jpg to file-1.jpg)
    s/k/r> r
    two-1.txt: renamed from: two.txt
    two-2.txt: renamed from: two.txt
    two-3.txt: renamed from: two.txt

The result being

    $ rclone lsl drive:dupes
      6048320 2016-03-05 16:23:16.798000000 one.txt
       564374 2016-03-05 16:22:52.118000000 two-1.txt
      6048320 2016-03-05 16:22:46.185000000 two-2.txt
      1744073 2016-03-05 16:22:38.104000000 two-3.txt

Dedupe can be run non interactively using the `--dedupe-mode` flag or by using an extra parameter with the same value

  * `--dedupe-mode interactive` - interactive as above.
  * `--dedupe-mode skip` - removes identical files then skips anything left.
  * `--dedupe-mode first` - removes identical files then keeps the first one.
  * `--dedupe-mode newest` - removes identical files then keeps the newest one.
  * `--dedupe-mode oldest` - removes identical files then keeps the oldest one.
  * `--dedupe-mode rename` - removes identical files then renames the rest to be different.

For example to rename all the identically named photos in your Google Photos directory, do

    rclone dedupe --dedupe-mode rename "drive:Google Photos"

Or

    rclone dedupe rename "drive:Google Photos"


```
rclone dedupe [mode] remote:path
```

### Options

```
      --dedupe-mode value   Dedupe mode interactive|skip|first|newest|oldest|rename. (default "interactive")
```

### Options inherited from parent commands

```
      --acd-templink-threshold value   Files >= this size will be downloaded via their tempLink. (default 9G)
      --ask-password                   Allow prompt for password for encrypted configuration. (default true)
      --b2-chunk-size value            Upload chunk size. Must fit in memory. (default 96M)
      --b2-test-mode string            A flag string for X-Bz-Test-Mode header.
      --b2-upload-cutoff value         Cutoff for switching to chunked upload (default 190.735M)
      --b2-versions                    Include old versions in directory listings.
      --bwlimit value                  Bandwidth limit in kBytes/s, or use suffix b|k|M|G
      --checkers int                   Number of checkers to run in parallel. (default 8)
  -c, --checksum                       Skip based on checksum & size, not mod-time & size
      --config string                  Config file. (default "/home/ncw/.rclone.conf")
      --contimeout duration            Connect timeout (default 1m0s)
      --cpuprofile string              Write cpu profile to file
      --delete-after                   When synchronizing, delete files on destination after transfering
      --delete-before                  When synchronizing, delete files on destination before transfering
      --delete-during                  When synchronizing, delete files during transfer (default)
      --delete-excluded                Delete files on dest excluded from sync
      --drive-auth-owner-only          Only consider files owned by the authenticated user. Requires drive-full-list.
      --drive-chunk-size value         Upload chunk size. Must a power of 2 >= 256k. (default 8M)
      --drive-formats string           Comma separated list of preferred formats for downloading Google docs. (default "docx,xlsx,pptx,svg")
      --drive-full-list                Use a full listing for directory list. More data but usually quicker. (obsolete)
      --drive-upload-cutoff value      Cutoff for switching to chunked upload (default 8M)
      --drive-use-trash                Send files to the trash instead of deleting permanently.
      --dropbox-chunk-size value       Upload chunk size. Max 150M. (default 128M)
  -n, --dry-run                        Do a trial run with no permanent changes
      --dump-bodies                    Dump HTTP headers and bodies - may contain sensitive info
      --dump-filters                   Dump the filters to the output
      --dump-headers                   Dump HTTP headers - may contain sensitive info
      --exclude string                 Exclude files matching pattern
      --exclude-from string            Read exclude patterns from file
      --files-from string              Read list of source-file names from file
  -f, --filter string                  Add a file-filtering rule
      --filter-from string             Read filtering patterns from a file
      --ignore-existing                Skip all files that exist on destination
      --ignore-size                    Ignore size when skipping use mod-time or checksum.
  -I, --ignore-times                   Don't skip files that match size and time - transfer all files
      --include string                 Include files matching pattern
      --include-from string            Read include patterns from file
      --log-file string                Log everything to this file
      --low-level-retries int          Number of low level retries to do. (default 10)
      --max-age string                 Don't transfer any file older than this in s or suffix ms|s|m|h|d|w|M|y
      --max-depth int                  If set limits the recursion depth to this. (default -1)
      --max-size value                 Don't transfer any file larger than this in k or suffix b|k|M|G (default off)
      --memprofile string              Write memory profile to file
      --min-age string                 Don't transfer any file younger than this in s or suffix ms|s|m|h|d|w|M|y
      --min-size value                 Don't transfer any file smaller than this in k or suffix b|k|M|G (default off)
      --modify-window duration         Max time diff to be considered the same (default 1ns)
      --no-check-certificate           Do not verify the server SSL certificate. Insecure.
      --no-gzip-encoding               Don't set Accept-Encoding: gzip.
      --no-traverse                    Don't traverse destination file system on copy.
      --no-update-modtime              Don't update destination mod-time if files identical.
      --onedrive-chunk-size value      Above this size files will be chunked - must be multiple of 320k. (default 10M)
      --onedrive-upload-cutoff value   Cutoff for switching to chunked upload - must be <= 100MB (default 10M)
  -q, --quiet                          Print as little stuff as possible
      --retries int                    Retry operations this many times if they fail (default 3)
      --size-only                      Skip based on size only, not mod-time or checksum
      --stats duration                 Interval to print stats (0 to disable) (default 1m0s)
      --swift-chunk-size value         Above this size files will be chunked into a _segments container. (default 5G)
      --timeout duration               IO idle timeout (default 5m0s)
      --transfers int                  Number of file transfers to run in parallel. (default 4)
  -u, --update                         Skip files that are newer on the destination.
  -v, --verbose                        Print lots more stuff
```

### SEE ALSO
* [rclone](/commands/rclone/)	 - Sync files and directories to and from local and remote object stores - v1.32

###### Auto generated by spf13/cobra on 4-Aug-2016