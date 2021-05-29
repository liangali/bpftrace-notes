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

#### bpftrace permission denied issue
https://github.com/iovisor/bpftrace/issues/293

try running snap connect bpftrace:system-trace to enable bpftrace to access system tracing 
(see http://smackerelofopinion.blogspot.com/2018/11/high-level-tracing-with-bpftrace.html)

