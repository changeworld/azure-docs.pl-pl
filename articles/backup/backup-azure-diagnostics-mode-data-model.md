---
title: Usługa Azure Monitor rejestruje model danych
description: W tym artykule dowiesz się więcej o szczegółach modelu usługi Azure Monitor Log Analytics dla danych usługi Azure Backup.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586380"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Model danych usługi Log Analytics dla danych usługi Azure Backup

Użyj modelu danych usługi Log Analytics, aby utworzyć niestandardowe alerty z usługi Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Ten model danych jest w odniesieniu do trybu diagnostyki platformy Azure wysyłania zdarzeń diagnostycznych do usługi Log Analytics (LA). Aby poznać model danych dla nowego trybu specyficznego dla zasobu, można zapoznać się z następującym artykułem: [Model danych dla zdarzeń diagnostycznych kopii zapasowej usługi Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Korzystanie z modelu danych usługi Azure Backup

Następujące pola są używane jako część modelu danych do tworzenia wizualizacji, zapytań niestandardowych i pulpitu nawigacyjnego zgodnie z wymaganiami.

### <a name="alert"></a>Alerty

Ta tabela zawiera szczegółowe informacje o polach związanych z alertami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| AlertUniqueId_s |Tekst |Unikatowy identyfikator wygenerowanego alertu |
| AlertType_s |Tekst |Typ alertu, na przykład Kopia zapasowa |
| AlertStatus_s |Tekst |Stan alertu, na przykład Aktywny |
| AlertOccurrenceDateTime_s |Data/godzina |Data i godzina utworzenia alertu |
| AlertSeverity_s |Tekst |Ważność alertu, na przykład krytyczne |
|AlertTimeToResolveInMinutes_s    | Liczba        |Czas pracy w celu rozwiązania alertu. Puste dla aktywnych alertów.         |
|AlertConsolidationStatus_s   |Tekst         |Określenie, czy alert jest alertem skonsolidowanym, czy nie         |
|CountOfAlertsConsolidated_s     |Liczba         |Liczba wpisów skonsolidowanych, jeśli jest to alert skonsolidowany          |
|AlertRaisedOn_s     |Tekst         |Typ jednostki, na której alert jest wywoływany         |
|AlertCode_s     |Tekst         |Kod umożliwiający jednoznaczną identyfikację typu alertu         |
|RecommendedAction_s   |Tekst         |Zalecane działanie w celu rozwiązania problemu         |
| EventName_s |Tekst |Nazwa wydarzenia. Zawsze usługa AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| SchemaVersion_s |Tekst |Bieżąca wersja schematu, na przykład **V2** |
| State_s |Tekst |Bieżący stan obiektu alertu, na przykład Active, Deleted |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowych, na przykład IaaSVM, FileFolder, do którego należy ten alert |
| OperationName |Tekst |Nazwa bieżącej operacji, na przykład Alert |
| Kategoria |Tekst |Kategoria danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor. Zawsze usługa AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| ProtectedContainerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera skojarzonego z alertem (został ProtectedServerUniqueId_s w wersji 1)|
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionego magazynu skojarzonego z alertem |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Unikatowy identyfikator zasobu, o którym zbierane są dane. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład przechowalnia |

### <a name="backupitem"></a>Kopia zapasowa

Ta tabela zawiera szczegółowe informacje o polach związanych z elementami kopii zapasowej.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |Nazwa wydarzenia. Zawsze usługa AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| BackupItemId_s |Tekst |Identyfikator elementu kopii zapasowej (to pole dotyczy tylko schematu w wersji 1) |
| BackupItemName_s |Tekst |Nazwa elementu kopii zapasowej |
| BackupItemFriendlyName_s |Tekst |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemType_s |Tekst |Typ elementu kopii zapasowej, na przykład maszyna wirtualna, filefolder |
| BackupItemProtectionState_s |Tekst |Stan ochrony elementu kopii zapasowej |
| BackupItemAppVersion_s |Tekst |Wersja aplikacji elementu kopii zapasowej |
| ProtectionState_s |Tekst |Bieżący stan ochrony elementu kopii zapasowej, na przykład Protected, ProtectionStopped |
| ProtectionGroupName_s |Tekst | Nazwa grupy ochrony, w przypadku gdy znajduje się element kopii zapasowej, jest chroniona w systemach SC DPM i MABS, jeśli dotyczy|
| SecondaryBackupProtectionState_s |Tekst |Czy ochrona pomocnicza jest włączona dla elementu kopii zapasowej|
| SchemaVersion_s |Tekst |Wersja schematu, na przykład **V2** |
| State_s |Tekst |Stan obiektu elementu kopii zapasowej, na przykład Aktywny, Usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowych, na przykład IaaSVM, FileFolder, do którego należy ten element kopii zapasowej |
| OperationName |Tekst |Nazwa operacji, na przykład BackupItem |
| Kategoria |Tekst |Kategoria danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor. Zawsze usługa AzureBackupReport |
| Zasób |Tekst |Zasób, dla którego zbierane są dane, na przykład nazwa magazynu usług recovery services |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla gromadzonych danych, na przykład identyfikator zasobu magazynu usług recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (dla np. magazyn usług odzyskiwania) dla gromadzonych danych |
| ResourceGroup |Tekst |Grupa zasobów zasobu (dla ex. magazyn usług odzyskiwania) dla gromadzonych danych |
| ResourceProvider |Tekst |Dostawca zasobów do zbierania danych, na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu do zbierania danych, na przykład Przechowalnia |

### <a name="backupitemassociation"></a>Tworzenie kopii zapasowychZmoczesy

Ta tabela zawiera szczegółowe informacje o skojarzeniach elementów kopii zapasowej z różnymi encjami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, zawsze jest AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu, jest to **V2** |
| State_s |Tekst |Bieżący stan obiektu skojarzenia elementu kopii zapasowej, na przykład Aktywny, Usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| BackupItemSourceSize_s |Tekst | Rozmiar front-endu elementu kopii zapasowej |
| BackupManagementServerUniqueId_s |Tekst | Pole umożliwiające jednoznaczną identyfikację serwera zarządzania zapasami kopii zapasowych, za pomocą którego chroniony jest element kopii zapasowej, jeśli ma to zastosowanie |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchniętych do usługi Log Analytics, jest to usługa AzureBackupReport |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — BackupItemAssociation |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| ProtectedContainerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera skojarzonego z elementem kopii zapasowej (został ProtectedServerUniqueId_s w wersji 1) |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu zawierającego element kopii zapasowej |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (dla np. magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (dla ex. magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów do zbierania danych, na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu danych jest zbierany, na przykład Przechowalnia |

### <a name="backupmanagementserver"></a>Serwer BackupManagementServer

Ta tabela zawiera szczegółowe informacje o skojarzeniach elementów kopii zapasowej z różnymi encjami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
|BackupManagementServerName_s     |Tekst         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Tekst         |Wersja agenta kopii zapasowej platformy Azure na serwerze zarządzania kopiami zapasowymi          |
|BackupManagementServerVersion_s     |Tekst         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagementServerOSVersion_s    |Tekst            |Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi|
|BackupManagementServerType_s     |Tekst         |Typ serwera zarządzania kopiami zapasowymi, jako MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Pole umożliwiające jednoznaczną identyfikację serwera zarządzania kopiami zapasowymi       |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (dla np. magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (dla ex. magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów do zbierania danych, na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu danych jest zbierany, na przykład Przechowalnia |

### <a name="job"></a>Zadanie

Ta tabela zawiera szczegółowe informacje o polach związanych z zadaniami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |Nazwa wydarzenia. Zawsze usługa AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |Wersja schematu, na przykład **V2** |
| State_s |Tekst |Bieżący stan obiektu zadania, na przykład Active, Deleted |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — Zadanie |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor, jest to usługa AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera skojarzony z zadaniem |
| ProtectedContainerUniqueId_s |Tekst | Unikatowy identyfikator umożliwiający identyfikację chronionego kontenera, na który jest uruchamiane zadanie |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionego magazynu |
| JobOperation_s |Tekst |Operacja, dla której zadanie jest uruchamiane, na przykład Kopia zapasowa, Przywracanie, Konfigurowanie kopii zapasowej |
| JobStatus_s |Tekst |Stan gotowego zadania, na przykład Zakończone, Nie powiodło się |
| JobFailureCode_s |Tekst |Ciąg kodu awarii z powodu niepowodzenia zadania |
| JobStartDateTime_s |Data/godzina |Data i godzina rozpoczęcia pracy zadania |
| BackupStorageDestination_s |Tekst |Miejsce docelowe przechowywania kopii zapasowych, na przykład Chmura, Dysk  |
| AdHocOrScheduledJob_s |Tekst | Pole do określenia, czy zadanie ma ad hoc czy zaplanowane |
| JobDurationInSecs_s | Liczba |Całkowity czas trwania zadania w sekundach |
| DataTransferredInMB_s | Liczba |Dane przesyłane w MB dla tego zadania|
| JobUniqueId_g |Tekst |Unikatowy identyfikator umożliwiający identyfikację zadania |
| RecoveryJobDestination_s |Tekst | Miejsce docelowe zadania odzyskiwania, w którym dane są odzyskiwane |
| RecoveryJobRPDateTime_s |DateTime | Data, godzina utworzenia punktu odzyskiwania, który jest odzyskiwany został utworzony |
| RecoveryJobRPLocation_s |Tekst | Miejsce przechowywania punktu odzyskiwania, który jest odzyskiwany|
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania|
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład przechowalnia |

### <a name="policy"></a>Zasady

Ta tabela zawiera szczegółowe informacje o polach związanych z zasadami.

| Pole | Typ danych | Wersje obowiązujące | Opis |
| --- | --- | --- | --- |
| EventName_s |Tekst ||To pole reprezentuje nazwę tego zdarzenia, zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||To pole oznacza bieżącą wersję schematu, jest to **V2** |
| State_s |Tekst ||Bieżący stan obiektu zasad, na przykład Aktywny, Usunięty |
| BackupManagementType_s |Tekst ||Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| OperationName |Tekst ||To pole reprezentuje nazwę bieżącej operacji — Zasady |
| Kategoria |Tekst ||To pole reprezentuje kategorię danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor, jest to usługa AzureBackupReport |
| Zasób |Tekst ||Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| PolicyUniqueId_g |Tekst ||Unikatowy identyfikator w celu zidentyfikowania zasad |
| PolicyName_s |Tekst ||Nazwa zdefiniowanej zasady |
| BackupFrequency_s |Tekst ||Częstotliwość, z jaką są uruchamiane kopie zapasowe, na przykład codziennie, co tydzień |
| BackupTimes_s |Tekst ||Data i godzina planowania kopii zapasowych |
| BackupDaysOfTheWeek_s |Tekst ||Dni tygodnia, w których zaplanowano tworzenie kopii zapasowych |
| RetentionDuration_s |Liczbowy całkowity ||Czas przechowywania skonfigurowanych kopii zapasowych |
| DailyRetentionDuration_s |Liczbowy całkowity ||Całkowity czas przechowywania w dniach skonfigurowanych kopii zapasowych |
| DailyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania dziennego przechowywania |
| WeeklyRetentionDuration_s |Liczba dziesiętna ||Całkowity tygodniowy czas przechowywania w tygodniach dla skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania tygodniowego przechowywania |
| WeeklyRetentionDaysOfTheWeek_s |Tekst ||Dni tygodnia wybrane do tygodniowego przechowywania |
| MonthlyRetentionDuration_s |Liczba dziesiętna ||Całkowity czas przechowywania w miesiącach dla skonfigurowanych kopii zapasowych |
| MonthlyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania miesięcznego przechowywania |
| MonthlyRetentionFormat_s |Tekst ||Typ konfiguracji dla miesięcznego przechowywania, na przykład codziennie w przypadku dnia, co tydzień na podstawie tygodnia |
| MonthlyRetentionDaysOfTheWeek_s |Tekst ||Dni tygodnia wybrane do miesięcznego przechowywania |
| MonthlyRetentionWeeksOfTheMonth_s |Tekst ||Tygodnie miesiąca, w którym skonfigurowano miesięczne przechowywanie, na przykład Pierwszy, Ostatni itp. |
| YearlyRetentionDuration_s |Liczba dziesiętna ||Całkowity czas przechowywania w latach dla skonfigurowanych kopii zapasowych |
| YearlyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania letniego przechowywania |
| YearlyRetentionMonthsOfTheYear_s |Tekst ||Miesiące roku wybrane do rocznego przechowywania |
| YearlyRetentionFormat_s |Tekst ||Typ konfiguracji dla letniego przechowywania, na przykład, codziennie w przypadku dnia, co tydzień na podstawie | |
| YearlyRetentionDaysOfTheMonth_s |Tekst ||Daty miesiąca wybranego do rocznego dokowej |
| SynchronisationFrequencyPerDay_s |Liczbowy całkowity |v2|Liczba zsynchronizowanych kopii zapasowych plików w ciągu dnia dla programów SC DPM i MABS |
| DiffBackupFormat_s |Tekst |v2|Format dla różnicowych kopii zapasowych dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure |
| DiffBackupTime_s |Time |v2|Czas na różnicowe kopie zapasowe dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure|
| DiffBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas przechowywania różnicowych kopii zapasowych dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure|
| LogBackupFrequency_s |Liczba dziesiętna |v2|Częstotliwość tworzenia kopii zapasowych dziennika dla języka SQL|
| LogBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas przechowywania kopii zapasowych dziennika dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure|
| DiffBackupDaysofTheWeek_s |Tekst |v2|Dni tygodnia dla różnicowych kopii zapasowych dla sql w kopii zapasowej maszyny Wirtualnej platformy Azure|
| SourceSystem |Tekst ||System źródłowy bieżących danych — Azure |
| ResourceId |Tekst ||Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst ||Identyfikator subskrypcji zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst ||Grupa zasobów zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst ||Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst ||Typ zasobu, dla którego zbierane są dane. Na przykład przechowalnia |

### <a name="policyassociation"></a>Siekanie zasad

Ta tabela zawiera szczegółowe informacje o skojarzeniach zasad z różnymi jednostkami.

| Pole | Typ danych | Wersje obowiązujące | Opis |
| --- | --- | --- | --- |
| EventName_s |Tekst ||To pole reprezentuje nazwę tego zdarzenia, zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||To pole oznacza bieżącą wersję schematu, jest to **V2** |
| State_s |Tekst ||Bieżący stan obiektu zasad, na przykład Aktywny, Usunięty |
| BackupManagementType_s |Tekst ||Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| OperationName |Tekst ||To pole reprezentuje nazwę bieżącej operacji — Sieciące zasady |
| Kategoria |Tekst ||To pole reprezentuje kategorię danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor, jest to usługa AzureBackupReport |
| Zasób |Tekst ||Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| PolicyUniqueId_g |Tekst ||Unikatowy identyfikator w celu zidentyfikowania zasad |
| VaultUniqueId_s |Tekst ||Unikatowy identyfikator skarbca, do którego należy ta zasada |
| BackupManagementServerUniqueId_s |Tekst |v2 |Pole umożliwiające jednoznaczną identyfikację serwera zarządzania zapasami kopii zapasowych, za pomocą którego chroniony jest element kopii zapasowej, jeśli ma to zastosowanie        |
| SourceSystem |Tekst ||System źródłowy bieżących danych — Azure |
| ResourceId |Tekst ||Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst ||Identyfikator subskrypcji zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst ||Grupa zasobów zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst ||Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst ||Typ zasobu, dla którego zbierane są dane. Na przykład przechowalnia |

### <a name="protected-container"></a>Kontener chroniony

Ta tabela zawiera podstawowe pola dotyczące kontenerów chronionych. (Był ProtectedServer w v1)

| Pole | Typ danych | Opis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Tekst | Pole umożliwiające jednoznaczną identyfikację kontenera chronionego |
| ProtectedContainerOSType_s |Tekst |Typ systemu operacyjnego chronionego kontenera |
| ProtectedContainerOSVersion_s |Tekst |Wersja chronionego kontenera z systemami operacyjnymi |
| AgentVersion_s |Tekst |Numer wersji agenta kopii zapasowej lub agenta ochrony (w przypadku SC DPM i MABS) |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej. Na przykład IaaSVM, FileFolder |
| EntityState_s |Tekst |Bieżący stan chronionego obiektu serwera. Na przykład Aktywne, Usunięte |
| ProtectedContainerFriendlyName_s |Tekst |Przyjazna nazwa chronionego serwera |
| ProtectedContainerName_s |Tekst |Nazwa chronionego kontenera |
| ProtectedContainerWorkloadType_s |Tekst |Typ kopii zapasowej kontenera chronionego. Na przykład IaaSVMContainer |
| ProtectedContainerLocation_s |Tekst |Czy kontener chroniony znajduje się lokalnie, czy na platformie Azure |
| ProtectedContainerType_s |Tekst |Czy kontener chroniony jest serwerem, czy kontenerem |
| ProtectedContainerProtectionState_s"  |Tekst |Stan ochrony chronionego kontenera |

### <a name="storage"></a>Magazyn

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynowaniem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| CloudStorageInBytes_s |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, obliczony na podstawie najnowszej wartości (to pole dotyczy tylko schematu w wersji 1)|
| ProtectedInstances_s |Liczba dziesiętna |Liczba chronionych wystąpień używanych do obliczania magazynu frontendu w rozliczeniach, obliczona na podstawie najnowszej wartości |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu, jest to **V2** |
| State_s |Tekst |Bieżący stan obiektu pamięci masowej, na przykład Aktywny, Usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — Magazyn |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor, jest to usługa AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera, dla którego obliczana jest pamięć masowa |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu magazynu jest obliczany |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład przechowalnia |
| StorageUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| StorageType_s |Tekst |Typ pamięci masowej, na przykład chmura, wolumin, dysk |
| StorageName_s |Tekst |Nazwa jednostki magazynu, na przykład E:\ |
| StorageTotalSizeInGBs_s |Tekst |Całkowity rozmiar magazynu w GB, zużywany przez encję magazynu|

### <a name="storageassociation"></a>Siekanie magazynowania

Ta tabela zawiera podstawowe pola związane z magazynem łączące magazyn z innymi encjami.

| Pole | Typ danych | Opis |
| --- | --- |  --- |
| StorageUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu, jest to **V2** |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej powiązanego z encją magazynu |
| BackupManagementServerUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania serwera zarządzania kopiami zapasowymi powiązanego z encją magazynu|
| VaultUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania magazynu związanego z encją magazynu|
| StorageConsumedInMBs_s |Liczba|Rozmiar magazynu zużywanego przez odpowiedni element kopii zapasowej w odpowiednim magazynie |
| StorageAllocatedInMBs_s |Liczba |Rozmiar miejsca do magazynowania przydzielonego przez odpowiedni element kopii zapasowej w odpowiednim magazynie typu Dysk|

### <a name="vault"></a>Magazyn

Ta tabela zawiera szczegółowe informacje o polach związanych z przechowalnią.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu, jest to **V2** |
| State_s |Tekst |Bieżący stan obiektu przechowalni, na przykład Aktywny, Usunięty |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — Vault |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor, jest to usługa AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, pokazuje nazwę magazynu usług recovery services |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator skarbca |
| VaultName_s |Tekst |Nazwa magazynu |
| AzureDataCenter_s |Tekst |Centrum danych, w którym znajduje się przechowalnia |
| StorageReplicationType_s |Tekst |Typ replikacji magazynu dla przechowalni, na przykład GeoRedundant |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn usług odzyskiwania), dla których zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład przechowalnia |

### <a name="backup-management-server"></a>Serwer zarządzania kopiami zapasowymi

Ta tabela zawiera podstawowe pola dotyczące serwerów zarządzania kopiami zapasowymi.

|Pole  |Typ danych  | Opis  |
|---------|---------|----------|
|BackupManagementServerName_s     |Tekst         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Tekst         |Wersja agenta kopii zapasowej platformy Azure na serwerze zarządzania kopiami zapasowymi          |
|BackupManagementServerVersion_s     |Tekst         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagementServerOSVersion_s     |Tekst            |Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi|
|BackupManagementServerType_s     |Tekst         |Typ serwera zarządzania kopiami zapasowymi, jako MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Pole umożliwiające jednoznaczną identyfikację serwera zarządzania kopiami zapasowymi       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

W tej tabeli określono obciążenie woluminu, z które jest skojarzone wolumin.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| StorageUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| BackupItemType_s |Tekst |Obciążenia, dla których ten wolumin jest preferowanym magazynem|

### <a name="protectedinstance"></a>Chroniona nieskładka

Ta tabela zawiera podstawowe pola związane z wystąpieniami chronionymi.

| Pole | Typ danych |Wersje obowiązujące | Opis |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Tekst |v2|Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej maszyn wirtualnych kopii zapasowej przy użyciu programu DPM, MABS|
| ProtectedContainerUniqueId_s |Tekst |v2|Unikatowy identyfikator używany do identyfikowania chronionego kontenera dla wszystkiego, z wyjątkiem maszyn wirtualnych kopii zapasowych przy użyciu programu DPM, MABS|
| ProtectedInstanceCount_s |Tekst |v2|Liczba wystąpień chronionych dla skojarzonego elementu kopii zapasowej lub chronionego kontenera w tym dniu i godzinie|

### <a name="recoverypoint"></a>Punkt odzyskiwania

Ta tabela zawiera podstawowe pola związane z punktem odzyskiwania.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej maszyn wirtualnych kopii zapasowej przy użyciu programu DPM, MABS|
| OldestRecoveryPointTime_s |Tekst |Data godziny najstarszego punktu odzyskiwania dla elementu kopii zapasowej|
| OldestRecoveryPointLocation_s |Tekst |Lokalizacja najstarszego punktu odzyskiwania elementu kopii zapasowej|
| LatestRecoveryPointTime_s |Tekst |Data daty ostatniego punktu odzyskiwania elementu kopii zapasowej|
| LatestRecoveryPointLocation_s |Tekst |Lokalizacja najnowszego punktu odzyskiwania elementu kopii zapasowej|

## <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Poniżej znajduje się kilka przykładów ułatwiające pisanie zapytań dotyczących danych usługi Azure Backup, które znajdują się w tabeli Diagnostyka platformy Azure:

- Wszystkie udane zadania tworzenia kopii zapasowych

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Wszystkie zadania tworzenia kopii zapasowych nie powiodły się

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowych maszyn wirtualnych platformy Azure

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowych dziennika SQL

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Wszystkie pomyślne zadania agenta usługi Azure Backup

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>Następne kroki

Po przejrzeniu modelu danych można rozpocząć [tworzenie zapytań niestandardowych](../azure-monitor/learn/tutorial-logs-dashboards.md) w dziennikach usługi Azure Monitor, aby utworzyć własny pulpit nawigacyjny.
