---
title: Model danych dla usługi Kopia zapasowa Azure
description: Ten artykuł zawiera informacje o szczegóły modelu danych usługi Power BI dla raportów kopia zapasowa Azure.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a17e011452f9b87c1201cea12f394a9cdd18e54b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606226"
---
# <a name="data-model-for-azure-backup-reports"></a>Model danych dla raportów usługi Azure Backup
W tym artykule opisano model danych usługi Power BI, używany do tworzenia raportów usługi Kopia zapasowa Azure. Użycie tego modelu danych, można filtrować istniejących raportów na podstawie odpowiednich pól i więcej co ważniejsze, tworzyć własne raporty przy użyciu tabel i pól w modelu. 

## <a name="creating-new-reports-in-power-bi"></a>Tworzenie nowych raportów w usłudze Power BI
Usługa Power BI udostępnia funkcje dostosowywania za pomocą którego można [tworzenie raportów przy użyciu modelu danych](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Za pomocą usługi Kopia zapasowa Azure modelu danych
Następujące pola w ramach modelu danych służy do tworzenia raportów i dostosowywanie istniejących raportów.

### <a name="alert"></a>Alerty
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych pól powiązanych alertów.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Liczby całkowitej |Liczba alertów utworzone w wybranym okresie |
| % ActiveAlertsCreatedInPeriod |Procentowe |Odsetek aktywnych alertów w wybranym okresie |
| % CriticalAlertsCreatedInPeriod |Procentowe |Procent alerty krytyczne w wybranym okresie |
| AlertOccurenceDate |Date |Data utworzenia alertu |
| AlertSeverity |Tekst |Ważność alertu, na przykład krytyczne |
| AlertStatus |Tekst |Stan alertu, na przykład aktywny |
| AlertType |Tekst |Typ wygenerowany alert, na przykład kopii zapasowej |
| AlertUniqueId |Tekst |Unikatowy identyfikator wygenerowany alert |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Liczba dziesiętna |Średni czas (w minutach), aby rozwiązać alert dla wybranego okresu |
| EntityState |Tekst |Bieżący stan obiektu alertu, na przykład aktywny, usunięte |

### <a name="backup-item"></a>Element kopii zapasowej
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych kopii zapasowej pola związane z elementu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #BackupItems |Liczby całkowitej |Liczba elementów kopii zapasowych |
| #UnprotectedBackupItems |Liczby całkowitej |Liczba elementów kopii zapasowych zatrzymanie ochrony lub skonfigurowane dla kopii zapasowych, ale nie rozpoczęto tworzenie kopii zapasowych|
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| BackupItemFriendlyName |Tekst |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemId |Tekst |Identyfikator elementu kopii zapasowej |
| BackupItemName |Tekst |Nazwa elementu kopii zapasowej |
| BackupItemType |Tekst |Typ elementu kopii zapasowej na przykład maszynę Wirtualną, FileFolder |
| EntityState |Tekst |Bieżący stan obiektu elementu kopii zapasowej, na przykład aktywny, usunięte |
| LastBackupDateTime |Data/godzina |Czas ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej |
| LastBackupState |Tekst |Stan ostatniej kopii zapasowej dla wybranego elementu kopii zapasowej, na przykład, Powodzenie, Niepowodzenie |
| LastSuccessfulBackupDateTime |Data/godzina |Czas ostatniej pomyślnej kopii zapasowej dla wybranego elementu kopii zapasowej |
| ProtectionState |Tekst |Bieżący stan elementu kopii zapasowej, na przykład chronione, ProtectionStopped ochrony |

### <a name="calendar"></a>Kalendarz
Ta tabela zawiera szczegółowe informacje o polach związanych z kalendarzem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Date |Date |Data wybrane do filtrowania danych |
| DateKey |Tekst |Unikatowy klucz dla każdego elementu daty |
| DayDiff |Liczba dziesiętna |Różnica w dniu do filtrowania danych, na przykład, wartość 0 wskazuje danych w bieżącym dniu, -1 oznacza danych poprzednich jeden dzień, 0 i -1 wskazuje danych dla bieżącego i poprzedniego dnia  |
| Miesiąc |Tekst |Miesiąc roku wybrane do filtrowania danych, rozpoczyna się pierwszego dnia miesiąca i kończy się dzień 31- |
| MonthDate | Date |Data w miesiącu, gdy kończy się miesiąca wybrane do filtrowania danych |
| MonthDiff |Liczba dziesiętna |Różnica w miesiącu filtrowania danych, na przykład, wartość 0 wskazuje bieżącego miesiąca, -1 oznacza danych poprzedniego miesiąca, 0 i -1 wskazuje danych dla bieżącego i poprzedniego miesiąca |
| Tydzień |Tekst |Tydzień wybrane do filtrowania danych, tydzień zaczyna się od niedzieli, a kończy w sobotę |
| WeekDate |Date |Data w tygodniu, gdy kończy się tydzień wybrane do filtrowania danych |
| WeekDiff |Liczba dziesiętna |Różnica w tygodniu filtrowania danych, na przykład, wartość 0 wskazuje danych bieżącego tygodnia, -1 oznacza danych poprzedniego tygodnia, 0 i -1 wskazuje danych dla bieżącego i poprzedniego tygodnia |
| Rok |Tekst |Wybrana do filtrowania danych rok kalendarza |
| YearDate |Date |Data w roku, gdy kończy się roku wybrane do filtrowania danych |

### <a name="job"></a>Zadanie
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych pól związanych z pracą.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Liczby całkowitej |Liczba zadań utworzonych w wybranym okresie |
| % FailuresForJobsCreatedInPeriod |Procentowe |Wartość procentowa ogólne zadania błędów w wybranym okresie |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Liczba dziesiętna |80. wartość percentylu danych przesyłanych w MB **kopii zapasowej** zadania utworzone w wybranym okresie |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach, **Zakończono tworzenie kopii zapasowej** zadania utworzone w wybranym okresie |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Liczba dziesiętna |Średni czas w minutach, **Ukończono przywracanie** zadania utworzone w wybranym okresie |
| BackupStorageDestination |Tekst |Miejsce docelowe magazynu kopii zapasowych na przykład chmura dysku  |
| EntityState |Tekst |Bieżący stan obiektu zadania, na przykład aktywny, usunięte |
| JobFailureCode |Tekst |Ciąg kodu błędu z powodu którego wystąpiło niepowodzenie zadania |
| JobOperation |Tekst |Operacja, dla którego zadanie jest uruchamiane na przykład kopii zapasowych, przywracania, konfigurowanie usługi Kopia zapasowa |
| JobStartDate |Date |Data rozpoczęcia zadania działania |
| JobStartTime |Time |Czas uruchomienia zadania |
| JobStatus |Tekst |Stan zadania Zakończono na przykład ukończone, nie powiodło się |
| JobUniqueId |Tekst |Unikatowy identyfikator zadania |

### <a name="policy"></a>Zasady
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych pól związane z zasadami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Policies |Liczby całkowitej |Liczba zasad tworzenia kopii zapasowych, które istnieją w systemie |
| #PoliciesInUse |Liczby całkowitej |Liczba zasad aktualnie używane do konfigurowania kopii zapasowych |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| BackupDaysOfTheWeek |Tekst |Dni tygodnia, gdy zaplanowanych kopii zapasowych |
| BackupFrequency |Tekst |Częstotliwość, z którym są uruchamiane kopii zapasowych, na przykład, codziennie, co tydzień |
| BackupTimes |Tekst |Data i godzina, kiedy zaplanowane tworzenie kopii zapasowych |
| DailyRetentionDuration |Liczby całkowitej |Czas trwania całkowita przechowywania w dniach skonfigurowanych kopii zapasowych |
| DailyRetentionTimes |Tekst |Data i godzina, gdy skonfigurowano przechowywania codziennych |
| EntityState |Tekst |Bieżący stan obiektu zasad, na przykład aktywny, usunięte |
| MonthlyRetentionDaysOfTheMonth |Tekst |Daty miesiąca wybrany do przechowywania miesięcznego |
| MonthlyRetentionDaysOfTheWeek |Tekst |Wybrane dni tygodnia do przechowywania miesięcznego |
| MonthlyRetentionDuration |Liczba dziesiętna |Czas przechowywania całkowitej w miesiącach skonfigurowanych kopii zapasowych |
| MonthlyRetentionFormat |Tekst |Wpisz konfiguracji do przechowywania miesięczne na przykład codziennie na podstawie co tydzień na podstawie tydzień dzień |
| MonthlyRetentionTimes |Tekst |Data i godzina, gdy miesięczne przechowywania jest skonfigurowany |
| MonthlyRetentionWeeksOfTheMonth |Tekst |Tygodnie miesiąca, w przypadku przechowywania miesięczne na przykład skonfigurować First, Last itp. |
| PolicyName |Tekst |Nazwa zdefiniowane zasady |
| PolicyUniqueId |Tekst |Unikatowy identyfikator zasady |
| RetentionType |Tekst |Typ zasad przechowywania na przykład codziennie, co tydzień, co miesiąc, co rok |
| WeeklyRetentionDaysOfTheWeek |Tekst |Wybrane dni tygodnia do przechowywania co tydzień |
| WeeklyRetentionDuration |Liczba dziesiętna |Całkowita liczba tygodniowy czas przechowywania w tygodniach skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes |Tekst |Data i godzina, gdy jest skonfigurowany co tydzień przechowywania |
| YearlyRetentionDaysOfTheMonth |Tekst |Daty miesiąca wybrany do przechowywania roczne |
| YearlyRetentionDaysOfTheWeek |Tekst |Wybrane dni tygodnia do przechowywania roczne |
| YearlyRetentionDuration |Liczba dziesiętna |Czas przechowywania całkowitej w latach skonfigurowanych kopii zapasowych |
| YearlyRetentionFormat |Tekst |Wpisz konfiguracji do przechowywania corocznych na przykład codziennie na podstawie co tydzień na podstawie tydzień dzień |
| YearlyRetentionMonthsOfTheYear |Tekst |Wybrane miesięcy do przechowywania roczne |
| YearlyRetentionTimes |Tekst |Data i godzina, gdy corocznych przechowywania jest skonfigurowany |
| YearlyRetentionWeeksOfTheMonth |Tekst |Tygodnie miesiąca, gdy corocznych przechowywania jest na przykład skonfigurować First, Last itp. |

### <a name="protected-server"></a>Serwer chroniony
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych pola chronione związanych z serwerem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedServers |Liczby całkowitej |Liczba chronionych serwerów |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AzureBackupAgentOSType |Tekst |Typ systemu operacyjnego agenta usługi Kopia zapasowa Azure |
| AzureBackupAgentOSVersion |Tekst |Wersja systemu operacyjnego agenta usługi Kopia zapasowa Azure |
| AzureBackupAgentUpdateDate |Tekst |Data aktualizacji agenta usługi Kopia zapasowa agenta |
| AzureBackupAgentVersion |Tekst |Numer wersji wersja agenta kopii zapasowej |
| BackupManagementType |Tekst |Typ dostawcy do wykonywania kopii zapasowej na przykład IaaSVM FileFolder |
| EntityState |Tekst |Bieżący stan obiektu serwera chronionego, na przykład aktywny, usunięte |
| ProtectedServerFriendlyName |Tekst |Przyjazna nazwa serwera chronionego |
| ProtectedServerName |Tekst |Nazwa serwera chronionego |
| ProtectedServerType |Tekst |Typ serwera chronionego kopię zapasową na przykład IaaSVMContainer |
| ProtectedServerName |Tekst |Nazwa serwera chronionego, do którego kopii zapasowej elementu należy |
| RegisteredContainerId |Tekst |Identyfikator kontenera zarejestrowany dla kopii zapasowej |

### <a name="storage"></a>Magazyn
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych pól związanych z magazynu.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #ProtectedInstances |Liczba dziesiętna |Liczba wystąpień chronionych służące do obliczania magazynu frontonu w rozliczeń, obliczonej na podstawie wartości najnowsze w wybrana wartość czasu |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| CloudStorageInMB |Liczba dziesiętna |Magazynu kopii zapasowej w chmurze używane przez kopie zapasowe, które są obliczane na podstawie najnowszej wartości w wybrana wartość czasu |
| EntityState |Tekst |Bieżący stan obiektu, na przykład aktywny, usunięte |
| LastUpdatedDate |Date |Data ostatniej aktualizacji wybranego wiersza |

### <a name="time"></a>Time
Ta tabela zawiera szczegółowe informacje o polach związanych z czasem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| Godzina |Time |Godzina dnia, na przykład 1:00:00 PM |
| HourNumber |Liczba dziesiętna |Liczba godzin w ciągu dnia na przykład 13,00 |
| Minuta |Liczba dziesiętna |Minuta |
| PeriodOfTheDay |Tekst |Odstęp okresu czasu w ciągu dnia na przykład AM 12-3 |
| Time |Time |Porę dnia, na przykład 00:00:01: 00 |
| TimeKey |Tekst |Wartość klucza odpowiadają za czas |

### <a name="vault"></a>Magazyn
Ta tabela zawiera pola podstawowe i agregacji za pośrednictwem różnych pól związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| #Vaults |Liczby całkowitej |Liczba magazynów |
| AsOnDateTime |Data/godzina |Ostatni czas odświeżania dla wybranego wiersza |
| AzureDataCenter |Tekst |Centrum danych, w którym znajduje się magazyn |
| EntityState |Tekst |Bieżący stan obiektu magazynu na przykład aktywny, usunięte |
| StorageReplicationType |Tekst |Typ replikacji magazynu dla magazynu, na przykład GeoRedundant |
| SubscriptionId |Tekst |Identyfikator subskrypcji klienta wybranego do generowania raportów |
| VaultName |Tekst |Nazwa magazynu |
| VaultTags |Tekst |Znaczniki skojarzone do magazynu |

## <a name="next-steps"></a>Kolejne kroki
Po przejrzeniu modelu danych do tworzenia raportów usługi Kopia zapasowa Azure można znaleźć następujące artykuły, aby uzyskać więcej informacji na temat tworzenia i wyświetlania raportów w usłudze Power BI.

* [Tworzenie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrowanie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
