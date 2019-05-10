---
title: Usługa Azure SQL Data Warehouse w kopii zapasowej i przywracania - migawek magazynu geograficznie nadmiarowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa i przywracania kopii zapasowych w usłudze Azure SQL Data Warehouse. Użycie magazynu kopii zapasowych danych można przywrócić magazyn danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych do przywrócenia w innym regionie geograficznym.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 914513bc19cc81da29efef12d50a6485233d169f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236578"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Kopia zapasowa i przywracanie usługi Azure SQL Data Warehouse

Dowiedz się, jak używać kopii zapasowej i przywracania w usłudze Azure SQL Data Warehouse. Użyj punktów przywracania magazynu danych do odzyskania lub kopiowania usługi data warehouse do poprzedniego stanu w regionie podstawowym. Użyj danych, Magazyn geograficznie nadmiarowych kopii zapasowych do przywrócenia w innym regionie geograficznym.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co to jest migawki magazynu danych

A *migawki magazynu danych* tworzy punkt przywracania, można wykorzystać, aby odzyskać lub kopii Hurtownia danych do poprzedniego stanu.  Ponieważ usługa SQL Data Warehouse to Rozproszony system, migawek magazynu danych składa się z wielu plików, które znajdują się w usłudze Azure storage. Migawki przechwytują przyrostowe zmiany w danych przechowywanych w magazynie danych.

A *przywracania z magazynu danych* nowego magazynu danych, który jest tworzony z punktu przywracania do istniejącego lub usunięty magazyn danych. Przywracanie magazynu danych jest fundamentalna część każdej strategii odzyskiwania firmy, jak ciągłość działalności biznesowej i odzyskiwanie po awarii, ponieważ ponownie tworzy dane po przypadkowym uszkodzeniem lub usunięciem. Magazyn danych jest również zaawansowanym mechanizmem służącym do tworzenia kopii magazynu danych do celów testowych lub badawczych.  Usługa SQL Data Warehouse przywracania stawki mogą się różnić w zależności od rozmiaru bazy danych i lokalizacji magazynu danych źródłowych i docelowych. Średnio w tym samym regionie szybkości przywracania zwykle po około 20 minut. 

## <a name="automatic-restore-points"></a>Automatyczne punkty przywracania

Migawki to wbudowana funkcja usług, który tworzy punktów przywracania. Nie masz umożliwiających włączenie tej funkcji. Obecnie automatyczne punkty przywracania nie można usunąć przez użytkowników, których używa usługi, przywracania wskazuje Obsługa umowy SLA dla odzyskiwania.

Usługa SQL Data Warehouse tworzy migawki magazynu danych w ciągu dnia, tworzenie punktów przywracania, które są dostępne przez siedem dni. Nie można zmienić tego okresu przechowywania. Usługa SQL Data Warehouse obsługuje cel punktu odzyskiwania ośmiu godzin (RPO). Możesz przywrócić magazyn danych w regionie głównym, z jednego z migawki wykonane w ciągu ostatnich siedmiu dni.

Aby sprawdzić, podczas uruchamiania Ostatnia migawka, należy uruchomić to zapytanie w trybie online SQL Data Warehouse.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

Ta funkcja umożliwia ręcznie wyzwalacza migawki do utworzenia punktów przywracania z magazynu danych, przed i po duże zmiany. Ta funkcja zapewnia, że punkty przywracania są logicznie spójnego zapewniającą dodatkowej ochrony danych w przypadku dowolnego obciążenia przerw i błędy użytkowników dla czasu Szybkie odzyskiwanie. Punkty przywracania na zdefiniowanych przez użytkownika są dostępne przez siedem dni i są automatycznie usuwane w Twoim imieniu. Nie można zmienić okres przechowywania punktów przywracania zdefiniowanych przez użytkownika. **punkty przywracania 42 zdefiniowanych przez użytkownika** jest gwarantowana w dowolnym momencie w czasie, więc muszą być [usunięte](https://go.microsoft.com/fwlink/?linkid=875299) przed utworzeniem innego punktu przywracania. Możesz wyzwolić migawki do utworzenia punktów przywracania zdefiniowanych przez użytkownika za pośrednictwem [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) lub witrynie Azure portal.

> [!NOTE]
> Jeśli potrzebujesz więcej niż 7 dni punktów przywracania, należy głosowania dla tej funkcji [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Można również utworzenie punktu przywracania zdefiniowanych przez użytkownika i Przywróć z punktu przywracania nowo utworzony do nowego magazynu danych. Po przywróceniu, masz w magazynie danych online i rozwiązaniem przez czas nieokreślony, aby zapisać koszty operacji obliczeniowych. Wstrzymano bazę danych spowoduje naliczenie opłaty za magazyn zgodnie ze stawką usługi Azure Premium Storage. Aktywna kopia magazynu przywróconych danych, należy można wznowić, które powinny zająć tylko kilka minut.

### <a name="restore-point-retention"></a>Czas przechowywania punktu przywracania

Poniższe szczegóły listy okresów przechowywania punktów przywracania:

1. Usługa SQL Data Warehouse spowoduje usunięcie punktu przywracania w przypadku trafienia 7-dniowy okres przechowywania **i** gdy istnieją co najmniej 42 łączna liczba punktów przywracania (w tym zarówno zdefiniowanych przez użytkownika i automatycznie)
2. Migawki nie są wykonywane, gdy magazyn danych jest wstrzymany
3. Wiek punkt przywracania jest zaokrąglana w dni kalendarzowe bezwzględny, od chwili, gdy punkt przywracania jest pobierana, łącznie z, gdy magazyn danych jest wstrzymany
4. W dowolnym momencie w czasie magazyn danych jest gwarantowane jest można przechowywać maksymalnie 42 punkty przywracania na zdefiniowanych przez użytkownika i 42 automatyczne punkty przywracania tak długo, jak te punkty przywracania nie osiągnęły 7-dniowy okres przechowywania
5. Jeśli wykonywana jest migawka, Magazyn danych jest następnie wstrzymany w okresie dłuższym niż 7 dni, a następnie wznawia, możliwe jest punkt przywracania i utrzymany do chwili istnieją 42 łączna liczba punktów przywracania (w tym zarówno zdefiniowanych przez użytkownika i automatycznie)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Migawki przechowywania po upuszczeniu magazynu danych

Gdy upuścisz suszarkę hurtowni danych, usługa SQL Data Warehouse tworzy migawkę końcowe i zapisuje go przez siedem dni. Magazyn danych można przywrócić do punktu końcowego przywracania utworzone na usunięcie.

> [!IMPORTANT]
> Jeśli usuniesz logiczne wystąpienie programu SQL server, wszystkie bazy danych, które należą do wystąpienia również zostaną usunięte i nie można odzyskać. Nie można przywrócić usuniętego serwera.

## <a name="geo-backups-and-disaster-recovery"></a>Po awarii i geograficznej kopie zapasowe odzyskiwania

Usługa SQL Data Warehouse wykonuje geograficznej kopii zapasowej raz dziennie w celu [sparowanym centrum danych](../best-practices-availability-paired-regions.md). Cel punktu odzyskiwania dla przywracania geograficznego wynosi 24 godziny. Geograficznej kopii zapasowej można przywrócić serwer w każdym innym regionie, w których usługa SQL Data Warehouse jest obsługiwane. Geograficznej kopii zapasowej gwarantuje, że można przywrócić magazyn danych, w przypadku, gdy nie masz dostępu do punktów przywracania w regionie podstawowym.

Kopie zapasowe geograficznej są domyślnie włączone. Jeśli magazyn danych jest Gen1, możesz to zrobić [zrezygnować](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy) w razie potrzeby. Nie możesz zrezygnować z kopii zapasowych geograficznej dla Gen2 ochrony danych jest wbudowaną gwarantowane.

> [!NOTE]
> Jeśli potrzebujesz ustawić częstsze punkty odzyskiwania dla kopii zapasowych geograficznie, Zagłosuj na tę możliwość [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse). Można również utworzenie punktu przywracania zdefiniowanych przez użytkownika i przywracanie z punktu przywracania nowo utworzony nowy magazyn danych w innym regionie. Po przywróceniu, masz w magazynie danych online i rozwiązaniem przez czas nieokreślony, aby zapisać koszty operacji obliczeniowych. Wstrzymano bazę danych spowoduje naliczenie opłaty za magazyn zgodnie ze stawką usługi Azure Premium Storage. Potrzebujesz kopii aktywnej, magazynu danych, możesz wznowić, które powinny zająć tylko kilka minut.

## <a name="backup-and-restore-costs"></a>Koszty i przywracania kopii zapasowych

Zauważ, że rachunku za platformę Azure zawiera element wiersza dla magazynu i element wiersza dla magazynu odzyskiwania po awarii. Opłata za magazyn jest całkowity koszt przechowywania danych w regionie podstawowym oraz zmiany przyrostowe przechwycone przez migawki. Aby uzyskać bardziej szczegółowy opis jak rozliczane są migawki, zobacz [zrozumienie sposobu migawek naliczania opłat za](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Geograficznie nadmiarowy opłaty po awarii obejmuje koszt do przechowywania kopii zapasowych geograficznie.  

Łączny koszt magazynu danych podstawowych i siedmiu dni zmian migawki jest zaokrąglana do najbliższego TB. Na przykład jeśli magazyn danych to 1,5 TB, a migawki przechwytuje 100 GB, są rozliczane za 2 TB danych według stawek usługi Azure Premium Storage.

Jeśli używasz magazynu geograficznie nadmiarowego otrzymasz oddzielna opłata przechowywanie. Magazyn geograficznie nadmiarowy jest rozliczana zgodnie ze standardową stawką geograficznie magazyn geograficznie nadmiarowy (RA-GRS).

Aby uzyskać więcej informacji na temat cennik usługi SQL Data Warehouse Zobacz [SQL Data Warehouse — cennik]. Nie są naliczane za wyjście danych podczas przywracania w różnych regionach.

## <a name="restoring-from-restore-points"></a>Przywracanie z punktów przywracania

Każda migawka tworzy punkt przywracania, który reprezentuje czas uruchomienia migawki. Aby przywrócić magazyn danych, wybierz punkt przywracania i wydać polecenie restore.  

Możesz zachować magazynu przywróconych danych i bieżący lub usuń jedno z nich. Jeśli chcesz zamienić bieżącego magazynu danych w magazynie przywróconych danych, można zmienić za pomocą [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) z opcją zmodyfikować nazwę.

Aby przywrócić magazyn danych, zobacz [Przywracanie magazynu danych przy użyciu witryny Azure portal](sql-data-warehouse-restore-database-portal.md), [Przywracanie magazynu danych przy użyciu programu PowerShell](sql-data-warehouse-restore-database-powershell.md), lub [Przywracanie magazynu danych przy użyciu interfejsów API REST](sql-data-warehouse-restore-database-rest-api.md).

Aby przywrócić magazyn danych usuniętych lub wstrzymana, możesz [Utwórz bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Przywracanie między

Jeśli trzeba przywrócić w bezpośrednio w subskrypcji, Zagłosuj na tę możliwość [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Przywróć na inny serwer logiczny i ["Przenieś"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) serwera między subskrypcjami do przywracania między subskrypcjami. 

## <a name="geo-redundant-restore"></a>Przywracanie magazynu geograficznie nadmiarowego

Możesz [Przywracanie magazynu danych](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) do dowolnego regionu obsługi usługa SQL Data Warehouse o takim samym poziomie wydajności wybrany.

> [!NOTE]
> Do magazynu geograficznie nadmiarowego przywracania musi nie rezygnujesz z tej funkcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o planowaniu po awarii, zobacz [omówienie ciągłości działania](../sql-database/sql-database-business-continuity.md)
