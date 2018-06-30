---
title: Tworzenie i konfigurowanie obszaru roboczego analizy dzienników przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Rejestrowanie danych dotyczących Analytics korzysta z serwerów w sieci lokalnej lub w chmurze infrastruktury. Można zbierać dane maszyny z usługi Azure storage, gdy generowane przez diagnostycznych platformy Azure.
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
ms.component: na
ms.openlocfilehash: 375ae9a82af4a1f8a86b529b597ed479388e66dc
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129340"
---
# <a name="manage-log-analytics-using-powershell"></a>Zarządzanie usługą Log Analytics przy użyciu programu PowerShell
Można użyć [poleceń cmdlet programu PowerShell analizy dziennika](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) do wykonywania różnych funkcji w analizy dzienników przy użyciu wiersza polecenia lub w ramach skryptu.  Przykłady zadania, które można wykonać przy użyciu programu PowerShell:

* Tworzenie obszaru roboczego
* Dodawanie lub usuwanie rozwiązania
* Importowanie i eksportowanie zapisanych wyszukiwań
* Tworzenie grupy komputerów
* Włącz zbieranie dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
* Zebrać liczników wydajności z komputerów z systemami Linux i Windows
* Zbierać zdarzenia z dziennika systemowego na komputerach z systemem Linux 
* Zbieranie zdarzeń z dzienników zdarzeń systemu Windows
* Zbieranie dzienników zdarzeń niestandardowych
* Dodaj agenta analizy dziennika do maszyny wirtualnej platformy Azure
* Konfigurowanie analizy dzienników do indeksowania danych zbieranych za pomocą diagnostyki Azure

W tym artykule przedstawiono dwa przykłady ilustrujące niektórych funkcji, które można wykonywać z programu PowerShell.  Można to sprawdzić [dokumentacji poleceń cmdlet programu PowerShell analizy dziennika](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) do innych funkcji.

> [!NOTE]
> Analiza dzienników była wcześniej określana usługi Operational Insights, dlatego jest to nazwa używana w polecenia cmdlet.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady pracy z wersją 2.3.0 lub nowszej modułu AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Tworzenie i konfigurowanie obszaru roboczego analizy dzienników
Przedstawiono w następującym przykładowym skrypcie jak:

1. Tworzenie obszaru roboczego
2. Lista dostępnych rozwiązań
3. Dodawanie rozwiązania do obszaru roboczego
4. Importuj zapisane wyszukiwania
5. Eksport zapisane wyszukiwania
6. Tworzenie grupy komputerów
7. Włącz zbieranie dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
8. Zbierania z komputerów z systemem Linux liczników wydajności dysku logicznego (% użytych węzłów i; Wolne megabajty; % Używane miejsce; Transfery dyskowe/s; Odczyty dysku/s; Zapisy dysku/s)
9. Zbieraj zdarzenia dziennika systemowego z komputerów z systemem Linux
10. Zbieranie zdarzeń błędu i ostrzeżenia w dzienniku zdarzeń aplikacji z komputerów z systemem Windows
11. Zbieraj dane licznika wydajności dostępna pamięć (MB) z komputerów z systemem Windows
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
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Konfigurowanie analizy dzienników do indeksowania Diagnostyka Azure
Bez agenta monitorowania zasobów platformy Azure, zasoby muszą mieć Diagnostyka Azure włączona i skonfigurowana do zapisania do obszaru roboczego analizy dzienników. Takie podejście wysyła dane bezpośrednio do analizy dzienników i nie wymaga dane są zapisywane na koncie magazynu. Obsługiwane zasoby obejmują:

| Typ zasobu | Dzienniki | Metryki |
| --- | --- | --- |
| Bramy aplikacji    | Yes | Yes |
| Konta usługi Automation     | Yes | |
| Konta usługi Batch          | Yes | Yes |
| Data Lake analytics     | Yes | | 
| Data Lake store         | Yes | |
| Puli elastycznej SQL        |     | Yes |
| Przestrzeń nazw centrum zdarzeń     |     | Yes |
| Centra IoT Hub                |     | Yes |
| Usługa Key Vault               | Yes | |
| Moduły równoważenia obciążenia          | Yes | |
| Logic Apps              | Yes | Yes |
| Grupy zabezpieczeń sieci | Yes | |
| Pamięć podręczna Redis             |     | Yes |
| Usługi wyszukiwania         | Yes | Yes |
| Przestrzeń nazw magistrali usług   |     | Yes |
| SQL (v12)               |     | Yes |
| Witryny sieci Web               |     | Yes |
| Farmach serwerów sieci Web        |     | Yes |

Aby uzyskać szczegółowe informacje dostępne metryki, zapoznaj się [obsługiwane metryki z monitorem Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Szczegóły dostępne dzienniki, można znaleźć w [obsługiwanych usług i schematu dla dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Poprzednie polecenie cmdlet umożliwia również zbieranie dzienników z zasobów, które znajdują się w różnych subskrypcji. Polecenie cmdlet jest w stanie pracy w subskrypcjach, ponieważ udostępniasz identyfikator zasobu tworzenia dzienników i obszaru roboczego, które dzienniki są wysyłane do.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Konfigurowanie analizy dzienników do indeksowania diagnostycznych platformy Azure z magazynu
Aby gromadzić dane dzienników z poziomu działającego wystąpienia usługi w chmurze klasycznego lub klastra sieci szkieletowej usług, należy najpierw zapisać danych do magazynu Azure. Analiza dzienników następnie jest skonfigurowana do zbierania dzienników z konta magazynu. Obsługiwane zasoby obejmują:

* Usługi w chmurze klasyczny (role sieci web i proces roboczy)
* Klastry usługi sieci szkieletowej

W poniższym przykładzie przedstawiono sposób:

1. Wyświetl listę istniejących kont magazynu i lokalizacje, które Log Analytics będzie indeksowanie danych z
2. Tworzenie konfiguracji można odczytać z konta magazynu
3. Zaktualizuj konfigurację nowo utworzony indeks danych z lokalizacji dodatkowej
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

Powyższy skrypt umożliwia również zbieranie dzienników z kont magazynu w ramach różnych subskrypcji. Skrypt jest w stanie działać w subskrypcjach, ponieważ udostępniasz identyfikator zasobu konta magazynu i odpowiedniego klucza dostępu. Jeśli zmienisz klawisz dostępu, należy zaktualizować wiedzę magazynu do nowego klucza.


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd poleceń cmdlet programu PowerShell analizy dziennika](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) dodatkowe informacje na temat konfiguracji analizy dzienników przy użyciu programu PowerShell.

