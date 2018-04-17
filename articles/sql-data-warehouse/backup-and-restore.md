---
title: Azure SQL Data Warehouse w kopii zapasowej i przywracania - migawki geograficznie nadmiarowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa i przywracania kopii zapasowych w usłudze Azure SQL Data Warehouse. Użyj magazynu kopii zapasowych danych do przywrócenia magazynu danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowego kopii zapasowych do przywrócenia w innym regionie geograficznym.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/11/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 21708f51d09d640721af196d2ffa91aede97ffb3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Kopia zapasowa i przywracanie w magazynie danych SQL Azure
Dowiedz się, jak działa i przywracania kopii zapasowych w usłudze Azure SQL Data Warehouse. Użyj magazynu kopii zapasowych danych do przywrócenia magazynu danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowego kopii zapasowych do przywrócenia w innym regionie geograficznym. 

## <a name="what-is-backup-and-restore"></a>Co to jest kopia zapasowa i przywracanie?
A *kopii zapasowej magazynu danych* jest kopii bazy danych używanej do przywrócenia hurtowni danych.  Ponieważ Magazyn danych SQL to Rozproszony system, kopia zapasowa magazynu danych składa się z wielu plików, które znajdują się w magazynie Azure. Kopia zapasowa magazynu danych zawiera migawki lokalnej bazy danych i geograficzna kopie zapasowe baz danych i pliki, które są skojarzone z magazynu danych. 

A *przywracania magazynu danych* jest nowy magazyn danych, która jest tworzona na podstawie istniejącej kopii zapasowej lub usunięte dane magazynu. W magazynie danych przywróconych ponownie tworzy w magazynie kopii zapasowej danych w określonym czasie. Przywrócenie magazynu danych jest integralną część każdej strategii odzyskiwania biznesowych, jak ciągłości i odzyskiwaniem po awarii, ponieważ ponownie tworzy dane po przypadkowym uszkodzenia lub usunięcia.

Przywraca zarówno lokalnych, jak i geograficzne są częścią możliwości odzyskiwania po awarii programu SQL Data Warehouse. 

## <a name="local-snapshot-backups"></a>Tworzenie kopii zapasowych migawka lokalna
Migawka lokalna kopie zapasowe są wbudowane funkcje usługi.  Nie masz je włączyć. 

Magazyn danych SQL trwa migawek magazynu danych w ciągu dnia. Migawki są dostępne na siedem dni. Magazyn danych SQL obsługuje osiem godzin cel punktu odzyskiwania (RPO). Magazyn danych w regionie podstawowym można przywrócić do dowolnego z migawki w ciągu ostatnich siedmiu dni.

Aby wyświetlić podczas uruchamiania Ostatnia migawka, uruchom to zapytanie na online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Migawki przechowywania, gdy magazyn danych jest wstrzymana.
Usługi SQL Data Warehouse nie tworzy migawki i nie wygasa migawek, podczas gdy magazyn danych jest wstrzymana. Wiek migawki nie ulega zmianie, gdy magazyn danych jest wstrzymana. Przechowywania migawki opiera się na liczbę dni, przez które magazynu danych jest w trybie online nie dni kalendarzowych.

Jeśli migawki rozpoczyna się października 1 na 4 pm i magazynu danych zostało wstrzymane 3 października po 16: 00, migawki są maksymalnie dwa dni. Gdy w magazynie danych powróci do trybu online migawka jest dwóch dni. Jeśli w magazynie danych jest dostępna online 5 października godzinie 4, migawki jest dwa dni i pozostaje pięć dni.

Gdy w magazynie danych powróci do trybu online, SQL Data Warehouse wznawia nowych migawek i wygasa migawek, gdy mają one ponad siedem dni danych.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Przechowywania migawki po upuszczeniu magazynu danych
Po upuszczeniu magazyn danych SQL Data Warehouse tworzy migawkę końcowego i zapisze go na siedem dni. Magazyn danych można przywrócić do punktu końcowego przywracania utworzonego w dniu usunięcia. 

> [!IMPORTANT]
> Usunięcie logicznej wystąpienia programu SQL server, wszystkie bazy danych, które należą do tego wystąpienia, również zostaną usunięte i nie może zostać odzyskany. Nie można przywrócić usuniętego serwera.
> 

## <a name="geo-backups"></a>Kopie zapasowe Geo
Usługa SQL Data Warehouse wykonuje kopię zapasową geograficznie raz dziennie w celu [centrum danych sparowanego](../best-practices-availability-paired-regions.md). Cel punktu odzyskiwania dla przywracaniem geograficznym wynosi 24 godziny. Można przywrócić kopii zapasowej z magazynu geograficznie do serwera w regionie łączyć geo. Kopia zapasowa geograficznie gwarantuje, że można przywrócić magazynu danych, w przypadku, gdy nie masz dostępu do migawki w danym regionie podstawowym.

Kopie zapasowe geograficznie są domyślnie. Jeśli magazyn danych jest zoptymalizowana pod kątem elastyczność, możesz [zrezygnować](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) w razie potrzeby. Nie można zrezygnować z geograficznie kopii zapasowych z zoptymalizowane dla warstwy wydajności obliczeniowej.

## <a name="backup-costs"></a>Koszty kopii zapasowej
Zauważ, że rachunku Azure ma elementu wiersza dla usługi Azure Premium Storage i elementu wiersza dla magazynu geograficznie nadmiarowego. Magazyn w warstwie Premium jest całkowity koszt przechowywania danych w regionie podstawowym, w tym migawki.  Opłata geograficznie nadmiarowego obejmuje koszt przechowywania kopii zapasowych geo.  

Łączny koszt dla magazynu danych podstawowych i siedem dni migawek obiektów Blob platformy Azure jest zaokrąglana do najbliższego TB. Na przykład jeśli magazyn danych jest 1,5 TB i migawki używają 100 GB, są rozliczane za 2 TB danych stawkami Azure Premium Storage. 

> [!NOTE]
> Każda migawka jest początkowo pusta i zwiększa się w miarę wprowadzania zmian w magazynie danych podstawowych. Wszystkie migawki wzrost rozmiaru jako zmiany magazynu danych. W związku z tym kosztów magazynowania dla migawek wzrostu zgodnie z szybkość zmian.
> 
> 

Jeśli korzystasz z magazynu geograficznie nadmiarowego, pojawi się opłat oddzielnie. Magazyn geograficznie nadmiarowy jest rozliczane według stawki standardowe dostęp do odczytu geograficznie magazynu geograficznie nadmiarowego (RA-GRS).

Aby uzyskać więcej informacji na temat cen usługi SQL Data Warehouse, zobacz [cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restoring-from-restore-points"></a>Przywracanie z punktów przywracania
Każda migawka ma punkt przywracania, który reprezentuje uruchomienia migawki. Aby przywrócić magazynu danych, wybierz punkt przywracania i wydać polecenie restore.  

Usługi SQL Data Warehouse zawsze przywrócenie kopii zapasowej do nowego magazynu danych. Możesz zachować w magazynie danych przywróconych i bieżący lub usuń jedno z nich. Jeśli chcesz zamienić bieżącego magazynu danych z magazynem przywróconych danych, można zmienić za pomocą [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) z opcją zmodyfikować nazwę. 

Aby przywrócić magazynu danych, zobacz [przywrócić hurtowni danych przy użyciu portalu Azure](sql-data-warehouse-restore-database-portal.md), [przywrócić hurtowni danych przy użyciu programu PowerShell](sql-data-warehouse-restore-database-powershell.md), lub [przywrócić hurtowni danych przy użyciu T-SQL](sql-data-warehouse-restore-database-rest-api.md) .

Aby przywrócić hurtowni danych usuniętych lub wstrzymana, możesz [Utwórz bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Przywracanie geograficznie nadmiarowego
Magazyn danych można przywrócić na dowolny region obsługujący magazyn danych SQL Azure o takim samym poziomie wydajności wybrany. 

> [!NOTE]
> Aby wykonać przywracanie geograficznie nadmiarowego musi nie rezygnujesz z tej funkcji.
> 
> 

## <a name="restore-timeline"></a>Przywróć osi czasu
W ciągu ostatnich siedmiu dni, można przywrócić bazy danych do dowolnego punktu przywracania dostępne. Migawki Uruchom co cztery do ośmiu godzin i są dostępne na siedem dni. Gdy migawka jest starsza niż siedmiu dni, wygaśnie i jego punktu przywracania nie jest już dostępny. 

Tworzenie kopii zapasowych odbywa się przed hurtowni danych wstrzymania. Jeśli magazyn danych jest wstrzymana na ponad siedem dni, nie ma żadnych punktów przywracania. 

## <a name="restore-costs"></a>Przywróć kosztów
Opłata magazynu dla magazynu danych przywróconych jest rozliczane według stawki Azure Premium Storage. 

Jeśli magazynu przywróconych danych zostanie wstrzymana, są naliczane opłaty za magazyn szybkością Azure Premium Storage. Zaletą wstrzymanie jest nie są naliczane opłaty za zasoby obliczeniowe.

Aby uzyskać więcej informacji na temat cen usługi SQL Data Warehouse, zobacz [cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Przywróć przypadki użycia
Podstawowym zastosowaniem przywracania magazynu danych jest odzyskiwanie danych po przypadkowej utraty danych lub uszkodzenia. Umożliwia także przywracania magazynu danych do zachowania kopii zapasowej przez ponad siedem dni. Po przywróceniu kopii zapasowej online ma w magazynie danych i rozwiązaniem przez nieokreślony czas w celu ograniczenia kosztów obliczeń. Opłaty za magazyn szybkością Azure Premium Storage wiąże się z wstrzymanej bazy danych. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o planowaniu po awarii, zobacz [omówienie ciągłości działalności biznesowej](../sql-database/sql-database-business-continuity.md)
