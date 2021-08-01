# Computer Hardware & Architecture Primer

### Generic Disclaimers
- This is a __*very raw draft*__ with lots of jumping around. Eventually I'll come back to this later to clean up and expand the sections below. Feel free to contribute with a PR in the meanwhile.
- The items below are an overly simplified look at a few items of a fast moving field. 


### A few topics we'll try and tackle
- Motivation of why you might care
- A very quick stroll through various parts of the compute stack
- Qualitative look at main components (cpu, gpu, memory, fpgas, etc)
- Start thinking between layers of the stack, device level <-> high level languages 

***

# Motivation

![Alt text](./CA/xkcd.png?raw=true "XKCD")


<h6><center>Mostly factual representation of how the average cloud user views computer hardware</center></h6>
<center>Source: https://xkcd.com/353/</center>

### Thanks to abstraction we can do amazing things with almost zero hardware knowledge... So, why should you care?


###### Performance 
- ML & data demands are growing while computer speed ups are slowing. However, with some basic understanding around computer hardware & architecture we can still make significant performance increases. 

###### Integrity
- Being able to understand what is a software vs hardware issue/vulnerability. Don't spend months debugging code that's really a hardware issue. 

###### Special Applications
- Niche applications can have stringent requirements like extremely low latency. A self-driving car can't wait seconds to respond to pedestrians in a crosswalk.

###### Forward Thinking
- Keeping an eye on current & future tech for possible integration to existing or future projects. 

***

# Computer Abstraction

- Abstraction is what allows us to view each layer independently by defining (mostly) clear interaction between layers. 
- While definitely not perfect, this enables a lot of coding portability between different machines and significantly reduces complexity in high level programming languages.
- We'll take a closer look at the lower parts of the stack leading up to programming languages.

![Alt text](./CA/stack.png?raw=true "Abstraction")

<h6><center> Abstraction Stack</center></h6>


## Foundation layer

#### A few topics (among many others) that enable the current computing paradigm... 
- Boolean algebra: The ability to reduce complex ideas and problems into mathematical operations on True and False values (Or 1's and 0's reflecting voltage high and low).
- Semiconductors: The technology currently underpinning current computing devices. Most notably is the transistor, which can be thought of as an electrical on/off switch.
- Digital Logic Design: Using transistors and other electrical components to implement boolean logic gates (and, or, not, nand, etc...). These are the building blocks used to make the sequential (think store data and preserve state) and combinational logic (perform math operations) necessary for a modern computer.


#### Analogy
- Definitely the worst analogy here, but...
- Think of a light bulb. It has two states, on and off. Yet with this single bit of information we can represent a wide variety of things. The fasten seatbelt light on the airplane, an "on the air" indicator at a radio station, a restaurant "open" sign, and many others. There's an incredible amount of ideas a single point of true or false information can be applied to. 
- A computer isn't terribly different, except it's much more complex. At it's heart a computer is also state machine, but with many more states and a lot more switches. Modern CPUs can contain *__billions__* of transistors. 

***

## Device level


#### A quick note on Latency vs Bandwidth
- Latency is how much time it takes to send information from one point to the next. For example, how long it might take for a CPU to start receiving requested data from main memory. 
- Latency is limited by the time it takes a signal to physically travel over a medium (copper, fiber, air, etc..) plus any computing overhead (like any transistors switching) involved with the process. 
- Bandwidth is how much data can be sent in a given amount of time. The faster the frequency on a bus (the copper lane(s) physically connecting computer devices) the higher the potential data transfer rate. 
- We're going to try and keep things as qualitative as possible below.

#### Analogy: 
- Think of a sports car vs a full bus...
- The car is faster to get to it's destination (lower latency)
- But the bus can carry a lot more people (higher bandwidth)


![Alt text](./CA/latency.png?raw=true "Latency Table")

<h6><center>Slightly dated, but still rather interesting to see how fast latency increases as you move away from the CPU. Why it's important to keep data as close to the processing as possible.</center></h6>
<center>Source: ??</center>

## CPU (Central Processing Unit)

![Alt text](./CA/cpu.png?raw=true "Intel i7 CPU")

<h6><center>Intel i7 CPU Rendering</center></h6>
<center>Source: Intel Press</center>

- CPUs serve as the brain of a computer. They are highly connected to the rest of the system, including things such as input and output (keyboard, mouse, monitors). 
- A modern CPU is design to be a highly versatile tool able to handle a wide range of operations with low latency. It mostly executes these tasks in a sequential manner.  

###### To better understand CPUs imagine we loaded an infinitely long stack of instructions and data inputs into the (infinitely large) main memory of a very simple single core cpu. It might look something like this if we were to watch: 

    While instructions:
        Fetch next instruction and inputs from main memory (external from CPU)
        Decode instruction provided
        Run specified operation on given inputs 
        Store result to memory or output


- This isn't terribly far off from what a modern CPU does. In fact modern CPUs are more or less an evolution from older computers that accepted punch cards in and gave punch cards out. 
- Modern CPUs have just received a significant number of improvements over the years to make things run significantly faster.

![Alt text](./CA/cpucontrol.png?raw=true "Block Diagram")

<h6><center>Simplified CPU block diagram</center></h6>
<center>Source: https://en.wikipedia.org/wiki/Central_processing_unit#/media/File:ABasicComputer.gif</center>

#### A few things a Modern CPU does to speed things up
- Pipelining: Think of it as an assembly line for instructions to fully utilize all parts of the CPU.
- Out-Of-Order Execution: Ability to service unrelated items next in the pipeline. Important for instructions that take a long time (Like loading something from disk).
- Multiple Cores: Multiple cores to distribute high level applications across.
- Multithreading: Similar to multitasking... you might be working on two items, but really it's just switching between the two.
- Vectorization: Applying a task across multiple cores to parallelize computing.
- Superscaling: Not terribly unlike when a manager overloads you with extra work even though you're already busy.
- Speculative Execution: Guessing what might come next in a program and caching that result in the CPU cache incase it's called later. This saves time going to main memory. Loops are a good example of when the next step is easy to predict.

<div class="alert-danger">
Security Side Note: Speculative execution is what enables hardware attacks such as Spectre and Meltdown. Not going to go into full details, but more or less a malicious actor uses speculative execution to trick a CPU into caching private data from memory that the actor can then extract through various means. 
</div>

#### A few issues
- CPUs run faster than main memory can provide data, leading to noticeable delays.
- When multiple cores share a memory controller it's easy to bottleneck the interface between memory and the CPU. Think of cars trying to get in and out of downtown during rush hour.

![Alt text](./CA/cpublock.png?raw=true "Block Diagram")

<h6><center>Caching on a four core CPU</center></h6>
<center>Source: Nvidia Press</center>

#### Mitigation
- To mitigate this to some extent (but it's definitely still a big problem) CPUs have a great deal of caching to core ratio on the silicon die to attempt to alleviate some of this demand.
- Caching here is done in hardware logic without you knowing at the software level. 

## Memory & Storage

![Alt text](./CA/ddr4.png?raw=true "DDR4")

<h6><center>128 GB DDR4 Memory Module</center></h6>
<center>Source: SK Hynix</center>

### There are several tiers of storage in modern computing systems
###### CPU Registers <-> CPU Caches <-> Main Memory <-> SSD / Disk / Tape (yes, tape is still used)
- Generally as we move left to right the following happens...
- longer latency, smaller bandwidth, larger storage size, lower cost.
- For now we're just going to focus on CPU main memory.

#### Main Memory or RAM (random access memory)

- Generally referred to as the "Memory" of the CPU, used as a staging ground for CPU operations.
- Where data, instructions, programming functions & variables, OS information, and more are stored during computer runtime.
- High endurance (doesn't wear out easily from lots of reads and writes, unlike SSDs which can lose the ability to hold charge after too many writes due to their physical design).
- Main memory design in recent years has focused more on increasing storage capacity and bandwidth, not lowering latency.

![Alt text](./CA/memoryblock.png?raw=true "Memory Diagram")

<h6><center>Don't worry about the fine details above, just realize memory is similar to an array of 1's and 0's representing our data and programs</center></h6>
<center>Source: semiengineering.com</center>

- Main Memory uses capacitors which require constant "refreshes" to maintain charge even when data is at rest. If power is turned off we lose all data in memory (why memory is referred to as volatile). 
- This makes memory power hungry and also raises latency due to the need to regularly refresh these cells to ensure data integrity.

<div class="alert-danger">
Security Side Note: As the size of these units has shrunk they have become more and more susceptible to bit flips from electromagnetic coupling between memory cells. This is exaggerated if a specific row (or several rows) is being accessed rapidly. While the resulting data corruption is scary (And confusing if it's being viewed as a software issue), it's even more chilling that it's possible to use this method in order to execute a privilege escalation attack. Look up Rowhammer for more information. </div>

#### Another side note...
- There's such a thing as non-volatile (doesn't lose charge) memory that has a different physical construction, but it's not nearly as performant and definitely not as widespread as DRAM. The main benefit is increased storage capacity and being non-volatile. Latency however is much higher (but still generally faster than going to SSD or Disk). 

## GPUs (Graphics Processing Unit)

![Alt text](./CA/gpu.png?raw=true "GPU")

<h6><center> Nvidia GPU Card</center></h6>
<center>Source: Nvidia Press </center>

- GPUs were originally used to offload heavy graphics loads from the CPU.
- Built to handle the generation of pixels from the underlying 3d shapes. 

###### GPUs were built with a very specific task in mind, leading to the following characteristics
- Optimized for doing a few operations very well (like matrix multiplication, as lots of vectors are involved in pixel calculations).
- Highly parallel nature since pixels are independent and need to be displayed all at once.
- GPU Memory optimized for throughput over latency, but still acceptable latency.
- Just to be extra clear here... GPUs have their own memory similar to main memory technology. If you look at the above card there are several memory blocks directly surrounding the GPU chip in the middle.


#### What this ends up looking like at a basic level
- A lot of smaller cores optimized for doing a specific set of operations.
- Highly parallel nature where one control unit runs data on several cores.
- Much less caching space compared to CPUs.

![Alt text](./CA/gpublock.png?raw=true "GPU Block Diagram")

<h6><center> Simple GPU Diagram... Note build vs CPU diagram further above</center></h6>
<center>Source: Nvidia Press </center>

###### GPGPU (General Purpose GPU)
- GPGPU is what came about after people realized they could use GPUs for additional tasks that benefited from GPU's parallel nature vs more sequential CPUs.
- Neural networks in particular are frequently trained on GPUs due to the substantial speed up since we can parallelize computing each node in a single layer as they are independent.

![Alt text](./CA/neuralnet.png?raw=true "GPU Block Diagram")

<h6><center> Neural Network</center></h6>
<center>Source: ?? </center>

### FPGAs (Field Programmable Gate Arrays)

![Alt text](./CA/fpga.png?raw=true "FPGA")

<h6><center> Intel PCIe FPGA </center></h6>
<center>Source: Intel Press</center>

- Unlike CPUs and GPUs which have fixed hardware logic implementations, the FPGA is a device with lots of reprogrammable logic blocks used to create custom hardware logic. 

#### What does that mean exactly?
- We can program an FPGA to execute whatever logic (or device) we like. If you wanted to make a CPU, you could. In fact many companies will prototype their designs in FPGAs. 
- Getting closer to data & ML, FPGAs can be used to implement a machine learning model directly in hardware logic for super low latency inference. 
- By implementing a model directly in hardware logic on an FPGA we can cut out a lot of overhead inherent in CPUs and GPUs due to their more general purpose nature. 

![Alt text](./CA/fpgablock.png?raw=true "FPGA")

<h6><center> FPGA Block Diagram </center></h6>
<center>Source: NI Press</center>

#### Benefits
- Reprogrammable logic. 
- Make almost anything you can think of directly in hardware logic. 
- Truly parallel. We can use part of the system to do one task and another to do something totally different. These tasks can be completely isolated and run fully parallel unaware of the other. 
- Low latency. Implement solution in pure hardware logic (bit in bits out... no need for software overhead). Depending on implementation it's also possible to avoid going to external memory like CPUs and GPUs do for storing intermediate results.  
- Generally more energy efficient compared to GPUs and CPUs.
- Long life cycles. Can physically last years, and can be reprogrammed to adapt to design changes.

#### Drawbacks:
- Complexity: Traditionally to program an FPGA you needed to know hardware languages like VHDL or Verilog. There are some newer tools that use higher level languages, but it's not clear how well some of those optimize the underlying hardware. 
- Portability: Doesn't seem very portable between cloud providers at the moment. Hopefully this is simplified in the future (and there might already be good work being done on this already), but moving an FPGA implementation from one cloud provider to another sounds less than pleasant. 

#### Examples
- Microsoft has been doing a lot of work around FPGAs in their cloud...
- Bing uses FPGAs to hardware accelerate software defined networking.
- Azure touts FPGA integration in their cloud for machine learning.

## ASICs (Application Specific Integrated Circuit)

![Alt text](./CA/tpu.png?raw=true "TPU")

<h6><center>Google TPU ML ASIC</center></h6>
<center>Source: Google Press</center>

- More or less exactly what it sounds like, an ASIC is a custom chip for a specific usage.
- Think of it similar an to FPGA, but all the connections are etched permanently and any excess logic and size is removed. 

###### Benefits over FPGA approach:
- Better performance.
- Lower power consumption.
- Lower unit cost when able to do large enough volume.

###### Drawbacks:
- Development time can be long and costly to do correctly.
- Not reconfigurable, can be a slow turnaround time for updates.

## Summary of devices

![Alt text](./CA/microsoftdocs.png?raw=true "Overview")

<h6><center>Silicon Options for ML</center></h6>
<center>Source: Microsoft Press</center>

### PCBs

![Alt text](./CA/brd.png?raw=true "Title")

<h6><center>PCB Routing Rendering </center></h6>
<center>Source: Cadence Allegro?</center>

- Printed circuit boards are the nervous system of a computer, as it electrically connects all devices in a computer. 
- PCBs are generally several layers thick to accommodate all necessary electrical connections and mechanical requirements. 
- How these devices are connected directly impacts our ability to use the computer. For instance, several modern architectures generally have main memory attached directly to CPUs and nothing else. To access memory, signals must travel over the copper connecting these two devices.

###### A few terms
- Bus: A bus is a logical grouping of similar signals. Like a PCIe bus for example. 
- Bus width: How many copper connections are present in the bus. PCIe x16 for example means there are 16 copper lanes that data can be transmitted over in parallel. 

###### A note on board quality
- How well a board is designed can have a significant impact on performance. 
- Much like how a poor quality road impacts efficient traffic flow, a poor quality PCB can limit high speed data transfer. 
- Your CPU and GPU may be PCIe gen 4 capable, but they will not run at that speed if the board can't support it.

***

## Microarchitecture & ISA

#### Microarchitecture:
- How something (say a cpu for instance) implements the hardware logic to do it's computing.
- Intel CPUs uarch codenames you might have heard of... (Ivybridge, Haswell, Skylake, etc..)

#### Instruction Set Architecture (ISA): 
- A standardized collection of commands the hardware knows how to run.
- x86 and ARM being two common ISAs you've probably heard of.

###### Analogy:
- Let's think of a bicycle...
- In this context the microarchitecture might refer to the bicycle drivetrain. Does it use a chain? A belt? Is it electric? 
- The ISA instruction here might be something as simple as pedaling. 
- So if we instruct the bicycle to pedal it should no matter what drivetrain, but one might be more power efficient and allow us to go faster.

#### Ok great, but how do I use this?
- When code is compiled/interpreted it converts something easy for a human to read into the instructions easy for the cpu to understand.  
- Machine code is the 1's and 0's representing the various instructions from the ISA and other information... like the operation happening, what registers (temporary storage in the cpu for operations) to use, memory addresses, etc...
- Assembly code is more or less the human readable version of machine code.
- Obviously there's generally not a 1:1 mapping for high level languages to assembly/machine code.

###### For example... Something as simple as 'a = a*2' in a high level language would be several lines of machine code that might do something along these lines after the instructions are fetched and decoded...
- load value of "a" from main memory into a cpu register
- shift bits of same register to left (multiply by 2)
- store resulting value back to memory, possibly caching locally as well 

###### Interpreted languages like python are a touch different as they use an intermediate step since they are dynamic. You can actually see the intermediate code using the dis module... 

    import dis
    
    def show_byte_code(value, mult=2):
        result = value * mult
        return result
    
    dis.dis(show_byte_code)

    0 LOAD_FAST                0 (value)
    2 LOAD_FAST                1 (mult)
    4 BINARY_MULTIPLY
    6 STORE_FAST               2 (result)
    
    8 LOAD_FAST                2 (result)
    10 RETURN_VALUE

### Suffice to say that ISAs + compilers make the life of a programmer significantly easier. Imagine having to write code compatible for every different CPU...

*** 

## Operating System (very simplified) 
- Interface between user and hardware.
- CLI/guis for interacting with system.
- Management of resources and security (kernel).
- System libraries for programming. 

#### The Kernel 
- The kernel of an OS handles a lot of the system resources available to users only through system calls.
- Think of it as an API to access the underlying hardware resources that is called when we run  terminal commands, applications, or execute various programs.
- The kernel handles management of processes, memory, and devices & drivers among several other tasks.

![Alt text](./CA/kernel.png?raw=true "Kernel")

<h6><center> Simple View of Kernel </center></h6>
<center>Source: ??</center>

###### Where does all of this "Live" though?
- Remember fundamentally we're just running through of a stack of instructions on a CPU. The OS is just helping us achieve this through running its own control instructions. These instructions, libraries, and other information are generally things stored in a portion of main memory and disk.
- This is obviously a very basic view and we're totally skipping over a great deal of how a system boots and gets to this state. Things like the role of ROM, firmware, UEFI/BIOS, etc... Feel free to look this info up on your own.

*** 

### CPUs + GPUs for Data

#### A quick view of CPU + GPU interaction 
- For this example let's assume all our data can fit in memory. Here's a basic flow of how data moves...
- Data from disk is loaded into to CPU memory.
- Data is copied from CPU memory to GPU memory over the PCIe bus.
- CPU tells GPU to run instructions over the data currently in GPU memory.
- Data is loaded and computed on the GPU, results are stored back to GPU memory.
- Final results after all necessary computations on GPU are copied from GPU memory back to CPU memory. 

![Alt text](./CA/cpugpu.png?raw=true "Title")

<h6><center>CPU + GPU Interaction </center></h6>
<center>Source: Nvidia Press</center>

#### Shortening data paths
- That's a lot of a data movement. As these tools grow, we'll always want to reduce the data path traveled. 
- Some tools like nvidia's GPU direct enables a user with a PCIe switch in front of their gpus (and assuming it's connected to something) to bypass sending data to the CPU and load it directly into the GPU from storage. This saves cpu cycles and memory copy time.  

#### Reducing data overheads
- Equally important is how can we limit the amount of data being transferred between and stored at the devices.
- Depending on circumstances... can we reduce our data to float 16? Or even int 8 (if available on the GPU in question) without hurting end model performance? 
- By default float 32 is used most places, which just means we have 32x 1's and 0's to represent a number's sign, exponent, and significand. By converting to float 16 we reduce memory sizes by half. 
- Depending on the GPU there can be significant performance increases as well during training when using float 16 or int 8.
- Other topics like sparse matrices and pruning can be explored as well to limit data and compute overheads. 

### Overall, try to visualize/balance how data interacts with the physical system and how to reduce unnecessary movement, data volume, and compute overhead in tackling a given problem

***

# How ML is changing hardware

### Where things are going
- Lots of startups trying to make new and exciting chips for machine learning.
- Expect to see more improvements around ML demands at all levels of the stack from device to software. (Already seeing a lot of this in current hardware... for example "neural cores" showing up in new CPUs for ML acceleration).
- Hopefully a lot more portability between solutions to simplify programmer's time.
- Hybrid CPU & GPUs like AMD's APU.

 ### A couple last fun items...

#### Tesla
- Self driving car hardware with custom chips for fast inference & builtin redundancy.

![Alt text](./CA/fsd.png?raw=true "Image Source Tesla")


<h6><center>Tesla FSD </center></h6>
<center>Source: Tesla Press</center>

#### Nvidia DGX
- AI/ML based computing system with large focus on GPUs, but still has 2x powerful cpus!
- Note the NVlinks present to maximize potential data movement between gpu memory.

![Alt text](./CA/dgx.png?raw=true "Title")


<h6><center> Nvidia DGX </center></h6>
<center>Source: Nvidia Press</center>