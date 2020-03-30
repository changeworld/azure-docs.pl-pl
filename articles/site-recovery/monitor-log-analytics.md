---
title: Monitoruj odzyskiwanie witryny platformy Azure za pomocą dzienników monitora platformy Azure
description: Dowiedz się, jak monitorować usługę Azure Site Recovery za pomocą dzienników monitora platformy Azure (log analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133414"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorowanie usługi Site Recovery przy użyciu dzienników usługi Azure Monitor

W tym artykule opisano sposób monitorowania maszyn replikowanych przez usługę Azure [Site Recovery](site-recovery-overview.md)przy użyciu [dzienników usługi Azure Monitor](../azure-monitor/platform/data-platform-logs.md)i usługi Log [Analytics](../azure-monitor/log-query/log-query-overview.md).

Dzienniki usługi Azure Monitor zapewniają platformę danych dziennika, która zbiera dzienniki aktywności i diagnostyki wraz z innymi danymi monitorowania. W dziennikach usługi Azure Monitor można używać usługi Log Analytics do pisania i testowania zapytań dziennika oraz do interaktywnej analizy danych dziennika. Można wizualizować i wysyłać zapytania wyniki dziennika oraz konfigurować alerty do wykonywania akcji na podstawie monitorowanych danych.

W przypadku odzyskiwania witryn można dzienniki usługi Azure Monitor, aby ułatwić następujące czynności:

- **Monitorowanie stanu i stanu odzyskiwania witryny**. Na przykład można monitorować kondycję replikacji, stan pracy awaryjnej testu, zdarzenia odzyskiwania lokacji, cele punktu odzyskiwania (RPO) dla chronionych komputerów i szybkość zmiany dysku/danych.
- **Konfigurowanie alertów dotyczących odzyskiwania witryny**. Na przykład można skonfigurować alerty dotyczące kondycji komputera, stanu trybu failover testu lub stanu zadania odzyskiwania witryny.

Korzystanie z dzienników usługi Azure Monitor z odzyskiwaniem witryny jest obsługiwane dla replikacji **platformy Azure na platformę Azure** i **vmware VMware VM/physical server do** replikacji platformy Azure.

> [!NOTE]
> Aby uzyskać dzienniki danych zmian i dzienniki szybkości przekazywania dla maszyn VMware i fizycznych, należy zainstalować agenta monitorowania firmy Microsoft na serwerze przetwarzania. Ten agent wysyła dzienniki maszyn replikujących do obszaru roboczego. Ta funkcja jest dostępna tylko dla wersji agenta mobilności 9.30.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto, co jest potrzebne:

- Co najmniej jedna maszyna chroniona w magazynie usług odzyskiwania.
- Obszar roboczy usługi Log Analytics do przechowywania dzienników odzyskiwania witryny. [Dowiedz się więcej o](../azure-monitor/learn/quick-create-workspace.md) konfigurowaniu obszaru roboczego.
- Podstawowa wiedza na temat sposobu pisania, uruchamiania i analizowania zapytań dziennika w usłudze Log Analytics. [Dowiedz się więcej](../azure-monitor/log-query/get-started-portal.md).

Przed rozpoczęciem zalecamy [zapoznanie się z często występującymi pytaniami dotyczącymi monitorowania.](monitoring-common-questions.md)

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurowanie odzyskiwania witryny do wysyłania dzienników

1. W przechowalni kliknij pozycję **Ustawienia** > diagnostyczne Dodaj ustawienie**diagnostyczne**.

    ![Wybieranie rejestrowania diagnostycznego](./media/monitoring-log-analytics/add-diagnostic.png)

2. W **obszarze Ustawienia diagnostyczne**określ nazwę i zaznacz pole **Wyślij do usługi Log Analytics**.
3. Wybierz subskrypcję dzienników usługi Azure Monitor i obszar roboczy usługi Log Analytics.
4. Wybierz **diagnostykę platformy Azure** w przełączniku.
5. Z listy dzienników wybierz wszystkie dzienniki z prefiksem **AzureSiteRecovery**. Następnie kliknij przycisk **OK**.

    ![Wybór obszaru roboczego](./media/monitoring-log-analytics/select-workspace.png)

Dzienniki odzyskiwania lokacji zaczynają być podawane do tabeli **(AzureDiagnostics)** w wybranym obszarze roboczym.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Konfigurowanie agenta monitorowania firmy Microsoft na serwerze przetwarzania do wysyłania dzienników szybkości odchyłania i przekazywania

Informacje o szybkości zmian danych i szybkości przekazywania danych źródłowych dla maszyn VMware/fizycznych można przechwycić w środowisku lokalnym. Aby to włączyć, agent monitorowania firmy Microsoft musi być zainstalowany na serwerze przetwarzania.

1. Przejdź do obszaru roboczego Usługi Log Analytics i kliknij ustawienia **zaawansowane**.
2. Kliknij stronę **Połączone źródła** i wybierz pozycję **Serwery systemu Windows**.
3. Pobierz agenta systemu Windows (64 bit) na serwerze przetwarzania. 
4. [Uzyskiwanie identyfikatora i klucza obszaru roboczego](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Konfigurowanie agenta do używania protokołu TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Zakończ instalację agenta,](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) podając uzyskany identyfikator obszaru roboczego i klucz.
7. Po zakończeniu instalacji przejdź do obszaru roboczego Usługi Log Analytics i kliknij ustawienia **zaawansowane**. Przejdź do strony **Dane** i kliknij dalej **liczniki wydajności systemu Windows**. 
8. Kliknij **na '+',** aby dodać następujące dwa liczniki z interwałem próbkowania wynoszącym 300 sekund:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Dane szybkości zmian i przekazywania zaczną być podawane do obszaru roboczego.


## <a name="query-the-logs---examples"></a>Kwerenda dzienniki - przykłady

Pobierasz dane z dzienników przy użyciu zapytań dziennika napisanych [w języku zapytania Kusto](../azure-monitor/log-query/get-started-queries.md). Ta sekcja zawiera kilka przykładów typowych zapytań, które można użyć do monitorowania odzyskiwania witryny.

> [!NOTE]
> W niektórych przykładach użyto **replicationProviderName_s** ustawionego na **A2A**. Spowoduje to pobranie maszyn wirtualnych platformy Azure, które są replikowane do pomocniczego regionu platformy Azure przy użyciu usługi Site Recovery. W tych przykładach można zastąpić **A2A** **inMageAzureV2**, jeśli chcesz pobrać lokalne maszyny wirtualne VMware lub serwery fizyczne, które są replikowane na platformie Azure przy użyciu usługi Site Recovery.


### <a name="query-replication-health"></a>Kondycja replikacji kwerendy

Ta kwerenda kreśli wykres kołowy dla bieżącego stanu replikacji wszystkich chronionych maszyn wirtualnych platformy Azure, w podziale na trzy stany: Normalny, Ostrzeżenie lub Krytyczny.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Wersja usługi mobilności kwerend

Ta kwerenda wykreśla wykres kołowy dla maszyn wirtualnych platformy Azure replikowanych z usługą Site Recovery w podziale na wersję agenta mobilności, który są uruchomione.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Czas RPO kwerendy

Ta kwerenda kreśli wykres słupkowy maszyn wirtualnych platformy Azure replikowanych z usługą Site Recovery, w podziale według celu punktu odzyskiwania (RPO): Mniej niż 15 minut, od 15 do 30 minut, więcej niż 30 minut.

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

![RPO kwerendy](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Zadania odzyskiwania witryny kwerendy

Ta kwerenda pobiera wszystkie zadania odzyskiwania witryny (dla wszystkich scenariuszy odzyskiwania po awarii), wyzwalane w ciągu ostatnich 72 godzin i ich stan ukończenia.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Zdarzenia odzyskiwania witryny kwerendy

Ta kwerenda pobiera wszystkie zdarzenia odzyskiwania witryny (dla wszystkich scenariuszy odzyskiwania po awarii) wywoływane w ciągu ostatnich 72 godzin, wraz z ich ważnością. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Stan trybu failover testu kwerendy (wykres kołowy)

Ta kwerenda kreśli wykres kołowy dla stanu trybu failover testu maszyn wirtualnych platformy Azure replikowanych za pomocą usługi Site Recovery.

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

### <a name="query-test-failover-state-table"></a>Stan trybu failover testu kwerendy (tabela)

Ta kwerenda kreśli tabelę dla stanu trybu failover testu maszyn wirtualnych platformy Azure replikowanych za pomocą usługi Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>RPO maszyny kwerendy

Ta kwerenda kreśli wykres trendu, który śledzi obiekt RPO określonej maszyny wirtualnej platformy Azure (ContosoVM123) w ciągu ostatnich 72 godzin.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![RPO maszyny kwerendy](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Szybkość zmiany danych kwerendy (współczynnik zmian) i szybkość przekazywania dla maszyny Wirtualnej platformy Azure

Ta kwerenda kreśli wykres trendu dla określonej maszyny Wirtualnej platformy Azure (ContosoVM123), która reprezentuje szybkość zmiany danych (Write Bytes per Second) i szybkość przekazywania danych. 

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
![Zmiana danych kwerendy](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Szybkość zmiany danych kwerendy (współczynnik zmian) i szybkość przesyłania dla urządzenia VMware lub komputera fizycznego

> [!Note]
> Upewnij się, że skonfigurować agenta monitorowania na serwerze przetwarzania, aby pobrać te dzienniki. Zapoznaj się [z instrukcjami, aby skonfigurować agenta monitorowania](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Ta kwerenda kreśli wykres trendu dla określonego **dysku0** replikowanego elementu **win-9r7sfh9qlru**, który reprezentuje szybkość zmiany danych (Write Bytes per Second) i szybkość przekazywania danych. Nazwę dysku można znaleźć w bloku **Dyski** z replikowanego elementu w magazynie usług odzyskiwania. Nazwa wystąpienia, która ma być używana w kwerendzie, to nazwa DNS komputera, po której następuje _ i nazwa dysku, jak w tym przykładzie.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Process Server wypycha te dane co 5 minut do obszaru roboczego usługi Log Analytics. Te punkty danych reprezentują średnią obliczoną przez 5 minut.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Podsumowanie odzyskiwania po awarii kwerendy (platforma Azure na platformę Azure)

Ta kwerenda wykreśla tabelę podsumowań dla maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure.  Pokazuje nazwę maszyny Wirtualnej, stan replikacji i ochrony, cel RPO, stan trybu failover testu, wersję agenta mobilności, wszelkie aktywne błędy replikacji i lokalizację źródłową.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Podsumowanie odzyskiwania po awarii kwerendy (serwery VMware/fizyczne)

Ta kwerenda wykreśla tabelę podsumowań dla maszyn wirtualnych VMware i serwerów fizycznych replikowanych na platformie Azure.  Pokazuje nazwę komputera, stan replikacji i ochrony, cel punktu administracyjnego, stan trybu failover testu, wersję agenta mobilności, wszelkie aktywne błędy replikacji i odpowiedni serwer procesu.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Konfigurowanie alertów - przykłady

Alerty usługi Site Recovery można skonfigurować na podstawie danych usługi Azure Monitor. [Dowiedz się więcej](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) o konfigurowaniu alertów dziennika. 

> [!NOTE]
> W niektórych przykładach użyto **replicationProviderName_s** ustawionego na **A2A**. To ustawia alerty dla maszyn wirtualnych platformy Azure, które są replikowane do pomocniczego regionu platformy Azure. W tych przykładach można zastąpić **A2A** **inMageAzureV2,** jeśli chcesz ustawić alerty dla lokalnych maszyn wirtualnych VMware lub serwerów fizycznych replikowanych na platformie Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Wiele maszyn w stanie krytycznym

Skonfiguruj alert, jeśli więcej niż 20 replikowanych maszyn wirtualnych platformy Azure przejdzie w stan krytyczny.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Dla alertu ustaw **wartość progu** na 20.

### <a name="single-machine-in-a-critical-state"></a>Pojedyncza maszyna w stanie krytycznym

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
Dla alertu ustaw **wartość progu** na 1.

### <a name="multiple-machines-exceed-rpo"></a>Wiele maszyn przekracza RPO

Skonfiguruj alert, jeśli obiekt RPO dla więcej niż 20 maszyn wirtualnych platformy Azure przekracza 30 minut.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Dla alertu ustaw **wartość progu** na 20.

### <a name="single-machine-exceeds-rpo"></a>Pojedyncza maszyna przekracza RPO

Skonfiguruj alert, jeśli obiekt RPO dla pojedynczej maszyny wirtualnej platformy Azure przekracza 30 minut.

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
Dla alertu ustaw **wartość progu** na 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Test pracy awaryjnej dla wielu maszyn przekracza 90 dni

Skonfiguruj alert, jeśli ostatnia pomyślna próba awaryjna była większa niż 90 dni dla więcej niż 20 maszyn wirtualnych. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Dla alertu ustaw **wartość progu** na 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Test pracy awaryjnej dla pojedynczej maszyny przekracza 90 dni

Skonfiguruj alert, jeśli ostatni pomyślny test pracy awaryjnej dla określonej maszyny Wirtualnej był więcej niż 90 dni temu.
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
Dla alertu ustaw **wartość progu** na 1.

### <a name="site-recovery-job-fails"></a>Zadanie odzyskiwania lokacji kończy się niepowodzeniem

Skonfiguruj alert, jeśli zadanie odzyskiwania witryny (w tym przypadku zadanie ponownetrócenie) nie powiedzie się w dowolnym scenariuszu odzyskiwania witryny w ciągu ostatniego dnia. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Dla alertu ustaw **wartość progu** na 1 i **Okres** na 1440 minut, aby sprawdzić błędy w ostatnim dniu.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](site-recovery-monitor-and-troubleshoot.md) wbudowanym monitorowaniu odzyskiwania witryny.
