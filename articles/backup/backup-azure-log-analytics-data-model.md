---
title: Model danych usługi Log Analytics na potrzeby usługi Azure Backup
description: Ten artykuł zawiera informacje o szczegóły modelu danych usługi Log Analytics dla danych usługi Azure Backup.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 32ce9cb456e302647e2e09db6ed549cd4795fed7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448394"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Model danych analizy dzienników dla danych usługi Azure Backup
Tworzenie raportów przy użyciu modelu danych usługi Log Analytics. Z modelem danych utworzenia niestandardowych kwerend i pulpitów nawigacyjnych lub dostosowywanie danych usługi Azure Backup możesz dowolnie.

## <a name="using-azure-backup-data-model"></a>Przy użyciu modelu danych usługi Azure Backup
Następujące pola, które są udostępniane w ramach modelu danych umożliwia tworzenie wizualizacji, zapytania niestandardowe i pulpit nawigacyjny zgodnie z wymaganiami.

### <a name="alert"></a>Alerty
Ta tabela zawiera szczegółowe informacje dotyczące alertów powiązanych pól.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| AlertUniqueId_s |Tekst |Unikatowy identyfikator wygenerowany alert |
| AlertType_s |Tekst |Typ alertu, na przykład kopię zapasową |
| AlertStatus_s |Tekst |Stan alertu, na przykład aktywny |
| AlertOccurenceDateTime_s |Data/godzina |Data i godzina utworzenia alertu |
| AlertSeverity_s |Tekst |Ważność alertu, na przykład, krytyczny |
| EventName_s |Tekst |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| SchemaVersion_s |Tekst |Bieżąca wersja schematu, na przykład **V1** |
| State_s |Tekst |Bieżący stan alertu obiektu, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten alert |
| OperationName |Tekst |Nazwa bieżącej operacji, na przykład Alert |
| Kategoria |Tekst |Kategoria danych diagnostycznych wypchnięte do usługi Log Analytics. Zawsze AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego, skojarzonych z tym alertem |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionego magazynu skojarzonego z alertem |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Unikatowy identyfikator dla zasobu o tym, jakie dane są zbierane. Na przykład identyfikator zasobu magazynu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="backupitem"></a>BackupItem
Ta tabela zawiera szczegółowe informacje o kopii zapasowej pól powiązanych elementów.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| BackupItemId_s |Tekst |Identyfikator elementu kopii zapasowej |
| BackupItemName_s |Tekst |Nazwa elementu kopii zapasowej |
| BackupItemFriendlyName_s |Tekst |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemType_s |Tekst |Typ elementu kopii zapasowej, na przykład maszyna wirtualna FileFolder |
| ProtectedServerName_s |Tekst |Nazwa chronionego serwera, do którego element kopii zapasowej należy do |
| ProtectionState_s |Tekst |Bieżący stan ochrony w element kopii zapasowej, na przykład chronione, ProtectionStopped |
| SchemaVersion_s |Tekst |Wersja schematu, na przykład **V1** |
| State_s |Tekst |Stan obiektu elementów kopii zapasowej, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten element kopii zapasowej |
| OperationName |Tekst |Nazwa operacji, na przykład BackupItem |
| Kategoria |Tekst |Kategoria danych diagnostycznych wypchnięte do usługi Log Analytics. Zawsze AzureBackupReport |
| Zasób |Tekst |Zasób danych, które są zbierane, na przykład nazwa magazynu usługi Recovery Services |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu dla zbieranych danych, na przykład magazyn usługi Recovery Services identyfikatora zasobu |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (na przykład. Magazyn usługi Recovery Services) dla zbieranych danych |
| ResourceGroup |Tekst |Grupa zasobów zasobu (na przykład. Magazyn usługi Recovery Services) dla zbieranych danych |
| ResourceProvider |Tekst |Dostawca zasobów dla danych zbieranych, na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu dla danych, które są zbierane, na przykład magazyny. |

### <a name="backupitemassociation"></a>BackupItemAssociation
Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia elementu kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu skojarzenia elementu kopii zapasowej, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - BackupItemAssociation |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| PolicyUniqueId_g |Tekst |Unikatowy identyfikator zasad skojarzonych z elementu kopii zapasowej |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego, skojarzony element kopii zapasowej |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu zawierającego element kopii zapasowej |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (na przykład. Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (na przykład. Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów dla danych zbieranych, na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu dla danych są gromadzone, na przykład magazyny |

### <a name="job"></a>Zadanie
Ta tabela zawiera szczegółowe informacje o polach związanych z pracą.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |Wersja schematu, na przykład **V1** |
| State_s |Tekst |Bieżący stan obiektu zadania, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — zadanie |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego skojarzone zadania |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionego magazynu. |
| JobOperation_s |Tekst |Operacja, dla którego zadanie jest uruchamiane na przykład kopii zapasowej, przywracanie, skonfiguruj w kopii zapasowej |
| JobStatus_s |Tekst |Stan zadania zakończone, na przykład, zakończone, nie powiodło się |
| JobFailureCode_s |Tekst |Z powodu którego wystąpiło niepowodzenie zadania ciągu kodu błędu |
| JobStartDateTime_s |Data/godzina |Data i godzina, kiedy zadania uruchamiania wprowadzenie |
| BackupStorageDestination_s |Tekst |Docelowy magazyn kopii zapasowych, na przykład, chmura, dysku  |
| JobDurationInSecs_s | Liczba |Czas trwania zadania łączna liczba w ciągu kilku sekund |
| DataTransferredInMB_s | Liczba |Dane przesyłane w Megabajtach dla tego zadania|
| JobUniqueId_g |Tekst |Unikatowy identyfikator zadania |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services|
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="policy"></a>Zasady
Ta tabela zawiera szczegółowe informacje o polach związane z zasadami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu zasad, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — zasady |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| PolicyUniqueId_g |Tekst |Unikatowy identyfikator zasad |
| PolicyName_s |Tekst |Nazwy zdefiniowane zasady |
| BackupFrequency_s |Tekst |Częstotliwość, z którym są uruchamiane kopii zapasowych, na przykład, codziennie, co tydzień |
| BackupTimes_s |Tekst |Data i godzina, kiedy są zaplanowane kopie zapasowe |
| BackupDaysOfTheWeek_s |Tekst |Dni tygodnia, gdy zostały zaplanowane kopie zapasowe |
| RetentionDuration_s |Liczba całkowita |Czas trwania przechowywania skonfigurowane kopie zapasowe |
| DailyRetentionDuration_s |Liczba całkowita |Czas trwania przechowywania całkowitej dni skonfigurowane kopie zapasowe |
| DailyRetentionTimes_s |Tekst |Data i godzina, gdy skonfigurowano przechowywanie codziennego |
| WeeklyRetentionDuration_s |Liczba dziesiętna |Łączny czas przechowywania co tydzień, w ciągu tygodni skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes_s |Tekst |Data i godzina, jeśli skonfigurowano przechowywanie tygodniowego |
| WeeklyRetentionDaysOfTheWeek_s |Tekst |Dni tygodnia wybrane do przechowywania danych co tydzień |
| MonthlyRetentionDuration_s |Liczba dziesiętna |Czas trwania przechowywania całkowitej miesiące w przypadku skonfigurowane kopie zapasowe |
| MonthlyRetentionTimes_s |Tekst |Data i godzina, po skonfigurowaniu przechowywanie miesięcznego |
| MonthlyRetentionFormat_s |Tekst |Typ konfiguracji do miesięcznych przechowywania danych, na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| MonthlyRetentionDaysOfTheWeek_s |Tekst |Dni tygodnia wybrane do przechowywania miesięczne |
| MonthlyRetentionWeeksOfTheMonth_s |Tekst |Tygodnie miesiąca, gdy miesięczne przechowywanie jest skonfigurowany, na przykład pierwszy, ostatni itp. |
| YearlyRetentionDuration_s |Liczba dziesiętna |Czas trwania przechowywania całkowitej lat skonfigurowane kopie zapasowe |
| YearlyRetentionTimes_s |Tekst |Data i godzina, po skonfigurowaniu roczne przechowywanie |
| YearlyRetentionMonthsOfTheYear_s |Tekst |Miesiące roku wybrane do przechowywania roczne |
| YearlyRetentionFormat_s |Tekst |Typ konfiguracji do rocznych przechowywania danych, na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| YearlyRetentionDaysOfTheMonth_s |Tekst |Daty, miesiąca, wybrane do przechowywania roczne |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="policyassociation"></a>PolicyAssociation
Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia zasad z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu zasad, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - PolicyAssociation |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| PolicyUniqueId_g |Tekst |Unikatowy identyfikator zasad |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu, do których te zasady należy do |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="protectedserver"></a>ProtectedServer
Ta tabela zawiera szczegółowe informacje o chronionych polach związanych z serwerem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| ProtectedServerName_s |Tekst |Nazwa chronionego serwera |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu chronionego serwera, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - ProtectedServer |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego |
| RegisteredContainerId_s |Tekst |Identyfikator kontenera zarejestrowany dla kopii zapasowej |
| ProtectedServerType_s |Tekst |Typ serwera chronionego, na przykład Windows |
| ProtectedServerFriendlyName_s |Tekst |Przyjazna nazwa chronionego serwera |
| AzureBackupAgentVersion_s |Tekst |Numer wersji agenta w wersji kopii zapasowej |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Ta tabela zawiera szczegółowe informacje o serwerze chronionym skojarzenia z innymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu skojarzenia chronionego serwera, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - ProtectedServerAssociation |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu, do którego należy ten chroniony serwer |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="storage"></a>Magazyn
Ta tabela zawiera szczegółowe informacje o polach związane z magazynowaniem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| CloudStorageInBytes_s |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, które są obliczane na podstawie najnowszej wartości |
| ProtectedInstances_s |Liczba dziesiętna |Liczba chronionych wystąpień służące do obliczania magazynu frontonu w rozliczeń, obliczonej na podstawie wartości najnowsze |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu magazynu, na przykład, aktywne, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera, wykonując zadania tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - Storage |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego, dla którego jest obliczana magazynu |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu dla magazynu jest obliczana. |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

### <a name="vault"></a>Magazyn
Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z magazynu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole wskazuje bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu magazynu, na przykład, aktywne, usunięte |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — magazyn |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypchnięte do usługi Log Analytics, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego dane są zbierane, pokazuje nazwę magazynu usługi Recovery Services |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu |
| VaultName_s |Tekst |Nazwa magazynu |
| AzureDataCenter_s |Tekst |Centrum danych, w którym znajduje się magazyn |
| StorageReplicationType_s |Tekst |Typ replikacji magazynu dla magazynu, na przykład GeoRedundant |
| SourceSystem |Tekst |Źródłowy system aktualnych danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład Magazyn identyfikator zasobu usługi Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np.) Magazyn usługi Recovery Services) dla których dane są zbierane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego dane są zbierane. Na przykład dostawcy Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego dane są zbierane. Na przykład magazyny |

## <a name="next-steps"></a>Kolejne kroki
Po przejrzeniu modelu danych do tworzenia raportów usługi Azure Backup, możesz rozpocząć [Tworzenie pulpitu nawigacyjnego](../log-analytics/log-analytics-dashboards.md) w usłudze Log Analytics.
