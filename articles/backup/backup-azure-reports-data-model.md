---
title: Model danych dla Azure Backup
description: W tym artykule omówiono Power BI szczegóły modelu danych dla Azure Backup raportów.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: 53051c88d5723f61fa08e431bdf7ab531e88123e
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689148"
---
# <a name="data-model-for-azure-backup-reports"></a>Model danych dla raportów usługi Azure Backup
W tym artykule opisano Power BI model danych używany do tworzenia raportów Azure Backupowych. Korzystając z tego modelu danych, można filtrować istniejące raporty na podstawie odpowiednich pól i co ważniejsze, tworzyć własne raporty przy użyciu tabel i pól w modelu. 

## <a name="creating-new-reports-in-power-bi"></a>Tworzenie nowych raportów w Power BI
Power BI udostępnia funkcje dostosowywania, za pomocą których można [tworzyć raporty przy użyciu modelu danych](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Korzystanie z Azure Backup model danych
Do tworzenia raportów i dostosowywania istniejących raportów można użyć poniższych pól dostępnych jako część modelu danych.

### <a name="alert"></a>Alerty
Ta tabela zawiera podstawowe pola i agregacje dla różnych pól związanych z alertami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Liczba całkowita |Liczba alertów utworzonych w wybranym okresie |
| %ActiveAlertsCreatedInPeriod |Procentowy |Procent aktywnych alertów w wybranym okresie |
| %CriticalAlertsCreatedInPeriod |Procentowy |Procent alertów krytycznych w wybranym okresie |
| AlertOccurrenceDate |Date |Data utworzenia alertu |
| AlertSeverity |Text |Ważność alertu, na przykład krytyczne |
| AlertStatus |Text |Stan alertu na przykład aktywny |
| AlertType |Text |Typ wygenerowanego alertu, na przykład kopia zapasowa |
| AlertUniqueId |Text |Unikatowy identyfikator wygenerowanego alertu |
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Liczba dziesiętna |Średni czas (w minutach) rozwiązywania alertu w wybranym okresie |
| EntityState |Text |Bieżący stan obiektu alertu na przykład aktywny, usunięty |

### <a name="backup-item"></a>Element kopii zapasowej
Ta tabela zawiera podstawowe pola i agregacje na różne pola powiązane z elementem kopii zapasowej.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #BackupItems |Liczba całkowita |Liczba elementów kopii zapasowej |
| #UnprotectedBackupItems |Liczba całkowita |Liczba elementów kopii zapasowych zatrzymanych na potrzeby ochrony lub skonfigurowanych na potrzeby wykonywania kopii zapasowych, ale nie uruchomiono|
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| BackupItemFriendlyName |Text |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemId |Text |Identyfikator elementu kopii zapasowej |
| BackupItemName |Text |Nazwa elementu kopii zapasowej |
| BackupItemType |Text |Typ elementu kopii zapasowej na przykład VM, FileFolder |
| EntityState |Text |Bieżący stan obiektu elementu kopii zapasowej na przykład, aktywny, usunięty |
| LastBackupDateTime |Date/Time |Godzina ostatniej kopii zapasowej wybranego elementu kopii zapasowej |
| LastBackupState |Text |Stan ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej, na przykład pomyślne, Niepowodzenie |
| LastSuccessfulBackupDateTime |Date/Time |Godzina ostatniej pomyślnej kopii zapasowej wybranego elementu kopii zapasowej |
| ProtectionState |Text |Bieżący stan ochrony elementu kopii zapasowej na przykład chronione, ProtectionStopped |

### <a name="calendar"></a>Kalendarz
Ta tabela zawiera szczegółowe informacje o polach związanych z kalendarzem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Date |Date |Data wybrana do filtrowania danych |
| DateKey |Text |Unikatowy klucz dla każdego elementu daty |
| DayDiff |Liczba dziesiętna |Różnica w dniu filtrowania danych na przykład 0 oznacza dane bieżącego dnia,-1 oznacza poprzednie dane z jednego dnia, 0 i-1 oznacza dane dla bieżącego i poprzedniego dnia  |
| Miesiąc |Text |Miesiąc roku wybranego do filtrowania danych, miesiąc zaczyna się pierwszego dnia i kończą się 31 dniem |
| MonthDate | Date |Data w miesiącu, w którym następuje zakończenie miesiąca, wybrana do filtrowania danych |
| MonthDiff |Liczba dziesiętna |Różnica w miesiącu na filtrowanie danych na przykład, 0 oznacza dane bieżącego miesiąca,-1 oznacza dane poprzedniego miesiąca, 0 i-1 oznacza dane dla bieżącego i poprzedniego miesiąca |
| Tydzień |Text |Tydzień wybrany do filtrowania danych, tydzień zaczyna się w niedzielę i kończący się w sobotę |
| WeekDate |Date |Data w tygodniu, w którym następuje koniec tygodnia, wybrana do filtrowania danych |
| WeekDiff |Liczba dziesiętna |Różnica w tygodniu do filtrowania danych na przykład, 0 oznacza dane bieżącego tygodnia,-1 oznacza dane z poprzedniego tygodnia, 0 i-1 oznacza dane dla bieżącego i poprzedniego tygodnia |
| Rok |Text |Rok kalendarzowy wybrany do filtrowania danych |
| YearDate |Date |Data w roku, w którym następuje zakończenie roku, wybrana do filtrowania danych |

### <a name="job"></a>Zadanie
Ta tabela zawiera podstawowe pola i agregacje dla różnych pól związanych z zadaniami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Liczba całkowita |Liczba zadań utworzonych w wybranym okresie |
| %FailuresForJobsCreatedInPeriod |Procentowy |Procent ogólnych błędów zadań w wybranym okresie |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Liczba dziesiętna |wartość percentylu 80th danych transferowanych w MB dla zadań **tworzenia kopii zapasowej** utworzonych w wybranym okresie |
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach dla **ukończonych zadań tworzenia kopii zapasowej** utworzonych w wybranym okresie |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach dla **ukończonych zadań przywracania** utworzonych w wybranym okresie |
| BackupStorageDestination |Text |Miejsce docelowe magazynu kopii zapasowych na przykład Cloud, Disk  |
| EntityState |Text |Bieżący stan obiektu zadania na przykład aktywny, usunięty |
| JobFailureCode |Text |Ciąg kodu błędu z powodu niepowodzenia zadania |
| JobOperation |Text |Operacja, dla której zadanie jest uruchamiane na przykład kopia zapasowa, przywracanie, konfigurowanie kopii zapasowej |
| JobStartDate |Date |Data uruchomienia zadania |
| JobStartTime |Time |Czas uruchomienia zadania |
| JobStatus |Text |Stan ukończonego zadania, na przykład zakończone, Niepowodzenie |
| JobUniqueId |Text |Unikatowy identyfikator identyfikujący zadanie |

### <a name="policy"></a>Zasady
Ta tabela zawiera podstawowe pola i agregacje dla różnych pól związanych z zasadami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Policies |Liczba całkowita |Liczba zasad tworzenia kopii zapasowych, które istnieją w systemie |
| #PoliciesInUse |Liczba całkowita |Liczba zasad, które są obecnie używane do konfigurowania kopii zapasowych |
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| BackupDaysOfTheWeek |Text |Dni tygodnia, w których zaplanowano tworzenie kopii zapasowych |
| BackupFrequency |Text |Częstotliwość, z jaką kopie zapasowe są uruchamiane na przykład codziennie, co tydzień |
| Przestoje |Text |Data i godzina zaplanowanego tworzenia kopii zapasowych |
| DailyRetentionDuration |Liczba całkowita |Łączny czas przechowywania w dniach dla skonfigurowanych kopii zapasowych |
| DailyRetentionTimes |Text |Data i godzina skonfigurowania codziennego przechowywania |
| EntityState |Text |Bieżący stan obiektu zasad na przykład aktywny, usunięty |
| MonthlyRetentionDaysOfTheMonth |Text |Daty w miesiącu wybrane do przechowywania miesięcznie |
| MonthlyRetentionDaysOfTheWeek |Text |Dni tygodnia wybrane do przechowywania miesięcznie |
| MonthlyRetentionDuration |Liczba dziesiętna |Łączny czas przechowywania w miesiącach dla skonfigurowanych kopii zapasowych |
| MonthlyRetentionFormat |Text |Typ konfiguracji do przechowywania miesięcznie na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień |
| MonthlyRetentionTimes |Text |Data i godzina skonfigurowania miesięcznego przechowywania |
| MonthlyRetentionWeeksOfTheMonth |Text |Tygodnie miesiąca w przypadku skonfigurowania miesięcznego przechowywania na przykład, First, Last itd. |
| PolicyName |Text |Nazwa zdefiniowanych zasad |
| PolicyUniqueId |Text |Unikatowy identyfikator identyfikujący zasady |
| Czas przechowywania |Text |Typ zasad przechowywania na przykład codziennie, co tydzień, co miesiąc, co rok |
| WeeklyRetentionDaysOfTheWeek |Text |Dni tygodnia wybrane do przechowywania tygodniowego |
| WeeklyRetentionDuration |Liczba dziesiętna |Łączny czas trwania przechowywania w tygodniach dla skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes |Text |Data i godzina skonfigurowania przechowywania tygodniowego |
| YearlyRetentionDaysOfTheMonth |Text |Daty w miesiącu wybrane do przechowywania rocznego |
| YearlyRetentionDaysOfTheWeek |Text |Dni tygodnia wybrane do przechowywania rocznego |
| YearlyRetentionDuration |Liczba dziesiętna |Łączny czas przechowywania w latach dla skonfigurowanych kopii zapasowych |
| YearlyRetentionFormat |Text |Typ konfiguracji do przechowywania rocznego na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień |
| YearlyRetentionMonthsOfTheYear |Text |Miesiąc roku wybranego do przechowywania rocznego |
| YearlyRetentionTimes |Text |Data i godzina skonfigurowania okresu przechowywania rocznego |
| YearlyRetentionWeeksOfTheMonth |Text |Tygodnie miesiąca, w którym jest skonfigurowane okresowe przechowywanie na przykład, First, Last itd. |

### <a name="protected-server"></a>Serwer chroniony
Ta tabela zawiera podstawowe pola i agregacje dla różnych chronionych pól związanych z serwerem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedServers |Liczba całkowita |Liczba chronionych serwerów |
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| AzureBackupAgentOSType |Text |Typ systemu operacyjnego agenta Azure Backup |
| AzureBackupAgentOSVersion |Text |Wersja systemu operacyjnego agenta Azure Backup |
| AzureBackupAgentUpdateDate |Text |Data zaktualizowania agenta kopii zapasowej agenta |
| AzureBackupAgentVersion |Text |Numer wersji kopii zapasowej agenta |
| BackupManagementType |Text |Typ dostawcy służący do wykonywania kopii zapasowych na przykład, IaaSVM, FileFolder |
| EntityState |Text |Bieżący stan obiektu chronionego serwera na przykład aktywny, usunięty |
| ProtectedServerFriendlyName |Text |Przyjazna nazwa serwera chronionego |
| ProtectedServerName |Text |Nazwa chronionego serwera |
| ProtectedServerType |Text |Typ chronionego serwera kopii zapasowej na przykład IaaSVMContainer |
| ProtectedServerName |Text |Nazwa serwera chronionego, do którego należy element kopii zapasowej |
| RegisteredContainerId |Text |Identyfikator kontenera zarejestrowanego dla kopii zapasowej |

### <a name="storage"></a>Magazyn
Ta tabela zawiera podstawowe pola i agregacje dla różnych pól związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedInstances |Liczba dziesiętna |Liczba chronionych wystąpień używanych do obliczenia magazynu frontonu w rozliczeniach obliczona na podstawie wartości najnowszej w wybranym czasie |
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| CloudStorageInMB |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, obliczony na podstawie najnowszej wartości w wybranym czasie |
| EntityState |Text |Bieżący stan obiektu na przykład aktywny, usunięty |
| LastUpdatedDate |Date |Data ostatniej aktualizacji wybranego wiersza |

### <a name="time"></a>Time
Ta tabela zawiera szczegółowe informacje o polach związanych z czasem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Godzina |Time |Godzina dnia na przykład 1:00:00 PM |
| HourNumber |Liczba dziesiętna |Liczba godzin w dniu na przykład 13,00 |
| Minuta |Liczba dziesiętna |Minuta godziny |
| PeriodOfTheDay |Text |Gniazdo okresu w dniu na przykład 12-3 AM |
| Time |Time |Godzina dnia na przykład 12:00:01 AM |
| TimeKey |Text |Wartość klucza reprezentująca czas |

### <a name="vault"></a>Magazyn
Ta tabela zawiera podstawowe pola i agregacje dla różnych pól związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Vaults |Liczba całkowita |Liczba magazynów |
| AsOnDateTime |Date/Time |Godzina ostatniego odświeżenia wybranego wiersza |
| AzureDataCenter |Text |Centrum danych, w którym znajduje się magazyn |
| EntityState |Text |Bieżący stan obiektu magazynu na przykład aktywny, usunięty |
| StorageReplicationType |Text |Typ replikacji magazynu dla magazynu na przykład geonadmiarowości |
| SubscriptionId |Text |Identyfikator subskrypcji klienta wybranego do generowania raportów |
| VaultName |Text |Nazwa magazynu |
| VaultTags |Text |Tagi skojarzone z magazynem |

## <a name="next-steps"></a>Następne kroki
Po przejrzeniu modelu danych do tworzenia raportów Azure Backup należy zapoznać się z następującymi artykułami, aby uzyskać więcej informacji na temat tworzenia i wyświetlania raportów w programie Power BI.

* [Tworzenie raportów w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrowanie raportów w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
