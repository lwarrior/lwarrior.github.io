---
layout: post
title:  PJSUA Python Module
date:   2016-03-23 23:35:07 +0800
categories: jekyll update
---


PJSUA的Python模块是PJSUA_API的一个面向对象的Python外壳/抽象。它为构建SIP多媒体UA应用提供了更高层次的API。他将信令、媒体和NAT穿越等功能整合成易用的呼叫控制API，账户管理，联系人列表管理，presence和即时通信，还有一些像本地会议，文件流，本地回放，录音等多媒体特性，以及STUN/TRUN/ICE等强大的NAT穿越技术。

下面是结合[官网](http://pjsip.org)提供的实例进行了简单修改，可用于对远程SIP服务提供者的检测。

以下是过程记录。

### 下载源码及编译python模块 ###

    wget http://www.pjsip.org/release/2.4.5/pjproject-2.4.5.tar.bz2
    tar jxvf pjproject-2.4.5.tar.bz2
    ./configure --prefix=/usr --libdir=/usr/lib --enable-shared --disable-sound --disable-resample --disable-video --disable-opencore-amr
    make dep
    make
    make install
    ldconfig
    cd pjproject-2.4.5/pjsip-apps/src/python
    python ./setup.py install

### 演示代码 simplecall.py ###

    #!/bin/env python
    #-*-coding:utf-8-*-

    __author__ = "Liu Warrior"
    __version__ = "0.1"
    __date__ = "2016-03-18"
    __license__ = "GPL"
    
    import sys
    import pjsua as pj
    
    # Logging callback
    def log_cb(level, str, len):
        print str,

    # Callback to receive events from Call
    class MyCallCallback(pj.CallCallback):
        def __init__(self, call=None):
            pj.CallCallback.__init__(self, call)
    
        # Notification when call state has changed
        def on_state(self):
            print "Call is", self.call.info().state_text,
            print "last code =", self.call.info().last_code, 
            print "(" + self.call.info().last_reason + ")"
            if self.call.info().last_code == 200 and self.call.info().state_text == 'CONNECTING':
                self.call.hangup(603, 'Cancel', None)
                sys.exit(1)

        # Notification when call's media state has changed.
        def on_media_state(self):
            global lib
            if self.call.info().media_state == pj.MediaState.ACTIVE:
                # Connect the call to sound device
                call_slot = self.call.info().conf_slot
                lib.conf_connect(call_slot, 0)
                lib.conf_connect(0, call_slot)

    # Check command line argument
    if len(sys.argv) != 2:
        print "Usage: simplecall.py <dst-URI>"
        sys.exit(1)
    
    try:
        # Create library instance
        lib = pj.Lib()
        # Init library with default config
    
        lib.init(log_cfg = pj.LogConfig(level=3, callback=log_cb))
    
        # No actual audio device
        lib.set_null_snd_dev()
        # Create UDP transport which listens to any available port
        transport = lib.create_transport(pj.TransportType.UDP)
    
        # Start the library
        lib.start()

        # Create local/user-less account
        acc = lib.create_account_for_transport(transport)
    
        # Make call
        call = acc.make_call(sys.argv[1], MyCallCallback())
    
        lib.destroy()
        lib = None
    
    except pj.Error, e:
        print "Exception: " + str(e)
        lib.destroy()
        lib = None
        sys.exit(1)

### 测试 ###

由于是在Linux cmdline模式下，而且没有网卡，所以添加了上面的set_null_snd_dev()以避免无法调用audio设别而产生错误。

执行命令：

    ./simplecall.py sip:123456@192.168.1.100:5060

192.168.1.100上可以知识Ringing()和Answer()即可。


参考：

1.PJSUA Python Module：[http://trac.pjsip.org/repos/wiki/Python_SIP_Tutorial](http://trac.pjsip.org/repos/wiki/Python_SIP_Tutorial)
