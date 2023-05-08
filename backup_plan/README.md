# research plan 
## what needs to be baked up?
- we need to backup all the puppet config files if we lose them it could take hours to bring them back.
- nagios/prometheus files should also be backed up as this is a good tool to see how the vm's are running.

## backup method
- we should backup data a least 2 time a day .

- Full Backup: This method copies everything in the virtual machine to a backup location. It is good for restoring the entire virtual machine if there is a problem, but it can take a long time and use a lot of space.

- Incremental Backup: This method only copies the changes made since the last backup, which is faster and uses less space than a full backup. But it might take longer to restore data because multiple backups might be needed.

- Differential Backup: This method only copies the changes made since the last full backup, which is faster than a full backup and uses less space than an incremental backup. But restoring data might take longer than with an incremental backup.

- Continuous Data Protection: This method backs up data in real-time as it is created or modified. It provides the most current protection, but it can be resource-intensive and might need extra hardware or software.

- Snapshot Backup: This method creates a copy of a virtual machine's disk at a specific time. It is fast and doesn't use much space, but it doesn't protect against data corruption or hardware failure.

## backup automation
- we can use automation for runing a backup task when every we need by using a cron task.

- here is a example of how we could run a task. 
```md 
0 1 * * * /path/to/backup/script.sh
```

- and we could make as many as we need.

## recovery plan
- Define recovery objectives: Determine how quickly you need to recover critical systems and data after a disaster.

- We should update the plan for any changes we make to what is being backed up.

