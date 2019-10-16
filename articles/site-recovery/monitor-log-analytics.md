---
title: Monitoruj Azure Site Recovery z dziennikami Azure Monitor (Log Analytics) | Microsoft Docs
description: Informacje na temat monitorowania Azure Site Recovery z dziennikami Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: raynew
ms.openlocfilehash: 889fa3bee17aa3b0300431b058332c5ec10d9faf
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331922"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorowanie usługi Site Recovery przy użyciu dzienników usługi Azure Monitor

W tym artykule opisano sposób monitorowania maszyn replikowanych przez usługę Azure [Site Recovery](site-recovery-overview.md)przy użyciu [dzienników Azure monitor](../azure-monitor/platform/data-platform-logs.md)i [log Analytics](../azure-monitor/log-query/log-query-overview.md).

Dzienniki Azure Monitor udostępniają platformę danych dziennika, która zbiera dane dotyczące aktywności i dzienników diagnostycznych oraz inne dane monitorowania. W ramach Azure Monitor dzienników używasz Log Analytics do zapisywania i testowania zapytań dzienników oraz do interaktywnego analizowania danych dziennika. Możesz wizualizować i badać wyniki dzienników oraz konfigurować alerty w celu podejmowania działań w oparciu o monitorowane dane.

Site Recovery można Azure Monitor dzienników, aby ułatwić wykonywanie następujących czynności:

- **Monitoruj Site Recovery kondycję i stan**. Na przykład można monitorować kondycję replikacji, stan testu pracy w trybie failover, zdarzenia Site Recovery, cele punktu odzyskiwania (RPO) dla chronionych maszyn i szybkość zmian dysku/danych.
- **Skonfiguruj alerty dla Site Recovery**. Można na przykład skonfigurować alerty dotyczące kondycji komputera, stanu testu pracy w trybie failover lub Site Recovery stanu zadania.

Korzystanie z dzienników Azure Monitor z Site Recovery jest obsługiwane w przypadku replikacji **platformy Azure do platformy** Azure oraz do replikacji platformy Azure **/serwera fizycznego** .

> [!NOTE]
> Dzienniki danych o zmienionych i szybkości przekazywania są dostępne tylko dla maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto, co jest potrzebne:

- Co najmniej jedna maszyna chroniona w magazynie Recovery Services.
- Obszar roboczy Log Analytics do przechowywania dzienników Site Recovery. [Dowiedz się więcej o](../azure-monitor/learn/quick-create-workspace.md) konfigurowaniu obszaru roboczego.
- Podstawowe informacje na temat pisania, uruchamiania i analizowania zapytań dzienników w Log Analytics. [Dowiedz się więcej](../azure-monitor/log-query/get-started-portal.md).

Zalecamy zapoznanie się z [typowymi pytaniami monitorowania](monitoring-common-questions.md) przed rozpoczęciem.

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurowanie Site Recovery do wysyłania dzienników

1. W magazynie kliknij pozycję **Ustawienia diagnostyczne** > **Dodaj ustawienie diagnostyczne**.

    ![Wybierz rejestrowanie diagnostyczne](./media/monitoring-log-analytics/add-diagnostic.png)

2. W obszarze **Ustawienia diagnostyczne**Określ nazwę, a następnie zaznacz pole wyboru **Wyślij do log Analytics**.
3. Wybierz opcję subskrypcja dzienników Azure Monitor i obszar roboczy Log Analytics.
4. Wybierz **Diagnostyka Azure** w przełączniku.
5. Z listy dziennik wybierz wszystkie dzienniki z prefiksem **AzureSiteRecovery**. Następnie kliknij przycisk **OK**.

    ![Wybór obszaru roboczego](./media/monitoring-log-analytics/select-workspace.png)

Dzienniki Site Recovery rozpoczynają się do tabeli (**AzureDiagnostics**) w wybranym obszarze roboczym.


## <a name="query-the-logs---examples"></a>Wysyłanie zapytań do dzienników — przykłady

Pobieranie danych z dzienników przy użyciu zapytań dzienników utworzonych przy użyciu [języka zapytań Kusto](../azure-monitor/log-query/get-started-queries.md). Ta sekcja zawiera kilka przykładów typowych zapytań, których można użyć do monitorowania Site Recovery.

> [!NOTE]
> W niektórych przykładach użyto **replicationProviderName_s** ustawionych do **— A2A**. Spowoduje to pobranie maszyn wirtualnych platformy Azure replikowanych do regionu pomocniczego platformy Azure przy użyciu Site Recovery. W tych przykładach można zastąpić **— A2A** z **InMageAzureV2**, jeśli chcesz pobrać lokalne maszyny wirtualne VMware lub serwery fizyczne replikowane do platformy Azure przy użyciu Site Recovery.


### <a name="query-replication-health"></a>Kondycja replikacji zapytań

To zapytanie przedstawia wykres kołowy dla bieżącej kondycji replikacji wszystkich chronionych maszyn wirtualnych platformy Azure, podzielone na trzy stany: normalne, ostrzegawcze lub krytyczne.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Wersja usługi mobilności zapytań

To zapytanie przedstawia wykres kołowy dla maszyn wirtualnych platformy Azure replikowanych za pomocą Site Recovery, podzielony przez wersję agenta mobilności, na którym są uruchomione.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Czas RPO zapytania

To zapytanie przedstawia wykres słupkowy maszyn wirtualnych platformy Azure replikowanych za pomocą Site Recovery, podzielony przez cel punktu odzyskiwania (RPO): mniej niż 15 minut, od 15-30 minut, ponad 30 minut.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Cel punktu odzyskiwania](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Zadania Site Recovery zapytań

To zapytanie pobiera wszystkie zadania Site Recovery (dla wszystkich scenariuszy odzyskiwania po awarii), wyzwolone w ciągu ostatnich 72 godzin i ich stan ukończenia.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Zdarzenia Site Recovery zapytań

To zapytanie pobiera wszystkie zdarzenia Site Recovery (dla wszystkich scenariuszy odzyskiwania po awarii) zgłoszone w ciągu ostatnich 72 godzin wraz z ich ważnością. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Test stanu przełączenia w tryb failover (wykres kołowy)

To zapytanie przedstawia wykres kołowy dla stanu testowej pracy w trybie failover maszyn wirtualnych platformy Azure replikowanych za pomocą Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Test stanu pracy w trybie failover (tabela)

To zapytanie przedstawia tabelę stanu testu pracy w trybie failover maszyn wirtualnych platformy Azure replikowanych za pomocą Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Zbadaj cel punktu odzyskiwania maszyny

To zapytanie przedstawia wykres trendu, który śledzi cel punktu odzyskiwania określonej maszyny wirtualnej platformy Azure (ContosoVM123) w ciągu ostatnich 72 godzin.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Zbadaj cel punktu odzyskiwania maszyny](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>Współczynnik zmian danych zapytania dla maszyny wirtualnej

> [!NOTE] 
> Informacje o postępie są dostępne tylko dla maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure.

To zapytanie przedstawia wykres trendu dla określonej maszyny wirtualnej platformy Azure (ContosoVM123), który śledzi współczynnik zmian danych (bajty zapisu na sekundę) i szybkość przekazywania danych. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Zmień dane zapytania](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Zapytanie dotyczące odzyskiwania po awarii (Azure na platformę Azure)

To zapytanie przedstawia tabelę podsumowującą dla maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure.  Są w nim wyświetlane nazwy maszyn wirtualnych, replikacja i stan ochrony, cel punktu odzyskiwania, stan testu pracy w trybie failover, wersja agenta mobilności, wszystkie aktywne błędy replikacji i lokalizacja źródłowa.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Podsumowanie dotyczące odzyskiwania po awarii (serwery VMware/fizyczne)

To zapytanie przedstawia tabelę podsumowującą dla maszyn wirtualnych VMware i serwerów fizycznych replikowanych do platformy Azure.  Przedstawiono w nim nazwę komputera, stan replikacji i ochrony, cel punktu odzyskiwania, stan testu pracy w trybie failover, wersja agenta mobilności, wszystkie aktywne błędy replikacji i odpowiedni serwer przetwarzania.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Konfigurowanie alertów — przykłady

Można skonfigurować alerty Site Recovery w oparciu o dane Azure Monitor. [Dowiedz się więcej](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) o konfigurowaniu alertów dziennika. 

> [!NOTE]
> W niektórych przykładach użyto **replicationProviderName_s** ustawionych do **— A2A**. Powoduje to ustawienie alertów dla maszyn wirtualnych platformy Azure, które są replikowane do pomocniczego regionu platformy Azure. W tych przykładach można zastąpić **— A2A** z **InMageAzureV2** , jeśli chcesz ustawić alerty dla lokalnych maszyn wirtualnych VMware lub serwerów fizycznych replikowanych do platformy Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Wiele maszyn w stanie krytycznym

Skonfiguruj alert, jeśli więcej niż 20 zreplikowanych maszyn wirtualnych platformy Azure przejdzie w stan krytyczny.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Dla alertu Ustaw **wartość progu** na 20.

### <a name="single-machine-in-a-critical-state"></a>Pojedynczy komputer w stanie krytycznym

Skonfiguruj alert, jeśli określona replikowana maszyna wirtualna platformy Azure przejdzie w stan krytyczny.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Dla alertu Ustaw **wartość progu** na 1.

### <a name="multiple-machines-exceed-rpo"></a>Wiele maszyn przekracza cel punktu odzyskiwania

Skonfiguruj alert, jeśli cel punktu odzyskiwania dla ponad 20 maszyn wirtualnych platformy Azure przekroczy 30 minut.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Dla alertu Ustaw **wartość progu** na 20.

### <a name="single-machine-exceeds-rpo"></a>Pojedynczy komputer przekracza cel punktu odzyskiwania

Skonfiguruj alert, jeśli cel punktu odzyskiwania dla pojedynczej maszyny wirtualnej platformy Azure przekroczy 30 minut.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Dla alertu Ustaw **wartość progu** na 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Test pracy w trybie failover dla wielu maszyn przekracza 90 dni

Skonfiguruj alert, jeśli Ostatnia pomyślna testowa praca w trybie failover była większa niż 90 dni, przez ponad 20 maszyn wirtualnych. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Dla alertu Ustaw **wartość progu** na 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Test pracy w trybie failover dla pojedynczej maszyny przekracza 90 dni

Skonfiguruj alert, jeśli ostatni pomyślny test pracy w trybie failover dla określonej maszyny wirtualnej był większy niż 90 dni temu.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Dla alertu Ustaw **wartość progu** na 1.

### <a name="site-recovery-job-fails"></a>Niepowodzenie zadania Site Recovery

Skonfiguruj alert, jeśli zadanie Site Recovery (w tym przypadku zadanie ponownego włączania ochrony) zakończy się niepowodzeniem dla żadnego scenariusza Site Recovery w ciągu ostatniego dnia. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Dla alertu Ustaw **wartość progu** na 1 i **okres** na 1440 minut, aby sprawdzić błędy w ciągu ostatniego dnia.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat](site-recovery-monitor-and-troubleshoot.md) wbudowanego monitorowania Site Recovery.
