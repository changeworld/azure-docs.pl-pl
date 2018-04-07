---
title: Dodawanie monitorowania i diagnostyki do maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Szablon usługi Azure Resource Manager umożliwia utworzenie nowej maszyny wirtualnej systemu Windows z rozszerzeniem diagnostycznych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: jeconnoc
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
ms.openlocfilehash: e54060769f19546ad3ccb8c52df928eeebf03776
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Użyj monitorowania i diagnostyki z szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager
Rozszerzenie diagnostyki Azure udostępnia możliwości monitorowania i diagnostyki na maszynie wirtualnej z systemem Windows Azure. Te możliwości w maszynie wirtualnej można włączyć w tym rozszerzenia jako częścią szablonu usługi Azure Resource Manager. Zobacz [tworzenia szablonów usługi Azure Resource Manager z rozszerzeń maszyny Wirtualnej](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) uzyskać więcej informacji o tym wszystkie rozszerzenia w ramach szablonu maszyny wirtualnej. W tym artykule opisano, jak dodać rozszerzenie diagnostyki Azure do szablonu maszyny wirtualnej systemu windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Dodaj rozszerzenie diagnostyki Azure w definicji zasobu maszyny Wirtualnej
Aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej systemu Windows, należy dodać rozszerzenie jako zasób maszyny Wirtualnej w szablonie usługi Resource Manager.

Dla prostego Menedżera zasobów konfigurację rozszerzenia, aby dodać oparte na maszynie wirtualnej *zasobów* tablicy dla maszyny wirtualnej: 

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

Inny Konwencji wspólnej polega na dodaniu Konfiguracja rozszerzenia w węźle głównym zasobów szablonu zamiast definiować go w węźle zasobów maszyny wirtualnej. Z tej metody, należy jawnie określić hierarchiczną relację między rozszerzenia i maszynę wirtualną z *nazwa* i *typu* wartości. Na przykład: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Rozszerzenie zawsze jest skojarzony z maszyną wirtualną, możesz bezpośrednio zdefiniować go bezpośrednio w węźle zasobu maszyny wirtualnej lub zdefiniować ją na poziomie podstawowym i umożliwia hierarchiczne konwencji nazewnictwa skojarzyć go z maszyny wirtualnej.

Zestawy skalowania maszyny wirtualnej konfiguracji rozszerzenia jest przewidziany w *extensionProfile* właściwość *VirtualMachineProfile*.

*Wydawcy* właściwość z wartością **Microsoft.Azure.Diagnostics** i *typu* właściwość z wartością **IaaSDiagnostics** jednoznacznie zidentyfikować rozszerzenia diagnostyki Azure.

Wartość *nazwa* właściwości może służyć do odwoływania się do rozszerzenia w grupie zasobów. Ustawienie jej specjalnie do **Microsoft.Insights.VMDiagnosticsSettings** umożliwia można łatwo zidentyfikować w portalu Azure, zapewniając, że monitorowanie wykresy Pokaż się poprawnie w portalu Azure.

*TypeHandlerVersion* Określa wersję rozszerzenia, o których chcesz użyć. Ustawienie *autoUpgradeMinorVersion* wersja pomocnicza do **true** gwarantuje, że możesz pobrać najnowszą wersję pomocniczą rozszerzenia, które jest dostępne. Zdecydowanie zaleca się, że możesz zawsze ustawić *autoUpgradeMinorVersion* zawsze być **true** tak, aby zawsze uzyskać próbę użycia najnowsze rozszerzenia diagnostyki dostępne nowe funkcje i poprawki błędów. 

*Ustawienia* element zawiera właściwości konfiguracji rozszerzenia, które można ustawić i ponownie odczytywana rozszerzenia (nazywane czasami publicznej konfiguracji). *Xmlcfg* właściwość zawiera konfigurację xml na podstawie dzienników diagnostycznych liczniki wydajności itp., które są zbierane przez agenta diagnostyki. Zobacz [schemat konfiguracji diagnostyki](https://msdn.microsoft.com/library/azure/dn782207.aspx) uzyskać więcej informacji na temat samego schematu xml. Typowym rozwiązaniem jest przechowywanie rzeczywistego pliku xml konfiguracji jako zmienną w szablonie usługi Azure Resource Manager i następnie połącz i base64 kodować je, aby ustawić wartość *xmlcfg*. Zobacz sekcję dotyczącą [zmienne konfiguracji diagnostyki](#diagnostics-configuration-variables) Aby dowiedzieć się więcej o sposobie przechowywania xml w zmiennych. *StorageAccount* właściwość określa nazwę konta magazynu, do których diagnostyki transferu danych. 

Właściwości w *protectedSettings* (Konfiguracja czasami określone jako prywatne) można ustawić, ale nie można odczytać po ustawiany. Tylko do zapisu rodzaj *protectedSettings* ułatwia przydatne w przypadku przechowywania kluczy tajnych, takie jak klucz konta magazynu którym są zapisywane dane diagnostyczne.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Określanie konta magazynu diagnostyki jako parametrów
Powyżej fragment json rozszerzenia diagnostyki przyjmuje dwa parametry *existingdiagnosticsStorageAccountName* i *existingdiagnosticsStorageResourceGroup* określić wielkość magazynu diagnostyki konto, w którym będą przechowywane dane diagnostyczne. Określanie diagnostycznego konta magazynu, zgodnie z parametrem można łatwo zmienić konto magazynu diagnostyki w różnych środowiskach, na przykład może chcesz użyć konta magazynu różne dane diagnostyczne do testowania, a druga dla użytkownika wdrożenia produkcyjnego.  

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

Jest to najlepsze rozwiązanie, aby określić konto magazynu diagnostyki w innej grupie zasobów niż grupa zasobów dla maszyny wirtualnej. Grupa zasobów jest uznawana za jednostkę wdrożenia własnej okres istnienia, maszynę wirtualną można wdrożyć i wdrożone nowe aktualizacje konfiguracji zostały wprowadzone go do niego, ale można kontynuować przechowywanie danych diagnostycznych z tego samego konta magazynu przez te wdrożenia maszyny wirtualnej. Mając konto magazynu w różnych zasobów umożliwia konta magazynu, aby akceptować dane z różnych wdrożenia maszyny wirtualnej, co ułatwia rozwiązywanie problemów w różnych wersjach.

> [!NOTE]
> Jeśli tworzysz szablon maszyny wirtualnej systemu windows z programu Visual Studio, domyślne konto magazynu może być ustawiona do używania tego samego konta magazynu, gdzie jest przekazywany dysk VHD maszyny wirtualnej. To uprościć początkowej konfiguracji maszyny wirtualnej. Ponownie współczynnika szablonu, aby użyć innego konta magazynu, który może być przekazany jako parametr. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Zmienne konfiguracji diagnostyki
Definiuje powyżej fragment json rozszerzenia diagnostyki *accountid* zmiennej, aby uprościć pobieranie klucz konta magazynu dla magazynu diagnostyki:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

*Xmlcfg* właściwości rozszerzenia diagnostyki jest definiowana za pomocą wielu zmiennych, które są połączone ze sobą. Wartości tych zmiennych są w formacie xml, więc muszą być prawidłowo wpisywany podczas ustawiania zmiennych json.

Poniżej opisano plik xml konfiguracji diagnostyki, który zbiera dane liczników wydajności poziomu standardowych systemowych oraz niektóre dzienniki zdarzeń systemu windows i dzienników infrastruktury Diagnostyka. Ma zostały zmienione znaczenie i prawidłowo sformatowane, dzięki czemu konfiguracji można wkleić bezpośrednio do sekcji zmiennych szablonu. Zobacz [schemat konfiguracji diagnostyki](https://msdn.microsoft.com/library/azure/dn782207.aspx) więcej człowieka przykład do odczytu pliku konfiguracji XML.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Węzeł xml definicji metryk w powyższej konfiguracji jest element konfiguracji ważne, ponieważ definiuje sposób liczników wydajności zdefiniowanych we wcześniejszej części kodu xml w *PerformanceCounter* węzła są agregowane i przechowywane. 

> [!IMPORTANT]
> Te metryki dysku wykresy monitorowania i alertów w portalu Azure.  **Metryki** węzła o *resourceID* i **MetricAggregation** muszą być zawarte w konfiguracji diagnostyki dla maszyny Wirtualnej, jeśli chcesz wyświetlić dane w portalu Azure monitorowania maszyny Wirtualnej. 
> 
> 

Poniżej przedstawiono przykładowy kod xml definicji metryk: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*ResourceID* atrybutu unikatowo identyfikuje maszyny wirtualnej w ramach subskrypcji. Upewnij się, że należy użyć funkcji subscription() i resourceGroup() tak, aby szablon automatycznie aktualizuje te wartości na podstawie subskrypcji i grupy zasobów, które wdrażasz.

W przypadku tworzenia wielu maszyn wirtualnych w pętli, musisz wypełnić *resourceID* wartości z funkcji copyIndex() poprawnie rozróżnianie każdego poszczególnych maszyn wirtualnych. *XmlCfg* wartość może zostać zaktualizowane do obsługi to w następujący sposób:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Wartość MetricAggregation *PT1M* i *PT1H* odpowiednio oznaczającego agregacji na minutę i agregacji w ciągu godziny.

## <a name="wadmetrics-tables-in-storage"></a>Tabele WADMetrics w magazynie
Konfiguracja metryki powyżej generuje tabel na koncie magazynu diagnostyki z następujących konwencji nazewnictwa:

* **WADMetrics**: standardowego prefiksu dla wszystkich tabel WADMetrics
* **PT1H** lub **PT1M**: oznacza, że tabela zawiera agregowanie danych ponad 1 godzina lub 1 minuta
* **P10D**: oznacza, że tabela będzie zawierać danych dla 10 dni od uruchomienia tabeli na zbieranie danych
* **V2S**: stała znakowa
* **RRRRMMDD**: Data, w którym tabeli rozpoczęcia, zbieranie danych

Przykład: *WADMetricsPT1HP10DV2S20151108* zawierają zagregowane ponad godzinę 10 dni, począwszy od dnia 2015-11-lis dane metryk    

Każda tabela WADMetrics będzie zawierać następujące kolumny:

* **PartitionKey**: Klucz partycji jest tworzony na podstawie *resourceID* wartość do unikatowej identyfikacji zasobu maszyny Wirtualnej. Na przykład: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: zgodne z formatem `<Descending time tick>:<Performance Counter Name>`. Malejącej obliczanie osi czasu jest Takty maksymalny czas minus godzina rozpoczęcia okresu agregacji. Na przykład jeśli okres próbki rozpoczęty w dniu 2015-10-lis i będzie 00:00Hrs UTC, a następnie obliczenia: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Dla dostępnych bajtów pamięci licznika wydajności klucz wiersza będą wyglądać jak: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: Nazwa licznika wydajności. Odpowiada *counterSpecifier* zdefiniowane w pliku konfiguracji xml.
* **Maksymalna**: maksymalna wartość licznika wydajności w okresie agregacji.
* **Minimalna**: minimalną wartość licznika wydajności w okresie agregacji.
* **Całkowita liczba**: sumę wszystkich wartości licznika wydajności zgłoszonych w okresie agregacji.
* **Liczba**: zgłosił sumę wartości licznika wydajności.
* **Średnia**: wartość średnia (łączna/liczba) licznika wydajności w okresie agregacji.

## <a name="next-steps"></a>Następne kroki
* Kompletnego przykładowego szablonu z rozszerzeniem diagnostyki maszyny wirtualnej systemu Windows, temacie [201-rozszerzenia maszyny wirtualnej — monitorowanie diagnostycznych](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Wdrażanie przy użyciu szablonu usługi Azure Resource Manager [programu Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lub [wiersza polecenia platformy Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Dowiedz się więcej o [tworzenia szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)
