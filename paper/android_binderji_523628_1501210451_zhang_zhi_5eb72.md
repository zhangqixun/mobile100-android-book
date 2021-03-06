# Android Binder机制(1501210451 张志康)

学号：1501210451  姓名：张志康  专业：集成电路工程

# 
本文主要分析native层和Java层的Android binder通信机制。

binder是Android最为常见的进程通信机制之一，其驱动和通信库是binder的核心，分别由C和C++编写，应用程序通过JNI同底层库进行关联，也就是native层驱动和通信库通过Java层包装后被Java层调用。

源代码网址：http://androidxref.com/4.2_r1/

参考博客：http://blog.csdn.net/coding_glacier/article/details/7520199



**一、native层整体通信流程**

* **通信流程概要** 

    在探究binder通信流程之前，首先我们需要了解Binder机制的四个组件：Client、Server、Service Manager和Binder驱动程序。关系如图：
    ![](zzk_1.png)
    
    应用程序最终目的是完成Client组件和Server组件之间的通信。ServiceManger对于大家而言是一个公共接入点，0便是ServiceManger的句柄值。

    从表面看通信建立的流程便是注册和获取的过程： 
    1、client通过参数（Parcel包）传递进行通信请求；
    
    2、在收到通信请求时，Server组件需要通过0这个句柄值访问ServiceManger，在ServiceManger中注册一个binder实体。并关联一个字符串；
    
    3、Client组件通过0这个标识去访问ServiceManger，通过一个字符去查询Server组件的引用，此ServiceManger将Server注册的binder实体的一个引用传递给Client端，此时client便可根据这个引用同server进行通信了。
    
    由以上可知，在收到请求时server将一个binder实体传递给C进程，而client得到的只是binder的一个引用，进而调用binder实体的函数。BpBinder和BBinder分别代表binder 的引用和实体，它们均继承自IBinder类。
    
    在描述具体流程之前我们先来了解binder通信中需要用到的三个主要基类：
    
    1.基类IInterface：
    为server端提供接口，它的子类声明了service能够实现的所有的方法；
    
    2.基类IBinder
    BBinder与BpBinder均为IBinder的子类，因此可以看出IBinder定义了binder IPC的通信协议，BBinder与BpBinder在这个协议框架内进行的收和发操作，构建了基本的binder IPC机制。
    
    3.基类BpRefBase
    client端在查询SM获得所需的的BpBinder后，BpRefBase负责管理当前获得的BpBinder实例。
    
* **ServiceManger**

    首先我们来了解一下在通信流程中ServiceManger所做的工作。
    
	ServiceManger是一个linux级进程，是一个service管理器（service向SM注册是，service就是一个client，而ServiceManger便是server），即我们前边提到的：每一个service被使用之前，均要向ServiceManger注册，客户端通过查询ServiceManger是否存在此服务来获取service的handle（标识符）。
	    ServiceManger入口函数为：service_manager.c
        位于：/frameworks/base/cmds/servicemanager/

        int main(int argc, char **argv)
        {
            struct binder_state *bs;
            void *svcmgr = BINDER_SERVICE_MANAGER;
        
            bs = binder_open(128*1024);
        	
            if (binder_become_context_manager(bs)) {
                ALOGE("cannot become context manager (%s)\n", strerror(errno));
                return -1;
            }
        
            svcmgr_handle = svcmgr;
            binder_loop(bs, svcmgr_handler);
            return 0;
        }
        主要工作：
            1. 初始化binder，打开/dev/binder设备，在内存中为binder映射128Kb空间。
        bs = binder_open(128*1024);
        其中binder_open位于binder.c中，源代码为：
        struct binder_state *binder_open(unsigned mapsize)
        {
            struct binder_state *bs;
        
            bs = malloc(sizeof(*bs));
            if (!bs) {
                errno = ENOMEM;
                return 0;
            }
        
            bs->fd = open("/dev/binder", O_RDWR);
            ……
            return 0;
        }
            2. 指定SM对于代理binder的handle为0，即client尝试同SM通信时创建一个handle为0的代理binder。
        void *svcmgr = BINDER_SERVICE_MANAGER;
        svcmgr_handle = svcmgr;
        其中BINDER_SERVICE_MANAGER在binder.h中被指定为0：
        #define BINDER_SERVICE_MANAGER ((void*) 0)
            3. 通知binder driver(BD)使SM成为BD的context manager；
        if (binder_become_context_manager(bs)) {
        LOGE("cannot become context manager (%s)/n", strerror(errno));
        return -1;
        }
        binder_become_context_manager(bs)源码位于binder.c中：
        	int binder_become_context_manager(struct binder_state *bs)
        {
            return ioctl(bs->fd, BINDER_SET_CONTEXT_MGR, 0);
        }
            4.进入一个死循环，不断读取内核的binder    driver，查看是否有对service的操作请求，如果有调用svcmgr_handler来处理请求操作：
        binder_loop(bs, svcmgr_handler);
        binder_loop(,)源码位于binder.c中：
        void binder_loop(struct binder_state *bs, binder_handler func)
        {
            int res;
            struct binder_write_read bwr;
            unsigned readbuf[32];
        	……
            }
        }
            5.维护一个svclist列表来存储service的信息。
        	源码位于service_manager.c：
        int svcmgr_handler(struct binder_state *bs,
                           struct binder_txn *txn,
                           struct binder_io *msg,
                           struct binder_io *reply)
        {
            struct svcinfo *si;
            ……
        }
![](zzk_2.png)
* **ProcessState**

    ProcessState是每个进程在使用Binder通信时都需要维护的，用来描述当前进程的binder状态。

    ProcessState主要完成两个功能：
    
    1.创建一个thread负责与内核中的binder模块进行通信（Poolthread）。
    
    在Binder IPC中，所有进程均会启动一个thread来负责与binder来直接通信，也就是不断读写binder，这个线程主体是一个IPCThreadState对象（具体介绍见第4节）。

	        Poolthread启动方式：ProcessState::self()->startThreadPool();
        /frameworks/native/libs/binder/ProcessState.cpp
        136void ProcessState::startThreadPool()
        {
            AutoMutex _l(mLock);
        `   if (!mThreadPoolStarted) {
                mThreadPoolStarted = true;
                spawnPooledThread(true);
            }
        }

	2.为知道的handle创建一个BpBinder对象，并管理进程中所有的BpBinder对象。
	
    BpBinder在第一节已经提到，其主要功能是负责client向BD发送调用请求的数据，是client端binder通信的核心，通过调用transact向BD发送调用请求的数据。
    
	ProcessState通过如下函数获取BpBinder对象：
	
	        /frameworks/native/libs/binder/ProcessState.cpp
        	sp<IBinder> ProcessState::getContextObject(const sp<IBinder>& caller)
        {
            return getStrongProxyForHandle(0);
        }

        sp<IBinder> ProcessState::getStrongProxyForHandle(int32_t handle)
        {
            sp<IBinder> result;
        
            AutoMutex _l(mLock);
        
            handle_entry* e = lookupHandleLocked(handle);
		……
            return result;
        }

        ProcessState::handle_entry* ProcessState::lookupHandleLocked(int32_t handle)
        {
            const size_t N=mHandleToObject.size();
            if (N <= (size_t)handle) {
                handle_entry e;
                e.binder = NULL;
                e.refs = NULL;
                status_t err = mHandleToObject.insertAt(e, N, handle+1-N);
                if (err < NO_ERROR) return NULL;
            }
            return &mHandleToObject.editItemAt(handle);
        }

    在获取BpBinder对象的过程中，ProcessState会维护一个BpBinder的vecto：mHandleToObject(具体调用过程见上述源代码)。
    
    创建一个BpBinder实例时，回去查询mHandleToObject，如果对应的handler以及有binder指针，就不再创建，否则创建并插入到mHandlerToObject中（具体代码见上述的lookupHandleLocked）。
    
    BpBinder构造函数位于/frameworks/native/libs/binder/BpBinder.cpp：
    
        	BpBinder::BpBinder(int32_t handle)
            : mHandle(handle)
            , mAlive(1)
            , mObitsSent(0)
            , mObituaries(NULL)
        {
            ALOGV("Creating BpBinder %p handle %d\n", this, mHandle);
        
            extendObjectLifetime(OBJECT_LIFETIME_WEAK);
            IPCThreadState::self()->incWeakHandle(handle);
        }
        
    通过此构造函数我们可以发现：BpBinder会将通信中server的handle记录下来。当有数据发送时，会把数据的发送目标通知BD。
    
        	
* **IPCThreadState**

    IPCThreadState也是一个单例模式，由上边我们已知每个进程维护一个ProcessState实例，且ProcessState只启动一个Pool thread，因此一个进程之后启动一个Pool thread。
    
    IPCThreadState实际内容为：

        void IPCThreadState::joinThreadPool(bool isMain)
        {
            LOG_THREADPOOL("**** THREAD %p (PID %d) IS JOINING THE THREAD POOL\n", (void*)pthread_self(), getpid());
        
            mOut.writeInt32(isMain ? BC_ENTER_LOOPER : BC_REGISTER_LOOPER);        
            set_sched_policy(mMyThreadId, SP_FOREGROUND); 
            status_t result;
            do {
                int32_t cmd;
                if (mIn.dataPosition() >= mIn.dataSize()) {
                    size_t numPending = mPendingWeakDerefs.size();
                    if (numPending > 0) {
                        for (size_t i = 0; i < numPending; i++) {
                            RefBase::weakref_type* refs = mPendingWeakDerefs[i];
                            refs->decWeak(mProcess.get());
                        }
                        mPendingWeakDerefs.clear();
                    }
                    numPending = mPendingStrongDerefs.size();
                    if (numPending > 0) {
                        for (size_t i = 0; i < numPending; i++) {
                            BBinder* obj = mPendingStrongDerefs[i];
                            obj->decStrong(mProcess.get());
                        }
                        mPendingStrongDerefs.clear();
                }
                }
                result = talkWithDriver();
                if (result >= NO_ERROR) {
                    size_t IN = mIn.dataAvail();
                    if (IN < sizeof(int32_t)) continue;
                    cmd = mIn.readInt32();
                    IF_LOG_COMMANDS() {
                        alog << "Processing top-level Command: "
                            << getReturnString(cmd) << endl;
                    }
                    result = executeCommand(cmd);
                }
                if(result == TIMED_OUT && !isMain)         {
                    break;
                }
            } while (result != -ECONNREFUSED && result != -EBADF);
        
            LOG_THREADPOOL("**** THREAD %p (PID %d)         IS LEAVING THE THREAD POOL err=%p\n",
                (void*)pthread_self(), getpid(), (void*)result);
        
            mOut.writeInt32(BC_EXIT_LOOPER);
            talkWithDriver(false);
        }
    ProcessState中有2个Parcel成员（mIn和mOut），由以上代码可见，Pool Thread会不断查询BD中是否有数据可读，若有，则保存在mIn；不停检查mOut是否有数据需要向BD发送，若有，则写入BD。
    
    根据第三节提到的：BpBinder通过调用transact向BD发送调用请求的数据，也就是说ProcessState中生成的BpBinder实例通过调用IPCThreadState的transact函数来向mOut中写入数据，这样的话这个binder IPC过程的client端的调用请求的发送过程就讲述完毕。
    
    IPCThreadState有两个重要的函数，talkWithDriver函数负责从BD读写数据，executeCommand函数负责解析并执行mIn中的数据。
    
![](zzk_3.png)
*  **两个接口类**

   1.BpINTERFACE

	 client在获得server端service时，server端向client提供一个接口，client在这个接口基础上创建一个BpINTERFACE，使用此对象，client端的应用能够像本地调用一样直接调用server端的方法，而不必关系binder IPC实现。
	 
	    BpINTERFACE原型如下：
        /frameworks/native/include/binder/IInterface.h 
        template<typename INTERFACE>
        class BpInterface : public INTERFACE, public BpRefBase
        {
        public:
                                        BpInterface(const sp<IBinder>& remote);
        
        protected:
            virtual IBinder*            onAsBinder();
        };
        
    可见，BpINTERFACE继承自INTERFACE、BpRefBase。
    
    BpINTERFACE既实现了service中各方法的本地操作，将每个方法的参数以Parcel的形式发送给BD。同时又将BpBinder作为了自己的成员来管理，将BpBinder存储在mRemote中，BpServiceManager通过调用BpRefBase的remote()来获得BpBinder指针。
    
    2.BnINTERFACE	
 
	
        同样位于/frameworks/native/include/binder/IInterface.h 
        template<typename INTERFACE>
        class BnInterface : public INTERFACE, public BBinder
        {
        public:
            virtual sp<IInterface>      queryLocalInterface(const String16& _descriptor);
            virtual const String16&     getInterfaceDescriptor() const;
        
        protected:
            virtual IBinder*            onAsBinder();
        }; 


  由代码可知，BnInterface继承自INTERFACE、BBinder。
  
  class BBinder : public
  
  IBinder，由此可见，server端的binder操作及状态维护是通过BBinder来实现的。BBinder即为binder的本质。
    
	
 3.接口类总结
	
  由上节的描述及刚才对于两个接口类源代码分析可知：BpBinder是client端用于创建消息发送的机制，而BBinder是server端用于接口消息的通道。

  BpBinder是client创建的用于消息发送的代理，其transact函数用于向IPCThreadState发送消息，通知其有消息要发送给BD，部分源代码如下：

	/frameworks/native/libs/binder/BpBinder.cpp
    status_t BpBinder::transact(
        uint32_t code, const Parcel& data, Parcel* reply, uint32_t flags)
    {
        if (mAlive) {
            status_t status = IPCThreadState::self()->transact(
                mHandle, code, data, reply, flags);
            if (status == DEAD_OBJECT) mAlive = 0;
            return status;
        }
        return DEAD_OBJECT;
            }
            default:
                return UNKNOWN_TRANSACTION;
        }
    }
  由BBinder的源码可知，其作用是当IPCThreadState收到BD消息时，通过transact方法将其传递给它的子类BnSERVICE的onTransact函数执行server端的操作。部分源码如下：

        /frameworks/native/libs/binder/Binder.cpp
    	status_t BBinder::transact(
            uint32_t code, const Parcel& data, Parcel* reply, uint32_t flags)
        {
            data.setDataPosition(0); 
            status_t err = NO_ERROR;
            switch (code) {
                case PING_TRANSACTION:
                    reply->writeInt32(pingBinder());
                    break;
                default:
                    err = onTransact(code, data, reply, flags);
                    break;
            }
            if (reply != NULL) {
                reply->setDataPosition(0);
            }
            return err;
        }
        
  由上述可知，BpINTERFACE，BnINTERFACE均来自同一接口类IINTERFACE，由此保证了service方法在C/S两端的一致性。

* **writeStrongBinder和readStrongBinder**

 

1. writeStrongBinder是client将一个binder传送给server时需要调用的函数。

        具体源码如下：
    	status_t Parcel::writeStrongBinder(const     sp<IBinder>& val)
    	{
    	    return flatten_binder(ProcessState::self(), val, this);
    	}
    	flatten_binder为：
        status_t flatten_binder(const sp<ProcessState>& proc,
        const sp<IBinder>& binder, Parcel* out)
        {
            flat_binder_object obj;
        
            obj.flags = 0x7f | FLAT_BINDER_FLAG_ACCEPTS_FDS;
            if (binder != NULL) {
                IBinder *local = binder->localBinder();
                if (!local) {
                    BpBinder *proxy = binder->remoteBinder();
                if (proxy == NULL) {
                    LOGE("null proxy");
            }
            const int32_t handle = proxy ? proxy->handle() : 0;
            obj.type = BINDER_TYPE_HANDLE;
            obj.handle = handle;
            obj.cookie = NULL;
        } else {
            obj.type = BINDER_TYPE_BINDER;
            obj.binder = local->getWeakRefs();
            obj.cookie = local;
        }
        } else {
            obj.type = BINDER_TYPE_BINDER;
            obj.binder = NULL;
            obj.cookie = NULL;
        } 
        return finish_flatten_binder(binder, obj, out);
        }
  下边举例说明，addService源码为：
    	/frameworks/native/libs/binder/IServiceManager.cpp
    	virtual status_t addService(const String16& name,     const sp<IBinder>& service,
                    bool allowIsolated)
            {
                Parcel data, reply;
                data.writeInterfaceToken(IServiceManager::getInterfaceDescriptor());
                data.writeString16(name);
                data.writeStrongBinder(service);
                data.writeInt32(allowIsolated ? 1 : 0);
                status_t err =         remote()->transact(ADD_SERVICE_TRANSACTION, data, &reply);
                return err == NO_ERROR ? reply.readExceptionCode() : err;
            }
  由上述代码块可知，写入到parcel的binder类型为BINDER_TYPE_BINDER，然而SM收到的Service的binder类型必须为BINDER_TYPE_HANDLE才会将其添加到svclist中，因此说，addService开始传递的binder类型为BINDER_TYPE_BINDER然而SM收到的binder类型为BINDER_TYPE_HANDLE，中间经历了一个改变，代码如下：
        	drivers/staging/android/Binder.c
    	static void binder_transaction(struct binder_proc *proc,
                   struct binder_thread *thread,
                   struct binder_transaction_data *tr, int reply){
        	……
            if (fp->type == BINDER_TYPE_BINDER)
           fp->type = BINDER_TYPE_HANDLE;
         else
              fp->type = BINDER_TYPE_WEAK_HANDLE;
        fp->handle = ref->desc;
        ……
        }
  由以上函数可知，SM只保存了Service binder的handle和name，当client需要和Service通信时，如何才能获得Service得binder呢？需要由readStrongBinder来完成。
2. readStrongBinder

  Client向server请求时，server向BD发送一个binder返回给SM(保存handle和name)，当IPCThreadState收到由返回的parcel时，client通过这一函数将这个server返回给SM的binder读出。

        源码为：
        /frameworks/native/libs/binder/Parcel.cpp
        sp<IBinder> Parcel::readStrongBinder() const
        {
            sp<IBinder> val;
            unflatten_binder(ProcessState::self(), *this,      &val);
            return val;
        }

        unflatten_binder为：
        status_t unflatten_binder(const sp<ProcessState>& proc,
            const Parcel& in, sp<IBinder>* out)
        {
            const flat_binder_object* flat = in.readObject(false);
        
            if (flat) {
                switch (flat->type) {
                    case BINDER_TYPE_BINDER:
                        *out = static_cast<IBinder*>(flat->cookie);
                        return finish_unflatten_binder(NULL, *flat, in);
                    case BINDER_TYPE_HANDLE:
                        *out = proc->getStrongProxyForHandle(flat->handle);
                        return finish_unflatten_binder(
                            static_cast<BpBinder*>(out->get()),         *flat, in);
                }
            }
            return BAD_TYPE;
        }
        
  由如上源码可知：发现如果server返回的binder类型为BINDER_TYPE_BINDER的话，直接获取这个binder；如果server返回的binder类型为BINDER_TYPE_HANDLE时，那么需要重新创建一个BpBinder返回给client。Client通过获得SMhandle来重新构建代理binder与server进行通信。

至此，native通信机制已构建完毕。
  

**二、Java层的binder机制**

*下边来解析一下java层对于binder的封装过程，分四部分来进行介绍：Java层ServiceManager的结构、如何注册一个Service、如何得到一个Service、Service代理对象方法的过程。*
   
***ServiceManager的结构:**

      在Java层，ServiceManager的函数源码为：
	    /frameworks/base/core/java/android/os/ServiceManager.java
	    public final class ServiceManager {
                } 
            public static IBinder getService(String name) {
                
            }
        
            public static void addService(String name, IBinder service) {
               
            }
        
            public static void addService(String name, IBinder service, boolean allowIsolated) {
            }
        
            public static IBinder checkService(String name) {
            }
        
            public static String[] listServices() throws RemoteException {
                
            }
        
            public static void initServiceCache(Map<String, IBinder> cache) {
                
            }
由源码可知，ServiceManager没有继承其他类，下边我们来分析ServiceManager管理binder通信的流程。


* **在Java层注册Service：**

  通过ServiceManager的addService()可注册自己，其传输了两个参数：String name, IBinder service，分别为name和BBinder的子类对象，跟native层ServiceManager中Service的注册方法相一致。

	    具体源码如下：
	    public static void addService(String name, IBinder service) {
                try {
                    getIServiceManager().addService(name, service, false);
                } catch (RemoteException e) {
                    Log.e(TAG, "error in addService", e);
                }
            }
        
  getIServiceManager().addService表明将此操作请求转发给了getIServiceManager()，返回一个IServiceManger类型的sServiceManager对象，源码如下：

    	private static IServiceManager getIServiceManager() {
                if (sServiceManager != null) {
                    return sServiceManager;
                }
                sServiceManager = ServiceManagerNative.asInterface(BinderInternal.getContextObject());
                return sServiceManager;
            }
        
  BinderInternal.getContextObject在native层得到BpBinder对象。

  ServiceManagerNative.asInterface
  将BpBinder封装为Java层可用的ServiceManagerProxy对象。

  下面来通过源码具体分析BpBinder封装为ServiceManagerProxy的过程：

	static public IServiceManager asInterface(IBinder obj)
            {
                if (obj == null) {
                    return null;
                }
                IServiceManager in =
                    (IServiceManager)obj.queryLocalInterface(descriptor);
                if (in != null) {
                    return in;
                }
        
                return new ServiceManagerProxy(obj);
            }
    
  由源码可知，通过asInterface的转换，BpBinder对象生成了ServiceManagerProxy对象。也就是说getIServiceManager()得到的是一个ServiceManagerProxy对象，那么ServiceManagerProxy又是什么，下边来具体分析一下。

        class ServiceManagerProxy implements IServiceManager {
            public ServiceManagerProxy(IBinder remote) {
                mRemote = remote;
            }
            public IBinder asBinder() {
                return mRemote;
            }
            public IBinder getService(String name) throws RemoteException {
                
            }
            public IBinder checkService(String name) throws RemoteException {
                
            }
            public void addService(String name, IBinder service, boolean allowIsolated)
                    throws RemoteException {
               
            }
            public String[] listServices() throws RemoteException {
                
            }
            public void setPermissionController(IPermissionController controller)
                    throws RemoteException {
                
            }
            private IBinder mRemote;
        }
        
  由源码可知，ServiceManagerProxy继承自IServiceManager，提供add、get、list、check等方法。由以上分析可知，通过getIServiceManager的便可得到ServiceManagerProxy对象，调用其addService方法便可进行注册，addService源码如下：

        public void addService(String name, IBinder     service, boolean allowIsolated)
                    throws RemoteException {
                Parcel data = Parcel.obtain();
                Parcel reply = Parcel.obtain();
                data.writeInterfaceToken(IServiceManager.descriptor);
            data.writeString(name);
                data.writeStrongBinder(service);
                data.writeInt(allowIsolated ? 1 : 0);
                mRemote.transact(ADD_SERVICE_TRANSACTION, data, reply, 0);
                reply.recycle();
                data.recycle();
            }
        
  可知，将name和Service对象封装到Parcel中，调用transact()方法送出，并将当前操作标记为ADD_SERVICE_TRANSACTION，根据上一章提到的内容，transact()便会调用到BpBinder中，此时便进入到native层的使用，这部分内容已经在上一章节分析完毕，具体流程图如下：
![](zzk_4.jpg)

* **客户端得到一个Service：**

  主要流程如下：通过Java层的ServerManager得到相应的Service，然后通过asInterface()将得到的对象转为客户端可直接调用的代理对象，然后调用代理对象的updateAdnRecordsEfBySearch()方法。

        具体分析如下：
        首先，通过ServerManager得到相应的BpBinder对象。
        源码位于ServerManager.java中
        public static IBinder getService(String name) {
                try {
                    IBinder service = sCache.get(name);
                    if (service != null) {
                        return service;
                    } else {
                        return getIServiceManager().getService(name);
                    }
                } catch (RemoteException e) {
                    Log.e(TAG, "error in getService", e);
                }
                return null;
            }
        可见，调用getIServiceManager()对象的getService()方法，代码如下。
        private static IServiceManager getIServiceManager() {
                if (sServiceManager != null) {
                    return sServiceManager;
                }
        
                // Find the service manager
                sServiceManager = ServiceManagerNative.asInterface(BinderInternal.getContextObject());
                return sServiceManager;
            }
        
        
  可知通过IServiceManager得到的是一个ServiceManager在Java层的代理对象，下边来分析此代理对象的getService(     )方法。

        /frameworks/base/core/java/android/os/ServiceManagerNative.java
        public IBinder getService(String name) throws RemoteException {
                Parcel data = Parcel.obtain();
                Parcel reply = Parcel.obtain();
                data.writeInterfaceToken(IServiceManager.descriptor);
                data.writeString(name);
                mRemote.transact(GET_SERVICE_TRANSACTION, data, reply, 0);
                IBinder binder = reply.readStrongBinder();
                reply.recycle();
                data.recycle();
                return binder;
            }
        
  可见，getService请求被转交给native层，由上一章分析可知，native层得到请求后会将目标Service的BpBinder返回给客户端，得到BpBinder对象后，通过asInterface()得到一个Proxy对象，客户端便通过这个代理类调用服务端定义的各种方法。具体客户端得到Service的流程图如下：
![](zzk_5.jpg)



   

**三、总结**

**Binder通信整体流程图如下：**


![](zzk_6.jpg)



