---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 072864d565e2edbddd4b7df851ad0e30daf7e5fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387965"
---
Diagnozowanie problemów z usługą w chmurze Microsoft Azure wymaga zbierania plików dziennika usługi na maszynach wirtualnych, w momencie wystąpienia problemów. Można użyć AzureLogCollector rozszerzenia na żądanie wykonaj jednorazowy zbieranie dzienników z przynajmniej jednej chmury usługi maszyny wirtualnej (z ról sieć web i ról procesów roboczych) i przenieść zebranych plików na konto magazynu platformy Azure — wszystko to bez konieczności zdalne logowanie do dowolnej maszyn wirtualnych.

> [!NOTE]
> Opisy większości zarejestrowanych informacji znajduje się w temacie http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.
> 
> 

Istnieją dwa tryby gromadzenia, zależne od typów plików, które mają być zbierane.

* **Gościa platformy Azure agenta dzienniki tylko (GA)**. Ten tryb kolekcji obejmuje wszystkie dzienniki związane z agentów gościa platformy Azure i innymi składnikami platformy Azure.
* **Wszystkie dzienniki (pełne)**. Ten tryb kolekcji zbiera wszystkie pliki w trybie GA plus:
  
  * dzienniki zdarzeń systemu i aplikacji
  * Dzienniki błędów HTTP
  * Dzienniki usług IIS
  * Dzienniki Instalatora
  * inne dzienniki systemu

W obu trybach kolekcji można określić dodatkowe dane folderów kolekcji, używając kolekcji o następującej strukturze:

* **Nazwa**: Nazwa kolekcji używana jako nazwa podfolderu wewnątrz pliku zip z zebranych plików.
* **Lokalizacja**: Ścieżka do folderu na maszynie wirtualnej, w którym znajdują się pliki, które mają być zbierane.
* **SearchPattern**: Wzorzec nazw plików, które mają być zbierane. Wartość domyślna to "\*"
* **Cykliczne**: Jeśli pliki mają być zbierane są rekursywnie znajduje się w lokalizacji określonej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Nie masz konta magazynu dla rozszerzenia do zapisywania plików zip wygenerowany.
* Azure PowerShell. Zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)] Aby uzyskać instrukcje instalacji.

## <a name="add-the-extension"></a>Dodawanie rozszerzenia
Możesz użyć [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) poleceń cmdlet lub [interfejsów API REST zarządzania usługami](https://msdn.microsoft.com/library/ee460799.aspx) można dodać rozszerzenia AzureLogCollector.

Dla usług w chmurze, istniejące polecenia cmdlet programu Azure Powershell **AzureServiceExtension zestaw**, może służyć do włączyć rozszerzenie na wystąpieniach roli usługi w chmurze. Za każdym razem, gdy to rozszerzenie jest włączone za pomocą tego polecenia cmdlet, zbieranie danych dziennika jest wyzwalana dla wybranej roli rodzajami wybranych ról.

W przypadku maszyn wirtualnych istniejące polecenia cmdlet programu Azure Powershell **AzureVMExtension zestaw**, może służyć do włączyć rozszerzenie na maszynach wirtualnych. Za każdym razem, gdy to rozszerzenie jest włączone za pomocą polecenia cmdlet, zbieranie danych dziennika jest wyzwalany w każdym wystąpieniu.

To rozszerzenie używa wewnętrznie, PublicConfiguration opartych na formacie JSON i PrivateConfiguration. Poniżej znajduje się układ próbkę JSON dla konfiguracji publicznych i prywatnych.

### <a name="publicconfiguration"></a>PublicConfiguration

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

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> To rozszerzenie nie wymaga **privateConfiguration**. Można po prostu podać pustą strukturą dla **— PrivateConfiguration** argumentu.
> 
> 

Możesz wykonać jedną z dwóch poniższe kroki, aby dodać AzureLogCollector do co najmniej jedno wystąpienie usługi w chmurze lub maszyny wirtualnej wybranych ról, co powoduje wyzwolenie kolekcji na każdej maszynie Wirtualnej do uruchamiania i wysyłać zebrane pliki określone konto platformy Azure.

## <a name="adding-as-a-service-extension"></a>Dodawanie jako rozszerzenie usługi
1. Postępuj zgodnie z instrukcjami, aby połączyć z programu Azure PowerShell do Twojej subskrypcji.
2. Określ nazwę, miejsca, ról i ról wystąpień usługi do których chcesz dodać, a następnie włączyć rozszerzenie AzureLogCollector.

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

3. Określ folder dodatkowych danych, dla których będą zbierane pliki (ten krok jest opcjonalny).

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
   > Można użyć tokenu `%roleroot%` do określenia dysku głównym roli, ponieważ nie korzysta się z dyskiem stałym.
   > 
   > 
4. Podaj nazwę konta usługi Azure storage i klucz, do którego zostanie przekazane zebranych plików.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Wywołanie SetAzureServiceLogCollector.ps1 (dołączona na końcu tego artykułu) w następujący sposób włączyć rozszerzenie AzureLogCollector dla usługi w chmurze. Po zakończeniu wykonywania, można znaleźć przekazanego pliku w obszarze `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Poniżej znajduje się definicja parametry przekazywane do skryptu. (To jest kopiowana poniżej także.)

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

* **ServiceName**: Twoja nazwa usługi w chmurze.
* **role**: Lista ról, takich jak "WebRole1" lub "WorkerRole1".
* **Wystąpienia**: Listę nazw wystąpień roli, oddzielone przecinkiem — Użyj ciągu symbol wieloznaczny ("*") dla wszystkich wystąpień roli.
* **Gniazda**: Nazwa miejsca. "Produkcyjne" lub "Staging".
* **Tryb**: Tryb kolekcji. "Pełnej" lub "GA".
* **StorageAccountName**: Nazwa konta usługi Azure storage do przechowywania zebranych danych.
* **StorageAccountKey**: Nazwa klucza konta magazynu platformy Azure.
* **AdditionalDataLocationList**: Lista następującą strukturę:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Dodawanie jako rozszerzenie maszyny Wirtualnej
Postępuj zgodnie z instrukcjami, aby połączyć z programu Azure PowerShell do Twojej subskrypcji.

1. Określ nazwę usługi, maszyny Wirtualnej i tryb kolekcji.

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
  
2. Podaj nazwę konta usługi Azure storage i klucz, do którego zostanie przekazane zebranych plików.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Wywołanie SetAzureVMLogCollector.ps1 (dołączona na końcu tego artykułu) w następujący sposób włączyć rozszerzenie AzureLogCollector dla usługi w chmurze. Po zakończeniu wykonywania, można znaleźć przekazanego pliku w obszarze `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Poniżej znajduje się definicja parametry przekazywane do skryptu. (To jest kopiowana poniżej także.)

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

* **ServiceName**: Twoja nazwa usługi w chmurze.
* **VMName**: Nazwa maszyny Wirtualnej.
* **Tryb**: Tryb kolekcji. "Pełnej" lub "GA".
* **StorageAccountName**: Nazwa konta usługi Azure storage do przechowywania zebranych danych.
* **StorageAccountKey**: Nazwa klucza konta magazynu platformy Azure.
* **AdditionalDataLocationList**: Lista następującą strukturę:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Pliki skryptów programu PowerShell rozszerzenie
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
Teraz można przejrzeć, lub skopiować dzienników z jednego miejsca proste.

