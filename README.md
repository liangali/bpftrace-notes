# bpftrace-notes

## installation

```bash
 sudo snap install bpftrace  
 sudo snap connect bpftrace:system-trace  
```

or

```bash
 sudo apt install bpftrace  
```

## build bpftrace from source code

```bash
sudo apt-get update
sudo apt-get install -y bison cmake flex g++ git libelf-dev zlib1g-dev libfl-dev systemtap-sdt-dev binutils-dev
sudo apt-get install -y llvm-7-dev llvm-7-runtime libclang-7-dev clang-7
sudo apt-get install libgtest libgtest-dev libgmock-dev

git clone https://github.com/iovisor/bpftrace
mkdir bpftrace/build; cd bpftrace/build;

cmake -DCMAKE_BUILD_TYPE=Release ..
make -j8
sudo make install
```

## examples
### Lesson 1. Listing Probes

```bash
sudo bpftrace -l 'tracepoint:syscalls:sys_enter_*'
```

### Lesson 2. Hello World
```bash
sudo bpftrace -e 'BEGIN { printf("hello world\n"); }'
```

### Lesson 3. File Opens
```bash
sudo bpftrace -e 'tracepoint:syscalls:sys_enter_openat { printf("%s %s\n", comm, str(args->filename)); }'

# list function parameters
sudo bpftrace -vl tracepoint:i915:i915_request_queue
tracepoint:i915:i915_request_queue
    u32 dev;
    u64 ctx;
    u16 class;
    u16 instance;
    u32 seqno;
    u32 flags;

sudo bpftrace -e 'tracepoint:i915:i915_request_queue { printf("[%s-%d], %d, %llx\n", comm, pid, args->dev, args->ctx); }'
```

### Lesson 4. Syscall Counts By Process
```bash
sudo bpftrace -e 'tracepoint:raw_syscalls:sys_enter { @[comm] = count(); }'

sudo bpftrace -e 'tracepoint:i915:i915_request_queue { @[comm] = count(); }'
# @[ffmpeg]: 100
# @[code]: 342
# @[gnome-shell]: 369
# @[Xorg]: 407

```

### Lesson 5. Distribution of read() Bytes

```bash
sudo bpftrace -e 'tracepoint:syscalls:sys_exit_read /pid >= 5000/ { @bytes = hist(args->ret); }'
sudo bpftrace -e 'tracepoint:syscalls:sys_exit_read /pid >= 5845/ { @bytes = hist(args->ret); }'
```

### Lesson 6. Kernel Dynamic Tracing of read() Bytes

```bash
sudo bpftrace -e 'kretprobe:vfs_read { @bytes = lhist(retval, 0, 2000, 200); }'
sudo bpftrace -e 'kprobe:i915_gem_create_ioctl { printf("[%s-%d]\n", comm, pid); }'
```

### Lesson 12. Kernel Struct Tracing
```bash
sudo bpftrace path.bt
```
### 

```bash
sudo bpftrace -e 'kprobe:vfs_read { @start[tid] = nsecs; } kretprobe:vfs_read /@start[tid]/ { @ns[comm] = hist(nsecs - @start[tid]); delete(@start[tid]); }'

sudo bpftrace -e 'kprobe:i915_gem_create_ioctl { printf("[%s-%d]\n", comm, pid); }'

```
## bpftrace permission denied issue
https://github.com/iovisor/bpftrace/issues/293

try running snap connect bpftrace:system-trace to enable bpftrace to access system tracing 
(see http://smackerelofopinion.blogspot.com/2018/11/high-level-tracing-with-bpftrace.html)

## reference

One-Liner Tutorial
https://github.com/iovisor/bpftrace/blob/master/docs/tutorial_one_liners.md
