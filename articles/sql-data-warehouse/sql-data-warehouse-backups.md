---
title: Azure SQL Data Warehouse kopii zapasowych - migawki geograficznie nadmiarowego | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kopii zapasowych wbudowaną bazą danych SQL Data Warehouse, umożliwiające przywracanie Azure SQL Data Warehouse punkt przywracania lub inny region geograficzny.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198505"
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Kopia zapasowa i przywracanie w usłudze SQL Data Warehouse
W tym artykule opisano szczegółowe informacje na temat tworzenia kopii zapasowych w usłudze SQL Data Warehouse. Umożliwia przywracanie bazy danych migawek w regionie podstawowym kopii zapasowych magazynu danych lub przywrócić kopię zapasową geograficznie regionu łączyć z magazynu geograficznie. 

## <a name="what-is-a-data-warehouse-backup"></a>Co to jest kopia zapasowa magazynu danych?
Kopia zapasowa magazynu danych jest kopią bazy danych, którego można użyć do przywrócenia hurtowni danych.  Ponieważ Magazyn danych SQL to Rozproszony system, kopia zapasowa magazynu danych składa się z wielu plików, które znajdują się w magazynie Azure. Kopia zapasowa magazynu danych zawiera migawki lokalnej bazy danych i geograficzna kopie zapasowe baz danych i pliki, które są skojarzone z magazynu danych. 

## <a name="local-snapshot-backups"></a>Tworzenie kopii zapasowych migawka lokalna
Magazyn danych SQL trwa migawek magazynu danych w ciągu dnia. Migawki są dostępne na siedem dni. Magazyn danych SQL obsługuje osiem godzin cel punktu odzyskiwania (RPO). Magazyn danych w regionie podstawowym można przywrócić do dowolnego z migawki w ciągu ostatnich siedmiu dni.

Aby wyświetlić podczas uruchamiania Ostatnia migawka, uruchom to zapytanie na online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

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

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Migawki przechowywania, gdy magazyn danych jest wstrzymana.
Usługi SQL Data Warehouse nie tworzy migawki i nie wygasa migawek, podczas gdy magazyn danych jest wstrzymana. Wiek migawki nie ulega zmianie, gdy magazyn danych jest wstrzymana. Przechowywania migawki opiera się na liczbę dni, przez które magazynu danych jest w trybie online nie dni kalendarzowych.

Jeśli migawki rozpoczyna się października 1 na 4 pm i magazynu danych zostało wstrzymane 3 października po 16: 00, migawki są maksymalnie dwa dni. Gdy w magazynie danych powróci do trybu online migawka jest dwóch dni. Jeśli w magazynie danych jest dostępna online 5 października godzinie 4, migawki jest dwa dni i pozostaje pięć dni.

Gdy w magazynie danych powróci do trybu online, SQL Data Warehouse wznawia nowych migawek i wygasa migawek, gdy mają one ponad siedem dni danych.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Czy można przywrócić hurtowni danych porzuconych?
Po upuszczeniu magazyn danych SQL Data Warehouse tworzy migawkę końcowego i zapisze go na siedem dni. Magazyn danych można przywrócić do punktu końcowego przywracania utworzonego w dniu usunięcia. 

> [!IMPORTANT]
> Usunięcie logicznej wystąpienia programu SQL server, wszystkie bazy danych, które należą do tego wystąpienia, również zostaną usunięte i nie może zostać odzyskany. Nie można przywrócić usuniętego serwera.
> 

## <a name="next-steps"></a>Kolejne kroki
Aby przywrócić SQL data warehouse, zobacz [przywrócić SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

Omówienie ciągłości działalności, można zobaczyć [omówienie ciągłości działalności biznesowej](../sql-database/sql-database-business-continuity.md)
