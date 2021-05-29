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

```bash
sudo bpftrace -l 'tracepoint:syscalls:sys_enter_*'

sudo bpftrace -e 'BEGIN { printf("hello world\n"); }'

sudo bpftrace -e 'tracepoint:syscalls:sys_enter_openat { printf("%s %s\n", comm, str(args->filename)); }'
```

#### bpftrace permission denied issue
https://github.com/iovisor/bpftrace/issues/293

try running snap connect bpftrace:system-trace to enable bpftrace to access system tracing 
(see http://smackerelofopinion.blogspot.com/2018/11/high-level-tracing-with-bpftrace.html)

## reference

One-Liner Tutorial
https://github.com/iovisor/bpftrace/blob/master/docs/tutorial_one_liners.md
