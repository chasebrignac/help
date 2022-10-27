# help
Anything helpful I find goes here.

I only hope it helps you and you can pay me back by starring my repos and telling your team about how helpful it was.

### Running out of space in Ubuntu

The dreaded error message
```
No such file or directory
```

I would recommend starting with the following:

First check how much space you have available and which partition is filling up with the following command:
```
df -h
```
The output would look something like this:

```
Filesystem      Size  Used Avail Use% Mounted on
udev            2.0G     0  2.0G   0% /dev
tmpfs           395M  1.8M  393M   1% /run
/dev/vda1        25G   20G  4.4G  82% /
```
In my case I am using 20G out of 25GB available for my root partition. Basically the df command reports the file system disk space usage.

Also if you have enough space, make sure to check your Inode usage:
```
df -i
```
Then depending on the partition which is filling up you could use the following command to find any files that are larger than 500MB for example:
```
sudo find / -type f -size +500M -exec ls -lh {} \;
```
The output should look something like:

```
-rw-r--r-- 1 root root 1.8G May 13 15:43 /home/user1/large-file.txt
-rw-r--r-- 1 root root 1.8G May 13 15:42 /home/bobby/large-file.txt
-rw-r--r-- 1 root root 1.8G May 13 15:43 /home/bobby/public_html/wp-content/core.dump
-rw-r--r-- 1 root root 1.8G May 13 15:42 /home/bobby/public_html/error_log
```
Depending on the size of your disk, the command might take a while to complete. You can then go ahead and delete any of the large files that you do not need.

If there are no large files maybe there are a lot of small files which are exhausting the available webspace, what you could do is use the du command which estimates the file space usage:
```
du -h --max-depth=1 /
```
For example:

```
root@bobby:~$ cd /home/
root@bobby:/home$ du -h --max-depth=1
16K ./testuser
28K ./user
16K ./root
1.8G ./user2
5.0G ./bobby
7.1G .
```
Then we can cd into the largest folder and run the command again:

```
root@bobby:/home$ cd bobby/
root@bobby:/home/bobby$ du -h --max-depth=1
76K ./.npm
4.0K ./script
5.0G ./public_html
12K ./.ssh
5.0G .
```
We would need to do that a few more times until we get to the folder that is using those 5GB:

```
root@bobby:/home/bobby$ cd public_html/
root@bobby:/home/bobby/public_html$ du -h --max-depth=1
8.0K ./test
4.9G ./wp-content
4.0K ./wpcli.bobbyiliev.com
14M ./wp-includes
7.4M ./wp-admin
16K ./node
5.0G .
```
Then one more time:

```
root@bobby:/home/bobby/public_html$ cd wp-content/
root@bobby:/home/bobby/public_html/wp-content$ du -h --max-depth=1
3.6M ./themes
252K ./plugins
4.0K ./upgrade
4.9G ./large-folder
4.9G .
```
And again:

```
root@bobby:/home/bobby/public_html/wp-content/large-folder$ du -h --max-depth=1
4.9G .
```
We’ve found the folder! We can see how many files are in the folder by running this command:

```
root@bobby:/home/bobby/public_html/wp-content/large-folder$ ls -lah | wc -l
4794
```
Note: if you have a database service like MySQL, make sure Not to delete any files manually from the MySQL folder. This would cause issues with your database.

If you are unable to clear any disk space I would recommend upgrading your Droplet.

Let me know if you have any questions!

Regards,

Bobby Source
