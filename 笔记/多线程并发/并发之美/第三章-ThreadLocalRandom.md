### 1 Random类及其局限性

Random类是java提供的一个随机数生成的工具类。它本身是线程安全的。一个随机数的生成是需要一个默认的种子，这个种子默认是long类型的数据，可以通过构造函数来指定。

```java
//用于存储种子的变量
private final AtomicLong seed;

//默认的构造函数
public Random() {
   		//随机的生成一个种子
        this(seedUniquifier() ^ System.nanoTime());
    }
	
    private static long seedUniquifier() {
        for (;;) {
            long current = seedUniquifier.get();
            long next = current * 181783497276652981L;
            if (seedUniquifier.compareAndSet(current, next))
                return next;
        }
          private static final AtomicLong seedUniquifier
        = new AtomicLong(8682522807148012L);
    }
    //指定种子的构造函数
      public Random(long seed) {
        if (getClass() == Random.class)
            this.seed = new AtomicLong(initialScramble(seed));
        else {
            // subclass might have overriden setSeed
            this.seed = new AtomicLong();
            setSeed(seed);
        }
    }
```



#### 1.1Random生成随机数的过程

- 根据老的种子生成新的种子。
- 根据新的种子计算新的随机数。

源码解析

```java
//生成一个在0-bound的随机整数
public int nextInt(int bound) {
    //（1）参数检查
        if (bound <= 0)
            throw new IllegalArgumentException(BadBound);
		//（2）根据老的种子生成新的种子
        int r = next(31);
        int m = bound - 1;
    //（3）根据新的种子生成随机数
        if ((bound & m) == 0)  // i.e., bound is a power of 2
            r = (int)((bound * (long)r) >> 31);
        else {
            for (int u = r;
                 u - (r = u % bound) + m < 0;
                 u = next(31))
                ;
        }
        return r;
    }
//保证在多线程下产生的随机数是随机的
protected int next(int bits) {
        long oldseed, nextseed;
    
        AtomicLong seed = this.seed;
        do {
            //获取到当前原子变量种子
            oldseed = seed.get();
            //根据当前种子的生成新的种子
            nextseed = (oldseed * multiplier + addend) & mask;
            /**如果在多线程的情况下，可能有多个线程执行到这里，如果不同步，会造成线程拿到的种子是形同的，生成相同的随机数，使用CAS操作，保证在多线程的情况下，只有一个线程去将旧种子更新为新种子，保证随机数的随机性。*/
        } while (!seed.compareAndSet(oldseed, nextseed));
        return (int)(nextseed >>> (48 - bits));
    }
```

#### 1.2 Random的局限性

在多线程的情况下，生成随机数的过程中，同时只有一个线程去修改，造成大量线程自旋，降低性能。

### 2 ThreadLocalRandom

#### 2.1  概述

TreadLocalReandom通过在线程内部维护种子变量，来解决Random类在多线程的情况下，竞争一个变量，而造成的性能问题。

#### 2.2 源码解析

ThreadLocalRandom继承了Random类并重写了nextInt(),ThreadLocalRandom中并没有存放seed变量，具体的种子变量存放在调用线程的ThreadLocalRandomSeed变量里面，当调用ThreadLocalRandom的crrent()时候，就是调用线程ThreadLocalRandomSeed的变量，也就是初始化种子。

当调用ThreadLocalRedom中的nextInt()方法，实际是获取到当前线程的种子，然后利用这个种子计算得到新的种子，将新的种子更新到ThreadLocalRedom中，最后根据新的种子生成新的随机值。

instance是static final的TreadLocalRanom实例。当多个线程通过ThreadLocalRandom的crrent(）获取ThreadLocalRandom时，获取到的一个实例。

```java
static final ThreadLocalRandom instance = new ThreadLocalRandom();
```

##### Unsafe

```java

// Unsafe是Java提供硬件级别的原子操作
    private static final sun.misc.Unsafe UNSAFE;
    private static final long SEED;
    private static final long PROBE;
    private static final long SECONDARY;
    static {
        try {
            UNSAFE = sun.misc.Unsafe.getUnsafe();
            Class<?> tk = Thread.class;
            //获取Thread类里面threadLocalRandomSeed变量在Thread的偏移量
            SEED = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("threadLocalRandomSeed"));
             //获取Thread类里面threadLocalRandomProbe变量在Thread的偏移量
            PROBE = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("threadLocalRandomProbe"));
            /获取Thread类里面threadLocalRandomSecondarySeed变量在Thread的偏移量
            SECONDARY = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("threadLocalRandomSecondarySeed"));
        } catch (Exception e) {
            throw new Error(e);
        }
    }
```

##### current()

```java
 static final ThreadLocalRandom instance = new ThreadLocalRandom();
  public static ThreadLocalRandom current() {
      //如果当前线程threadLocalRandomProbe是0(默认是0)，说明该线程第一次调用，需要初始化该线程的种子
        if (UNSAFE.getInt(Thread.currentThread(), PROBE) == 0)
            //初始化种子方法
            localInit();
      //返回ThreadLocalRandom实例
        return instance;
    }
//初始化种子的方法
    static final void localInit() {
        //根据probeGenerator设置threadLocalRandomProbe的值
        int p = probeGenerator.addAndGet(PROBE_INCREMENT);
        int probe = (p == 0) ? 1 : p; // skip 0
        //生成该线程的种子
        long seed = mix64(seeder.getAndAdd(SEEDER_INCREMENT));
        Thread t = Thread.currentThread();
        //将上面计算的值设置到当前线程里
        UNSAFE.putLong(t, SEED, seed);
        UNSAFE.putInt(t, PROBE, probe);
    }
```

##### nextInt(int bound）

```java
//生成随机数的方法
public int nextInt(int bound) {
        if (bound <= 0)
            throw new IllegalArgumentException(BadBound);
    //根据当前线程的种子生成新种子
        int r = mix32(nextSeed());
    //根据新种子生成随机数
        int m = bound - 1;
        if ((bound & m) == 0) // power of two
            r &= m;
        else { // reject over-represented candidates
            for (int u = r >>> 1;
                 u + m - (r = u % bound) < 0;
                 u = mix32(nextSeed()) >>> 1)
                ;
        }
        return r;
    }
    final long nextSeed() {
        Thread t; long r; // read and update per-thread seed
        // UNSAFE.getLong(t, SEED)获取当前线程中种子的值，然后将其加GAMMA作为新种子，然后写回threadLocalRandomSeed中
        UNSAFE.putLong(t = Thread.currentThread(), SEED,
                       r = UNSAFE.getLong(t, SEED) + GAMMA);
        return r;
    }
```

### 3 总结

Random类在多线程的情况下会造成自旋竞争，threadLocalRandom类使每个线程都持有本地的种子变量，避免竞争。