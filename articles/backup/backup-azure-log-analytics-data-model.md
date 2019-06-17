---
title: Usługa Azure Monitor w dziennikach modelu danych usługi Azure Backup
description: Ten artykuł dotyczy usługi Azure Monitor rejestruje Szczegóły modelu danych dla danych usługi Azure Backup.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61234975"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Model danych analizy dzienników dla danych usługi Azure Backup

Użyj modelu danych usługi Log Analytics, aby utworzyć niestandardowe alerty z usługą Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Przy użyciu modelu danych usługi Azure Backup

Następujące pola, które są udostępniane w ramach modelu danych umożliwia tworzenie wizualizacji, zapytania niestandardowe i pulpit nawigacyjny zgodnie z wymaganiami.

### <a name="alert"></a>Alerty

Ta tabela zawiera szczegółowe informacje dotyczące alertów powiązanych pól.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| AlertUniqueId_s |Text |Unikatowy identyfikator wygenerowany alert |
| AlertType_s |Text |Typ alertu, na przykład kopię zapasową |
| AlertStatus_s |Text |Stan alertu, na przykład aktywny |
| AlertOccurrenceDateTime_s |Data/godzina |Data i godzina utworzenia alertu |
| AlertSeverity_s |Text |Ważność alertu, na przykład, krytyczny |
|AlertTimeToResolveInMinutes_s    | Liczba        |Czas poświęcony na rozwiązania alertu. Puste w przypadku aktywnych alertów.         |
|AlertConsolidationStatus_s   |Text         |Ustalenie, czy alert jest to skonsolidowany alert, czy nie         |
|CountOfAlertsConsolidated_s     |Liczba         |Liczba alertów skonsolidowanych, jeśli jest to skonsolidowany alert          |
|AlertRaisedOn_s     |Text         |Typ obiektu, który ten alert jest zgłaszany na         |
|AlertCode_s     |Text         |Kod, aby jednoznacznie zidentyfikować określony typ alertu         |
|RecommendedAction_s   |Text         |Akcja zalecana w celu rozwiązania alertu         |
| EventName_s |Text |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| SchemaVersion_s |Text |Bieżąca wersja schematu, na przykład **V2** |
| State_s |Text |Bieżący stan alertu obiektu, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten alert |
| OperationName |Text |Nazwa bieżącej operacji, na przykład Alert |
| Category |Text |Kategoria danych diagnostycznych wypchnięte do usługi Azure Monitor dzienniki. Zawsze AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Text |Unikatowy identyfikator serwera chronionego, skojarzonych z tym alertem |
| VaultUniqueId_s |Text |Unikatowy identyfikator chronionego magazynu skojarzonego z alertem |
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Unikatowy identyfikator dla zasobu o tym, jakie dane są zbierane. Na przykład identyfikator zasobu magazynu usługi Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="backupitem"></a>BackupItem

Ta tabela zawiera szczegółowe informacje o kopii zapasowej pól powiązanych elementów.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej |
| BackupItemId_s |Text |Identyfikator elementu kopii zapasowej |
| BackupItemName_s |Text |Nazwa elementu kopii zapasowej |
| BackupItemFriendlyName_s |Text |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemType_s |Text |Typ elementu kopii zapasowej, na przykład maszyna wirtualna FileFolder |
| ProtectionState_s |Text |Bieżący stan ochrony w element kopii zapasowej, na przykład chronione, ProtectionStopped |
| ProtectionGroupName_s |Text | Nazwa grupy ochrony w element kopii zapasowej jest chroniony, SC programu DPM i serwera usługi Mab, jeśli ma to zastosowanie|
| SecondaryBackupProtectionState_s |Text |Włączenie ochrony pomocniczej dla elementu kopii zapasowej|
| SchemaVersion_s |Text |Wersja schematu, na przykład **V2** |
| State_s |Text |Stan obiektu elementów kopii zapasowej, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten element kopii zapasowej |
| OperationName |Text |Nazwa operacji, na przykład BackupItem |
| Category |Text |Kategoria danych diagnostycznych wypchnięte do usługi Azure Monitor dzienniki. Zawsze AzureBackupReport |
| Resource |Text |Zasób danych, które są zbierane, na przykład nazwa magazynu usługi Recovery Services |
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Identyfikator zasobu dla zbieranych danych, na przykład magazyn usługi Recovery Services identyfikatora zasobu |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (na przykład. Magazyn usługi Recovery Services) dla zbieranych danych |
| ResourceGroup |Text |Grupa zasobów zasobu (na przykład. Magazyn usługi Recovery Services) dla zbieranych danych |
| ResourceProvider |Text |Dostawca zasobów dla danych zbieranych, na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu dla danych, które są zbierane, na przykład magazyny. |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia elementu kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Text |To pole wskazuje bieżącą wersję schematu, jest **V2** |
| State_s |Text |Bieżący stan obiektu skojarzenia elementu kopii zapasowej, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Rozmiar frontonu elementu kopii zapasowej |
| BackupManagementServerUniqueId_s |Text | Pole do unikatowego identyfikowania serwera zarządzania kopii zapasowej elementu kopii zapasowej jest chroniony przez funkcję, jeśli ma to zastosowanie |
| Category |Text |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji - BackupItemAssociation |
| Resource |Text |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| PolicyUniqueId_g |Text |Unikatowy identyfikator zasad skojarzonych z elementu kopii zapasowej |
| ProtectedServerUniqueId_s |Text |Unikatowy identyfikator serwera chronionego, skojarzony element kopii zapasowej |
| VaultUniqueId_s |Text |Unikatowy identyfikator magazynu zawierającego element kopii zapasowej |
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (na przykład. Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text |Grupa zasobów zasobu (na przykład. Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text |Dostawca zasobów dla danych zbieranych, na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu dla danych są gromadzone, na przykład magazyny |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia elementu kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Text         |Wersja agenta usługi Azure Backup na serwerze zarządzania kopiami zapasowymi          |
|BackupManagementServerVersion_s     |Text         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagementServerOSVersion_s    |Text            |Wersja systemu operacyjnego kopii zapasowej serwera zarządzania|
|BackupManagementServerType_s     |Text         |Typ zarządzania kopiami zapasowymi serwera programu DPM z serwera usługi Mab, SC|
|BackupManagementServerUniqueId_s     |Text         |Pole do unikatowego identyfikowania serwera zarządzania kopii zapasowej       |
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (na przykład. Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text |Grupa zasobów zasobu (na przykład. Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text |Dostawca zasobów dla danych zbieranych, na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu dla danych są gromadzone, na przykład magazyny |

### <a name="job"></a>Zadanie

Ta tabela zawiera szczegółowe informacje o polach związanych z pracą.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Text |Wersja schematu, na przykład **V2** |
| State_s |Text |Bieżący stan obiektu zadania, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji — zadanie |
| Category |Text |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Azure Monitor dzienników, jest AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Text |Unikatowy identyfikator serwera chronionego skojarzone zadania |
| ProtectedContainerUniqueId_s |Text | Unikatowy identyfikator kontenera chronionych zadanie jest uruchamiane na |
| VaultUniqueId_s |Text |Unikatowy identyfikator chronionego magazynu. |
| JobOperation_s |Text |Operacja, dla którego zadanie jest uruchamiane na przykład kopii zapasowej, przywracanie, skonfiguruj w kopii zapasowej |
| JobStatus_s |Text |Stan zadania zakończone, na przykład, zakończone, nie powiodło się |
| JobFailureCode_s |Text |Z powodu którego wystąpiło niepowodzenie zadania ciągu kodu błędu |
| JobStartDateTime_s |Data/godzina |Data i godzina, kiedy zadania uruchamiania wprowadzenie |
| BackupStorageDestination_s |Text |Docelowy magazyn kopii zapasowych, na przykład, chmura, dysku  |
| AdHocOrScheduledJob_s |Text | Aby określić, czy zadanie jest w trybie Ad Hoc lub zaplanowane |
| JobDurationInSecs_s | Liczba |Czas trwania zadania łączna liczba w ciągu kilku sekund |
| DataTransferredInMB_s | Liczba |Dane przesyłane w Megabajtach dla tego zadania|
| JobUniqueId_g |Text |Unikatowy identyfikator zadania |
| RecoveryJobDestination_s |Text | Miejsce docelowe zadania odzyskiwania, gdzie dane są odzyskiwane. |
| RecoveryJobRPDateTime_s |DateTime | Data godzina utworzenia punktu odzyskiwania, który jest odzyskiwany |
| RecoveryJobRPLocation_s |Text | Miejsce przechowywania punktu odzyskiwania, który jest odzyskiwany|
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services|
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="policy"></a>Zasady

Ta tabela zawiera szczegółowe informacje o polach związane z zasadami.

| Pole | Typ danych | Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| EventName_s |Text ||To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text ||To pole wskazuje bieżącą wersję schematu, jest **V2** |
| State_s |Text ||Bieżący stan obiektu zasad, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text ||Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text ||To pole reprezentuje nazwę bieżącej operacji — zasady |
| Category |Text ||To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Azure Monitor dzienników, jest AzureBackupReport |
| Resource |Text ||Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| PolicyUniqueId_g |Text ||Unikatowy identyfikator zasad |
| PolicyName_s |Text ||Nazwy zdefiniowane zasady |
| BackupFrequency_s |Text ||Częstotliwość, z którym są uruchamiane kopii zapasowych, na przykład, codziennie, co tydzień |
| BackupTimes_s |Text ||Data i godzina, kiedy są zaplanowane kopie zapasowe |
| BackupDaysOfTheWeek_s |Text ||Dni tygodnia, gdy zostały zaplanowane kopie zapasowe |
| RetentionDuration_s |Liczba całkowita ||Czas trwania przechowywania skonfigurowane kopie zapasowe |
| DailyRetentionDuration_s |Liczba całkowita ||Czas trwania przechowywania całkowitej dni skonfigurowane kopie zapasowe |
| DailyRetentionTimes_s |Text ||Data i godzina, gdy skonfigurowano przechowywanie codziennego |
| WeeklyRetentionDuration_s |Liczba dziesiętna ||Łączny czas przechowywania co tydzień, w ciągu tygodni skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes_s |Text ||Data i godzina, jeśli skonfigurowano przechowywanie tygodniowego |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dni tygodnia wybrane do przechowywania danych co tydzień |
| MonthlyRetentionDuration_s |Liczba dziesiętna ||Czas trwania przechowywania całkowitej miesiące w przypadku skonfigurowane kopie zapasowe |
| MonthlyRetentionTimes_s |Text ||Data i godzina, po skonfigurowaniu przechowywanie miesięcznego |
| MonthlyRetentionFormat_s |Text ||Typ konfiguracji do miesięcznych przechowywania danych, na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dni tygodnia wybrane do przechowywania miesięczne |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Tygodnie miesiąca, gdy miesięczne przechowywanie jest skonfigurowany, na przykład pierwszy, ostatni itp. |
| YearlyRetentionDuration_s |Liczba dziesiętna ||Czas trwania przechowywania całkowitej lat skonfigurowane kopie zapasowe |
| YearlyRetentionTimes_s |Text ||Data i godzina, po skonfigurowaniu roczne przechowywanie |
| YearlyRetentionMonthsOfTheYear_s |Text ||Miesiące roku wybrane do przechowywania roczne |
| YearlyRetentionFormat_s |Text ||Typ konfiguracji do rocznych przechowywania danych, na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Daty, miesiąca, wybrane do przechowywania roczne |
| SynchronisationFrequencyPerDay_s |Liczba całkowita |v2|Ile razy w ciągu dnia, tworzenie kopii zapasowej plików jest synchronizowane programu DPM SC i serwera usługi Mab |
| DiffBackupFormat_s |Text |v2|Format dla różnych kopii zapasowych SQL w usłudze Kopia zapasowa maszyny Wirtualnej platformy Azure |
| DiffBackupTime_s |Time |v2|Czas różnicowe kopie zapasowe SQL w usłudze Azure VM Backup|
| DiffBackupRetentionDuration_s |Liczba dziesiętna |v2|Okres przechowywania dla różnych kopii zapasowych dla programu SQL w usłudze Azure VM Backup|
| LogBackupFrequency_s |Liczba dziesiętna |v2|Częstotliwość kopii zapasowych dziennika SQL|
| LogBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas trwania przechowywania kopii zapasowych dziennika SQL w usłudze Azure VM Backup|
| DiffBackupDaysofTheWeek_s |Text |v2|Dni tygodnia dla różnych kopii zapasowych dla programu SQL w usłudze Azure VM Backup|
| SourceSystem |Text ||Źródłowy system aktualnych danych — Azure |
| ResourceId |Text ||Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Text ||Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text ||Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text ||Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text ||Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="policyassociation"></a>PolicyAssociation

Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia zasad z różnymi jednostkami.

| Pole | Typ danych | Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| EventName_s |Text ||To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text ||To pole wskazuje bieżącą wersję schematu, jest **V2** |
| State_s |Text ||Bieżący stan obiektu zasad, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text ||Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text ||To pole reprezentuje nazwę bieżącej operacji - PolicyAssociation |
| Category |Text ||To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Azure Monitor dzienników, jest AzureBackupReport |
| Resource |Text ||Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| PolicyUniqueId_g |Text ||Unikatowy identyfikator zasad |
| VaultUniqueId_s |Text ||Unikatowy identyfikator magazynu, do których te zasady należy do |
| BackupManagementServerUniqueId_s |Text |v2 |Pole do unikatowego identyfikowania serwera zarządzania kopii zapasowej elementu kopii zapasowej jest chroniony przez funkcję, jeśli ma to zastosowanie        |
| SourceSystem |Text ||Źródłowy system aktualnych danych — Azure |
| ResourceId |Text ||Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Text ||Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text ||Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text ||Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text ||Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="protected-container"></a>Chronione kontenera

Ta tabela zawiera pola podstawowe informacje o kontenerach chronione. (Było ProtectedServer w wersji 1)

| Pole | Typ danych | Opis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Pole do unikatowego identyfikowania kontener chronione |
| ProtectedContainerOSType_s |Text |Typ systemu operacyjnego chronionych kontenera |
| ProtectedContainerOSVersion_s |Text |Wersja systemu operacyjnego chronionych kontenera |
| AgentVersion_s |Text |Numer wersji agenta kopii zapasowej lub agenta ochrony (w przypadku programu DPM SC i serwera usługi MAB) |
| BackupManagementType_s |Text |Typ dostawcy do wykonywania kopii zapasowej na przykład IaaSVM FileFolder |
| EntityState_s |Text |Bieżący stan obiektu chronionego serwera, na przykład, aktywne, usunięte |
| ProtectedContainerFriendlyName_s |Text |Przyjazna nazwa chronionego serwera |
| ProtectedContainerName_s |Text |Nazwa chronionego kontenera |
| ProtectedContainerWorkloadType_s |Text |Typ kontenera chronione kopii zapasowej na przykład IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Czy chroniony kontenera jest znajdujących się lokalnie lub na platformie Azure |
| ProtectedContainerType_s |Text |Czy chroniony kontenera jest na serwerze lub kontener |

### <a name="storage"></a>Magazyn

Ta tabela zawiera szczegółowe informacje o polach związane z magazynowaniem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| CloudStorageInBytes_s |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, które są obliczane na podstawie najnowszej wartości |
| ProtectedInstances_s |Liczba dziesiętna |Liczba chronionych wystąpień służące do obliczania magazynu frontonu w rozliczeń, obliczonej na podstawie wartości najnowsze |
| EventName_s |Text |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text |To pole wskazuje bieżącą wersję schematu, jest **V2** |
| State_s |Text |Bieżący stan obiektu magazynu, na przykład, aktywne, usunięte |
| BackupManagementType_s |Text |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji - Storage |
| Category |Text |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Azure Monitor dzienników, jest AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Text |Unikatowy identyfikator serwera chronionego, dla którego jest obliczana magazynu |
| VaultUniqueId_s |Text |Unikatowy identyfikator magazynu dla magazynu jest obliczana. |
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="storageassociation"></a>StorageAssociation

Ta tabela zawiera podstawowe pola związane z magazynowaniem magazynu nawiązywania połączenia z innymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| SchemaVersion_s |Text |To pole wskazuje bieżącą wersję schematu, jest **V2** |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej, związane z jednostką magazynu |
| BackupManagementServerUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania serwera zarządzania kopiami zapasowymi, związane z jednostką magazynu|
| VaultUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania magazynu związane z jednostką magazynu|
| StorageConsumedInMBs_s |Liczba|Rozmiar magazynu używane przez odpowiedni element kopii zapasowej z odpowiedniego magazynu |
| StorageAllocatedInMBs_s |Liczba |Rozmiar pamięci przydzielonej przez odpowiadający element kopii zapasowej w magazynie odpowiedni typ dysku|

### <a name="vault"></a>Magazyn

Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z magazynu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Text |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Text |To pole wskazuje bieżącą wersję schematu, jest **V2** |
| State_s |Text |Bieżący stan obiektu magazynu, na przykład, aktywne, usunięte |
| OperationName |Text |To pole reprezentuje nazwę bieżącej operacji — magazyn |
| Category |Text |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Azure Monitor dzienników, jest AzureBackupReport |
| Resource |Text |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| VaultUniqueId_s |Text |Unikatowy identyfikator magazynu |
| VaultName_s |Text |Nazwa magazynu |
| AzureDataCenter_s |Text |Centrum danych, w którym znajduje się magazyn |
| StorageReplicationType_s |Text |Typ replikacji magazynu dla magazynu, na przykład GeoRedundant |
| SourceSystem |Text |Źródłowy system aktualnych danych — Azure |
| ResourceId |Text |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Text |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Text |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Text |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Text |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="backup-management-server"></a>Serwer zarządzania kopiami zapasowymi

Ta tabela zawiera pola podstawowe o serwery zarządzania kopiami zapasowymi.

|Pole  |Typ danych  | Opis  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Text         |Wersja agenta usługi Azure Backup na serwerze zarządzania kopiami zapasowymi          |
|BackupManagmentServerVersion_s     |Text         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagmentServerOSVersion_s     |Text            |Wersja systemu operacyjnego kopii zapasowej serwera zarządzania|
|BackupManagementServerType_s     |Text         |Typ zarządzania kopiami zapasowymi serwera programu DPM z serwera usługi Mab, SC|
|BackupManagmentServerUniqueId_s     |Text         |Pole do unikatowego identyfikowania serwera zarządzania kopii zapasowej       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ta tabela określa workload(s), który wolumin jest skojarzona z.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| StorageUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| BackupItemType_s |Text |Obciążeń, dla których ten wolumin jest preferowany Magazyn|

### <a name="protectedinstance"></a>ProtectedInstance

Ta tabela zawiera pola pokrewne podstawowe chronionych wystąpień.

| Pole | Typ danych |Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych z kopii zapasowej za pomocą programu DPM, serwera usługi Mab|
| ProtectedContainerUniqueId_s |Text |v2|Unikatowy identyfikator używany do identyfikowania chronionych kontenera dla wszystkim, z wyjątkiem maszyn wirtualnych z kopii zapasowej za pomocą programu DPM, serwera usługi Mab|
| ProtectedInstanceCount_s |Text |v2|Liczba chronionych wystąpień dla skojarzonego elementu kopii zapasowej lub chronionego kontener na daty i godziny|

### <a name="recoverypoint"></a>RecoveryPoint

Ta tabela zawiera podstawowe odzyskiwania punktu powiązanych pól.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych z kopii zapasowej za pomocą programu DPM, serwera usługi Mab|
| OldestRecoveryPointTime_s |Text |Data i godzina starsze punkty odzyskiwania dla elementu kopii zapasowej|
| OldestRecoveryPointLocation_s |Text |Lokalizacja najstarszy punkt odzyskiwania dla elementu kopii zapasowej|
| LatestRecoveryPointTime_s |Text |Data i godzina najnowszego punktu odzyskiwania dla elementu kopii zapasowej|
| LatestRecoveryPointLocation_s |Text |Lokalizacja najnowszy punkt odzyskiwania dla elementu kopii zapasowej|

## <a name="next-steps"></a>Kolejne kroki

Po przejrzeniu modelu danych, można rozpocząć [tworzenia zapytań niestandardowych](../azure-monitor/learn/tutorial-logs-dashboards.md) w dzienniki usługi Azure Monitor, aby utworzyć własny pulpit nawigacyjny.
