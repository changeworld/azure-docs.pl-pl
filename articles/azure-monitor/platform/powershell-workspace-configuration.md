---
title: Tworzenie & konfigurowanie usługi Log Analytics za pomocą programu PowerShell
description: Obszary robocze usługi Log Analytics w usłudze Azure Monitor przechowują dane z serwerów w infrastrukturze lokalnej lub w chmurze. Dane maszyny z usługi Azure Storage są zbierane podczas generowania przez diagnostykę platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054707"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Zarządzanie obszarem roboczym usługi Log Analytics w usłudze Azure Monitor przy użyciu programu PowerShell

[Polecenia cmdlet programu Microsoft usługi Log Analytics powershell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) służy do wykonywania różnych funkcji w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor z wiersza polecenia lub jako część skryptu.  Przykłady zadań, które można wykonać za pomocą programu PowerShell:

* Tworzenie obszaru roboczego
* Dodawanie lub usuwanie roztworu
* Importowanie i eksportowanie zapisanych wyszukiwań
* Tworzenie grupy komputerów
* Włączanie zbierania dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
* Zbieranie liczników wydajności z komputerów z systemem Linux i Windows
* Zbieranie zdarzeń z syslog na komputerach z systemem Linux
* Zbieranie zdarzeń z dzienników zdarzeń systemu Windows
* Zbieranie niestandardowych dzienników zdarzeń
* Dodawanie agenta analizy dzienników do maszyny wirtualnej platformy Azure
* Konfigurowanie analizy dzienników do indeksowania danych zebranych przy użyciu diagnostyki platformy Azure

Ten artykuł zawiera dwa przykłady kodu, które ilustrują niektóre funkcje, które można wykonać z programu PowerShell.  Można odwołać się do [polecenia cmdlet usługi Log Analytics programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) dla innych funkcji.

> [!NOTE]
> Usługa Log Analytics była wcześniej nazywana analizą operacyjną, dlatego jest nazwą używaną w poleceniach cmdlet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady działają z wersją 1.0.0 lub nowszą modułu Az.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Tworzenie i konfigurowanie obszaru roboczego usługi Log Analytics
Poniższy przykład skryptu ilustruje, jak:

1. Tworzenie obszaru roboczego
2. Wymień dostępne rozwiązania
3. Dodawanie rozwiązań do obszaru roboczego
4. Importowanie zapisanych wyszukiwań
5. Eksportowanie zapisanych wyszukiwań
6. Tworzenie grupy komputerów
7. Włączanie zbierania dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
8. Zbieranie liczników perf dysku logicznego z komputerów z systemem Linux (% używanych iod; Darmowe megabajty; % używanej przestrzeni; Transfery dysków/s; Odczyty dysku/s; Zapisy na dysku/s)
9. Zbieranie zdarzeń syslog z komputerów z systemem Linux
10. Zbieranie zdarzeń błędów i ostrzeżeń z dziennika zdarzeń aplikacji z komputerów z systemem Windows
11. Licznik wydajności Zbieranie pamięci dostępnych bajtów z komputerów z systemem Windows
12. Zbieranie dziennika niestandardowego

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
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
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
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
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

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

> [!NOTE]
> Format parametru **CustomLogRawJson,** który definiuje konfigurację dziennika niestandardowego, może być złożony. Użyj [Get-AzOperationalInsightsDataSource,](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) aby pobrać konfigurację dla istniejącego dziennika niestandardowego. **Właściwość właściwości** jest konfiguracja wymagana dla **Parametru CustomLogRawJson.**

W powyższym przykładzie regexDelimiter\\został zdefiniowany jako "n" dla nowej linii. Ogranicznik dziennika może być również sygnaturą czasową.  Są to obsługiwane formaty:

| Format | Format Json RegEx \\ używa dwóch dla każdego \ w standardowym RegEx, więc jeśli testowanie w aplikacji RegEx zmniejszyć \\ do \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> dwie spacje po MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> gdzie + jest + lub <br> gdzie przesunięcie czasu zzzz | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T jest literą literową T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurowanie usługi Log Analytics do wysyłania diagnostyki platformy Azure
W przypadku bezagentowego monitorowania zasobów platformy Azure zasoby muszą mieć włączoną i skonfigurowaną diagnostykę platformy Azure i skonfigurowaną do zapisu w obszarze roboczym usługi Log Analytics. Takie podejście wysyła dane bezpośrednio do obszaru roboczego i nie wymaga, aby dane były zapisywane na koncie magazynu. Obsługiwane zasoby obejmują:

| Typ zasobu | Dzienniki | Metryki |
| --- | --- | --- |
| Bramy aplikacji    | Tak | Tak |
| Konta usługi Automation     | Tak | |
| Konta usługi Batch          | Tak | Tak |
| Analiza usługi Data Lake     | Tak | |
| Magazyn Data Lake         | Tak | |
| Elastyczna pula SQL        |     | Tak |
| Przestrzeń nazw centrum zdarzeń     |     | Tak |
| Centra IoT Hub                |     | Tak |
| Usługa Key Vault               | Tak | |
| Moduły równoważenia obciążenia          | Tak | |
| Logic Apps              | Tak | Tak |
| Grupy zabezpieczeń sieci | Tak | |
| Azure Cache for Redis             |     | Tak |
| Usługi wyszukiwania         | Tak | Tak |
| Obszar nazw usługi Service Bus   |     | Tak |
| SQL (wersja 12)               |     | Tak |
| Witryny sieci Web               |     | Tak |
| Farmy serwerów sieci Web        |     | Tak |

Szczegółowe informacje na temat dostępnych metryk można znaleźć w [metrykach obsługiwanych za pomocą usługi Azure Monitor.](../../azure-monitor/platform/metrics-supported.md)

Szczegółowe informacje na temat dostępnych dzienników można znaleźć w [obsługiwanych usługach i schemacie dzienników zasobów.](../../azure-monitor/platform/diagnostic-logs-schema.md)

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Można również użyć poprzedniego polecenia cmdlet do zbierania dzienników z zasobów, które znajdują się w różnych subskrypcji. Polecenie cmdlet może działać w ramach subskrypcji, ponieważ udostępniasz identyfikator dziennika tworzenia zasobu i obszaru roboczego, do którego są wysyłane dzienniki.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurowanie obszaru roboczego usługi Log Analytics w celu zbierania diagnostyki platformy Azure z magazynu
Aby zbierać dane dziennika z poziomu uruchomionego wystąpienia klasycznej usługi w chmurze lub klastra sieci szkieletowej usług, należy najpierw zapisać dane w magazynie platformy Azure. Obszar roboczy usługi Log Analytics jest następnie skonfigurowany do zbierania dzienników z konta magazynu. Obsługiwane zasoby obejmują:

* Klasyczne usługi w chmurze (role sieci web i procesowe)
* Klastry sieci szkieletowej usług

W poniższym przykładzie pokazano, jak:

1. Wyświetl listę istniejących kont magazynu i lokalizacji, z których obszar roboczy będzie indeksować dane
2. Tworzenie konfiguracji do odczytu z konta magazynu
3. Aktualizowanie nowo utworzonej konfiguracji do indeksu danych z dodatkowych lokalizacji
4. Usuwanie nowo utworzonej konfiguracji

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

Poprzedni skrypt służy również do zbierania dzienników z kont magazynu w różnych subskrypcjach. Skrypt może działać w różnych subskrypcjach, ponieważ udostępniasz identyfikator zasobu konta magazynu i odpowiedni klucz dostępu. Po zmianie klucza dostępu, należy zaktualizować wgląd magazynu, aby mieć nowy klucz.


## <a name="next-steps"></a>Następne kroki
* [Przejrzyj polecenia cmdlet programu Log Analytics programu PowerShell,](https://docs.microsoft.com/powershell/module/az.operationalinsights/) aby uzyskać dodatkowe informacje na temat używania programu PowerShell do konfiguracji usługi Log Analytics.

