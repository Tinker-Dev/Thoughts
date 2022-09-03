# Restore USB Drive Structure

After having tooled around with a USB Linux version using your image is overwritten or multi partitioned flash pen drive, you might find it necessary to revert it back to a single fat partition (restore the flash pen drive to its original state) that can again be read by all computers. Windows users can follow the Windows instructions below to Restore a Flash Drive using the HP USB Format Tool. For those working from Linux this task can easily be accomplished via the Linux Flash Drive Restoration tutorial that follows.  

  

## Restoring your USB key to its original state:

1. Open a command Prompt as administrator (cmd.exe)
2. Type **Diskpart** and press Enter
3. Type **List Disk** and press Enter
4. Type **Select Disk** X (where X is the disk number of your USB drive) and press Enter
5. Type **Clean** and press Enter
6. Type **Create Partition Primary** and press Enter
7. Type **Active** and press Enter
8. Type Format fs=**Fat32 Quick** and press Enter
9. Type **Exit** and press Enter