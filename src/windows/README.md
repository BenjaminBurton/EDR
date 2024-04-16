## Windows Config

#### Disable Windows Defender (Windows 11 is tricky)
		
		1. Disable Tamper Protection
		   
		   a. clic
		   b. Click Settings
		   c. Click Privacy & Security on left side
		   d. Click Windows Security
		   e. Click Virus & threat protection
		   f. Under Virus & threat protection click Manage settings
		   g. Toggle OFF the Tamper Protection  & click yes
		   h. Now Toggle OFF every other option
		
		2. Permanentely Disable Defender via Group Policy Editor
		   
		   a. In the search bar Type cmd
		   b. Right Click Command Prompt & Run as Admin
		   c. Run the following command
			   gpedit.msc + enter
		d. Inside Local Group Policy Editor
			e. Click Computer configuration > Admin Templates >
			Windows Components > Microsoft Defender Antivirus
		f. Double click Turn off Microsoft Defender Antivirus
		g. Select Enabled (this policy prevents defender scans)
		h. Click Apply
		i. Click OK
		
		3. Permanently Disable Defender
		   
		   a. In the admin Command Prompt paste or type this command
			
			REG ADD "hklm\software\policies\microsoft\windows 
			defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
		
		4. Boot into Safe Mode to disable ALL Defender services
		   
		   a. Click in the search bar & type msconfig + enter
		   b. Go to the Boot tab & select Boot Options
		   c. Check the Safe boot and Minimal box
		   d. Click Apply
		   e. The system will now restart in Safe Mode
		
		5. In Safe mode disable services via the registry
		   
		   a. Click in the search bar & Type regedit + enter
		   b. We will now change each registry value below to 4
		   Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\
		   Sense
		   WdBoot
		   WinDefend
		   WdNisDrv
		   WdNisSvc
		   WdFilter
		   
		6. Now leave Safe Mode how we got into it
		   a. Click in the search bar & Type msconfig + enter
		   b. Go to Boot tab & select Boot Options
		   c. Uncheck the Safe boot box
		   d. Click Apply
		   e. The System will restart back into the normal desktop


### Prevent VM from going into standby mode
	
	Pull up a Admin Command Prompt and Type the following 
	command to prevent the VM from going into sleep or 
	standby mode
	
		powercfg /change standby-timeout-ac 0
		powercfg /change standby-timeout-dc 0
		powercfg /change monitor-timeout-ac 0
		powercfg /change monitor-timeout-dc 0
		powercfg /change hibernate-timeout-ac 0
		powercfg /change hibernate-timeout-dc 0

#### Install Sysmon in Windows VM (for Granular Telemetry)
	
	Launch Admin PowerShell console
		a. type powershell in search bar
		b. Right click Windows Powershell
		c. Click Run as Administrator
	
	Download Sysmon w/ this command
	
	Invoke-WebRequest -Uri 
	https://download.sysinternals.com/files/Symon.zip -OutFile 
	C:\Windows\Temp\Sysmon.zip


	Unzip the Sysmon zip file
	
	Expand-Archive -LiteralPath C:\Windows\Temp\Sysmon.zip 
	-DestinationPath C:\Windows\Temp\Sysmon
	
	
	Download SwiftOnSecurity's Sysmon config from GitHub
	
	Invoke-WebRequest -Uri 
	https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-
	config/master/sysmonconfig-export.xml -OutFile 
	C:\Windows\Temp\Sysmon\sysmonconfig.xml
	
	
	Install Sysmon w/Swift's config 
	
	C:\Windows\Temp\Sysmon\Sysmon64.exe -accepteula -i 
	C:\Windows\Temp\Sysmon\sysmonconfig.xml
	
	
	Validate Sysmon64 service is installed and running
	
	Get-Service sysmon64
	
	
	Check the presence of Sysmon Event Logs
	
	Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational"
	-MaxEvents 10


### Install LimaCharlie EDR on Windows VM

#### Create LimaCharlie Account & Login

	
	Login to LimaCharlie & Create an Organization
	
	a. Give it a unique name
	b. Data residency Region (closest to you)
	c. Disable Demo Configuration
	d. Set Template to "Extended Detection & Response Standard"
	
	
	After creating the organization 
	
	a. Add a Sensor
	b. Select Windows
	c. Provide a description (Windows Lab or something)
	d. Click create
	e. Select the Installation key we created from step c
	f. Select the x86-64(.exe) sensor
	g. DO NOT CLICK "download the selected installer"
	h. Inside our Windows VM open a Admin PowerShell Prompt
	i. Type these commands (User is the username of the VM)
		
		cd C:\Users\User\Downloads
		
	Invoke-WebRequest -Uri 
	https://downloads.limacharlie.io/sensor/windows/64 -OutFile 
	C:\Users\User\Downloads\lc_sensor.exe
	
	
	Shift into a standard command prompt by typing
		cmd.exe
	
	
	Now we can copy & paste the installation key to run installer
	from step g above where it says DO NOT CLICK
	The installation key will look similar to this
	
	lc_sensor.exe -i AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
	
	
	Ignore the ERROR saying "service installed" 
	this is expected output but..... if you experience an error
	installing the EXE try the MSI from the installer dialog
	
	
	Give it a few seconds and you should see the connection
	it will say Detected new sensor!
	
	

#### Configure LimaCharlie 

	To ship event logs alongside its own EDR telemetry

	a. Left-side menu click "Artifact Collection"
	b. Next to Artifact Collection Rules click Add Rule
	c. give it a name something like (windows-sysmon-logs)
	d. select Windows for Platform
	e. wel://Microsoft-Windows-Sysmon/Operational:*
		for Path Pattern
	f. 10 for Retention Period
	g. click Save Rule
	
	LimaCharlie will now start shipping Sysmon logs Providing 
	a wealth of EDR-like Telemetry, some of which is redundant to 
	LimaCharlie's own telemetry
	
	A reason we are ingesting Sysmon logs is that the built-in
	Sigma rules we enabled previously largely depend on Sysmon logs
	as thats what most of them were written for
	
	Close all open windows on the VM and lets move 
	on over to the Ubuntu Linux VM.
	
	PRO TIP: Take a Snapshot of you VM so you can revert to this
	clean state in case it gets hosed later.