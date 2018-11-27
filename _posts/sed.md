# sed




    sed 's/old/new/g' a.txt #替换a.txt
    sed -n '1p;$p' a.txt # 打印第一行和最后一行
    
    for i in `seq 1 6`
    do
        sed -i '$d' /opt/a.txt
    done                                              #删除6行
    
    
    sed '/abcd/aword' a.txt #找到abcd这一行 在后面添加word
    
    
    sed -i 's/text$/&word/g' /opt/a.txt #在以test结尾的行添加word
    
