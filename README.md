# CMPE283-Assignment3
## Assignment 3: Instrumentation via hypercall

### Team Members
	•	Madhurima Subodh Dani (015261974)
	•	Shreemathi Duvvuri (015273102)

### [1] For each member in your team, provide 1 paragraph detailing what parts of the lab that member implemented / researched.
- Madhurima
  - Understood that CentOS kernel is easy to work with, hence gathered steps to spin up a CentOS kernel
  - Pushed final files into git and cloned them into VM
  - Executed steps required in outer and inner VM and collected snippets
  - Discussed on assignment questions and answers
- Shreemathi
  - Understood code changes required for assignment 3
  - Used our assignment 2's base files for cpuid.c and vmx.c and updated them for assignment 3
  - Discussed on assignment questions and answers
  - Created documentation and updated README.md

### [2] Steps Followed

- For Assignment 3, we used centOS as inner VM instead of ubuntu as done for assignment 2
  -  Downloaded a CentOS iso file and executed below command to create domain
      virt-install  --network bridge:virbr0 --name madhurimaCentOS \
     --os-variant=centos7.0 --ram=1024 --vcpus=1  \
     --disk path=/var/lib/libvirt/images/madhurimaCentOS-os.qcow2,format=qcow2,bus=virtio,size=5 \
      --graphics none  --location=/home/mdani/CentOS-7-x86_64-DVD-2009.iso \
      --extra-args="console=tty0 console=ttyS0,115200"  --check all=off
  -  Completed bootup steps of inner VM as guided on screen
  <img width="771" alt="image" src="https://user-images.githubusercontent.com/51197183/165874170-21912b89-9d87-4419-9e88-419147cb59ad.png">


-  Updated cpuid.c and vmx.c files for leaf node %eax=0x4FFFFFFD and %eax=0x4FFFFFFC in local machine and pushed the changes to git
-  In VM, cloned assignement 3 repository to get updated files
<img width="1098" alt="Screen Shot 2022-04-27 at 9 04 22 PM" src="https://user-images.githubusercontent.com/51197183/165873322-ab7cd28b-13b1-45d7-bf31-19fa6b6596a1.png">

-  In VM, cloned assignement 3 repository to get updated files
-  Entered into bash mode: sudo bash
-  From linux directory, executed:  make -j 8 modules
<img width="734" alt="Screen Shot 2022-04-27 at 9 19 32 PM" src="https://user-images.githubusercontent.com/51197183/165873460-b9023106-d816-447d-84cd-4478900b012c.png">

-  From linux directory, executed:  make -j 8 modules
-  make INSTALL_MOD_STRIP=1 modules_install && make install
<img width="1345" alt="Screen Shot 2022-04-27 at 9 21 50 PM" src="https://user-images.githubusercontent.com/51197183/165873640-5410319d-8527-43ec-83ea-c41d5c9a98b2.png">

-  Searched for kvm file: lsmod | grep kvm 
-  rmmod kvm_intel
-  rmmod kvm
-  modprobe kvm
-  modprobe kvm_intel
-  lsmod | grep kvm
<img width="749" alt="Screen Shot 2022-04-27 at 9 23 14 PM" src="https://user-images.githubusercontent.com/51197183/165873834-f05a1178-8488-47d3-b62a-a1202ae118c7.png">

-  Start the inner VM: sudo virsh start madhurimaCentOS
- Open console for inner VM: sudo virsh console madhurimaCentOS
<img width="428" alt="image" src="https://user-images.githubusercontent.com/51197183/165874413-8583e41e-6ea7-49d0-b6af-8f92db373070.png">

- Executed one sample case in inner VM: cpuid -l 0x4ffffffd -s 10
- Got error that cpuid is not installed, so installed it via yum: yum -y install cpuid
<img width="1101" alt="Screen Shot 2022-04-27 at 9 50 36 PM" src="https://user-images.githubusercontent.com/51197183/165874755-d4f296ac-aebb-4b83-98d1-e3d03985ae83.png">

- Tried executing with random exit numbers and checked corresponding messages in outher VM using dmesg:
<img width="340" alt="image" src="https://user-images.githubusercontent.com/51197183/165877714-4aa959d9-e8da-48b3-b3ff-5522f94d665c.png">


- Created a command.sh file in inner VM and wrote cpuid -l 0x4ffffffd -s X instructions from x = 0 to 69
- Executed command file: ./command.sh
<img width="535" alt="Screen Shot 2022-04-27 at 10 21 15 PM" src="https://user-images.githubusercontent.com/51197183/165875138-97162f66-8428-4b9f-9472-1d66608cdfc4.png">

- In outer VM, checked messages using command: dmesg
<img width="723" alt="image" src="https://user-images.githubusercontent.com/51197183/165879173-0d84eced-860e-4ec3-af5b-aabd836247ab.png">


- Rebooted inner VM: reboot
<img width="1359" alt="Screen Shot 2022-04-27 at 10 21 40 PM" src="https://user-images.githubusercontent.com/51197183/165875447-1c7d9e17-1b21-4746-ab12-ba21268bd940.png">


- Logged into inner VM as it came up and executed command file again
<img width="887" alt="Screen Shot 2022-04-27 at 10 21 56 PM" src="https://user-images.githubusercontent.com/51197183/165875518-87fc722b-5fe8-4eb3-8c02-ea9e744607ec.png">

- Checked messages in outer VM using command: dmesg
<img width="443" alt="image" src="https://user-images.githubusercontent.com/51197183/165879204-54a1bb08-de46-4518-b08c-57ea7126e1c0.png">


### [3] Questions

- Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there 
more exits performed during certain VM operations? Approximately how many exits does a full VM 
boot entail?
    - The frequency of exits kept increasing steadily not necessarily a linear rise (Refer exits for exit# 1 from snippets pasted above). Observed significant increase in exit numbers after rebooting the inner VM. After a full VM boot, approximately 3973521 exits occurred.

  
- Of the exit types defined in the SDM, which are the most frequent? Least?
    - Below were the most frequent exits:
      -  Exit Number	Exit Reason
          10	        CPUID
          30	        I/O Instruction
          32	        MSR access
          1	        External Interrupt
          28	        Control Register Acesses
          48	        EPT Violation
          49	        EPT Misconfiguration
          7	        Interrupt Window
          12	        HLT
          0	        Exception
    - Most of the exits didn't occur at all. Below are least frequent exit type along with the remaining others:
      -  Exit Number	Exit Reason
          54	        WBINVD or WBNOINVD
          29	        MOV DR
          55	        XSETBV
          44	        APIC Access









