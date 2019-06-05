
#!/bin/bash

echo "# Delete Log File Start!!"

# ---------- option variables

TARGET_FILE_SIZE=5

LIMIT_USAGE=30

TARGET_FILES=("/usr/local/tomcat7/logs/manager.*.log"

                "/usr/local/tomcat7/logs/manager.log.*"
		
                "/usr/local/tomcat7/logs/localhost.*.log"
		
                "/usr/local/tomcat7/logs/localhost_access_log.*.txt"
		
                "/usr/local/tomcat7/logs/host-manager.*.log"
		
                "/usr/local/tomcat7/logs/catalina.*.log"
		
		)
		
EXCLUDE_DATE=$(date +%Y-%m-%d)

# ---------- get disk usage (%)

usage_c=`df -k $1 | /bin/awk '{ use = 0 } { n += 1 } { a = $3 } { b = $4 } n == 2 { use = (a/(a+b)*100); print use } END { }'`

usage=`echo $(printf %.0f $usage_c)`

#echo "$usage"

# ---------- deleteFiles function

function deleteFiles() {

	# if [ -f $1 ]
	
	# then
	
		# ---------- get target file size
		
		arr=( $(ls -tr $1 | awk '{ print $NF }') )
		
		arr_size="${#arr[@]}"
		
		if [ $arr_size -gt 0 ]
		
		then
		
			target_size=$TARGET_FILE_SIZE
			
			if [ $arr_size -lt $target_size ]
			
			then
			
				target_size=$arr_size
				
			fi
			
			# echo "# target_size: $target_size"
			
			# echo "# arr_size: $arr_size"

			# ---------- delete files
			
			if [ $usage -ge $LIMIT_USAGE ]
			
			then
			
				count=0
				
				for i in "${arr[@]}"
				
				do
				
					if [[ "$i" != *"$EXCLUDE_DATE"* ]]
					
					then
					
						if [ $count -le $target_size ]
						
						then
						
							# echo $i
							
							rm -rf $i
							
							count=$[count+1]
							
						fi
						
					fi
					
				done
				
				if [ "$count" -gt 0 ]
				
				then
				
					echo "$1: $count"
					
				fi
				
			fi
			
		fi
		
	# fi
	
}


# ---------- delete

if [ "${#TARGET_FILES[@]}" -gt 0 ]

then

	for i in "${TARGET_FILES[@]}"
	
	do
	
		deleteFiles "$i"
		
	done
	
fi

echo "# Delete Log File End!!"

