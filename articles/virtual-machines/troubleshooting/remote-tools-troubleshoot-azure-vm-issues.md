---
title: Rozwiązywanie problemów dotyczących maszyny Wirtualnej platformy Azure za pomocą narzędzi zdalnego | Dokumentacja firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 513ce98703e67053ab0bcac3e6fc7a3e959f6870
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717269"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Użyj narzędzia zdalnej rozwiązywać problemy z maszyny Wirtualnej platformy Azure

Podczas rozwiązywania problemów, na maszynie wirtualnej (VM) platformy Azure, można połączyć z maszyną wirtualną, przy użyciu narzędzi zdalnych, które zostały omówione w tym artykule, zamiast przy użyciu protokołu RDP (Remote Desktop).

## <a name="serial-console"></a>Konsola szeregowa

Użyj [Konsola szeregowa maszyny wirtualnej](serial-console-windows.md) do uruchamiania poleceń na zdalnej maszynie Wirtualnej platformy Azure.

## <a name="remote-cmd"></a>Polecenia zdalnego

Pobierz [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Połączenie z maszyną wirtualną, uruchamiając następujące polecenie:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Polecenie musi uruchomić na komputerze, na którym znajduje się w tej samej sieci Wirtualnej.
>* DIP lub nazwy hosta można zastąpić \<komputera >.
>* Parametr -s zapewnia, że wywoływania polecenia przy użyciu konta systemowego (uprawnienia administratora).
>* Narzędzie PsExec korzysta z portów TCP 135 i 445. W związku z tym dwa porty muszą być otwarte na zaporze.

## <a name="run-commands"></a>Uruchamianie poleceń

Zobacz [uruchamiania skryptów programu PowerShell na maszynie Wirtualnej Windows za pomocą polecenia Uruchom](../windows/run-command.md) Aby uzyskać więcej informacji o sposobie używania funkcji uruchamiania poleceń na uruchamianie skryptów na maszynie Wirtualnej.

## <a name="customer-script-extension"></a>Rozszerzenia skryptów klienta

Funkcja rozszerzenie niestandardowego skryptu do uruchomienia skryptu niestandardowego na docelowej maszynie Wirtualnej. Aby użyć tej funkcji, muszą być spełnione następujące warunki:

* Maszyna wirtualna ma łączność.

* Agent platformy Azure jest zainstalowany i działa zgodnie z oczekiwaniami na maszynie Wirtualnej.

* Rozszerzenie nie została wcześniej zainstalowana na maszynie Wirtualnej.
 
  Rozszerzenie doda skrypt tylko za pierwszym razem, jest używany. Jeśli później użyć tej funkcji, rozszerzenie rozpozna był już używany i nie będzie przekazywać nowy skrypt.

Będzie konieczne Przekaż skrypt do konta magazynu i wygenerować własną kontenera. Następnie uruchom następujący skrypt programu Azure PowerShell na komputerze, który ma łączność z maszyną Wirtualną.

### <a name="for-v1-vms"></a>W przypadku maszyn wirtualnych w wersji 1

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>W przypadku maszyn wirtualnych w wersji 2

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Zdalnego programu PowerShell

>[!Note]
>Tak, aby można było używać tej opcji, TCP portu 5986 (HTTPS) musi być otwarty.
>
>W przypadku maszyn wirtualnych ARM możesz otworzyć portu 5986 na sieciową grupę zabezpieczeń (NSG). Aby uzyskać więcej informacji zobacz grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych frontonu REDDOG musi mieć punktu końcowego, który ma prywatnej portu (5986) i port publiczny. Następnie musisz także otworzyć ten publiczny port umożliwiających dostęp w sieciowej grupie zabezpieczeń.

### <a name="set-up-the-client-computer"></a>Konfigurowanie komputera klienckiego

Zdalne ustanawianie połączenia z maszyną Wirtualną przy użyciu programu PowerShell, należy najpierw skonfigurować komputer kliencki w celu zezwalania na połączenia. Aby to zrobić, należy dodać maszynę Wirtualną do listy zaufanych hostów programu PowerShell, uruchamiając następujące polecenie, zgodnie z potrzebami.

Aby dodać jedną maszynę Wirtualną do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Aby dodać wiele maszyn wirtualnych do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Aby dodać wszystkie komputery do listy zaufanych hostów:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Włącz RemotePS na maszynie Wirtualnej

Dla klasycznych maszyn wirtualnych Użyj niestandardowego rozszerzenia skryptu, aby uruchomić następujący skrypt:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

W przypadku maszyn wirtualnych ARM należy użyć polecenia Uruchom z portalu do uruchomienia skryptu EnableRemotePS:

![Uruchamianie polecenia](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Uruchom następujące polecenie, w zależności od klienta w lokalizacji komputera:

* Poza siecią Wirtualną lub wdrożenia

  * Dla klasycznej maszyny Wirtualnej uruchom następujące polecenie:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Dla maszyny Wirtualnej ARM należy najpierw dodać nazwę DNS do publicznego adresu IP. Aby uzyskać szczegółowe instrukcje, zobacz [tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure portal dla maszyny Wirtualnej z systemem Windows](../windows/portal-create-fqdn.md). Następnie uruchom poniższe polecenie:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Wewnątrz sieci Wirtualnej lub wdrożenie uruchom następujące polecenie:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Ustawienie flagi SkipCaCheck pomija wymaganie, aby zaimportować certyfikat do maszyny Wirtualnej podczas uruchamiania sesji.

Można również użyć polecenia cmdlet Invoke-Command, do zdalnego uruchamiania skryptu na maszynie Wirtualnej:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Rejestr zdalny

>[!Note]
>Port TCP 135 lub 445 musi być otwarty, aby można było używać tej opcji.
>
>W przypadku maszyn wirtualnych ARM trzeba otworzyć portu 5986 na sieciową grupę zabezpieczeń. Aby uzyskać więcej informacji zobacz grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych frontonu REDDOG musi mieć punktu końcowego, który zawiera prywatne portu 5986 i port publiczny. Musisz także otworzyć ten port publicznego sieciowej grupy zabezpieczeń.

1. Korzystając z innej maszyny Wirtualnej w tej samej sieci Wirtualnej Otwórz Edytor rejestru (regedit.exe).

2. Wybierz **pliku** >**połączyć sieci rejestru**.

   ![Opcja zdalnego](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Znajdź docelowej maszyny Wirtualnej przez **hostname** lub **dynamicznego adresu IP** (preferowane), wprowadzając ją w polu "Wprowadź nazwę obiektu do wybrania".

   ![Opcja zdalnego](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Wprowadź poświadczenia dla docelowej maszyny Wirtualnej.

5. Wprowadź wszelkie zmiany rejestru niezbędne.

## <a name="remote-services-console"></a>Konsola usługi zdalnej

>[!Note]
>Porty TCP 135 lub 445 musi być otwarty, aby można było używać tej opcji.
>
>W przypadku maszyn wirtualnych ARM trzeba otworzyć portu 5986 na sieciową grupę zabezpieczeń. Aby uzyskać więcej informacji zobacz grupy zabezpieczeń. 
>
>W przypadku maszyn wirtualnych frontonu REDDOG musi mieć punktu końcowego, który zawiera prywatne portu 5986 i port publiczny. Następnie musisz także otworzyć ten port publicznego sieciowej grupy zabezpieczeń.

1. Z innej maszyny Wirtualnej w tej samej sieci Wirtualnej, otwórz wystąpienie **Services.msc**.

2. Kliknij prawym przyciskiem myszy **usługi (lokalne)** .

3. Wybierz **Podłącz do innego komputera**.

   ![Usługa zdalna](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Wprowadź dynamicznego adresu IP dla docelowej maszyny Wirtualnej.

   ![Dane wejściowe DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Wprowadź niezbędne zmiany do usług.

## <a name="next-steps"></a>Następne kroki

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Niestandardowy skrypt rozszerzenia dla Windows przy użyciu klasycznego modelu wdrażania](../extensions/custom-script-classic.md)

Narzędzie PsExec jest częścią [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).

Aby uzyskać więcej informacji na temat pakietu PSTools, zobacz [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


