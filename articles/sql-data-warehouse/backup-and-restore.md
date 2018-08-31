---
title: Usługa Azure SQL Data Warehouse w kopii zapasowej i przywracania - migawek magazynu geograficznie nadmiarowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa i przywracania kopii zapasowych w usłudze Azure SQL Data Warehouse. Użycie magazynu kopii zapasowych danych można przywrócić magazyn danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych do przywrócenia w innym regionie geograficznym.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/24/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e9b5005fad1eeb13314e1fb6a5708bb02b96cbf9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248668"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Kopia zapasowa i przywracanie usługi Azure SQL Data Warehouse
Dowiedz się, jak działa i przywracania kopii zapasowych w usłudze Azure SQL Data Warehouse. Użyj danych magazynu migawek do odzyskiwania lub skopiuj magazynu danych do poprzedniego punktu przywracania w regionie podstawowym. Użyj danych, Magazyn geograficznie nadmiarowych kopii zapasowych do przywrócenia w innym regionie geograficznym. 

## <a name="what-is-a-data-warehouse-snapshot"></a>Co to jest migawki magazynu danych?
A *migawki magazynu danych* tworzy punkt przywracania, można wykorzystać, aby odzyskać lub kopii Hurtownia danych do poprzedniego stanu.  Ponieważ usługa SQL Data Warehouse to Rozproszony system, migawek magazynu danych składa się z wielu plików, które znajdują się w usłudze Azure storage. Migawki przechwytują przyrostowe zmiany w danych przechowywanych w magazynie danych.

A *przywracania z magazynu danych* nowego magazynu danych, który jest tworzony z punktu przywracania do istniejącego lub usunięty magazyn danych. Przywracanie magazynu danych jest fundamentalna część każdej strategii odzyskiwania firmy, jak ciągłość działalności biznesowej i odzyskiwanie po awarii, ponieważ ponownie tworzy dane po przypadkowym uszkodzeniem lub usunięciem. Magazyn danych jest również zaawansowanym mechanizmem służącym do tworzenia kopii magazynu danych do celów testowych lub badawczych.  Usługa SQL Data Warehouse używa mechanizmów szybkiego przywracania, w tym samym regionie, który został zmierzony na mniej niż 20 minut dla dowolnego rozmiaru danych. 

## <a name="automatic-restore-points"></a>Automatyczne punkty przywracania
Migawki to wbudowana funkcja usług, co powoduje utworzenie punktów przywracania. Nie masz umożliwiających włączenie tej funkcji. Obecnie automatyczne punkty przywracania nie można usunąć przez użytkowników, których używa usługi, przywracania wskazuje Obsługa umowy SLA dla odzyskiwania.

Usługa SQL Data Warehouse tworzy migawki magazynu danych w ciągu dnia, tworzenie punktów przywracania, które są dostępne przez siedem dni. Nie można zmienić tego okresu przechowywania. Usługa SQL Data Warehouse obsługuje osiem godzin cel punktu odzyskiwania (RPO). Możesz przywrócić magazyn danych w regionie głównym, z jednego z migawki wykonane w ciągu ostatnich siedmiu dni.

Aby sprawdzić, podczas uruchamiania Ostatnia migawka, należy uruchomić to zapytanie w trybie online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika
Ta funkcja umożliwia ręcznie wyzwalacza migawki do utworzenia punktów przywracania z magazynu danych, przed i po duże zmiany. Ta funkcja zapewnia, że punkty przywracania są logicznie spójnego zapewniającą dodatkowej ochrony danych w przypadku dowolnego obciążenia przerw i błędy użytkowników dla czasu Szybkie odzyskiwanie. Punkty przywracania na zdefiniowanych przez użytkownika są dostępne przez siedem dni i są automatycznie usuwane w Twoim imieniu. Nie można zmienić okres przechowywania punktów przywracania zdefiniowanych przez użytkownika. Tylko 42 punkty przywracania na zdefiniowanych przez użytkownika są obsługiwane w dowolnym momencie w czasie, więc muszą być [usunięte](https://go.microsoft.com/fwlink/?linkid=875299) przed utworzeniem innego punktu przywracania. Możesz wyzwolić migawki do utworzenia punktów przywracania zdefiniowanych przez użytkownika za pośrednictwem [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) lub w witrynie Azure Portal.


> [!NOTE]
> Jeśli potrzebujesz więcej niż 7 dni punktów przywracania, należy głosowania dla tej funkcji [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Można również utworzenie punktu przywracania zdefiniowanych przez użytkownika i Przywróć z punktu przywracania nowo utworzony do nowego magazynu danych. Po przywróceniu, masz w magazynie danych online i rozwiązaniem przez czas nieokreślony, aby zapisać koszty operacji obliczeniowych. Wstrzymano bazę danych spowoduje naliczenie opłaty za magazyn zgodnie ze stawką usługi Azure Premium Storage. Aktywna kopia magazynu przywróconych danych, należy można wznowić, które powinny zająć tylko kilka minut.
>

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Migawki przechowywania, gdy magazyn danych jest wstrzymany
Usługa SQL Data Warehouse nie powoduje utworzenia migawki, a nie wygasa punktów przywracania, gdy magazyn danych jest wstrzymany. Przywróć punkty nie należy zmieniać, gdy magazyn danych jest wstrzymany. Przywracanie punktu przechowywania opiera się na liczbę dni, przez które Magazyn danych jest w trybie online, nie w dni kalendarzowe.

Na przykład jeśli migawka rozpoczyna się 1 października od 16: 00 i magazyn danych jest wstrzymany 3 października o 16: 00, punkty przywracania są maksymalnie dwa dni. Gdy magazyn danych powróci do trybu online punkt przywracania jest dwa dni. Jeśli w magazynie danych powróci do trybu online 5 października o 16: 00, punkt przywracania jest dwa dni i pozostaje pięć dni.

Gdy magazyn danych powróci do trybu online, usługa SQL Data Warehouse wznawia, tworząc nowe punkty przywracania i wygasa je, gdy mają więcej niż siedem dni danych.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Migawki przechowywania po upuszczeniu magazynu danych
Gdy upuścisz suszarkę hurtowni danych, usługa SQL Data Warehouse tworzy migawkę końcowe i zapisuje go przez siedem dni. Magazyn danych można przywrócić do punktu końcowego przywracania utworzone na usunięcie. 

> [!IMPORTANT]
> Jeśli usuniesz logiczne wystąpienie programu SQL server, wszystkie bazy danych, które należą do wystąpienia również zostaną usunięte i nie można odzyskać. Nie można przywrócić usuniętego serwera.
>

## <a name="geo-backups"></a>Kopie zapasowe geograficznie
Usługa SQL Data Warehouse wykonuje geograficznej kopii zapasowej raz dziennie w celu [sparowanym centrum danych](../best-practices-availability-paired-regions.md). Cel punktu odzyskiwania dla przywracania geograficznego wynosi 24 godziny. Geograficznej kopii zapasowej można przywrócić serwer w każdym innym regionie, w których usługa SQL Data Warehouse jest obsługiwane. Geograficznej kopii zapasowej gwarantuje, że można przywrócić magazyn danych, w przypadku, gdy nie masz dostępu do punktów przywracania w regionie podstawowym.

Kopie zapasowe geograficznej są domyślnie włączone. Jeśli magazyn danych jest Gen1, możesz to zrobić [zrezygnować](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) w razie potrzeby. Nie możesz zrezygnować z kopii zapasowych geograficznej dla Gen2 ochrony danych jest wbudowaną gwarantowane.

> [!NOTE]
> Jeśli potrzebujesz ustawić częstsze punkty odzyskiwania dla kopii zapasowych geograficznie, Zagłosuj na tę możliwość [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse). Można również utworzenie punktu przywracania zdefiniowanych przez użytkownika i przywracanie z punktu przywracania nowo utworzony nowy magazyn danych w innym regionie. Po przywróceniu, masz w magazynie danych online i rozwiązaniem przez czas nieokreślony, aby zapisać koszty operacji obliczeniowych. Wstrzymano bazę danych spowoduje naliczenie opłaty za magazyn zgodnie ze stawką usługi Azure Premium Storage. a następnie Wstrzymaj. Potrzebujesz kopii aktywnej, magazynu danych, możesz wznowić, które powinny zająć tylko kilka minut.
>


## <a name="backup-and-restore-costs"></a>Koszty i przywracania kopii zapasowych
Zauważ, że rachunku za platformę Azure zawiera element wiersza dla magazynu i element wiersza dla magazynu odzyskiwania po awarii. Opłata za magazyn jest całkowity koszt przechowywania danych w regionie podstawowym oraz zmiany przyrostowe przechwycone przez migawki. Bardziej szczegółowy opis w sposób migawki są obecnie pobierane, można znaleźć na stronie to [dokumentacji](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Geograficznie nadmiarowy opłaty po awarii obejmuje koszt do przechowywania kopii zapasowych geograficznie.  

Łączny koszt magazynu danych podstawowych i siedmiu dni zmian migawki jest zaokrąglana do najbliższego TB. Na przykład jeśli magazyn danych to 1,5 TB, a migawki przechwytuje 100 GB, są rozliczane za 2 TB danych według stawek usługi Azure Premium Storage. 

Jeśli używasz magazynu geograficznie nadmiarowego otrzymasz oddzielna opłata przechowywanie. Magazyn geograficznie nadmiarowy jest rozliczana zgodnie ze standardową stawką geograficznie magazyn geograficznie nadmiarowy (RA-GRS).

Aby uzyskać więcej informacji na temat cennik usługi SQL Data Warehouse, zobacz [cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) i [ruch wychodzący opłaty](https://azure.microsoft.com/pricing/details/bandwidth/) podczas przywracania między regionami.

## <a name="restoring-from-restore-points"></a>Przywracanie z punktów przywracania
Każda migawka tworzy punkt przywracania, który reprezentuje czas uruchomienia migawki. Aby przywrócić magazyn danych, wybierz punkt przywracania i wydać polecenie restore.  

Możesz zachować magazynu przywróconych danych i bieżący lub usuń jedno z nich. Jeśli chcesz zamienić bieżącego magazynu danych w magazynie przywróconych danych, można zmienić za pomocą [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) z opcją zmodyfikować nazwę. 

Aby przywrócić magazyn danych, zobacz [Przywracanie magazynu danych przy użyciu witryny Azure portal](sql-data-warehouse-restore-database-portal.md), [Przywracanie magazynu danych przy użyciu programu PowerShell](sql-data-warehouse-restore-database-powershell.md), lub [Przywracanie magazynu danych przy użyciu języka T-SQL](sql-data-warehouse-restore-database-rest-api.md) .

Aby przywrócić magazyn danych usuniętych lub wstrzymana, możesz [Utwórz bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Przywracanie magazynu geograficznie nadmiarowego
Możesz [Przywracanie magazynu danych](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) do dowolnego regionu obsługi usługa SQL Data Warehouse o takim samym poziomie wydajności wybrany. 

> [!NOTE]
> Do magazynu geograficznie nadmiarowego przywracania musi nie rezygnujesz z tej funkcji.
>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o planowaniu po awarii, zobacz [omówienie ciągłości działania](../sql-database/sql-database-business-continuity.md)
