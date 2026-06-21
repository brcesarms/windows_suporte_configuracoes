
# Suporte


### Exibindo o nome do computador e o nome do usuario atual
```shell
Get-CimInstance -ClassName Win32_Desktop
  
```

---


### Desabilitar Power Manage
```shell
powercfg.exe /SETACVALUEINDEX SCHEME_CURRENT SUB_VIDEO VIDEOIDLE 0
powercfg.exe /SETDCVALUEINDEX SCHEME_CURRENT SUB_VIDEO VIDEOIDLE 0
powercfg.exe /SETACVALUEINDEX SCHEME_CURRENT SUB_SLEEP STANDBYIDLE 0
powercfg.exe /SETDCVALUEINDEX SCHEME_CURRENT SUB_SLEEP STANDBYIDLE 0
powercfg.exe /SETACVALUEINDEX SCHEME_CURRENT SUB_SLEEP HIBERNATEIDLE 0
powercfg.exe /SETDCVALUEINDEX SCHEME_CURRENT SUB_SLEEP HIBERNATEIDLE 0

```

---


<!--
#### 1D - Creating restore point
* Source:
* <https://youtu.be/vi2lAsxo3Ws>

```shell
New-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SystemRestore" -Name "SystemRestorePointCreationFrequency" -PropertyType DWord -Value 0

Enable-ComputerRestore -Drive "C:\"

# Cria uma nova tarefa agendada
$TaskAction = New-ScheduledTaskAction -Execute 'powershell.exe' -Argument '-ExecutionPolicy Bypass -Command "Checkpoint-Computer -Description \"My Restore Point Startup\" -RestorePointType \"MODIFY_SETTINGS\""'
$Trigger = New-ScheduledTaskTrigger -AtStartup

Register-ScheduledTask -Action $TaskAction -Trigger $Trigger -TaskName "MyRestorePointTask" -Description "Tarefa para criar ponto de restauração ao iniciar o computador" -RunLevel Highest

```
 [⬆️](#️⃣-contents)

&nbsp;

#### 1E - Add Credential
```shell
$username = "padrao"
$password = "123456"
$cmdkeyCommand = "cmdkey /add:192.168.0.34 /user:$username /pass:$password"
Invoke-Expression -Command $cmdkeyCommand

```


-->

### Habilitar usuario administrador
```shell
Enable-LocalUser -Name "Administrador"
Set-LocalUser -Name "Administrador" -Password (ConvertTo-SecureString -String "absemsau*" -AsPlainText -Force)

```

---

### Gerenciar usuarios
```shell
# Removing user "user" from admin group and making it default user
net localgroup administrators user /delete
net localgroup users user /add

# Setting the password for the user "user" to not expire and preventing the user from being able to change the password
wmic useraccount where "name='user'" set passwordexpires=false
net user user /passwordchg:no

```

---

### Renomear computador
```shell
Write-Host "Rename Computer"
$RENAME = Read-Host "escreva nome do computador"
Rename-Computer -NewName $RENAME
-Restart  

```

---

### CHKDSK
```shell
chkdsk c: /r

```

### ou
```shell
Repair-Volume C -OfflineScanAndFix

```

---

### Defrag Windows
* Running DEFRAG HD
```shell
defrag C: /v

```

### ou
```shell
Optimize-Volume -DriveLetter C -Defrag -TierOptimize -Verbose

```

* Running DEFRAG SSD
```shell
Optimize-Volume -DriveLetter C -ReTrim -Verbose  
  
```

---

#### SFC / DISM
```shell
sfc /scannow

```

```shell
DISM /Online /Cleanup-image /Restorehealth  

```

### ou
```shell
Repair-WindowsImage -Online -StartComponentCleanup -RestoreHealth

```

---

### Update User Policy
```shell
gpupdate /force

```
### ou
```shell
Invoke-Expression -Command "gpupdate /force"

```

---

### Reiniciar print spooler service
```shell
# Stop print spooler service
Stop-Service -Name Spooler -Force

# To delete the files
Remove-Item -Path "$env:SystemRoot\System32\spool\PRINTERS\*.*"

# Start print spooler service
Start-Service -Name Spooler

# Restart print spooler service
Restart-Service -Name Spooler -Force  
  
```

---

### Refresh Interface Network
```shell
#flushdns
ipconfig /flushdns

#release
ipconfig /release

#renew
ipconfig /renew

# Desable Wi-Fi... 
NETSH interface set interface name=Wi-Fi admin=DISABLE 
 
# Enable Wi-Fi... 
NETSH interface set interface name=Wi-Fi admin=ENABLE 
 
# Desable Ethernet... 
NETSH interface set interface name=Ethernet admin=DISABLE 
 
# Enable Ethernet... 
NETSH interface set interface name=Ethernet admin=ENABLE  

```

---

### Network Folder Mapping
```shell
# \\srv-storage-01\semsau
net use \\srv-storage-01\semsau$ /PERSISTENT:YES
#New-PSDrive –Name “V” –PSProvider FileSystem –Root “\\srv-storage-01\semsau$ ” –Persist  

```

```shell
# \\srv-storage-01\semsau-fms$
net use \\srv-storage-01\semsau-fms$ /PERSISTENT:YES

```

```shell
# \\srv-storage-01\semsau-pad$
net use \\srv-storage-01\semsau-pad$ /PERSISTENT:YES

```

```shell
# \\srv-storage-01\semsau-atencao-basica$
net use \\srv-storage-01\semsau-atencao-basica$ /PERSISTENT:YES

```

```shell
# \\srv-storage-01\semsau-visa$
net use \\srv-storage-01\semsau-visa$ /PERSISTENT:YES

```

---

### Enable Dark Theme
```shell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize /v AppsUseLightTheme /t REG_DWORD /d 0 /f
  
```

---

#### Turn Off Windows Activity History
```shell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced /v Start_TrackDocs /t REG_DWORD /d 0 /f
  
```

---

#### Disable Background apps
```shell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications /v GlobalUserDisabled /t REG_DWORD /d 1 /f
  
```
