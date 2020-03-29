---
title: Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher — INTERFEJS API REST | Dokumenty firmy Microsoft
description: Na tej stronie wyjaśniono, jak zarządzać funkcją przechwytywania pakietów funkcji Obserwatora sieciowego przy użyciu interfejsu API rest platformy Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 5199cf95452f93db2c2dd747fcabc67a6722d31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840897"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Zarządzanie przechwytywaniami pakietów za pomocą usługi Azure Network Watcher przy użyciu interfejsu API usługi Azure REST

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów Obserwatora sieciowego umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytujesz tylko ruch, który chcesz. Przechwytywanie pakietów pomaga diagnozować anomalie sieci zarówno reaktywnie, jak i proaktywnie. Inne zastosowania obejmują zbieranie statystyk sieciowych, uzyskiwanie informacji o włamaniach do sieci, debugowanie komunikacji klient-serwer i wiele więcej. Dzięki możliwości zdalnego wyzwalania przechwytywania pakietów, ta funkcja zmniejsza obciążenie związane z ręcznym uruchamianiem przechwytywania pakietów i na żądanym komputerze, co pozwala zaoszczędzić cenny czas.

W tym artykule oprowadza cię przez różne zadania zarządzania, które są obecnie dostępne do przechwytywania pakietów.

- [**Uzyskaj przechwytywanie pakietów**](#get-a-packet-capture)
- [**Lista wszystkich przechwytuje pakietów**](#list-all-packet-captures)
- [**Zapytanie o stan przechwytywania pakietów**](#query-packet-capture-status)
- [**Rozpoczynanie przechwytywania pakietów**](#start-packet-capture)
- [**Zatrzymywanie przechwytywania pakietów**](#stop-packet-capture)
- [**Usuwanie przechwytywania pakietów**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu wywołać interfejs API odpoczynku obserwatora sieciowego do uruchomienia weryfikacji przepływu IP. ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci.

> Przechwytywanie pakietów wymaga `AzureNetworkWatcherExtension`rozszerzenia maszyny wirtualnej . Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Windows odwiedź [rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i maszyny wirtualnej z systemem Linux, odwiedź rozszerzenie maszyny [wirtualnej usługi Azure Network Watcher Agent dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobieranie maszyny wirtualnej

Uruchom następujący skrypt, aby zwrócić maszynę wirtualną. Te informacje są potrzebne do uruchomienia przechwytywania pakietów.

Następujący kod wymaga zmiennych:

- **subscriptionId** — identyfikator subskrypcji można również pobrać za pomocą polecenia cmdlet **Get-AzSubscription.**
- **resourceGroupName** — nazwa grupy zasobów zawierającej maszyny wirtualne.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Z następujących danych wyjściowych identyfikator maszyny wirtualnej jest używany w następnym przykładzie.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Uzyskaj przechwytywanie pakietów

Poniższy przykład pobiera stan pojedynczego przechwytywania pakietów

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Poniższe odpowiedzi są przykładami typowej odpowiedzi zwracanej podczas wykonywania zapytań o stan przechwytywania pakietów.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Lista wszystkich przechwytuje pakietów

Poniższy przykład pobiera wszystkie sesje przechwytywania pakietów w regionie.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Następująca odpowiedź jest przykładem typowej odpowiedzi zwróconej podczas uzyskiwania wszystkich przechwytuje pakiet

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Stan przechwytywania pakietów kwerend

Poniższy przykład pobiera wszystkie sesje przechwytywania pakietów w regionie.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Następująca odpowiedź jest przykładem typowej odpowiedzi zwróconej podczas wykonywania zapytań o stan przechwytywania pakietów.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Uruchamianie przechwytywania pakietów

Poniższy przykład tworzy przechwytywanie pakietów na maszynie wirtualnej.  Przykład jest sparametryzowany, aby umożliwić elastyczność w tworzeniu przykładu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Zatrzymaj przechwytywanie pakietów

Poniższy przykład zatrzymuje przechwytywanie pakietów na maszynie wirtualnej.  Przykład jest sparametryzowany, aby umożliwić elastyczność w tworzeniu przykładu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Usuwanie przechwytywania pakietów

Poniższy przykład usuwa przechwytywanie pakietów na maszynie wirtualnej.  Przykład jest sparametryzowany, aby umożliwić elastyczność w tworzeniu przykładu.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Usunięcie przechwytywania pakietów nie powoduje usunięcia pliku z konta magazynu

## <a name="next-steps"></a>Następne kroki

Instrukcje dotyczące pobierania plików z kont usługi Azure Storage można znaleźć w [obszarze Wprowadzenie do usługi Azure Blob Storage przy użyciu programu .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Innym narzędziem, które może być używane, jest Eksplorator magazynu. Więcej informacji na temat Eksploratora magazynu można znaleźć tutaj pod następującym linkiem: [Eksplorator magazynu](https://storageexplorer.com/)

Dowiedz się, jak zautomatyzować przechwytywanie pakietów za pomocą alertów maszyn wirtualnych, przeglądając [Tworzenie wyzwalanego alertu przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md)













