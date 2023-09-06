#### [1. Operating Systems must support GPU abstractions](../Papers/Operating%20Systems%20must%20support%20GPU%20abstractions.pdf)
**Background**:
- Most systems view GPU as I/O device, which only provides limited system calls, and lack of modularity, and thus difficult to programming and cannot ensure fairness, isolation and performance.
- For GPU scheduling, current GPU framework uses vendor-provided scheduling algorithm (simple), and sometimes violate fairness.

**Motivation**:
- System spends far more time marshaling data structures and migrating data than it does actually computing on the GPU.
- When two unrelated tasks: CPU-bounded and GPU-bounded tasks execute, the load cannot balance the entire system effectively. GPUs are not preemptible, and Windows is also unable to interrupt GPU programs.

**Main Contribution**: 
- A kernel abstraction to manage interactive and high-compute devices like GPUs (and other accelerator devices).

**Proposed OS Abstractions**:
- *PTask* analogous to process
- *Port* 
- *Channel* analogous to pipe
- *Graph*
- Design new system calls support PTask
- For long-latency computation PTasks, the scheduler can serve it when the CPU threads are blocked.
- When a process's CPU threads are waiting for low-latency computation PTasks, it can either busy-wait, or use CPU computation resources to help GPU computation.


#### [2. PTask: Operating System Abstractions To Manage GPUs as Compute Devices](../Papers/PTask_Operating%20System%20Abstractions%20to%20Manage%20GPUs%20as%20Compute%20Devices.pdf)
Note: Discuss in detail compared to [1](../Papers/Operating%20Systems%20must%20support%20GPU%20abstractions.pdf). Background and motivation are the same. A good example for explaining the unnecessary data movement in Figure 6.

**Detailed Proposed OS Abstractions**: 
- *Port*: three different types. 
  - InputPort, OutputPort, and StickyPort
  - Two status: occupied and unoccupied
- *Datablock*: contain a unit of data flow along the data graph
  - Buffer-map: map memory spaces to device specific buffer object. (Divide seperately)
  - My understanding: For example, the datablock is created in the buffer in CPU memory, and when we utilize GPU to accelerate (say in ptask), it will generate a new view (a buffer in GPU memory), and track this most up-to-date view.
- *Template*: provide meta-data describing datablock (dimension, layout, types of resources)
- A thread pool for GPU dispatch and port data movement
- *Channels* have capacity.
- For Output has variable length, first run on GPU to determine the output size and offset. Dynamical allocation is impossible.
- PTask status:
  - Waiting (for inputs)
  - Queued(inputs available, waiting for a GPU)
  - Executing (running on the GPU)
  - Completed (finished execution, waiting to have its outputs consumed)

**PTask Prototype Implementation**:
- Scheduling Policy: 
  - First-available: access is arbitrated by locks on the accelerator (GPU) data structures
  - FIFO
  - Priority


**Limitation** (mentioned in the article):
- Can only support *static graph* [doesn't support cyclic / dynamic / algorithms that are hard to construct data-flow graph ]
- Solution1: unrolling loop
- Solution2: expressed as a single ptask graph.
