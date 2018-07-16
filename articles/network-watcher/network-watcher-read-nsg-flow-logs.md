---
title: Dzienniki przepływów sieciowych grup zabezpieczeń odczytu | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób analizowania dzienników przepływu sieciowych grup zabezpieczeń
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 492a0a63198fe2013cfeac0459fc6da8521a5e6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056804"
---
# <a name="read-nsg-flow-logs"></a>Odczytywanie dzienników przepływu sieciowych grup zabezpieczeń

Dowiedz się, jak odczytać wpisy dzienników przepływu sieciowych grup zabezpieczeń przy użyciu programu PowerShell.

Dzienniki przepływu sieciowej grupy zabezpieczeń są przechowywane na koncie magazynu w [blokowe obiekty BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs.md#about-block-blobs). Blokowe obiekty BLOB składają się z mniejszych bloków. Każdy dziennik jest oddzielnym blokowych obiektów blob, który jest generowany co godzinę. Nowe dzienniki są generowane co godzinę, dzienniki są aktualizowane przy użyciu nowych wpisów, co kilka minut przy użyciu najnowszych danych. W tym artykule dowiesz się, jak odczytać części dzienników przepływów.

## <a name="scenario"></a>Scenariusz

W poniższym scenariuszu znajduje się przykład dziennika przepływu, który jest przechowywany na koncie magazynu. Firma Microsoft przejrzeć jak selektywnie odczytu najnowszych zdarzeń w dzienników przepływu sieciowych grup zabezpieczeń. W tym artykule używamy programu PowerShell, jednak kwestie omówione w artykule, nie są ograniczone do języka programowania i mają zastosowanie do wszystkich językach obsługiwanych przez interfejsy API usługi Azure Storage

## <a name="setup"></a>Konfigurowanie

Przed przystąpieniem do wykonywania, konieczne jest posiadanie sieci grupy przepływu rejestrowanie zabezpieczeń włączone w jednej lub wielu grup zabezpieczeń sieci w ramach Twojego konta. Aby uzyskać instrukcje na temat włączania zabezpieczeń sieci dzienniki przepływu, zapoznaj się z następującym artykułem: [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Pobieranie listy zablokowanych

Następujące polecenie programu PowerShell konfiguruje zmienne wymagane do wykonywania zapytań blob dzienników przepływu sieciowej grupy zabezpieczeń i listy bloki w [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azurestorage-8.1.3) blokowych obiektów blob. Zaktualizuj skrypt, który ma zawierać prawidłowe wartości dla danego środowiska.

```powershell
function Get-NSGFlowLogBlockList {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzureStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockList()

        # Return the Block List
        $blockList
    }
}
$blockList = Get-NSGFlowLogBlockList -subscriptionId "00000000-0000-0000-0000-000000000000" -NSGResourceGroupName "resourcegroupname" -storageAccountName "storageaccountname" -storageAccountResourceGroup "sa-rg" -macAddress "000D3AF8196E" -logTime "03/07/2018 22:00"
```

`$blockList` Zmiennej zwraca listę bloków w obiekcie blob. Każdy blokowy obiekt blob zawiera co najmniej dwa bloki.  Pierwszy blok może się składać z `21` bajtów, ten blok zawiera otwierające nawiasy dziennika json. Inne bloku jest zamykających nawiasów kwadratowych i może się składać z `9` bajtów.  Jak widać następujący dziennik przykładzie zawiera siedem wpisów, jest pojedynczy wpis. Wszystkie nowe wpisy w dzienniku są dodawane na końcu bezpośrednio przed ostatnim blokiem.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Przeczytaj blokowych obiektów blob

Następnie należy odczytać `$blocklist` zmiennej w celu pobrania danych. W tym przykładzie, który możemy wykonać iterację listy blokowania odczytu bajtów z każdego bloku i ich historii w tablicy. Używamy [DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadrangetobytearray?view=azurestorage-8.1.3#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadRangeToByteArray_System_Byte___System_Int32_System_Nullable_System_Int64__System_Nullable_System_Int64__Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metody do pobierania danych.

```powershell
# Set the size of the byte array to the largest block
$maxvalue = ($blocklist | measure Length -Maximum).Maximum

# Create an array to store values in
$valuearray = @()

# Define the starting index to track the current block being read
$index = 0

# Loop through each block in the block list
for($i=0; $i -lt $blocklist.count; $i++)
{

# Create a byte array object to story the bytes from the block
$downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

# Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
$CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index+3,$($blockList[$i].Length-1)) | Out-Null

# Increment the index by adding the current block length to the previous index
$index = $index + $blockList[$i].Length

# Retrieve the string from the byte array

$value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

# Add the log entry to the value array
$valuearray += $value
}
```

Teraz `$valuearray` tablica zawiera wartość ciągu każdy blok. Aby sprawdzić, czy wpis, Pobierz drugi na ostatnią wartość z tablicy, uruchamiając `$valuearray[$valuearray.Length-2]`. Firma Microsoft nie chcę ostatnią wartość jest po prostu nawias zamykający.

Wyniki tej wartości zostaną wyświetlone w następującym przykładzie:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Ten scenariusz jest przykładem sposobu odczytywania wpisów dzienników przepływu sieciowych grup zabezpieczeń bez konieczności przeanalizować cały dziennik. Może odczytywać nowe wpisy w dzienniku, jak zostały napisane przy użyciu Identyfikatora bloku lub przez śledzenie długość bloki przechowywane w blokowych obiektów blob. Dzięki temu można odczytać tylko nowe wpisy.


## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [wizualizowanie dzienników przepływów Azure Network Watcher NSG przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) dowiedzieć się więcej o innych sposobach wyświetlania dzienników przepływu sieciowych grup zabezpieczeń.

Aby dowiedzieć się więcej na temat obiektów blob usługi storage można znaleźć: [powiązania magazynu obiektów Blob platformy Azure Functions](../azure-functions/functions-bindings-storage-blob.md)
