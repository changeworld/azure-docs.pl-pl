---
title: Automatyczne nadmiarowe kopie zapasowe
description: SQL Database automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i używa magazynu geograficznie nadmiarowego do odczytu platformy Azure na potrzeby nadmiarowości geograficznej.
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
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461780"
---
# <a name="automated-backups"></a>Automatyczne kopie zapasowe

SQL Database automatycznie tworzy kopie zapasowe bazy danych przechowywane przez czas trwania skonfigurowanego okresu przechowywania i korzysta z [magazynu geograficznie nadmiarowego platformy Azure do odczytu (RA-GRS)](../storage/common/storage-redundancy.md) , aby upewnić się, że są one zachowywane, nawet jeśli centrum danych jest niedostępne. Te kopie zapasowe są tworzone automatycznie. Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Jeśli reguły zabezpieczeń wymagają, aby kopie zapasowe były dostępne przez dłuższy czas (do 10 lat), można skonfigurować [długoterminowe przechowywanie](sql-database-long-term-retention.md) pojedynczych baz danych i pul elastycznych.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co to jest SQL Database kopia zapasowa

SQL Database używa technologii SQL Server do tworzenia [pełnych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12 godzin i [kopii zapasowych dziennika transakcji](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5-10 minut. Kopie zapasowe są przechowywane w obiektach [blob magazynu RA-GRS](../storage/common/storage-redundancy.md) , które są replikowane do [sparowanego centrum danych](../best-practices-availability-paired-regions.md) w celu ochrony przed awarią centrum danych. W przypadku przywracania bazy danych usługa określa, które kopie zapasowe pełnych, różnicowych i dzienników transakcji muszą zostać przywrócone.

Tych kopii zapasowych można użyć do:

- **Przywróć istniejącą bazę danych do punktu w czasie w ciągu ostatnich** w okresie przechowywania przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. W przypadku pojedynczej bazy danych i pul elastycznych ta operacja spowoduje utworzenie nowej bazy danych na tym samym serwerze, na którym znajduje się oryginalna baza danych. W wystąpieniu zarządzanym ta operacja umożliwia utworzenie kopii bazy danych lub tego samego lub innego wystąpienia zarządzanego w ramach tej samej subskrypcji.
- **Przywracanie usuniętej bazy danych do czasu jej usunięcia** lub w dowolnym momencie w okresie przechowywania. Usunięta baza danych może zostać przywrócona tylko na tym samym serwerze logicznym lub wystąpieniu zarządzanym, w którym została utworzona oryginalna baza danych.
- **Przywracanie bazy danych do innego regionu geograficznego**. Funkcja przywracania geograficznego umożliwia odzyskanie danych po awarii geograficznej, gdy nie można uzyskać dostępu do serwera i bazy danych. Tworzy nową bazę danych na dowolnym istniejącym serwerze w dowolnym miejscu na świecie.
- **Przywracanie bazy danych z określonej długoterminowej kopii zapasowej** na pojedyncza baza danych lub Pula elastyczna, jeśli baza danych została skonfigurowana z użyciem długoterminowych zasad przechowywania. LTR umożliwia przywrócenie starej wersji bazy danych przy użyciu [Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) lub [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) w celu spełnienia żądania zgodności lub w celu uruchomienia starej wersji aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).
- Aby wykonać przywracanie, zobacz [przywracanie bazy danych z kopii zapasowych](sql-database-recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure Storage termin *replikacja* dotyczy kopiowania plików z jednej lokalizacji do innej. *Replikacja bazy danych* SQL dotyczy utrzymywania synchronizacji wielu pomocniczych baz danych z podstawową bazą danych.

Niektóre z tych operacji można wypróbować, korzystając z następujących przykładów:

| | Witryna Azure Portal | Azure PowerShell |
|---|---|---|
| Zmień przechowywanie kopii zapasowych | [pojedyncza baza danych](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Wystąpienie zarządzane](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [pojedyncza baza danych](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Zmiana długoterminowego przechowywania kopii zapasowych | [Pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane — nie dotyczy  | [pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md)<br/>Wystąpienie zarządzane — nie dotyczy  |
| Przywracanie bazy danych z punktu w czasie | [Pojedyncza baza danych](sql-database-recovery-using-backups.md#point-in-time-restore) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Przywracanie usuniętej bazy danych | [Pojedyncza baza danych](sql-database-recovery-using-backups.md) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Przywracanie bazy danych z usługi Azure Blob Storage | Pojedyncza baza danych — nie dotyczy <br/>Wystąpienie zarządzane — nie dotyczy  | Pojedyncza baza danych — nie dotyczy <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

SQL Database obsługuje samoobsługowe przywracanie do punktu w czasie (kopie) przez automatyczne tworzenie pełnych kopii zapasowych, różnicowych kopii zapasowych i kopii zapasowych dziennika transakcji. Tworzenie pełnych kopii zapasowych bazy danych jest tworzone co tydzień, różnicowe kopie zapasowe bazy danych są zwykle tworzone co 12 godzin, a kopie zapasowe dziennika transakcji są zwykle tworzone co 5-10 minut, z częstotliwością opartą na wielkości i liczbie działań związanych z bazą danych. Pierwsza pełna kopia zapasowa jest zaplanowana natychmiast po utworzeniu bazy danych. Zwykle kończy się to w ciągu 30 minut, ale może trwać dłużej, gdy baza danych ma znaczący rozmiar. Na przykład początkowa kopia zapasowa może trwać dłużej w przywróconej bazie danych lub kopii bazy danych. Po wykonaniu pierwszej pełnej kopii zapasowej wszystkie dalsze kopie zapasowe są automatycznie zaplanowane i zarządzane w trybie dyskretnym w tle. Dokładny chronometraż wszystkich kopii zapasowych bazy danych jest określany przez usługę SQL Database w miarę zrównoważenia całkowitego obciążenia systemu. Nie można zmienić ani wyłączyć zadań tworzenia kopii zapasowej.

Kopie zapasowe kopie są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji, zobacz [przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="long-term-retention"></a>Długoterminowe przechowywanie

Pojedyncze i w puli bazy danych oferują możliwość skonfigurowania długoterminowego przechowywania (LTR) dla pełnych kopii zapasowych w okresie do 10 lat w usłudze Azure Blob Storage. Jeśli zasada LTR jest włączona, cotygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić inne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Użycie magazynu zależy od wybranej częstotliwości wykonywania kopii zapasowych i okresów przechowywania. Możesz użyć [kalkulatora cen ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) , aby oszacować koszt magazynu ltr.

Podobnie jak w przypadku kopie, kopie zapasowe LTR są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji, zobacz [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Użycie magazynu kopii zapasowych 

W przypadku pojedynczych baz danych całkowite użycie magazynu kopii zapasowych jest obliczane w następujący sposób:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

W przypadku pul elastycznych łączny rozmiar magazynu kopii zapasowych jest agregowany na poziomie puli i obliczany w następujący sposób:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

Kopie zapasowe, które są starsze niż okres przechowywania, są automatycznie przeczyszczane na podstawie ich sygnatur czasowych. Ze względu na to, że różnicowe kopie zapasowe i kopie zapasowe dzienników wymagają użycia wcześniejszej pełnej kopii zapasowej, są one usuwane razem w tygodniowych fragmentach. 

Azure SQL Database oblicza łączny magazyn kopii zapasowych w magazynie jako wartość zbiorczą. Co godzinę ta wartość jest raportowana w potoku rozliczania na platformie Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego w celu obliczenia zużycia na koniec każdego miesiąca. Po porzucenia bazy danych zużycie zmniejsza się w miarę starzenia się kopii zapasowych. Gdy kopie zapasowe staną się starsze niż okres przechowywania, rozliczenia zostaną zatrzymane. 

   > [!IMPORTANT]
   > Kopie zapasowe bazy danych są przechowywane przez określony okres przechowywania, nawet jeśli baza danych została porzucona. Chociaż porzucanie i ponowne tworzenie bazy danych często może zaoszczędzić na kosztach magazynu i obliczeń, może zwiększyć koszty magazynowania kopii zapasowej, ponieważ zachowujemy kopię zapasową dla określonego okresu przechowywania (co jest co najmniej 7 dni jako minimum) dla każdej porzuconej bazy danych, za każdym razem. 



### <a name="monitor-consumption"></a>Monitorowanie użycia

Każdy typ kopii zapasowej (pełny, różnicowa i log) jest raportowany w bloku monitorowania bazy danych jako oddzielna Metryka. Na poniższym diagramie przedstawiono sposób monitorowania użycia magazynu kopii zapasowych dla pojedynczej bazy danych. Ta funkcja jest obecnie niedostępna dla wystąpień zarządzanych.

![Monitoruj użycie kopii zapasowej bazy danych w bloku monitorowanie bazy danych Azure Portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>Dostosuj użycie magazynu kopii zapasowych

Nadmierne użycie magazynu kopii zapasowych będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych. Można rozważyć wdrożenie niektórych spośród następujących technik dostrajania, aby bardziej ograniczyć użycie magazynu kopii zapasowych:

* Skrócenie [okresu przechowywania kopii zapasowej](#change-pitr-backup-retention-period-using-azure-portal) do minimum możliwego dla Twoich potrzeb.
* Unikaj wykonywania dużych operacji zapisu częściej niż to trzeba, takich jak ponowne kompilacje indeksów.
* W przypadku dużych operacji ładowania danych Rozważ użycie [klastrowanych indeksów magazynu kolumn](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), zmniejszenie liczby indeksów nieklastrowanych, a także uwzględnienie operacji ładowania zbiorczego z liczbą wierszy około 1 000 000.
* W Ogólnego przeznaczenia warstwie usług zainicjowany magazyn danych jest tańszy niż cena nadmiarowego magazynu kopii zapasowych, dzięki czemu klienci, którzy mają ciągle wysokie koszty magazynowania kopii zapasowych, mogą rozważyć zwiększenie magazynu danych w celu zaoszczędzenia na Magazyn kopii zapasowych.
* Użyj bazy danych TempDB w logice ETL do przechowywania tymczasowych wyników zamiast tabel trwałych (dotyczy tylko wystąpienia zarządzanego).
* Należy rozważyć wyłączenie szyfrowania TDE dla baz danych, które nie zawierają poufnych danych (np. programistycznych lub testowych baz danych, na przykład). Kopie zapasowe nieszyfrowanych baz danych są zazwyczaj kompresowane z wyższym współczynnikiem kompresji.

> [!IMPORTANT]
> W przypadku analiz, składnicy danych \ obciążenia magazynu danych zdecydowanie zaleca się użycie [klastrowanych indeksów magazynu kolumn](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), zmniejszenie liczby indeksów nieklastrowanych, a także uwzględnienie operacji ładowania zbiorczego z liczbą wierszy około 1 000 000 w celu zmniejszenia nadmiernego zużycia magazynu kopii zapasowych.


## <a name="storage-costs"></a>Koszty magazynowania

Cena magazynu zależy od modelu DTU lub modelu rdzeń wirtualny. 

### <a name="dtu-model"></a>Model DTU

Nie ma dodatkowej opłaty za magazyn kopii zapasowych baz danych i pul elastycznych przy użyciu modelu DTU. 

### <a name="vcore-model"></a>Model rdzenia wirtualnego

W przypadku pojedynczych baz danych jest dostępna minimalna ilość miejsca w magazynie kopii zapasowych równa 100% rozmiaru bazy danych. W przypadku pul elastycznych i wystąpień zarządzanych minimalna ilość miejsca w magazynie kopii zapasowej równa 100% przydzielony magazyn danych dla puli lub rozmiaru wystąpienia jest zapewniana bez dodatkowych opłat. Opłata za dodatkowe użycie magazynu kopii zapasowych będzie naliczana w GB/miesiąc. To dodatkowe zużycie będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych.

Usługa Azure SQL DB obliczy całkowity magazyn kopii zapasowych w magazynie jako wartość zbiorczą. Co godzinę ta wartość jest raportowana w potoku rozliczeń platformy Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego w celu uzyskania zużycia na koniec każdego miesiąca. Po porzucenia bazy danych zmniejszamy użycie jako wiek kopii zapasowych. Rozliczenia zostaną zatrzymane, gdy staną się starsze niż okres przechowywania. Ponieważ wszystkie kopie zapasowe dzienników i różnicowe kopie zapasowe są zachowywane przez pełny okres przechowywania, bazy danych, które są wielokrotnie modyfikowane, będą mieć wyższe opłaty za kopie zapasowe. 

Załóżmy, że baza danych ma 744 GB miejsca w magazynie kopii zapasowych, a ta kwota pozostaje stała przez cały miesiąc. Aby przekonwertować zbiorcze użycie magazynu na godzinę, należy podzielić ją na 744,0 (31 dni miesięcznie * 24 godziny dziennie). W takim przypadku baza danych SQL Database wykorzystano 1 GB kopie kopii zapasowej co godzinę. Rozliczenia na platformie Azure zostaną zagregowane i zostanie wyświetlone użycie 744 GB przez cały miesiąc i koszt w oparciu o stawkę $/GB/mo w Twoim regionie. 

Teraz bardziej skomplikowany przykład. Załóżmy, że czas przechowywania bazy danych wzrósł do 14 dni w środku miesiąca, a to (teoretycznie) skutkuje łącznym magazynem kopii zapasowych Podwajanie do 1488 GB. Baza danych SQL może zgłosić 1 GB użycia w godzinach 1-372, a następnie zgłosić użycie jako 2 GB dla godzin 373-744. Ta wartość zostanie zagregowana jako końcowy rachunek 1116 GB/miesiąc. 

### <a name="monitor-costs"></a>Monitorowanie kosztów

Aby zrozumieć koszty magazynu kopii zapasowych, przejdź do pozycji **Cost Management + rozliczenia** na Azure Portal, wybierz pozycję **Cost Management**, a następnie wybierz pozycję **Analiza kosztów**. Wybierz żądaną subskrypcję jako **zakres**, a następnie odfiltruj odpowiedni czas i usługę. 

Dodaj filtr dla **nazwy usługi**, a następnie z listy rozwijanej wybierz pozycję **baza danych SQL** . Użyj filtru **podkategorii mierników** , aby wybrać licznik rozliczeń dla usługi. W przypadku pojedynczej bazy danych lub puli elastycznej wybierz **jedną/elastyczną pulę kopie Backup Storage**. W przypadku wystąpienia zarządzanego wybierz pozycję **mi kopie Backup Storage**. Podkategorie **magazynu** i **obliczeń** mogą również być przydatne, chociaż nie są one skojarzone z kosztami magazynu kopii zapasowych. 

![Analiza kosztów magazynu kopii zapasowych](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>Przechowywanie kopii zapasowych

Wszystkie bazy danych Azure SQL Database (pojedyncze, w puli i zarządzane wystąpienia bazy danych) mają domyślny okres przechowywania kopii zapasowych wynoszący **siedem** dni. [Okres przechowywania kopii zapasowej można zmienić do 35 dni](#change-pitr-backup-retention-period).

Jeśli usuniesz bazę danych, SQL Database będą zachować kopie zapasowe w taki sam sposób, jak w przypadku bazy danych w trybie online. Na przykład po usunięciu podstawowej bazy danych, która ma okres przechowywania wynoszący siedem dni, kopia zapasowa, która jest starsza niż cztery dni, jest zapisywana przez trzy więcej dni.

Jeśli chcesz zachować kopie zapasowe dłużej niż maksymalny okres przechowywania, możesz zmodyfikować właściwości kopii zapasowej, aby dodać jeden lub więcej długoterminowych okresów przechowywania do bazy danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Usunięcie programu Azure SQL Server, który hostuje bazy danych SQL, spowoduje również usunięcie wszystkich pul elastycznych i baz danych, które należą do serwera, i nie będzie można ich odzyskać. Nie można przywrócić usuniętego serwera. Jeśli jednak skonfigurowano długoterminowe przechowywanie, kopie zapasowe baz danych z tą literą nie zostaną usunięte i można przywrócić te bazy danych.

## <a name="encrypted-backups"></a>Szyfrowane kopie zapasowe

Jeśli baza danych jest zaszyfrowana przy użyciu TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku, łącznie z kopiami zapasowymi LTR. Gdy TDE jest włączona dla bazy danych Azure SQL, kopie zapasowe również są szyfrowane. Wszystkie nowe bazy danych SQL Azure są domyślnie skonfigurowane z włączoną funkcją TDE. Aby uzyskać więcej informacji na temat TDE, zobacz [transparent Data Encryption z Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integralność kopii zapasowych

W związku z tym zespół inżynierów Azure SQL Database automatycznie testuje przywracanie zautomatyzowanych kopii zapasowych baz danych znajdujących się na serwerach logicznych i w pulach elastycznych (nie jest to dostępne w wystąpieniu zarządzanym). Po przywróceniu do momentu bazy danych otrzymują również kontrolę integralności przy użyciu polecenia DBCC CHECKDB.

Wystąpienie zarządzane pobiera automatycznie początkową kopię zapasową z `CHECKSUM` baz danych przywróconych przy użyciu natywnego polecenia `RESTORE` lub usługi migracji danych po zakończeniu migracji.

Wszelkie problemy znalezione podczas kontroli integralności spowodują powstanie alertu dla zespołu inżynieryjnego. Aby uzyskać więcej informacji na temat integralności danych w Azure SQL Database, zobacz [integralność danych w Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Zgodność

W przypadku migrowania bazy danych z warstwy usług opartych na jednostkach DTU do warstwy usług opartych na rdzeń wirtualny, przechowywanie kopie jest zachowywane w celu zapewnienia, że zasady odzyskiwania danych aplikacji nie zostały naruszone. Jeśli domyślne przechowywanie nie spełnia wymagań dotyczących zgodności, można zmienić okres przechowywania kopie przy użyciu programu PowerShell lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Zmienianie okresu przechowywania kopii zapasowych](#change-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>Zmień okres przechowywania kopii zapasowej kopie

Domyślny okres przechowywania kopii zapasowej kopie można zmienić przy użyciu Azure Portal, programu PowerShell lub interfejsu API REST. W poniższych przykładach pokazano, jak zmienić przechowywanie kopie na 28 dni.

> [!WARNING]
> W przypadku skrócenia bieżącego okresu przechowywania wszystkie istniejące kopie zapasowe starsze niż nowy okres przechowywania nie będą już dostępne. W przypadku zwiększenia bieżącego okresu przechowywania program SQL Database będzie przechowywał istniejące kopie zapasowe do momentu osiągnięcia dłuższego okresu przechowywania.

> [!NOTE]
> Te interfejsy API będą mieć wpływ tylko na kopie okres przechowywania. Jeśli skonfigurowano opcję LTR dla bazy danych, nie będzie to miało wpływu. Aby uzyskać więcej informacji na temat zmiany okresu przechowywania LTR, zobacz [długoterminowe przechowywanie](sql-database-long-term-retention.md)danych.

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Zmień okres przechowywania kopii zapasowej kopie przy użyciu Azure Portal

Aby zmienić kopie okres przechowywania kopii zapasowej przy użyciu Azure Portal, przejdź do obiektu serwera, którego okres przechowywania chcesz zmienić w portalu, a następnie wybierz odpowiednią opcję na podstawie tego, który obiekt serwera jest modyfikowany.

#### <a name="single-database--elastic-pools"></a>[Jedna baza danych & pule elastyczne](#tab/single-database)

Zmiana kopie przechowywania kopii zapasowych dla pojedynczych baz danych Azure SQL Database odbywa się na poziomie serwera. Zmiany wprowadzone na poziomie serwera dotyczą baz danych na tym serwerze. Aby zmienić kopie dla serwera Azure SQL Database z Azure Portal, przejdź do bloku przegląd serwera, kliknij pozycję Zarządzaj kopiami zapasowymi w menu nawigacji, a następnie kliknij pozycję Konfiguruj przechowywanie na pasku nawigacyjnym.

![Zmień Azure Portal kopie](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

Zmiana przechowywania kopii zapasowych kopie dla wystąpienia zarządzanego SQL Database odbywa się na poziomie poszczególnych baz danych. Aby zmienić kopie przechowywanie kopii zapasowych dla bazy danych wystąpienia z Azure Portal, przejdź do bloku przegląd poszczególnych baz danych, a następnie kliknij pozycję Konfiguruj przechowywanie kopii zapasowych na pasku nawigacyjnym.

![Zmień Azure Portal kopie](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Zmienianie okresu przechowywania kopii zapasowych kopie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Zmień okres przechowywania kopie przy użyciu interfejsu API REST

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

Kod stanu: 200

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

Aby uzyskać więcej informacji, zobacz [interfejs API REST przechowywania kopii zapasowych](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Następne kroki

- Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Aby dowiedzieć się więcej na temat innych Azure SQL Database rozwiązań z zakresu ciągłości biznesowej, zobacz temat [ciągłość działania — Omówienie](sql-database-business-continuity.md).
- Aby przywrócić do punktu w czasie za pomocą Azure Portal, zobacz [przywracanie bazy danych do punktu w czasie przy użyciu Azure Portal](sql-database-recovery-using-backups.md).
- Aby przywrócić do punktu w czasie za pomocą programu PowerShell, zobacz [przywracanie bazy danych do punktu w czasie za pomocą programu PowerShell](scripts/sql-database-restore-database-powershell.md).
- Aby skonfigurować, zarządzać i przywracać długoterminowe przechowywanie zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage za pomocą Azure Portal, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych przy użyciu Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Aby skonfigurować, zarządzać i przywracać długoterminowe przechowywanie zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage przy użyciu programu PowerShell, zobacz [Zarządzanie długoterminowym przechowywaniem kopii zapasowych za pomocą programu PowerShell](sql-database-long-term-backup-retention-configure.md).
