---
title: Model danych dla zdarzeń diagnostycznych usługi Azure Backup
description: Ten model danych jest w odniesieniu do trybu specyficznego dla zasobu wysyłania zdarzeń diagnostycznych do usługi Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583388"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Model danych dla zdarzeń diagnostyki kopii zapasowych platformy Azure

## <a name="coreazurebackup"></a>CoreAzureBackup (właśc.

Ta tabela zawiera informacje o podstawowych jednostkach kopii zapasowej, takich jak magazyny i elementy kopii zapasowej.

| **Pole**                         | **Typ danych** | **Opis**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Tekst          | Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania. |
| OperationName                     | Tekst          | To pole reprezentuje nazwę bieżącej operacji — BackupItem, BackupItemAssociation lub ProtectedContainer. |
| Kategoria                          | Tekst          | To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników usługi Azure Monitor. Na przykład CoreAzureBackup. |
| AgentVersion (Wersja agenta)                      | Tekst          | Numer wersji agenta kopii zapasowej lub agenta ochrony (w przypadku SC DPM i MABS) |
| Usługa AzureBackupAgentVersion           | Tekst          | Wersja agenta kopii zapasowej platformy Azure na serwerze zarządzania kopiami zapasowymi |
| Usługa AzureDataCenter                   | Tekst          | Centrum danych, w którym znajduje się przechowalnia                       |
| BackupItemAppVersion              | Tekst          | Wersja aplikacji elementu kopii zapasowej                       |
| Nazwa kopii zapasowej            | Tekst          | Przyjazna nazwa elementu kopii zapasowej                             |
| Nazwa kopii zapasowej                    | Tekst          | Nazwa elementu kopii zapasowej                                      |
| BackupItemProtectionState         | Tekst          | Stan ochrony elementu kopii zapasowej                          |
| Rozmiar kopii zapasowej            | Tekst          | Rozmiar front-endu elementu kopii zapasowej                            |
| Typ kopii zapasowej                    | Tekst          | Typ elementu kopii zapasowej. Na przykład: Maszyna wirtualna, FileFolder             |
| BackupItemUniqueId                | Tekst          | Unikatowy identyfikator elementu kopii zapasowej                         |
| Typ serwera serwerów generyczów generatywnych        | Tekst          | Typ serwera zarządzania kopiami zapasowymi, tak jak w systemie MABS, SC DPM     |
| BackupManagementServerUniqueId    | Tekst          | Pole umożliwiające jednoznaczną identyfikację serwera zarządzania kopiami zapasowymi      |
| Rodzaj y manania kopii zapasowej              | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej. Na przykład IaaSVM, FileFolder |
| Nazwa serwera generyczów        | Tekst          | Nazwa serwera zarządzania kopiami zapasowymi                         |
| BackupManagementServerOSVersion   | Tekst          | Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi                   |
| BackupManagementServerVersion     | Tekst          | Wersja serwera zarządzania kopiami zapasowymi                      |
| LatestRecoveryPointLocation (Lokalizacja programu LatestRecoveryPointLocation)       | Tekst          | Lokalizacja najnowszego punktu odzyskiwania elementu kopii zapasowej    |
| NajnowszeRecoveryPointTime           | DateTime      | Data daty ostatniego punktu odzyskiwania elementu kopii zapasowej   |
| NajstarszarecoveryPointLocation       | Tekst          | Lokalizacja najstarszego punktu odzyskiwania elementu kopii zapasowej    |
| NajstarszyRecoveryPointTime           | DateTime      | Data daty ostatniego punktu odzyskiwania elementu kopii zapasowej   |
| ZasadaUniqueId                    | Tekst          | Unikatowy identyfikator w celu zidentyfikowania zasad                             |
| ProtectedContainerFriendlyName    | Tekst          | Przyjazna nazwa chronionego serwera                        |
| ChronioneZamieszczacz Lokalizacja        | Tekst          | Czy kontener chroniony znajduje się lokalnie, czy na platformie Azure |
| Nazwa protectedContainerName            | Tekst          | Nazwa chronionego kontenera                            |
| ProtectedContainerOSType          | Tekst          | Typ systemu operacyjnego chronionego kontenera                          |
| ProtectedContainerOSVersion       | Tekst          | Wersja chronionego kontenera z systemami operacyjnymi                        |
| ProtectedContainerProtectionState ProtecttectionState ProtecttectionState ProtectedContainerProtectionState Protect | Tekst          | Stan ochrony chronionego kontenera                  |
| ProtectedContainerType            | Tekst          | Czy kontener chroniony jest serwerem, czy kontenerem  |
| ProtectedContainerUniqueId        | Tekst          | Unikatowy identyfikator używany do identyfikowania chronionego kontenera dla wszystkiego, z wyjątkiem maszyn wirtualnych kopii zapasowych przy użyciu programu DPM, MABS |
| ProtectedContainerWorkloadType    | Tekst          | Typ kopii zapasowej kontenera chronionego. Na przykład IaaSVMContainer |
| ProtectionGroupName               | Tekst          | Nazwa grupy ochrony, w przypadku gdy znajduje się element kopii zapasowej, jest chroniona w systemach SC DPM i MABS, jeśli dotyczy |
| ResourceGroupName                 | Tekst          | Grupa zasobów zasobu (na przykład magazyn usług odzyskiwania) dla gromadzonych danych |
| Wersja schematu                     | Tekst          | To pole oznacza bieżącą wersję schematu, jest to **V2** |
| SecondaryBackupProtectionState (Stan ochrony wtórnej)    | Tekst          | Czy ochrona pomocnicza jest włączona dla elementu kopii zapasowej  |
| Stan                             | Tekst          | Stan obiektu elementu kopii zapasowej. Na przykład Aktywne, Usunięte |
| Typ ponownego rozłożenia magazynu            | Tekst          | Typ replikacji magazynu dla przechowalni. Na przykład GeoRedundant |
| SubscriptionId                    | Tekst          | Identyfikator subskrypcji zasobu (na przykład magazyn usług odzyskiwania), dla którego zbierane są dane |
| Nazwa krypty                         | Tekst          | Nazwa magazynu                                            |
| Tagi VaultTags                         | Tekst          | Znaczniki skojarzone z zasobem przechowalni                    |
| VaultUniqueId (Nieumieja)                     | Tekst          | Unikatowy identyfikator przechowalni                             |
| SourceSystem                      | Tekst          | System źródłowy bieżących danych — Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Ta tabela zawiera szczegółowe informacje o polach związanych z alertami.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Unikatowy identyfikator zasobu, o którym zbierane są dane. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| OperationName                  | Tekst          | Nazwa bieżącej operacji. Na przykład alert            |
| Kategoria                       | Tekst          | Kategoria danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor — AddonAzureBackupAlerts |
| Kod alertu                      | Tekst          | Kod umożliwiający jednoznaczną identyfikację typu alertu                     |
| AlertConsolidationStatus       | Tekst          | Określenie, czy alert jest alertem skonsolidowanym, czy nie         |
| AlertOccurrenceDateTime        | DateTime      | Data i godzina utworzenia alertu                     |
| AlertRaisedOn                  | Tekst          | Typ jednostki, na której alert jest wywoływany                        |
| AlertEverity (Zawsze alerty)                  | Tekst          | Ważność alertu. Na przykład krytyczne                 |
| Stan alertów                    | Tekst          | Stan alertu. Na przykład aktywne                     |
| AlertTimeToResolveInMinutes    | Liczba        | Czas pracy w celu rozwiązania alertu. Puste dla aktywnych alertów.     |
| Typ alertu                      | Tekst          | Typ alertu. Na przykład kopia zapasowa                           |
| AlertUniqueId                  | Tekst          | Unikatowy identyfikator wygenerowanego alertu                    |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| BackupManagementServerUniqueId | Tekst          | Pole umożliwiające jednoznaczną identyfikację serwera zarządzania zapasami kopii zapasowych, za pomocą którego chroniony jest element kopii zapasowej, jeśli ma to zastosowanie |
| Rodzaj y manania kopii zapasowej           | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| LiczbaAlertsSkonsolidowany      | Liczba        | Liczba wpisów skonsolidowanych, jeśli jest to alert skonsolidowany  |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator chronionego serwera skojarzonego z alertem |
| Zalecanaakcja              | Tekst          | Zalecane działanie w celu rozwiązania problemu                      |
| Wersja schematu                  | Tekst          | Bieżąca wersja schematu, na przykład **V2**            |
| Stan                          | Tekst          | Bieżący stan obiektu alertu, na przykład Active, Deleted |
| DyskUniqueId                | Tekst          | Unikatowy identyfikator używany do identyfikowania jednostki magazynu                |
| VaultUniqueId (Nieumieja)                  | Tekst          | Unikatowy identyfikator używany do identyfikowania przechowalni związanej z alertem    |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Ta tabela zawiera podstawowe pola związane z wystąpieniami chronionymi.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Unikatowy identyfikator zasobu, o którym zbierane są dane. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| OperationName                  | Tekst          | Nazwa operacji, na przykład ProtectedInstance         |
| Kategoria                       | Tekst          | Kategoria danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor — AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator elementu kopii zapasowej                                 |
| BackupManagementServerUniqueId | Tekst          | Pole umożliwiające jednoznaczną identyfikację serwera zarządzania zapasami kopii zapasowych, za pomocą którego chroniony jest element kopii zapasowej, jeśli ma to zastosowanie |
| Rodzaj y manania kopii zapasowej           | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowych, na przykład IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator umożliwiający identyfikację chronionego kontenera, na który jest uruchamiane zadanie |
| Licznik protectedInstance         | Tekst          | Liczba wystąpień chronionych dla skojarzonego elementu kopii zapasowej lub chronionego kontenera w tym dniu i godzinie |
| Wersja schematu                  | Tekst          | Bieżąca wersja schematu, na przykład **V2**            |
| Stan                          | Tekst          | Stan obiektu elementu kopii zapasowej, na przykład Aktywny, Usunięty |
| VaultUniqueId (Nieumieja)                  | Tekst          | Unikatowy identyfikator chronionego magazynu skojarzonego z chronionym wystąpieniem |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Ta tabela zawiera szczegółowe informacje o polach związanych z zadaniami.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| OperationName                  | Tekst          | To pole reprezentuje nazwę bieżącej operacji — Zadanie    |
| Kategoria                       | Tekst          | To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników usługi Azure Monitor — AddonAzureBackupJobs |
| AdhocOrScheduledOb            | Tekst          | Pole do określenia, czy zadanie ma ad hoc czy zaplanowane           |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej powiązanego z encją magazynu |
| BackupManagementServerUniqueId | Tekst          | Unikatowy identyfikator używany do identyfikowania serwera zarządzania kopiami zapasowymi powiązanego z encją magazynu |
| Rodzaj y manania kopii zapasowej           | Tekst          | Typ dostawcy do wykonywania kopii zapasowych, na przykład IaaSVM, FileFolder, do którego należy ten alert |
| DataTransferredInMB            | Liczba        | Dane przesyłane w MB dla tego zadania                          |
| JobDurationInSecs              | Liczba        | Całkowity czas trwania zadania w sekundach                                |
| Kod JobFailureCode                 | Tekst          | Ciąg kodu awarii z powodu niepowodzenia zadania    |
| Działanie zadania                   | Tekst          | Operacja, dla której zadanie jest uruchamiane, na przykład Kopia zapasowa, Przywracanie, Konfigurowanie kopii zapasowej |
| Typ operacji zlecenia            | Tekst          | Podtypu operacji zadania. Na przykład "Log", w przypadku zadania tworzenia kopii zapasowej dziennika |
| Czas rozpoczęcia zadania               | DateTime      | Data i godzina rozpoczęcia pracy zadania                       |
| JobStatus                      | Tekst          | Stan gotowego zadania, na przykład Zakończone, Nie powiodło się   |
| JobUniqueId (JobUniqueId)                    | Tekst          | Unikatowy identyfikator umożliwiający identyfikację zadania                                |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator chronionego serwera skojarzonego z alertem |
| RecoveryJobDestination         | Tekst          | Miejsce docelowe zadania odzyskiwania, w którym dane są odzyskiwane   |
| OdzyskiwanieJobRPDateTime          | DateTime      | Data, godzina utworzenia punktu odzyskiwania, który jest odzyskiwany, została utworzona |
| RecoveryJobLokacja            | Tekst          | Miejsce przechowywania punktu odzyskiwania, który jest odzyskiwany |
| Rodzaj lokalizacji odzyskiwania           | Tekst          | Typ lokalizacji odzyskiwania                                |
| Wersja schematu                  | Tekst          | Bieżąca wersja schematu, na przykład **V2**            |
| Stan                          | Tekst          | Bieżący stan obiektu alertu, na przykład Active, Deleted |
| VaultUniqueId (Nieumieja)                  | Tekst          | Unikatowy identyfikator chronionego magazynu skojarzonego z alertem |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolityka

Ta tabela zawiera szczegółowe informacje o polach związanych z zasadami.

| **Pole**                       | **Typ danych**  | **Opis**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Tekst           | Unikatowy identyfikator zasobu, o którym zbierane są dane. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| OperationName                   | Tekst           | Nazwa operacji, na przykład, Policy lub PolicyAssociation |
| Kategoria                        | Tekst           | Kategoria danych diagnostycznych wypchniętych do dzienników usługi Azure Monitor — AddonAzureBackupPolicy |
| Dni kopii zapasowejOfTheWeek             | Tekst           | Dni tygodnia, w których zaplanowano tworzenie kopii zapasowych            |
| Częstotliwość tworzenia kopii zapasowych                 | Tekst           | Częstotliwość, z jaką są uruchamiane kopie zapasowe. Na przykład codziennie, co tydzień |
| Rodzaj y manania kopii zapasowej            | Tekst           | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej. Na przykład IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Tekst           | Pole umożliwiające jednoznaczną identyfikację serwera zarządzania zapasami kopii zapasowych, za pomocą którego chroniony jest element kopii zapasowej, jeśli ma to zastosowanie |
| Czas tworzenia kopii zapasowych                     | Tekst           | Data i godzina planowania kopii zapasowych                     |
| DailyRetentionDuration          | Liczbowy całkowity   | Całkowity czas przechowywania w dniach skonfigurowanych kopii zapasowych      |
| DailyRetentionCzasy             | Tekst           | Data i godzina skonfigurowania dziennego przechowywania            |
| DiffBackupDaysOfTheWeek         | Tekst           | Dni tygodnia dla różnicowych kopii zapasowych dla sql w kopii zapasowej maszyny Wirtualnej platformy Azure |
| DiffBackupFormat                | Tekst           | Format dla różnicowych kopii zapasowych dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure   |
| DiffBackupRetentionDuration     | Liczba dziesiętna | Czas przechowywania różnicowych kopii zapasowych dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure |
| DiffBackupCzas                  | Time           | Czas na różnicowe kopie zapasowe dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure     |
| Częstotliwość logbackup              | Liczba dziesiętna | Częstotliwość tworzenia kopii zapasowych dziennika dla języka SQL                            |
| LogBackupRetentionDuration      | Liczba dziesiętna | Czas przechowywania kopii zapasowych dziennika dla języka SQL w kopii zapasowej maszyny Wirtualnej platformy Azure |
| Miesięczne DniRetentionaOfTheMonth  | Tekst           | Tygodnie miesiąca, w którym skonfigurowano miesięczne przechowywanie.  Na przykład, Pierwszy, Ostatni, itp. |
| MiesięczneRetentionDaysOfTheWeek   | Tekst           | Dni tygodnia wybrane do miesięcznego przechowywania              |
| MiesięczneRetentionDuration        | Tekst           | Całkowity czas przechowywania w miesiącach dla skonfigurowanych kopii zapasowych    |
| MiesięczneFormatretention          | Tekst           | Typ konfiguracji dla miesięcznego przechowywania. Na przykład, codziennie w przypadku dni, co tydzień w tygodniu |
| Miesięczne czasy dotłamności           | Tekst           | Data i godzina skonfigurowania miesięcznego przechowywania           |
| Miesięczne TydzieńyRetentsOfTheMonth | Tekst           | Tygodnie miesiąca, w którym skonfigurowano miesięczne przechowywanie.   Na przykład, Pierwszy, Ostatni, itp. |
| PolicyName                      | Tekst           | Nazwa zdefiniowanej zasady                                   |
| ZasadaUniqueId                  | Tekst           | Unikatowy identyfikator w celu zidentyfikowania zasad                             |
| Strefa czasu polityki                  | Tekst           | Strefa czasowa, w której pola czasu zasad są określone w dziennikach |
| RetencjaDuration               | Tekst           | Czas przechowywania skonfigurowanych kopii zapasowych                    |
| Rodzaj retencji                   | Tekst           | Rodzaj retencji                                            |
| Wersja schematu                   | Tekst           | To pole oznacza bieżącą wersję schematu, jest to **V2** |
| Stan                           | Tekst           | Bieżący stan obiektu zasad. Na przykład Aktywne, Usunięte |
| SynchronizacjaFrequencyPerDay  | Liczbowy całkowity   | Liczba zsynchronizowanych kopii zapasowych plików w ciągu dnia dla programów SC DPM i MABS |
| VaultUniqueId (Nieumieja)                   | Tekst           | Unikatowy identyfikator przechowalni, do której należy ta zasada          |
| TygodnikRetentionDaysOfTheWeek    | Tekst           | Dni tygodnia wybrane do tygodniowego przechowywania               |
| TygodnikRetentionDuration         | Liczba dziesiętna | Całkowity tygodniowy czas przechowywania w tygodniach dla skonfigurowanych kopii zapasowych |
| Tygodniowe Czasy            | Tekst           | Data i godzina skonfigurowania tygodniowego przechowywania            |
| RokretentionDaysOfTheMonth   | Tekst           | Daty miesiąca wybranego do rocznego dokowej             |
| RokretentionDaysOfTheWeek    | Tekst           | Dni tygodnia wybrane do rocznego przechowywania               |
| RoczneretentionDuration         | Liczba dziesiętna | Całkowity czas przechowywania w latach dla skonfigurowanych kopii zapasowych     |
| RoczneRetentionFormat           | Tekst           | Typ konfiguracji dla letniego przechowywania, na przykład, codziennie w przypadku dnia, co tydzień na podstawie |
| RoczneRetentionMonthsOfTheYear  | Tekst           | Miesiące roku wybrane do rocznego przechowywania             |
| Roczneczasy            | Tekst           | Data i godzina skonfigurowania letniego przechowywania            |
| RoczneRetentionTytyOfMonth  | Tekst           | Tygodnie miesiąca wybrane do rocznego do letniego przechowywania             |
| SourceSystem                    | Tekst           | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynowaniem.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Identyfikator zasobu dla gromadzonych danych. Na przykład identyfikator zasobu magazynu usług odzyskiwania |
| OperationName                  | Tekst          | To pole reprezentuje nazwę bieżącej operacji — Siekanie magazynu lub magazynu |
| Kategoria                       | Tekst          | To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników usługi Azure Monitor — AddonAzureBackupStorage |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej maszyn wirtualnych kopii zapasowej przy użyciu programu DPM, MABS |
| BackupManagementServerUniqueId | Tekst          | Pole umożliwiające jednoznaczną identyfikację serwera zarządzania zapasami kopii zapasowych, za pomocą którego chroniony jest element kopii zapasowej, jeśli ma to zastosowanie |
| Rodzaj y manania kopii zapasowej           | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej. Na przykład IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | Tekst          | Obciążenie, dla którego ten wolumin jest preferowanym magazynem      |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator chronionego serwera skojarzonego z alertem |
| Wersja schematu                  | Tekst          | Wersja schematu. Na przykład **v2**                   |
| Stan                          | Tekst          | Stan obiektu elementu kopii zapasowej. Na przykład Aktywne, Usunięte |
| MagazynallocatedInmBs          | Liczba        | Rozmiar miejsca do magazynowania przydzielonego przez odpowiedni element kopii zapasowej w odpowiednim magazynie typu Dysk |
| MagazynyConsumedInmBs           | Liczba        | Rozmiar magazynu zużywanego przez odpowiedni element kopii zapasowej w odpowiednim magazynie |
| Nazwa magazynu                    | Tekst          | Nazwa jednostki magazynu. Na przykład E:\                      |
| StorageTotalSizeInGBs          | Tekst          | Całkowity rozmiar magazynu w GB, zużywany przez encję magazynu     |
| Rodzaj magazynu                    | Tekst          | Typ pamięci masowej, na przykład chmura, wolumin, dysk             |
| DyskUniqueId                | Tekst          | Unikatowy identyfikator używany do identyfikowania jednostki magazynu                |
| VaultUniqueId (Nieumieja)                  | Tekst          | Unikatowy identyfikator używany do identyfikowania magazynu związanego z encją magazynu |
| Nazwa woluminu             | Tekst          | Przyjazna nazwa woluminu pamięci masowej                          |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak wysyłać dane diagnostyczne do usługi Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Dowiedz się, jak zapisywać kwerendy w tabelach specyficznych dla zasobów](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)