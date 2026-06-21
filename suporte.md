
---



&nbsp;



- [Habilitar a execução de scripts do PowerShell](#habilitar-a-execução-de-scripts-do-powershell)
- [Exibindo o nome do computador e o nome do usuario atual](#exibindo-o-nome-do-computador-e-o-nome-do-usuario-atual)
- [Desabilitar Power Manage](#desabilitar-power-manage)
- [Habilitar usuario administrador](#habilitar-usuario-administrador)
- [Gerenciar usuarios](#gerenciar-usuarios)
- [Renomear computador](#renomear-computador)
- [CHKDSK](#chkdsk)
- [Defrag Windows](#defrag-windows)
- [SFC e DISM](#sfc-e-dism)
- [Atualizar políticas de usuário](#atualizar-políticas-de-usuário)
- [Reiniciar print spooler service](#reiniciar-print-spooler-service)
- [Atualizar interface de rede](#atualizar-interface-de-rede)
- [Mapeamento de pasta de rede](#mapeamento-de-pasta-de-rede)
- [Ativar tema escuro](#ativar-tema-escuro)
- [Desativar o histórico de atividades do Windows](#desativar-o-histórico-de-atividades-do-windows)
- [Desabilitar aplicativos em segundo plano](#desabilitar-aplicativos-em-segundo-plano)



&nbsp;



---



&nbsp;



#### Habilitar a execução de scripts do PowerShell
```PowerShell
# Para verificar as configurações atuais da política de execução
Get-ExecutionPolicy
  
```
```PowerShell
# Para definir a política como Irrestrita
set-ExecutionPolicy unrestricted
  
```

---


#### Exibindo o nome do computador e o nome do usuario atual
```PowerShell
Get-CimInstance -ClassName Win32_Desktop
  
```

---


#### Desabilitar Power Manage
```PowerShell
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

#### Habilitar usuario administrador
```PowerShell
Enable-LocalUser -Name "Administrador"
Set-LocalUser -Name "Administrador" -Password (ConvertTo-SecureString -String "absemsau*" -AsPlainText -Force)

```

---

#### Gerenciar usuarios
```PowerShell
# Removing user "user" from admin group and making it default user
net localgroup administrators user /delete
net localgroup users user /add

# Setting the password for the user "user" to not expire and preventing the user from being able to change the password
wmic useraccount where "name='user'" set passwordexpires=false
net user user /passwordchg:no

```

---

#### Renomear computador
```PowerShell
Write-Host "Rename Computer"
$RENAME = Read-Host "escreva nome do computador"
Rename-Computer -NewName $RENAME
-Restart  
 
```

---

#### CHKDSK
```PowerShell
chkdsk c: /r
 
```

ou

```PowerShell
Repair-Volume C -OfflineScanAndFix
 
```

---

#### Defrag Windows
```PowerShell
# Running DEFRAG HD
defrag C: /v
 
```

ou

```PowerShell
Optimize-Volume -DriveLetter C -Defrag -TierOptimize -Verbose
 
```

```PowerShell
# Running DEFRAG SSD
Optimize-Volume -DriveLetter C -ReTrim -Verbose  
  
```

---

#### SFC e DISM
```shell
sfc /scannow

```

```PowerShell
DISM /Online /Cleanup-image /Restorehealth  

```

ou

```PowerShell
Repair-WindowsImage -Online -StartComponentCleanup -RestoreHealth

```

---

#### Atualizar políticas de usuário
```PowerShell
gpupdate /force

```

ou

```PowerShell
Invoke-Expression -Command "gpupdate /force"

```

---

#### Reiniciar print spooler service
```PowerShell
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

#### Atualizar interface de rede
```PowerShell
# flushdns
ipconfig /flushdns

# release
ipconfig /release

# renew
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

#### Mapeamento de pasta de rede
```PowerShell
# \\srv-storage-01\semsau
net use \\srv-storage-01\semsau$ /PERSISTENT:YES
#New-PSDrive –Name “V” –PSProvider FileSystem –Root “\\srv-storage-01\semsau$ ” –Persist  

```

```PowerShell
# \\srv-storage-01\semsau-fms$
net use \\srv-storage-01\semsau-fms$ /PERSISTENT:YES

```

```PowerShell
# \\srv-storage-01\semsau-pad$
net use \\srv-storage-01\semsau-pad$ /PERSISTENT:YES

```

```PowerShell
# \\srv-storage-01\semsau-atencao-basica$
net use \\srv-storage-01\semsau-atencao-basica$ /PERSISTENT:YES

```

```PowerShell
# \\srv-storage-01\semsau-visa$
net use \\srv-storage-01\semsau-visa$ /PERSISTENT:YES

```

---

#### Ativar tema escuro
```PowerShell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize /v AppsUseLightTheme /t REG_DWORD /d 0 /f
  
```

---

#### Desativar o histórico de atividades do Windows
```PowerShell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced /v Start_TrackDocs /t REG_DWORD /d 0 /f
  
```

---

#### Desabilitar aplicativos em segundo plano
```PowerShell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications /v GlobalUserDisabled /t REG_DWORD /d 1 /f
  
```
