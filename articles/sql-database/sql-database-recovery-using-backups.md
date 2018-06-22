---
title: Przywracanie z kopii zapasowej bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Więcej informacji na temat punktu w czasie przywracania, która umożliwia przywracanie bazy danych SQL Azure z wcześniejszego punktu w czasie (do 35 dni).
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ad4725ff6871ef489c6a10656af9a76e588edfa1
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308425"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Odzyskiwanie bazy danych Azure SQL przy użyciu kopii zapasowych bazy danych automatycznych
Baza danych SQL oferuje następujące opcje bazy danych odzyskiwania przy użyciu [automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) i [kopie zapasowe w przechowywania długoterminowego](sql-database-long-term-retention.md). Można przywrócić z kopii zapasowej bazy danych, aby:

* Nową bazę danych na tym samym serwerze logicznym odzyskiwane do określonego punktu w czasie w okresie przechowywania. 
* Baza danych na tym samym serwerze logicznym odzyskiwane do czasu usunięcia do usuniętej bazy danych.
* Nową bazę danych na dowolnym serwerze logicznym w dowolnym regionie przywrócona do stanu ostatniej codzienne wykonywanie kopii zapasowych w magazynie obiektów blob z replikacją geograficzną (RA-GRS).

> [!IMPORTANT]
> Nie można zastąpić istniejącą bazę danych podczas przywracania.
>

Przywrócona baza danych generuje koszt dodatkowe miejsce do magazynowania w następujących warunkach: 
- Przywracanie P11 — P15 S4 S12 lub P1 — P6 Jeśli maksymalnego rozmiaru bazy danych jest większa niż 500 GB.
- Przywracanie P1 — P6 do S4 S12 Jeśli maksymalnego rozmiaru bazy danych jest większa niż 250 GB.

Nadmiarowe jest koszt, ponieważ maksymalny rozmiar przywróconej bazy danych jest większa niż ilość miejsca w magazynie uwzględnione na poziomie wydajności i wszelkie dodatkowe miejsce do magazynowania elastycznie powyżej uwzględniona ilość jest bardzo obciążona.  Aby cenach szczegóły dodatkowe miejsce do magazynowania, zobacz [bazy danych SQL cennikiem](https://azure.microsoft.com/pricing/details/sql-database/).  Jeśli rzeczywista ilość miejsca jest mniejsza niż ilość miejsca w magazynie uwzględnione, następnie z żadnymi dodatkowymi kosztami można tego uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględniona ilość.  

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia [kopiowanie bazy danych](sql-database-copy.md). 
>


## <a name="recovery-time"></a>Czas odzyskiwania
Czas odzyskiwania Przywracanie bazy danych przy użyciu kopii zapasowych automatycznych bazy danych jest w pełni funkcjonalne przez kilka czynników: 

* Rozmiar bazy danych
* Poziom wydajności bazy danych
* Liczba zaangażowanych dzienników transakcji
* Liczba działań, który ma być powtórzone odzyskiwania do punktu przywracania
* Przepustowość sieci, w przypadku przywracania w innym regionie 
* Liczba przywracania równoczesnych żądań przetwarzanych w docelowym regionie. 
  
  Dla bardzo dużych i/lub aktywnej bazy danych przywracania może zająć kilka godzin. Jeśli istnieje długimi awaria w regionie, jest to możliwe, że ma dużą liczbę żądań geograficzne przetwarzanych przez innych regionów. Gdy istnieje wiele żądań, czasu odzyskiwania może zwiększyć dla baz danych w tym regionie. Pełne przywracanie większości baz danych w ciągu 12 godzin.

Jednej subskrypcji, istnieją pewne ograniczenia liczby żądań równoczesnych przywracania (w tym punkcie w czasie przywracania, przywracania geograficznie i przywrócenie z długoterminowego przechowywania kopii zapasowej) są przesłane i przystąpiła:
|  | **Maksymalna liczba jednoczesnych żądań przetwarzanych** | **Maksymalna liczba jednoczesnych żądań przesyłaniu** |
| :--- | --: | --: |
|Pojedyncza baza danych (dla subskrypcji)|10|60|
|Puli elastycznej (dla każdej puli)|4|200|
||||

Nie ma żadnych wbudowanych funkcji do zbiorczego przywracania. [Bazy danych SQL Azure: pełne odzyskiwanie serwera](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skrypt jest przykładem jedną z metod wykonasz to zadanie.

> [!IMPORTANT]
> Aby odzyskać przy użyciu automatycznego tworzenia kopii zapasowych, musi być członkiem roli współautora serwera SQL w ramach subskrypcji lub być właścicielem subskrypcji. Odzyskiwanie można przeprowadzić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Można przywrócić istniejącej bazy danych do wcześniejszego punktu w czasie jako nową bazę danych na tym samym serwerze logicznym przy użyciu portalu Azure [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), lub [interfejsu API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Aby uzyskać przykładowy skrypt programu PowerShell przedstawiający sposób wykonywania w momencie przywracania bazy danych, zobacz [Przywróć bazę danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).
>

Wszystkie warstwy lub wydajności poziomu usługi, a jako pojedynczej bazy danych lub w puli elastycznej można przywrócić bazy danych. Upewnij się, że masz wystarczające zasoby na serwerze logicznym lub w puli elastycznej, do której są Przywracanie bazy danych. Po wykonaniu tych czynności przywróconej bazy danych jest normalne, pełni dostępny online bazy danych. Przywrócona baza danych jest pobierana stawkami normalnym na podstawie poziomu wydajności i warstwę usług. Nie wiąże się z opłatami do czasu ukończenia przywracania bazy danych.

Zazwyczaj należy przywrócić bazę danych do wcześniejszego stanu na potrzeby odzyskiwania. Po tej czynności można traktować jako zamiennik oryginalnej bazy danych przywróconej bazy danych lub użyj go, aby pobrać dane, a następnie zaktualizuj oryginalnej bazy danych. 

* ***Baza danych zamiany:*** Jeśli przywróconej bazy danych służy jako serwer zamienny dla oryginalnej bazy danych, należy sprawdzić poziom wydajności i/lub warstwy usług są odpowiednie i skalować bazy danych, jeśli to konieczne. Można zmienić nazwy oryginalnej bazy danych, a następnie nadaj przywróconej bazy danych w oryginalnej nazwy przy użyciu [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) polecenia T-SQL. 
* ***Odzyskiwanie danych:*** Jeśli planujesz do pobierania danych z przywróconej bazy danych, aby odzyskać sprawność po błędzie użytkownika lub aplikacji, należy zapisać i wykonywanie skryptów odzyskiwania danych niezbędnych do wyodrębniania danych z przywróconej bazy danych do oryginalnej bazy danych. Choć operacja przywracania może zająć dużo czasu, przywracanej bazy danych jest widoczna na liście bazy danych w trakcie procesu przywracania. Po usunięciu bazy danych podczas przywracania operacja przywracania została anulowana i nie są naliczane opłaty bazy danych, która nie została ukończona, przywracania. 

### <a name="azure-portal"></a>Azure Portal

Aby odzyskać do punktu w czasie przy użyciu portalu Azure, otwórz stronę bazy danych, a następnie kliknij przycisk **przywrócić** na pasku narzędzi.

![punkt w czasie przywracania](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych
Do czasu usunięcia dla usuniętej bazy danych na tym samym serwerze logicznym przy użyciu portalu Azure, można przywrócić usuniętą bazę [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), lub [REST (createMode = przywracanie)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Można przywrócić usuniętej bazy danych do wcześniejszego punktu w czasie w trakcie przechowywania, przy użyciu [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!TIP]
> Aby uzyskać przykładowy skrypt programu PowerShell przedstawiająca sposób Przywracanie usuniętej bazy danych, zobacz [Przywróć bazę danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> W przypadku usunięcia wystąpienia serwera bazy danych SQL Azure, jej baz danych, również zostaną usunięte i nie może zostać odzyskany. Obecnie nie jest obsługiwane dla przywracanie usuniętych serwera.
> 

### <a name="azure-portal"></a>Azure Portal

Aby odzyskać usuniętej bazy danych podczas jego [okres przechowywania na podstawie jednostek dtu w warstwie modelu](sql-database-service-tiers-dtu.md) lub [okres przechowywania na podstawie vCore modelu](sql-database-service-tiers-vcore.md) przy użyciu portalu Azure, otwórz stronę dla serwera i w obszarze działania, kliknij przycisk **Usunięte bazy danych**.

![usunięte bazy danych — Przywracanie-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![usunięte bazy danych — Przywracanie-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Przywracanie geograficzne
Można przywrócić bazy danych SQL na dowolnym serwerze w dowolnym regionie Azure z najnowszych replikacją geograficzną pełne i różnicowe kopie zapasowe. Geograficzne używa geograficznie nadmiarowej kopii zapasowej jako źródła i można odzyskać bazę danych, nawet jeśli bazy danych lub centrum danych jest niedostępny z powodu awarii. 

Geograficzne jest domyślną opcją odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie, w którym hostowana jest baza danych. Jeśli na dużą skalę zdarzenia w wynikach regionu w niedostępności aplikacji bazy danych, można przywrócić bazy danych z replikacją geograficzną kopii zapasowych na serwerze w innym regionie. Nastąpi opóźnienie między po różnicowej kopii zapasowej jest zajęty i gdy jest replikacją geograficzną Azure blob w innym regionie. To opóźnienie może trwać do godziny, tak, w przypadku awarii można się do godzinę utraty danych. Na poniższej ilustracji przedstawiono przywracania bazy danych z ostatniej kopii zapasowej dostępne w innym regionie.

![geograficzne](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Aby uzyskać przykładowy skrypt programu PowerShell przedstawiający sposób wykonywania przywracaniem geograficznym, zobacz [Przywróć bazę danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

W momencie przywracania na dodatkowej geograficzna nie jest obecnie obsługiwane. W momencie przywracania jest możliwe tylko dla podstawowej bazy danych. Aby uzyskać szczegółowe informacje o korzystaniu z przywracaniem geograficznym odzyskiwania po awarii, zobacz [odzyskiwanie po awarii](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Odzyskiwanie z kopii zapasowych jest najbardziej podstawowa rozwiązań odzyskiwania po awarii dostępne w bazie danych SQL z celem najdłuższym punktu odzyskiwania (RPO) i szacowania czasu odzyskiwania (Wstaw). Rozwiązania z wykorzystaniem mały rozmiar baz danych, (np. podstawowej usługi warstwy lub mały rozmiar baz danych w pule elastyczne dzierżawy) geograficzne jest często rozsądne rozwiązanie odzyskiwania po awarii z Wstaw 12 godzin. Rozwiązania przy użyciu dużych baz danych i wymagają odzyskiwania krótszy czas, należy rozważyć użycie [trybu Failover grup i aktywna replikacja geograficzna](sql-database-geo-replication-overview.md). Aktywna replikacja geograficzna oferuje znacznie niższe RPO i Wstaw jako wystarczy Inicjuj tryb failover do dodatkowej stale zreplikowane. Aby uzyskać więcej informacji dotyczących opcji ciągłości biznesowej, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Azure Portal

Aby przywracaniem geograficznym w bazie danych podczas jego [okres przechowywania na podstawie jednostek dtu w warstwie modelu](sql-database-service-tiers-dtu.md) lub [okres przechowywania na podstawie vCore modelu](sql-database-service-tiers-vcore.md) przy użyciu portalu Azure, otwórz stronę baz danych, a następnie kliknij przycisk **Dodaj** . W **wybierz źródło** pole tekstowe, wybierz **kopii zapasowej**. Określ kopię zapasową, z którego do przeprowadzenia odzyskiwania w regionie i na serwerze wybranych przez użytkownika. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programowo odzyskiwanie przy użyciu automatycznego tworzenia kopii zapasowych
Jak już wspomniano, oprócz portalu Azure programowo przy użyciu programu Azure PowerShell lub interfejsu API REST można wykonać odzyskiwanie bazy danych. W poniższych tabelach opisano zestaw dostępnych poleceń.

### <a name="powershell"></a>PowerShell
| Polecenie cmdlet | Opis |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Pobiera co najmniej jedną bazę danych. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Przywraca bazę danych SQL. |
|  | |

### <a name="rest-api"></a>Interfejs API REST
| Interfejs API | Opis |
| --- | --- |
| [REST (createMode = odzyskiwania)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Przywraca bazę danych |
| [GET, Utwórz lub zaktualizuj stan bazy danych](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Zwraca informacje o stanie podczas operacji przywracania |
|  | |

## <a name="summary"></a>Podsumowanie
Automatyczne kopie zapasowe chronić baz danych użytkownika i błędy aplikacji, usuwać przypadkowemu bazy danych i długimi przestojami. Ta funkcja wbudowana jest dostępna dla wszystkich warstw usług i poziomy wydajności. 

## <a name="next-steps"></a>Kolejne kroki
* Omówienie ciągłości działalności biznesowej i scenariuszy, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL bazy danych automatycznego tworzenia kopii zapasowych, zobacz [bazy danych SQL automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md).
* Aby uzyskać informacje dotyczące długoterminowego przechowywania, zobacz [przechowywania długoterminowego](sql-database-long-term-retention.md).
* Informacje na temat opcji odzyskiwania szybsze, zobacz [trybu Failover grupy i aktywna replikacja geograficzna](sql-database-geo-replication-overview.md).  
