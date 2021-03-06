#-------------------------------------------------------------------------------------------------------------
# 一个Makefile文件通常所包含的内容和步骤：
#	1. 指定路径  VPATH
#	2. 源文件和目标文件列表，如：
#		SRC_FILES =
#		OBJ_FILES =
#		APP_FILES =
#		DEP_FILES = 头文件依赖
#       3.所需的编译标志，如：
#		CFLAGS =
#		LDFLAGS =
#	4.指定默认生成目标，如:
#		all: app1 app2
#	    4.1 手动设置特定目标生成规则
#	5.目标文件，库文件，应用程序的生成规则，如：
#		%.o:%.c
#			gcc -c .........
#		libapp.a:$(LIBOBJ)
#			ar ........
#	6.清理方法
#	7.所包含头文件的依赖和跟踪
#	8.安装方法
#
#
#
#
#                                ----- 自动依赖跟踪例子 -----
#
# 1. make会从头到尾一次次分析Makefile（从树根捋到每个树叉），每分析完一遍确定一个可以解决的目标或未知量，
#  接着就运行解出这个目标或未知量。
#  这样一次次直到最终目标生成。因此会看到$(warning...)内容被调用了好几次。
#
# 2. %.dep:%.c 这样的模板应该是按照最近原则进行匹配的。不然 %:%.c 这个模板可被所有情况引用了！！！
#
# 3. Makefile中应用shell命令需要注意：
#	3.1 每个命令需要用“;”分割，并且每一此shell命令从头到尾必须用”\“连接符号。
#	3.2 shell中的$var在Makefile中必须用$$var
#
#---------------------------------------------------------------------------------------------------------------

CC=/home/midas-zhou/openwrt_widora/staging_dir/toolchain-mipsel_24kec+dsp_gcc-4.8-linaro_uClibc-0.9.33.2/bin/mipsel-openwrt-linux-gcc


#### ----- 产生文件列表 ------
SRC_FILES = $(wildcard *.c)
OBJS = $(patsubst %.c, %.o, $(SRC_FILES))
DEP_FILES = $(patsubst %.c,%.dep,$(SRC_FILES))
APPS = tmp_app

#### -----  编译标志 -----
CFLAGS = -Wall
CFLAGS += -I./ -I./include
LDFLAGS = -L./lib -ljpeg -lm


#----- 打印依赖文件  include $(DEP_FILES)
#-----如果前面有include命令，那么make命令只会执行下面all中的第一个依赖文件，除非用make all完整命令
#-----把 include &(DEP_FILES) 防到后面就 OK了。
#----在Makefile中$$var才相当于shell中的$var
$(warning "----- all:$(APPS) -----")
all:  $(APPS)
	@for var in $(APPS);\
	do \
		cat $$var.dep; \
	done

$(warning "----- %:%.c -----")


#### ------ 手动设置特定目标生成规则  ------
####----- !!!! manually edit here !!!! -----
tmp_app: $(OBJS)  ###touch.o spi.o
	$(CC) $(CFLAGS) $(LDFLAGS) $(OBJS) -o tmp_app


#### ----- 目标文件自动生成规则 -----
%:%.c $(DEP_FILES)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $@.c


#### ----- 清除目标 -----
clean:
	rm -rf $(OBJS) $(APPS) $(DEP_FILES)


include $(DEP_FILES)

$(warning "----- %.dep: %.c -----")

#### ---- 依赖文件自动生成规则 -----
%.dep: %.c
	@set -e; rm -f $@
	@$(CC) -MM $(CFLAGS) $< > $@.123
	@sed 's,\($*\)\.o[: ]*,\1 : ,g' < $@.123 > $@
	cat $@.123


#------- \1 表示第一个$(*)所代表的内容
	@rm -f $@.123


#----- 下面这个会被执行两遍 ---------
$(warning "----- end -----")

#-------------------------------------------------------------------------------------------
#
#  自动依赖的例子：
#  midas-zhou@midas-hp:~/apptest$ gcc -MM fbin_r.c | sed 's,\($*\)\.o[: ]*,\1 : ,g'
#  fbin_r : fbin_r.c fbin.h
#
#   gcc -M fbin_r.c 会将标准库的头文件也包括进来，用 gcc -MM 就不会了。
#
#-------------------------------------------------------------------------------------------
