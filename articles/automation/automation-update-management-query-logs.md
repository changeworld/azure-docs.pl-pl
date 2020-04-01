---
title: Kwerenda usługi Azure Update Management dzienniki
description: W tym artykule opisano sposób wykonywania zapytań o dzienniki dotyczące zarządzania aktualizacjami w obszarze roboczym usługi Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 03/31/2020
ms.topic: conceptual
ms.openlocfilehash: 863016bbeda9b4aec3bf2b4e12830bd30098150f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437843"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Rekordy aktualizacji kwerend dla zarządzania aktualizacjami w dziennikach monitora platformy Azure

Oprócz szczegółów, które są podane w rozwiązaniu zarządzanie aktualizacjami, można wyszukiwać dzienniki przechowywane w obszarze roboczym usługi Log Analytics. Na stronie rozwiązania w lewym okienku wybierz pozycję **Dzienniki**. Zostanie otwarta strona **Wyszukiwanie dzienników.**

Możesz również dowiedzieć się, jak dostosować zapytania lub używać ich od różnych klientów i nie tylko, odwiedzając: [Dokumentacja interfejsu API wyszukiwania usługi Log Analytics](https://dev.loganalytics.io/).

## <a name="update-records"></a>Rekordy Update (Aktualizacja)

Rekordy zbierane przez usługi Zarządzanie aktualizacjami dla maszyn wirtualnych z systemem Windows i Linux oraz typy danych wyświetlane w wynikach wyszukiwania dziennika. W poniższych sekcjach opisano te rekordy.

### <a name="required-updates"></a>Wymagane aktualizacje

Tworzony jest rekord `RequiredUpdate` z typem, który reprezentuje aktualizacje wymagane przez komputer. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny urządzenia do raportowania. |
| KBID | Identyfikator artykułu bazy wiedzy Dla aktualizacji systemu Windows. |
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager lub obszaru roboczego Usługi Log Analytics. | 
| Product (Produkt) | Produkty, dla których dotyczy aktualizacja. | 
| PublishDate | Data, w którym aktualizacja jest gotowa do pobrania i zainstalowania z witryny Windows Update. |
| Serwer | | 
| SourceHealthServiceId (ida usługi sourcehealthserviceid) | Unikatowy identyfikator reprezentujący identyfikator agenta systemu Windows usługi Log Analytics. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie usługi Azure Active Directory w organizacjach. | 
| TimeGenerated | Data i godzina utworzenia rekordu. | 
| Typ | *Aktualizacja* | 
| Klasyfikacja aktualizacji | Wskazuje typ aktualizacji, które można zastosować. W przypadku systemu Windows:<br> *Aktualizacje krytyczne*<br> *Aktualizacje zabezpieczeń*<br> *Pakiety zbiorcze aktualizacji*<br> *Pakiety funkcji*<br> *Dodatki Service Pack*<br> *Aktualizacje definicji*<br> *narzędzia*<br> *Aktualizacje*. Dla Linuksa:<br> *Aktualizacje krytyczne i zabezpieczeń*<br> *Inne* |
| AktualizacjaEverity | Ocena ważności usterki. Wartości to:<br> *Krytyczny*<br> *Ważne*<br> *Średni*<br> *Niskie* |
| Tytuł aktualizacji | Tytuł aktualizacji.|

### <a name="update"></a>Aktualizacja

Tworzony jest rekord `Update` z typem, który reprezentuje dostępne aktualizacje i ich stan instalacji komputera. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Źródło zatwierdzenia | Dotyczy tylko systemu operacyjnego Windows. Wartością jest *Microsoft Update*. |
| Approved (Zatwierdzono) | *Prawda* lub *fałsz* |
| Klasyfikacja | *Aktualizacje* |
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny urządzenia do raportowania. |
| ComputerEnvironment (Środowiska komputerowe) | *Platforma Azure* lub *platforma nienawiązywna.* |
| MSRCBulletinID | Numer identyfikatora biuletynu zabezpieczeń | 
| MSRCSeverity | Ocena ważności usterki. Wartości to:<br> *Krytyczny*<br> *Ważne*<br> *Średni*<br> *Niskie* |  
| KBID | Identyfikator artykułu bazy wiedzy Dla aktualizacji systemu Windows. |
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager lub obszaru roboczego Usługi Log Analytics. |
| UpdateID | Unikatowy identyfikator aktualizacji oprogramowania. |
| RevisionNumber | Numer poprawki określonej wersji aktualizacji. |
| Optional (Opcjonalność) | *Prawda* lub *fałsz* | 
| RebootBehavior | Zachowanie ponownego uruchomienia po zainstalowaniu/odinstalowaniu aktualizacji. |
| _ResourceId | Unikatowy identyfikator zasobu, z który jest skojarzony rekord. |
| Typ | *Aktualizacja* |
| VMUUID | Unikatowy identyfikator maszyny wirtualnej. |
| MG | Unikatowy identyfikator grupy zarządzania lub obszaru roboczego usługi Log Analytics. | 
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie usługi Azure Active Directory w organizacjach. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Data i godzina utworzenia rekordu. | 
| SourceComputerId | Unikatowy identyfikator reprezentujący komputer źródłowy. | 
| Tytuł | Tytuł aktualizacji. |
| PublishedDate (UTC) | Data, w którym aktualizacja jest gotowa do pobrania i zainstalowania z witryny Windows Update.  |
| UpdateState | Bieżący stan aktualizacji. | 
| Product (Produkt) | Produkty, dla których dotyczy aktualizacja. |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. | 
| ResourceGroup | Nazwa grupy zasobów, do na które należy zasób. | 
| ResourceProvider | Określa dostawcę zasobów. | 
| Zasób | Nazwa zasobu. | 
| ResourceType | Nazwa typu zasobu. | 

### <a name="update-agent"></a>Agent aktualizacji

Tworzony `UpdateAgent` jest rekord z typem, który zawiera szczegółowe informacje o agencie aktualizacji na komputerze. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| AgeofOldMissingRequiredUpdate | | 
| Funkcja AutomatycznaUpdateEnabled | | 
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny urządzenia do raportowania. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager lub obszaru roboczego Usługi Log Analytics. |
| OSVersion | Wersja systemu operacyjnego. |
| Serwer | |
| SourceHealthServiceId (ida usługi sourcehealthserviceid) | Unikatowy identyfikator reprezentujący identyfikator agenta systemu Windows usługi Log Analytics. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie usługi Azure Active Directory w organizacjach. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Typ | *Aktualizacja* | 
| WindowsUpdateAgentVersion | Wersja agenta Usługi Windows Update. |
| WSUSServer | Pokazuje błędy, jeśli agent usługi Windows Update ma problem z pomocą w rozwiązywaniu problemów. |

### <a name="update-deployment-status"></a>Stan wdrożenia aktualizacji 

Tworzony `UpdateRunProgress` jest rekord z typem, który zapewnia stan wdrożenia aktualizacji zaplanowanego wdrożenia na komputerze. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny urządzenia do raportowania. |
| ComputerEnvironment (Środowiska komputerowe) | *Platforma Azure* lub *platforma nienawiązywna.* | 
| CorrelationId | Unikatowy identyfikator zadania runbooka uruchamianego dla aktualizacji. |
| EndTime | Czas zakończenia procesu synchronizacji. | 
| BłądWynik | Kod błędu usługi Windows Update wygenerowany, jeśli aktualizacja nie powiedzie się. | 
| Stan instalacji | Możliwe stany instalacji aktualizacji na komputerze klienckim, *W toku*, *Powiodło się*, Częściowo nie *powiodło się*. |
| KBID | Identyfikator artykułu bazy wiedzy Dla aktualizacji systemu Windows. | 
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager lub obszaru roboczego Usługi Log Analytics. |
| OSType | Określa typ systemu *operacyjnego, Windows* lub *Linux*. | 
| Product (Produkt) | Produkty, dla których dotyczy aktualizacja. |
| Zasób | Nazwa zasobu. | 
| ResourceId | Unikatowy identyfikator zasobu, z który jest skojarzony rekord. |
| ResourceProvider | Określa dostawcę zasobów. | 
| ResourceType | Nazwa typu zasobu. | 
| SourceComputerId | Unikatowy identyfikator reprezentujący komputer źródłowy. | 
| SourceSystem | *OperationsManager* |
| StartTime | Czas, kiedy aktualizacja jest zaplanowane do zainstalowania. |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. | 
| SucceededOnRetry (UdaneOnRetry) | Pokazuje, kiedy wykonanie aktualizacji nie powiodło się przy pierwszej próbie, a bieżąca operacja jest próbą ponowienia próby. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Tytuł | Tytuł aktualizacji. |
| Typ | *UpdateRunProgress* |
| UpdateId (Ida aktualizacji) | Unikatowy identyfikator aktualizacji oprogramowania. |
| VMUUID | Unikatowy identyfikator maszyny wirtualnej. |
| _ResourceId | Unikatowy identyfikator zasobu, z który jest skojarzony rekord. |

### <a name="update-summary"></a>Podsumowanie aktualizacji 

Tworzony jest rekord `UpdateSummary` z typem, który zawiera podsumowanie aktualizacji według komputera. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis | 
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny urządzenia do raportowania. |
| ComputerEnvironment (Środowiska komputerowe) | *Platforma Azure* lub *platforma nienawiązywna.* | 
| CriticalUpdatesMissing | Brak odpowiedniej liczby aktualizacji krytycznych. | 
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager lub obszaru roboczego Usługi Log Analytics. |
| NETRuntimeVersion | Wersja programu .NET Framework zainstalowana na komputerze z systemem Windows. |
| OldestMissingSecurityUpdateBucket | Wartości to:<br> *Ostatnie,* jeśli wartość jest mniejsza niż 30 dni<br> *30 dni temu*<br> *60 dni temu*<br> *90 dni temu*<br> *120 dni temu*<br> *150 dni temu*<br> *180 dni temu*<br> *Starsze,* gdy wartość jest większa niż 180 dni | 
| OldestMissingSecurityUpdateInDays | Całkowita liczba dni dla najstarszej aktualizacji wykrytej w stosownych przypadkach, która nie została zainstalowana. |
| OsVersion | Wersja systemu operacyjnego. |
| OtherUpdatesMissing | Liczba wykrytych aktualizacji brakuje. |
| Zasób |  Nazwa zasobu. | 
| ResourceGroup | Nazwa grupy zasobów, do na które należy zasób. |
| ResourceId | Unikatowy identyfikator zasobu, z który jest skojarzony rekord. |
| ResourceProvider | Określa dostawcę zasobów. |
| ResourceType | Nazwa typu zasobu. |
| Ponowne odwdzięczenie | *True* lub *False*. |
| SecurityUpdatesMissing | Liczba brakujących aktualizacji zabezpieczeń ma zastosowanie.| 
| SourceComputerId | Unikatowy identyfikator maszyny wirtualnej. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| TotalUpdatesMissing | Całkowita liczba brakujących aktualizacji. | 
| Typ | *UpdateSummary* |
| VMUUID | Unikatowy identyfikator maszyny wirtualnej. |
| WindowsUpdateAgentVersion | Wersja agenta Usługi Windows Update. |
| WindowsUpdateSetting | Pokazuje stan agenta usługi Windows Update. Możliwe wartości:<br> *Planowana instalacja*<br> *Powiadamianie przed instalacją*<br> Błąd zwrócony ze złej kondycji agenta WUA. | 
| WSUSServer | Pokazuje błędy, jeśli agent usługi Windows Update ma problem z pomocą w rozwiązywaniu problemów. |
| _ResourceId | Unikatowy identyfikator zasobu, z który jest skojarzony rekord. |

## <a name="sample-queries"></a>Przykładowe zapytania

W poniższych sekcjach przedstawiono przykładowe kwerendy dziennika dla rekordów aktualizacji, które są zbierane dla zarządzania aktualizacjami.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Upewnij się, że maszyny nienawiązane z platformą Azure są wbudowane

Aby potwierdzić, że bezpośrednio połączone maszyny komunikują się z dziennikami usługi Azure Monitor, uruchom jedno z następujących wyszukiwań dziennika.

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

Na komputerze z systemem Windows można przejrzeć następujące informacje, aby zweryfikować łączność agenta za pomocą dzienników usługi Azure Monitor:

1. W Panelu sterowania otwórz program **Microsoft Monitoring Agent**. Na karcie **Usługa Azure Log Analytics** agent wyświetla następujący komunikat: Agent monitorowania firmy Microsoft pomyślnie połączył się z **usługą Log Analytics.**
2. Otwórz dziennik zdarzeń systemu Windows. Przejdź do **dzienników aplikacji i usług\Programu Operations Manager** i wyszukaj identyfikator zdarzenia 3000 i identyfikator zdarzenia 5002 ze **źródłowego łącznika usługi**. Te zdarzenia informują o tym, że komputer został zarejestrowany w obszarze roboczym usługi Log Analytics i odbiera konfigurację.

Jeśli agent nie może komunikować się z dziennikami usługi Azure Monitor, a agent jest skonfigurowany do komunikowania się z Internetem za pośrednictwem zapory lub serwera proxy, upewnij się, że zapora lub serwer proxy jest poprawnie skonfigurowany. Aby dowiedzieć się, jak sprawdzić, czy zapora lub serwer proxy jest poprawnie skonfigurowany, zobacz [Konfiguracja sieci dla agenta systemu Windows](../azure-monitor/platform/agent-windows.md) lub [konfiguracja sieci dla agenta systemu Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Jeśli systemy Linux są skonfigurowane do komunikowania się z serwerem proxy lub bramą usługi Log Analytics i dołączasz to rozwiązanie, zaktualizuj uprawnienia *proxy.conf,* aby udzielić grupie omiuser uprawnienia do odczytu pliku za pomocą następujących poleceń:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nowo dodani agenci systemu Linux pokazują stan **Updated** po przeprowadzeniu oceny. Ten proces może potrwać do 6 godzin.

Aby potwierdzić, że grupa zarządzania programu Operations Manager komunikuje się z dziennikami usługi Azure Monitor, zobacz [Sprawdzanie poprawności integracji programu Operations Manager z dziennikami usługi Azure Monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Pojedyncze kwerendy oceny maszyn wirtualnych platformy Azure (Windows)

Zastąp wartość VMUUID identyfikatorem graficznym maszyny wirtualnej, do której wysyłasz kwerendy. Można znaleźć VMUUID, który powinien być używany przez uruchomienie następującej kwerendy w dziennikach usługi Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista brakujących aktualizacji

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

### <a name="single-azure-vm-assessment-queries-linux"></a>Pojedyncze zapytania oceny maszyn wirtualnych platformy Azure (Linux)

W przypadku niektórych dystrybucji systemu Linux występuje [niezgodność endianness](https://en.wikipedia.org/wiki/Endianness) z wartością VMUUID, która pochodzi z usługi Azure Resource Manager i co jest przechowywane w dziennikach usługi Azure Monitor. Poniższa kwerenda sprawdza dopasowanie na obu endianness. Zastąp wartości VMUUID z big-endian i mało endian format guiD prawidłowo zwrócić wyniki. Można znaleźć VMUUID, który powinien być używany przez uruchomienie następującej kwerendy w dziennikach usługi Azure Monitor:`Update | where Computer == "<machine name>"
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

#### <a name="missing-updates-list"></a>Lista brakujących aktualizacji

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

### <a name="multi-vm-assessment-queries"></a>Zapytania o ocenę wielu maszyn wirtualnych

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

#### <a name="missing-updates-list"></a>Lista brakujących aktualizacji

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

* Użyj wyszukiwania dzienników w [dziennikach usługi Azure Monitor,](../log-analytics/log-analytics-log-searches.md) aby wyświetlić szczegółowe dane aktualizacji.
* [Tworzenie alertów](automation-tutorial-update-management.md#configure-alerts) dla stanu wdrożenia aktualizacji.
