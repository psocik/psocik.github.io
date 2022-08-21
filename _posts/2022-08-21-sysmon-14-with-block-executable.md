Microsoft introduced new version of Sysinternals software - Sysmon version 14.0 - [https://docs.microsoft.com/en-gb/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-gb/sysinternals/downloads/sysmon).

 This major update to Sysmon, an advanced host monitoring tool, adds a new event type, *FileBlockExecutable* that prevents processes from creating executable files in specified locations.

[Neo23x0 ](https://github.com/Neo23x0)released new sysmon config with blocking rules: [https://github.com/Neo23x0/sysmon-config/blob/master/sysmonconfig-export-block.xml](https://github.com/Neo23x0/sysmon-config/blob/master/sysmonconfig-export-block.xml)

I will present some changes what was made:

There is new section in file:

```html
	<!--SYSMON EVENT ID 27 : FILE BLOCK [FileBlockExecutable]-->
	<RuleGroup name="ImageBlock" groupRelation="or">
		<FileBlockExecutable onmatch="include">
          
          ...
          
        </FileBlockExecutable>
	</RuleGroup>
```

This rule will trigger when any of conditions will be present in the system. Developers divided rules in few groups.

```html
			<!-- Executables dropped to suspicious folders -->
			<TargetFilename condition="begin with">C:\Users\Public\</TargetFilename> <!-- often used staging directories; could cause false positives --> 
			<TargetFilename condition="begin with">C:\Perflogs\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\Fonts\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\debug\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\Tasks\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\tracing\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\Help\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\Logs\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="begin with">C:\Windows\System32\spool\SERVERS\</TargetFilename> <!-- often used in exploits against print spooler --> 
			<TargetFilename condition="begin with">C:\Windows\System32\spool\PRINTERS\</TargetFilename> <!-- often used in exploits against print spooler --> 
			<TargetFilename condition="begin with">C:\Windows\Help\</TargetFilename> <!-- often used staging directories --> 
			<TargetFilename condition="contains all">C:\Users\;\Music\</TargetFilename> <!-- often used staging directories in User folders --> 
			<TargetFilename condition="contains all">C:\Users\;\Pictures\</TargetFilename> <!-- often used staging directories in User folders --> 
			<TargetFilename condition="contains all">C:\Users\;\Videos\</TargetFilename> <!-- often used staging directories in User folders --> 
			<TargetFilename condition="contains all">C:\Users\;\Contacts\</TargetFilename> <!-- often used staging directories in User folders --> 
            
```

First part contains several paths where it is common to store malware for execution. These rules will prevent any process to store executable block in those paths. Last four are a bit different, because we do want to block saving for any user in the system, there is condition "**contains all**" which match with: must be in *C:\\Users\\* and has to be in *\\Contact\\.*

When I tried to save copy of file cmd.exe in My profile in Music folder sysmon blocked the action.

[![2022-08-21-sysmon-14-with-block-executable-1.png](/images/2022-08-21-sysmon-14-with-block-executable/2022-08-21-sysmon-14-with-block-executable-1.png)](/images/2022-08-21-sysmon-14-with-block-executable/2022-08-21-sysmon-14-with-block-executable-1.png)

```html
			<!-- Executables double extensions -->
			<TargetFilename condition="end with">.pdf.exe</TargetFilename>
			<TargetFilename condition="end with">.doc.exe</TargetFilename>
			<TargetFilename condition="end with">.docx.exe</TargetFilename>
			<TargetFilename condition="end with">.xls.exe</TargetFilename>
			<TargetFilename condition="end with">.xlsx.exe</TargetFilename>
			<TargetFilename condition="end with">.xlsm.exe</TargetFilename>
			<TargetFilename condition="end with">.docm.exe</TargetFilename>
			<TargetFilename condition="end with">.ppt.exe</TargetFilename>
			<TargetFilename condition="end with">.pptx.exe</TargetFilename>
			<TargetFilename condition="end with">.txt.exe</TargetFilename>
			<TargetFilename condition="end with">.rtf.exe</TargetFilename>
			<TargetFilename condition="end with">.htm.exe</TargetFilename>
			<TargetFilename condition="end with">.html.exe</TargetFilename>
			<TargetFilename condition="end with">.iso.exe</TargetFilename>
			<TargetFilename condition="end with">.zip.exe</TargetFilename>
			<TargetFilename condition="end with">.rar.exe</TargetFilename>
			<TargetFilename condition="end with">.7z.exe</TargetFilename>
            
```

Second section prevent storing files with double extension, and it must be executable - Sysmon event id 27 condition. Any process will not be able to store executable files.

I used notepad++ to open cmd.exe file and tried to store it on my desktop with name: ping.pdf.exe. Sysmon blocked this event.

[![2022-08-21-sysmon-14-with-block-executable-2.png](/images/2022-08-21-sysmon-14-with-block-executable/2022-08-21-sysmon-14-with-block-executable-2.png)](/images/2022-08-21-sysmon-14-with-block-executable/2022-08-21-sysmon-14-with-block-executable-2.png)

Next section of config file contains hashes of well know samples of malware.

```html
<!-- Hacktool Blocks based on Imphashes -->
			<Hashes condition="contains">IMPHASH=BCCA3C247B619DCD13C8CDFF5F123932</Hashes> <!-- PetitPotam -->
			<Hashes condition="contains">IMPHASH=3A19059BD7688CB88E70005F18EFC439</Hashes> <!-- PetitPotam -->
			<Hashes condition="contains">IMPHASH=bf6223a49e45d99094406777eb6004ba</Hashes> <!-- PetitPotam -->
			<Hashes condition="contains">IMPHASH=0C106686A31BFE2BA931AE1CF6E9DBC6</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=0D1447D4B3259B3C2A1D4CFB7ECE13C3</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=1B0369A1E06271833F78FFA70FFB4EAF</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=4C1B52A19748428E51B14C278D0F58E3</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=4D927A711F77D62CEBD4F322CB57EC6F</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=66EE036DF5FC1004D9ED5E9A94A1086A</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=672B13F4A0B6F27D29065123FE882DFC</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=6BBD59CEA665C4AFCC2814C1327EC91F</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=725BB81DC24214F6ECACC0CFB36AD30D</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=9528A0E91E28FBB88AD433FEABCA2456</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=9DA6D5D77BE11712527DCAB86DF449A3</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=A6E01BC1AB89F8D91D9EAB72032AAE88</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=B24C5EDDAEA4FE50C6A96A2A133521E4</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=D21BBC50DCC169D7B4D0F01962793154</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=FCC251CCEAE90D22C392215CC9A2D5D6</Hashes> <!-- Mimikatz -->
			<Hashes condition="contains">IMPHASH=23867A89C2B8FC733BE6CF5EF902F2D1</Hashes> <!-- JuicyPotato  -->
			<Hashes condition="contains">IMPHASH=A37FF327F8D48E8A4D2F757E1B6E70BC</Hashes> <!-- JuicyPotato  -->
			<Hashes condition="contains">IMPHASH=6118619783FC175BC7EBECFF0769B46E</Hashes> <!-- RoguePotato -->
			<Hashes condition="contains">IMPHASH=959A83047E80AB68B368FDB3F4C6E4EA</Hashes> <!-- RoguePotato -->
			<Hashes condition="contains">IMPHASH=563233BFA169ACC7892451F71AD5850A</Hashes> <!-- RoguePotato -->
			<Hashes condition="contains">IMPHASH=87575CB7A0E0700EB37F2E3668671A08</Hashes> <!-- RoguePotato -->
			<Hashes condition="contains">IMPHASH=13F08707F759AF6003837A150A371BA1</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=1781F06048A7E58B323F0B9259BE798B</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=233F85F2D4BC9D6521A6CAAE11A1E7F5</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=24AF2584CBF4D60BBE5C6D1B31B3BE6D</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=632969DDF6DBF4E0F53424B75E4B91F2</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=713C29B396B907ED71A72482759ED757</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=749A7BB1F0B4C4455949C0B2BF7F9E9F</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=8628B2608957A6B0C6330AC3DE28CE2E</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=8B114550386E31895DFAB371E741123D</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=94CB940A1A6B65BED4D5A8F849CE9793</Hashes> <!-- PwDumpX -->
			<Hashes condition="contains">IMPHASH=9D68781980370E00E0BD939EE5E6C141</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=B18A1401FF8F444056D29450FBC0A6CE</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=CB567F9498452721D77A451374955F5F</Hashes> <!-- Pwdump -->
			<Hashes condition="contains">IMPHASH=730073214094CD328547BF1F72289752</Hashes> <!-- Htran -->
			<Hashes condition="contains">IMPHASH=17B461A082950FC6332228572138B80C</Hashes> <!-- Cobalt Strike beacons -->
			<Hashes condition="contains">IMPHASH=DC25EE78E2EF4D36FAA0BADF1E7461C9</Hashes> <!-- Cobalt Strike beacons -->
			<Hashes condition="contains">IMPHASH=819B19D53CA6736448F9325A85736792</Hashes> <!-- Cobalt Strike beacons -->
			<Hashes condition="contains">IMPHASH=829DA329CE140D873B4A8BDE2CBFAA7E</Hashes> <!-- Cobalt Strike beacons -->
			<Hashes condition="contains">IMPHASH=C547F2E66061A8DFFB6F5A3FF63C0A74</Hashes> <!-- PPLDump -->
			<Hashes condition="contains">IMPHASH=0588081AB0E63BA785938467E1B10CCA</Hashes> <!-- PPLDump -->
			<Hashes condition="contains">IMPHASH=0D9EC08BAC6C07D9987DFD0F1506587C</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=BC129092B71C89B4D4C8CDF8EA590B29</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=4DA924CF622D039D58BCE71CDF05D242</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=E7A3A5C377E2D29324093377D7DB1C66</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=9A9DBEC5C62F0380B4FA5FD31DEFFEDF</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=AF8A3976AD71E5D5FDFB67DDB8DADFCE</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=0C477898BBF137BBD6F2A54E3B805FF4</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=0CA9F02B537BCEA20D4EA5EB1A9FE338</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=3AB3655E5A14D4EEFC547F4781BF7F9E</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=E6F9D5152DA699934B30DAAB206471F6</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=3AD59991CCF1D67339B319B15A41B35D</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=FFDD59E0318B85A3E480874D9796D872</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=0CF479628D7CC1EA25EC7998A92F5051</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=07A2D4DCBD6CB2C6A45E6B101F0B6D51</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=D6D0F80386E1380D05CB78E871BC72B1</Hashes> <!-- NanoDump -->
			<Hashes condition="contains">IMPHASH=38D9E015591BBFD4929E0D0F47FA0055</Hashes> <!-- HandleKatz -->
			<Hashes condition="contains">IMPHASH=0E2216679CA6E1094D63322E3412D650</Hashes> <!-- HandleKatz -->
			<Hashes condition="contains">IMPHASH=ADA161BF41B8E5E9132858CB54CAB5FB</Hashes> <!-- DripLoader -->
			<Hashes condition="contains">IMPHASH=2A1BC4913CD5ECB0434DF07CB675B798</Hashes> <!-- DripLoader -->
			<Hashes condition="contains">IMPHASH=11083E75553BAAE21DC89CE8F9A195E4</Hashes> <!-- DripLoader -->
			<Hashes condition="contains">IMPHASH=A23D29C9E566F2FA8FFBB79267F5DF80</Hashes> <!-- DripLoader -->
			<Hashes condition="contains">IMPHASH=4A07F944A83E8A7C2525EFA35DD30E2F</Hashes> <!-- CreateMiniDump -->
			<Hashes condition="contains">IMPHASH=767637C23BB42CD5D7397CF58B0BE688</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=14C4E4C72BA075E9069EE67F39188AD8</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=3C782813D4AFCE07BBFC5A9772ACDBDC</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=7D010C6BB6A3726F327F7E239166D127</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=89159BA4DD04E4CE5559F132A9964EB3</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=6F33F4A5FC42B8CEC7314947BD13F30F</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=5834ED4291BDEB928270428EBBAF7604</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=5A8A8A43F25485E7EE1B201EDCBC7A38</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=DC7D30B90B2D8ABF664FBED2B1B59894</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=41923EA1F824FE63EA5BEB84DB7A3E74</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=3DE09703C8E79ED2CA3F01074719906B</Hashes> <!-- UACMe Akagi -->
			<Hashes condition="contains">IMPHASH=A53A02B997935FD8EEDCB5F7ABAB9B9F</Hashes> <!-- WCE -->
			<Hashes condition="contains">IMPHASH=E96A73C7BF33A464C510EDE582318BF2</Hashes> <!-- WCE -->
			<Hashes condition="contains">IMPHASH=32089B8851BBF8BC2D014E9F37288C83</Hashes> <!-- Sliver Stagers -->
			<Hashes condition="contains">IMPHASH=09D278F9DE118EF09163C6140255C690</Hashes> <!-- Dumpert -->
			<Hashes condition="contains">IMPHASH=03866661686829D806989E2FC5A72606</Hashes> <!-- Dumpert -->
			<Hashes condition="contains">IMPHASH=E57401FBDADCD4571FF385AB82BD5D6D</Hashes> <!-- Dumpert -->
```

Next section of config file contains process names which should not save executable files in the system in any folder.

```html
			<!-- Microsoft Office Programs Dropping Executables -->
			<Image condition="image">winword.exe</Image>
			<Image condition="image">excel.exe</Image>
			<Image condition="image">powerpnt.exe</Image>
			<Image condition="image">msaccess.exe</Image>
			<Image condition="image">mspub.exe</Image>
			<Image condition="image">eqnedt32.exe</Image>
			<Image condition="image">visio.exe</Image>
			<Image condition="image">wordpad.exe</Image>
			<Image condition="image">wordview.exe</Image>

			<!-- LOLBINs that can be used to download executables -->
			<Image condition="image">certutil.exe</Image>
			<Image condition="image">certoc.exe</Image>
			<Image condition="image">CertReq.exe</Image>
			<!-- <Image condition="image">bitsadmin.exe</Image> (depends on the environment; comment in if you're sure that bitsadmin doesn't do that in your env) -->
			<Image condition="image">Desktopimgdownldr.exe</Image>
			<Image condition="image">esentutl.exe</Image>
			<Image condition="image">expand.exe</Image>
			<Image condition="image">finger.exe</Image>

			<!-- Executables that should never drop an executable to disk (but may after a previous process injection or if it's malware that uses a legitimate name)-->
			<Image condition="image">notepad.exe</Image>
			<Image condition="image">AcroRd32.exe</Image>
			<Image condition="image">RdrCEF.exe</Image>
			<Image condition="image">mshta.exe</Image>
			<Image condition="image">hh.exe</Image>
```

**How to install and run Sysmon?**

[![2022-08-21-sysmon-14-with-block-executable-3.png](/images/2022-08-21-sysmon-14-with-block-executable/2022-08-21-sysmon-14-with-block-executable-3.png)](/images/2022-08-21-sysmon-14-with-block-executable/2022-08-21-sysmon-14-with-block-executable-3.png)

Just download from first link in the article sysmon software. Then download <span class="author flex-self-stretch">Neo23x0</span> config *sysmonconfig-export-block.xml*.

Extract sysmon zip, copy config file to sysmon folder and start the terminal/powershell console. Run sysmon with parameters:

```powershell
.\Sysmon64.exe -accepteula -i .\sysmonconfig-export-block.xml
```

Now your system is protected with all the rules implemented by developers. This config file also contains lots of great stuff for detecting malicious behavior in your system. All logs are written to event log: **Microsoft-Windows-Sysmon/Operational**

You can display logs with command:

```powershell
Get-WinEvent -LogName Microsoft-Windows-Sysmon/Operational | select id, message | fl
```
Enjoy detection of malicious executable blocks !
**See you soon !**
