# Note về windows kernel

Memory layout
Upper 2GB for kernel space: Add: 0x80000000 -  
Lower 2GB for User space 0 - 0x7ffffff

Running process only access user add space. kernel Code can access both

When A thread in pa process excute, the OS change porcessor-specific register to point to page dicrectory

Page directory base register is CR3, in ARM in TTBR

User/kernal address range store in 2 symbols in the kernel : MmSystemRangeStart (kernel) and MmHighestUserAddress (user). Have 64KB gap between 2 space refered as no-access region.

## PROCESS AND THREADS

A thread is defined by two kernel data structuresL ETHREAD and KTHREAD
ETHREAD contain housekeeping infomation: thread ID, associalted process, debugging enabled
KTHREAD store scheduling infomation for thread dispatcher such as thread stack infor, processor run, aleartable state
Windows scheduler operate on threads

