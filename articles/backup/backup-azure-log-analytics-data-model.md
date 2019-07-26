---
title: Model danych Azure Monitor dzienników dla Azure Backup
description: Ten artykuł zawiera informacje o Azure Monitor szczegóły modelu danych dla Azure Backup danych.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 6563eefffee0ed8d9ce94c3e0a1e24b0d32314f0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466156"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics model danych dla Azure Backup danych

Użyj Log Analytics model danych do tworzenia niestandardowych alertów z Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Korzystanie z Azure Backup model danych

Możesz użyć następujących pól dostarczonych jako część modelu danych do tworzenia wizualizacji, niestandardowych zapytań i pulpitu nawigacyjnego zgodnie z wymaganiami.

### <a name="alert"></a>Alerty

Ta tabela zawiera szczegółowe informacje o polach związanych z alertami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| AlertUniqueId_s |Text |Unikatowy identyfikator wygenerowanego alertu |
| AlertType_s |Text |Typ alertu, na przykład kopia zapasowa |
| AlertStatus_s |Text |Stan alertu, na przykład aktywne |
| AlertOccurrenceDateTime_s |Date/Time |Data i godzina utworzenia alertu |
| AlertSeverity_s |Text |Ważność alertu, na przykład krytyczne |
|AlertTimeToResolveInMinutes_s    | Number        |Czas potrzebny na rozwiązanie alertu. Puste dla aktywnych alertów.         |
|AlertConsolidationStatus_s   |Text         |Określ, czy alert jest skonsolidowanym alertem, czy nie         |
|CountOfAlertsConsolidated_s     |Number         |Liczba alertów skonsolidowanych, jeśli jest to skonsolidowany alert          |
|AlertRaisedOn_s     |Text         |Typ jednostki, w której jest wywoływany alert         |
|AlertCode_s     |Text         |Kod umożliwiający jednoznaczną identyfikację typu alertu         |
|RecommendedAction_s   |Text         |Akcja zalecana w celu rozwiązania alertu         |
| EventName_s |Text |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| SchemaVersion_s |Text |Bieżąca wersja schematu, na przykład **v2** |
| State_s |Text |Bieżący stan obiektu alertu, na przykład aktywny, usunięty |
| BackupManagementType_s |Text |Typ dostawcy służący do wykonywania kopii zapasowej, na przykład IaaSVM, FileFolder, do którego należy ten alert |
| OperationName |Text |Nazwa bieżącej operacji, na przykład alertu |
| Kategoria |Text |Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor. Zawsze AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedContainerUniqueId_s |Text |Unikatowy identyfikator chronionego serwera skojarzony z alertem (został ProtectedServerUniqueId_s w wersji 1)|
| VaultUniqueId_s |Text |Unikatowy identyfikator chronionego magazynu skojarzonego z alertem |
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Unikatowy identyfikator zasobu, na którym są zbierane dane. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="backupitem"></a>BackupItem

Ta tabela zawiera szczegółowe informacje dotyczące pól kopii zapasowej powiązanych z elementem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej |
| BackupItemId_s |Text |Identyfikator elementu kopii zapasowej (to pole jest tylko dla schematu v1) |
| BackupItemName_s |Text |Nazwa elementu kopii zapasowej |
| BackupItemFriendlyName_s |Text |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemType_s |Text |Typ elementu kopii zapasowej, na przykład VM, FileFolder |
| BackupItemProtectionState_s |Text |Stan ochrony elementu kopii zapasowej |
| BackupItemAppVersion_s |Text |Wersja aplikacji elementu kopii zapasowej |
| ProtectionState_s |Text |Bieżący stan ochrony elementu kopii zapasowej, na przykład protected, ProtectionStopped |
| ProtectionGroupName_s |Text | Nazwa grupy ochrony, w której jest chroniona kopia zapasowa, dla programu SC DPM i serwera usługi MAB, jeśli ma zastosowanie|
| SecondaryBackupProtectionState_s |Text |Czy dla elementu kopii zapasowej jest włączona ochrona pomocnicza|
| SchemaVersion_s |Text |Wersja schematu, na przykład **v2** |
| State_s |Text |Stan obiektu elementu kopii zapasowej, na przykład aktywny, usunięty |
| BackupManagementType_s |Text |Typ dostawcy służący do wykonywania kopii zapasowej, na przykład IaaSVM, FileFolder, do której należy ten element kopii zapasowej |
| OperationName |Text |Nazwa operacji, na przykład BackupItem |
| Kategoria |Text |Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor. Zawsze AzureBackupReport |
| Resource |Text |Zasób, dla którego zbierane są dane, na przykład Recovery Services nazwa magazynu |
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych, na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services) dla zbieranych danych |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services) dla zbieranych danych |
| ResourceProvider |Text |Dostawca zasobów zbieranych danych, na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu zbieranych danych, na przykład magazyny |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ta tabela zawiera szczegółowe informacje o skojarzeniach elementów kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Text |To pole oznacza bieżącą wersję schematu, to **v2** |
| State_s |Text |Bieżący stan obiektu skojarzenia elementu kopii zapasowej, na przykład aktywny, usunięty |
| BackupManagementType_s |Text |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Rozmiar frontonu elementu kopii zapasowej |
| BackupManagementServerUniqueId_s |Text | Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby |
| Kategoria |Text |To pole reprezentuje kategorię danych diagnostycznych wypychanych do Log Analytics, AzureBackupReport |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji — BackupItemAssociation |
| Resource |Text |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedContainerUniqueId_s |Text |Unikatowy identyfikator chronionego serwera skojarzony z elementem kopii zapasowej (został ProtectedServerUniqueId_s w wersji 1) |
| VaultUniqueId_s |Text |Unikatowy identyfikator magazynu zawierającego element kopii zapasowej |
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text |Dostawca zasobów zbieranych danych, na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu zbieranych danych, na przykład magazyny |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ta tabela zawiera szczegółowe informacje o skojarzeniach elementów kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Text         |Wersja agenta Azure Backup na serwerze zarządzania kopiami zapasowymi          |
|BackupManagementServerVersion_s     |Text         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagementServerOSVersion_s    |Text            |Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi|
|BackupManagementServerType_s     |Text         |Typ serwera zarządzania kopiami zapasowymi, jako serwera usługi MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Pole do unikatowego identyfikowania serwera zarządzania kopiami zapasowymi       |
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text |Dostawca zasobów zbieranych danych, na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu zbieranych danych, na przykład magazyny |

### <a name="job"></a>Zadanie

Ta tabela zawiera szczegółowe informacje o polach związanych z zadaniami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Text |Wersja schematu, na przykład **v2** |
| State_s |Text |Bieżący stan obiektu zadania, na przykład aktywny, usunięty |
| BackupManagementType_s |Text |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji — zadanie |
| Kategoria |Text |To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor, AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedServerUniqueId_s |Text |Unikatowy identyfikator chronionego serwera skojarzonego z zadaniem |
| ProtectedContainerUniqueId_s |Text | Unikatowy identyfikator identyfikujący chroniony kontener, w którym jest uruchamiane zadanie |
| VaultUniqueId_s |Text |Unikatowy identyfikator chronionego magazynu |
| JobOperation_s |Text |Operacja, dla której zadanie jest uruchamiane na przykład kopia zapasowa, przywracanie, konfigurowanie kopii zapasowej |
| JobStatus_s |Text |Stan ukończonego zadania, na przykład ukończone, zakończone niepowodzeniem |
| JobFailureCode_s |Text |Ciąg kodu błędu z powodu niepowodzenia zadania |
| JobStartDateTime_s |Date/Time |Data i godzina uruchomienia zadania |
| BackupStorageDestination_s |Text |Miejsce docelowe magazynu kopii zapasowych, na przykład Chmura, dysk  |
| AdHocOrScheduledJob_s |Text | Pole, aby określić, czy zadanie jest w trybie ad hoc, czy zaplanowane |
| JobDurationInSecs_s | Number |Łączny czas trwania zadania w sekundach |
| DataTransferredInMB_s | Number |Dane transferowane w MB dla tego zadania|
| JobUniqueId_g |Text |Unikatowy identyfikator identyfikujący zadanie |
| RecoveryJobDestination_s |Text | Miejsce docelowe zadania odzyskiwania, w którym dane są odzyskiwane |
| RecoveryJobRPDateTime_s |Datetime | Data, godzina utworzenia odzyskiwanego punktu odzyskiwania |
| RecoveryJobRPLocation_s |Text | Lokalizacja, w której zapisano odzyskiwany punkt odzyskiwania|
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services|
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="policy"></a>Zasady

Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z zasadami.

| Pole | Typ danych | Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| EventName_s |Text ||To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text ||To pole oznacza bieżącą wersję schematu, to **v2** |
| State_s |Text ||Bieżący stan obiektu zasad, na przykład aktywny, usunięty |
| BackupManagementType_s |Text ||Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text ||To pole reprezentuje nazwę bieżącej operacji — zasady |
| Kategoria |Text ||To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor, AzureBackupReport |
| Resource |Text ||Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| PolicyUniqueId_g |Text ||Unikatowy identyfikator identyfikujący zasady |
| PolicyName_s |Text ||Nazwa zdefiniowanych zasad |
| BackupFrequency_s |Text ||Częstotliwość, z jaką są uruchamiane kopie zapasowe, na przykład codziennie, co tydzień |
| BackupTimes_s |Text ||Data i godzina zaplanowanego tworzenia kopii zapasowych |
| BackupDaysOfTheWeek_s |Text ||Dni tygodnia, w których zaplanowano tworzenie kopii zapasowych |
| RetentionDuration_s |Liczba całkowita ||Czas przechowywania skonfigurowanych kopii zapasowych |
| DailyRetentionDuration_s |Liczba całkowita ||Łączny czas przechowywania w dniach dla skonfigurowanych kopii zapasowych |
| DailyRetentionTimes_s |Text ||Data i godzina skonfigurowania codziennego przechowywania |
| WeeklyRetentionDuration_s |Liczba dziesiętna ||Łączny czas trwania przechowywania w tygodniach dla skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes_s |Text ||Data i godzina skonfigurowania przechowywania tygodniowego |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dni tygodnia wybrane do przechowywania tygodniowego |
| MonthlyRetentionDuration_s |Liczba dziesiętna ||Łączny czas przechowywania w miesiącach dla skonfigurowanych kopii zapasowych |
| MonthlyRetentionTimes_s |Text ||Data i godzina skonfigurowania miesięcznego przechowywania |
| MonthlyRetentionFormat_s |Text ||Typ konfiguracji do przechowywania miesięcznego, na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dni tygodnia wybrane do przechowywania miesięcznie |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Tygodnie miesiąca, w którym jest skonfigurowane przechowywanie miesięczne, na przykład pierwsze, ostatnie itd. |
| YearlyRetentionDuration_s |Liczba dziesiętna ||Łączny czas przechowywania w latach dla skonfigurowanych kopii zapasowych |
| YearlyRetentionTimes_s |Text ||Data i godzina skonfigurowania okresu przechowywania rocznego |
| YearlyRetentionMonthsOfTheYear_s |Text ||Miesiąc roku wybranego do przechowywania rocznego |
| YearlyRetentionFormat_s |Text ||Typ konfiguracji do przechowywania rocznego, na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Daty w miesiącu wybrane do przechowywania rocznego |
| SynchronisationFrequencyPerDay_s |Liczba całkowita |v2|Liczba przypadków synchronizacji kopii zapasowej plików dla programu SC DPM i serwera usługi MAB |
| DiffBackupFormat_s |Text |v2|Format różnicowych kopii zapasowych dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure |
| DiffBackupTime_s |Time |v2|Czas różnicowych kopii zapasowych bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure|
| DiffBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas przechowywania różnicowych kopii zapasowych dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure|
| LogBackupFrequency_s |Liczba dziesiętna |v2|Częstotliwość tworzenia kopii zapasowych dzienników dla bazy danych SQL|
| LogBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas przechowywania kopii zapasowych dziennika dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Dni tygodnia różnicowych kopii zapasowych dla bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure|
| SourceSystem |Text ||System źródłowy bieżących danych — Azure |
| ResourceId |Text ||Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text ||Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text ||Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text ||Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Text ||Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="policyassociation"></a>PolicyAssociation

Ta tabela zawiera szczegółowe informacje o skojarzeniach zasad z różnymi jednostkami.

| Pole | Typ danych | Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| EventName_s |Text ||To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text ||To pole oznacza bieżącą wersję schematu, to **v2** |
| State_s |Text ||Bieżący stan obiektu zasad, na przykład aktywny, usunięty |
| BackupManagementType_s |Text ||Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text ||To pole reprezentuje nazwę bieżącej operacji — PolicyAssociation |
| Kategoria |Text ||To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor, AzureBackupReport |
| Resource |Text ||Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| PolicyUniqueId_g |Text ||Unikatowy identyfikator identyfikujący zasady |
| VaultUniqueId_s |Text ||Unikatowy identyfikator magazynu, do którego należą te zasady |
| BackupManagementServerUniqueId_s |Text |v2 |Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby        |
| SourceSystem |Text ||System źródłowy bieżących danych — Azure |
| ResourceId |Text ||Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text ||Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text ||Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text ||Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Text ||Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="protected-container"></a>Chroniony kontener

Ta tabela zawiera podstawowe pola dotyczące chronionych kontenerów. (Was ProtectedServer w wersji 1)

| Pole | Typ danych | Opis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Pole do unikatowego identyfikowania chronionego kontenera |
| ProtectedContainerOSType_s |Text |Typ systemu operacyjnego chronionego kontenera |
| ProtectedContainerOSVersion_s |Text |Wersja systemu operacyjnego chronionego kontenera |
| AgentVersion_s |Text |Numer wersji kopii zapasowej agenta lub agenta ochrony (w przypadku oprogramowania SC DPM i serwera usługi MAB) |
| BackupManagementType_s |Text |Typ dostawcy służący do wykonywania kopii zapasowej. Na przykład IaaSVM, FileFolder |
| EntityState_s |Text |Bieżący stan obiektu chronionego serwera. Na przykład aktywne, usunięte |
| ProtectedContainerFriendlyName_s |Text |Przyjazna nazwa serwera chronionego |
| ProtectedContainerName_s |Text |Nazwa chronionego kontenera |
| ProtectedContainerWorkloadType_s |Text |Utworzono kopię zapasową typu chronionego kontenera. Na przykład IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Czy chroniony kontener znajduje się lokalnie, czy na platformie Azure |
| ProtectedContainerType_s |Text |Czy chroniony kontener jest serwerem lub kontenerem |
| ProtectedContainerProtectionState_s’  |Text |Stan ochrony chronionego kontenera |

### <a name="storage"></a>Magazyn

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| CloudStorageInBytes_s |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, obliczony na podstawie najnowszej wartości (to pole jest tylko dla schematu v1)|
| ProtectedInstances_s |Liczba dziesiętna |Liczba chronionych wystąpień używanych do obliczenia magazynu frontonu w ramach rozliczeń obliczonych na podstawie najnowszej wartości |
| EventName_s |Text |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text |To pole oznacza bieżącą wersję schematu, to **v2** |
| State_s |Text |Bieżący stan obiektu magazynu, na przykład aktywny, usunięty |
| BackupManagementType_s |Text |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji — Storage |
| Kategoria |Text |To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor, AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedServerUniqueId_s |Text |Unikatowy identyfikator serwera chronionego, dla którego jest naliczany magazyn |
| VaultUniqueId_s |Text |Unikatowy identyfikator magazynu dla magazynu jest obliczany |
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |
| StorageUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| StorageType_s |Text |Typ magazynu, na przykład Chmura, wolumin, dysk |
| StorageName_s |Text |Nazwa jednostki magazynu, na przykład E:\ |
| StorageTotalSizeInGBs_s |Text |Łączny rozmiar magazynu (w GB) zużyty przez jednostkę magazynu|

### <a name="storageassociation"></a>StorageAssociation

Ta tabela zawiera podstawowe pola powiązane z magazynem łączące magazyn z innymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| SchemaVersion_s |Text |To pole oznacza bieżącą wersję schematu, to **v2** |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej powiązanego z jednostką magazynu |
| BackupManagementServerUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania serwera zarządzania kopiami zapasowymi powiązanego z jednostką magazynową|
| VaultUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania magazynu związanego z jednostką magazynu|
| StorageConsumedInMBs_s |Number|Rozmiar magazynu zużywanego przez odpowiedni element kopii zapasowej w odpowiednim magazynie |
| StorageAllocatedInMBs_s |Number |Rozmiar magazynu przydzielonego przez odpowiedni element kopii zapasowej w odpowiednim magazynie typu dysk|

### <a name="vault"></a>Magazyn

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text |To pole oznacza bieżącą wersję schematu, to **v2** |
| State_s |Text |Bieżący stan obiektu magazynu, na przykład aktywny, usunięty |
| OperationName |Text |To pole reprezentuje nazwę bieżącego magazynu operacji |
| Kategoria |Text |To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor, AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| VaultUniqueId_s |Text |Unikatowy identyfikator magazynu |
| VaultName_s |Text |Nazwa magazynu |
| AzureDataCenter_s |Text |Centrum danych, w którym znajduje się magazyn |
| StorageReplicationType_s |Text |Typ replikacji magazynu dla magazynu, na przykład geonadmiarowości |
| SourceSystem |Text |System źródłowy bieżących danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| Grupa zasobów |Text |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Text |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="backup-management-server"></a>Serwer zarządzania kopiami zapasowymi

Ta tabela zawiera podstawowe pola dotyczące serwerów zarządzania kopiami zapasowymi.

|Pole  |Typ danych  | Opis  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Text         |Wersja agenta Azure Backup na serwerze zarządzania kopiami zapasowymi          |
|BackupManagmentServerVersion_s     |Text         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagmentServerOSVersion_s     |Text            |Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi|
|BackupManagementServerType_s     |Text         |Typ serwera zarządzania kopiami zapasowymi, jako serwera usługi MAB, SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |Pole do unikatowego identyfikowania serwera zarządzania kopiami zapasowymi       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

W tej tabeli określono obciążenia, z którymi skojarzony jest wolumin.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| StorageUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| BackupItemType_s |Text |Obciążenia, dla których ten wolumin jest preferowanym magazynem|

### <a name="protectedinstance"></a>ProtectedInstance

Ta tabela zawiera podstawowe pola powiązane z chronionymi wystąpieniami.

| Pole | Typ danych |Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych, których kopia zapasowa jest wykonywana za pomocą programu DPM, serwera usługi MAB|
| ProtectedContainerUniqueId_s |Text |v2|Unikatowy identyfikator używany do identyfikowania chronionego kontenera dla wszystkiego z wyjątkiem maszyn wirtualnych, których kopia zapasowa została utworzona przy użyciu programu DPM, serwera usługi MAB|
| ProtectedInstanceCount_s |Text |v2|Liczba chronionych wystąpień dla skojarzonego elementu kopii zapasowej lub chronionego kontenera w tej dacie i godzinie|

### <a name="recoverypoint"></a>RecoveryPoint

Ta tabela zawiera podstawowe pola związane z punktem odzyskiwania.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych, których kopia zapasowa jest wykonywana za pomocą programu DPM, serwera usługi MAB|
| OldestRecoveryPointTime_s |Text |Data i godzina najstarszego punktu odzyskiwania dla elementu kopii zapasowej|
| OldestRecoveryPointLocation_s |Text |Lokalizacja najstarszego punktu odzyskiwania dla elementu kopii zapasowej|
| LatestRecoveryPointTime_s |Text |Data i godzina ostatniego punktu odzyskiwania dla elementu kopii zapasowej|
| LatestRecoveryPointLocation_s |Text |Lokalizacja najnowszego punktu odzyskiwania dla elementu kopii zapasowej|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu modelu danych możesz rozpocząć [Tworzenie niestandardowych zapytań](../azure-monitor/learn/tutorial-logs-dashboards.md) w dziennikach Azure monitor, aby utworzyć własny pulpit nawigacyjny.
