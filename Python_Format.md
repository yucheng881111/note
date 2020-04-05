# Competitive Programming Python format

```
from sys import stdin,stdout

while(True):
    ans=0
    s=stdin.readline()
    if(s==''):
        break
    
    stdout.write(str(ans))
    stdout.write('\n')
```

1. stdin.readline()像是input()，一次讀取一列字串
2. 適用於不知大小的輸入，有需要再.split(delim)
3. 輸入很大時不建議用re來split，因其比對格式很慢
4. 讀取到空字串跳出，有EOF的效果
5. 輸入很大時不建議用input()再except EOFError，效率較差
6. stdout.write()中間只放str，且後面不同於print()，不會加\n




