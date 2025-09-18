# OCI Runtime Error: runc Missing Binary 📝

## Context
Container failed to start with:
```text
OCI runtime create failed: runc create failed: unable to start container process: exec: "/usr/local/s2i/run": stat /usr/local/s2i/run: no such file or directory: unknown
```

## Investigation
- Checked image → based on `ubi9/openjdk-21-runtime:1.23`.  
- Dockerfile had duplicate `FROM` statements (ubi9 + ubi8).  
- Final layer dropped `/usr/local/s2i/run`.  

## Fix
1. Cleaned Dockerfile → single base image.  
2. Rebuilt → binary present, container started.  

## Lesson learned
When building with multiple `FROM`, last one wins.  
Always verify required binaries exist in final image.
