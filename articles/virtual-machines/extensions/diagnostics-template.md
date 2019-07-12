---
title: Dodawanie monitorowania i diagnostyki na maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Aby utworzyć nową maszynę wirtualną Windows za pomocą rozszerzenie diagnostyki platformy Azure, należy użyć szablonu usługi Azure Resource Manager.
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
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d1c5598bd7ea5b3f35d5447935953d4cd55664a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706755"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Użyj monitorowania i diagnostyki za pomocą szablonów maszyn wirtualnych Windows i usługi Azure Resource Manager
Rozszerzenie diagnostyki platformy Azure zawiera funkcje monitorowania i diagnostyki na maszynie wirtualnej na podstawie Windows Azure. Aby włączyć te możliwości na maszynie wirtualnej, łącznie z rozszerzeniem w ramach szablonu usługi Azure Resource Manager. Zobacz [Tworzenie szablonów usługi Azure Resource Manager przy użyciu rozszerzeń maszyn wirtualnych](../windows/template-description.md#extensions) uzyskać więcej informacji o tym każde rozszerzenie jako część szablonu maszyny wirtualnej. W tym artykule opisano, jak dodać rozszerzenie Diagnostyka Azure do szablonu maszyny wirtualnej systemu windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Dodaj rozszerzenie diagnostyki platformy Azure w definicji zasobu maszyny Wirtualnej
Aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej Windows, musisz dodać rozszerzenie jako zasób maszyny Wirtualnej w szablonie usługi Resource Manager.

Dla prostych Menedżera zasobów na podstawie maszyny wirtualnej Dodaj konfigurację rozszerzenia *zasobów* tablicy dla maszyny wirtualnej: 

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

Jest innym typową Konwencją można dodać konfiguracji rozszerzenia w węźle głównym zasobów szablonu zamiast definiować go w węźle zasobów maszyny wirtualnej. W przypadku tej metody, należy jawnie określić hierarchicznych relacji między rozszerzenie i maszynę wirtualną z *nazwa* i *typu* wartości. Przykład: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozszerzenie zawsze jest skojarzone z maszyną wirtualną, możesz bezpośrednio definiowania go bezpośrednio w węźle zasobu maszyny wirtualnej lub definiowania go na podstawowym poziomie i użyj hierarchiczne konwencji nazewnictwa, aby skojarzyć go z maszyny wirtualnej.

Dla zestawów skalowania maszyn wirtualnych w konfiguracji rozszerzenia jest określona w *extensionProfile* właściwość *VirtualMachineProfile*.

*Wydawcy* właściwość z wartością **Microsoft.Azure.Diagnostics** i *typu* właściwość z wartością **IaaSDiagnostics**jednoznacznie zidentyfikować przez rozszerzenie Diagnostyka Azure.

Wartość *nazwa* właściwość może służyć do odwoływania się do rozszerzenia w grupie zasobów. Ustawieniem dla niego specjalnie do **Microsoft.Insights.VMDiagnosticsSettings** umożliwia można łatwo zidentyfikować w portalu Azure, zapewniając, że monitorowanie wykresy show się poprawnie w witrynie Azure portal.

*TypeHandlerVersion* Określa wersję rozszerzenia, o których chcesz użyć. Ustawienie *autoUpgradeMinorVersion* wersja pomocnicza do **true** gwarantuje, że Pobierz najnowszą wersję pomocniczą rozszerzenia, które jest dostępne. Zdecydowanie zaleca się, że zawsze ustawić *autoUpgradeMinorVersion* zawsze są **true** tak, aby zawsze uzyskać najnowsze rozszerzenia diagnostyki dostępne za pomocą nowych funkcji i poprawek błędów. 

*Ustawienia* element zawiera konfiguracji właściwości rozszerzenia, które można ustawić i Odczyt, powrót po awarii z rozszerzeniem (czasami określane jako publiczna Konfiguracja). *Xmlcfg* właściwość zawiera konfigurację opartymi na języku xml dzienniki diagnostyczne liczniki wydajności itp., które są zbierane przez agenta diagnostyki. Zobacz [schemat usługi Diagnostyka konfiguracji](https://msdn.microsoft.com/library/azure/dn782207.aspx) Aby uzyskać więcej informacji na temat schematu xml, sam. Powszechną praktyką jest do przechowywania konfiguracji rzeczywiste xml jako zmienna w szablonie usługi Azure Resource Manager i następnie łączyć i base64 — kodowanie je, aby ustawić wartość *xmlcfg*. Zobacz sekcję dotyczącą [zmienne konfiguracji diagnostyki](#diagnostics-configuration-variables) Aby dowiedzieć się więcej o tym, jak przechowywać dane xml w zmiennych. *StorageAccount* właściwość określa nazwę konta magazynu, do których diagnostyki transferu danych. 

Właściwości w *protectedSettings* (Konfiguracja czasami określone jako prywatne) można ustawić, ale nie można odczytać po ustawiania. Tylko do zapisu rodzaj *protectedSettings* sprawia, że przydatne do przechowywania wpisów tajnych, takich jak klucz konta magazynu na którym są zapisywane dane diagnostyczne.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Określanie konta magazynu diagnostyki jako parametrów
Diagnostyki rozszerzenia fragmentu kodu json powyżej przyjmuje dwa parametry *existingdiagnosticsStorageAccountName* i *existingdiagnosticsStorageResourceGroup* określić wielkość magazynu diagnostyki konto, w którym będą przechowywane dane diagnostyczne. Określanie konta magazynu diagnostyki, jako parametr można łatwo zmienić konto magazynu diagnostyki w różnych środowiskach, na przykład warto użyć różnych diagnostyczne konto magazynu, do testowania i inna dla użytkownika Wdrażanie w środowisku produkcyjnym.  

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

Jest najlepszym rozwiązaniem, aby określić konto magazynu diagnostyki w innej grupie zasobów niż grupa zasobów dla maszyny wirtualnej. Grupa zasobów jest uznawana za jednostkę wdrożenia za pomocą swój własny okres istnienia, maszynę wirtualną można wdrożyć i ponownego wdrażania nowych aktualizacji konfiguracji zostały wprowadzone go do niego, ale możesz chcieć kontynuować przechowywanie danych diagnostycznych na tym samym koncie magazynu w wdrażanie tych maszyn wirtualnych. Masz konto magazynu w różnych zasobów umożliwia konto magazynu, aby akceptować dane od różnych wdrożeń maszyn wirtualnych, co ułatwia rozwiązywanie problemów w różnych wersjach.

> [!NOTE]
> Jeśli utworzysz szablon maszyny wirtualnej systemu windows w programie Visual Studio, domyślne konto magazynu może być ustawione do użycia tego samego konta magazynu, którego dysk VHD maszyny wirtualnej jest przekazany. To uprościć początkowej konfiguracji maszyny wirtualnej. Ponownie wziąć pod uwagę szablon do użycia innego konta magazynu, w można przekazać jako parametru. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Zmienne konfiguracji diagnostyki
Poprzedni fragment kodu json rozszerzenia diagnostyki definiuje *accountid* zmiennej, aby uprościć wprowadzenie klucza konta magazynu do przechowywania diagnostyki:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

*Xmlcfg* właściwość rozszerzenia diagnostyki jest definiowana za pomocą wielu zmiennych, które są połączone ze sobą. Wartości tych zmiennych są w formacie xml w związku z czym muszą być poprzedzone znakiem zmiany znaczenia poprawnie podczas ustawiania zmiennych json.

Na poniższym przykładzie opisano plik xml konfiguracji diagnostyki, która zbiera liczników wydajności poziomu standardowy system wraz z niektórych dzienników zdarzeń systemu windows i dzienniki infrastruktury diagnostycznej. Został poprzedzone znakiem zmiany znaczenia i poprawnie sformatowany, tak aby konfiguracji można wkleić bezpośrednio do sekcji zmiennych szablonu. Zobacz [schemat usługi Diagnostyka konfiguracji](https://msdn.microsoft.com/library/azure/dn782207.aspx) bardziej ludzki przykład można odczytać konfiguracji xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Węzeł xml definicji metryk w powyższej konfiguracji jest element konfiguracji ważne, ponieważ definiuje, jak liczniki wydajności zdefiniowany wcześniej w pliku xml w *PerformanceCounter* zagregowane, przechowywane węzła. 

> [!IMPORTANT]
> Te metryki dysku wykresów i alertów monitorowania w witrynie Azure portal.  **Metryki** węzeł z *resourceID* i **MetricAggregation** muszą być zawarte w konfiguracji diagnostyki dla maszyny Wirtualnej, jeśli chcesz zobaczyć monitorowania danych w maszynie Wirtualnej Witryna Azure portal. 
> 
> 

Poniższy przykład przedstawia kod xml, definicje metryki: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*ResourceID* atrybut unikatowo identyfikuje maszyny wirtualnej w ramach subskrypcji. Pamiętaj użyć funkcji subscription() i resourceGroup(), tak aby ten szablon automatycznie aktualizuje te wartości, w oparciu o subskrypcję i grupę zasobów, które są wdrażane do.

W przypadku tworzenia wielu maszyn wirtualnych w pętli, musisz wypełnić *resourceID* wartość przy użyciu funkcji copyIndex() poprawnie odróżnienie każdej pojedynczej maszyny Wirtualnej. *XmlCfg* wartość mogą być aktualizowane, aby to umożliwić w następujący sposób:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Wartość MetricAggregation *PT1M* i *PT1H* oznaczającego agregacji na minutę i agregację ponad godzinę, odpowiednio.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabel w magazynie
Konfiguracja metryki powyżej generuje tabel w ramach konta magazynu diagnostyki za pomocą następujących konwencji nazewnictwa:

* **WADMetrics**: Standardowa prefiks dla wszystkich tabel WADMetrics
* **PT1H** lub **PT1M**: Oznacza, że tabela zawiera zagregowane dane ponad 1 godzinę lub 1 minuta
* **P10D**: Oznacza, że tabela będzie zawierać dane przez 10 dni od uruchamianiu tabeli zbierania danych
* **V2S**: Stała typu String
* **yyyymmdd**: Data, w którym tabeli rozpoczęcia, zbieranie danych

Przykład: *WADMetricsPT1HP10DV2S20151108* zawiera dane metryk są agregowane w ciągu godziny przez 10 dni, począwszy od listopada-11-2015    

Każda tabela WADMetrics zawiera następujące kolumny:

* **PartitionKey**: Klucz partycji jest tworzony na podstawie *resourceID* wartość do unikatowej identyfikacji zasobu maszyny Wirtualnej. Na przykład: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: W formacie `<Descending time tick>:<Performance Counter Name>`. Malejąco obliczania osi czasu jest impulsów maksymalny czas minus godziną rozpoczęcia okresu agregacji. Na przykład jeśli okres próbkowania pracę od 2015-10-lis i będzie 00:00Hrs UTC, a następnie obliczenie: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Dla dostępne bajty pamięci licznika wydajności klucz wiersza będzie wyglądać następująco: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: To nazwa licznika wydajności. Odpowiada to *counterSpecifier* zdefiniowane w pliku konfiguracyjnym xml.
* **Maksymalna**: Maksymalna wartość licznika wydajności w okresie agregacji.
* **Minimum**: Minimalna wartość licznika wydajności w okresie agregacji.
* **Łączna liczba**: Sumę wszystkich wartości licznika wydajności zgłoszone w okresie agregacji.
* **Liczba**: Całkowita liczba wartości zgłoszone dla licznika wydajności.
* **Średnia**: Wartość średnia (łącznie/count) licznika wydajności w okresie agregacji.

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać kompletny przykładowy szablon maszyny wirtualnej Windows za pomocą rozszerzenia diagnostyki, zobacz [201-maszyny wirtualnej — monitorowanie — — rozszerzenia diagnostyki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Wdrażanie przy użyciu szablonu usługi Azure Resource Manager [programu Azure PowerShell](../windows/ps-template.md) lub [wiersza polecenia platformy Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Dowiedz się więcej o [Tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)
