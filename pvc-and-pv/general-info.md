# General information
Just some general information regarding PV's and PVC's.

## How they work together:
PV's and PVC's have a *one-to-one* relationship. This means that only a single PVC can claim a PV. While this is so, one can still have multiple pods consuming the same **PVC**.

How this will work depends on the storage backend and its access mode. Take the following table below:

| PV   | PVC   | Access Mode | Description                                                               |
|------|-------|-------------|---------------------------------------------------------------------------|
| PV-1 | PVC-1 | RWO         | ReadWriteOnce -- the volume can be mounted as read-write by a single node |
| PV-2 | PVC-2 | ROX         | ReadOnlyMany -- the volume can be mounted read-only by many nodes         |
| PV-3 | PVC-3 | RWM         | ReadWriteMany -- the volume can be mounted as read-write by many nodes    |
