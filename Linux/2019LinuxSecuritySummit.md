# Notes from the 2019 Linux Security Summit

## Monday, August 19

- Most interesting talk of the day:
  - breaking & protecting the Linux Kernel stack - [event](https://lssna19.sched.com/event/RHaN/breaking-and-protecting-linux-kernel-stack-elena-reshetova-intel?iframe=no&w=&sidebar=yes&bg=no)- PDF slides saved to laptop
- Other talks:
  - TrenchBoot - how to nicely boot system with Intel TXT & AMD SVM - [event](https://lssna19.sched.com/event/RHb0/trenchboot-how-to-nicely-boot-system-with-intel-txt-and-amd-svm-daniel-kiper-oracle-daniel-smith-apertus-solutions?iframe=no&w=&sidebar=yes&bg=no) - [PDF slides](https://static.sched.com/hosted_files/lssna19/75/trenchboot_ot_lss_20190815.final.ds.dk.pdf)
  - making Containers safer - [event](https://lssna19.sched.com/event/RHa5/making-containers-safer-stephane-graber-christian-brauner-canonical-ltd?iframe=no&w=&sidebar=yes&bg=no) - [PDF slides](https://static.sched.com/hosted_files/lssna19/ca/LSS%20NA%202019_%20Making%20Containers%20Safer.pdf)
  - Kernel runtime security instrumentation - [event](https://lssna19.sched.com/event/RHai/kernel-runtime-security-instrumentation-kp-singh-google?iframe=no&w=&sidebar=yes&bg=no) - [PDF slides](https://static.sched.com/hosted_files/lssna19/03/Kernel%20Runtime%20Security%20Instrumentation.pdf)
  - securing TPM secrets with TXT & Kernel secrets - [event](https://lssna19.sched.com/event/RHaB/securing-tpm-secrets-with-txt-and-kernel-signatures-paul-moore-cisco?iframe=no&w=&sidebar=yes&bg=no) - [PDF slides](https://static.sched.com/hosted_files/lssna19/17/lss-securing_tpm_with_txt-pmoore-201909-r2.pdf)
  - the why & how of libseccomp - [event](https://lssna19.sched.com/event/RHaK/tutorial-the-why-and-how-of-libseccomp-tom-hromatka-oracle-paul-moore-cisco?iframe=no&w=&sidebar=yes&bg=no) - [PDF slides](https://static.sched.com/hosted_files/lssna19/4c/TheWhyAndHowOfLibseccomp-r4-pm.pdf)

## Tuesday, August 20

## Notes

### Making Containers Safer: Current state and future of unprivileged containers
- by Stéphane Graber & Christian Brauner
- Why do we care about safe containers?
- Different types of containers and terminology
- Privileged vs. unprivileged containers
  - Privileged containers
    - Container uid 0 is identical to host uid 0 --> Container breakouts are extremely serious.
    - Industry standard --> Most workloads use privileged containers.
    - Security depends on LSMs, capability, seccomp coverage --> Privileges are not isolated (enough or at all). Policies tend to fail open.
  - Unprivileged containers
    - Container uid 0 is NOT identical to host uid 0 --> Container breakouts are bad but not as damaging.
    - Slow adoption outside of LXC/LXD --> Layered filesystem containers don’t go well with user namespaces requirements.
    - User namespaces as main security mechanism --> Namespace for isolating core privilege concepts (capabilities, DAC). LSMs used on top of that as an extra safety net.
- Are privileged containers really that unsafe? Yes.
- Making the best use of existing solutions
  - Namespaces
    - Too few namespaces are often used --> Industry has tendency to only use a subset.
    - Use as much of them as possible (especially the user namespace) --> But be careful about the ordering at setup time.
    - Beware of clunky API --> No way to atomically _setns_ to all namespace of process, etc...
  - Seccomp
    - Essential for privileged containers --> “open_by_handle_at” anyone?
    - Useful safety net for unprivileged containers --> Syscall blocking/logging/interception, deal with broken userspace.
  - LSMs
    - Essential for privileged containers --> Blocking access to various parts of procfs and sysfs.
    - Most frequently used full LSMs are SELinux, AppArmor --> Similar profile goals for both.
- Looking ahead: Recent and future improvements
  - Seccomp: notify target
    - Allows running less privileged containers --> Unprivileged containers can be granted very specific privileges.
    - Seccomp asks userspace for return value and errno --> Execution does NOT continue in the kernel, userspace must do the work.
    - Initial support landed in 5.0 --> Userspace requires un-released libseccomp.
  - Seccomp: notify target -> resume syscalls
    - Builds on top of existing notify target --> Effectively a new type of return value from userspace.
    - Allows for complex userspace filtering --> For cases where the kernel cannot filter on some arguments.
    - No raised privileges --> Execution continues in the kernel with original privileges.
  - Seccomp: extended syscall filtering
    - In-kernel filtering of pointer arguments --> Filter syscalls such as clone3(), bpf() etc.
    - Discussion scheduled for KSummit in Lisbon
  - LSM: stacking
    - Run Ubuntu on Fedora (AppArmor) or Android on Ubuntu (SELinux) --> Allows them to retain their individual LSM policies.
    - Goal is to stack major LSMs on top of each other --> AppArmor on SELinux or SELinux on AppArmor.
    - Currently can stack minor LSMs with major LSMs --> TOMOYO, loadpin, etc. with AppArmor or selinux
  - LSM: safeSetID
    - Restrict id transitions through setid-like syscalls --> System policy determines what transitions are allowed.
    - Mostly useful for privileged containers --> Can be used to allow a limited range of uid/gid for the container.
    - Will be in Linux 5.3
  - New mount API
    - Use file descriptors for mounting --> Mounts are created, configured, and setup via file descriptors.
    - Anonymous mounts --> Mounts that are not attached to any path in the filesystem.
    - Avoids numerous race conditions --> Container managers cannot trust the container’s mount table.
    - Potential for clean uid/gid shifting
  - Keyring namespacing
    - Namespace keyring facility --> Allows to have per-container keyrings.
    - Use by network filesystems --> Keyring namespaces will allow per-container authentication.
  - pidfd API
    - File descriptor referring to a process --> Eliminates inherent races in process management.
    - Get with clone() and CLONE_PIDFD --> Request pidfd be returned together with pid.
    - Send signals with pidfd_send_signal() --> Race-free signal sending (no accidental wrong target).
    - Get pidfd for an existing process with pidfd_open() --> Create pidfd for processes created without CLONE_PIDFD.
    - Poll on a pidfd --> Get exit notifcation for non-child processes.
- Website: https://linuxcontainers.org/lxd
- Code: https://github.com/lxc/lxd
- Online demo: https://linuxcontainers.org/lxd/try-it

### Kernel Runtime Security Instrumentation
- [PDF slides](https://static.sched.com/hosted_files/lssna19/03/Kernel%20Runtime%20Security%20Instrumentation.pdf)
- Signals
  - correlation with maliciousness
  - A process that executes and deletes its own executable.
  - A Kernel module that loads and "hides" itself
  - "Suspicious" environment variables
- Mitigation
  - malicious, stop it!
  - Prevent mounting of USB drives on servers.
  - Dynamic whitelist of known Kernel modules.
  - Prevent known vulnerable binaries from running.
- // MISSING NOTES //
- Why LSM?
  - Mapping to security behaviours rather than the API.
  - Easy to miss if instrumenting using syscalls (eg. execve, execveat)
  - Benefit the LSM ecosystem by incorporating feedback from the security community
- Key Design Principles
  - Keep the helpers precise and granular
  - No access to kernel data structures in eBPF, maintain backward compatibility
- Why not Audit?
  - Mitigation needs to be handled separately
  - Performance overhead when enabled (without rules)
  - Rigid formatting constraints
- Why not seccomp + eBPF?
  - LSM maps better to security behaviours.
  - Multiple syscalls can represent a single behaviour. (eg. execve, execveat)
  - TOCTTOU for user-memory based checks as verification happens before the syscall captures memory from user space.
- Why not kprobes + eBPF?
  - `bpf_probe_read{_str}` give direct access to kernel data structures 
  - Dependency on Kernel Data Structures makes deployment hard. 
  - kprobes is not a stable API, with no guarantees on locks, IRQs preemption etc.
- Why not Landlock?
  - Landlock is geared towards creation of security sandboxes for unprivileged processes.
  - KRSI gives granular access to security behaviours with an ecosystem of security focused helpers.
- // SOME GRAPHS FOR PERFORMANCE ANALYSIS //

### Securing TPM Secrets with TXT and Kernel Signatures
- [PDF slides](https://static.sched.com/hosted_files/lssna19/17/lss-securing_tpm_with_txt-pmoore-201909-r2.pdf) • by Paul Moore
- Problem Statement
  - Store secrets in the TPM
  - Restrict access to the secrets to authorized kernels
  - Work on legacy BIOS as well as UEFI based systems
  - Easy to manage, handle updates gracefully
- Protecting TPM Secrets
  - “Seal” data to a set of PCRs
    – A specific set of PCR values are used as a key to lock/unlock TPM secrets
  - TPM protects PCRs from tampering
- Setting TPM PCRs in Early Boot
  - Secure boot measures system state into the PCRs
    – firmware / config
    – bootloader, etc.
    – kernel
  - When the components change, the PCR values change
- Detour: Secure Boot
  - UEFI Secure Boot
    - UEFI verifies signature of everything it executes
      – static root of trust
      – public key embedded in firmware
      – Microsoft controls master keys
    - PCR 7 measures the kernel’s signing authority
      – stable across multiple kernels with the same signer
  - Intel Trusted Execution Technology (TXT)
    - Hardware and firmware creates a dynamic root of trust
      – “SINIT ACM”
    - TXT “measured launch environment” verifies and bootstraps the kernel
      – tboot bootloader
    - PCRs measuring the OS are based on hashes
      – not a stable base for TPM sealing
- Solving The Problem
  - Applying Secure Boot to the Problem
    - UEFI Secure Boot
      – stable PCR
      – only works on modern UEFI systems
    - Intel TXT
      – unstable PCRs
      – works on legacy BIOS and all UEFI systems
  - Lessons Learned
    - Signature based PCRs are stable assuming the same signing authority
    - TXT works regardless of the firmware type (legacy/UEFI)
    - TXT verification of the kernel/initrd/cmdline happens in the tboot bootloader
  - Proposed Solution (TXT/sig)
    - “Extend” tboot to support signature verification using the PECOFF format
      – same format as UEFI Secure Boot
      – add the signing authority to the tboot policy
    - No changes to SINIT ACM required
  - Open Issues
    - No signature verification of the initrd or command line
      – problem for UEFI too
      – may be able to use UEFI workarounds
      – existing digest verification OK
    - Unknown upstream acceptance of the tboot changes
      – prototype is still a work in progress
  - Current Status
    - Modifications to tboot are ongoing
      – tboot verifies the PECOFF digests, but signature is failing
      – expect success soon (LSS-EU or sooner)
    - Licenses and early boot environment challenges

### The why and how of libseccomp
- [PDF slides](https://static.sched.com/hosted_files/lssna19/4c/TheWhyAndHowOfLibseccomp-r4-pm.pdf) • by Paul Moore & Tom Hromatka • [The libseccomp project on GitHub](https://github.com/seccomp/libseccomp)
- The world is a scary place
  - Bugs are both inevitable and a security risk
    – Application bugs can lead to malicious code execution
    – Malicious code can exercise kernel vulnerabilities
    – Kernel vulnerabilities can lead to ... @!$&%#!
  - Focus on containing bugs and limiting the risks
    – System hardening
    – Access controls
    – ... and syscall filtering
- What is syscall filtering?
  - Enables the kernel to intercept syscalls and take action
    – Kill or signal the calling process
    – Notify a separate tracing process
    – Cause the syscall to abort with a specific “errno” value
  - Configured on a per-process or per-thread basis
    – Configured at application run-time
    – Inherited by the process' children
    – Enforced by the kernel
- Syscall filtering attempt #1
  - “seccomp mode 1”
    – First appeared in Linux 2.6.23
    – Fixed list of allowed syscalls:
      - read(2), write(2), exit(2), sigreturn(2)
  - Not very flexible, not a general purpose solution
    – Unable to filter on architecture/ABI
    – Unable to change the syscall whitelist
    – Unable to filter on syscall arguments
- Syscall filtering attempt #2
  - Enhanced syscall filtering, aka “seccomp mode 2”, aka “seccomp-bpf”
    – First appeared in Linux 3.5
    – Currently supported architectures/ABIs
      - x86, x86_64, x32, arm, aarch64, mips, mipsel, mips64, mipsel64, mips64n32, parisc, parisc64, ppc, ppc64, ppc64le, s390, s390x, … ?
  - Syscall filters are now defined by a filtering language
    – Applications create their own syscall filter
    – Filter on arch/ABI and any syscall, including arguments
    – Filtering is done by the kernel with provisions for delegation to userspace
  - Filters are written in BPF (Berkley Packet Filter)
    – Simple low level language, e.g. assembly
      - Multiple filters can be loaded for each process
    – Syscalls are filtered through the entire filter stack
      -  Most restrictive result “wins”
    – Not possible to unload or overwrite a loaded filter
      - Syscall filters are inherited across fork()/exec() boundaries
    – Not possible to escape from a syscall filter once set
- Room for improvement
  - Syscalls are different for each architecture
    - Filters are arch/ABI dependent
  - BPF language is 32-bit
    - 64-bit support is possible, but the BPF code is more complex
  - Good, non-trivial filters can be cumbersome
    - Similar to coding in assembly language
    - Difficult to maintain
  - Learning curve may be too steep for developers
    - Developers want to focus on their application, not syscall filtering
- libseccomp (aka attempt #3)
  - Provides a programmatic API for seccomp filters
    – Architecture/ABI independent
    – Transparent support for both 32-bit and 64-bit architectures
    – No BPF knowledge required
  - Provides documentation and examples
    – Currently 33 manpages, 100+ tests/examples in both C and Python
  - Generate safe, optimized filters with much less effort
    – Library generates BPF to ensure proper operation across multiple ABIs
    – Library optimizes filters for size/speed automatically
- Whitelist or Blacklist?
  - Whitelist
    - Pros
      - Good for small, “surgical” use cases
      - New syscalls will default to being blocked
    - Cons
      - A generic whitelist for all applications may be large, and slow
  - Blacklist
    - Pros
      - Good for generic, block-the-reallyscary-syscalls use cases
      - In these cases, the filter is often smaller and faster
    - Cons
      - Filters must be monitored and possibly updated every time the system is updated
      - New syscalls are allowed
- Other Considerations
  - Block entire syscalls or just specific parameters?
  - Additional architectures/ABIs?
  - Be careful with strings
  - Be careful with parameter filtering across architectures



