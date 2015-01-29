---
layout: post
title: "MikroTik RouterBoard Backup Script"
date: 2014-12-26 20:38:25 -0500
comments: true
categories: ["networking", "mikrotik"]
---
{% highlight bash %}
#!/bin/bash

base=/home/backups/mikrotik/hosts

hour=`date "+%H"`
dow=`date "+%w"`
dom=`date "+%d"`

if [ "$dom" = "01" ]; then
	backup_file=`date "+%Y-%m-%d"`.monthly
elif [ "$dow" = "0" ]; then
	backup_file=`date "+%Y-%m-%d"`.weekly
else
	backup_file=`date "+%Y-%m-%d"`.daily
fi

find $base -mtime +30 -name "*weekly*" -exec rm {} \;
find $base -mtime +7 -name "*daily*" -exec rm {} \;

cd $base
ls -1 | while read host ; do
	echo backing up $host
	ssh -n admin-ssh@$host "/system backup save name=$backup_file" > /dev/null
	ssh -n admin-ssh@$host "/export file=$backup_file" > /dev/null
	scp admin-ssh@$host:$backup_file.backup $base/$host > /dev/null
	scp admin-ssh@$host:$backup_file.rsc $base/$host > /dev/null
	echo "rm $backup_file.backup" | sftp admin-ssh@$host &> /dev/null
	echo "rm $backup_file.rsc" | sftp admin-ssh@$host &> /dev/null
done

{% endhighlight %}