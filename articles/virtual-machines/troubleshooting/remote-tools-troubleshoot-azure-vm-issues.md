---
title: Używanie narzędzi zdalnych do rozwiązywania problemów z maszynami wirtualnymi platformy Azure | Dokumenty firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920012"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Rozwiązywanie problemów z maszynami wirtualnymi usługi Azure za pomocą narzędzi zdalnych

Podczas rozwiązywania problemów na maszynie wirtualnej platformy Azure (VM), można połączyć się z maszyną wirtualną przy użyciu narzędzi zdalnych, które zostały omówione w tym artykule zamiast przy użyciu protokołu RDP (Remote Desktop Protocol).

## <a name="serial-console"></a>Konsola szeregowa

Użyj [konsoli szeregowej dla maszyn wirtualnych platformy Azure](serial-console-windows.md) do uruchamiania poleceń na zdalnej maszynie wirtualnej platformy Azure.

## <a name="remote-cmd"></a>Zdalny cmd

Pobierz [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Połącz się z maszyną wirtualną, uruchamiając następujące polecenie:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Polecenie musi być uruchamiane na komputerze, który jest w tej samej sieci wirtualnej.
>* DIP lub HostName może \<służyć do zastąpienia> komputera.
>* Parametr -s zapewnia, że polecenie jest wywoływane przy użyciu konta systemowego (uprawnienia administratora).
>* PsExec używa portów TCP 135 i 445. W rezultacie oba porty muszą być otwarte na zaporze.

## <a name="run-command"></a>Uruchom polecenie 

Aby uzyskać więcej informacji na temat używania funkcji polecenia uruchom do uruchamiania skryptów na maszynie wirtualnej, zobacz [Uruchamianie skryptów programu PowerShell w maszynie Wirtualnej systemu Windows za pomocą polecenia uruchom](../windows/run-command.md).

## <a name="custom-script-extension"></a>Rozszerzenie niestandardowego skryptu

Za pomocą funkcji Niestandardowe rozszerzenie skryptu można uruchomić skrypt niestandardowy na docelowej maszynie wirtualnej. Aby korzystać z tej funkcji, muszą być spełnione następujące warunki:

* Maszyna wirtualna ma łączność.
* Agent maszyny wirtualnej platformy Azure jest zainstalowany i działa zgodnie z oczekiwaniami na maszynie Wirtualnej.
* Rozszerzenie nie zostało wcześniej zainstalowane na maszynie Wirtualnej.
 
  Rozszerzenie wstrzykuje skrypt tylko po raz pierwszy, że jest używany. Jeśli użyjesz tej funkcji później, rozszerzenie rozpoznaje, że był już używany i nie przekazuje nowego skryptu.

Przekaż skrypt do konta magazynu i wygeneruj własny kontener. Następnie uruchom następujący skrypt w programie Azure PowerShell na komputerze, który ma łączność z maszyną wirtualną.

### <a name="for-classic-deployment-model-vms"></a>Dla klasycznych modeli wdrażania maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Dla maszyn wirtualnych usługi Azure Resource Manager

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Zdalny program PowerShell

>[!NOTE]
>Port TCP 5986 (HTTPS) musi być otwarty, aby można było użyć tej opcji.
>
>W przypadku maszyn wirtualnych usługi Azure Resource Manager należy otworzyć port 5986 w sieciowej grupie zabezpieczeń (NSG). Aby uzyskać więcej informacji, zobacz Grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych RDFE musi istnieć punkt końcowy z portem prywatnym (5986) i portem publicznym. Następnie należy również otworzyć ten publiczny port na nsg.

### <a name="set-up-the-client-computer"></a>Konfigurowanie komputera klienckiego

Aby zdalnie połączyć się z maszyną wirtualną za pomocą programu PowerShell, należy najpierw skonfigurować komputer kliencki, aby zezwolić na połączenie. Aby to zrobić, dodaj maszynę wirtualną do listy zaufanych hostów programu PowerShell, uruchamiając odpowiednie polecenie następujące.

Aby dodać jedną maszynę wirtualną do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Aby dodać wiele maszyn wirtualnych do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Aby dodać wszystkie komputery do listy hostów zaufanych:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Włączanie remoteps na maszynie wirtualnej

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania użyj rozszerzenia skryptu niestandardowego, aby uruchomić następujący skrypt:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

W przypadku maszyn wirtualnych usługi Azure Resource Manager użyj poleceń uruchamiania z portalu, aby uruchomić skrypt EnableRemotePS:

![Uruchom polecenie ](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Uruchom następujące polecenie na podstawie lokalizacji komputera klienckiego:

* Poza siecią wirtualną lub wdrożeniem

  * W przypadku maszyny Wirtualnej utworzonej przy użyciu klasycznego modelu wdrażania uruchom następujące polecenie:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * W przypadku maszyny Wirtualnej usługi Azure Resource Manager należy najpierw dodać nazwę DNS do publicznego adresu IP. Aby uzyskać szczegółowe kroki, zobacz [Tworzenie w pełni kwalifikowanej nazwy domeny w portalu Azure dla maszyny Wirtualnej systemu Windows](../windows/portal-create-fqdn.md). Następnie uruchom poniższe polecenie:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Wewnątrz sieci wirtualnej lub wdrożenia uruchom następujące polecenie:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Ustawienie SkipCaCheck flagi pomija wymóg zaimportowania certyfikatu do maszyny Wirtualnej po uruchomieniu sesji.

Można również użyć polecenia cmdlet invoke-command do zdalnego uruchamiania skryptu na maszynie wirtualnej.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Rejestr zdalny

>[!NOTE]
>Aby korzystać z tej opcji, port TCP 135 lub 445 musi być otwarty.
>
>W przypadku maszyn wirtualnych usługi Azure Resource Manager należy otworzyć port 5986 na sieciowej grupie sieciowej. Aby uzyskać więcej informacji, zobacz Grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych RDFE musi istnieć punkt końcowy z portem prywatnym 5986 i portem publicznym. Musisz również otworzyć ten publiczny port na nsg.

1. Z innej maszyny Wirtualnej w tej samej sieci wirtualnej otwórz edytor rejestru (regedit.exe).

2. Wybierz pozycję**Rejestr sieciowy połączenia** **plików** > .

   ![Edytor rejestru](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Znajdź docelową maszynę wirtualną według **nazwy hosta** lub **dynamicznego adresu IP** (preferowanego), wprowadzając ją w polu Wprowadź nazwę **obiektu, aby zaznaczyć.**

   ![Wprowadź nazwę obiektu, aby zaznaczyć pole](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Wprowadź poświadczenia dla docelowej maszyny Wirtualnej.

5. Wprowadzać wszelkie niezbędne zmiany w rejestrze.

## <a name="remote-services-console"></a>Konsola usług zdalnych

>[!NOTE]
>Aby korzystać z tej opcji, muszą być otwarte porty TCP 135 lub 445.
>
>W przypadku maszyn wirtualnych usługi Azure Resource Manager należy otworzyć port 5986 na sieciowej grupie sieciowej. Aby uzyskać więcej informacji, zobacz Grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych RDFE musi istnieć punkt końcowy z portem prywatnym 5986 i portem publicznym. Musisz również otworzyć ten publiczny port na nsg.

1. Z innej maszyny Wirtualnej w tej samej sieci wirtualnej otwórz wystąpienie **services.msc**.

2. Kliknij prawym przyciskiem myszy **pozycję Usługi (lokalne).**

3. Wybierz pozycję **Połącz z innym komputerem**.

   ![Usługa zdalna](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Wprowadź dynamiczny adres IP docelowej maszyny Wirtualnej.

   ![Wejściowy dynamiczny adres IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Wprowadzać wszelkie niezbędne zmiany w usługach.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat polecenia cmdlet Enter-PSSession, zobacz [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Aby uzyskać więcej informacji na temat niestandardowego rozszerzenia skryptu dla systemu Windows przy użyciu klasycznego modelu wdrażania, zobacz [Niestandardowe rozszerzenie skryptu dla systemu Windows](../extensions/custom-script-classic.md).
- PsExec jest częścią [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).
- Aby uzyskać więcej informacji na temat pakietu PSTools Suite, zobacz [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


