---
title: Model danych usługi Log Analytics na potrzeby usługi Azure Backup
description: Ten artykuł zawiera informacje o szczegóły modelu danych analizy dzienników dla danych kopii zapasowej Azure.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a2ba1d523e7a6364420302eee095f24fd08eadb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605777"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Model danych analizy dziennika dla danych kopia zapasowa Azure
W tym artykule opisano model danych używany do wypychania danych raportowania do analizy dzienników. Użycie tego modelu danych, można tworzyć zapytania niestandardowe pulpity nawigacyjne i wykorzystanie go w analizy dzienników. 

## <a name="using-azure-backup-data-model"></a>Za pomocą usługi Kopia zapasowa Azure modelu danych
Następujące pola w ramach modelu danych służy do tworzenia elementów wizualnych, niestandardowych kwerend i pulpitu nawigacyjnego zgodnie z wymaganiami.

### <a name="alert"></a>Alerty
Ta tabela zawiera szczegóły dotyczące alertu związane pola.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| AlertUniqueId_s |Tekst |Unikatowy identyfikator wygenerowany alert |
| AlertType_s |Tekst |Typ wygenerowany alert, na przykład kopii zapasowej |
| AlertStatus_s |Tekst |Stan alertu, na przykład aktywny |
| AlertOccurenceDateTime_s |Data/godzina |Data i godzina utworzenia alertu |
| AlertSeverity_s |Tekst |Ważność alertu, na przykład krytyczne |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej, do którego należy ten alert |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu alertu, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten alert |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - alertu |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator elementu chronionej, do którego należy ten alert |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator elementu chronionej, do którego należy ten alert |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="backupitem"></a>BackupItem
Ta tabela zawiera szczegółowe informacje dotyczące tworzenia kopii zapasowej pól związanych z elementu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| BackupItemId_s |Tekst |Identyfikator elementu kopii zapasowej |
| BackupItemName_s |Tekst |Nazwa elementu kopii zapasowej |
| BackupItemFriendlyName_s |Tekst |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemType_s |Tekst |Typ elementu kopii zapasowej, na przykład maszynę Wirtualną, FileFolder |
| ProtectedServerName_s |Tekst |Nazwa serwera chronionego, do którego kopii zapasowej elementu należy do |
| ProtectionState_s |Tekst |Bieżący stan ochrony elementu kopii zapasowej, na przykład chronione, ProtectionStopped |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu elementu kopii zapasowej, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten element kopii zapasowej |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - BackupItem |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="backupitemassociation"></a>BackupItemAssociation
Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia elementu kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu skojarzenia elementu kopii zapasowej, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten element kopii zapasowej |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - BackupItemAssociation |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| PolicyUniqueId_g |Tekst |Unikatowy identyfikator zasad, który element kopii zapasowej jest skojarzony z |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego, do którego należy ten element kopii zapasowej |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu, do którego należy ten element kopii zapasowej |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="job"></a>Zadanie
Ta tabela zawiera szczegółowe informacje o polach związanych z pracą.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej, do którego należy to zadanie |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu zadania, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy to zadanie |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — zadanie |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator chronionej do którego należy to zadanie |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionej do którego należy to zadanie |
| JobOperation_s |Tekst |Operacja, dla którego zadanie jest uruchamiane na przykład kopii zapasowych, przywracania, konfigurowanie usługi Kopia zapasowa |
| JobStatus_s |Tekst |Stan zadania zakończono, na przykład ukończone, nie powiodło się |
| JobFailureCode_s |Tekst |Ciąg kodu błędu z powodu którego wystąpiło niepowodzenie zadania |
| JobStartDateTime_s |Data/godzina |Data i godzina podczas zadań uruchomiona wprowadzenie |
| BackupStorageDestination_s |Tekst |Miejsce docelowe magazynu kopii zapasowej, na przykład chmury, dysku  |
| JobDurationInSecs_s | Liczba |Zadanie całkowity czas w sekundach |
| DataTransferredInMB_s | Liczba |Dane przekazywane w MB dla tego zadania|
| JobUniqueId_g |Tekst |Unikatowy identyfikator zadania |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="policy"></a>Zasady
Ta tabela zawiera szczegółowe informacje o polach związane z zasadami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu zasad, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ta zasada |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — zasady |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| PolicyUniqueId_g |Tekst |Unikatowy identyfikator zasady |
| PolicyName_s |Tekst |Nazwa zdefiniowane zasady |
| BackupFrequency_s |Tekst |Częstotliwość, z którym są uruchamiane kopii zapasowych, na przykład, codziennie, co tydzień |
| BackupTimes_s |Tekst |Data i godzina, kiedy zaplanowane tworzenie kopii zapasowych |
| BackupDaysOfTheWeek_s |Tekst |Dni tygodnia, gdy zaplanowanych kopii zapasowych |
| RetentionDuration_s |Liczby całkowitej |Czas przechowywania kopii zapasowych skonfigurowany |
| DailyRetentionDuration_s |Liczby całkowitej |Czas trwania całkowita przechowywania w dniach skonfigurowanych kopii zapasowych |
| DailyRetentionTimes_s |Tekst |Data i godzina, gdy skonfigurowano przechowywania codziennych |
| WeeklyRetentionDuration_s |Liczba dziesiętna |Całkowita liczba tygodniowy czas przechowywania w tygodniach skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes_s |Tekst |Data i godzina, gdy jest skonfigurowany co tydzień przechowywania |
| WeeklyRetentionDaysOfTheWeek_s |Tekst |Wybrane dni tygodnia do przechowywania co tydzień |
| MonthlyRetentionDuration_s |Liczba dziesiętna |Czas przechowywania całkowitej w miesiącach skonfigurowanych kopii zapasowych |
| MonthlyRetentionTimes_s |Tekst |Data i godzina, gdy miesięczne przechowywania jest skonfigurowany |
| MonthlyRetentionFormat_s |Tekst |Typ konfiguracji miesięczne okresu przechowywania, na przykład codziennie na podstawie co tydzień na podstawie tydzień dzień |
| MonthlyRetentionDaysOfTheWeek_s |Tekst |Wybrane dni tygodnia do przechowywania miesięcznego |
| MonthlyRetentionWeeksOfTheMonth_s |Tekst |Tygodnie miesiąca, jeśli została skonfigurowana miesięczne przechowywania, na przykład pierwszy, ostatni itp. |
| YearlyRetentionDuration_s |Liczba dziesiętna |Czas przechowywania całkowitej w latach skonfigurowanych kopii zapasowych |
| YearlyRetentionTimes_s |Tekst |Data i godzina, gdy corocznych przechowywania jest skonfigurowany |
| YearlyRetentionMonthsOfTheYear_s |Tekst |Wybrane miesięcy do przechowywania roczne |
| YearlyRetentionFormat_s |Tekst |Typ konfiguracji corocznych okresu przechowywania, na przykład codziennie na podstawie co tydzień na podstawie tydzień dzień |
| YearlyRetentionDaysOfTheMonth_s |Tekst |Daty miesiąca wybrany do przechowywania roczne |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="policyassociation"></a>PolicyAssociation
Ta tabela zawiera szczegółowe informacje dotyczące skojarzenia zasad z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu zasad, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej na przykład IaaSVM FileFolder, do którego należy ta zasada |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - PolicyAssociation |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| PolicyUniqueId_g |Tekst |Unikatowy identyfikator zasady |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu, do którego należy ta zasada |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="protectedserver"></a>ProtectedServer
Ta tabela zawiera szczegółowe informacje o chronionych polach związanych z serwerem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| ProtectedServerName_s |Tekst |Nazwa serwera chronionego |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu serwera chronionego, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej na przykład IaaSVM FileFolder, do którego należy ten serwer chroniony |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - ProtectedServer |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego |
| RegisteredContainerId_s |Tekst |Identyfikator kontenera zarejestrowany dla kopii zapasowej |
| ProtectedServerType_s |Tekst |Typ serwera chronionego kopię zapasową na przykład systemu Windows |
| ProtectedServerFriendlyName_s |Tekst |Przyjazna nazwa serwera chronionego |
| AzureBackupAgentVersion_s |Tekst |Numer wersji wersja agenta kopii zapasowej |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Ta tabela zawiera szczegółowe informacje o serwerze chronionym powiązania z innych jednostek.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu skojarzenia chronionego serwera, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ten serwer chroniony |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji - ProtectedServerAssociation |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu, do którego należy ten serwer chroniony |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

### <a name="storage"></a>Magazyn
Ta tabela zawiera szczegółowe informacje dotyczące pola dotyczące magazynu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| CloudStorageInBytes_s |Liczba dziesiętna |Magazynu kopii zapasowej w chmurze używane przez kopie zapasowe, które są obliczane na podstawie najnowszej wartości |
| ProtectedInstances_s |Liczba dziesiętna |Liczba służące do obliczania magazynu frontonu w rozliczeń, obliczonej na podstawie wartości najnowsze wystąpienia chronione |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu magazynu, na przykład aktywny, usunięte |
| BackupManagementType_s |Tekst |Typ dostawcy do wykonywania kopii zapasowej, na przykład IaaSVM FileFolder, do którego należy ta pamięć masowa |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — magazyn |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego, dla której jest obliczany magazynu |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu dla magazynu jest obliczana. |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla których są zbierane dane - magazynów representse tego pola |

### <a name="vault"></a>Magazyn
Ta tabela zawiera szczegółowe informacje dotyczące pola związane z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia, jest zawsze AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole określa bieżącą wersję schematu, jest **V1** |
| State_s |Tekst |Bieżący stan obiektu magazynu, na przykład aktywny, usunięte |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — magazyn |
| Kategoria |Tekst |To pole reprezentuje kategorię do analizy dzienników danych diagnostycznych, jest AzureBackupReport |
| Zasób |Tekst |Jest to zasobu, dla którego dane są zbierane, będzie wyświetlana nazwa magazynu usług odzyskiwania |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu |
| VaultName_s |Tekst |Nazwa magazynu |
| AzureDataCenter_s |Tekst |Centrum danych, w którym znajduje się magazyn |
| StorageReplicationType_s |Tekst |Typ replikacji magazynu dla magazynu, na przykład GeoRedundant |
| SourceSystem |Tekst |System źródła danych bieżącego - Azure |
| ResourceId |Tekst |To pole reprezentuje identyfikator zasobu dla którego dane są zbierane, zawiera identyfikator zasobu magazynu usług odzyskiwania |
| SubscriptionId |Tekst |To pole reprezentuje identyfikator zasobów (RS magazynu), dla którego dane są zbierane w subskrypcji |
| ResourceGroup |Tekst |To pole reprezentuje grupę zasobów, zasobów (RS magazynu), dla którego dane są zbierane |
| ResourceProvider |Tekst |To pole reprezentuje dostawcy zasobów, dla których są zbierane dane - Microsoft.RecoveryServices |
| ResourceType |Tekst |To pole reprezentuje typ zasobu, dla których są zbierane dane - magazynów |

## <a name="next-steps"></a>Kolejne kroki
Po przejrzeniu modelu danych do tworzenia raportów usługi Kopia zapasowa Azure można rozpocząć [Tworzenie pulpitu nawigacyjnego](../log-analytics/log-analytics-dashboards.md) w analizy dzienników.
