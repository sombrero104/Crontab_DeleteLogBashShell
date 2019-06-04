# Crontab_DeleteLogBashShell




#!/bin/bash

echo "# Delete Log File Start!!"

# ---------- option variables
TARGET_FILE_SIZE=10
LIMIT_USAGE=10

# ---------- get disk usage (%)
usage_c=`df -k $1 | /bin/awk '{ use = 0 } { n += 1 } { a = $3 } { b = $4 } n == 2 { use = (a/(a+b)*100); print use } END { }'`
usage=`echo $(printf %.0f $usage_c)`
#echo "$usage"


# ---------- get target file size
arr=( $(ls -tr /usr/local/tomcat7/logs/manager.log.* | awk '{ print $NF }') )
arr_size="${#arr[@]}"
target_size=$TARGET_FILE_SIZE

if [ $arr_size -lt $target_size ]
then
        target_size=$arr_size
fi

echo "# target_size: $target_size"
echo "# arr_size: $arr_size"


# ---------- delete files
if [ $usage -ge $LIMIT_USAGE ]
then
        count=0
        for i in "${arr[@]}"
        do
                if [ $count -le $target_size ]
                then
                        echo $i
                fi
                count=$[count+1]
                #echo $count
        done
fi

echo "# Delete Log File End!!"
