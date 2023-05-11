# Resolve .NET Memory Leaks With Windbg

## In Windows Presentation Foundation Applications

- In this we open task manager 64 bit exe .
C:\Windows\SysWOW64\taskmgr.exe

- Take dump of x86 WPF application.

- Open x86 Windbg and drop dump file there.

- Use either of one command below to load SOS.dll (SOS Debugging Extension).
!loadby sos mscorwks
!load C:\Windows\Microsoft.NET\Framework\v4.0.30319\SOS.dll


- Finding the GC root
How garbage collection works, is out of scope for this article, 
but suffice it to say that as long as an Object has one or more "roots", 
or references it will not be garbage collected. Windbg has tools for us 
to find all the roots or references to an object. First, we need to find 
a memory location of one of the objects(Classs prceeding with Namespace), 
so we run the following command, to get all memory addresses of the 
allocated object.
Use below command to heap dump
!dumpheap -type Test_Wpf_App.ViewModel.MainWindowViewModel

0:000> !dumpheap -type Test_Wpf_App
 Address       MT     Size
02e86d4c 00ed949c      116     
02edb4a0 058a1dc0      472     
02f346b8 058a2c04       20     

Statistics:
      MT    Count    TotalSize Class Name
058a2c04        1           20 Test_Wpf_App.ViewModel.MainWindowViewModel
00ed949c        1          116 Test_Wpf_App.App
058a1dc0        1          472 Test_Wpf_App.MainWindow
Total 3 objects


- Pick one of the memory addresses and MT(MethodTable Address) and use the following command to find the GCRoot.
!gcroot 058a2c04

- If it gives 0 unique roots than its okay otherwise track down each root and resolve
the code changes in application.


## In Silverlight Applications

- In this we open task manager 64 bit exe .
C:\Windows\SysWOW64\taskmgr.exe

- Take dump of x86 Silverlight application.

- Open x86 Windbg and drop dump file there.

- Use either of one command below to load SOS.dll (SOS Debugging Extension).
!load C:\Program Files (x86)\Microsoft Silverlight\5.1.50918.0\sos.dll

- Finding the GC root
How garbage collection works, is out of scope for this article, 
but suffice it to say that as long as an Object has one or more "roots", 
or references it will not be garbage collected. Windbg has tools for us 
to find all the roots or references to an object. First, we need to find 
a memory location of one of the objects(Classs prceeding with Namespace),
so we run the following command, to get all memory addresses of the allocated 
object.
Use below command to heap dump
!dumpheap -type Test_SilverLight_App

0:000> !dumpheap -type Test_SilverLight_App
 Address       MT     Size
02e86d4c 00ed949c      116     
02edb4a0 058a1dc0      472     
02f346b8 058a2c04       20     

Statistics:
      MT    Count    TotalSize Class Name
058a2c04        1           20 Test_SilverLight_App.ViewModel.MainWindowViewModel
00ed949c        1          116 Test_SilverLight_App.App
058a1dc0        1          472 Test_SilverLight_App.MainWindow
Total 3 objects


- Pick one of the memory addresses and MT (MethodTable Address) and use the following command to find the GCRoot.
!gcroot 058a2c04

- If it gives 0 unique roots than its okay otherwise track down each root and resolve
the code changes in application.
