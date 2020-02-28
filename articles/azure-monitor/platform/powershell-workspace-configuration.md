---
title: Aby utworzyć i skonfigurować obszar roboczy usługi Log Analytics przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Log Analytics obszary robocze w Azure Monitor przechowywanie danych z serwerów w infrastrukturze lokalnej lub w chmurze. Można zbierać dane maszyn z usługi Azure storage wygenerowanym przez narzędzie diagnostyczne systemu Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 6f3f21a7148c59de452d6407fd9a1067b86faae4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659304"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Zarządzanie obszarem roboczym Log Analytics w Azure Monitor przy użyciu programu PowerShell

Za pomocą [poleceń cmdlet programu PowerShell log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) można wykonywać różne funkcje w log Analytics obszarze roboczym w Azure monitor z wiersza polecenia lub jako część skryptu.  Przykłady zadań, które można wykonać przy użyciu programu PowerShell:

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

Ten artykuł zawiera dwa przykłady kodu, które pokazują niektóre funkcje, które można wykonywać za pomocą programu PowerShell.  Aby uzyskać informacje o innych funkcjach, można zapoznać się z artykułem [poleceń cmdlet programu log Analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

> [!NOTE]
> Usługa log Analytics był wcześniej nazywany programem Operational Insights, dlatego jest to nazwa używana w poleceniach cmdlet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady działają w wersji 1.0.0 lub nowszej modułu AZ. OperationalInsights.


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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
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
> Format parametru **CustomLogRawJson** , który definiuje konfigurację dla dziennika niestandardowego, może być skomplikowany. Użyj [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) , aby pobrać konfigurację istniejącego dziennika niestandardowego. Właściwość **Właściwości** jest konfiguracją wymaganą dla parametru **CustomLogRawJson** .

W powyższym przykładzie regexDelimiter został zdefiniowany jako "\\n" dla nowego wiersza. Ogranicznik dziennika może być również sygnaturą czasową.  Są to obsługiwane formaty:

| Format | Format wyrażenia regularnego JSON używa dwóch \\ dla każdego elementu \ w standardowym wyrażeń regularnych, dlatego jeśli testowanie w aplikacji wyrażenia regularnego zmniejsza \\ do \ | | |
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
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> gdzie + is + lub a- <br> gdzie zzzz przesunięcie czasu | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T to litera litera T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurowanie Log Analytics do wysyłania diagnostyki Azure
Bez wykorzystania agentów monitorowania zasobów platformy Azure, zasoby muszą mieć diagnostyki platformy Azure, włączony i skonfigurowany do zapisu do obszaru roboczego usługi Log Analytics. Takie podejście wysyła dane bezpośrednio do obszaru roboczego i nie wymaga zapisywania danych na koncie magazynu. Zasoby obsługiwane obejmują:

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

Aby uzyskać szczegółowe informacje o dostępnych metrykach, zapoznaj się z tematem [obsługiwane metryki z Azure monitor](../../azure-monitor/platform/metrics-supported.md).

Szczegółowe informacje o dostępnych dziennikach znajdują się w tematach [obsługiwane usługi i schematy dzienników zasobów](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Poprzedni polecenie cmdlet umożliwia również zbieranie dzienników z zasobów, które należą do różnych subskrypcji. Polecenie cmdlet może współdziałać między subskrypcjami od momentu, gdy podajesz identyfikator zarówno zasobów tworzących dzienniki, jak i obszar roboczy, do którego są wysyłane dzienniki.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurowanie obszaru roboczego Log Analytics w celu zbierania danych diagnostycznych platformy Azure z magazynu
Aby zebrać dane dzienników z uruchomionego wystąpienia klasyczną usługę w chmurze lub klaster usługi Service fabric, musisz najpierw zapisać dane do usługi Azure storage. Następnie zostanie skonfigurowany obszar roboczy Log Analytics do zbierania dzienników z konta magazynu. Zasoby obsługiwane obejmują:

* Klasyczne cloud services (role sieci web i proces roboczy)
* Klastry usługi Service fabric

W poniższym przykładzie przedstawiono sposób:

1. Wyświetl listę istniejących kont magazynu i lokalizacji, z których obszar roboczy będzie indeksować dane
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

Powyższy skrypt umożliwia również zbieranie dzienników z kont magazynu w różnych subskrypcjach. Skrypt może współdziałać między subskrypcjami od momentu, gdy podajesz identyfikator zasobu konta magazynu i odpowiedni klucz dostępu. Zmiana klucza dostępu, musisz zaktualizować szczegółowe dane o magazynie zapewnienie nowego klucza.


## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [log Analytics poleceniami cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) , aby uzyskać dodatkowe informacje na temat konfigurowania log Analytics przy użyciu programu PowerShell.

