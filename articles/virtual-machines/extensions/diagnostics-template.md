---
title: Dodawanie diagnostyki & monitorowania do maszyny wirtualnej platformy Azure
description: Użyj szablonu Usługi Azure Resource Manager, aby utworzyć nową maszynę wirtualną systemu Windows z rozszerzeniem diagnostyki platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2490c3de60e0deac6a1a4ddc5abc95cb46e240b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073838"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Używanie monitorowania i diagnostyki za pomocą szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager
Rozszerzenie diagnostyki platformy Azure zapewnia funkcje monitorowania i diagnostyki na maszynie wirtualnej platformy Azure opartej na systemie Windows. Można włączyć te możliwości na maszynie wirtualnej, dołączając rozszerzenie jako część szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat dołączania dowolnego rozszerzenia jako części szablonu maszyny wirtualnej, zobacz [Tworzenie szablonów usługi Azure Resource Manager z rozszerzeniami maszyn wirtualnych.](../windows/template-description.md#extensions) W tym artykule opisano, jak można dodać rozszerzenie diagnostyki platformy Azure do szablonu maszyny wirtualnej systemu Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Dodawanie rozszerzenia diagnostyki platformy Azure do definicji zasobu maszyny Wirtualnej
Aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej systemu Windows, należy dodać rozszerzenie jako zasób maszyny Wirtualnej w szablonie Menedżera zasobów.

W przypadku prostej maszyny wirtualnej opartej na Menedżerze zasobów dodaj konfigurację rozszerzenia do *tablicy zasobów* dla maszyny wirtualnej: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Inną powszechną konwencją jest dodanie konfiguracji rozszerzenia w węźle zasobów głównych szablonu zamiast definiowania go w węźle zasobów maszyny wirtualnej. Dzięki takiemu podejściu należy jawnie określić hierarchiczną relację między rozszerzeniem a maszyną wirtualną z wartościami *nazwy* i *typu.* Przykład: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozszerzenie jest zawsze skojarzone z maszyną wirtualną, można bezpośrednio zdefiniować go w węźle zasobu maszyny wirtualnej bezpośrednio lub zdefiniować go na poziomie podstawowym i użyć hierarchicznej konwencji nazewnictwa, aby skojarzyć ją z maszyną wirtualną.

Dla virtual machine scale Sets konfiguracja rozszerzeń jest określona w *extensionProfile* właściwości *VirtualMachineProfile*.

Właściwość *wydawcy* o wartości **Microsoft.Azure.Diagnostics** i właściwość *typu* o wartości **IaaSDiagnostics** jednoznacznie identyfikuje rozszerzenie diagnostyki platformy Azure.

Wartość właściwości *name* może służyć do odwoływania się do rozszerzenia w grupie zasobów. Ustawienie go specjalnie na **Microsoft.Insights.VMDiagnosticsSettings** umożliwia łatwo zidentyfikować przez portal Azure, zapewniając, że wykresy monitorowania są wyświetlane poprawnie w witrynie Azure portal.

*TypeHandlerVersion* określa wersję rozszerzenia, którego chcesz użyć. Ustawienie *autoUpgradeMinorVersion* wersji pomocniczej do **true** zapewnia, że masz najnowszą wersję pomocniczą rozszerzenia, które jest dostępne. Zdecydowanie zaleca się, aby zawsze ustawić *autoUpgradeMinorVersion* zawsze **być prawdziwe,** dzięki czemu zawsze można uzyskać do korzystania z najnowszych dostępnych rozszerzenie diagnostyki ze wszystkimi nowymi funkcjami i poprawki błędów. 

Element *ustawień* zawiera właściwości konfiguracji dla rozszerzenia, które można ustawić i odczytać z rozszerzenia (czasami nazywane konfiguracją publiczną). Właściwość *xmlcfg* zawiera konfigurację opartą na xml dla dzienników diagnostycznych, liczników wydajności itp., które są zbierane przez agenta diagnostycznego. Zobacz [Schemat konfiguracji diagnostyki, aby](https://msdn.microsoft.com/library/azure/dn782207.aspx) uzyskać więcej informacji na temat samego schematu xml. Powszechną praktyką jest przechowywanie rzeczywistej konfiguracji xml jako zmiennej w szablonie usługi Azure Resource Manager, a następnie łączenie i kodowanie base64 w celu ustawienia wartości *xmlcfg*. Zobacz sekcję [dotyczącą zmiennych konfiguracji diagnostyki,](#diagnostics-configuration-variables) aby dowiedzieć się więcej o sposobie przechowywania xml w zmiennych. *Właściwość storageAccount* określa nazwę konta magazynu, na które są przesyłane dane diagnostyczne. 

Właściwości w *protectedSettings* (czasami określane jako konfiguracja prywatna) można ustawić, ale nie można odczytać z powrotem po ustawieniu. Charakter tylko do zapisu *protectedSettings* sprawia, że przydatne do przechowywania wpisów tajnych, takich jak klucz konta magazynu, gdzie są zapisywane dane diagnostyczne.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Określanie konta magazynu diagnostyki jako parametrów
Fragment kodu rozszerzenia diagnostyki przyjmuje dwa parametry *existingdiagnosticsStorageAccountName* i *existingdiagnosticsStorageResourceGroup,* aby określić konto magazynu diagnostyki, na którym przechowywane są dane diagnostyczne. Określenie konta magazynu diagnostyki jako parametru ułatwia zmianę konta magazynu diagnostyki w różnych środowiskach, na przykład można użyć innego konta magazynu diagnostyki do testowania i innego dla wdrożenia produkcji.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Najlepszym rozwiązaniem jest określenie konta magazynu diagnostyki w innej grupie zasobów niż grupa zasobów dla maszyny wirtualnej. Grupę zasobów można uznać za jednostkę wdrożeniową z własnym okresem istnienia, maszynę wirtualną można wdrożyć i ponownie wdrożyć, gdy zostaną wprowadzone do niej nowe aktualizacje konfiguracji, ale można kontynuować przechowywanie danych diagnostycznych na tym samym koncie magazynu w całej tych wdrożeń maszyn wirtualnych. Posiadanie konta magazynu w innym zasobie umożliwia kontu magazynu akceptowanie danych z różnych wdrożeń maszyn wirtualnych, co ułatwia rozwiązywanie problemów w różnych wersjach.

> [!NOTE]
> Jeśli utworzysz szablon maszyny wirtualnej systemu Windows z programu Visual Studio, domyślne konto magazynu może być ustawione na użycie tego samego konta magazynu, na którym jest przekazywana maszyna wirtualna VHD. Ma to na celu uproszczenie początkowej konfiguracji maszyny Wirtualnej. Ponownie współczynnik szablonu, aby użyć innego konta magazynu, które mogą być przekazywane jako parametr. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Zmienne konfiguracji diagnostyki
Poprzedni fragment kodu json rozszerzenia diagnostyki definiuje zmienną *accountid,* aby uprościć uzyskiwanie klucza konta magazynu dla magazynu diagnostycznego:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Właściwość *xmlcfg* dla rozszerzenia diagnostyki jest definiowana przy użyciu wielu zmiennych, które są połączone ze sobą. Wartości tych zmiennych są w formacie xml, więc muszą być poprawnie zmienione podczas ustawiania zmiennych json.

W poniższym przykładzie opisano xml konfiguracji diagnostyki, która zbiera standardowe liczniki wydajności na poziomie systemu wraz z niektórymi dziennikami zdarzeń systemu Windows i dziennikami infrastruktury diagnostycznej. Został on zmieniony i sformatowany poprawnie, dzięki czemu konfiguracja może być bezpośrednio wklejona do sekcji zmiennych szablonu. Zobacz [schemat konfiguracji diagnostyki,](https://msdn.microsoft.com/library/azure/dn782207.aspx) aby uzyskać bardziej czytelny dla człowieka przykład xml konfiguracji.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Węzeł XML definicji metryki w powyższej konfiguracji jest ważnym elementem konfiguracji, ponieważ definiuje sposób agregowania i przechowywania liczników wydajności zdefiniowanych wcześniej w xml w węźle *PerformanceCounter.* 

> [!IMPORTANT]
> Te metryki napędzają wykresy monitorowania i alerty w witrynie Azure portal.  Węzeł **Metryki** z *resourceID* i **MetricAggregation** musi być uwzględniony w konfiguracji diagnostyki maszyny Wirtualnej, jeśli chcesz zobaczyć dane monitorowania maszyny Wirtualnej w witrynie Azure portal. 
> 
> 

W poniższym przykładzie przedstawiono xml dla definicji metryk: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Atrybut *resourceID* jednoznacznie identyfikuje maszynę wirtualną w ramach subskrypcji. Upewnij się, że używasz funkcji subscription() i resourceGroup(), aby szablon automatycznie aktualizacje tych wartości na podstawie subskrypcji i grupy zasobów, które wdrażane do.

Jeśli tworzysz wiele maszyn wirtualnych w pętli, musisz wypełnić wartość *resourceID* funkcją copyIndex(), aby poprawnie odróżnić każdą pojedynczą maszynę wirtualną. Wartość *xmlCfg* można zaktualizować, aby obsługiwać tę wartość w następujący sposób:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Wartość MetricAggregation *PT1M* i *PT1H* oznacza agregację w ciągu minuty i agregację w ciągu godziny, odpowiednio.

## <a name="wadmetrics-tables-in-storage"></a>Tabele WADMetrics w magazynie
Powyższa konfiguracja metryki generuje tabele na koncie magazynu diagnostyki z następującymi konwencjami nazewnictwa:

* **WADMetrics**: Standardowy prefiks dla wszystkich tabel WADMetrics
* **PT1H** lub **PT1M**: Oznacza, że tabela zawiera dane zagregowane w ciągu 1 godziny lub 1 minuty
* **P10D**: Oznacza, że tabela będzie zawierać dane przez 10 dni od momentu rozpoczęcia zbierania danych przez tabelę
* **V2S**: Stała ciągu
* **yyyymmdd**: Data rozpoczęcia zbierania danych przez tabelę

Przykład: *WADMetricsPT1HP10DV2S20151108* zawiera dane metryczne zagregowane przez godzinę przez 10 dni, począwszy od 11-nov-2015    

Każda tabela WADMetrics zawiera następujące kolumny:

* **PartitionKey**: Klucz partycji jest skonstruowany na podstawie wartości *resourceID,* aby jednoznacznie zidentyfikować zasób maszyny Wirtualnej. Na przykład: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Jest `<Descending time tick>:<Performance Counter Name>`zgodny z formatem . Obliczanie znacznika czasu malejącego jest maksymalnym znacznikiem czasu minus czas początku okresu agregacji. Na przykład, jeśli okres próbny rozpoczął się 10-Nov-2015 i 00:00Hrs UTC następnie obliczenia będą: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. W przypadku licznika wydajności dostępne bajty dostępnej będzie wyglądał następująco:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **Nazwa przeciwstawna:** Jest to nazwa licznika wydajności. To pasuje *do counterSpecifier* zdefiniowane w konfiguracji xml.
* **Maksymalna**: Maksymalna wartość licznika wydajności w okresie agregacji.
* **Minimum**: Minimalna wartość licznika wydajności w okresie agregacji.
* **Suma**: Suma wszystkich wartości licznika wydajności zgłoszonych w okresie agregacji.
* **Liczba:** Całkowita liczba wartości zgłoszonych dla licznika wydajności.
* **Średnia**: Średnia wartość (całkowita/liczba) licznika wydajności w okresie agregacji.

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać kompletny przykładowy szablon maszyny wirtualnej systemu Windows z rozszerzeniem diagnostyki, zobacz [rozszerzenie 201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Wdrażanie szablonu usługi Azure Resource Manager przy użyciu [usługi Azure PowerShell](../windows/ps-template.md) lub [wiersza polecenia platformy Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Dowiedz się więcej o [tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)
