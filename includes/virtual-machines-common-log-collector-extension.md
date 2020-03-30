---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085265"
---
Diagnozowanie problemów z usługą w chmurze platformy Microsoft Azure wymaga zbierania plików dziennika usługi na maszynach wirtualnych w miarę występowania problemów. Rozszerzenie AzureLogCollector na żądanie umożliwia wykonywanie jednorazowego zbierania dzienników z jednej lub więcej maszyn wirtualnych usługi w chmurze (zarówno z ról sieci Web, jak i ról procesu roboczego) i przenoszenie zebranych plików na konto magazynu platformy Azure — wszystko to bez zdalnego logowania do dowolnego maszyn wirtualnych.

> [!NOTE]
> Opisy większości zarejestrowanych informacji można znaleźć na stroniehttps://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Istnieją dwa tryby zbierania w zależności od typów plików do zebrania.

* **Tylko dzienniki agenta gościa platformy Azure (GA)**. Ten tryb zbierania danych zawiera wszystkie dzienniki związane z agentami gościa platformy Azure i innymi składnikami platformy Azure.
* **Wszystkie dzienniki (pełne)**. Ten tryb zbierania kolekcji zbiera wszystkie pliki w trybie GA plus:
  
  * dzienniki zdarzeń systemu i aplikacji
  * Dzienniki błędów HTTP
  * Dzienniki usług IIS
  * Dzienniki instalacji
  * inne dzienniki systemowe

W obu trybach zbierania dodatkowych folderów zbierania danych można określić przy użyciu kolekcji następującej struktury:

* **Nazwa**: Nazwa kolekcji, używana jako nazwa podfolderu wewnątrz pliku zip z zebranymi plikami.
* **Lokalizacja:** Znajduje się ścieżka do folderu na maszynie wirtualnej, w którym znajdują się pliki do zebrania.
* **SearchPattern**: Wzór nazw plików do zebrania. Wartość domyślna to "\*"
* **Cykliczne**: jeśli pliki, które mają być zbierane znajdują się rekursywnie w określonej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Mieć konto magazynu dla rozszerzenia, aby zapisać wygenerowane pliki zip.
* Azure PowerShell. Aby uzyskać instrukcje dotyczące instalowania, zobacz Instalowanie programu [Azure PowerShell].](/powershell/azure/install-az-ps)

## <a name="add-the-extension"></a>Dodawanie rozszerzenia
Aby dodać rozszerzenie azurelogcollector, można użyć poleceń cmdlet programu [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) lub [interfejsów API REST zarządzania usługami.](https://msdn.microsoft.com/library/ee460799.aspx)

W przypadku usług w chmurze można włączyć **Set-AzureServiceExtension**rozszerzenie w wystąpieniach roli usługi w chmurze. Za każdym razem, gdy to rozszerzenie jest włączone za pośrednictwem tego polecenia cmdlet, zbieranie dzienników jest wyzwalane w wybranych wystąpieniach ról wybranych ról.

W przypadku maszyn wirtualnych można włączyć rozszerzenie **Set-AzureVMExtension**na maszynach wirtualnych. Za każdym razem, gdy to rozszerzenie jest włączone za pośrednictwem poleceń cmdlet, kolekcja dziennika jest wyzwalana w każdym wystąpieniu.

Wewnętrznie to rozszerzenie używa publicconfiguration oparte JSON i PrivateConfiguration. Poniżej przedstawiono układ przykładowego JSON dla konfiguracji publicznej i prywatnej.

### <a name="publicconfiguration"></a>Konfiguracja publiczna

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>Konfiguracja prywatna

```json
{

}
```

> [!NOTE]
> To rozszerzenie nie wymaga **privateConfiguration**. Można po prostu podać pustą strukturę dla **-PrivateConfiguration** argument.
> 
> 

Można wykonać jeden z dwóch następujących kroków, aby dodać AzureLogCollector do jednego lub więcej wystąpień usługi w chmurze lub maszyny wirtualnej wybranych ról, która wyzwala kolekcje na każdej maszynie wirtualnej do uruchamiania i wysyłania zebranych plików do konta platformy Azure określonych.

## <a name="adding-as-a-service-extension"></a>Dodawanie jako rozszerzenia usługi
1. Postępuj zgodnie z instrukcjami, aby połączyć program Azure PowerShell z subskrypcją.
2. Określ nazwę usługi, gniazdo, role i wystąpienia ról, do których chcesz dodać i włączyć rozszerzenie AzureLogCollector.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Określ dodatkowy folder danych, dla którego pliki będą zbierane (ten krok jest opcjonalny).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > Token można `%roleroot%` użyć, aby określić dysk główny roli, ponieważ nie używa stałego dysku.
   > 
   > 
4. Podaj nazwę konta magazynu platformy Azure i klucz, do którego zostaną przekazane zebrane pliki.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Wywołanie SetAzureServiceLogCollector.ps1 (zawarte na końcu artykułu) w następujący sposób, aby włączyć rozszerzenie AzureLogCollector dla usługi w chmurze. Po zakończeniu wykonywania można znaleźć przesłany plik w obszarze`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Poniżej przedstawiono definicję parametrów przekazanych do skryptu. (To jest kopiowane poniżej, jak również.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **Nazwa usługi:** Nazwa usługi w chmurze.
* **Role**: Lista ról, takich jak "WebRole1" lub "WorkerRole1".
* **Wystąpienia:** Lista nazw wystąpień roli oddzielonych przecinkami — użyj ciągu wieloznacznego ("*") dla wszystkich wystąpień roli.
* **Gniazdo**: Nazwa gniazda. "Produkcja" lub "Inscenizacja".
* **Tryb:** Tryb zbierania. "Full" lub "GA".
* **StorageAccountName**: Nazwa konta magazynu platformy Azure do przechowywania zebranych danych.
* **StorageAccountKey:** Nazwa klucza konta magazynu platformy Azure.
* **AdditionalDataLocationList**: Lista następujących struktur:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Dodawanie jako rozszerzenia maszyny Wirtualnej
Postępuj zgodnie z instrukcjami, aby połączyć program Azure PowerShell z subskrypcją.

1. Określ nazwę usługi, maszynę wirtualną i tryb kolekcji.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Podaj nazwę konta magazynu platformy Azure i klucz, do którego zostaną przekazane zebrane pliki.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Wywołanie SetAzureVMLogCollector.ps1 (zawarte na końcu artykułu) w następujący sposób, aby włączyć rozszerzenie AzureLogCollector dla usługi w chmurze. Po zakończeniu wykonywania można znaleźć przesłany plik w obszarze`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Poniżej przedstawiono definicję parametrów przekazanych do skryptu. (To jest kopiowane poniżej, jak również.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **Nazwa usługi:** Nazwa usługi w chmurze.
* **Nazwa VMName**: Nazwa maszyny Wirtualnej.
* **Tryb:** Tryb zbierania. "Full" lub "GA".
* **StorageAccountName**: Nazwa konta magazynu platformy Azure do przechowywania zebranych danych.
* **StorageAccountKey:** Nazwa klucza konta magazynu platformy Azure.
* **AdditionalDataLocationList**: Lista następujących struktur:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Pliki skryptu programu Extention PowerShell
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Następne kroki
Teraz możesz sprawdzić lub skopiować dzienniki z jednej prostej lokalizacji.

