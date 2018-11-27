# for循环

    #!/bin/bash
    for website in www.baidu.com www.taobao.com www.qq.com
    do
            echo $website
    done


 


----------


   
    
    for i in `seq 1 100`
    do
            echo "num in $i"
    done
    


----------


    x=0
    for ((y=1;y<=100;y++))
    do
            x=`expr $x + $y`
    done
    echo $x