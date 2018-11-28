    # while
    
    #!/bin/bash
    while read line
    do
            echo $line
    done < www.txt


----------

    j=0
    i=1
    while ((i<=100))
    do
            j=`expr $i + $j`
            ((i++))
    done
    echo $j


----------

    FILE="/etc/passwd"
    while true
    do
            echo "the time is `date '+ %F-%T'`"
            OLD=`md5sum $FILE |cut -d " " -f 1`
            sleep 5
            NEW=`md5sum $FILE|cut -d " " -f 1`
            if [[ $OLD = $NEW  ]];then
                    echo 'the file has been modifield'
            fi
    done

