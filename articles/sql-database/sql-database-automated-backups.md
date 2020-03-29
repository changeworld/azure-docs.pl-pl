---
title: Automatyczne, geob nadmiarowe kopie zapasowe
description: Baza danych SQL automatycznie tworzy lokalną kopię zapasową bazy danych co kilka minut i używa magazynu geograficznego dostępu do odczytu platformy Azure w celu zapewnienia nadmiarowości geograficznej.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061760"
---
# <a name="automated-backups"></a>Automatyczne kopie zapasowe

Usługa Azure SQL Database automatycznie tworzy kopie zapasowe bazy danych, które są przechowywane przez czas trwania skonfigurowanego okresu przechowywania. Używa usługi Azure [do odczytu dostępu geograficznego magazynu (RA-GRS),](../storage/common/storage-redundancy.md) aby upewnić się, że kopie zapasowe są zachowywane, nawet jeśli centrum danych jest niedostępny.

Kopie zapasowe bazy danych są istotną częścią każdej strategii ciągłości biznesowej i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Jeśli reguły zabezpieczeń wymagają, aby kopie zapasowe były dostępne przez dłuższy czas (do 10 lat), można skonfigurować [długoterminowe przechowywanie](sql-database-long-term-retention.md) w pojedynczych bazach danych i pulach elastycznych baz danych.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co to jest kopia zapasowa bazy danych SQL?

Baza danych SQL używa technologii SQL Server do tworzenia [pełnych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12 godzin i [kopii zapasowych dziennika transakcji](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5 do 10 minut. Kopie zapasowe są przechowywane w [obiektach blob magazynu RA-GRS,](../storage/common/storage-redundancy.md) które są replikowane do [sparowanego centrum danych](../best-practices-availability-paired-regions.md) w celu ochrony przed awarią centrum danych. Podczas przywracania bazy danych usługa określa, które pełne, różnicowe i kopie zapasowe dziennika transakcji muszą zostać przywrócone.

Tych kopii zapasowych można użyć w następujących celach:

- **Przywróć istniejącą bazę danych do punktu w czasie w przeszłości w** okresie przechowywania przy użyciu witryny Azure portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. W przypadku pojedynczych baz danych i pul elastycznej bazy danych ta operacja utworzy nową bazę danych na tym samym serwerze co oryginalna baza danych. W wystąpieniu zarządzanym ta operacja może utworzyć kopię bazy danych lub tego samego lub innego wystąpienia zarządzanego w ramach tej samej subskrypcji.
- **Przywróć usuniętą bazę danych do czasu usunięcia** lub w dowolnym momencie w okresie przechowywania. Usuniętą bazę danych można przywrócić tylko na tym samym serwerze logicznym lub wystąpieniu zarządzanym, w którym utworzono oryginalną bazę danych.
- **Przywracanie bazy danych do innego regionu geograficznego**. Przywracanie geograficzne umożliwia odzyskanie danych po katastrofie geograficznej, gdy nie można uzyskać dostępu do serwera i bazy danych. Tworzy nową bazę danych na dowolnym istniejącym serwerze, w dowolnym miejscu na świecie.
- **Przywracanie bazy danych z określonej długoterminowej kopii zapasowej** w pojedynczej bazie danych lub puli elastycznej bazy danych, jeśli baza danych jest skonfigurowana z zasadami długoterminowego przechowywania (LTR). LTR umożliwia przywrócenie starej wersji bazy danych przy użyciu [witryny Azure portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) lub [usługi Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) w celu spełnienia żądania zgodności lub uruchomić starą wersję aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

Aby wykonać przywracanie, zobacz [Przywracanie bazy danych z kopii zapasowych](sql-database-recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure Storage termin *replikacja* odnosi się do kopiowania plików z jednej lokalizacji do drugiej. *Replikacja bazy danych* programu SQL Server odnosi się do utrzymywania wielu pomocniczych baz danych zsynchronizowanych z podstawową bazą danych.

Niektóre z tych operacji można wypróbować, korzystając z następujących przykładów:

| | Witryna Azure Portal | Azure PowerShell |
|---|---|---|
| Zmienianie przechowywania kopii zapasowych | [Pojedyncza baza danych](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Wystąpienie zarządzane](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Pojedyncza baza danych](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Zmiana długoterminowego przechowywania kopii zapasowych | [Pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane — nie dotyczy  | [Pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md)<br/>Wystąpienie zarządzane — nie dotyczy  |
| Przywracanie bazy danych z punktu w czasie | [Pojedyncza baza danych](sql-database-recovery-using-backups.md#point-in-time-restore) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Przywracanie usuniętej bazy danych | [Pojedyncza baza danych](sql-database-recovery-using-backups.md) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Przywracanie bazy danych z magazynu obiektów blob platformy Azure | Pojedyncza baza danych - Nie dotyczy <br/>Wystąpienie zarządzane — nie dotyczy  | Pojedyncza baza danych - Nie dotyczy <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Baza danych SQL obsługuje samoobsługi dla przywracania punktu w czasie (PITR) przez automatyczne tworzenie pełnych kopii zapasowych, różnicowych kopii zapasowych i kopii zapasowych dziennika transakcji. Kopie zapasowe pełnej bazy danych są tworzone co tydzień, a różnicowe kopie zapasowe bazy danych są zazwyczaj tworzone co 12 godzin. Kopie zapasowe dziennika transakcji są zazwyczaj tworzone co 5 do 10 minut. Częstotliwość kopii zapasowych dziennika transakcji jest oparta na rozmiarze obliczeń i ilości aktywności bazy danych. 

Pierwsza pełna kopia zapasowa jest zaplanowana natychmiast po utworzeniu bazy danych. Ta kopia zapasowa zwykle kończy się w ciągu 30 minut, ale może potrwać dłużej, gdy baza danych jest duża. Na przykład początkowa kopia zapasowa może trwać dłużej na przywróconej bazy danych lub kopii bazy danych. Po utworzeniu pierwszej pełnej kopii zapasowej wszystkie kolejne kopie zapasowe są zaplanowane automatycznie i zarządzane w trybie dyskretnym w tle. Dokładne terminy wykonywania wszystkich kopii zapasowych bazy danych są określane przez usługę SQL Database, ponieważ równoważy ona całkowite obciążenie systemu. Nie można zmienić ani wyłączyć zadań tworzenia kopii zapasowej.

Kopie zapasowe PITR są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji na temat PITR, zobacz [Przywracanie punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Długoterminowe przechowywanie

W przypadku pojedynczych i pulchowanych baz danych można skonfigurować długoterminowe przechowywanie (LTR) pełnych kopii zapasowych przez okres do 10 lat w magazynie obiektów Blob platformy Azure. Jeśli włączysz zasady LTR, tygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, miesięcznych i/lub rocznych kopii zapasowych. Zużycie magazynu zależy od wybranej częstotliwości kopii zapasowych i okresu przechowywania lub okresów. Kalkulator cen [LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) służy do szacowania kosztów magazynu LTR.

Podobnie jak kopie zapasowe PITR, kopie zapasowe LTR są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji na temat LTR, zobacz [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Zużycie pamięci masowej kopii zapasowej

W przypadku pojedynczych baz danych to równanie służy do obliczania całkowitego użycia magazynu kopii zapasowych:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

W przypadku pule elastycznej bazy danych całkowity rozmiar magazynu kopii zapasowej jest agregowany na poziomie puli i jest obliczany w następujący sposób:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Kopie zapasowe, które występują przed okresem przechowywania są automatycznie czyszte na podstawie ich sygnatury czasowej. Ponieważ różnicowe kopie zapasowe i kopie zapasowe dziennika wymagają wcześniejszej pełnej kopii zapasowej, aby były przydatne, są one czyszczące razem w cotygodniowych fragmentach.

Usługa Azure SQL Database oblicza całkowitą pamięć masową kopii zapasowych w retencji jako wartość skumulowaną. Co godzinę ta wartość jest raportowana do potoku rozliczeń platformy Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego w celu obliczenia zużycia na koniec każdego miesiąca. Po upuszczeniu bazy danych zużycie zmniejsza się wraz z wiekiem kopii zapasowych. Po wykonaniu kopii zapasowych stają się starsze niż okres przechowywania, płatności zatrzymuje.

   > [!IMPORTANT]
   > Kopie zapasowe bazy danych są przechowywane przez określony okres przechowywania, nawet jeśli baza danych została porzucona. Podczas gdy upuszczenie i ponowne utworzenie bazy danych może często zaoszczędzić na kosztach magazynowania i obliczeń, może to zwiększyć koszty przechowywania kopii zapasowych, ponieważ firma Microsoft zachowuje kopię zapasową przez określony okres przechowywania (co najmniej 7 dni) dla każdej porzuconej bazy danych, co czas jego upuszczenia.

### <a name="monitor-consumption"></a>Monitorowanie zużycia

Każdy typ kopii zapasowej (pełny, różnicowy i dziennik) jest zgłaszany w bloku monitorowania bazy danych jako oddzielne metryki. Na poniższym diagramie pokazano, jak monitorować zużycie magazynu kopii zapasowej dla pojedynczej bazy danych. Ta funkcja jest obecnie niedostępna dla wystąpień zarządzanych.

![Monitorowanie zużycia kopii zapasowej bazy danych w witrynie Azure portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Precyzyjne dostosowywanie zużycia pamięci masowej kopii zapasowej

Nadmierne zużycie magazynu kopii zapasowej zależy od obciążenia i rozmiaru poszczególnych baz danych. Rozważmy niektóre z następujących technik dostrajania, aby zmniejszyć zużycie magazynu kopii zapasowych:

* Skróć [okres przechowywania kopii zapasowej](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) do minimum możliwego do twoich potrzeb.
* Należy unikać wykonywania dużych operacji zapisu, takich jak indeks odbudowuje, częściej niż trzeba.
* W przypadku operacji ładowania dużych danych należy rozważyć użycie [indeksów klastrowanego magazynu kolumn](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), zmniejszyć liczbę indeksów nieklastrowanych i wziąć pod uwagę operacje obciążenia zbiorczego z liczbą wierszy około 1 miliona.
* W warstwie usług ogólnego przeznaczenia aprowizowanego magazynu danych jest tańsze niż cena nadmiaru magazynu kopii zapasowej. Jeśli masz stale wysokie koszty nadmiaru magazynu kopii zapasowych, można rozważyć zwiększenie magazynu danych, aby zaoszczędzić na magazynie kopii zapasowych.
* Użyj TempDB zamiast stałych tabel w logice ETL do przechowywania wyników tymczasowych. (Dotyczy tylko wystąpienia zarządzanego).
* Należy rozważyć wyłączenie szyfrowania TDE dla baz danych, które nie zawierają poufnych danych (rozwoju lub testowania baz danych, na przykład). Kopie zapasowe niezaszyfrowanych baz danych są zazwyczaj kompresowane z wyższym współczynnikiem kompresji.

> [!IMPORTANT]
> W przypadku obciążeń magazynu magazynów danych analitycznych \ zdecydowanie zaleca się używanie [indeksów klastrowanego magazynu kolumn,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)zmniejszenie liczby indeksów nieklastrowanych i rozważenie operacji obciążenia zbiorczego z liczbą wierszy około 1 miliona w celu zmniejszenia nadmiernego zużycia magazynu kopii zapasowych.

## <a name="storage-costs"></a>Koszty magazynowania

Cena magazynu różni się w zależności od tego, czy używasz modelu DTU, czy modelu vCore.

### <a name="dtu-model"></a>Model jednostki DTU

Nie ma żadnych dodatkowych opłat za magazyn kopii zapasowych dla baz danych i pul elastycznej bazy danych, jeśli używasz modelu jednostek DTU.

### <a name="vcore-model"></a>Model rdzenia wirtualnego

W przypadku pojedynczych baz danych minimalna ilość magazynu kopii zapasowej równa 100 procentom rozmiaru bazy danych jest dostępna bez dodatkowych opłat. W przypadku puli elastycznej bazy danych i wystąpień zarządzanych minimalna kwota magazynu kopii zapasowych równa 100 procentom przydzielonego magazynu danych dla puli lub rozmiaru wystąpienia jest dostępna bez dodatkowych opłat. Dodatkowe użycie magazynu kopii zapasowych wiąże się z comiesięczną opłatą za ilość używanych GB. To dodatkowe zużycie zależy od obciążenia i rozmiaru poszczególnych baz danych.

Usługa Azure SQL Database obliczy całkowitą pamięć masową kopii zapasowej w retencji jako wartość skumulowaną. Co godzinę ta wartość jest raportowana do potoku rozliczeń platformy Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego, aby uzyskać zużycie na koniec każdego miesiąca. Po upuszczeniu bazy danych firma Microsoft zmniejsza zużycie wraz z wiekiem kopii zapasowych. Po wykonaniu kopii zapasowych stają się starsze niż okres przechowywania, płatności zatrzymuje. Ponieważ wszystkie kopie zapasowe dziennika i różnicowe kopie zapasowe są przechowywane przez cały okres przechowywania, silnie zmodyfikowane bazy danych będą miały wyższe opłaty za tworzenie kopii zapasowych.

Załóżmy, że baza danych zgromadziła 744 GB magazynu kopii zapasowych i że ta kwota pozostaje stała przez cały miesiąc. Aby przekonwertować to skumulowane zużycie magazynu na użycie godzinowe, podziel je przez 744,0 (31 dni w miesiącu * 24 godziny dziennie). Więc SQL Database będzie raport, że baza danych używane 1 GB kopii zapasowej PITR co godzinę. Rozliczenia platformy Azure zagregują to zużycie i pokażą użycie 744 GB dla całego miesiąca. Koszt będzie oparty na kursie $/GB/miesiąc w Twoim regionie.

Teraz bardziej złożony przykład. Załóżmy, że baza danych ma jego przechowywania wzrosła do 14 dni w środku miesiąca. Załóżmy, że ten wzrost (hipotetycznie) powoduje podwojenie całkowitego magazynu kopii zapasowej do 1488 GB. Baza danych SQL będzie raport 1 GB użycia dla godzin od 1 do 372. Raport użycia jako 2 GB dla godzin 373 do 744. To użycie zostanie zagregowane do ostatecznego rachunku w wysokości 1116 GB/miesiąc.

### <a name="monitor-costs"></a>Monitoruj koszty

Aby zrozumieć koszty magazynowania kopii zapasowych, przejdź do **usługi Zarządzanie kosztami + Rozliczenia** w witrynie Azure portal, wybierz pozycję Zarządzanie **kosztami**, a następnie wybierz pozycję **Analiza kosztów**. Wybierz żądaną subskrypcję jako **zakres**, a następnie filtruj okres i usługę, która Cię interesuje.

Dodaj filtr **nazwy usługi,** a następnie wybierz **bazę danych SQL** na liście rozwijanej. Użyj filtru **podkategorii licznika,** aby wybrać licznik rozliczeń dla usługi. W przypadku pojedynczej bazy danych lub puli elastycznej bazy danych wybierz **magazyn kopii zapasowych pojedynczej/elastycznej puli .** W przypadku wystąpienia zarządzanego wybierz **mi pitr backup storage**. Podkategorie **magazynu** i **obliczeń** mogą cię również zainteresować, ale nie są one skojarzone z kosztami magazynu kopii zapasowych.

![Analiza kosztów magazynowania kopii zapasowych](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Przechowywanie kopii zapasowych

Wszystkie bazy danych SQL platformy Azure (bazy danych pojedynczych, pulowanych i zarządzanych wystąpień) mają domyślny okres przechowywania kopii zapasowej wynoszący 7 dni. Okres [przechowywania kopii zapasowej](#change-the-pitr-backup-retention-period) można zmienić na 35 dni.

Jeśli usuniesz bazę danych, baza danych SQL zachowa kopie zapasowe w taki sam sposób, jak w przypadku bazy danych online. Na przykład jeśli usuniesz podstawową bazę danych, która ma okres przechowywania siedmiu dni, kopia zapasowa, która ma cztery dni, zostanie zapisana przez trzy dni.

Jeśli trzeba zachować kopie zapasowe dłużej niż maksymalny okres przechowywania, można zmodyfikować właściwości kopii zapasowej, aby dodać jeden lub więcej długoterminowych okresów przechowywania do bazy danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Jeśli usuniesz serwer SQL platformy Azure, który obsługuje bazy danych SQL, wszystkie pule elastycznej bazy danych i bazy danych, które należą do serwera są również usuwane. Nie da się ich odzyskać. Nie można przywrócić usuniętego serwera. Ale jeśli skonfigurowano długoterminowe przechowywanie, kopie zapasowe baz danych z LTR nie zostaną usunięte i te bazy danych można przywrócić.

## <a name="encrypted-backups"></a>Zaszyfrowane kopie zapasowe

Jeśli baza danych jest szyfrowana za pomocą TDE, kopie zapasowe są automatycznie szyfrowane w spoczynku, w tym kopie zapasowe LTR. Gdy TDE jest włączona dla bazy danych SQL platformy Azure, kopie zapasowe są również szyfrowane. Wszystkie nowe bazy danych SQL platformy Azure są domyślnie skonfigurowane z włączoną funkcją TDE. Aby uzyskać więcej informacji na temat TDE, zobacz [Przezroczyste szyfrowanie danych za pomocą usługi Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integralność kopii zapasowej

Na bieżąco zespół inżynierów usługi Azure SQL Database automatycznie testuje przywracanie automatycznych kopii zapasowych baz danych baz danych umieszczonych na serwerach logicznych i pulach elastycznych baz danych. (To badanie nie jest dostępne w wystąpieniu zarządzanym). Po przywróceniu punktu w czasie bazy danych otrzymują również sprawdzanie integralności bazy danych DBCC CHECKDB.

Wystąpienie zarządzane wykonuje `CHECKSUM` automatyczną początkową kopię `RESTORE` zapasową z bazami danych przywróconymi za pomocą polecenia macierzystego lub za pomocą usługi Azure Data Migration Service po zakończeniu migracji.

Wszelkie problemy znalezione podczas sprawdzania integralności spowoduje alert do zespołu inżynierów. Aby uzyskać więcej informacji, zobacz [Integralność danych w bazie danych SQL usługi Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Zgodność

Podczas migracji bazy danych z warstwy usług opartej na UTU do warstwy usług opartej na wynikach wirtualnych przechowywanie PITR jest zachowywane w celu zapewnienia, że zasady odzyskiwania danych aplikacji nie są zagrożone. Jeśli domyślna retencja nie spełnia wymagań dotyczących zgodności, można zmienić okres przechowywania PITR przy użyciu programu PowerShell lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Zmienianie okresu przechowywania kopii zapasowej PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Zmienianie okresu przechowywania kopii zapasowej PITR

Domyślny okres przechowywania kopii zapasowej PITR można zmienić przy użyciu witryny Azure portal, programu PowerShell lub interfejsu API REST. Poniższe przykłady ilustrują, jak zmienić przechowywanie PITR na 28 dni.

> [!WARNING]
> Jeśli skrócisz bieżący okres przechowywania, wszystkie istniejące kopie zapasowe, które są starsze niż nowy okres przechowywania nie są już dostępne. Jeśli zwiększysz bieżący okres przechowywania, baza danych SQL zachowa istniejące kopie zapasowe do końca dłuższego okresu przechowywania zostanie osiągnięty.

> [!NOTE]
> Te interfejsy API będą miały wpływ tylko na okres przechowywania PITR. Jeśli skonfigurowano LTR dla bazy danych, nie będzie to miało wpływu. Aby uzyskać informacje na temat zmiany okresów przechowywania LTR, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Zmienianie okresu przechowywania kopii zapasowej PITR przy użyciu witryny Azure portal

Aby zmienić okres przechowywania kopii zapasowej PITR przy użyciu witryny Azure portal, przejdź do obiektu serwera, którego okres przechowywania chcesz zmienić w portalu. Następnie wybierz odpowiednią opcję na podstawie obiektu serwera, który modyfikujesz.

#### <a name="single-database-and-elastic-database-pools"></a>[Pojedyncze bazy danych i elastyczne pule baz danych](#tab/single-database)

Zmiany w retencji kopii zapasowej PITR dla pojedynczych baz danych SQL platformy Azure są wykonywane na poziomie serwera. Zmiany wprowadzone na poziomie serwera dotyczą baz danych na serwerze. Aby zmienić przechowywanie PITR dla serwera bazy danych SQL platformy Azure z witryny Azure portal, przejdź do bloku przeglądu serwera. Wybierz **pozycję Zarządzaj kopiami zapasowymi** w lewym okienku, a następnie wybierz pozycję **Konfiguruj retencję** u góry ekranu:

![Zmiana retencji PITR, poziom serwera](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

Zmiany w retencji kopii zapasowej PITR dla wystąpień zarządzanych bazy danych SQL są wykonywane na poziomie poszczególnych baz danych. Aby zmienić przechowywanie kopii zapasowej PITR dla bazy danych wystąpienia z witryny Azure portal, przejdź do bloku przeglądu poszczególnych baz danych. Następnie wybierz pozycję **Konfiguruj przechowywanie kopii zapasowych** u góry ekranu:

![Zmienianie retencji PITR, wystąpienie zarządzane](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Zmienianie okresu przechowywania kopii zapasowej PITR przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Usługi AzureRM programu PowerShell jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać więcej informacji, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az są zasadniczo identyczne z argumentami w modułach AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Zmienianie okresu przechowywania kopii zapasowej PITR przy użyciu interfejsu API REST

#### <a name="sample-request"></a>Przykładowe żądanie

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Treść żądania

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Przykładowa odpowiedź

Kod statusu: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Aby uzyskać więcej informacji, zobacz [Interfejs API REST przechowywania kopii zapasowych](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Następne kroki

- Kopie zapasowe bazy danych są istotną częścią każdej strategii ciągłości biznesowej i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Aby dowiedzieć się więcej o innych rozwiązaniach ciągłości biznesowej usługi Azure SQL Database, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).
- Uzyskaj więcej informacji o tym, jak [przywrócić bazę danych do punktu w czasie przy użyciu witryny Azure portal](sql-database-recovery-using-backups.md).
- Uzyskaj więcej informacji o tym, jak [przywrócić bazę danych do punktu w czasie za pomocą programu PowerShell](scripts/sql-database-restore-database-powershell.md).
- Aby uzyskać informacje dotyczące konfigurowania, zarządzania i przywracania z długotrwałego przechowywania automatycznych kopii zapasowych w magazynie obiektów Blob platformy Azure przy użyciu witryny Azure portal, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych przy użyciu portalu Azure.](sql-database-long-term-backup-retention-configure.md)
- Aby uzyskać informacje dotyczące konfigurowania, zarządzania i przywracania z długotrwałego przechowywania automatycznych kopii zapasowych w magazynie obiektów Blob platformy Azure przy użyciu programu PowerShell, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych przy użyciu programu PowerShell](sql-database-long-term-backup-retention-configure.md).
