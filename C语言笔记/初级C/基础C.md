1. 变量的本质：实质上就是一段内容空间的别名。

2. 寻址的最小单位： Byte    

3. 2^10=1024B=1kB

4. 交换a,b数值的方法：  

   1. ```c
      1. 
         // 引用第三个变量t:
          t=a;
          a=b;
          b=t; 
      
      2.
          a=a+b;     //a=4 b=5   a=9  b=5
          b=a-b;     //a=4 b=5   a=9  b=4
          a=a-b;     //a=4 b=5   a=5  b=4
      3. 
          a=a^b; 
          b=a^b; 
          a=a^b;  
      ```

5. 打印一个数据二进制  

   ```c
   //打印一个数据的二进制数
    void disBin(char ch)
    {
    	int i=8;
    	while(i--)
    	{
    		if((1<<i)&ch)
   			printf("1");
   		else
   			printf("0");
   			
   		if(i%4 == 0)
   		{
   			printf(" ");
           }
   	}
   	putchar(10);  //表示换行
   }
   ```

6. 

   - 1字节    char  
     - char :                           -2^7 ~  -2^7-1    
     - unsigned  char :         0      ~   -2^8-1

   - 2字节   short  
     - unsigned   short：   0       ~    -65535（2^16-1）
     - short                    ：   -32768（-2^15） ~  32767（2^15-1）

   - 四字节   int  
     - unsigned   short： 0 ~  -2^32-1   
     - int                        ：  -2^31  ~  2^31-1

7.   

   - ```C
     char  ch = -1；    //1111 1111 
     short sh = -1；    //1111 1111 1111 1111 
     int   in = -1；    //1111 1111 1111 1111 1111 1111 1111 1111(FFFFFFFF)   
     printf("%hhx\n",ch);    //hhx表示短短整型
     printf("%hhx\n",sh);
     printf("%hhx\n",in);
     printf("%u",(unsigned char)ch);  //结果为255  
     ```

8. 

   一个浮点数（Floating Point Number）由三个基本成分构成：符号（Sign）、阶码（Exponent）和尾数（Mantissa）。通常，可以用下面的格式来表示浮点数：

   | S    | P    | M    |
   | ---- | ---- | ---- |
   |      |      |      |

9.  printf("%#x\n", num);        // 加#会显示出所代表的进制形式