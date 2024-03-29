.SUFFIXES: .c .o .lo

COMPILE = $(CC) -Wall -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -g -O -DDEBUG_FLAG -DOS_LINUX -DIOEVENT_USE_EPOLL
ENABLE_STATIC_LIB = 0
ENABLE_SHARED_LIB = 1
INC_PATH = -I../common -I../tracker -I/usr/local/include
LIB_PATH =  -lpthread -ldl -rdynamic
TARGET_PATH = /usr/local/bin
TARGET_LIB = /usr/local/lib
TARGET_INC = /usr/local/include
CONFIG_PATH = /etc/fdfs

FAST_STATIC_OBJS = ../common/hash.o ../common/chain.o \
                   ../common/shared_func.o ../common/ini_file_reader.o \
                   ../common/logger.o ../common/sockopt.o \
                   ../common/base64.o ../common/sched_thread.o \
                   ../common/http_func.o ../common/md5.o \
                   ../common/pthread_func.o ../common/local_ip_func.o \
                   ../common/avl_tree.o ../common/connection_pool.o

FDFS_STATIC_OBJS = ../common/fdfs_global.o ../common/fdfs_http_shared.o \
                   ../common/mime_file_parser.o ../tracker/tracker_proto.o \
                   ../tracker/fdfs_shared_func.o \
                   ../storage/trunk_mgr/trunk_shared.o \
                   tracker_client.o client_func.o \
                   client_global.o storage_client.o

STATIC_OBJS =  $(FAST_STATIC_OBJS) $(FDFS_STATIC_OBJS)

FAST_SHARED_OBJS = ../common/hash.lo ../common/chain.lo \
                   ../common/shared_func.lo ../common/ini_file_reader.lo \
                   ../common/logger.lo ../common/sockopt.lo \
                   ../common/base64.lo ../common/sched_thread.lo \
                   ../common/http_func.lo ../common/md5.lo \
                   ../common/pthread_func.lo ../common/local_ip_func.lo \
                   ../common/avl_tree.lo ../common/connection_pool.lo

FDFS_SHARED_OBJS = ../common/fdfs_global.lo ../common/fdfs_http_shared.lo \
                   ../common/mime_file_parser.lo ../tracker/tracker_proto.lo \
                   ../tracker/fdfs_shared_func.lo \
                   ../storage/trunk_mgr/trunk_shared.lo \
                   tracker_client.lo client_func.lo \
                   client_global.lo storage_client.lo

FAST_HEADER_FILES = ../common/common_define.h ../common/hash.h \
                    ../common/chain.h ../common/logger.h \
                    ../common/base64.h ../common/shared_func.h \
                    ../common/pthread_func.h ../common/ini_file_reader.h \
                    ../common/sockopt.h ../common/sched_thread.h \
                    ../common/http_func.h ../common/md5.h ../common/_os_bits.h \
                    ../common/local_ip_func.h ../common/avl_tree.h \
                    ../common/connection_pool.h

FDFS_HEADER_FILES = ../common/fdfs_define.h ../common/fdfs_global.h \
                    ../common/mime_file_parser.h ../common/fdfs_http_shared.h \
                    ../tracker/tracker_types.h ../tracker/tracker_proto.h \
                    ../tracker/fdfs_shared_func.h \
                    ../storage/trunk_mgr/trunk_shared.h \
                    tracker_client.h storage_client.h storage_client1.h \
                    client_func.h client_global.h fdfs_client.h

ALL_OBJS = $(STATIC_OBJS) $(FAST_SHARED_OBJS) $(FDFS_SHARED_OBJS)

ALL_PRGS = fdfs_monitor fdfs_test fdfs_test1 fdfs_crc32 fdfs_upload_file \
           fdfs_download_file fdfs_delete_file fdfs_file_info \
           fdfs_appender_test fdfs_appender_test1 fdfs_append_file \
           fdfs_upload_appender

STATIC_LIBS = libfastcommon.a libfdfsclient.a

SHARED_LIBS = libfastcommon.so.1 libfdfsclient.so.1

ALL_LIBS = $(STATIC_LIBS) $(SHARED_LIBS)

all: $(ALL_OBJS) $(ALL_PRGS) $(ALL_LIBS)
libfastcommon.so.1:
	$(COMPILE) -o $@ $< -shared $(FAST_SHARED_OBJS) $(LIB_PATH)
	ln -fs libfastcommon.so.1 libfastcommon.so
libfdfsclient.so.1:
	$(COMPILE) -o $@ $< -shared $(FDFS_SHARED_OBJS) $(LIB_PATH) -L. -lfastcommon
	ln -fs libfdfsclient.so.1 libfdfsclient.so
libfastcommon.a:
	ar cru $@ $< $(FAST_STATIC_OBJS)
libfdfsclient.a:
	ar cru $@ $< $(FDFS_STATIC_OBJS)
.o:
	$(COMPILE) -o $@ $<  $(STATIC_OBJS) $(LIB_PATH) $(INC_PATH)
.c:
	$(COMPILE) -o $@ $<  $(STATIC_OBJS) $(LIB_PATH) $(INC_PATH)
.c.o:
	$(COMPILE) -c -o $@ $<  $(INC_PATH)
.c.lo:
	$(COMPILE) -c -fPIC -o $@ $<  $(INC_PATH)
install:
	mkdir -p $(TARGET_PATH)
	mkdir -p $(CONFIG_PATH)
	mkdir -p $(TARGET_LIB)
	cp -f $(ALL_PRGS) $(TARGET_PATH)
	if [ 0 -eq 1 ]; then cp -f $(STATIC_LIBS) $(TARGET_LIB); fi
	if [ 1 -eq 1 ]; then cp -f $(SHARED_LIBS) $(TARGET_LIB); fi
	if [ 1 -eq 1 ]; then ln -fs $(TARGET_LIB)/libfastcommon.so.1 $(TARGET_LIB)/libfastcommon.so; fi
	if [ 1 -eq 1 ]; then ln -fs $(TARGET_LIB)/libfdfsclient.so.1 $(TARGET_LIB)/libfdfsclient.so; fi

	if [ ! -f $(CONFIG_PATH)/client.conf ]; then cp -f ../conf/client.conf ../conf/http.conf $(CONFIG_PATH); fi

	mkdir -p $(TARGET_INC)
	mkdir -p $(TARGET_INC)/fastcommon
	mkdir -p $(TARGET_INC)/fastdfs
	cp -f $(FAST_HEADER_FILES) $(TARGET_INC)/fastcommon
	cp -f $(FDFS_HEADER_FILES) $(TARGET_INC)/fastdfs

	if [ 1 -eq 1 -a $(TARGET_LIB) = "/usr/local/lib" ]; then sh ./fdfs_link_library.sh; fi
clean:
	rm -f $(ALL_OBJS) $(ALL_PRGS) $(ALL_LIBS) libfastcommon.so libfdfsclient.so

