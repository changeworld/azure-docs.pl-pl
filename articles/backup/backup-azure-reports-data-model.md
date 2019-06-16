---
title: Model danych do usługi Azure Backup
description: Ten artykuł zawiera informacje o szczegóły modelu danych usługi Power BI dla raportów usługi Azure Backup.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: c6160570644da108ba713e8229b38f9587495c92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337582"
---
# <a name="data-model-for-azure-backup-reports"></a>Model danych dla raportów usługi Azure Backup
W tym artykule opisano model danych usługi Power BI, używany do tworzenia raportów usługi Azure Backup. Przy użyciu tego modelu danych, można filtrować istniejące raporty na podstawie odpowiednich pól i więcej co ważniejsze, tworzyć własne raporty za pomocą tabel i pól w modelu. 

## <a name="creating-new-reports-in-power-bi"></a>Tworzenie nowych raportów w usłudze Power BI
Usługa Power BI udostępnia funkcje dostosowywania za pomocą którego możesz [tworzenie raportów przy użyciu modelu danych](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Przy użyciu modelu danych usługi Azure Backup
Aby tworzyć raporty i dostosowywanie istniejących raportów, można użyć następujących pól, które są udostępniane w ramach modelu danych.

### <a name="alert"></a>Alerty
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól powiązanych alertów.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Liczba całkowita |Liczba alertów utworzone w wybranym okresie |
| %ActiveAlertsCreatedInPeriod |Wartość procentowa |Odsetek aktywnych alertów w wybranym okresie |
| %CriticalAlertsCreatedInPeriod |Wartość procentowa |Wartość procentowa alerty krytyczne w wybranym okresie |
| AlertOccurrenceDate |Date |Data utworzenia alertu |
| AlertSeverity |Text |Ważność alertu, na przykład, krytyczny |
| AlertStatus |Text |Stan alertu, na przykład aktywny |
| AlertType |Text |Typ wygenerowany alert, na przykład kopię zapasową |
| AlertUniqueId |Text |Unikatowy identyfikator wygenerowany alert |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Liczba dziesiętna |Średni czas (w minutach), aby usunąć alert dla wybranego okresu |
| EntityState |Text |Bieżący stan alertu obiektu, na przykład, aktywne, usunięte |

### <a name="backup-item"></a>Element kopii zapasowej
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych kopii zapasowych pól powiązanych elementów.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #BackupItems |Liczba całkowita |Liczba elementów kopii zapasowej |
| #UnprotectedBackupItems |Liczba całkowita |Liczba elementów kopii zapasowej zatrzymanie ochrony lub skonfigurowane dla kopii zapasowych, ale nie uruchomić tworzenie kopii zapasowych|
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| BackupItemFriendlyName |Text |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemId |Text |Identyfikator elementu kopii zapasowej |
| BackupItemName |Text |Nazwa elementu kopii zapasowej |
| BackupItemType |Text |Typ elementu kopii zapasowej na przykład maszyna wirtualna FileFolder |
| EntityState |Text |Bieżący stan obiektu elementu kopii zapasowej, na przykład, aktywne, usunięte |
| LastBackupDateTime |Data/godzina |Czas ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej |
| LastBackupState |Text |Stan ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej, na przykład, Powodzenie, Niepowodzenie |
| LastSuccessfulBackupDateTime |Data/godzina |Czas ostatniego pomyślnego tworzenia kopii zapasowej dla wybranego elementu kopii zapasowej |
| ProtectionState |Text |Bieżący stan ochrony w element kopii zapasowej, na przykład chronione, ProtectionStopped |

### <a name="calendar"></a>Kalendarz
Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z kalendarza.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Date |Date |Data wybrana do filtrowania danych |
| DateKey |Text |Unikatowy klucz dla każdego elementu daty |
| DayDiff |Liczba dziesiętna |Różnica w ciągu dnia filtrowania danych, na przykład, 0 oznacza dane w bieżącym dniu, -1 oznacza danych poprzedniego jednego dnia, 0 i -1 wskazuje danych dla bieżącego i poprzedniego dnia  |
| Miesiąc |Text |Miesiąc roku wybrane do filtrowania danych, rozpoczyna się pierwszego dnia miesiąca i kończy się 31 dni |
| MonthDate | Date |Data w ciągu miesiąca, kiedy kończy się miesiąca wybrane do filtrowania danych |
| MonthDiff |Liczba dziesiętna |Różnica w miesiącu, w przypadku filtrowania danych, na przykład, 0 oznacza dane z bieżącego miesiąca, -1 oznacza danych z poprzedniego miesiąca, 0 i -1 wskazuje danych w bieżącym i poprzednim miesiącu. |
| Tydzień |Text |Tydzień wybrane do filtrowania danych, tydzień zaczyna się od niedzieli, a kończy się w sobotę |
| WeekDate |Date |Data w tydzień po tygodniu zostaje zakończona, wybrać filtrowanie danych |
| WeekDiff |Liczba dziesiętna |Różnica w tygodniu do filtrowania danych, na przykład, wartość 0 wskazuje danych w bieżącym tygodniu, -1 oznacza danych z poprzedniego tygodnia, 0 i -1 wskazuje danych dla bieżącego i poprzedniego tygodnia |
| Rok |Text |Rok kalendarzowy wybrane do filtrowania danych |
| YearDate |Date |Data w roku, kiedy kończy się roku, wybrać filtrowanie danych |

### <a name="job"></a>Zadanie
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól związanych z pracą.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Liczba całkowita |Liczba zadań utworzonych w wybranym okresie |
| %FailuresForJobsCreatedInPeriod |Wartość procentowa |Procent ogólnej zadaniami w wybranym okresie |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Liczba dziesiętna |80\. wartość percentylu danych przesyłanych w Megabajtach **kopii zapasowej** zadań utworzonych w wybranym okresie |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach, aby **Zakończono tworzenie kopii zapasowej** zadań utworzonych w wybranym okresie |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach, aby **ukończyć przywracania** zadań utworzonych w wybranym okresie |
| BackupStorageDestination |Text |Docelowy magazyn kopii zapasowych na przykład chmura dysku  |
| EntityState |Text |Bieżący stan obiektu zadania, na przykład, aktywne, usunięte |
| JobFailureCode |Text |Z powodu którego wystąpiło niepowodzenie zadania ciągu kodu błędu |
| JobOperation |Text |Operacja, dla którego zadanie jest uruchamiane na przykład kopii zapasowej, przywracanie, skonfiguruj w kopii zapasowej |
| JobStartDate |Date |Data, gdy zadanie rozpoczęcia działania |
| JobStartTime |Time |Gdy zadanie rozpoczęcia działania |
| JobStatus |Text |Stan zadania zakończone na przykład, zakończone, nie powiodło się |
| JobUniqueId |Text |Unikatowy identyfikator zadania |

### <a name="policy"></a>Zasady
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych zasad dotyczących pól.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Policies |Liczba całkowita |Liczba zasad tworzenia kopii zapasowych, które istnieją w systemie |
| #PoliciesInUse |Liczba całkowita |Limit liczby zasad, które są obecnie używane do konfigurowania tworzenia kopii zapasowych |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| BackupDaysOfTheWeek |Text |Dni tygodnia, gdy zostały zaplanowane kopie zapasowe |
| BackupFrequency |Text |Częstotliwość, z którym są wykonywane kopie zapasowe na przykład, codziennie, co tydzień |
| BackupTimes |Text |Data i godzina, kiedy są zaplanowane kopie zapasowe |
| DailyRetentionDuration |Liczba całkowita |Czas trwania przechowywania całkowitej dni skonfigurowane kopie zapasowe |
| DailyRetentionTimes |Text |Data i godzina, gdy skonfigurowano przechowywanie codziennego |
| EntityState |Text |Bieżący stan obiektu zasad, na przykład, aktywne, usunięte |
| MonthlyRetentionDaysOfTheMonth |Text |Daty, miesiąca, wybrane do przechowywania miesięczne |
| MonthlyRetentionDaysOfTheWeek |Text |Dni tygodnia wybrane do przechowywania miesięczne |
| MonthlyRetentionDuration |Liczba dziesiętna |Czas trwania przechowywania całkowitej miesiące w przypadku skonfigurowane kopie zapasowe |
| MonthlyRetentionFormat |Text |Typ konfiguracji do przechowywania miesięczne na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| MonthlyRetentionTimes |Text |Data i godzina, po skonfigurowaniu przechowywanie miesięcznego |
| MonthlyRetentionWeeksOfTheMonth |Text |Tygodnie miesiąca, gdy jest przechowywanie miesięcznego na przykład skonfigurować pierwszy i ostatni itp. |
| PolicyName |Text |Nazwy zdefiniowane zasady |
| PolicyUniqueId |Text |Unikatowy identyfikator zasad |
| RetentionType |Text |Typ zasady przechowywania na przykład codziennie, co tydzień, co miesiąc, co rok |
| WeeklyRetentionDaysOfTheWeek |Text |Dni tygodnia wybrane do przechowywania danych co tydzień |
| WeeklyRetentionDuration |Liczba dziesiętna |Łączny czas przechowywania co tydzień, w ciągu tygodni skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes |Text |Data i godzina, jeśli skonfigurowano przechowywanie tygodniowego |
| YearlyRetentionDaysOfTheMonth |Text |Daty, miesiąca, wybrane do przechowywania roczne |
| YearlyRetentionDaysOfTheWeek |Text |Dni tygodnia wybrane do przechowywania roczne |
| YearlyRetentionDuration |Liczba dziesiętna |Czas trwania przechowywania całkowitej lat skonfigurowane kopie zapasowe |
| YearlyRetentionFormat |Text |Typ konfiguracji do przechowywania roczne na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| YearlyRetentionMonthsOfTheYear |Text |Miesiące roku wybrane do przechowywania roczne |
| YearlyRetentionTimes |Text |Data i godzina, po skonfigurowaniu roczne przechowywanie |
| YearlyRetentionWeeksOfTheMonth |Text |Tygodnie miesiąca, gdy jest roczne przechowywanie na przykład skonfigurować pierwszy i ostatni itp. |

### <a name="protected-server"></a>Chroniony serwer
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych chronionych polach związanych z serwerem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedServers |Liczba całkowita |Liczba serwerów chronionych |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AzureBackupAgentOSType |Text |Typ systemu operacyjnego agenta usługi Azure Backup |
| AzureBackupAgentOSVersion |Text |Wersja systemu operacyjnego agenta usługi Azure Backup |
| AzureBackupAgentUpdateDate |Text |Data aktualizacji agenta usługi Kopia zapasowa agenta |
| AzureBackupAgentVersion |Text |Numer wersji agenta w wersji kopii zapasowej |
| BackupManagementType |Text |Typ dostawcy do wykonywania kopii zapasowej na przykład IaaSVM FileFolder |
| EntityState |Text |Bieżący stan obiektu chronionego serwera, na przykład, aktywne, usunięte |
| ProtectedServerFriendlyName |Text |Przyjazna nazwa chronionego serwera |
| ProtectedServerName |Text |Nazwa chronionego serwera |
| ProtectedServerType |Text |Typ serwera chronionego kopii zapasowej na przykład IaaSVMContainer |
| ProtectedServerName |Text |Nazwa chronionego serwera, do którego element kopii zapasowej należy |
| RegisteredContainerId |Text |Identyfikator kontenera zarejestrowany dla kopii zapasowej |

### <a name="storage"></a>Magazyn
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól związane z magazynowaniem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedInstances |Liczba dziesiętna |Liczba chronionych wystąpień służące do obliczania magazynu frontonu w rozliczeń, obliczonej na podstawie wartości najnowsze w zaznaczony czas |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| CloudStorageInMB |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, które są obliczane na podstawie najnowszej wartości w wybranym |
| EntityState |Text |Bieżący stan obiektu, na przykład, aktywne, usunięte |
| LastUpdatedDate |Date |Data ostatniej aktualizacji wybranego wiersza |

### <a name="time"></a>Time
Ta tabela zawiera szczegółowe informacje o polach związanych z czasem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Godzina |Time |Godziny dnia, na przykład 1:00:00 PM |
| HourNumber |Liczba dziesiętna |Liczba godzin w ciągu dnia, na przykład 13,00 |
| Minuta |Liczba dziesiętna |Minuta |
| PeriodOfTheDay |Text |Okres okna czasowego w ciągu dnia, na przykład 12-3 AM |
| Time |Time |Pora dnia na przykład 0:00:01: 00 |
| TimeKey |Text |Wartość klucza do reprezentowania czasu |

### <a name="vault"></a>Magazyn
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól powiązanych z magazynu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Vaults |Liczba całkowita |Liczba magazynów |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AzureDataCenter |Text |Centrum danych, w którym znajduje się magazyn |
| EntityState |Text |Bieżący stan obiektu magazynu, na przykład, aktywne, usunięte |
| StorageReplicationType |Text |Typ replikacji magazynu dla magazynu, na przykład GeoRedundant |
| SubscriptionId |Text |Identyfikator subskrypcji klienta wybranego do generowania raportów |
| VaultName |Text |Nazwa magazynu |
| VaultTags |Text |Znaczniki skojarzone w magazynie |

## <a name="next-steps"></a>Kolejne kroki
Po przejrzeniu modelu danych do tworzenia raportów usługi Azure Backup, zapoznaj się następujące artykuły, aby uzyskać więcej informacji na temat tworzenia i wyświetlania raportów w usłudze Power BI.

* [Tworzenie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrowanie raportów usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
