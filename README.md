# CyberFS

## Intro
CyberFS is an open source Linux file system written in Rust. The goal of it is to incorporate Cybersecurity principals and mitigations directly in the file system.  
  
I got the idea for this when thinking about ransomware attacks and how we currently try to prevent and recover from them using 3rd-party solutions. In the case of ransomware, it's an attack directly to the data that the file system manages. I asked myself, "Why don't we have any solutions that detect, stop, quarantine, and recover from these attacks directly on the file system level?". As a software developer who has multiple degrees and Cybersecurity certifications, I feel that I'm in a unique position to try and create the first Cybersecurity aware file system to prevent these kinds of attacks.  
  
## Design Principal
I've gotten to work and started designing CyberFS by researching and understanding what other file systems are doing right. ZFS has a really good model of what a file system should look like, and so I have started the design based on features I like in ZFS.  

### Design Highlights

- **Copy on Write (CoW)**: Allows file versioning on each write.
    - **Cybersecurity Context**: Using the versioning feature of CoW, CyberFS will be able to rollback files that were affected during a ransomware attack to the pre-attack state.
- **Immutable Snapshots**: Allows for a redundancy if an attack gets through the detection mechanism built into CyberFS. Like ZFS, these are immutable and cannot be tampered with.  
- **A/B System**: As a 3rd layer of redundancy, there will be an A/B-like system that will keep track of all file states and have the ability to reboot into the last known good state.  
- **Automatic Ransomware Detection**: CyberFS has ransomware detection built-in. The detection engine will be able to detect both file-level and system-level ransomware attacks.  
- **Self-healing**: Detecting an attack is just the first step. Using the CoW nature of the file system, CyberFS will be able to roll affected files back to the state they were before the attack. For system-level attacks, such as drive encryption, the detection engine will first analyze possible intentions, the process making the action request, and request the encryption key from the process to be placed outside of the system that the action is taking place. This will give the user/administrator the power to reverse the action if needed.  
  
### Main Design Goals
- **Familiar**: CyberFS will not change the way that end-users will interact with the file system. It will be transparent in that the user can still interact with their files just like they always have. User space will always feel like home.  
- **Safely Performant**: CyberFS is being developed in Rust for the safety guarantees that go along with Rust's performance.
- **Efficient**: Using Rust's powerful concurrency features, CyberFS has the goal of ***NEVER*** being the bottleneck on any workload.
    - **Multi-threading**: CyberFS is being designed to be multi-threaded and async in the places where bottlenecks could occur, such as the CoW and ransomware detection engine.
    - **Benchmarking and QA**: A separate file system specific benchmarking and QA tool is also being designed to be able to accurately compare CyberFS to other file systems. Ensuring a non-biased approach to development and improvements. This benchmarking and QA suite is being designed to handle all of CyberFS's features - including simulating known ransomware attacks on the file system - benchmarking them, and providing valuable information back so improvements can be made.  
- **Reliability**: File systems are no good if they lose your data. CyberFS has this in mind and is being designed to never have this issue. If I, the developer, wouldn't use it - then no one should. Which means that once CyberFS is stable enough, I will be using it on my daily driver - putting my money where my mouth is.