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


## bpftrace permission denied issue
https://github.com/iovisor/bpftrace/issues/293

try running snap connect bpftrace:system-trace to enable bpftrace to access system tracing 
(see http://smackerelofopinion.blogspot.com/2018/11/high-level-tracing-with-bpftrace.html)

## reference

One-Liner Tutorial
https://github.com/iovisor/bpftrace/blob/master/docs/tutorial_one_liners.md
