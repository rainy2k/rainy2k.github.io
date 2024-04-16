---
title: MIPS
date: 2022-09-15 11:57:00
tags: 
categories: 
- 语言
- MIPS
---

MIPS课程笔记
<!--more-->

# 存储单位寻址
```mipsasm
lw $s1,8($a1)
sw $s1,8($a1)
lw rt,offset(rs) #源操作数为基址寻址
sw rt,offset(rs) #目标操作数为基址寻址
```

# 数组
一下列举一些cpp到mips的转换:

```CPP
cin>>h;
A[12] = A[8]+h;
```

```mipsasm
li 	$v0,5
syscall
move 	$s2,$v0
#ori 	$s2,$v0,0
lw 	$t0,32($s3) #4*8 = 32,基址寻址
add 	$t0,$s2,$t0
sw	$t0,48($s3) #4*12 = 48
```



```cpp
int array[1024];
```

```mipsasm
	.data
array:	.space 4096 
#	.word 1024 is ok
```



```cpp
int pof2[16] = {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16};
```

```mipsasm
pof2:	.word 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16
```



```mipsasm
la		$a0,pof2
lw		$s0,8($a0)
```



```mipsasm
lui		$s1,0x1234
ori		$s1,$s1
addi	        $t0,$0,100
sw		$t0,0($s1)
```





立即数寻址/寄存器寻址{addi/add $s0,$s1,5/$s2}

# 赋值


全局/static变量:

```mipsasm
	.data
i	.word 100
```



```mipsasm
####################
		.data
i:		.space 4
####################
		.text
la		$t0,i
addi	        $t1,$0,100
sw		$t1,0($t0)
####################
li		$t0,100
sw		$t0,i
####################
```



```mipsasm
#语句：i=j;
############################################
#设i,j分别为$s1,$s0
add  	$s1,$0,$s0
############################################
#situation1:
#i为动态变量，地址为0x12345678
#j被指派到寄存器$s1
############################################
lui 	$t0,0x1234      # $s1<----0x12340000
ori 	$t0,$t0,0x5678  # $s1<----0x12345678
lw  	$s1,0($t0)
############################################
#situation2:
#i,j均为动态变量，地址分别为
#0x12345678,0x87654320
############################################
lui  	$t0,0x1234　　#取出来放到高位
ori  	$t0,$t0,0x5678
lw   	$t1,0($t0)
lui  	$t0,0x8765
ori  	$t0,0x4320
sw   	$t1,0($t0)
############################################
#i,j均为自动变量，宏寻址
lw   	$t0,i 
sw   	$t0,j 
```





# 宏指令

mult和div都是二元的，指令周期分别为32和38，它们的宏指令为三元

```mipsasm
mult	$a1,$s1
mflo	$v0			#存低32位
mfhi	$v1			#存高32位
```



```mipsasm
div	$a1,$s1
mflo	$v0			#存商
mfhi	$v1			#存余数
```



```mipsasm
#加载地址宏指令
la		Rdest,address
#加载立即数宏指令
li		Rdest,imm

#$at寄存器编号为1,给汇编程序使用,把address或者imm加载到$at里面
lui		$at,imm		
ori		Rdest,$at,imm

move	        Rdest,Rsrc
```





```mipsasm
Rdest:	存放结果的寄存器
Rsrc:	立即数或者寄存器名
(rs/rt):源操作数
rd:		目的操作数
```



循环移位宏指令

```mipsasm
rol		Rd,Rs,Src2	#循环左移
ror		Rd,Rs,Src2	#循环右移
```



查看实现方法：

```mipsasm
#	rol
srl		$at,Rs,32-sa	#假设sa = 1,则at = rs>>31,仅保留最高位
sll		Rd,Rs,sa		#rd = rs<<1,向左移一位
or		Rd,Rd,$at		#rd = (rs<<1)|(rs>>31)
#	ror
sll		$a1,Rs,32-sa
sri		Rd,Rs,sa
or		Rd,Rd,$at
```





跳转

```mipsasm
blez 	$s6,Quiz	#小于等于0
beqz 	$s6,Quiz	#等于0
bgez 	$s6,Quiz	#大于等于0
```



统计字符串中字符的数量

```mipsasm
la	 	$t2,str
xor		$t1,$t1,$t1	#清空$t1

nextCh:
lb	$t0,($t2)	#load a byte from $t2(the string)
begz	$t0,strEnd	
addiu	$t1,$t1,1
addiu	$t2,$t2,1	#t2地址加1,移动one byte
j nextCh

strEnd:
```



sqrt函数的使用

```mipsasm
#$s0=sqrt($a0*$a0+$a1*a1);
mult 	$a0,$a0
mflo 	$t0        #注意这里体会只低位
mult 	$a1,$a1
mflo 	$t1
add  	$a0,$t0,$t1
jal  	srt        #call the sqrt function
move 	$s0,$v0    #result of sqr is returned in $v0(standard convention)
```



set操作

```mipsasm
slt			<
seq			==
sge			>=
sle	sleu	        <=
sne			!=
```



跳转

```mipsasm
j		target	#无条件跳转
beq		xxxxxx	#条件跳转
#bne,bgez,bgtz,blez,bltz
```







syscall

```assembly
# 指令编号(服务号)放入:$v0
# 输入参数事先放入$a1,$a0
# 执行syscall
# 返回值在$v0中
```





```mipsasm
		.data
msg:    .asciiz "input a char"、

		.text
		.globl 	main
		.ent 	main   
main:   
		li 		$v0,4
		la 		$a0,msg
		syscall

		li 		$v0,12
		syscall
		move 	        $a1,$v0

		li 		$v0,11
		li 		$a0,0x0a 
		syscall

		move 	        $a0,$a1
		li 		$v0,11
		syscall

		li 		$v0,10
		syscall
		.end main
```



while循环/for循环

```cpp
v0=1;
while(a1<a1){
    t1=mem[a1];
    t2=mem[a2];
    if(t1!=t2)break;
    a1++;
    a2--;     
}
v0=0;
return 0;
```

```mipsasm
	li		$v0,1
loop:
	bgeu	        $a1,$a2,done
	lb		$t1,0($a1)
	lb		$t2,0($a2)
	bne		$t1,$t2,break
	addi	        $a1,$a1,1
	addi	        $a2,$a2,-1
	b		loop
break:
	li		$v0,1
```



```cpp
a0 = 0;
for(int t0 = 10;t0>0;t0--)
    a0+=t0;
```

```mipsasm
	li		$a0,1
	li		$t0,10
loop:
	add		$a0,$a0,$t0
	addi	        $t0,$t0,-1
	bgtz	        $t0,loop
```



switch语句：

```cpp
s0=32
top:
    cout<<"input a value from 1 to 3";
    cin>>v0;
    switch(v0){
        case(1){s0<<=1;break;}
        case(2){s0<<=2;break;}
        case(3){s0<<=3;break;}
        defalt:goto top;
    }
    cout<<s0;
```

```mipsasm
            .data
            .align  2          
###############################################################
# 插播一个小知识：
#   alignment:
#   字节：占用一个内存单元
#   半字：占用相邻两个内存单元，地址必须为偶地址，末尾至少1个0
#   字：四个，末尾至少2个0
#   双字：八个，被8整除，末尾至少3个0
#	因为MIPS指令字规模是32bits定长，所以每次都要以字对齐的方式在内存中存放指令
#	所以要.align 2	#	貌似是4字节对齐
#	"当一条指令已从内存取出并存放到指令寄存器(IR)中后，PC地址增量4"
###############################################################
jumptable:  .word   top,case1,case2,case3
msg:        .asciiz "\n\ninput a value from 1 to 3:"
            .text
top:
            li    $v0,4
            la    $a0,msg
            syscall

            li    $v0,5
            syscall

            blez  $v0,top
            li    $t3,3
            bgt   $v0,$t3,top
#################################################################################################
            la    $a1,jumptable   #jumptable的首地址保存在$a1寄存器里面
            sll   $t0,$v0,2       #这个程序利用输入的字符在jumptable里面寻址，地址是4个byte一个单元的
                                  #$t0是位移量，$a1是基地址 
                                  #compute word offset
            add   $t1,$a1,$t0     #$t1现在应该是一个在jumptable里的指针,那么也就是jumptable的基地址
            lw    $t2,0($t1)      #$t2:load an address from jumptable
                                  #访问主存单元寻址必须用lw Rt,offset(Rs)
            jr    $t2             #是你小伙计!!!!!
################################################################################################

case1:      sll   $s0,$s0,1
            b     output
case2:      sll   $s0,$s0,2
            b     output
case3:      sll   $s0,$s0,3
output:
            li    $v0,1
            move  $a0,$s0
            syscall

```





>寄存器 rigister 速度快，代价高，数量有限
>内存：速度慢，容量大，G字节规模，代价低
>高速缓冲储存器：cache
>符号扩展：将符号位扩展为高位数字
>二进制加法；不同符号只要不溢出可以直接相加
>二进制减法：减数取负然后二进制加法
>溢出检测：
>符号不同:不可能溢出
>符号相同:必须和操作数有相同的符号,否则溢出
>ATTENTION:最高位进位不一定溢出，两个负数相加一定进位但不一定溢出



```mipsasm
la	$a0,string	#string的起始位置在$a0处
jal	string_len	
move	$t0,$v0		#调用结束,$v0被存放了string_len的结果,将$v0的值放入$t0
```



```mipsasm
		.text
		.globl	main
		.ent 	main
main:
	jal		ret			# way1 of calling function 
        la		$t0,ret		# way2 of calling function 
        jalr	        $ra,$t0
        li		$v0,1
        syscall
        .end	main
ret:	
        jr		$ra
```





```mipsasm
##########################################
#HOMEWORK4:Fibonacci
#Rabbit array
#Editor:VScode
##########################################
##########################################
        .data
msg1:   .asciiz "Now you have 10 rabbits~\n"
msg2:   .asciiz "the Fibonacci number of 10 is : "
msg3:   .asciiz "\n"
###########################################
        .text
        .globl main
main:

    li    $v0,4
    la    $a0,msg1
    syscall

    ##1
    subi  $sp,$sp,32
    sw    $ra,28($sp)
    sw    $fp,24($sp)
    addiu $fp,$sp,28
    ##2

    li    $t0,10
    sw    $t0,16($sp)

    lw    $a0,16($sp)     #argument fib(n)
    jal   fib
    move  $t0,$v0

    li    $v0,4
    la    $a0,msg2
    syscall

    li    $v0,1
    move  $a0,$t0
    syscall

    lw    $ra,28($sp)
    lw    $fp,24($sp)
    addiu $sp,$sp,32

    li    $v0,10
    syscall

fib:
                            #if(n==1||n==0)return 1;
                            #else return tailfib(n,1,1);
    addiu $sp,$sp,-24
    sw    $ra,20($sp)
    sw    $fp,16($sp)
    addiu $fp,$sp,20        #set up a frame pointer

    li    $t0,1
    sw    $a0,4($fp)        #save argument(n) fp4 saves n

    move  $v1,$a0
    beqz  $v1,then          #if n==0
    beq   $v1,$t0,then      #if n==1

    lw    $a0,4($fp)        #load argument(n)
    li    $a1,1             #$a1 is pre1
    move  $s3,$a0
    move  $s1,$t0
    move  $s2,$t0
    jal   tailfib
    j done

then:

    li   $v0,1
    j    done

done:

    lw    $ra,20($sp)
    lw    $fp,16($sp)
    addiu $sp,$sp,24
    jr    $ra
    .end  fact

tailfib:
    li     $t1,2
    beq    $s3,$t1,then1
    lw     $v0,4($sp)
    add    $v0,$s1,$s2
    move   $s2,$s1
    move   $s1,$v0
    sub    $s3,$s3,1
    sw     $v0,4($sp)
    j      tailfib

then1:
    lw     $v0,4($sp)
    jr     $ra


###########EOF############################
```







```mipsasm
#homework6:
#       int tak(int x,int y,int z){
#               x<=y?:z:tak(tak(x-1,y,z),tak(y-1,z,x),tak(z-1,x,y));
#       }
#editor:VScode
#######################################################
           .data
msg1:      .asciiz  "tak(18,12,6)is: "
#######################################################
        .text
        .globl main
main:
        li    $v0,4            #instruction
        la    $a0,msg1
        syscall

        ##
        addiu $sp,$sp,-28
           sw $ra,24($sp)
           sw $fp,20($sp)
        addiu $fp,$sp,24
        ##

        li     $a0,18            #x==18
        li     $a1,12            #y==12
        li     $a2,6            #z==6
        move   $a3,$a2
        jal     tak

        move $a0,$v0        #out put the result
        li $v0,1
        syscall

        lw $ra,24($sp)        #restore $ra
        lw $fp,20($sp)        #restore $fp
        addiu $sp,$sp,28    #pop stack

        li    $v0,10        #YOU NEED ONE !!!!!!!
        syscall

tak:
        ##
        addiu $sp,$sp,-28    #stack frame is 28 bytes long
        sw $ra,24($sp)
        sw $fp,20($sp)
        addiu $fp,$sp,24
        ##

        sw $a0,4($fp)        #x
        lw $v0,4($fp)        #x
        sw $a1,8($fp)        #y
        lw $v1,8($fp)        #y
        sw $a2,12($fp)       #z
        bgt $a0,$a1,endless
        move $v0,$a2
        j end

endless:
        #
        lw  $v1,4($fp)
        addiu $a0,$v1,-1    #a==tak(x-1,y,z)
        lw $a1,8($fp)
        lw $a2,12($fp)
        move    $a3,$a2
        jal tak
        sw $v0,16($fp)
        #
        lw $v1,8($fp)        #b==tak(y-1,z,x)
        addiu $a0,$v1,-1
        lw $a1,12($fp)
        lw $a2,4($fp)
        move    $a3,$a2
        jal tak
        sw $v0,20($fp)
        #
        lw $v1,12($fp)        #c==tak(z-1,x,y)
        addiu $a0,$v1,-1
        lw $a1,4($fp)
        lw $a2,8($fp)
        move $a3,$a2
        jal tak
        move $a2,$v0
        #
        lw $a0,16($fp)        #answer==tak(a,b,c)
        lw $a1,20($fp)
        move    $a3,$a2
        jal tak
end:
        lw,$ra,24($sp)
        lw $fp,20($sp)
        addiu $sp,$sp,28
        jr $ra

###########EOF#########################
```
