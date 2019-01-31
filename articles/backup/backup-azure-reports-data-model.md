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
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299422"
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
| %ActiveAlertsCreatedInPeriod |Procentowe |Odsetek aktywnych alertów w wybranym okresie |
| %CriticalAlertsCreatedInPeriod |Procentowe |Wartość procentowa alerty krytyczne w wybranym okresie |
| AlertOccurrenceDate |Date |Data utworzenia alertu |
| AlertSeverity |Tekst |Ważność alertu, na przykład, krytyczny |
| AlertStatus |Tekst |Stan alertu, na przykład aktywny |
| AlertType |Tekst |Typ wygenerowany alert, na przykład kopię zapasową |
| AlertUniqueId |Tekst |Unikatowy identyfikator wygenerowany alert |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Liczba dziesiętna |Średni czas (w minutach), aby usunąć alert dla wybranego okresu |
| EntityState |Tekst |Bieżący stan alertu obiektu, na przykład, aktywne, usunięte |

### <a name="backup-item"></a>Element kopii zapasowej
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych kopii zapasowych pól powiązanych elementów.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #BackupItems |Liczba całkowita |Liczba elementów kopii zapasowej |
| #UnprotectedBackupItems |Liczba całkowita |Liczba elementów kopii zapasowej zatrzymanie ochrony lub skonfigurowane dla kopii zapasowych, ale nie uruchomić tworzenie kopii zapasowych|
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| BackupItemFriendlyName |Tekst |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemId |Tekst |Identyfikator elementu kopii zapasowej |
| BackupItemName |Tekst |Nazwa elementu kopii zapasowej |
| BackupItemType |Tekst |Typ elementu kopii zapasowej na przykład maszyna wirtualna FileFolder |
| EntityState |Tekst |Bieżący stan obiektu elementu kopii zapasowej, na przykład, aktywne, usunięte |
| LastBackupDateTime |Data/godzina |Czas ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej |
| LastBackupState |Tekst |Stan ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej, na przykład, Powodzenie, Niepowodzenie |
| LastSuccessfulBackupDateTime |Data/godzina |Czas ostatniego pomyślnego tworzenia kopii zapasowej dla wybranego elementu kopii zapasowej |
| ProtectionState |Tekst |Bieżący stan ochrony w element kopii zapasowej, na przykład chronione, ProtectionStopped |

### <a name="calendar"></a>Kalendarz
Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z kalendarza.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Date |Date |Data wybrana do filtrowania danych |
| DateKey |Tekst |Unikatowy klucz dla każdego elementu daty |
| DayDiff |Liczba dziesiętna |Różnica w ciągu dnia filtrowania danych, na przykład, 0 oznacza dane w bieżącym dniu, -1 oznacza danych poprzedniego jednego dnia, 0 i -1 wskazuje danych dla bieżącego i poprzedniego dnia  |
| Miesiąc |Tekst |Miesiąc roku wybrane do filtrowania danych, rozpoczyna się pierwszego dnia miesiąca i kończy się 31 dni |
| MonthDate | Date |Data w ciągu miesiąca, kiedy kończy się miesiąca wybrane do filtrowania danych |
| MonthDiff |Liczba dziesiętna |Różnica w miesiącu, w przypadku filtrowania danych, na przykład, 0 oznacza dane z bieżącego miesiąca, -1 oznacza danych z poprzedniego miesiąca, 0 i -1 wskazuje danych w bieżącym i poprzednim miesiącu. |
| Tydzień |Tekst |Tydzień wybrane do filtrowania danych, tydzień zaczyna się od niedzieli, a kończy się w sobotę |
| WeekDate |Date |Data w tydzień po tygodniu zostaje zakończona, wybrać filtrowanie danych |
| WeekDiff |Liczba dziesiętna |Różnica w tygodniu do filtrowania danych, na przykład, wartość 0 wskazuje danych w bieżącym tygodniu, -1 oznacza danych z poprzedniego tygodnia, 0 i -1 wskazuje danych dla bieżącego i poprzedniego tygodnia |
| Rok |Tekst |Rok kalendarzowy wybrane do filtrowania danych |
| YearDate |Date |Data w roku, kiedy kończy się roku, wybrać filtrowanie danych |

### <a name="job"></a>Zadanie
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól związanych z pracą.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Liczba całkowita |Liczba zadań utworzonych w wybranym okresie |
| %FailuresForJobsCreatedInPeriod |Procentowe |Procent ogólnej zadaniami w wybranym okresie |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Liczba dziesiętna |80. wartość percentylu danych przesyłanych w Megabajtach **kopii zapasowej** zadań utworzonych w wybranym okresie |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach, aby **Zakończono tworzenie kopii zapasowej** zadań utworzonych w wybranym okresie |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach, aby **ukończyć przywracania** zadań utworzonych w wybranym okresie |
| BackupStorageDestination |Tekst |Docelowy magazyn kopii zapasowych na przykład chmura dysku  |
| EntityState |Tekst |Bieżący stan obiektu zadania, na przykład, aktywne, usunięte |
| JobFailureCode |Tekst |Z powodu którego wystąpiło niepowodzenie zadania ciągu kodu błędu |
| JobOperation |Tekst |Operacja, dla którego zadanie jest uruchamiane na przykład kopii zapasowej, przywracanie, skonfiguruj w kopii zapasowej |
| JobStartDate |Date |Data, gdy zadanie rozpoczęcia działania |
| JobStartTime |Time |Gdy zadanie rozpoczęcia działania |
| JobStatus |Tekst |Stan zadania zakończone na przykład, zakończone, nie powiodło się |
| JobUniqueId |Tekst |Unikatowy identyfikator zadania |

### <a name="policy"></a>Zasady
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych zasad dotyczących pól.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Policies |Liczba całkowita |Liczba zasad tworzenia kopii zapasowych, które istnieją w systemie |
| #PoliciesInUse |Liczba całkowita |Limit liczby zasad, które są obecnie używane do konfigurowania tworzenia kopii zapasowych |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| BackupDaysOfTheWeek |Tekst |Dni tygodnia, gdy zostały zaplanowane kopie zapasowe |
| BackupFrequency |Tekst |Częstotliwość, z którym są wykonywane kopie zapasowe na przykład, codziennie, co tydzień |
| BackupTimes |Tekst |Data i godzina, kiedy są zaplanowane kopie zapasowe |
| DailyRetentionDuration |Liczba całkowita |Czas trwania przechowywania całkowitej dni skonfigurowane kopie zapasowe |
| DailyRetentionTimes |Tekst |Data i godzina, gdy skonfigurowano przechowywanie codziennego |
| EntityState |Tekst |Bieżący stan obiektu zasad, na przykład, aktywne, usunięte |
| MonthlyRetentionDaysOfTheMonth |Tekst |Daty, miesiąca, wybrane do przechowywania miesięczne |
| MonthlyRetentionDaysOfTheWeek |Tekst |Dni tygodnia wybrane do przechowywania miesięczne |
| MonthlyRetentionDuration |Liczba dziesiętna |Czas trwania przechowywania całkowitej miesiące w przypadku skonfigurowane kopie zapasowe |
| MonthlyRetentionFormat |Tekst |Typ konfiguracji do przechowywania miesięczne na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| MonthlyRetentionTimes |Tekst |Data i godzina, po skonfigurowaniu przechowywanie miesięcznego |
| MonthlyRetentionWeeksOfTheMonth |Tekst |Tygodnie miesiąca, gdy jest przechowywanie miesięcznego na przykład skonfigurować pierwszy i ostatni itp. |
| PolicyName |Tekst |Nazwy zdefiniowane zasady |
| PolicyUniqueId |Tekst |Unikatowy identyfikator zasad |
| RetentionType |Tekst |Typ zasady przechowywania na przykład codziennie, co tydzień, co miesiąc, co rok |
| WeeklyRetentionDaysOfTheWeek |Tekst |Dni tygodnia wybrane do przechowywania danych co tydzień |
| WeeklyRetentionDuration |Liczba dziesiętna |Łączny czas przechowywania co tydzień, w ciągu tygodni skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes |Tekst |Data i godzina, jeśli skonfigurowano przechowywanie tygodniowego |
| YearlyRetentionDaysOfTheMonth |Tekst |Daty, miesiąca, wybrane do przechowywania roczne |
| YearlyRetentionDaysOfTheWeek |Tekst |Dni tygodnia wybrane do przechowywania roczne |
| YearlyRetentionDuration |Liczba dziesiętna |Czas trwania przechowywania całkowitej lat skonfigurowane kopie zapasowe |
| YearlyRetentionFormat |Tekst |Typ konfiguracji do przechowywania roczne na przykład codziennie na podstawie co tydzień w oparciu o tydzień dzień |
| YearlyRetentionMonthsOfTheYear |Tekst |Miesiące roku wybrane do przechowywania roczne |
| YearlyRetentionTimes |Tekst |Data i godzina, po skonfigurowaniu roczne przechowywanie |
| YearlyRetentionWeeksOfTheMonth |Tekst |Tygodnie miesiąca, gdy jest roczne przechowywanie na przykład skonfigurować pierwszy i ostatni itp. |

### <a name="protected-server"></a>Chroniony serwer
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych chronionych polach związanych z serwerem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedServers |Liczba całkowita |Liczba serwerów chronionych |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AzureBackupAgentOSType |Tekst |Typ systemu operacyjnego agenta usługi Azure Backup |
| AzureBackupAgentOSVersion |Tekst |Wersja systemu operacyjnego agenta usługi Azure Backup |
| AzureBackupAgentUpdateDate |Tekst |Data aktualizacji agenta usługi Kopia zapasowa agenta |
| AzureBackupAgentVersion |Tekst |Numer wersji agenta w wersji kopii zapasowej |
| BackupManagementType |Tekst |Typ dostawcy do wykonywania kopii zapasowej na przykład IaaSVM FileFolder |
| EntityState |Tekst |Bieżący stan obiektu chronionego serwera, na przykład, aktywne, usunięte |
| ProtectedServerFriendlyName |Tekst |Przyjazna nazwa chronionego serwera |
| ProtectedServerName |Tekst |Nazwa chronionego serwera |
| ProtectedServerType |Tekst |Typ serwera chronionego kopii zapasowej na przykład IaaSVMContainer |
| ProtectedServerName |Tekst |Nazwa chronionego serwera, do którego element kopii zapasowej należy |
| RegisteredContainerId |Tekst |Identyfikator kontenera zarejestrowany dla kopii zapasowej |

### <a name="storage"></a>Magazyn
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól związane z magazynowaniem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedInstances |Liczba dziesiętna |Liczba chronionych wystąpień służące do obliczania magazynu frontonu w rozliczeń, obliczonej na podstawie wartości najnowsze w zaznaczony czas |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| CloudStorageInMB |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, które są obliczane na podstawie najnowszej wartości w wybranym |
| EntityState |Tekst |Bieżący stan obiektu, na przykład, aktywne, usunięte |
| LastUpdatedDate |Date |Data ostatniej aktualizacji wybranego wiersza |

### <a name="time"></a>Time
Ta tabela zawiera szczegółowe informacje o polach związanych z czasem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Godzina |Time |Godziny dnia, na przykład 1:00:00 PM |
| HourNumber |Liczba dziesiętna |Liczba godzin w ciągu dnia, na przykład 13,00 |
| Minuta |Liczba dziesiętna |Minuta |
| PeriodOfTheDay |Tekst |Okres okna czasowego w ciągu dnia, na przykład 12-3 AM |
| Time |Time |Pora dnia na przykład 0:00:01: 00 |
| TimeKey |Tekst |Wartość klucza do reprezentowania czasu |

### <a name="vault"></a>Magazyn
Ta tabela zawiera podstawowe pól i agregacji za pośrednictwem różnych pól powiązanych z magazynu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Vaults |Liczba całkowita |Liczba magazynów |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AzureDataCenter |Tekst |Centrum danych, w którym znajduje się magazyn |
| EntityState |Tekst |Bieżący stan obiektu magazynu, na przykład, aktywne, usunięte |
| StorageReplicationType |Tekst |Typ replikacji magazynu dla magazynu, na przykład GeoRedundant |
| SubscriptionId |Tekst |Identyfikator subskrypcji klienta wybranego do generowania raportów |
| VaultName |Tekst |Nazwa magazynu |
| VaultTags |Tekst |Znaczniki skojarzone w magazynie |

## <a name="next-steps"></a>Kolejne kroki
Po przejrzeniu modelu danych do tworzenia raportów usługi Azure Backup, zapoznaj się następujące artykuły, aby uzyskać więcej informacji na temat tworzenia i wyświetlania raportów w usłudze Power BI.

* [Tworzenie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrowanie raportów usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
