---
title: Tworzenie zapytań dotyczących dzienników usługi Azure Update Management
description: W tym artykule opisano sposób wykonywania zapytań w dziennikach pod kątem Update Management w obszarze roboczym Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 01/10/2020
ms.topic: conceptual
ms.openlocfilehash: 5a1979b0e714f35694999c04e1f890b710d54ac9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867064"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Kwerendy aktualizacji rekordów dla Update Management w dziennikach Azure Monitor

Oprócz szczegółów dostępnych w rozwiązaniu Update Management można wyszukiwać dzienniki przechowywane w Log Analytics obszarze roboczym. Na stronie rozwiązanie w okienku po lewej stronie wybierz pozycję **dzienniki**. Zostanie otwarta strona **Wyszukiwanie w dzienniku** .

Możesz również dowiedzieć się, jak dostosować zapytania lub użyć ich z różnych klientów i więcej, odwiedzając: [log Analytics dokumentację interfejsu API wyszukiwania](https://dev.loganalytics.io/).

## <a name="update-records"></a>Rekordy Update (Aktualizacja)

Rekordy zbierane przez Update Management dla maszyn wirtualnych z systemami Windows i Linux oraz typy danych, które są wyświetlane w wynikach przeszukiwania dzienników. W poniższych sekcjach opisano te rekordy.

### <a name="required-updates"></a>Wymagane aktualizacje

Tworzony jest rekord typu `RequiredUpdate`, który reprezentuje aktualizacje wymagane przez komputer. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| KBID | Identyfikator artykułu bazy wiedzy dla usługi Windows Update. |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. | 
| Produkt | Produkty, dla których ma zastosowanie aktualizacja. | 
| PublishDate | Data gotowości do pobrania i zainstalowania aktualizacji z Windows Update. |
| Serwer | | 
| SourceHealthServiceId | Unikatowy identyfikator reprezentujący identyfikator Log Analytics agenta systemu Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie organizacji Azure Active Directory. | 
| TimeGenerated | Data i godzina utworzenia rekordu. | 
| Typ | *Aktualizacja* | 
| UpdateClassification | Wskazuje typ aktualizacji, które mogą być stosowane. W przypadku systemu Windows:<br> *Aktualizacje krytyczne*<br> *Aktualizacje zabezpieczeń*<br> *Pakiety zbiorcze aktualizacji*<br> *Pakiety Feature Pack*<br> *Dodatki Service Pack*<br> *Aktualizacje definicji*<br> *Narzędzia*<br> *Aktualizacje*. W przypadku systemu Linux:<br> *Aktualizacje krytyczne i zabezpieczeń*<br> *Inne* |
| UpdateSeverity | Klasyfikacja ważności luki w zabezpieczeniach. Wartości to:<br> *Najistotniejsz*<br> *Ważne*<br> *Umiarkowany*<br> *Niski* |
| UpdateTitle | Tytuł aktualizacji.|

### <a name="update"></a>Aktualizacja

Tworzony jest rekord typu `Update`, który reprezentuje dostępne aktualizacje i ich stan instalacji. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| ApprovalSource | Dotyczy tylko systemu operacyjnego Windows. Wartość jest *Microsoft Update*. |
| Approved (Zatwierdzono) | *Prawda* lub *Fałsz* |
| Klasyfikacja | *Aktualizacje* |
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| ComputerEnvironment | *Azure* lub *innym niż Azure*. |
| MSRCBulletinID | Numer IDENTYFIKACYJNy biuletynu zabezpieczeń | 
| MSRCSeverity | Klasyfikacja ważności luki w zabezpieczeniach. Wartości to:<br> *Najistotniejsz*<br> *Ważne*<br> *Umiarkowany*<br> *Niski* |  
| KBID | Identyfikator artykułu bazy wiedzy dla usługi Windows Update. |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| UpdateID | Unikatowy identyfikator aktualizacji oprogramowania. |
| RevisionNumber | Numer poprawki określonej poprawki aktualizacji. |
| Opcjonalne | *Prawda* lub *Fałsz* | 
| RebootBehavior | Zachowanie ponownego uruchomienia po zainstalowaniu/odinstalowaniu aktualizacji. |
| _ResourceId | Unikatowy identyfikator zasobu, z którym jest skojarzony rekord. |
| Typ | *Aktualizacja* |
| VMUUID | Unikatowy identyfikator dla maszyny wirtualnej. |
| MG | Unikatowy identyfikator grupy zarządzania lub Log Analytics obszaru roboczego. | 
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie organizacji Azure Active Directory. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Data i godzina utworzenia rekordu. | 
| SourceComputerId | Unikatowy identyfikator reprezentujący komputer źródłowy. | 
| Tytuł | Tytuł aktualizacji. |
| PublishedDate (UTC) | Data gotowości do pobrania i zainstalowania aktualizacji z Windows Update.  |
| UpdateState | Bieżący stan aktualizacji. | 
| Produkt | Produkty, dla których ma zastosowanie aktualizacja. |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. | 
| ResourceGroup | Nazwa grupy zasobów, do której należy zasób. | 
| ResourceProvider | Określa dostawcę zasobów. | 
| Zasób | Nazwa zasobu. | 
| ResourceType | Nazwa typu zasobu. | 

### <a name="update-agent"></a>Aktualizuj agenta

Tworzony jest rekord typu `UpdateAgent`, który zawiera szczegółowe informacje o agencie aktualizacji na komputerze. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| OSVersion | Wersja systemu operacyjnego. |
| Serwer | |
| SourceHealthServiceId | Unikatowy identyfikator reprezentujący identyfikator Log Analytics agenta systemu Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie organizacji Azure Active Directory. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Typ | *Aktualizacja* | 
| WindowsUpdateAgentVersion | Wersja agenta Windows Update. |
| WSUSServer | Przedstawia błędy, jeśli Agent Windows Update może pomóc w rozwiązaniu problemu. |

### <a name="update-deployment-status"></a>Aktualizuj stan wdrożenia 

Tworzony jest rekord typu `UpdateRunProgress`, który zapewnia stan wdrożenia aktualizacji zaplanowane wdrożenie według maszyny. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| ComputerEnvironment | *Azure* lub *innym niż Azure*. | 
| CorrelationId | Unikatowy identyfikator zadania elementu Runbook uruchomionego dla aktualizacji. |
| EndTime | Godzina, o której zakończył się proces synchronizacji. | 
| ErrorResult | Windows Update wygenerowany kod błędu, jeśli instalacja aktualizacji nie powiedzie się. | 
| InstallationStatus | Możliwe stany instalacji aktualizacji na komputerze klienckim, *w toku*, *zakończyły się powodzeniem*, *częściowo zakończyły się niepowodzeniem*. |
| KBID | Identyfikator artykułu bazy wiedzy dla usługi Windows Update. | 
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| OSType | Określa typ systemu operacyjnego, *Windows* lub *Linux*. | 
| Produkt | Produkty, dla których ma zastosowanie aktualizacja. |
| Zasób | Nazwa zasobu. | 
| ResourceId | Unikatowy identyfikator zasobu, z którym jest skojarzony rekord. |
| ResourceProvider | Określa dostawcę zasobów. | 
| ResourceType | Nazwa typu zasobu. | 
| SourceComputerId | Unikatowy identyfikator reprezentujący komputer źródłowy. | 
| SourceSystem | *OperationsManager* |
| StartTime | Godzina, o której zaplanowano zainstalowanie aktualizacji. |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. | 
| SucceededOnRetry | Pokazuje, kiedy wykonanie aktualizacji nie powiodło się przy pierwszej próbie, a bieżąca operacja jest ponowieniem próby. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Tytuł | Tytuł aktualizacji. |
| Typ | *UpdateRunProgress* |
| UpdateId | Unikatowy identyfikator aktualizacji oprogramowania. |
| VMUUID | Unikatowy identyfikator dla maszyny wirtualnej. |
| _ResourceId | Unikatowy identyfikator zasobu, z którym jest skojarzony rekord. |

### <a name="update-summary"></a>Podsumowanie aktualizacji 

Tworzony jest rekord typu `UpdateSummary`, który udostępnia podsumowanie aktualizacji według maszyn. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| ComputerEnvironment | *Azure* lub *innym niż Azure*. | 
| CriticalUpdatesMissing | Liczba krytycznych aktualizacji, które mają zastosowanie. | 
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| NETRuntimeVersion | Wersja .NET Framework zainstalowana na komputerze z systemem Windows. |
| OldestMissingSecurityUpdateBucket | | 
| OldestMissingSecurityUpdateInDays | |
| OsVersion | Wersja systemu operacyjnego. |
| OtherUpdatesMissing | Brak liczby wykrytych aktualizacji. |
| Zasób |  Nazwa zasobu. | 
| ResourceGroup | Nazwa grupy zasobów, do której należy zasób. |
| ResourceId | Unikatowy identyfikator zasobu, z którym jest skojarzony rekord. |
| ResourceProvider | Określa dostawcę zasobów. |
| ResourceType | Nazwa typu zasobu. |
| RestartPending | *True* lub *False*. |
| SecurityUpdatesMissing | Liczba zastosowanych brakujących aktualizacji zabezpieczeń.| 
| SourceComputerId | Unikatowy identyfikator dla maszyny wirtualnej. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| TotalUpdatesMissing | Łączna liczba wymaganych aktualizacji. | 
| Typ | *UpdateSummary* |
| VMUUID | Unikatowy identyfikator dla maszyny wirtualnej. |
| WindowsUpdateAgentVersion | Wersja agenta Windows Update. |
| WindowsUpdateSetting | Pokazuje stan agenta Windows Update. Możliwe wartości:<br> *Zaplanowana instalacja*<br> *Powiadom przed instalacją*<br> Błąd zwrócony przez agenta usługi WUA w złej kondycji. | 
| WSUSServer | Przedstawia błędy, jeśli Agent Windows Update może pomóc w rozwiązaniu problemu. |
| _ResourceId | Unikatowy identyfikator zasobu, z którym jest skojarzony rekord. |

## <a name="sample-queries"></a>Przykładowe zapytania

Poniższe sekcje zawierają przykładowe zapytania dzienników dla rekordów aktualizacji, które są zbierane dla Update Management.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Potwierdź, że maszyny spoza platformy Azure są dołączone

Aby upewnić się, że bezpośrednio połączone maszyny komunikują się z dziennikami Azure Monitor, Uruchom jedno z poniższych wyszukiwań w dziennikach.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na komputerze z systemem Windows można przejrzeć następujące informacje w celu zweryfikowania łączności agentów z dziennikami Azure Monitor:

1. W panelu sterowania Otwórz **Microsoft Monitoring Agent**. Na karcie **log Analytics Azure** Agent wyświetli następujący komunikat: **Microsoft Monitoring Agent pomyślnie nawiązał połączenie z log Analytics**.
2. Otwórz dziennik zdarzeń systemu Windows. Przejdź do pozycji **Application and Services Logs\Operations Manager** i Wyszukaj zdarzenia o identyfikatorze 3000 i identyfikatorze 5002 z **łącznika usługi**źródłowej. Te zdarzenia informują o tym, że komputer został zarejestrowany w obszarze roboczym usługi Log Analytics i odbiera konfigurację.

Jeśli Agent nie może komunikować się z dziennikami Azure Monitor i Agent jest skonfigurowany do komunikacji z Internetem za pomocą zapory lub serwera proxy, upewnij się, że zapora lub serwer proxy zostały prawidłowo skonfigurowane. Aby dowiedzieć się, jak upewnić się, że zapora lub serwer proxy są prawidłowo skonfigurowane, zobacz [Konfiguracja sieci dla agenta systemu Windows](../azure-monitor/platform/agent-windows.md) lub [Konfiguracja sieci dla agentów](../log-analytics/log-analytics-agent-linux.md)z systemem Linux.

> [!NOTE]
> Jeśli system Linux jest skonfigurowany do komunikowania się z serwerem proxy lub bramą Log Analytics i dołączasz to rozwiązanie, zaktualizuj *serwer proxy. conf* , aby przyznać grupie omiuser uprawnienia do odczytu w pliku przy użyciu następujących poleceń:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nowo dodani agenci systemu Linux pokazują stan **aktualizacji** po wykonaniu oceny. Ten proces może potrwać do 6 godzin.

Aby upewnić się, że Operations Manager grupy zarządzania komunikuje się z dziennikami Azure Monitor, zobacz temat [Weryfikuj integrację Operations Manager z dziennikami Azure monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Pojedyncze zapytania oceny maszyny wirtualnej platformy Azure (system Windows)

Zastąp wartość VMUUID wartością GUID maszyny wirtualnej, która jest używana do wykonywania zapytań. VMUUID, które powinny być używane, można znaleźć, uruchamiając następujące zapytanie w Azure Monitor dzienników: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Pojedyncze zapytania oceny maszyny wirtualnej platformy Azure (Linux)

W przypadku niektórych dystrybucje systemu Linux występuje niezgodność [endian](https://en.wikipedia.org/wiki/Endianness) z wartością VMUUID, która pochodzi z Azure Resource Manager i co jest przechowywane w dziennikach Azure monitor. Poniższe zapytanie sprawdza zgodność z dowolną przyciągiem. Zastąp wartości VMUUID wartościami formatu big-endian i little-endian identyfikatora GUID, aby prawidłowo zwrócić wyniki. VMUUID, które powinny być używane, można znaleźć, uruchamiając następujące zapytanie w Azure Monitor dzienników: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Zapytania oceny z obsługą wiele maszyn wirtualnych

#### <a name="computers-summary"></a>Podsumowanie komputerów

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista komputerów

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Następne kroki

* Użyj wyszukiwania w dzienniku [Azure monitor](../log-analytics/log-analytics-log-searches.md) , aby wyświetlić szczegółowe dane aktualizacji.
* [Utwórz alerty](automation-tutorial-update-management.md#configure-alerts) dla stanu wdrożenia aktualizacji.
