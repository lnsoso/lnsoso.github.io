---
layout: post
title: ! '[超长篇] Inject Your Code to a Portable Executable File'
author: Alvin
date: !binary |-
  MjAwNy0wNS0yNCAxNTo1MDowMSArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wNS0yNCAwNzo1MDowMSArMDgwMA==
---
转至: <a href="http://www.codeguru.com/cpp/w-p/system/misc/article.php/c11393">http://www.codeguru.com/cpp/w-p/system/misc/article.php/c11393</a>
<strong>Downloads</strong>
<li><a href="http://www.codeguru.com/dbfiles/get_file/pemaker1.zip?id=11393&lbl=PEMAKER1_ZIP&ds=20060302">pemaker1.zip</a> - 
</li>
<li><a href="http://www.codeguru.com/dbfiles/get_file/pemaker2.zip?id=11393&lbl=PEMAKER2_ZIP&ds=20060302">pemaker2.zip</a> - 
</li>
<li><a href="http://www.codeguru.com/dbfiles/get_file/pemaker3.zip?id=11393&lbl=PEMAKER3_ZIP&ds=20060302">pemaker3.zip</a> - 
</li>
<li><a href="http://www.codeguru.com/dbfiles/get_file/pemaker4.zip?id=11393&lbl=PEMAKER4_ZIP&ds=20060302">pemaker4.zip</a> - 
</li>
<li><a href="http://www.codeguru.com/dbfiles/get_file/pemaker5.zip?id=11393&lbl=PEMAKER5_ZIP&ds=20060302">pemaker5.zip</a> - 
</li>
<li><a href="http://www.codeguru.com/dbfiles/get_file/peviewer.zip?id=11393&lbl=PEVIEWER_ZIP&ds=20060302">peviewer.zip</a> - 
</li>
<li><a href="http://www.codeguru.com/dbfiles/get_file/test1.zip?id=11393&lbl=TEST1_ZIP&ds=20060302">test1.zip</a> - </li>
<a name="more"><font color="#000000"></font></a><a href="http://en.wikipedia.org/wiki/Windows_NT_3.51" target="new">Windows NT 3.51</a> (I mean, <a href="http://en.wikipedia.org/wiki/Windows_3.1" target="new">Win3.1</a>, <a href="http://en.wikipedia.org/wiki/Windows_95" target="new">Win95</a>, <a href="http://en.wikipedia.org/wiki/Windows_98" target="new">Win98</a> were not perfect <a href="http://en.wikipedia.org/wiki/Operating_System" target="new">OS</a>s). The MS-DOS data causes that your executable file to have the performance inside MS-DOS and <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/vccore98/HTML/_core_.2f.stub.asp" target="new">the MS-DOS Stub program</a> lets it display: <strong>"This program can not be run in MS-DOS mode"</strong> or <strong>"This program can be run only in Windows mode"</strong>, or some things like these comments when you try to run a Windows EXE file inside <a href="http://en.wikipedia.org/wiki/MS-DOS" target="new">MS-DOS 6.0</a>, where there is no footstep of Windows. Thus, this data is reserved for the code to indicate these comments in the <a href="http://en.wikipedia.org/wiki/MS-DOS" target="new">MS-DOS</a> <a href="http://en.wikipedia.org/wiki/Operating_System" target="new">operating system</a>. The most interesting part of the <a href="http://en.wikipedia.org/wiki/MS-DOS" target="new">MS-DOS</a> data is "<strong>MZ</strong>"! Can you believe, it refers to the name of "<a href="http://en.wikipedia.org/wiki/Mark_Zbikowski" target="new">Mark Zbikowski</a>", one of the first Microsoft programmers?
<font color="#000000"><img height="175" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEMAKER_GIF&ds=20060302" width="452" alt="" /></font>
<h3>0 Preface</h3>
You might demand to comprehend the ways a virus program injects its procedure into the interior of a portable executable file and corrupts it, or you are interested in implementing a packer or a protector to encrypt the data of your portable executable (PE) file. This article is committed to represent a brief discussion to realize the performance that is accomplished by EXE tools or some kinds of mal-ware.
You can employ this article's source code to create your custom EXE builder. It could be used to make an EXE protector in the right way, or with the wrong intention, to spread a virus. However, my purpose of writing this article has been the first application, so I will not be responsible for the immoral usage of these methods.
<h3>1 Prerequisites</h3>
There are no specific mandatory prerequisites to follow the topics in this article. If you are familiar with a debugger and also the portable file format, I suggest you to drop to Sections 2 and 3; the whole of these sections has been made for people who don't have any knowledge regarding the EXE file format or debuggers.
<h3>2 Portable Executable File Format</h3>
The Portable Executable file format was defined to provide the best way for the Windows Operating System to execute code and also to store the essential data that is needed to run a program&mdash;for example constant data, variable data, import library links, and resource data. It consists of MS-DOS file information, Windows NT file information, Section Headers, and Section images, as shown in Table 1.
<h4>2.1 The MS-DOS data</h4>
These data let you remember the first days of developing the Windows Operating System. You were at the beginning of a way to achieve a complete Operating System such as 
To me, only the offset of the PE signature in the <a href="http://en.wikipedia.org/wiki/MS-DOS" target="new">MS-DOS</a> data is important, so I can use it to find the position of the <a href="http://en.wikipedia.org/wiki/Windows_NT" target="new">Windows NT</a> data. I just recommend that you take a look at Table 1, and then observe the structure of <tt>IMAGE_DOS_HEADER</tt> in the <em><winnt.h></em> header in the <em><Microsoft Visual Studio .net path>\VC7\PlatformSDK\include\</em> folder or the <em><Microsoft Visual Studio 6.0 path>\VC98\include\</em> folder. I do not know why the Microsoft team has forgotten to provide some comment about this structure in the <a href="http://msdn.microsoft.com/" target="new">MSDN</a> library!
<pre><span class="codeKeyword">typedef</span> <span class="codeKeyword">struct</span> _IMAGE_DOS_HEADER { <span class="codeComment">// DOS .EXE header "MZ"</span>    WORD   e_magic;                <span class="codeComment">// Magic number</span>    WORD   e_cblp;                 <span class="codeComment">// Bytes on last page of file</span>    WORD   e_cp;                   <span class="codeComment">// Pages in file</span>    WORD   e_crlc;                 <span class="codeComment">// Relocations</span>    WORD   e_cparhdr;              <span class="codeComment">// Size of header in</span>                                   <span class="codeComment">// paragraphs</span>    WORD   e_minalloc;             <span class="codeComment">// Minimum extra paragraphs</span>                                   <span class="codeComment">// needed</span>    WORD   e_maxalloc;             <span class="codeComment">// Maximum extra paragraphs</span>                                   <span class="codeComment">// needed</span>    WORD   e_ss;                   <span class="codeComment">// Initial (relative) SS</span>                                   <span class="codeComment">// value</span>    WORD   e_sp;                   <span class="codeComment">// Initial SP value</span>    WORD   e_csum;                 <span class="codeComment">// Checksum</span>    WORD   e_ip;                   <span class="codeComment">// Initial IP value</span>    WORD   e_cs;                   <span class="codeComment">// Initial (relative) CS</span>                                   <span class="codeComment">// value</span>    WORD   e_lfarlc;               <span class="codeComment">// File address of relocation</span>                                   <span class="codeComment">// table</span>    WORD   e_ovno;                 <span class="codeComment">// Overlay number</span>    WORD   e_res[4];               <span class="codeComment">// Reserved words</span>    WORD   e_oemid;                <span class="codeComment">// OEM identifier</span>                                   <span class="codeComment">// (for e_oeminfo)</span>    WORD   e_oeminfo;              <span class="codeComment">// OEM information;</span>                                   <span class="codeComment">// e_oemid specific</span>    WORD   e_res2[10];             <span class="codeComment">// Reserved words</span>    LONG   <font color="#ff0000">e_lfanew</font>;               <span class="codeComment">// File address of the new</span>                                   <span class="codeComment">// exe header</span>  } IMAGE_DOS_HEADER, *PIMAGE_DOS_HEADER;</pre>
<tt>e_lfanew</tt> is the offset that refers to the position of the Windows NT data. I have provided a program to obtain the header information from an EXE file and to display it to you. To use the program, just try:
<h4>PE Viewer</h4>
<img height="314" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEVIEWER1_GIF&ds=20060302" width="491" alt="" />
<img height="363" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEVIEWER2_GIF&ds=20060302" width="500" alt="" />
(<a href="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEVIEWER2_GIF&ds=20060302" target="_blank">Full Size Image</a>)
<img height="313" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEVIEWER3_GIF&ds=20060302" width="500" alt="" />
(<a href="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEVIEWER3_GIF&ds=20060302" target="_blank">Full Size Image</a>)
This sample is useful for the whole of this article.
<strong>Table 1:</strong> Portable Executable file format structure
<table cellspacing="2" cellpadding="2" border="2">
<tbody>
<tr valign="top">
<td rowspan="17">MS-DOS 
            information</td>
<td rowspan="16"><tt>IMAGE_DOS_
            HEADER</tt></td>
<td>DOS EXE Signature</td>
<td rowspan="16">
<pre lang="text">00000000  ASCII <font color="#008000">"MZ"</font>00000002  DW 009000000004  DW 000300000006  DW 000000000008  DW 00040000000A  DW 00000000000C  DW FFFF0000000E  DW 000000000010  DW 00B800000012  DW 000000000014  DW 000000000016  DW 000000000018  DW 00400000001A  DW 00000000001C  DB 00b&b&0000003B  DB 000000003C  DD <font color="#ff0000">000000F0</font></pre>            </td>        </tr>
<tr valign="top">
<td><tt>DOS_PartPag</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_PageCnt</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_ReloCnt</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_HdrSize</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_MinMem</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_MaxMem</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_ReloSS</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_ExeSP</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_ChkSum</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_ExeIPP</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_ReloCS</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_TablOff</tt></td>        </tr>
<tr valign="top">
<td><tt>DOS_Overlay</tt></td>        </tr>
<tr valign="top">
<td><tt>b&
            </tt>Reserved words<tt>
            b&</tt></td>        </tr>
<tr valign="top">
<td>Offset to PE signature</td>        </tr>
<tr valign="top">
<td>MS-DOS Stub 
            Program</td>
<td colspan="2">
<pre lang="text">00000040  ..B:..B4.C!B8\LC!<font color="#008000">This program canno</font>00000060  <font color="#008000">t be run in DOS mode.</font>...$.......</pre>            </td>        </tr>
<tr valign="top">
<td rowspan="54">Windows NT 
            information
<tt>IMAGE_
            NT_HEADERS</tt>            </td>
<td>Signature</td>
<td>PE signature (PE)</td>
<td>
<pre lang="text"><font color="#ff0000">000000F0</font>  ASCII <font color="#008000">"PE"</font></pre>            </td>        </tr>
<tr valign="top">
<td rowspan="7"><tt>IMAGE_
            FILE_HEADER</tt></td>
<td><tt>Machine</tt></td>
<td rowspan="7">
<pre lang="text">000000F4  DW 014C000000F6  DW 0003000000F8  DD 3B7D8410000000FC  DD 0000000000000100  DD 0000000000000104  DW 00E000000106  DW 010F</pre>            </td>        </tr>
<tr valign="top">
<td><tt>NumberOfSections</tt></td>        </tr>
<tr valign="top">
<td><tt>TimeDateStamp</tt></td>        </tr>
<tr valign="top">
<td><tt>PointerToSymbolTable</tt></td>        </tr>
<tr valign="top">
<td><tt>NumberOfSymbols</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfOptionalHeader</tt></td>        </tr>
<tr valign="top">
<td><tt>Characteristics</tt></td>        </tr>
<tr valign="top">
<td rowspan="46"><tt>IMAGE_
            OPTIONAL_
            HEADER32</tt></td>
<td><tt>MagicNumber</tt></td>
<td rowspan="30">
<pre lang="text">00000108  DW 010B0000010A  DB 070000010B  DB 000000010C  DD 0001280000000110  DD 00009C0000000114  DD 0000000000000118  DD 000124750000011C  DD 0000100000000120  DD 0001400000000124  DD 0100000000000128  DD 000010000000012C  DD 0000020000000130  DW 000500000132  DW 000100000134  DW 000500000136  DW 000100000138  DW 00040000013A  DW 00000000013C  DD 0000000000000140  DD 0001F00000000144  DD 0000040000000148  DD 0001D7FC0000014C  DW 00020000014E  DW 800000000150  DD 0004000000000154  DD 0000100000000158  DD 001000000000015C  DD 0000100000000160  DD 0000000000000164  DD 00000010</pre>            </td>        </tr>
<tr valign="top">
<td><tt>MajorLinkerVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>MinorLinkerVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfCode</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfInitializedData</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfUninitializedData</tt></td>        </tr>
<tr valign="top">
<td><tt>AddressOfEntryPoint</tt></td>        </tr>
<tr valign="top">
<td><tt>BaseOfCode</tt></td>        </tr>
<tr valign="top">
<td><tt>BaseOfData</tt></td>        </tr>
<tr valign="top">
<td><tt>ImageBase</tt></td>        </tr>
<tr valign="top">
<td><tt>SectionAlignment</tt></td>        </tr>
<tr valign="top">
<td><tt>FileAlignment</tt></td>        </tr>
<tr valign="top">
<td><tt>MajorOSVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>MinorOSVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>MajorImageVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>MinorImageVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>MajorSubsystemVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>MinorSubsystemVersion</tt></td>        </tr>
<tr valign="top">
<td><tt>Reserved</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfImage</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfHeaders</tt></td>        </tr>
<tr valign="top">
<td><tt>CheckSum</tt></td>        </tr>
<tr valign="top">
<td><tt>Subsystem</tt></td>        </tr>
<tr valign="top">
<td><tt>DLLCharacteristics</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfStackReserve</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfStackCommit</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfHeapReserve</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfHeapCommit</tt></td>        </tr>
<tr valign="top">
<td><tt>LoaderFlags</tt></td>        </tr>
<tr valign="top">
<td><tt>NumberOfRvaAndSizes</tt></td>        </tr>
<tr valign="top">
<td rowspan="16"><tt>IMAGE_
            DATA_DIRECTORY[16]</tt></td>
<td>Export Table</td>        </tr>
<tr valign="top">
<td>Import Table</td>        </tr>
<tr valign="top">
<td>Resource Table</td>        </tr>
<tr valign="top">
<td>Exception Table</td>        </tr>
<tr valign="top">
<td>Certificate File</td>        </tr>
<tr valign="top">
<td>Relocation Table</td>        </tr>
<tr valign="top">
<td>Debug Data</td>        </tr>
<tr valign="top">
<td>Architecture Data</td>        </tr>
<tr valign="top">
<td>Global Ptr</td>        </tr>
<tr valign="top">
<td>TLS Table</td>        </tr>
<tr valign="top">
<td>Load Config Table</td>        </tr>
<tr valign="top">
<td>Bound Import Table</td>        </tr>
<tr valign="top">
<td>Import Address Table</td>        </tr>
<tr valign="top">
<td>Delay Import Descriptor</td>        </tr>
<tr valign="top">
<td>COM+ Runtime Header</td>        </tr>
<tr valign="top">
<td>Reserved</td>        </tr>
<tr valign="top">
<td rowspan="13">Sections 
            information</td>
<td rowspan="10"><tt>IMAGE_
            SECTION_
            HEADER[0]</tt></td>
<td><tt>Name[8]</tt></td>
<td rowspan="10">
<pre lang="text">000001E8  ASCII<font color="#008000">".text"</font>000001F0  DD 000126B0000001F4  DD 00001000000001F8  DD 00012800000001FC  DD 0000040000000200  DD 0000000000000204  DD 0000000000000208  DW 00000000020A  DW 00000000020C  DD 60000020    CODE|EXECUTE|READ</pre>            </td>        </tr>
<tr valign="top">
<td><tt>VirtualSize</tt></td>        </tr>
<tr valign="top">
<td><tt>VirtualAddress</tt></td>        </tr>
<tr valign="top">
<td><tt>SizeOfRawData</tt></td>        </tr>
<tr valign="top">
<td><tt>PointerToRawData</tt></td>        </tr>
<tr valign="top">
<td><tt>PointerToRelocations</tt></td>        </tr>
<tr valign="top">
<td><tt>PointerToLineNumbers</tt></td>        </tr>
<tr valign="top">
<td><tt>NumberOfRelocations</tt></td>        </tr>
<tr valign="top">
<td><tt>NumberOfLineNumbers</tt></td>        </tr>
<tr valign="top">
<td><tt>Characteristics</tt></td>        </tr>
<tr valign="top">
<td><tt>b&
            b&
            b&
            IMAGE_
            SECTION_
            HEADER[n]</tt></td>
<td colspan="2">
<pre lang="text">00000210  ASCII<font color="#008000">".data"</font>; SECTION00000218  DD 0000101C ; VirtualSize = 0x101C0000021C  DD 00014000 ; VirtualAddress = 0x1400000000220  DD 00000A00 ; SizeOfRawData = 0xA0000000224  DD 00012C00 ; PointerToRawData = 0x12C0000000228  DD 00000000 ; PointerToRelocations = 0x00000022C  DD 00000000 ; PointerToLineNumbers = 0x000000230  DW 0000     ; NumberOfRelocations = 0x000000232  DW 0000     ; NumberOfLineNumbers = 0x000000234  DD C0000040 ; Characteristics =                        INITIALIZED_DATA|READ|WRITE00000238  ASCII<font color="#008000">".rsrc"</font>; SECTION00000240  DD 00008960 ; VirtualSize = 0x896000000244  DD 00016000 ; VirtualAddress = 0x1600000000248  DD 00008A00 ; SizeOfRawData = 0x8A000000024C  DD 00013600 ; PointerToRawData = 0x1360000000250  DD 00000000 ; PointerToRelocations = 0x000000254  DD 00000000 ; PointerToLineNumbers = 0x000000258  DW 0000     ; NumberOfRelocations = 0x00000025A  DW 0000     ; NumberOfLineNumbers = 0x00000025C  DD 40000040 ; Characteristics =                        INITIALIZED_DATA|READ</pre>            </td>        </tr>
<tr valign="top">
<td><tt>SECTION[0]</tt></td>
<td colspan="2">
<pre lang="text">00000400  EA 22 DD 77 D7 23 DD 77  C*"C.wC.#C.w00000408  9A 18 DD 77 00 00 00 00  E!.C.w....00000410  2E 1E C7 77 83 1D C7 77  ..C.wF..C.w00000418  FF 1E C7 77 00 00 00 00  C?.C.w....00000420  93 9F E7 77 D8 05 E8 77  b.E8C'wC..C(w00000428  FD A5 E7 77 AD A9 E9 77  C=B%C'w&shy;B)C)w00000430  A3 36 E7 77 03 38 E7 77  B#6C'w.8C'w00000438  41 E3 E6 77 60 8D E7 77  AC#C&w`BC'w00000440  E6 1B E6 77 2B 2A E7 77  C&.C&w+*C'w00000448  7A 17 E6 77 79 C8 E6 77  z.C&wyC.C&w00000450  14 1B E7 77 C1 30 E7 77  ..C'wC.0C'wb&</pre>            </td>        </tr>
<tr valign="top">
<td><tt>b&
            b&
            b&
            SECTION[n]</tt></td>
<td colspan="2">
<pre lang="text">b&0001BF00  63 00 2E 00 63 00 68 00  c...c.h.0001BF08  6D 00 0A 00 43 00 61 00  m...C.a.0001BF10  6C 00 63 00 75 00 6C 00  l.c.u.l.0001BF18  61 00 74 00 6F 00 72 00  a.t.o.r.0001BF20  11 00 4E 00 6F 00 74 00  ..N.o.t.0001BF28  20 00 45 00 6E 00 6F 00   .E.n.o.0001BF30  75 00 67 00 68 00 20 00  u.g.h. .0001BF38  4D 00 65 00 6D 00 6F 00  M.e.m.o.0001BF40  72 00 79 00 00 00 00 00  r.y.....0001BF48  00 00 00 00 00 00 00 00  ........0001BF50  00 00 00 00 00 00 00 00  ........0001BF58  00 00 00 00 00 00 00 00  ........0001BF60  00 00 00 00 00 00 00 00  ........0001BF68  00 00 00 00 00 00 00 00  ........0001BF70  00 00 00 00 00 00 00 00  ........0001BF78  00 00 00 00 00 00 00 00  ........</pre>            </td>        </tr>    </tbody></table>
<h4>2.2 The Windows NT data</h4>
As mentioned in the preceding section, <tt>e_lfanew</tt> storage in the MS-DOS data structure refers to the location of the Windows NT information. Hence, if you assume that the <tt>pMem</tt> pointer relates the start point of the memory space for a selected portable executable file, you can retrieve the MS-DOS header and also the Windows NT headers by the following lines, which you also can perceive in the PE viewer sample (<em>pelib.cpp</em>, <tt>PEStructure::OpenFileName()</tt>):
<pre>IMAGE_DOS_HEADER        image_dos_header;IMAGE_NT_HEADERS        image_nt_headers;PCHAR pMem;b&memcpy(&image_dos_header, pMem,       <span class="codeKeyword">sizeof</span>(IMAGE_DOS_HEADER));memcpy(&image_nt_headers,       pMem+image_dos_header.e_lfanew,       <span class="codeKeyword">sizeof</span>(IMAGE_NT_HEADERS));</pre>
<a name="more"><font color="#000000"></font></a><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_nt_headers_str.asp" target="new"><tt>IMAGE_NT_HEADERS</tt></a> structure definition. It makes it possible to grasp what the image NT header maintains to execute a code inside the Windows NT OS. Now, you are conversant with the Windows NT structure; it consists of the <font color="#008000">"PE"</font> Signature, the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_file_header_str.asp" target="new">File Header</a>, and the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_optional_header_str.asp" target="new">Optional Header</a>. Do not forget to take a glimpse at their comments in the <a href="http://msdn.microsoft.com/" target="new">MSDN</a> Library and in Table 1.
It seems to be very simple, the retrieval of the headers information. I recommend inspecting the MSDN library regarding the 
One the whole, I consider merely, in most circumstances, the following cells of the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_nt_headers_str.asp" target="new"><tt>IMAGE_NT_HEADERS</tt></a> structure:
<pre>FileHeader->NumberOfSectionsOptionalHeader->AddressOfEntryPointOptionalHeader->ImageBaseOptionalHeader->SectionAlignmentOptionalHeader->FileAlignmentOptionalHeader->SizeOfImageOptionalHeader->DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT]              ->VirtualAddressOptionalHeader->DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT]              ->Size</pre>
You can observe the main purpose of these values clearly, and their role when the internal virtual memory space allocated for an EXE file by the Windows task manager if you pay attention to their explanations in <a href="http://msdn.microsoft.com/" target="new">MSDN</a> library, so I am not going to repeat the MSDN annotations here.
I should make a brief comment regarding the PE data directories, or <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_optional_header_str.asp" target="new"><tt>OptionalHeader</tt></a>-> <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_data_directory_str.asp" target="new"><tt>DataDirectory[]</tt></a>, because I think there are a few aspects of interest concerning them. When you come to survey the Optional header through the Windows NT information, you will find that there are <em>16</em> directories at the end of the Optional Header, where you can find the consecutive directories, including their Relative Virtual Address and Size. I just mention here the notes from <em><winnt.h></em> to clarify these information:
<pre><span class="codeComment">// Export Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_EXPORT          0<span class="codeComment">// Import Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_IMPORT          1<span class="codeComment">// Resource Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_RESOURCE        2<span class="codeComment">// Exception Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_EXCEPTION       3<span class="codeComment">// Security Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_SECURITY        4<span class="codeComment">// Base Relocation Table</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_BASERELOC       5<span class="codeComment">// Debug Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_DEBUG           6<span class="codeComment">// Architecture Specific Data</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_ARCHITECTURE    7<span class="codeComment">// RVA of GP</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_GLOBALPTR       8<span class="codeComment">// TLS Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_TLS             9<span class="codeComment">// Load Configuration Directory</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_LOAD_CONFIG    10<span class="codeComment">// Bound Import Directory in headers</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_BOUND_IMPORT   11<span class="codeComment">// Import Address Table</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_IAT            12<span class="codeComment">// Delay Load Import Descriptors</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_DELAY_IMPORT   13<span class="codeComment">// COM Runtime descriptor</span><span class="codeKeyword">#define</span> IMAGE_DIRECTORY_ENTRY_COM_DESCRIPTOR 14</pre>
The last one (15) was reserved for use in the future; I have not yet seen any purpose for it, even in PE64.
For instance, if you want to perceive the relative virtual address (RVA) and the size of the resource data, it is enough to retrieve them by:
<pre>DWORD dwRVA  = image_nt_headers.OptionalHeader->   DataDirectory[IMAGE_DIRECTORY_ENTRY_RESOURCE]->VirtualAddress;DWORD dwSize = image_nt_headers.OptionalHeader->   DataDirectory[IMAGE_DIRECTORY_ENTRY_RESOURCE]->Size;</pre>
To comprehend more regarding the significance of data directories, I forward you to Section 3.4.3 of the <a href="http://www.microsoft.com/whdc/system/platform/firmware/PECOFF.mspx" target="new">Microsoft Portable Executable and the Common Object File Format Specification</a> document by Microsoft, and furthermore Section 6 of this document, where you discern the various types of sections and their applications. You will see the section's advantage subsequently.
<h4>2.3 The Section Headers and Sections</h4>
You currently observe how the portable executable files declare the location and the size of a section on a disk storage file and inside the virtual memory space allocated for the program with <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_nt_headers_str.asp" target="new"><tt>IMAGE_NT_HEADERS</tt></a>-> <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_optional_header_str.asp" target="new"><tt>OptionalHeader</tt></a>-><tt>SizeOfImage</tt> by the Windows task manager, as well the characteristics to demonstrate the type of the section. To better understand the Section header as my previous declaration, I suggest having a brief look at the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_section_header_str.asp" target="new"><tt>IMAGE_SECTION_HEADER</tt></a> structure definition in the MSDN library. For an EXE packer developer, <tt>VirtualSize</tt>, <tt>VirtualAddress</tt>, <tt>SizeOfRawData</tt>, <tt>PointerToRawData</tt>, and <tt>Characteristics</tt> cells have significant rules. When developing an EXE packer, you should be clever enough to play with them. There are somet hings to note when you modify them; you should take care to align the <tt>VirtualSize</tt> and <tt>VirtualAddress</tt> according to <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_optional_header_str.asp" target="new"><tt>OptionalHeader</tt></a>-><tt>SectionAlignment</tt>, as well as <tt>SizeOfRawData</tt> and <tt>PointerToRawData</tt> in line with <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_optional_header_str.asp" target="new"><tt>OptionalHeader</tt></a>-><tt>FileAlignment</tt>. Otherwise, you will corrupt your target EXE file and it will never run. Regarding <tt>Characteristics</tt>, I pay attention mostly to establish a section by <tt>IMAGE_SCN_MEM_READ</tt> | <tt>IMAGE_SCN_MEM_WRITE</tt> | <tt>IMAGE_SCN_CNT_INITIALIZED_DATA</tt>, I prefer that my new section has the ability to initialize such data during the running process, such as import table; besides, I need it to be able to modify itself by the loader with my settings in the section characteristics to read- and writeable.
Moreover, you should pay attention to the section names; you can know the purpose of each section by its name. I will just forward you to Section 6 of the <a href="http://www.microsoft.com/whdc/system/platform/firmware/PECOFF.mspx" target="new">Microsoft Portable Executable and the Common Object File Format Specification</a> documents. I believe it represents the totality of sections by their names; this is also included in Table 2.
<strong>Table 2:</strong> Section names
<table cellspacing="2" cellpadding="2" border="2">
<tbody>
<tr>
<td><font color="#008000">".text"</font></td>
<td>Code Section</td>        </tr>
<tr>
<td><font color="#008000">"CODE"</font></td>
<td>Code Section of file linked by Borland Delphi or Borland Pascal</td>        </tr>
<tr>
<td><font color="#008000">".data"</font></td>
<td>Data Section</td>        </tr>
<tr>
<td><font color="#008000">"DATA"</font></td>
<td>Data Section of file linked by Borland Delphi or Borland Pascal</td>        </tr>
<tr>
<td><font color="#008000">".rdata"</font></td>
<td>Section for Constant Data </td>        </tr>
<tr>
<td><font color="#008000">".idata"</font></td>
<td>Import Table</td>        </tr>
<tr>
<td><font color="#008000">".edata" </font></td>
<td>Export Table</td>        </tr>
<tr>
<td><font color="#008000">".tls"</font></td>
<td>TLS Table</td>        </tr>
<tr>
<td><font color="#008000">".reloc"</font></td>
<td>Relocation Information</td>        </tr>
<tr>
<td><font color="#008000">".rsrc"</font></td>
<td>Resource Information</td>        </tr>    </tbody></table>
To comprehend the section headers and also the sections, you can run the sample PE viewer. With this PE viewer, you can realize only the application of the section headers in a file image, so to observe the main significance in the Virtual Memory, you should try to load a PE file by a debugger. The next section represents the main idea of using the virtual address and size in the virtual memory by using a debugger. The last note is about <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_nt_headers_str.asp" target="new"><tt>IMAGE_NT_HEADERS</tt></a>-> <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/image_file_header_str.asp" target="new"><tt>FileHeader</tt></a>-><tt>NumberOfSections</tt>, that provides a number of sections in a PE file. Do not forget to adjust it whenever you remove or add some sections to a PE file. I am talking about section injection!
<h3>3 Debugger, Disassembler and some Useful Tools</h3>
In this part, you will become familiar with the necessary and essential equipment to develop your PE tools.
<h4>3.1 Debuggers</h4>
The first essential prerequisite to become a PE tools developer is to have enough experience with bug tracer tools. Furthermore, you should know most of the assembly instructions. To me, the Intel documents are the best references. You can obtain them from the Intel site for IA-32, and on top of that IA-64; the future belongs to IA-64 CPUs, Windows XP 64-bit, and also PE64!
<ul>
<li><a href="http://www.intel.com/design/pentium4/manuals/index_new.htm#1" target="new">IA-32 Intel Architecture Software Developer's Manuals</a> </li>
<li><a href="http://www.intel.com/software/products/compilers/docs/linux/ref/asm_lan_lx.htm#cover.htm" target="new">Intel Itanium Architecture Assembly Language Reference Guide</a> </li>
<li><a href="http://www.intel.com/cd/ids/developer/asmo-na/eng/19415.htm" target="new">The Intel Itanium Processor Developer Resource Guide</a> </li></ul>
To trace a PE file, <a href="http://en.wikipedia.org/wiki/SoftICE" target="new">SoftICE</a> by <a href="http://www.compuware.com/" target="new">Compuware Corporation</a>, I knew it also as named <a href="http://en.wikipedia.org/wiki/Numega" target="new">NuMega</a> when I was at high school, is the best <a href="http://en.wikipedia.org/wiki/Debugger" target="new">debugger</a> in the world. It implements process tracing by using the <a href="http://en.wikipedia.org/wiki/Kernel_mode" target="new">kernel mode</a> method debugging without applying Windows debugging <a href="http://en.wikipedia.org/wiki/Application_programming_interface" target="new">application programming interface</a> (API) functions. In addition, I will introduce one perfect debugger in <a href="http://en.wikipedia.org/wiki/User_mode" target="new">user mode</a> level. It utilizes the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/debugging_reference.asp" target="new">Windows debugging API</a> to trace a PE file and also attaches itself to an active <a href="http://en.wikipedia.org/wiki/Computer_process" target="new">process</a>. These <a href="http://en.wikipedia.org/wiki/Application_programming_interface" target="new">API</a> functions have been provided by Microsoft teams, inside the Windows Kernel32 library, to trace a specific process, by using Microsoft tools, or perhaps, to make your own debugger! Some of those <a href="http://en.wikipedia.org/wiki/Application_programming_interface" target="new">API</a> functions inlude:
<ul><tt>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/createthread.asp" target="new">CreateThread()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/createprocess.asp" target="new">CreateProcess()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/openprocess.asp" target="new">OpenProcess()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/debugactiveprocess.asp" target="new">DebugActiveProcess()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/getthreadcontext.asp" target="new">GetThreadContext()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/setthreadcontext.asp" target="new">SetThreadContext()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/continuedebugevent.asp" target="new">ContinueDebugEvent()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/debugbreak.asp" target="new">DebugBreak()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/readprocessmemory.asp" target="new">ReadProcessMemory()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/writeprocessmemory.asp" target="new">WriteProcessMemory()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/suspendthread.asp" target="new">SuspendThread()</a> </li>
<li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/resumethread.asp" target="new">ResumeThread()</a> </li>    </tt></ul>
<h5>3.1.1 SoftICE</h5>
It was in 1987; Frank Grossman and Jim Moskun decided to establish a company called <a href="http://en.wikipedia.org/wiki/Numega" target="new">NuMega Technologies</a> in Nashua, NH, to develop some equipment to trace and test the reliability of Microsoft Windows software programs. Now, it is a part of <a href="http://en.wikipedia.org/wiki/Compuware" target="new">Compuware Corporation</a> and its product has participated to accelerate the reliability in Windows software, and additionally in Windows driver developments. Currently, everyone knows the Compuware DriverStudio that is used to establish an environment for implementing the elaboration of a kernel driver or a system file by aiding the <a href="http://www.microsoft.com/whdc/ddk/winddk.mspx" target="new">Windows Driver Development Kit (DDK)</a>. It bypasses the involvement of DDK to implement a portable executable file of kernel level for a Windows system software developer. For us, only one instrument of DriverStudio is important, <a href="http://en.wikipedia.org/wiki/SoftICE" target="new">SoftICE</a>; this debugger can be used to trace every portable executable file, a PE file for user mode level or a PE file for kernel mode level.
<strong>Figure 1:</strong> SoftICE Window
<table cellspacing="0" cellpadding="0" border="1">
<tbody bgcolor="#000000" color="gray">
<tr>
<td><font color="#808080"><font color="#00ccff">EAX=00000000</font>EBX=7FFDD000<font color="#00ccff"> ECX=0007FFB0 EDX=7C90EB94</font> ESI=FFFFFFFF EDI=7C919738 <font color="#00ccff">EBP=0007FFF0 ESP=0007FFC4 EIP=010119E0</font> o d i s <font color="#00ccff">z </font>a <font color="#00ccff">p</font> c
                CS=0008 DS=0023 SS=0010 ES=0023 FS=0030 GS=0000</font> <font color="#00ccff">SS:0007FFC4=87C816D4F</font></td>            </tr>
<tr>
<td><font color="#808080">0023:01013000 00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 ................ 0023:01013010 01 00 00 00 20 00 00 00-0A 00 00 00 0A 00 00 00 ................ 0023:01013020 20 00 00 00 00 00 00 00-53 63 69 43 61 6C 63 00 ........SciCalc. 0023:01013030 00 00 00 00 00 00 00 00-62 61 63 6B 67 72 6F 75 ........backgrou 0023:01013040 6E 64 00 00 00 00 00 00-2E 00 00 00 00 00 00 00 nd..............</font></td>            </tr>
<tr>
<td><font color="#808080">0010:0007FFC4 4F 6D 81 7C 38 07 91 7C-FF FF FF FF 00 90 FD 7F Om |8 b.| . 0010:0007FFD4 ED A6 54 80 C8 FF 07 00-E8 B4 F5 81 FF FF FF FF T . 0010:0007FFE4 F3 99 83 7C 58 6D 81 7C-00 00 00 00 00 00 00 00 Xm |........ 0010:0007FFF4 00 00 00 00 E0 19 01 01-00 00 00 00 00 00 00 00 .... ....</font></td>            </tr>
<tr>
<td><font color="#808080"><font color="#00ccff">010119E0 PUSH EBP</font> 010119E1 MOV EBP,ESP 010119E3 PUSH -1 010119E5 PUSH 01001570 010119EA PUSH 01011D60 010119EF MOV EAX,DWORD PTR FS:[0] 010119F5 PUSH EAX 010119F6 MOV DWORD PTR FS:[0],ESP 010119FD ADD ESP,-68 01011A00 PUSH EBX 01011A01 PUSH ESI 01011A02 PUSH EDI 01011A03 MOV DWORD PTR SS:[EBP-18],ESP 01011A06 MOV DWORD PTR SS:[EBP-4],0</font></td>            </tr>
<tr>
<td><font color="#808080">:_</font><font color="#808080">
                
                
                </font></td>            </tr>        </tbody>    </table>    
<h5>3.1.2 OllyDbg</h5>
It was about four years ago that I first saw this debugger by chance. For me, it was the best choice; I was not wealthy enough to purchase SoftICE, and at that time, SoftICE only had good functions for <a href="http://en.wikipedia.org/wiki/DOS" target="new">DOS</a>, <a href="http://en.wikipedia.org/wiki/Windows_98" target="new">Windows 98</a>, and <a href="http://en.wikipedia.org/wiki/Windows_2000" target="new">Windows 2000</a>. I found that this debugger supported all kinds of Windows versions. Therefore, I started to learn it very fast, and now it is my favorite debugger for the Windows OS. It is a debugger that can be used to trace all kinds of portable executable files except a <a href="http://en.wikipedia.org/wiki/Common_Language_Infrastructure" target="new">Common Language Infrastructure (CLI)</a> file format in user mode level, by using the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/debugging_reference.asp" target="new">Windows debugging API</a>. <strong>Oleh Yuschuk</strong>, the author, is one of worthiest software developers I have seen in my life. He is a Ukrainian who now lives in Germany. I should mention here that his debugger is the best choice for hacker and cracker parties around the world! It is freeware! You can try it from the <a href="http://www.ollydbg.de/" target="new">OllyDbg Homepage</a>.    
    <a name="more"><font color="#000000"> </font>
<strong>Figure 2:</strong> OllyDbg CPU Window
<img height="452" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=SCREENSHOT_JPG&ds=20060302" width="500" alt="" />
    (
<h5>3.1.3 Which parts are important in a debugger interface?</h5>
I have introduced two debuggers without talking about how you can employ them, and also which parts you should pay attention to. Regarding using debuggers, I refer you to their instructions in help documents. However, I want to explain briefly the important parts of a debugger; of course, I am talking about low-level debuggers, or in other words, machine-language debuggers of the x86 CPU families.
All of low-level debuggers consist of the following subdivisions:
<ol>
<li>Registers viewer.
<table cellspacing="2" cellpadding="2" border="2">
<tbody>
<tr>
<td align="center"><font color="#808080">EAX</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">ECX</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">EDX</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">EBX</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">ESP</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">EBP</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">ESI</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">EDI</font></td>                </tr>
<tr>
<td align="center"><font color="#808080">EIP</font></td>                </tr>
<tr>
<td>
<p align="center"><font color="#808080">o</font><font color="#808080"> d t s z a p c</font>                    </td>                </tr>            </tbody>        </table>        </li>
<li>Disassembler or Code viewer.
<table cellspacing="2" cellpadding="2" border="2">
<tbody>
<tr>
<td>
<pre>010119E0 PUSH EBP010119E1 MOV EBP,ESP010119E3 PUSH -1010119E5 PUSH 01001570010119EA PUSH 01011D60010119EF MOV EAX,DWORD PTR FS:[0]010119F5 PUSH EAX010119F6 MOV DWORD PTR FS:[0],ESP010119FD ADD ESP,-6801011A00 PUSH EBX01011A01 PUSH ESI01011A02 PUSH EDI01011A03 MOV DWORD PTR SS:[EBP-18],ESP01011A06 MOV DWORD PTR SS:[EBP-4],0</pre>                    </td>                </tr>            </tbody>        </table>        </li>
<li>Memory watcher.
<table cellspacing="0" cellpadding="0" width="560" border="1">
<tbody>
<tr>
<td><font color="#808080">0023:01013000 00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 ................ 0023:01013010 01 00 00 00 20 00 00 00-0A 00 00 00 0A 00 00 00 ................ 0023:01013020 20 00 00 00 00 00 00 00-53 63 69 43 61 6C 63 00 ........SciCalc. 0023:01013030 00 00 00 00 00 00 00 00-62 61 63 6B 67 72 6F 75 ........backgrou 0023:01013040 6E 64 00 00 00 00 00 00-2E 00 00 00 00 00 00 00 nd..............</font></td>                </tr>            </tbody>        </table>
         </li>
<li>Stack viewer.
<table cellspacing="0" cellpadding="0" width="560" border="1">
<tbody>
<tr>
<td><font color="#808080">0010:0007FFC4 4F 6D 81 7C 38 07 91 7C-FF FF FF FF 00 90 FD 7F Om |8 b.| . 0010:0007FFD4 ED A6 54 80 C8 FF 07 00-E8 B4 F5 81 FF FF FF FF T . 0010:0007FFE4 F3 99 83 7C 58 6D 81 7C-00 00 00 00 00 00 00 00 Xm |........ 0010:0007FFF4 00 00 00 00 E0 19 01 01-00 00 00 00 00 00 00 00 .... ....</font></td>                </tr>            </tbody>        </table>        </li>
<li>Command line, command buttons, or shortcut keys to follow the debugging process.
<table cellspacing="0" cellpadding="0" width="560" border="1">
<tbody>
<tr>
<td align="center">Command</td>
<td align="center">SoftICE</td>
<td align="center">OllyDbg</td>                </tr>
<tr>
<td align="center">Run</td>
<td align="center">F5</td>
<td align="center">F9</td>                </tr>
<tr>
<td align="center">Step Into</td>
<td align="center">F11</td>
<td align="center">F7</td>                </tr>
<tr>
<td align="center">Step Over</td>
<td align="center">F10</td>
<td align="center">F8</td>                </tr>
<tr>
<td align="center">Set Break Point</td>
<td align="center">F8</td>
<td align="center">F2</td>                </tr>            </tbody>        </table>                </li>    </ol>
You can compare Figures 1 and 2 to distinguish the difference between SoftICE and OllyDbg. When you want to trace a PE file, you should mostly consider these five subdivisions. Furthermore, every debugger comprises of some other useful parts; you should discover them by yourself.
<h4>3.2 Disassembler</h4>
You can consider OllyDbg and SoftICE to be excellent disassemblers, but I also want to introduce another disassembler tool that is famous in the reverse engineering world.
<h5>3.2.1 Proview disassembler</h5>
<a href="http://community.reverse-engineering.net/viewforum.php?f=50&sid=a77c210bc1030dd395452bb7e1f67439" target="new">Proview</a> or PVDasm is an admirable disassembler by the Reverse-Engineering-Community; it is still under development and bug fixing. You can find its disassmbler source engine and employ it to create your own disassembler.
<h5>3.2.2 W32Dasm</h5>
<a href="http://www.softpedia.com/get/Programming/Debuggers-Decompilers-Dissasemblers/WDASM.shtml" target="new">W32DASM</a> can disassemble both 16- and 32-bit executable file formats. In addition to its disassembling ability, you can employ it to analyze import, export, and resource data directories data.
<h5>3.2.3 IDA Pro</h5>
All reverse-engineering experts know that IDA Pro can be used to investigate, not only x86 instructions, but that of various kinds of CPU types like AVR, PIC, and so forth. It can illustrate the assembly source of a portable executable file by using colored graphics and tables, and is very useful for any newbie in this area. Furthermore, it has the capability to trace an executable file inside the user mode level in the same way as OllyDbg.
<h4>3.3 Some Useful Tools</h4>
A good PE tools developer is conversant with the tools that save his time, so I recommend that you select some appropriate instruments to investigate the base information under a portable executable file.
<h5>3.3.1 LordPE</h5>
LordPE by <a href="http://scifi.pages.at/yoda9k/aboutme.htm" target="new">y0da</a> is still the first choice to retrieve PE file information with the possibility to modify them.
<img height="206" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=LORDPE_GIF&ds=20060302" width="441" alt="" />
<h5>3.3.2 PEiD</h5>
<a href="http://peid.has.it/" target="new">PE iDentifier</a> is valuable to identify the type of compilers, packers, and cryptors of PE files. As of now, it can detect more than 500 different signature types of PE files.
<img height="166" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=PEID_GIF&ds=20060302" width="296" alt="" />
<h5>3.3.3 Resource Hacker</h5>
<a href="http://www.angusj.com/resourcehacker/" target="new">Resource Hacker </a>can be employed to modify resource directory information; icon, menu, version info, string table, and so on.
<img height="141" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=RESOURCEHACKER_GIF&ds=20060302" width="191" alt="" />
<h5>3.3.4 WinHex</h5>
<a href="http://www.winhex.com/winhex/index-m.html" target="new">WinHex</a>, it is clear what you can do with this tool.
<img height="230" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=WINHEX_GIF&ds=20060302" width="329" alt="" />
<h5>3.3.5 CFF Explorer</h5>
Eventually, CFF Explorer by Ntoskrnl is what you want to have as a PE Utility tool in your arsenal; it supports PE32/64, PE rebuild included <a href="http://en.wikipedia.org/wiki/Common_Language_Infrastructure" target="new">Common Language Infrastructure (CLI)</a> file. In other words, the <a href="http://en.wikipedia.org/wiki/Microsoft_.NET" target="new">.NET file</a>, a resource modifier, and much more facilities which can not be found in others. Just try to discover every unimaginable option by hand.
<img height="217" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=CFFEXPLORER_GIF&ds=20060302" width="301" alt="" />
<h3>4 Add a New Section and Change the OEP</h3>
You are ready to do the first step of making your project. I have provided a library to add a new section and rebuild the portable executable file. Before starting, I wnat you to get familiar with the headers of a PE file, by using <a href="http://www.ollydbg.de/" target="new">OllyDbg</a>. You should first open a PE file; that pops up a menu, <strong>View->Executable file</strong>. Again, you get a popup menu: <strong>Special->PE header</strong>. You will observe a scene similar to Figure 3. Now, come to the Main Menu <strong>View->Memory</strong>, and try to distinguish the sections inside the <strong>Memory map</strong> window.
<h4>Figure 3</h4>
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td><font color="#808080">
<pre>00000000000000020000000400000006000000080000000A0000000C0000000E00000010000000120000001400000016000000180000001A0000001C0000001D0000001E0000001F000000200000002100000022000000230000002400000025000000260000002700000028000000290000002A0000002B0000002C0000002D0000002E0000002F000000300000003100000032000000330000003400000035000000360000003700000038000000390000003A0000003B0000003C</pre>                </font></td>
<td>
<pre> 4D 5A 9000 0300 0000 0400 0000 FFFF 0000 B800 0000 0000 0000 4000 0000 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 F0000000</pre>                </td>
<td>
<pre> ASCII <font color="#008000">"MZ"</font> DW 0090 DW 0003 DW 0000 DW 0004 DW 0000 DW FFFF DW 0000 DW 00B8 DW 0000 DW 0000 DW 0000 DW 0040 DW 0000 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DB 00 DD <font color="#ff0000">000000F0</font></pre>                </td>
<td>
<pre> DOS EXE Signature DOS_PartPag = 90 (144.) DOS_PageCnt = 3 DOS_ReloCnt = 0 DOS_HdrSize = 4 DOS_MinMem = 0 DOS_MaxMem = FFFF (65535.) DOS_ReloSS = 0 DOS_ExeSP = B8 DOS_ChkSum = 0 DOS_ExeIP = 0 DOS_ReloCS = 0 DOS_TablOff = 40 DOS_Overlay = 0 Offset to PE signature</pre>                </td>            </tr>        </tbody>    </table>    
    <a name="more"><font color="#000000"> </font>
I want to explain how you can plainly change the Offset of Entry Point (OEP) in your sample file, <em>CALC.EXE</em> of Windows XP. First, by using a PE Tool, and also using your PE Viewer, you find OEP, <tt>0x00012475</tt>, and Image Base, <tt>0x01000000</tt>. This value of OEP is the Relative Virtual Address, so the Image Base value is used to convert it to the Virtual Address.
<table cellspacing="0" cellpadding="0" width="450" border="1">
<tbody>
<tr>
<td>
<strong>Virtual_Address = Image_Base + Relative_Virtual_Address</strong>                </td>            </tr>        </tbody>    </table>
<pre>DWORD OEP_RVA = image_nt_headers->   OptionalHeader.AddressOfEntryPoint ;<span class="codeComment">// OEP_RVA = 0x00012475</span>DWORD OEP_VA = image_nt_headers->   OptionalHeader.ImageBase + OEP_RVA ;<span class="codeComment">// OEP_VA = 0x01000000 + 0x00012475 = 0x01012475</span></pre>
<h4>PE Maker: Step 1</h4>
Download pemaker1.zip and test1.zip from the files at the end of this article.
<tt>DynLoader()</tt>, in <em>loader.cpp</em>, is reserved for the data of the new section&mdash;in other words, the <strong>Loader</strong>.
<h4>DynLoader Step 1</h4>
<pre><span class="codeKeyword">__stdcall</span> <span class="codeKeyword">void</span> DynLoader(){_asm{<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_MAGIC)<span class="codeComment">//----------------------------------</span>    MOV EAX,01012475h <span class="codeComment">// << Original OEP</span>    JMP EAX<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_END_MAGIC)<span class="codeComment">//----------------------------------</span>}}</pre>
Unfortunately, this source can only be applied for the sample test file. You should complete it by saving the value of the original OEP in the new section, and use it to reach the real OEP. I have accomplished it in Step 2 (Section 5).
<h4>4.1 Retrieve and Rebuild PE file</h4>
I have made a simple class library to recover PE information and to use it in a new PE file.
<h4>CPELibrary Class Step 1</h4>
<pre><span class="codeComment">//----------------------------------------------------------------</span><span class="codeKeyword">class</span> CPELibrary{<span class="codeKeyword">private</span>:    <span class="codeComment">//-----------------------------------------</span>    PCHAR                   pMem;    DWORD                   dwFileSize;    <span class="codeComment">//-----------------------------------------</span><span class="codeKeyword">protected</span>:    <span class="codeComment">//-----------------------------------------</span>    PIMAGE_DOS_HEADER       image_dos_header;    PCHAR                   pDosStub;    DWORD                   dwDosStubSize, dwDosStubOffset;    PIMAGE_NT_HEADERS       image_nt_headers;    PIMAGE_SECTION_HEADER   image_section_header[MAX_SECTION_NUM];    PCHAR                   image_section[MAX_SECTION_NUM];    <span class="codeComment">//-----------------------------------------</span><span class="codeKeyword">protected</span>:    <span class="codeComment">//-----------------------------------------</span>    DWORD PEAlign(DWORD dwTarNum,DWORD dwAlignTo);    <span class="codeKeyword">void</span> AlignmentSections();    <span class="codeComment">//-----------------------------------------</span>    DWORD Offset2RVA(DWORD dwRO);    DWORD RVA2Offset(DWORD dwRVA);    <span class="codeComment">//-----------------------------------------</span>    PIMAGE_SECTION_HEADER ImageRVA2Section(DWORD dwRVA);    PIMAGE_SECTION_HEADER ImageOffset2Section(DWORD dwRO);    <span class="codeComment">//-----------------------------------------</span>    DWORD ImageOffset2SectionNum(DWORD dwRVA);    PIMAGE_SECTION_HEADER AddNewSection(<span class="codeKeyword">char</span>* szName,DWORD dwSize);    <span class="codeComment">//-----------------------------------------</span><span class="codeKeyword">public</span>:    <span class="codeComment">//-----------------------------------------</span>    CPELibrary();    ~CPELibrary();    <span class="codeComment">//-----------------------------------------</span>    <span class="codeKeyword">void</span> OpenFile(<span class="codeKeyword">char</span>* FileName);    <span class="codeKeyword">void</span> SaveFile(<span class="codeKeyword">char</span>* FileName);    <span class="codeComment">//-----------------------------------------</span>};</pre>
In Table 1, the usage of <tt>image_dos_header</tt>, <tt>pDosStub</tt>, <tt>image_nt_headers</tt>, <tt>image_section_header</tt> [<tt>MAX_SECTION_NUM</tt>], and <tt>image_section</tt>[<tt>MAX_SECTION_NUM</tt>] is clear. You use <tt>OpenFile()</tt> and <tt>SaveFile()</tt> to retrieve and rebuild a PE file. Furthermore, <tt>AddNewSection()</tt> is employed to create the new section, the important step.    </a>
<h4>4.2 Create data for the new section</h4>
<a name="more"><font color="#000000"> </font></a><a href="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=LINKTIP1_GIF&ds=20060302" target="_blank">Full Size Image</a>)
You can comprehend the difference between incremental link and no-incremental link by looking at the following picture:    <img height="130" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=INCREMENTAL_LINK_GIF&ds=20060302" width="415" alt="" />
To acquire the virtual address of <tt>DynLoader()</tt>, you obtain the virtual address of <tt>JMP pemaker.DynLoader</tt> in the incremental link, but by no-incremental link, the real virtual address is gained by the following code:
<pre>DWORD dwVA= (DWORD) DynLoader;</pre>
This setting is more critical in the incremental link when you try to find the beginning and ending of the <strong>Loader</strong>, <tt>DynLoader()</tt>, by <tt>CPECryptor::ReturnToBytePtr()</tt>:
<pre><span class="codeKeyword">void</span>* CPECryptor::ReturnToBytePtr(<span class="codeKeyword">void</span>* FuncName, DWORD findstr){    <span class="codeKeyword">void</span>* tmpd;    __asm   {        mov eax, FuncName        jmp dfhjg:    inc eaxdf:     mov ebx, [eax]        cmp ebx, findstr        jnz hjg        mov tmpd, eax    }    <span class="codeKeyword">return</span> tmpd;}</pre>    
In <em>pecrypt.cpp</em>, I have represented another class, <tt>CPECryptor</tt>, to comprise the data of the new section. Nevertheless, the data of the new section is created by <tt>DynLoader()</tt> in <em>loader.cpp</em>, DynLoader Step 1. You use the <tt>CPECryptor</tt> class to enter this data in to the new section, and also some other stuff.
<h4>CPECryptor Class Step 1</h4>
<pre><span class="codeComment">//----------------------------------------------------------------</span><span class="codeKeyword">class</span> CPECryptor: <span class="codeKeyword">public</span> CPELibrary{<span class="codeKeyword">private</span>:    <span class="codeComment">//----------------------------------------</span>    PCHAR pNewSection;    <span class="codeComment">//----------------------------------------</span>    DWORD GetFunctionVA(<span class="codeKeyword">void</span>* FuncName);    <span class="codeKeyword">void</span>* ReturnToBytePtr(<span class="codeKeyword">void</span>* FuncName, DWORD findstr);    <span class="codeComment">//----------------------------------------</span><span class="codeKeyword">protected</span>:    <span class="codeComment">//----------------------------------------</span><span class="codeKeyword">public</span>:    <span class="codeComment">//----------------------------------------</span>    <span class="codeKeyword">void</span> CryptFile(<span class="codeKeyword">int</span>(__cdecl *callback) (<span class="codeKeyword">unsigned</span> <span class="codeKeyword">int</span>,                                           <span class="codeKeyword">unsigned</span> <span class="codeKeyword">int</span>));    <span class="codeComment">//----------------------------------------</span>};<span class="codeComment">//----------------------------------------------------------------</span></pre>
<h4>4.3 Some notes regarding creating a new PE file</h4>
<ul>
<li>Align the <tt>VirtualAddress</tt> and the <tt>VirtualSize</tt> of each section by <tt>SectionAlignment</tt>:
<pre>image_section_header[i]->VirtualAddress=    PEAlign(image_section_header[i]->VirtualAddress,    image_nt_headers->OptionalHeader.SectionAlignment);image_section_header[i]->Misc.VirtualSize=    PEAlign(image_section_header[i]->Misc.VirtualSize,    image_nt_headers->OptionalHeader.SectionAlignment);</pre>        </li>
<li>Align the <tt>PointerToRawData</tt> and the <tt>SizeOfRawData</tt> of each section by <tt>FileAlignment</tt>:
<pre>image_section_header[i]->PointerToRawData =    PEAlign(image_section_header[i]->PointerToRawData,            image_nt_headers->OptionalHeader.FileAlignment);image_section_header[i]->SizeOfRawData =    PEAlign(image_section_header[i]->SizeOfRawData,            image_nt_headers->OptionalHeader.FileAlignment);</pre>        </li>
<li>Correct the <tt>SizeofImage</tt> by the virtual size and the virtual address of the last section:
<pre>image_nt_headers->OptionalHeader.SizeOfImage =   image_section_header[LastSection]->VirtualAddress +   image_section_header[LastSection]->Misc.VirtualSize;</pre>        </li>
<li>Set the Bound Import Directory header to zero because this directory is not very important to execute a PE file:
<pre>image_nt_headers->   OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_BOUND_IMPORT].  VirtualAddress = 0;image_nt_headers->   OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_BOUND_                                IMPORT].Size = 0;</pre>        </li>    </ul>
<h4>4.4 Some notes regarding linking this VC Project</h4>
<ul>
<li>Set <em>Linker->General->Enable Incremental Linking</em> to <strong>No (/INCREMENTAL:NO)</strong>.
        
        <img height="125" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=LINKTIP1_GIF&ds=20060302" width="500" alt="" />
        (</li>    </ul>
<h3>5 Store Important Data and Reach the Original OEP</h3>
Right now, we save the Original OEP and also the Image Base in order to reach to the virtual address of OEP. I have reserved a free space at the end of <tt>DynLoader()</tt> to store them, DynLoader Step 2.
<h4>PE Maker - Step 2</h4>
Download the pemaker2.zip source files from the end of the article.
<h4>DynLoader Step 2</h4>
<pre><span class="codeKeyword">__stdcall</span> <span class="codeKeyword">void</span> DynLoader(){_asm{<span class="codeComment">//------------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_MAGIC)<span class="codeComment">//------------------------------------</span>Main_0:    PUSHAD    <span class="codeComment">// get base ebp</span>    CALL Main_1Main_1:    POP EBP    SUB EBP,OFFSET Main_1    MOV EAX,DWORD PTR [EBP+_RO_dwImageBase]    ADD EAX,DWORD PTR [EBP+_RO_dwOrgEntryPoint]    PUSH EAX    RETN <span class="codeComment">// >> JMP to Original OEP</span><span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_DATA1)<span class="codeComment">//----------------------------------<font color="#ff0000"></font><span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_END_MAGIC)<span class="codeComment">//----------------------------------</span>}}</span>_RO_dwImageBase:                DWORD_TYPE(0xCCCCCCCC)_RO_dwOrgEntryPoint:            DWORD_TYPE(0xCCCCCCCC)</pre>
The new function, <tt>CPECryptor::CopyData1()</tt>, will implement the copy of the Image Base value and the Offset of Entry Point value into 8 bytes of free space in the loader.
<h4>5.1 Restore the first register's context</h4>
It is important to recover the Original Context of the thread. You have not yet done it in the DynLoader Step 2 source code. You can modify the source of <tt>DynLoader()</tt> to repossess the first Context.
<pre><span class="codeKeyword">__stdcall</span> <span class="codeKeyword">void</span> DynLoader(){_asm{<span class="codeComment">//------------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_MAGIC)<span class="codeComment">//------------------------------------</span>Main_0:    <font color="#ff0000">PUSHAD<span class="codeComment">// Save the registers context in stack</span>    CALL Main_1Main_1:    POP EBP<span class="codeComment">// Get Base EBP</span>    SUB EBP,OFFSET Main_1    MOV EAX,DWORD PTR [EBP+_RO_dwImageBase]    ADD EAX,DWORD PTR [EBP+_RO_dwOrgEntryPoint]    MOV DWORD PTR [ESP+1Ch],EAX <span class="codeComment">// pStack.Eax <- EAX</span>    <font color="#ff0000">POPAD <span class="codeComment">// Restore the first registers context from stack</span>    PUSH EAX    XOR  EAX, EAX    RETN <span class="codeComment">// >> JMP to Original OEP</span><span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_DATA1)<span class="codeComment">//----------------------------------</span>_RO_dwImageBase:                DWORD_TYPE(0xCCCCCCCC)_RO_dwOrgEntryPoint:            DWORD_TYPE(0xCCCCCCCC)<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_END_MAGIC)<span class="codeComment">//----------------------------------</span>}}</font></font></pre>
<h4>5.2 Restore the original stack</h4>
You also can recover the original stack by setting the value of the beginning stack + <tt>0x34</tt> to the Original OEP, but it is not very important. Nevertheless, in the following code, I have accomplished the loader code by a simple trick to reach the OEP in addition to redecorating the stack. You can observe the implementation by tracing using <a href="http://www.ollydbg.de/" target="new">OllyDbg</a> or SoftICE.
<pre><span class="codeKeyword">__stdcall</span> <span class="codeKeyword">void</span> DynLoader(){_asm{<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_MAGIC)<span class="codeComment">//----------------------------------</span>Main_0:    PUSHAD    <span class="codeComment">// Save the registers context in stack</span>    CALL Main_1Main_1:    POP EBP    SUB EBP,OFFSET Main_1    MOV EAX,DWORD PTR [EBP+_RO_dwImageBase]    ADD EAX,DWORD PTR [EBP+_RO_dwOrgEntryPoint]    MOV DWORD PTR [ESP+54h],EAX    <span class="codeComment">// pStack.Eip <- EAX</span>    POPAD    <span class="codeComment">// Restore the first registers context from stack</span>    CALL _OEP_Jump    DWORD_TYPE(0xCCCCCCCC)_OEP_Jump:    PUSH EBP    MOV EBP,ESP    MOV EAX,DWORD PTR [ESP+3Ch]    <span class="codeComment">// EAX <- pStack.Eip</span>    MOV DWORD PTR [ESP+4h],EAX     <span class="codeComment">// _OEP_Jump RETURN pointer <- EAX</span>    XOR EAX,EAX    LEAVE    RETN<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_DATA1)<span class="codeComment">//----------------------------------</span>_RO_dwImageBase:                DWORD_TYPE(0xCCCCCCCC)_RO_dwOrgEntryPoint:            DWORD_TYPE(0xCCCCCCCC)<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_END_MAGIC)<span class="codeComment">//----------------------------------</span>}}</pre>
<h4>5.3 Approach OEP by structured exception handling</h4>
<a name="more"><font color="#000000"> </font></a><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/vccelng/htm/key_s-z_4.asp" target="new"><tt>try-except</tt> statement</a> in C++ clarifies the operation of <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/about_structured_exception_handling.asp" target="new">structured exception handling</a>. Besides the assembly code of this code, it elucidates the structured exception handler installation, the raise of an exception, and the exception handler function.
An exception is generated when a program falls into a fault code execution and an error happens, so in such a special condition, the program immediately jumps to a function called the exception handler from exception handler list of the Thread Information Block.
The next example of a 
<pre><span class="codeKeyword">#include</span> "stdafx.h"<span class="codeKeyword">#include</span> "windows.h"<span class="codeKeyword">void</span> RAISE_AN_EXCEPTION(){_asm{    INT 3    INT 3    INT 3    INT 3}}<span class="codeKeyword">int</span> _tmain(<span class="codeKeyword">int</span> argc, _TCHAR* argv[]){    <span class="codeKeyword">__try</span>    {        <span class="codeKeyword">__try</span>{            printf("1: Raise an Exception\n");            RAISE_AN_EXCEPTION();        }        <span class="codeKeyword">__finally</span>        {            printf("2: In Finally\n");        }    }    <span class="codeKeyword">__except</span>( printf("3: In Filter\n"), EXCEPTION_EXECUTE_HANDLER )    {        printf("4: In Exception Handler\n");    }    <span class="codeKeyword">return</span> 0;}</pre>
<pre><font color="#000000"><strong>; main()</strong></font><font color="#808080">00401000: PUSH EBP00401001: MOV EBP,ESP00401003: PUSH -100401005: PUSH 00407160<font color="#000000"><strong>; <span class="codeKeyword">__try</span> {</strong></font><font color="#008000">; the structured exception handler (SEH) installation </font><font color="#0000ff">0040100A: PUSH _except_handler30040100F: MOV EAX,DWORD PTR FS:[0]00401015: PUSH EAX00401016: MOV DWORD PTR FS:[0],ESP</font>0040101D: SUB ESP,800401020: PUSH EBX00401021: PUSH ESI00401022: PUSH EDI00401023: MOV DWORD PTR SS:[EBP-18],ESP<font color="#000000"><strong>;     <span class="codeKeyword">__try</span> {</strong></font>00401026: XOR ESI,ESI00401028: MOV DWORD PTR SS:[EBP-4],ESI0040102B: MOV DWORD PTR SS:[EBP-4],100401032: PUSH OFFSET <font color="#a52a2a">"1: Raise an Exception"</font>00401037: CALL printf0040103C: ADD ESP,4<font color="#008000">; the raise a exception, INT 3 exception</font>; RAISE_AN_EXCEPTION()<font color="#0000ff">0040103F: INT300401040: INT300401041: INT300401042: INT3</font><font color="#000000"><strong>;     } <span class="codeKeyword">__finally</span> {</strong></font>00401043: MOV DWORD PTR SS:[EBP-4],ESI00401046: CALL 0040104D0040104B: JMP 004010800040104D: PUSH OFFSET <font color="#a52a2a">"2: In Finally"</font>00401052: CALL printf00401057: ADD ESP,40040105A: RETN<font color="#000000"><strong>;     }</strong></font><font color="#000000"><strong>; }</strong></font><font color="#000000"><strong>; <span class="codeKeyword">__except</span>( </strong></font>0040105B: JMP 004010800040105D: PUSH OFFSET <font color="#a52a2a">"3: In Filter"</font>00401062: CALL printf00401067: ADD ESP,40040106A: MOV EAX,1 ; EXCEPTION_EXECUTE_HANDLER = 10040106F: RETN<font color="#000000"><strong>;     , EXCEPTION_EXECUTE_HANDLER )</strong></font><font color="#000000"><strong>; {</strong></font><font color="#008000">; the exception handler funtion</font><font color="#0000ff">00401070: MOV ESP,DWORD PTR SS:[EBP-18]00401073: PUSH OFFSET <font color="#a52a2a">"4: In Exception Handler"</font>00401078: CALL printf0040107D: ADD ESP,4</font><font color="#000000"><strong>; }</strong></font>00401080: MOV DWORD PTR SS:[EBP-4],-10040108C: XOR EAX,EAX<font color="#008000">; restore previous SEH</font><font color="#0000ff">0040108E: MOV ECX,DWORD PTR SS:[EBP-10]00401091: MOV DWORD PTR FS:[0],ECX</font>00401098: POP EDI00401099: POP ESI0040109A: POP EBX0040109B: MOV ESP,EBP0040109D: POP EBP0040109E: RETN</font></pre>
Make a Win32 console project, and link and run the preceding C++ code, to perceive the result:
<table cellspacing="0" cellpadding="0" width="400" border="1">
<tbody bgcolor="#000000" color="gray">
<tr>
<td><font color="#ffffff"><strong>1: Raise an Exception
                3: In Filter
                2: In Finally
                4: In Exception Handler
                _
                
                
                
                </strong></font></td>            </tr>        </tbody>    </table>    
This program runs the exception expression, <tt>printf("3: In Filter\n");</tt>, when an exception happens&mdash;in this example, the <tt>INT 3</tt> exception. You can employ other kinds of exception too. In <a href="http://www.ollydbg.de/" target="new">OllyDbg</a>, <strong>Debugging options->Exceptions</strong>, you can see a short list of different types of exceptions.
<img height="200" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=OLLYDBG_EXCEPTIONS_GIF&ds=20060302" width="280" alt="" />
<h5>5.3.1 Implement Exception Handler</h5>
You want to construct a structured exception handler to reach OEP. Now, I think you have distinguished the SEH installation, the exception raise, and the exception expression filter, by foregoing the assembly code. To establish your exception handler approach, you need to comprise the following codes:
<ul>
<li><strong>SEH installation</strong>:
<pre><font color="#808080">LEA EAX,[EBP+_except_handler1_OEP_Jump]PUSH EAXPUSH DWORD PTR FS:[0]MOV DWORD PTR FS:[0],ESP</font></pre>        </li>
<li><strong>An Exception Raise</strong>:
<pre><font color="#808080">INT 3</font></pre>        </li>
<li><strong>Exception handler expression filter</strong>:
<pre><font color="#808080">_except_handler1_OEP_Jump:   PUSH EBP   MOV EBP,ESP   ...   <span class="codeComment">// EXCEPTION_CONTINUE_SEARCH = 0</span>   MOV EAX, EXCEPTION_CONTINUE_SEARCH   LEAVE   RETN</font></pre>        </li>    </ul>
So, you yearn to make the ensuing C++ code in assembly language to inaugurate your engine to approach the Offset of the Entry Point by SEH.
<pre><span class="codeKeyword">__try</span>    <span class="codeComment">// SEH installation</span>{    __asm    {        INT 3    <span class="codeComment">// An Exception Raise</span>    }}<span class="codeKeyword">__except</span>( ..., EXCEPTION_CONTINUE_SEARCH ){}<span class="codeComment">// Exception handler expression filter</span></pre>
In assembly code...
<pre><font color="#808080">    <font color="#008000">; ----------------------------------------------------    ; the structured exception handler (SEH) installation    <font color="#000000"><strong>; <span class="codeKeyword">__try</span> {</strong></font></font>    LEA EAX,[EBP+_except_handler1_OEP_Jump]    PUSH EAX    PUSH DWORD PTR FS:[0]    MOV DWORD PTR FS:[0],ESP    <font color="#008000">; ----------------------------------------------------    ; the raise a INT 3 exception</font>    INT 3    INT 3    INT 3    INT 3    <font color="#000000"><strong>; }    ; <span class="codeKeyword">__except</span>( ... </strong></font>    <font color="#008000">; ----------------------------------------------------    ; exception handler expression filter</font>_except_handler1_OEP_Jump:    PUSH EBP    MOV EBP,ESP    ...    MOV EAX, EXCEPTION_CONTINUE_SEARCH ; EXCEPTION_CONTINUE_SEARCH = 0    LEAVE    RETN    <font color="#000000"><strong>; , EXCEPTION_CONTINUE_SEARCH ) { }</strong></font></font></pre>
The exception value, <tt>__except(..., Value)</tt>, determines how the exception is handled. It can have three values: 1, 0, -1. To understand them, refer to the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/vccelng/htm/key_s-z_4.asp" target="new"><tt>try-except</tt> statement</a> description in the MSDN library. You set it to <tt>EXCEPTION_CONTINUE_SEARCH (0)</tt>, not to run the exception handler function; therefore, by this value, the exception is not recognized. It is simply ignored, and the thread continues its code execution.
<h4>How the SEH installation is implemented</h4>
As you perceived from the illustrated code, the SEH installation is done by the FS segment register. Microsoft Windows 32 bit uses the FS segment register as a pointer to the data block of the main thread. The first <font color="#0000ff">0x1C</font> bytes comprise the information of the Thread Information Block (TIB). Therefore, <tt>FS:[00h]</tt> refers to <tt>ExceptionList</tt> of the main thread, Table 3. In your code, you have pushed the pointer to <tt>_except_handler1_OEP_Jump</tt> in the stack and changed the value of <tt>ExceptionList</tt>, <tt>FS:[00h]</tt>, to the beginning of the stack, <tt>ESP</tt>.
<h4>Thread Information Block (TIB)</h4>
<pre><span class="codeKeyword">typedef</span> <span class="codeKeyword">struct</span> _NT_TIB32 {   DWORD ExceptionList;   DWORD StackBase;   DWORD StackLimit;   DWORD SubSystemTib;   <span class="codeKeyword">union</span> {      DWORD FiberData;      DWORD Version;   };   DWORD ArbitraryUserPointer;   DWORD Self;} NT_TIB32, *PNT_TIB32;</pre>
<h4>Table 3: FS segment register and Thread Information Block</h4>
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[00h]</font></td>
<td align="center">ExceptionList</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[04h]</font></td>
<td align="center">StackBase</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[08h]</font></td>
<td align="center">StackLimit</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[0Ch]</font></td>
<td align="center">SubSystemTib</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[10h]</font></td>
<td align="center">FiberData / Version</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[14h]</font></td>
<td align="center">ArbitraryUserPointer</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">DWORD PTR FS:[18h]</font></td>
<td align="center">Self</td>            </tr>        </tbody>    </table>
<h5>5.3.2 Attain OEP by adjusting the Thread Context</h5>
In this part, you effectuate your performance by accomplishing the OEP approach. You change the Context of the thread and ignore every simple exception handling, and let the thread continue the execution, but in the original OEP!    
    <a name="more"><font color="#000000"> </font>
When an exception happens, the context of the processor during the time of the exception is saved in the stack. Through 
<pre>MOV EAX, ContextRecordMOV EDI, dwOEP                   ; EAX <- dwOEPMOV DWORD PTR DS:[EAX+0B8h], EDI ; pContext.Eip <- EAX</pre>
<h4>Win32 Thread Context structure</h4>
<pre><span class="codeKeyword">#define</span> MAXIMUM_SUPPORTED_EXTENSION     512<span class="codeKeyword">typedef</span> <span class="codeKeyword">struct</span> _CONTEXT {    <span class="codeComment">//-----------------------------------------</span>    DWORD ContextFlags;    <span class="codeComment">//-----------------------------------------</span>    DWORD   Dr0;    DWORD   Dr1;    DWORD   Dr2;    DWORD   Dr3;    DWORD   Dr6;    DWORD   Dr7;    <span class="codeComment">//-----------------------------------------</span>    FLOATING_SAVE_AREA FloatSave;    <span class="codeComment">//-----------------------------------------</span>    DWORD   SegGs;    DWORD   SegFs;    DWORD   SegEs;    DWORD   SegDs;    <span class="codeComment">//-----------------------------------------</span>    DWORD   Edi;    DWORD   Esi;    DWORD   Ebx;    DWORD   Edx;    DWORD   Ecx;    DWORD   Eax;    <span class="codeComment">//-----------------------------------------</span>    DWORD   Ebp;    DWORD   Eip;    DWORD   SegCs;    DWORD   EFlags;    DWORD   Esp;    DWORD   SegSs;    <span class="codeComment">//-----------------------------------------</span>    BYTE    ExtendedRegisters[MAXIMUM_SUPPORTED_EXTENSION];    <span class="codeComment">//----------------------------------------</span>} CONTEXT,*LPCONTEXT;</pre>
<h4>Table 4: CONTEXT</h4>
<table cellspacing="0" cellpadding="0" width="200" border="1">
<tbody>
<tr>
<td align="center" height="35">Context Flags</td>
<td align="center" height="35"><font color="#0000ff">0x00000000</font></td>
<td align="center" colspan="2" height="35"><tt>ContextFlags</tt></td>            </tr>
<tr>
<td align="center" rowspan="6">
Context Debug Registers                </td>
<td align="center"><font color="#0000ff">0x00000004</font></td>
<td align="center" colspan="2"><tt>Dr0</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000008</font></td>
<td align="center" colspan="2"><tt>Dr1</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x0000000C</font></td>
<td align="center" colspan="2"><tt>Dr2</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000010</font></td>
<td align="center" colspan="2"><tt>Dr3</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000014</font></td>
<td align="center" colspan="2"><tt>Dr6</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000018</font></td>
<td align="center" colspan="2"><tt>Dr7</tt></td>            </tr>
<tr>
<td align="center" rowspan="9">
Context Floating Point                </td>
<td align="center"><font color="#0000ff">0x0000001C</font></td>
<td align="center" rowspan="9"><tt>FloatSave</tt></td>
<td align="center"><tt>StatusWord</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000020</font></td>
<td align="center"><tt>StatusWord</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000024</font></td>
<td align="center"><tt>TagWord</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000028</font></td>
<td align="center"><tt>ErrorOffset</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x0000002C</font></td>
<td align="center"><tt>ErrorSelector</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000030</font></td>
<td align="center"><tt>DataOffset</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000034</font></td>
<td align="center"><tt>DataSelector</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000038
                ...
                0x00000087</font></td>
<td align="center"><tt>RegisterArea</tt> [<font color="#0000ff">0x50</font>]</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000088</font></td>
<td align="center"><tt>Cr0NpxState</tt></td>            </tr>
<tr>
<td align="center" rowspan="4">Context Segments</td>
<td align="center"><font color="#0000ff">0x0000008C</font></td>
<td align="center" colspan="2"><tt>SegGs</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000090</font></td>
<td align="center" colspan="2"><tt>SegFs</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000094</font></td>
<td align="center" colspan="2"><tt>SegEs</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x00000098</font></td>
<td align="center" colspan="2"><tt>SegDs</tt></td>            </tr>
<tr>
<td align="center" rowspan="6">Context Integer</td>
<td align="center"><font color="#0000ff">0x0000009C</font></td>
<td align="center" colspan="2"><tt>Edi</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000A0</font></td>
<td align="center" colspan="2"><tt>Esi</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000A4</font></td>
<td align="center" colspan="2"><tt>Ebx</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000A8</font></td>
<td align="center" colspan="2"><tt>Edx</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000AC</font></td>
<td align="center" colspan="2"><tt>Ecx</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000B0</font></td>
<td align="center" colspan="2"><tt>Eax</tt></td>            </tr>
<tr>
<td align="center" rowspan="6">Context Control</td>
<td align="center"><font color="#0000ff">0x000000B4</font></td>
<td align="center" colspan="2"><tt>Ebp</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000B8</font></td>
<td align="center" colspan="2"><tt>Eip</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000BC</font></td>
<td align="center" colspan="2"><tt>SegCs</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000C0</font></td>
<td align="center" colspan="2"><tt>EFlags</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000C4</font></td>
<td align="center" colspan="2"><tt>Esp</tt></td>            </tr>
<tr>
<td align="center"><font color="#0000ff">0x000000C8</font></td>
<td align="center" colspan="2"><tt>SegSs</tt></td>            </tr>
<tr>
<td align="center">Context Extended Registers</td>
<td align="center">
<p align="center"><font color="#0000ff">0x000000CC
                ...
                0x000002CB</font>                </td>
<td align="center" colspan="2"><tt>ExtendedRegisters</tt>[<font color="#0000ff">0x200</font>]</td>            </tr>        </tbody>    </table>
By the following code, you have accomplished the main purpose of coming to OEP by the structured exception handler:
<pre><span class="codeKeyword">__stdcall</span> <span class="codeKeyword">void</span> DynLoader(){_asm{<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_MAGIC)<span class="codeComment">//----------------------------------</span>Main_0:    PUSHAD  <span class="codeComment">// Save the registers context in stack</span>    CALL Main_1Main_1:    POP EBP    SUB EBP,OFFSET Main_1 <span class="codeComment">// Get Base EBP</span>    MOV EAX,DWORD PTR [EBP+_RO_dwImageBase]    ADD EAX,DWORD PTR [EBP+_RO_dwOrgEntryPoint]    MOV DWORD PTR [ESP+10h],EAX    <span class="codeComment">// pStack.Ebx <- EAX</span>    LEA EAX,[EBP+_except_handler1_OEP_Jump]    MOV DWORD PTR [ESP+1Ch],EAX    <span class="codeComment">// pStack.Eax <- EAX</span>    POPAD  <span class="codeComment">// Restore the first registers context from stack</span>    <span class="codeComment">//----------------------------------------------------</span>    <span class="codeComment">// the structured exception handler (SEH) installation</span>    PUSH EAX    XOR  EAX, EAX    PUSH DWORD PTR FS:[0]       <span class="codeComment">// NT_TIB32.ExceptionList</span>    MOV DWORD PTR FS:[0],ESP    <span class="codeComment">// NT_TIB32.ExceptionList <-ESP</span>    <span class="codeComment">//----------------------------------------------------</span>    <span class="codeComment">// the raise a INT 3 exception</span>    DWORD_TYPE(0xCCCCCCCC)    <span class="codeComment">//--------------------------------------------------------</span><span class="codeComment">// -------- exception handler expression filter ----------</span>_except_handler1_OEP_Jump:    PUSH EBP    MOV EBP,ESP    <span class="codeComment">//------------------------------</span>    MOV EAX,DWORD PTR SS:[EBP+010h]   <span class="codeComment">// PCONTEXT: pContext <- EAX</span>    <span class="codeComment">//==============================</span>    PUSH EDI    <span class="codeComment">// restore original SEH</span>    MOV EDI,DWORD PTR DS:[EAX+0C4h]    <span class="codeComment">// pContext.Esp</span>    PUSH DWORD PTR DS:[EDI]    POP DWORD PTR FS:[0]    ADD DWORD PTR DS:[EAX+0C4h],8    <span class="codeComment">// pContext.Esp</span>    <span class="codeComment">//------------------------------</span>    <span class="codeComment">// set the Eip to the OEP</span>    MOV EDI,DWORD PTR DS:[EAX+0A4h] <span class="codeComment">// EAX <- pContext.Ebx</span>    MOV DWORD PTR DS:[EAX+0B8h],EDI <span class="codeComment">// pContext.Eip <- EAX</span>    <span class="codeComment">//------------------------------</span>    POP EDI    <span class="codeComment">//==============================</span>    MOV EAX, EXCEPTION_CONTINUE_SEARCH    LEAVE    RETN<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_START_DATA1)<span class="codeComment">//----------------------------------</span>_RO_dwImageBase:                DWORD_TYPE(0xCCCCCCCC)_RO_dwOrgEntryPoint:            DWORD_TYPE(0xCCCCCCCC)<span class="codeComment">//----------------------------------</span>    DWORD_TYPE(DYN_LOADER_END_MAGIC)<span class="codeComment">//----------------------------------</span>}}</pre>
<h3>6 Build an Import Table and Reconstruct the Original Import Table</h3>
There are two ways to use the Windows <a href="http://en.wikipedia.org/wiki/Microsoft_Dynamic_Link_Library" target="new">dynamic link library (DLL)</a> in Windows application programming:
<ul>
<li><strong>Using Windows libraries by additional dependencies</strong>: 
        <a name="more"><font color="#000000"> </font>
<font color="#000000"><img height="145" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=DEPENDENCIES_GIF&ds=20060302" width="500" alt="" />
        </font>(        </a><a href="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=DEPENDENCIES_GIF&ds=20060302" target="_blank">Full Size Image</a>)</li>
<li><strong>Using Windows dynamic link libraries in run-time</strong>:
<pre><span class="codeComment">// DLL function signature</span><span class="codeKeyword">typedef</span> HGLOBAL (*importFunction_GlobalAlloc)(UINT, SIZE_T);...importFunction_GlobalAlloc __GlobalAlloc;<span class="codeComment">// Load DLL file</span>HINSTANCE hinstLib = LoadLibrary("Kernel32.dll");<span class="codeKeyword">if</span> (hinstLib == <span class="codeKeyword">NULL</span>){   <span class="codeComment">// Error - unable to load DLL</span>}<span class="codeComment">// Get function pointer</span>__GlobalAlloc =   (importFunction_GlobalAlloc)GetProcAddress(hinstLib,                                              "GlobalAlloc");<span class="codeKeyword">if</span> (addNumbers == <span class="codeKeyword">NULL</span>){    <span class="codeComment">// Error - unable to find DLL function</span>}FreeLibrary(hinstLib);</pre>        </li>    </ul>
When you make a Windows application project, the linker includes at least <em>kernel32.dll</em> in the base dependencies of your project. Without <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/loadlibrary.asp" target="new"><tt>LoadLibrary()</tt></a> and <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/getprocaddress.asp" target="new"><tt>GetProcAddress()</tt></a> of <em>Kernel32.dll</em>, you cannot load a DLL at run time. The dependencies information is stored in the import table section. By using <a href="http://www.microsoft.com/resources/documentation/Windows/XP/all/reskit/en-us/Default.asp?url=/resources/documentation/Windows/XP/all/reskit/en-us/prmb_tol_kewf.asp" target="new">Dependency Walker</a>, it is not so difficult to observe the DLL module and the functions that are imported into a PE file.
<img height="352" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=DEPENDENCY_WALKER_GIF&ds=20060302" width="480" alt="" />
You attempt to establish your custom import table to conduct your project. Furthermore, you have to fix up the original import table at the end to run the real code of the program.
<h4>PE Maker: Step 3</h4>
Download the pemaker3.zip source files from the end of the article.
<h4>6.1 Construct the Client Import Table</h4>
I strongly advise that you to read Section 6.4 of the <a href="http://www.microsoft.com/whdc/system/platform/firmware/PECOFF.mspx" target="new">Microsoft Portable Executable and the Common Object File Format Specification</a> document. This section contains the principal information to comprehend the import table performance. The import table data is accessible by a second data directory of the optional header from PE headers, so you can access it by using the following code:
<pre>DWORD dwVirtualAddress = image_nt_headers->   OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].      VirtualAddress;DWORD dwSize = image_nt_headers->   OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].      Size;</pre>
The <tt>VirtualAddress</tt> refers to structures by <tt>IMAGE_IMPORT_DESCRIPTOR</tt>. This structure contains the pointer to the imported DLL name and the relative virtual address of the first thunk.
<pre><span class="codeKeyword">typedef</span> <span class="codeKeyword">struct</span> _IMAGE_IMPORT_DESCRIPTOR {    <span class="codeKeyword">union</span> {        DWORD   Characteristics;        DWORD   OriginalFirstThunk;    };    DWORD   TimeDateStamp;    DWORD   ForwarderChain;    DWORD   <font color="#ff0000">Name</font>;         <span class="codeComment">// the imported DLL name</span>    DWORD   <font color="#ff0000">FirstThunk</font>;   <span class="codeComment">// the relative virtual address of the</span>                          <span class="codeComment">// first thunk</span>} IMAGE_IMPORT_DESCRIPTOR, *PIMAGE_IMPORT_DESCRIPTOR;</pre>
When a program is running, the Windows Task Manager sets the thunks by the virtual address of the function. The virtual address is found by the name of the function. At first, the thunks hold the relative virtual address of the function name, as shown in Table 5; during execution, they are fixed up by the virtual address of the functions (see Table 6).
<h4>Table 5: The Import Table in a file image</h4>
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td rowspan="8"><tt>IMAGE_IMPORT_
                DESCRIPTOR[0]</tt></td>
<td><tt>OriginalFirstThunk</tt></td>
<td colspan="2" rowspan="3"> </td>
<td colspan="2" rowspan="4"> </td>            </tr>
<tr>
<td><tt>TimeDateStamp</tt></td>            </tr>
<tr>
<td><tt>ForwarderChain</tt></td>            </tr>
<tr>
<td><tt>Name_RVA</tt></td>
<td>------></td>
<td><font color="#a52a2a">"kernel32.dll"<font color="#0000ff">,0</font></font></td>            </tr>
<tr>
<td><tt>FirstThunk_RVA</tt></td>
<td>------></td>
<td><tt>proc_1_name_RVA</tt></td>
<td>------></td>
<td><font color="#0000ff">0,0,</font><font color="#a52a2a">"LoadLibraryA"</font><font color="#0000ff">,0</font></td>            </tr>
<tr>
<td colspan="2" rowspan="3"> </td>
<td><tt>proc_2_name_RVA</tt></td>
<td>------></td>
<td><font color="#0000ff">0,0,</font><font color="#a52a2a">"GetProcAddress"</font><font color="#0000ff">,0</font></td>            </tr>
<tr>
<td><tt>proc_3_name_RVA</tt></td>
<td>------></td>
<td><font color="#0000ff">0,0,</font><font color="#a52a2a">"GetModuleHandleA"</font><font color="#0000ff">,0</font></td>            </tr>
<tr>
<td>...</td>
<td> </td>
<td> </td>            </tr>
<tr>
<td><tt>IMAGE_IMPORT_
                DESCRIPTOR[1]</tt></td>
<td colspan="5"> </td>            </tr>
<tr>
<td><tt>...</tt></td>
<td colspan="5"> </td>            </tr>
<tr>
<td><tt>IMAGE_IMPORT_
                DESCRIPTOR[n]</tt></td>
<td colspan="5"> </td>            </tr>        </tbody>    </table>    
<h4>Table 6: The Import Table in virtual memory</h4>
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td rowspan="8"><tt>IMAGE_IMPORT_DESCRIPTOR[0]</tt></td>
<td><tt>OriginalFirstThunk</tt></td>
<td colspan="2" rowspan="3"> </td>            </tr>
<tr>
<td><tt>TimeDateStamp</tt></td>            </tr>
<tr>
<td><tt>ForwarderChain</tt></td>            </tr>
<tr>
<td><tt>Name_RVA</tt></td>
<td><tt>------></tt></td>
<td><font color="#a52a2a">"kernel32.dll"<font color="#0000ff">,0</font></font></td>            </tr>
<tr>
<td><tt>FirstThunk_RVA</tt></td>
<td><tt>------></tt></td>
<td><tt>proc_1_VA</tt></td>            </tr>
<tr>
<td colspan="2" rowspan="3"> </td>
<td><tt>proc_2_VA</tt></td>            </tr>
<tr>
<td><tt>proc_3_VA</tt></td>            </tr>
<tr>
<td><tt>...</tt></td>            </tr>
<tr>
<td><tt>IMAGE_IMPORT_DESCRIPTOR[1]</tt></td>
<td colspan="3"> </td>            </tr>
<tr>
<td><tt>...</tt></td>
<td colspan="3"> </td>            </tr>
<tr>
<td><tt>IMAGE_IMPORT_DESCRIPTOR[n]</tt></td>
<td colspan="3"> </td>            </tr>        </tbody>    </table>    
You want to make a simple import table to import <tt>LoadLibrary()</tt>, and <tt>GetProcAddress()</tt> from <em>Kernel32.dll</em>. You need these two essential API functions to cover other API functions in run-time. The following assembly code shows how easily you can reach your solution:
<pre><font color="#808080">0101F000: <font color="#0000ff">00000000</font> ; OriginalFirstThunk0101F004: <font color="#0000ff">00000000</font> ; TimeDateStamp0101F008: <font color="#0000ff">00000000</font> ; ForwarderChain0101F00C: <font color="#0000ff">0001F034</font> ; Name;       ImageBase + 0001F034                                 -> 0101F034 -> "Kernel32.dll",00101F010: <font color="#0000ff">0001F028</font> ; FirstThunk; ImageBase + 0001F028 -> 0101F0280101F014: <font color="#0000ff">00000000</font>0101F018: <font color="#0000ff">00000000</font>0101F01C: <font color="#0000ff">00000000</font>0101F020: <font color="#0000ff">00000000</font>0101F024: <font color="#0000ff">00000000</font>0101F028: <font color="#0000ff">0001F041</font> ; ImageBase + 0001F041 -> 0101F041                     -> 0,0,"LoadLibraryA",00101F02C: <font color="#0000ff">0001F050</font> ; ImageBase + 0001F050 -> 0101F050                     -> 0,0,"GetProcAddress",00101F030: <font color="#0000ff">00000000</font>0101F034: <font color="#a52a2a"><span class="codeComment">'K' 'e' 'r' 'n' 'e' 'l' '3' '2' '.' 'd' 'l' 'l' </span>0001F041: <font color="#0000ff">00 00</font> <font color="#a52a2a"><span class="codeComment">'L' 'o' 'a' 'd' 'L' 'i' 'b' 'r' 'a' 'r' 'y' 'A'</span>0001F050: <font color="#0000ff">00 00</font> <font color="#a52a2a"><span class="codeComment">'G' 'e' 't' 'P' 'r' 'o' 'c' 'A' 'd' 'd' 'r' 'e' 's'</span>          <span class="codeComment">'s'</span></font> <font color="#0000ff">00</font></font> <font color="#0000ff">00</font></font><font color="#0000ff">00</font></font></pre>
After running...
<pre><font color="#808080">0101F000: <font color="#0000ff">00000000</font> ; OriginalFirstThunk0101F004: <font color="#0000ff">00000000</font> ; TimeDateStamp0101F008: <font color="#0000ff">00000000</font> ; ForwarderChain0101F00C: <font color="#0000ff">0001F034</font> ; Name;       ImageBase + 0001F034                                 -> 0101F034 -> "Kernel32.dll",00101F010: <font color="#0000ff">0001F028</font> ; FirstThunk; ImageBase + 0001F028 -> 0101F0280101F014: <font color="#0000ff">00000000</font>0101F018: <font color="#0000ff">00000000</font>0101F01C: <font color="#0000ff">00000000</font>0101F020: <font color="#0000ff">00000000</font>0101F024: <font color="#0000ff">00000000</font>0101F028: <font color="#ff0000">7C801D77</font> ; -> Kernel32.LoadLibrary()0101F02C: <font color="#ff0000">7C80AC28</font> ; -> Kernel32.GetProcAddress()0101F030: <font color="#0000ff">00000000</font>0101F034: <font color="#a52a2a"><span class="codeComment">'K' 'e' 'r' 'n' 'e' 'l' '3' '2' '.' 'd' 'l' 'l' </span>0001F041: <font color="#0000ff">00 00</font> <font color="#a52a2a"><span class="codeComment">'L' 'o' 'a' 'd' 'L' 'i' 'b' 'r' 'a' 'r' 'y' 'A'</span>0001F050: <font color="#0000ff">00 00</font> <font color="#a52a2a"><span class="codeComment">'G' 'e' 't' 'P' 'r' 'o' 'c' 'A' 'd' 'd' 'r' 'e' 's'</span>          <span class="codeComment">'s'</span></font> <font color="#0000ff">00</font></font> <font color="#0000ff">00</font></font><font color="#0000ff">00</font></font></pre>
I have prepared a class library to make every import table by using a client string table. The <tt>CITMaker</tt> class library in <em>itmaker.h</em>; it will build an import table by <tt>sz_IT_EXE_strings</tt> and also the relative virtual address of the import table.
<pre><span class="codeKeyword">static</span> <span class="codeKeyword">const</span> <span class="codeKeyword">char</span> *sz_IT_EXE_strings[]={    "Kernel32.dll",    "LoadLibraryA",    "GetProcAddress",    0,,    0,};</pre>
You subsequently employ this class library to establish an import table to support DLLs and OCXs, so this is a general library to present all possible import tables easily. The next step is clarified in the following code.
<pre>CITMaker *<font color="#ff0000">ImportTableMaker</font> = <span class="codeKeyword">new</span> CITMaker( IMPORT_TABLE_EXE );...pimage_section_header=AddNewSection( ".xxx", dwNewSectionSize );<span class="codeComment">// build import table by the current virtual address</span><font color="#ff0000">ImportTableMaker</font>-><font color="#008000">Build</font>( <font color="#0000ff">pimage_section_header->VirtualAddress</font> );memcpy( pNewSection, <font color="#ff0000">ImportTableMaker</font>-><font color="#008000">pMem</font>,<font color="#ff0000">ImportTableMaker</font>-><font color="#008000">dwSize</font> );...memcpy( image_section[image_nt_headers->FileHeader.NumberOfSections-1],        pNewSection,        dwNewSectionSize );...image_nt_headers->OptionalHeader.  DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].VirtualAddress  = <font color="#0000ff">pimage_section_header->VirtualAddress</font>;image_nt_headers->OptionalHeader.  DataDirectory[IMAGE_DIRECTORY_ENTRY_IMPORT].Size  = <font color="#ff0000">ImportTableMaker</font>-><font color="#008000">dwSize</font>;...<span class="codeKeyword">delete</span> <font color="#ff0000">ImportTableMaker</font>;</pre>
The import table is copied at the beginning of the new section, and the relevant data directory is adjusted to the relative virtual address of the new section and the size of the new import table.
<h4>6.2 Using other API functions at run time</h4>
At this time, you can load other DLLs and find the process address of other functions by using <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/loadlibrary.asp" target="new"><tt>LoadLibrary()</tt></a> and <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/getprocaddress.asp" target="new"><tt>GetProcAddress()</tt></a>:
<pre><font color="#808080">lea edi, <font color="#ff0000">@</font><font color="#a52a2a">"Kernel32.dll"</font><span class="codeComment">//-------------------</span><font color="#0000ff">push edimov eax,offset _p_LoadLibrarycall [ebp+eax] <span class="codeComment">//LoadLibrary(lpLibFileName);</span><span class="codeComment">//-------------------</span>mov esi,eax    <span class="codeComment">// esi -> hModule</span>lea edi, <font color="#ff0000">@</font><font color="#a52a2a">"GetModuleHandleA"</font><span class="codeComment">//-------------------</span><font color="#0000ff">push edipush esimov eax,offset _p_GetProcAddresscall [ebp+eax] <span class="codeComment">//GetModuleHandle=GetProcAddress(hModule, lpProcName);</span><span class="codeComment">//--------------------</span></font></font></font></pre>
    <a name="more"><font color="#000000"> </font></a><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/loadlibrary.asp" target="new"><tt>LoadLibrary()</tt></a> and <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/getprocaddress.asp" target="new"><tt>GetProcAddress()</tt></a> aid you in your effort to reach your intention.
I want to have a complete imported function table similar in performance done in a real EXE file. If you look inside a PE file, you will discover that an API call is done by an indirection jump through the virtual address of the API function:
<h4>JMP DWORD PTR [XXXXXXXX]</h4>
<pre><font color="#808080">...0101F028: <font color="#ff0000">7C801D77</font>      ; Virtual Address of kernel32.LoadLibrary()...0101F120: JMP DWORD PTR [<font color="#ff0000">0101F028</font>]...0101F230: CALL <font color="#ff0000">0101F120</font> ;  JMP to kernel32.LoadLibrary...</font></pre>
It makes it easy to expand the other part of your project by this performance, so you construct two data tables: the first for API virtual addresses, and the second for the <tt>JMP [XXXXXXXX]</tt>.
<pre><span class="codeKeyword">#define</span> __jmp_api               byte_type(0xFF) byte_type(0x25)__asm{...<span class="codeComment">//----------------------------------------------------------------</span>_p_GetModuleHandle:             dword_type(0xCCCCCCCC)_p_VirtualProtect:              dword_type(0xCCCCCCCC)_p_GetModuleFileName:           dword_type(0xCCCCCCCC)_p_CreateFile:                  dword_type(0xCCCCCCCC)_p_GlobalAlloc:                 dword_type(0xCCCCCCCC)<span class="codeComment">//----------------------------------------------------------------</span>_jmp_GetModuleHandle:           __jmp_api   dword_type(0xCCCCCCCC)_jmp_VirtualProtect:            __jmp_api   dword_type(0xCCCCCCCC)_jmp_GetModuleFileName:         __jmp_api   dword_type(0xCCCCCCCC)_jmp_CreateFile:                __jmp_api   dword_type(0xCCCCCCCC)_jmp_GlobalAlloc:               __jmp_api   dword_type(0xCCCCCCCC)<span class="codeComment">//----------------------------------------------------------------</span>...}</pre>
In the succeeding code, you have concluded your ambition to install a custom internal import table! (You cannot call it import table.)
<pre><font color="#808080">    ...    lea edi,[ebp+_p_szKernel32]    lea ebx,[ebp+_p_GetModuleHandle]    lea ecx,[ebp+_jmp_GetModuleHandle]    add ecx,02h_api_get_lib_address_loop:        push ecx        <font color="#0000ff">push edi        mov eax,offset _p_LoadLibrary        call [ebp+eax]    <span class="codeComment">//LoadLibrary(lpLibFileName);</span>        pop ecx        mov esi,eax       <span class="codeComment">// esi -> hModule</span>        push edi        call __strlen        add esp,04h        add edi,eax_api_get_proc_address_loop:            push ecx            <font color="#0000ff">push edi            push esi            mov eax,offset _p_GetProcAddress            <span class="codeComment">//GetModuleHandle=GetProcAddress(hModule, lpProcName);</span>            call [ebp+eax]            pop ecx</font>            <font color="#008000">mov [ebx],eax            mov [ecx],ebx    <span class="codeComment">// JMP DWORD PTR [XXXXXXXX]</span>            add ebx,04h            add ecx,06h            push edi            call __strlen            add esp,04h            add edi,eax            mov al,<span class="codeKeyword">byte</span> ptr [edi]        test al,al        jnz _api_get_proc_address_loop        inc edi        mov al,<span class="codeKeyword">byte</span> ptr [edi]    test al,al    jnz _api_get_lib_address_loop    ...</font></font></font></pre>
<h4>6.3 Fix up the Original Import Table</h4>
To run the program again, you should fix up the thunks of the actual import table; otherwise, you have a corrupted target PE file. Your code must correct all of the thunks the same as Table 5 to Table 6. Once more, 
<pre><font color="#808080">    ...    mov ebx,[ebp+<font color="#ff0000">_p_dwImportVirtualAddress</font>]    test ebx,ebx    jz _it_fixup_end    mov esi,[ebp+<font color="#ff0000">_p_dwImageBase</font>]    add ebx,esi             <span class="codeComment">// dwImageBase + dwImportVirtualAddress</span>_it_fixup_get_lib_address_loop:        mov eax,[ebx+00Ch]  <span class="codeComment">// image_import_descriptor.Name</span>        test eax,eax        jz _it_fixup_end        mov ecx,[ebx+010h]  <span class="codeComment">// image_import_descriptor.FirstThunk</span>        add ecx,esi        mov [ebp+<font color="#ff0000">_p_dwThunk</font>],ecx    <span class="codeComment">// dwThunk</span>        mov ecx,[ebx]       <span class="codeComment">// image_import_descriptor.Characteristics</span>        test ecx,ecx        jnz _it_fixup_table            mov ecx,[ebx+010h]_it_fixup_table:        add ecx,esi        mov [ebp+<font color="#ff0000">_p_dwHintName</font>],ecx    <span class="codeComment">// dwHintName</span>        add eax,esi  <span class="codeComment">// image_import_descriptor.Name + dwImageBase = ModuleName</span>        <font color="#0000ff">push eax     <span class="codeComment">// lpLibFileName</span>        mov eax,offset _p_LoadLibrary        call [ebp+eax]               <span class="codeComment">// LoadLibrary(lpLibFileName);</span>        test eax,eax        jz _it_fixup_end        mov edi,eax_it_fixup_get_proc_address_loop:            mov ecx,[ebp+<font color="#ff0000">_p_dwHintName</font>]    <span class="codeComment">// dwHintName</span>            mov edx,[ecx]            <span class="codeComment">// image_thunk_data.Ordinal</span>            test edx,edx            jz _it_fixup_next_module            test edx,080000000h      <span class="codeComment">// .IF( import by ordinal )</span>            jz _it_fixup_by_name                and edx,07FFFFFFFh    <span class="codeComment">// get ordinal</span>                jmp _it_fixup_get_addr_it_fixup_by_name:            add edx,esi  <span class="codeComment">// image_thunk_data.Ordinal</span>                         <span class="codeComment">// + dwImageBase = OrdinalName</span>            inc edx            inc edx                  <span class="codeComment">// OrdinalName.Name</span>_it_fixup_get_addr:            <font color="#0000ff">push edx <span class="codeComment">//lpProcName</span>            push edi                 <span class="codeComment">// hModule</span>            mov eax,offset _p_GetProcAddress            call [ebp+eax]    <span class="codeComment">// GetProcAddress(hModule, lpProcName);</span>            <font color="#008000">mov ecx,[ebp+<font color="#ff0000">_p_dwThunk</font>]    <span class="codeComment">// dwThunk</span>            mov [ecx],eax  <span class="codeComment">// correction the thunk</span>            <span class="codeComment">// dwThunk => next dwThunk</span>            add dword ptr [ebp+<font color="#ff0000">_p_dwThunk</font>], <font color="#0000ff">004h</font>            <span class="codeComment">// dwHintName => next dwHintName</span>            add dword ptr [ebp+<font color="#ff0000">_p_dwHintName</font>],<font color="#0000ff">004h</font>        jmp _it_fixup_get_proc_address_loop_it_fixup_next_module:        add ebx,014h      <span class="codeComment">// sizeof(IMAGE_IMPORT_DESCRIPTOR)</span>    jmp _it_fixup_get_lib_address_loop_it_fixup_end:    ...</font></font></font></font></pre>
<pre>
<h3>7 Support DLL and OCX</h3>
Now, you intend to include the <a href="http://en.wikipedia.org/wiki/Microsoft_Dynamic_Link_Library" target="new">dynamic link library (DLL)</a> and <a href="http://en.wikipedia.org/wiki/OCX" target="new">OLE-ActiveX Control</a> in your PE builder project. Supporting them is very easy if you pay attention to the two-time arrival into the Offset of Entry Point, the relocation table implementation, and the client import table.
<h4>PE Maker: Step 4</h4>
 
<a name="more"><font color="#000000"> </font></a><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/loadlibrary.asp" target="new"><tt>LoadLibrary()</tt></a>, or an OCX is registered by using <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/loadlibrary.asp" target="new"><tt>LoadLibrary()</tt></a> and <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/getprocaddress.asp" target="new"><tt>GetProcAddress()</tt></a> through calling <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/com/html/4442206b-b2ad-47d7-8add-18002c44c5a2.asp" target="new"><tt>DllRegisterServer()</tt></a>, the first of the OEP arrival is done.
 
<pre>hinstDLL = LoadLibrary( "test1.dll" );hinstOCX = LoadLibrary( "test1.ocx" );_DllRegisterServer = GetProcAddress( hinstOCX,                                     "DllRegisterServer" );_DllRegisterServer();    <span class="codeComment">// ocx register</span></pre>    
Download the pemaker4.zip source files from the end of the article.
<h4>7.1 Twice OEP approach</h4>
The Offset of Entry Point of a DLL file or an OCX file is touched by the main program atleast twice:
<ul>
<li><strong>Constructor</strong>: When a DLL is loaded by </li>
<li><strong>Destructor</strong>: When the main program frees the library usage by <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dllproc/base/freelibrary.asp" target="new"><tt>FreeLibrary()</tt></a>, the second OEP arrival happens.
 
<pre>FreeLibrary( hinstDLL );FreeLibrary( hinstOCX );</pre>        </li>    </ul>
To perform this, I have employed a trick that causes in the second time again, the instruction pointer (EIP) traveling towards the original OEP by the structured exception handler.
<pre><font color="#808080"><font color="#000000">_main_0:    pushad    <span class="codeComment">// save the registers context in stack</span>    call _main_1_main_1:    pop ebp    sub ebp,offset _main_1    <span class="codeComment">// get base ebp</span>    <span class="codeComment">//---------------- support dll, ocx  -----------------</span>_support_dll_0:</font>    jmp _support_dll_1        <span class="codeComment">// <font color="#ff0000">nop; nop;    // << trick</font></span>                              <span class="codeComment">// in the second time OEP</span>    <font color="#000000">jmp _support_dll_2</font>_support_dll_1:    <span class="codeComment">//----------------------------------------------------</span>    ...    <span class="codeComment">//---------------- support dll, ocx  1 ---------------</span>    mov edi,[ebp+_p_dwImageBase]    add edi,[edi+03Ch]            <span class="codeComment">// edi -> IMAGE_NT_HEADERS</span>    mov ax,word ptr [edi+016h]    <span class="codeComment">// edi -> image_nt_headers-></span>                                  <span class="codeComment">// FileHeader.Characteristics</span>    test ax,<font color="#008000">IMAGE_FILE_DLL</font>    jz _support_dll_2        mov ax, <font color="#ff0000">9090h <span class="codeComment">// << trick</span>        mov word ptr [ebp+_support_dll_0],ax</font></font><font color="#000000">_support_dll_2:    <span class="codeComment">//----------------------------------------------------</span>    ...    into OEP by SEH ...</font></pre>
I hope you caught the trick in the preceding code, but this is not all of it. You have a problem in <tt>ImageBase</tt>, when the library has been loaded in different image bases by the main program. You should write some code to find the real image base and store it to use forward.
<pre><font color="#808080">    mov eax,<font color="#008000">[esp+24h]</font>    <span class="codeComment">// the real imagebase</span>    mov ebx,<font color="#008000">[esp+30h]</font>    <span class="codeComment">// oep</span>    cmp eax,ebx    ja _no_dll_pe_file_0        cmp word ptr [eax],IMAGE_DOS_SIGNATURE        jne _no_dll_pe_file_0            mov [ebp+_p_dwImageBase],eax_no_dll_pe_file_0:</font></pre>
This code finds the real image base by investigating the stack information. By using the real image base and the formal image base, you should correct all memory calls inside the image program!! Don't be afraid; it will be done simply by the relocating the table information.
<h4>7.2 Implement relocation table</h4>
To understand the relocation table better, you can take a look at Section 6.6 of the <a href="http://www.microsoft.com/whdc/system/platform/firmware/PECOFF.mspx" target="new">Microsoft Portable Executable and Common Object File Format Specification</a> document. The relocation table contains many packages to relocate the information related to the virtual address inside the virtual memory image. Each package is comprised of an 8-byte header to exhibit the base virtual address and the number of data, demonstrated by the <tt>IMAGE_BASE_RELOCATION</tt> data structure.
<pre><span class="codeKeyword">typedef</span> <span class="codeKeyword">struct</span> _IMAGE_BASE_RELOCATION {   DWORD   VirtualAddress;   DWORD   SizeOfBlock;} IMAGE_BASE_RELOCATION, *PIMAGE_BASE_RELOCATION;</pre>
<h4>Table 7 - The Relocation Table</h4>
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td align="center" rowspan="7">Block[1]</td>
<td align="center" colspan="4">VirtualAddress</td>            </tr>
<tr>
<td align="center" colspan="4">SizeOfBlock</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">...</td>
<td align="center">...</td>
<td align="center">...</td>
<td align="center">...</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">00</td>
<td align="center">00</td>            </tr>
<tr>
<td align="center" rowspan="7">Block[2]</td>
<td align="center" colspan="4">VirtualAddress</td>            </tr>
<tr>
<td align="center" colspan="4">SizeOfBlock</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">...</td>
<td align="center">...</td>
<td align="center">...</td>
<td align="center">...</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">00</td>
<td align="center">00</td>            </tr>
<tr>
<td align="center">...</td>
<td align="center" colspan="4">
 
... 
                 </td>            </tr>
<tr>
<td align="center" rowspan="7">Block[n]</td>
<td align="center" colspan="4">VirtualAddress</td>            </tr>
<tr>
<td align="center" colspan="4">SizeOfBlock</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">type:4</td>
<td align="center">offset:12</td>            </tr>
<tr>
<td align="center">...</td>
<td align="center">...</td>
<td align="center">...</td>
<td align="center">...</td>            </tr>
<tr>
<td align="center">type:4</td>
<td align="center">offset:12</td>
<td align="center">00</td>
<td align="center">00</td>            </tr>        </tbody>    </table>    
Table 7 illustrates the main idea of the relocation table. Furthermore, you can upload a DLL or an OCX file in <a href="http://www.ollydbg.de/" target="new">OllyDbg</a> to observe the relocation table, the <em>".reloc"</em> section through <em>Memory map window</em>. By the way, you find the position of the relocation table by using the following code in your project:
<pre>DWORD dwVirtualAddress = image_nt_headers->  OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_BASERELOC].  VirtualAddress;DWORD dwSize = image_nt_headers->  OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_BASERELOC].Size;</pre>
By OllyDbg, you have the same as the following for the <em>".reloc"</em> section, by using the Long Hex viewer mode. In this example, the base virtual address is <strong>0x1000</strong> and the size of the block is <strong>0x184</strong>.
<pre>008E1000 : 00001000  00000184  30163000  30403028008E1010 : 30683054  308C3080  30AC309C  30D830CC008E1020 : 30E030DC  30E830E4  30F030EC  310030F4008E1030 : 3120310D  315F3150  31A431A0  31C031A8008E1040 : 31D031CC  31F431EC  31FC31F8  32043200008E1050 : 320C3208  32143210  324C322C  32583254008E1060 : 3260325C  32683264  3270326C  32B03274</pre>
It relocates the data in the subsequent virtual addresses:
<pre>0x1000 + 0x0000 = 0x10000x1000 + 0x0016 = 0x10160x1000 + 0x0028 = 0x10280x1000 + 0x0040 = 0x10400x1000 + 0x0054 = 0x1054...</pre>
Each package performs the relocation by using consecutive 4 bytes form its internal information. The first byte refers to the type of relocation and the next three bytes are the offset that must be used with the base virtual address and the image base to correct the image information.
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td align="center" width="30">type</td>
<td align="center" colspan="3">offset</td>            </tr>
<tr>
<td align="center"><font color="#0000ff">03</font></td>
<td align="center"><font color="#0000ff">00</font></td>
<td align="center"><font color="#0000ff">00</font></td>
<td align="center"><font color="#0000ff">00</font></td>            </tr>        </tbody>    </table>    
<h4>What is the type?</h4>
The type can be one of the following values:
<ul>
<li><tt>IMAGE_REL_BASED_ABSOLUTE (0)</tt>: No effect </li>
<li><tt>IMAGE_REL_BASED_HIGH (1)</tt>: Relocate by the high 16 bytes of the base virtual address and the offset </li>
<li><tt>IMAGE_REL_BASED_LOW (2)</tt>: Relocate by the low 16 bytes of the base virtual address and the offset </li>
<li><tt>IMAGE_REL_BASED_HIGHLOW (3)</tt>: Relocate by the base virtual address and the offset </li>    </ul>
<h4>What is done in the relocation?</h4>
By relocation, some values inside the virtual memory are corrected according to the current image base by the <em>".reloc"</em> section packages.
<table cellspacing="0" cellpadding="0" border="1">
<tbody>
<tr>
<td align="center"><strong>delta_ImageBase = current_ImageBase - image_nt_headers->OptionalHeader.ImageBase</strong></td>            </tr>        </tbody>    </table>    
<pre>mem[ current_ImageBase + 0x1000 ] =   mem[ current_ImageBase + 0x1000 ] + delta_ImageBase ;mem[ current_ImageBase + 0x1016 ] =   mem[ current_ImageBase + 0x1016 ] + delta_ImageBase ;mem[ current_ImageBase + 0x1028 ] =   mem[ current_ImageBase + 0x1028 ] + delta_ImageBase ;mem[ current_ImageBase + 0x1040 ] =   mem[ current_ImageBase + 0x1040 ] + delta_ImageBase ;mem[ current_ImageBase + 0x1054 ] =  mem[ current_ImageBase + 0x1054 ] + delta_ImageBase ;...</pre>
I have employed the following code from Morphine packer to implement the relocation.
<pre><font color="#808080">    ..._reloc_fixup:    mov eax,[ebp+_p_dwImageBase]    mov edx,eax    mov ebx,eax    add ebx,[ebx+3Ch]    <span class="codeComment">// edi -> IMAGE_NT_HEADERS</span>    <span class="codeComment">// edx ->image_nt_headers->OptionalHeader.ImageBase</span>    mov ebx,[ebx+034h]    <font color="#ff0000">sub edx,ebx <span class="codeComment">// edx -> reloc_correction    // delta_ImageBase</span>    je _reloc_fixup_end    mov ebx,[ebp+_p_dwRelocationVirtualAddress]    test ebx,ebx    jz _reloc_fixup_end    add ebx,eax_reloc_fixup_block:    mov eax,[ebx+004h]          <span class="codeComment">//ImageBaseRelocation.SizeOfBlock</span>    test eax,eax    jz _reloc_fixup_end    lea ecx,[eax-008h]    shr ecx,001h    lea edi,[ebx+008h]_reloc_fixup_do_entry:        movzx eax,word ptr [edi]<span class="codeComment">//Entry</span>        push edx        mov edx,eax        shr eax,00Ch            <span class="codeComment">//Type = Entry >> 12</span>        mov esi,[ebp+_p_dwImageBase]<span class="codeComment">//ImageBase</span>        and dx,00FFFh        add esi,[ebx]        add esi,edx        pop edx_reloc_fixup_HIGH:              <span class="codeComment">// IMAGE_REL_BASED_HIGH</span>        dec eax        jnz _reloc_fixup_LOW            mov eax,edx            shr eax,010h        <span class="codeComment">//HIWORD(Delta)</span>            jmp _reloc_fixup_LOW_fixup_reloc_fixup_LOW:               <span class="codeComment">// IMAGE_REL_BASED_LOW</span>            dec eax        jnz _reloc_fixup_HIGHLOW        movzx eax,dx            <span class="codeComment">//LOWORD(Delta)</span>_reloc_fixup_LOW_fixup:            <font color="#ff0000">add word ptr [esi],ax<span class="codeComment">// mem[x] = mem[x] + delta_ImageBase</span>        jmp _reloc_fixup_next_entry_reloc_fixup_HIGHLOW:           <span class="codeComment">// IMAGE_REL_BASED_HIGHLOW</span>            dec eax        jnz _reloc_fixup_next_entry        <font color="#ff0000">add [esi],edx           <span class="codeComment">// mem[x] = mem[x] + delta_ImageBase</span>_reloc_fixup_next_entry:        inc edi        inc edi                 <span class="codeComment">//Entry++</span>        loop _reloc_fixup_do_entry_reloc_fixup_next_base:    add ebx,[ebx+004h]    jmp _reloc_fixup_block_reloc_fixup_end:    ...</font></font></font></font></pre>
<h4>7.3 Build a special import table</h4>
To support the <a href="http://en.wikipedia.org/wiki/OCX" target="new">OLE-ActiveX Control</a> registration, you should present an appropriate import table to your target OCX and DLL file. Therefore, I have established an import table by the following string:
<pre><span class="codeKeyword">const</span> <span class="codeKeyword">char</span> *sz_IT_OCX_strings[]={   "Kernel32.dll",   "LoadLibraryA",   "GetProcAddress",   "GetModuleHandleA",   0,   "User32.dll",   "GetKeyboardType",   "WindowFromPoint",   0,   "AdvApi32.dll",   "RegQueryValueExA",   "RegSetValueExA",   "StartServiceA",   0,   "Oleaut32.dll",   "SysFreeString",   "CreateErrorInfo",   "SafeArrayPtrOfIndex",   0,   "Gdi32.dll",   "UnrealizeObject",   0,   "Ole32.dll",   "CreateStreamOnHGlobal",   "IsEqualGUID",   0,   "ComCtl32.dll",   "ImageList_SetIconSize",   0,   0,};</pre>
Without these API functions, the library can not be loaded, and moreover the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/com/html/4442206b-b2ad-47d7-8add-18002c44c5a2.asp" target="new"><tt>DllregisterServer()</tt></a> and <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/com/html/b71137a7-284e-4521-a3b2-9dad9c9d3c54.asp" target="new"><tt>DllUregisterServer()</tt></a> will not operate. In <tt>CPECryptor::CryptFile</tt>, I have distinguished between EXE files and DLL files in the initialization of the new import table object during creation:
<pre><span class="codeKeyword">if</span>(( image_nt_headers->FileHeader.Characteristics             & IMAGE_FILE_DLL ) == IMAGE_FILE_DLL ){    ImportTableMaker = <span class="codeKeyword">new</span> CITMaker( IMPORT_TABLE_OCX );}<span class="codeKeyword">else</span>{    ImportTableMaker = <span class="codeKeyword">new</span> CITMaker( IMPORT_TABLE_EXE );}</pre>
 
<h3>8 Preserve the Thread Local Storage</h3>
By using Thread Local Storage (TLS), a program is able to execute a multithreaded process, This performance mostly is used by <a href="http://www.borland.com/" target="new">Borland</a> linkers: <a href="http://www.borland.com/us/products/delphi/index.html" target="new">Delphi</a> and <a href="http://www.borland.com/us/products/cbuilder/index.html" target="new">C++ Builder</a>. When you pack a PE file, you should take care to keep the TLS clean; otherwise, your packer will not support Borland Delphi and C++ Builder linked EXE files. To comprehend TLS, I refer you to Section 6.7 of the <a href="http://www.microsoft.com/whdc/system/platform/firmware/PECOFF.mspx" target="new">Microsoft Portable Executable and Common Object File Format Specification</a> document. You can observe the TLS structure by <tt>IMAGE_TLS_DIRECTORY32</tt> in <em>winnt.h</em>.
<pre><span class="codeKeyword">typedef</span> <span class="codeKeyword">struct</span> _IMAGE_TLS_DIRECTORY32 {   DWORD   StartAddressOfRawData;   DWORD   EndAddressOfRawData;   DWORD   AddressOfIndex;   DWORD   AddressOfCallBacks;   DWORD   SizeOfZeroFill;   DWORD   Characteristics;} IMAGE_TLS_DIRECTORY32, * PIMAGE_TLS_DIRECTORY32;</pre>
    <a name="more"><font color="#000000"> </font></a><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/winui/winui/windowsuserinterface/windowing/dialogboxes/dialogboxreference/dialogboxfunctions/messagebox.asp" target="new"><tt>MessageBox()</tt></a> from <em>user32.dll</em>.
To keep the TLS directory safe, I have copied it in a special place inside the loader:
<pre><font color="#808080">..._tls_dwStartAddressOfRawData:   dword_type(0xCCCCCCCC)_tls_dwEndAddressOfRawData:     dword_type(0xCCCCCCCC)_tls_dwAddressOfIndex:          dword_type(0xCCCCCCCC)_tls_dwAddressOfCallBacks:      dword_type(0xCCCCCCCC)_tls_dwSizeOfZeroFill:          dword_type(0xCCCCCCCC)_tls_dwCharacteristics:         dword_type(0xCCCCCCCC)...</font></pre>
It is necessary to correct the TLS directory entry in the Optional Header:
<pre><span class="codeKeyword">if</span>(image_nt_headers->   OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_TLS].   VirtualAddress!=0){   memcpy(&pDataTable->image_tls_directory,          image_tls_directory,          <span class="codeKeyword">sizeof</span>(IMAGE_TLS_DIRECTORY32));   dwOffset=DWORD(pData1)-DWORD(pNewSection);   dwOffset+=<span class="codeKeyword">sizeof</span>(t_DATA_1)-<span class="codeKeyword">sizeof</span>(IMAGE_TLS_DIRECTORY32);   image_nt_headers->      OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_TLS].      VirtualAddress=dwVirtualAddress + dwOffset;}</pre>
<h3>9 Inject Your Code</h3>
You are ready to place your code inside the new section. Your code is a "Hello World!" message by 
<pre><font color="#808080">...push MB_OK | MB_ICONINFORMATIONlea eax,[ebp+_p_szCaption]push eaxlea eax,[ebp+_p_szText]push eaxpush <span class="codeKeyword">NULL</span>call _jmp_MessageBox<span class="codeComment">// MessageBox(NULL, szText, szCaption, MB_OK | MB_ICONINFORMATION) ;</span>...</font></pre>
<h4>PE Maker: Step 5</h4>
Download the pemaker5.zip source files from the end of the article.
<img height="119" src="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=HELLOWORLD_GIF&ds=20060302" width="146" alt="" />
<h3>10 Conclusion</h3>
By reading this article, you have perceived how easily you can inject code to a portable executable file. You can complete the code by using the source of other packers, create a packer in the same way as <a href="http://yodap.sourceforge.net/" target="new">Yoda's Protector</a>, and make your packer undetectable by mixing up with Morphine source code. I hope that you have enjoyed this brief discussion of one part of the reverse engineering field. See you again in the next discussion!
     </pre>    </a><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/exception_pointers_str.asp" target="new"><tt>EXCEPTION_POINTERS</tt></a>, you have access to the pointer of <tt>ContextRecord</tt>. The <tt>ContextRecord</tt> has the <a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/debug/base/context_str.asp" target="new"><tt>CONTEXT</tt></a> data structure, as seen in Table 4. This is the thread context during the exception time. When you ignore the exception by <tt>EXCEPTION_CONTINUE_SEARCH (0)</tt>, the instruction pointer, as well as the context, will be set to <tt>ContextRecord</tt> to return to the previous condition. Therefore, if you change the <tt>Eip</tt> of the Win32 Thread Context to the Original Offset of Entry Point, it will come clearly into OEP.</a><a href="http://www.codeguru.com/dbfiles/get_image.php?id=11393&lbl=SCREENSHOT_JPG&ds=20060302" target="_blank">Full Size Image</a>)
