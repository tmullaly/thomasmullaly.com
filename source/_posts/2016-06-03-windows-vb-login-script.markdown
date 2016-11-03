---
layout: post
title: "Windows VB Login Script"
date: 2016-06-03 09:30:38 -0400
comments: true
categories: ["windows"]
---
I'm recording this simple script I used here.
<!-- more -->
{% codeblock lang:vbnet login.vb %}
On Error Resume Next  

Set objNetwork = CreateObject ("WScript.Network")
Set CheckDrive = objNetwork.EnumNetworkDrives ()
Set objNTInfo = CreateObject("WinNTSystemInfo")

GetComputerName = lcase(objNTInfo.ComputerName)

DomainName = ucase(objNTInfo.DomainName)

	' Lets map some drives based on the users groups
	if inGroup("Innovation") then
		MapDrive "S:", "\\myfileserver\innovation"
	end if

	if inGroup("Public") then
		MapDrive "M:", "\\myfileserver\public"
	end if

	if inGroup("Finance") then
		MapDrive "L:", "\\myfileserver\finance"
	end if

	if inGroup("Public AdminShare") then
		MapDrive "Y:", "\\myfileserver\Admin Share"
	end if

	if inGroup("Training Manual") then
		MapDrive "T:", "\\myfileserver\Training Manual"
	end if
	
	' Starup some apps on login
	Run ("C:\Program Files\PathToExe\Program.exe")
	Run ("C:\Program Files\PathToExe2\Program2.exe")


function inGroup(groupName)
	set objNetwork = CreateObject("WScript.Network")
	set oUser = GetObject("WinNT://MYDOMAIN/" & objNetwork.UserName & ",user")
   
	' Enurmerate Groups for the user
	for each oGroup in oUser.Groups
	if ucase(oGroup.Name) = ucase(groupName) then
		inGroup = true
		exit for
	end if
	next
   
	set oUser = nothing
	set oGroup = nothing
	set onet = nothing
end function



Sub Run (ByVal sFile)
	Dim shell
	Set shell = CreateObject("WScript.Shell")
	shell.Run Chr(34) & sFile & Chr(34), 1, false
	Set shell = Nothing
End Sub



Sub RunArgs (ByVal sFile,sAttr)
	Dim shell
	Set shell = CreateObject("WScript.Shell")
	shell.Run Chr(34) & sFile & Chr(34) & sAttr, 1, false
	Set shell = Nothing
End Sub



Sub MapDrive(DriveLetter,RemotePath)

	AlreadyConnected = False
	For i = 0 To CheckDrive.Count - 1 Step 2
		If CheckDrive.Item(i) = DriveLetter Then AlreadyConnected = True
	Next
	
	' This tests to see if the Drive is already mapped, and if yes then disconnects it before reconnecting it
	If AlreadyConnected = True then
		objNetwork.RemoveNetworkDrive DriveLetter
		objNetwork.MapNetworkDrive DriveLetter, RemotePath
		
	Else
		objNetwork.MapNetworkDrive DriveLetter, RemotePath
	End if
	
End sub


Function DefaultGateway  
  
	DefaultGateway = Empty  
	Dim oDG, oDGs, WMI  
	Set WMI = GetObject("winmgmts:\\.\root\cimv2")  
	Set oDGs = WMI.ExecQuery _  
	("Select * from Win32_NetworkAdapterConfiguration where IPEnabled=TRUE")  
	For Each oDG in oDGs  
	If Not IsNull(oDG.DefaultIPGateway) Then  
	If Not oDG.defaultIPGateway(0) = "0.0.0.0" Then  
	DefaultGateway = oDG.DefaultIPGateway(0)  
	Exit For  
	End If  
	End If  
	Next  

End Function  
{% endcodeblock %}

