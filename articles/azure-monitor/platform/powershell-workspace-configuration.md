---
title: Aby utworzyć i skonfigurować obszar roboczy usługi Log Analytics przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Zaloguj Analytics używa danych z serwerów lokalnych lub infrastruktury chmury. Można zbierać dane maszyn z usługi Azure storage wygenerowanym przez narzędzie diagnostyczne systemu Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: e34d45d2d7c81ec5f15a5441ce2bb0f082c65155
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884116"
---
# <a name="manage-log-analytics-using-powershell"></a>Zarządzanie usługą Log Analytics przy użyciu programu PowerShell
Możesz użyć [poleceń cmdlet programu PowerShell programu Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) do wykonywania różnych funkcji w usłudze Log Analytics przy użyciu wiersza polecenia lub w ramach skryptu.  Przykłady zadań, które można wykonać przy użyciu programu PowerShell:

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

Ten artykuł zawiera dwa przykłady kodu, które pokazują niektóre funkcje, które można wykonywać za pomocą programu PowerShell.  Możesz zapoznać się z [Dokumentacja poleceń cmdlet środowiska PowerShell programu Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) do innych funkcji.

> [!NOTE]
> Usługa log Analytics był wcześniej nazywany programem Operational Insights, dlatego jest to nazwa używana w poleceniach cmdlet.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady pracy z wersją 2.3.0 lub nowszej moduł AzureRm.OperationalInsights.


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

```

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
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
W powyższym przykładzie regexDelimiter został zdefiniowany jako "\\n" dla nowego wiersza. Ogranicznik dziennika może być również sygnaturę czasową.  Poniżej przedstawiono obsługiwane formaty:

| Format | Format wyrażenia regularnego JSON korzysta z dwóch \\ dla każdego \ w standardowych wyrażeń regularnych więc jeśli testowania w aplikacji w języku wyrażeń regularnych zmniejszyć \\ do \ |
| --- | --- |
| RRRR MM-DD HH: MM: | ((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))\\\\s((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9] |
| M/D/RRRR GG: MM: SS AM/PM | (([0-1]\\\\d)\|[0-9]) / (([0-3]\\\\d)\|(\\\\d)) / ((\\\\d{2})\|() \\ \\d{4}))\\\\s ((\\\\d)\|([0-1]\\\\d)\|(2[0-4])): [0-5] [0-9]: [ 0-5] [0-9]\\\\s (AM\|PM\|jestem\|pm) |
| dd/mm/rrrr hh: mm: | ((([0-3]\\\\d)\|(\\\\d)) / (sty\|lutego\|marca\|maja\|kwi\|lip\|cze\|Sie\|Oct\|wrz\|lis\|gru\|sty\|lutego\|Oznacz\|może\|kwi\|lip\|cze\|sie\|oct\|wrz\|lis\|gru) / ((\\\\d{2})\|(\\\\d{4})) \\ \\s ((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9]) |
| MMM dd, rrrr hh: mm: | (((?: Jan(?:uary)? \|Feb(?:ruary)? \|Mar(?:ch)? \|Apr(?:il)? \|Może\|Jun(?:e)?\| Jul(?:y)? \|Aug(?:ust)? \|Sep(?:tember)? \|Września\|Oct(?:o numer)? \|Nov(?:ember)? \|Dec(?:ember)?)). *? ((?: (?: [0-2]? \\ \\d{1})\|(?: [3] [01]{1}))) (?! [\\\\d]).* ? ((?: (?: [1]{1}\\\\d{1}\\\\d{1}\\\\d{1})\|(?: [2]{1} \\ \\d{3}))) (?! [\\\\d]). *? ((?: (?: [0-1][0-9])\|(?: [2][0-3])\|(?: [0-9])):(?:[0-5][0-9])(?::[0-5][0-9])? (?:\\\\s? (?: am\|AM\|pm\|PM))?)) |
| rrmmdd: mm: ss | ([0-9]{2}([0] [1-9]\|[1][0-2]) ([0-2] [0-9]\|[3][0-1])\\\\s\\\\s? () [0-1]? [0-9] \|[2][0-3]):[0-5][0-9]:[0-5][0-9]) |
| ddMMyy: mm: ss | (([0-2] [0-9]\|[3][0-1]) ([0] [1-9]\|[1][0-2]) [0-9]{2}\\\\s\\\\s? () [0-1]? [0-9] \|[2][0-3]):[0-5][0-9]:[0-5][0-9]) |
| MMM d hh: mm: | (Sty\|lutego\|marca\|kwi\|może\|cze\|lip\|sie\|wrz\|Oct\|lis\|gru)\\ \\s\\\\s? () [0]? [1-9] \|[1 - 2] [0-9]\|[3][0-1])\\\\s ([0-1]? [ 0-9]\|[2][0-3]):([0-5][0-9]):([0-5][0-9]) |
| MMM d hh: mm:<br> dwie spacje po MMM | (Sty\|lutego\|marca\|kwi\|może\|cze\|lip\|sie\|wrz\|Oct\|lis\|gru)\\ \\s\\\\s ([0]? [ 1-9]\|[1 - 2] [0-9]\|[3][0-1])\\\\s ([0] [0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9]) |
| MMM d hh: mm: | (Sty\|lutego\|marca\|kwi\|może\|cze\|lip\|sie\|wrz\|Oct\|lis\|gru)\\ \\s ([0]? [ 1-9]\|[1 - 2] [0-9]\|[3][0-1])\\\\s ([0] [0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9]) |
| dd/mm/yyyy + zzzz<br> gdzie + jest + lub -<br> gdy przesunięcie czasu zzzz | (([0-2] [1-9]\|[3][0-1])\\\\/ (sty\|lutego\|marca\|kwi\|maja\|cze\|lip\|sie\|wrz \|Oct\|lis\|gru)\\\\/((19\|20) [0-9] [0-9]): ([0] [0-9]\|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\ \\s [\\\\+\|\\\\-] [0-9]{4}) |
| yyyy-MM-ddTHH:mm:ss<br> T jest literałem litera T | ((\\\\d{2})\|(\\\\d{4}))-([0-1]\\\\d)-(([0-3]\\\\d)\|(\\\\d))T((\\\\d)\|([0-1]\\\\d)\|(2[0-4])):[0-5][0-9]:[0-5][0-9] |

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfigurowanie usługi Log Analytics do indeksowania usługi Diagnostyka Azure
Bez wykorzystania agentów monitorowania zasobów platformy Azure, zasoby muszą mieć diagnostyki platformy Azure, włączony i skonfigurowany do zapisu do obszaru roboczego usługi Log Analytics. Ta metoda wysyła dane bezpośrednio do usługi Log Analytics i nie wymaga dane są zapisywane na koncie magazynu. Zasoby obsługiwane obejmują:

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

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Poprzedni polecenie cmdlet umożliwia również zbieranie dzienników z zasobów, które należą do różnych subskrypcji. Polecenie cmdlet jest w stanie działać w subskrypcjach, ponieważ informujemy identyfikator zasobu tworzenia dzienników i dzienniki są wysyłane do obszaru roboczego.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Konfigurowanie usługi Log Analytics do indeksowania diagnostyki platformy Azure z usługi storage
Aby zebrać dane dzienników z uruchomionego wystąpienia klasyczną usługę w chmurze lub klaster usługi Service fabric, musisz najpierw zapisać dane do usługi Azure storage. Log Analytics następnie zostanie skonfigurowana do zbierania dzienników z konta magazynu. Zasoby obsługiwane obejmują:

* Klasyczne cloud services (role sieci web i proces roboczy)
* Klastry usługi Service fabric

W poniższym przykładzie przedstawiono sposób:

1. Wyświetlanie listy istniejących kont magazynu i lokalizacje, które są indeksowane dane z usługi Log Analytics
2. Utwórz konfigurację do odczytu z konta magazynu
3. Zaktualizuj nowo utworzoną konfigurację do indeksowania danych z lokalizacji dodatkowej
4. Usuń konfigurację nowo utworzony

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Powyższy skrypt umożliwia również zbieranie dzienników z kont magazynu w różnych subskrypcjach. Skrypt jest w stanie działać w subskrypcjach, ponieważ informujemy identyfikator zasobu konta magazynu i odpowiedni klucz dostępu. Zmiana klucza dostępu, musisz zaktualizować szczegółowe dane o magazynie zapewnienie nowego klucza.


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd poleceń cmdlet programu PowerShell programu Log Analytics](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) dodatkowe informacje na temat konfiguracji usługi Log Analytics przy użyciu programu PowerShell.

