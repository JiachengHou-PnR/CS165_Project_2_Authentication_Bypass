# CS165_Project_2_Authentication_Bypass

CS 165 Project 2 (Fall 2021) University of California, Riverside.

By Sanchit Goel and Jiacheng Hou.

## Helpful Files

1. IDA Free: https://hex-rays.com/ida-free/#download
2. The IDA PRO Book: https://libgen.rs/book/index.php?md5=485167AC5D159164B5CAB270A8EDC3C7

## Part 1 Implementation

### Flag

The flag is:
            
    34gdfh340234

### Bypass Username Check

From examine the code, we see the username "admin"'s offset location is moved to ecx (in function sub_401080, address 0x1 0x4010D0), and the inputed username address is loaded to eax then the username is loaded to dl. 

1. At address 0x1 0x4010D7, change `cmp    dl, [ecx]` to `cmp    dl, [eax]` to bypass the comparison of username's first char.
2. At address 0x1 0x4010E2, change `cmp    dl, [ecx+1]` to `cmp    dl, [eax+1]` to bypass the second check.

### Bypass Password Check

Similar to bypassing username check, we modified the code where the inputed password is compared to the stored password, and changed the comparision code to be comparing to itself and always be true, therefore bypassing the password check.

## Part 2 Implementation

### Fix Banner

Text on top of window: function sub_76B074

1. Search 'trial'
2. Find strings
3. 'List cross references to...' at "aTrial..."
4. Go to function sub_76B074

### Register Window Code 

sub_76B784

### f

sub_76C12C

sub_76C290


## From Project Description

(Made for CS 165 @ UCR. Academic purposes only. Please do not redistribute)

* Operating system: Windows (The experiment is tested on Windows 7 only but I have no reason to believe that it will not run on Windows 8/8.1/10). As UCR students, you can download Windows 10 for free (and install it in your VM if needed): https://ucr.onthehub.com/WebStore/OfferingDetails.aspx?o=d4d37189-79e9-e611-9425-b8ca3a5db7a1
* Software required: IDA Pro. To reverse engineer a binary, we need a software capable of disassembling the binary instructions into assembly instructions that human can understand. A "freeware" version exists which is what we will use in this project. It can be found at https://www.hex-rays.com/products/ida/support/download_freeware.shtml. In addition, you need to download the Visual C++ Redistributable for Visual Studio 2015 (32-bit version marked as x86) at https://www.microsoft.com/en-us/download/details.aspx?id=48145.
* Reference book: The IDA PRO book, by Chris Eagle. 
* Other reference: Intel 64 and IA-32 Architectures Software Developer's Manual. Volume 2 (2A, 2B & 2C): Instruction Set Reference, A-Z

To carry out the project, you need to understand a few things which will be covered in class and lab sessions (Note that no programming is necessary for this project):
- Basic assembly language knowledge
- Stack layout
- Function calling convention

### Part 1:  Local authentication bypass -- capture the flag: 

In part 1 of the project, you will be asked to bypass the authentication used in a toy application created for this class. See authenticate_yourself.exe in the project folder. Upon successful authentication bypass, you will be awarded with a unique string that you can take to submit as part of the project outcome. 

#### Workflow:

- Locate the critical function of authentication (Hints: What gets printed when authentication fails? Which Windows APIs are used to print results to command line?).
- Understand how the authentication takes place. (Hints: Look at the branch, i.e., conditional jump instruction, that determines the outcome of the authentication.)
- Change the instructions related to the branch so they are no longer effective.
Note: You are welcome to propose your own workflow or tweak the existing one as long as it can achieve the same result. 

#### Submission requirement: 
- (1 points) Flag (unique string). 
- (2 points) The modified binary that can print the flag (unique string) automatically or even when the credential was incorrect. 
- (2 points) Documentation detailing the methodology and thought process that you went through to bypass the authentication. Figures can be used to illustrate the core program logic if needed. The document should include the instructions that are changed/added/deleted during the process in the form of (Address, Original Instruction, New Instruction). Please limit the document to 2 pages maximum (pdf or word). 

#### Hints: 
- Debugging the program during runtime can be extremely helpful. IDA has a built-in debugger so you can set breakpoints and observe the program flow. Ollydbg and WinDbg are also great debuggers on Windows.
- The instruction to patch a program is listed towards the end of the document. If you want to quickly test the effect of a patch, you can do so during a debugging session (it may not take any effect if you patch before starting the debugging session).



### Part 2: Real-world authentication bypass:

In part 2 of the project, you will be asked to change a real world application so you can continue using it after the trial period is over. The application can be downloaded at http://www.winedt.com/archive/winedt90-32.exe. Note that the methodology and workflow transfer directly from Part 1. However, the code base will be much more complex and it will be harder to locate the correct function that performed the check on trial period. 

#### Hints: 

- All logic is inside WinEdt.exe. 
- The software stores and checks the installation time in Windows Registry. 
- The software prints out error messages ("trial period is over") that may be searchable in the binary. 
- Debugging the program at runtime can allow you to see the actual value of memory and registers that otherwise cannot be seen statically.


#### Submission requirement: 

- (5 points) A modified WinEdt.exe that can be successfully launched either "1) without the "Expired Trial Period" banner at the top, or 2) without the pop-up reminding about the 31-day trial period" even when the local time is set to 1 year after the current time (verified by TA in labs).
- (2 bonus points awarded) if both requirements above are met.
- (6 points) Documentation detailing the methodology and thought process that you went through to bypass the authentication. Figures can be used to illustrate the core program logic if needed. The document should include the instructions that are changed/added/deleted during the process in the form of (Address, Original Instruction, New Instruction). Please limit the document to 2 pages maximum (pdf or word). You will still get partial credit for writing up your thought process and the difficulty you faced, even if you are not able to successfully modify WinEdt.exe.


#### Instructions for patching a binary:
- Go to the cfg folder in your IDA installation directory (e.g., C:\Program Files (x86)\IDA Free\cfg). Open the idagui.cfg file and change the following setting to true "DISPLAY_PATCH_SUBMENU    = YES". Restart IDA so the menu will now appear under "Edit->Patch Program -> Change Byte/Change Word/Assemble". 
- Use the menu to change the bytes at desired addresses. Note that the change only persists in memory and you will need to push the change to the binary. 
- To make the change persistent, we need to produce a diff file through IDA's menu "File->Produce file->Create Dif file...".
- Use the ida_patcher.exe binary distributed with the project to patch the WinEdt.exe (make sure to create a backup before you do so). 
- ida_patcher.exe [-i <binary>] [-p <dif file>]. Most likely you will do something like "ida_patcher.exe -i WinEdt.exe -p WinEdt.dif".
- Now the WinEdt.exe is patched, test it by changing your local clock.
(Details of earlier steps can be found at http://resources.infosecinstitute.com/applied-cracking-byte-patching-ida-pro/)



### Academic Integrity and Legal implications:

Since this project is highly result-driven, it is strictly prohibited to share answers across teams. Teams are not allowed to share the step-by-step procedure of their reverse engineering directly with each other. However, discussions on the understanding of the binary and procedures are encouraged.

Please note that the sole goal of the project is to help you learn the reverse engineering skills and it is illegal to use them to gain unauthorized use of otherwise commercial software. Some software strictly prohibits reverse engineering even if they are free.