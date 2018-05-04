---
title: Baza danych SQL automatycznego, geograficznie nadmiarowego kopii zapasowych Azure | Dokumentacja firmy Microsoft
description: Baza danych SQL automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i korzysta z magazynu geograficznie nadmiarowego Azure dostęp do odczytu dla nadmiarowość geograficzna.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="learn-about-automatic-sql-database-backups"></a>Więcej informacji na temat automatycznego tworzenia kopii zapasowej bazy danych SQL

Baza danych SQL automatycznie tworzy kopie zapasowe bazy danych i używa magazynu geograficznie nadmiarowego Azure dostęp do odczytu (RA-GRS), aby zapewnić nadmiarowość geograficzna. Te kopie zapasowe są tworzone automatycznie i bez dodatkowych opłat. Nie trzeba wykonywać żadnych czynności, aby były w stanie. Kopie zapasowe bazy danych są integralną część każdej strategii odzyskiwania biznesowych, jak ciągłości i odzyskiwaniem po awarii, ponieważ ich ochronę danych przed przypadkowym uszkodzenia lub usunięcia. Jeśli chcesz przechowywać kopie zapasowe w własne kontenera magazynu można skonfigurować zasadę długoterminowego przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Co to jest kopia zapasowa bazy danych SQL?

Baza danych SQL używa technologii SQL Server w celu utworzenia [pełne](https://msdn.microsoft.com/library/ms186289.aspx), [różnicowej](https://msdn.microsoft.com/library/ms175526.aspx), i [dziennika transakcji](https://msdn.microsoft.com/library/ms191429.aspx) kopii zapasowych na potrzeby punktu w czasie przywracania (PITR). Kopie zapasowe dziennika transakcji nastąpić zazwyczaj co 5 – 10 minut, przy częstotliwości na podstawie poziomu wydajności i ilości aktywności bazy danych. Kopie zapasowe dziennika transakcji, o pełne i różnicowe kopie zapasowe, można przywrócić bazę danych do określonego punktu w czasie na tym samym serwerze, który jest hostem bazy danych. Po przywróceniu bazy danych usługi danych liczbowych limit które dziennika pełnej, różnicowej i transakcji konieczne jest przywrócenie kopii zapasowych.


Te kopie zapasowe, można użyć:

* Przywróć bazę danych do punktu w czasie w okresie przechowywania. Ta operacja spowoduje utworzenie nowej bazy danych, w tym samym serwerze co oryginalnej bazy danych.
* Przywracanie usuniętej bazy danych do czasu, który został usunięty lub kiedykolwiek okresem przechowywania. W tym samym serwerze, na którym utworzono oryginalnej bazy danych można przywrócić tylko usunięte bazy danych.
* Przywróć bazę danych do innego regionu geograficznego. Dzięki temu można odzyskać z geograficzne po awarii, gdy nie można uzyskać dostępu do serwera i bazy danych. Tworzy nową bazę danych w dowolnym istniejącego serwera miejscu na świecie. 
* Z określonym długoterminowej kopii zapasowej należy przywrócić bazę danych, jeśli bazy danych został skonfigurowany z zasadami przechowywania długoterminowego. Dzięki temu można przywrócić starą wersję bazy danych, aby wykonać żądanie zgodności lub uruchomić starą wersję aplikacji. Zobacz [przechowywania długoterminowego](sql-database-long-term-retention.md).
* Aby wykonać operację przywracania, zobacz [przywrócić bazę danych z kopii zapasowych](sql-database-recovery-using-backups.md).

> [!NOTE]
> W magazynie Azure określenie *replikacji* odwołuje się do kopiowania plików z jednej lokalizacji do innej. SQL do *replikacji bazy danych* odwołuje się do przechowywania wiele pomocniczych baz danych synchronizowane z podstawowej bazy danych. 
> 

## <a name="how-often-do-backups-happen"></a>Jak często stanie kopie zapasowe?
Kopie zapasowe pełnej bazy danych są wykonywane co tydzień, kopie zapasowe różnicowej bazy danych są zazwyczaj wykonywane co kilka godzin, a dziennik transakcji, których kopie zapasowe są zazwyczaj wykonywane co 5 – 10 minut. Pierwsza pełna kopia zapasowa jest zaplanowane, natychmiast po utworzeniu bazy danych. Zazwyczaj wykonuje w ciągu 30 minut, ale może to trwać dłużej baza danych jest znaczne rozmiary. Na przykład początkowa kopia zapasowa może trwać dłużej w przywróconej bazy danych lub kopii bazy danych. Po pierwszym pełną kopię zapasową wszystkie dodatkowe kopie zapasowe są zaplanowane automatycznie i zarządzane w trybie dyskretnym w tle. Dokładny czas wszystkie kopie zapasowe bazy danych jest określana przez usługi SQL Database go jako ogólną obciążenia systemu. 

Replikacja geograficzna magazynu kopii zapasowych występuje na podstawie harmonogramu replikacji usługi Azure Storage.

## <a name="how-long-do-you-keep-my-backups"></a>Jak długo zachować kopiach zapasowych?
Każdej kopii zapasowej bazy danych SQL ma okres przechowywania, opiera się na warstwie usług bazy danych, która różni się między [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md). 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>Przechowywanie bazy danych na podstawie jednostek dtu w warstwie model kupna
Okres przechowywania bazy danych w model kupna jednostek dtu w warstwie zależy od warstwy usług. Okres przechowywania dla bazy danych dla:

* Warstwy usług podstawowa wynosi 7 dni.
* Standardowa usługa warstwa jest 35 dni.
* Warstwy usług Premium jest 35 dni.
* Warstwa ogólnego przeznaczenia jest można skonfigurować przy użyciu maksymalnie 35 dni (domyślnie co 7 dni) *
* Krytyczne warstwy biznesowej (wersja zapoznawcza) jest można skonfigurować przy użyciu maksymalnie 35 dni (domyślnie co 7 dni) *

\* Podczas udostępniania wersji zapoznawczej okres przechowywania kopii zapasowych nie konfiguruje się i zostanie rozwiązany do 7 dni.

Po skonwertowaniu bazy danych z dłużej przechowywania kopii zapasowych bazy danych z krótszą przechowywania wszystkich istniejących kopii zapasowych starsze niż okres przechowywania warstwy docelowych nie będą dostępne.

Po uaktualnieniu baza danych o krótszy okres przechowywania bazy danych z dłuższy okres bazy danych SQL przechowuje istniejące kopie zapasowe, aż do osiągnięcia dłuższy okres przechowywania. 

Usunięcie bazy danych, bazy danych SQL przechowuje kopie zapasowe w taki sam sposób miałoby to miejsce dla bazy danych online. Na przykład załóżmy, że należy usunąć podstawowa baza danych ma okres przechowywania wynosi siedem dni. Kopię zapasową, która jest czterech dni są zapisywane dla trzech dni.

> [!IMPORTANT]
> Usunięcie serwera Azure SQL hostującym bazy danych SQL, wszystkie bazy danych, które należą do tego serwera, również zostaną usunięte i nie może zostać odzyskany. Nie można przywrócić usuniętego serwera.

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>Przechowywanie bazy danych na podstawie vCore model kupna (wersja zapoznawcza)

Magazyn kopii zapasowych bazy danych jest przydzielony do obsługi punktu w czasie przywracania (PITR) i długi okres przechowywania STYLEM możliwościach bazy danych SQL. Ta pamięć masowa jest przydzielony osobno dla każdej bazy danych i rozliczane jako dwa osobne na bazę danych opłat. 

- **PITR**: jedna baza danych, kopie zapasowe są kopiowane do magazynu RA-GRS są automatycznie. Rozmiar magazynu zwiększa dynamicznie w miarę tworzenia nowych kopii zapasowych.  Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Użycia magazynu zależy szybkość zmian w bazie danych i okresu przechowywania. Można skonfigurować okres przechowywania osobne dla każdej bazy danych od 7 do 35 dni. Kwota minimalna magazynu równy 1 x rozmiar danych jest dostarczany bez dodatkowych opłat. Dla baz danych ta wartość jest wystarczająca do przechowywania kopii zapasowych 7 dni. Aby uzyskać więcej informacji, zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore)
- **Od lewej do prawej**: baza danych SQL udostępnia opcję konfigurowania długoterminowego przechowywania tworzenia pełnych kopii zapasowych maksymalnie 10 lat. Jeśli od lewej do prawej jest włączona, kopie zapasowe te są przechowywane w magazynie RA-GRS automatycznie, ale można kontrolować, jak często kopie zapasowe są kopiowane. Aby spełnić wymagania zgodności różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczne i roczne. Ta konfiguracja określi, ile miejsca do magazynowania, które będą używane do tworzenia kopii zapasowych od lewej do prawej. Kalkulator cen od lewej do prawej służy do szacowania kosztów magazynowania od lewej do prawej. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="how-to-extend-the-backup-retention-period"></a>Jak rozszerzyć okres przechowywania kopii zapasowej?

Jeśli aplikacja wymaga, aby tworzenie kopii zapasowych są dostępne dłużej niż maksymalny okres przechowywania kopii zapasowych PITR, można skonfigurować zasadę długoterminowego przechowywania kopii zapasowych dla pojedynczych baz danych (zasada od lewej do prawej). Dzięki temu można rozszerzyć okres przechowywania wbudowane it maksymalnie 10 lat z maksymalną 35 dni. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

Po dodaniu zasad od lewej do prawej z bazą danych przy użyciu portalu Azure lub interfejsu API cotygodniowe kopie zapasowe pełnej bazy danych będą automatycznie skopiowane do oddzielnych kontenera magazynu RA-GRS, w celu przechowywania długoterminowego (od lewej do prawej magazynu). Jeśli baza danych jest szyfrowany przy funkcji TDE kopie zapasowe są automatycznie szyfrowane, gdy. Baza danych SQL automatycznie usunie kopie zapasowe wygasłe na podstawie ich sygnatury czasowej i zasad od lewej do prawej. Po skonfigurowaniu zasad, nie trzeba zarządzać harmonogram tworzenia kopii zapasowych lub martwić usuwania starych plików. Aby wyświetlić, przywrócić lub usunąć te kopie zapasowe, można użyć portalu Azure lub programu PowerShell.

## <a name="are-backups-encrypted"></a>Kopie zapasowe są szyfrowane?

Po włączeniu funkcji TDE dla bazy danych Azure SQL kopii zapasowych również są szyfrowane. Wszystkie nowe bazy danych Azure SQL korzystają z funkcji TDE domyślnie włączone. Aby uzyskać więcej informacji o funkcji TDE, zobacz [przezroczystego szyfrowania danych z bazy danych SQL Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>Automatyczne kopie zapasowe są zgodne z GDPR?
Jeśli kopia zapasowa zawiera dane osobowe, czyli może ulec ogólne dane ochrony rozporządzenia (GDPR), należy zastosować środki zwiększone zabezpieczenia, aby chronić dane przed nieautoryzowanym dostępem. Aby można było zgodne z GDPR, należy sposób zarządzania żądania danych właścicieli danych bez konieczności uzyskiwania dostępu do kopii zapasowych.  Krótkoterminowe kopie zapasowe, jedno rozwiązanie można skrócić kopii zapasowej dozwolone okna w ciągu 30 dni, który określa czas do ukończenia żądania dostępu do danych.  Dłuższy okres kopie zapasowe są wymagane, zalecane jest przechowywanie tylko "pseudonymized" danych podczas tworzenia kopii zapasowych. Na przykład jeśli dane dotyczące osoby musi zostać usunięty lub zaktualizowany, nie trzeba będzie usunięcie lub aktualizowania istniejących kopii zapasowych. Można znaleźć więcej informacji na temat najlepszych rozwiązań w GDPR [zarządzania danymi zgodności GDPR](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html).

## <a name="next-steps"></a>Kolejne kroki

- Kopie zapasowe bazy danych są integralną część każdej strategii odzyskiwania biznesowych, jak ciągłości i odzyskiwaniem po awarii, ponieważ ich ochronę danych przed przypadkowym uszkodzenia lub usunięcia. Aby poznać inne bazy danych SQL Azure firm ciągłości rozwiązania, zobacz temat [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).
- Aby przywrócić do punktu w czasie przy użyciu portalu Azure, zobacz [przywrócenie bazy danych do punktu w czasie przy użyciu portalu Azure](sql-database-recovery-using-backups.md).
- Aby przywrócić do punktu w czasie przy użyciu programu PowerShell, zobacz [przywrócenie bazy danych do punktu w czasie przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).
- Do konfigurowania, zarządzania i przywrócenie z długoterminowego przechowywania automatycznego tworzenia kopii zapasowych w magazynie usług odzyskiwania Azure przy użyciu portalu Azure, zobacz [Zarządzanie długoterminowego przechowywania kopii zapasowych przy użyciu portalu Azure](sql-database-long-term-backup-retention-configure.md).
- Do konfigurowania, zarządzania i przywrócenie z długoterminowego przechowywania automatycznego tworzenia kopii zapasowych w magazynie usług odzyskiwania Azure przy użyciu programu PowerShell, zobacz [Zarządzanie długoterminowego przechowywania kopii zapasowych przy użyciu programu PowerShell](sql-database-long-term-backup-retention-configure.md).
