---
title: Aby utworzyć i skonfigurować obszar roboczy usługi Log Analytics przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Obszary robocze usługi log Analytics w usłudze Azure Monitor przechowywania danych z serwerów w sieci lokalnej lub infrastruktury chmury. Można zbierać dane maszyn z usługi Azure storage wygenerowanym przez narzędzie diagnostyczne systemu Azure.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: bwren
ms.openlocfilehash: 2d3f1ab6704a0f5ecd15190fd08b10485cdf1ee9
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510113"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Zarządzanie roboczym usługi Log Analytics w usłudze Azure Monitor przy użyciu programu PowerShell

Możesz użyć [poleceń cmdlet programu PowerShell programu Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) do wykonywania różnych funkcji w obszarze roboczym Log Analytics w usłudze Azure Monitor z wiersza polecenia lub w ramach skryptu.  Przykłady zadań, które można wykonać przy użyciu programu PowerShell:

* Tworzenie obszaru roboczego
* Dodawanie lub usuwanie rozwiązania
* Importowanie i eksportowanie zapisanych wyszukiwań
* Utwórz grupę komputerów
* Włącz zbieranie dzienników usług IIS na komputerach z zainstalowanym agentem Windows
* Liczniki wydajności są zbierane z komputerów z systemami Linux i Windows
* Zbieraj zdarzenia z dziennika systemu na komputerach z systemem Linux
* Zbieranie zdarzeń z dzienników zdarzeń Windows
* Zbieranie dzienników zdarzeń niestandardowych
* Dodaj agenta usługi log analytics na maszynie wirtualnej platformy Azure
* Skonfiguruj usługę log analytics do indeksowania danych zebranych za pomocą diagnostyki Azure

Ten artykuł zawiera dwa przykłady kodu, które pokazują niektóre funkcje, które można wykonywać za pomocą programu PowerShell.  Możesz zapoznać się z [Dokumentacja poleceń cmdlet środowiska PowerShell programu Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) do innych funkcji.

> [!NOTE]
> Usługa log Analytics był wcześniej nazywany programem Operational Insights, dlatego jest to nazwa używana w poleceniach cmdlet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady pracy z wersją 1.0.0 lub nowszej moduł Az.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Tworzenie i konfigurowanie obszaru roboczego usługi Log Analytics
Pokazano w następującym przykładowym skrypcie jak:

1. Tworzenie obszaru roboczego
2. Lista dostępnych rozwiązań
3. Dodawanie rozwiązań do obszaru roboczego
4. Importuj zapisane wyszukiwania
5. Eksportowanie zapisane wyszukiwania
6. Utwórz grupę komputerów
7. Włącz zbieranie dzienników usług IIS na komputerach z zainstalowanym agentem Windows
8. Zbieranie liczników wydajności dysku logicznego z komputerów z systemem Linux (% użytych węzłów i; Wolne megabajty; Używany obszar; % Transfery dyskowe/s; Odczyty dysku/s; Zapisy dysku/s)
9. Zbieranie zdarzeń dziennika systemu z komputerów z systemem Linux
10. Zbieranie zdarzeń błędu i ostrzeżenia w dzienniku zdarzeń aplikacji z komputerów Windows
11. Zbieraj dane licznika wydajności dostępna pamięć (MB) z komputerów Windows
12. Zbieranie dzienników niestandardowych

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
W powyższym przykładzie regexDelimiter został zdefiniowany jako "\\n" dla nowego wiersza. Ogranicznik dziennika może być również sygnaturę czasową.  Poniżej przedstawiono obsługiwane formaty:

| Format | Format wyrażenia regularnego JSON korzysta z dwóch \\ dla każdego \ w standardowych wyrażeń regularnych więc jeśli testowania w aplikacji w języku wyrażeń regularnych zmniejszyć \\ do \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\\\d)\|[0-9])/(([0-3]\\\\d)\|(\\\\d))/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\\\s(AM\|PM\|am\|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `((([0-3]\\\\d)\` | `(\\\\d))/(Jan\|Feb\|Mar\|May\|Apr\|Jul\|Jun\|Aug\|Oct\|Sep\|Nov\|Dec\|jan\|feb\|mar\|may\|apr\|jul\|jun\|aug\|oct\|sep\|nov\|dec)/((\\\\d{2})\|(\\\\d{4}))\\\\s((\\\\d)\` | `([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?\|Feb(?:ruary)?\|Mar(?:ch)?\|Apr(?:il)?\|May\|Jun(?:e)?\|Jul(?:y)?\|Aug(?:ust)?\|Sep(?:tember)?\|Sept\|Oct(?:ober)?\|Nov(?:ember)?\|Dec(?:ember)?)).*?((?:(?:[0-2]?\\\\d{1})\|(?:[3][01]{1})))(?![\\\\d]).*?((?:(?:[1]{1}\\\\d{1}\\\\d{1}\\\\d{1})\|(?:[2]{1}\\\\d{3})))(?![\\\\d]).*?((?:(?:[0-1][0-9])\|(?:[2][0-3])\|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\\\s?(?:am\|AM\|pm\|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]\|[1][0-2])([0-2][0-9]\|[3][0-1])\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]\|[3][0-1])([0][1-9]\|[1][0-2])[0-9]{2}\\\\s\\\\s?([0-1]?[0-9]\|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s?([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0-1]?[0-9]\|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> dwie spacje po MMM | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\s([0]?[1-9]\|[1-2][0-9]\|[3][0-1])\\\\s([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> gdzie + jest + lub - <br> gdy przesunięcie czasu zzzz | `(([0-2][1-9]\|[3][0-1])\\\\/(Jan\|Feb\|Mar\|Apr\|May\|Jun\|Jul\|Aug\|Sep\|Oct\|Nov\|Dec)\\\\/((19\|20)[0-9][0-9]):([0][0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\\\s[\\\\+\|\\\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T jest literałem litera T | `((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))T((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurowanie usługi Log Analytics do wysyłania usługi Diagnostyka Azure
Bez wykorzystania agentów monitorowania zasobów platformy Azure, zasoby muszą mieć diagnostyki platformy Azure, włączony i skonfigurowany do zapisu do obszaru roboczego usługi Log Analytics. Ta metoda wysyła dane bezpośrednio do obszaru roboczego i nie wymaga dane są zapisywane na koncie magazynu. Zasoby obsługiwane obejmują:

| Typ zasobu | Dzienniki | Metryki |
| --- | --- | --- |
| Bramy aplikacji    | Yes | Yes |
| Konta usługi Automation     | Yes | |
| Konta usługi Batch          | Yes | Yes |
| Data Lake analytics     | Yes | |
| Usługa Data Lake store         | Yes | |
| Pula elastyczna SQL        |     | Yes |
| Przestrzeń nazw centrum zdarzeń     |     | Yes |
| Centra IoT Hub                |     | Yes |
| Usługa Key Vault               | Yes | |
| Moduły równoważenia obciążenia          | Yes | |
| Logic Apps              | Yes | Yes |
| Grupy zabezpieczeń sieci | Yes | |
| Azure Cache for Redis             |     | Yes |
| Usługi wyszukiwania         | Yes | Yes |
| Przestrzeń nazw magistrali usług   |     | Yes |
| SQL (v12)               |     | Yes |
| Witryny sieci Web               |     | Yes |
| Farmach serwerów sieci Web        |     | Yes |

Szczegóły dostępne metryki, można znaleźć [metryki obsługiwane z usługą Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Szczegóły dostępne dzienniki, można znaleźć [obsługiwane usługi i schematu dla dzienników diagnostycznych](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Poprzedni polecenie cmdlet umożliwia również zbieranie dzienników z zasobów, które należą do różnych subskrypcji. Polecenie cmdlet jest w stanie działać w subskrypcjach, ponieważ udostępniasz identyfikator zasobu tworzenia dzienników i dzienniki są wysyłane do obszaru roboczego.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurowanie obszaru roboczego usługi Log Analytics, zbieranie diagnostyki platformy Azure z magazynu
Aby zebrać dane dzienników z uruchomionego wystąpienia klasyczną usługę w chmurze lub klaster usługi Service fabric, musisz najpierw zapisać dane do usługi Azure storage. Obszar roboczy usługi Log Analytics jest następnie konfigurowana do zbierania dzienników z konta magazynu. Zasoby obsługiwane obejmują:

* Klasyczne cloud services (role sieci web i proces roboczy)
* Klastry usługi Service fabric

W poniższym przykładzie przedstawiono sposób:

1. Wyświetlanie listy istniejących kont magazynu i lokalizacje, które są indeksowane dane z obszaru roboczego
2. Utwórz konfigurację do odczytu z konta magazynu
3. Zaktualizuj nowo utworzoną konfigurację do indeksowania danych z lokalizacji dodatkowej
4. Usuń konfigurację nowo utworzony

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Powyższy skrypt umożliwia również zbieranie dzienników z kont magazynu w różnych subskrypcjach. Skrypt jest w stanie działać w subskrypcjach, ponieważ informujemy identyfikator zasobu konta magazynu i odpowiedni klucz dostępu. Zmiana klucza dostępu, musisz zaktualizować szczegółowe dane o magazynie zapewnienie nowego klucza.


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd poleceń cmdlet programu PowerShell programu Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) dodatkowe informacje na temat konfiguracji usługi Log Analytics przy użyciu programu PowerShell.

