---
title: Przywracanie magazynu danych Azure — lokalnych i geograficznie nadmiarowego | Dokumentacja firmy Microsoft
description: Omówienie opcji przywracania bazy danych do przywrócenia bazy danych w magazynie danych SQL Azure.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198477"
---
# <a name="sql-data-warehouse-restore"></a>Przywrócenie magazynu danych SQL
> [!div class="op_single_selector"]
> * [Omówienie][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Usługi SQL Data Warehouse umożliwia zarówno lokalnych, jak i geograficzne jego awarii magazynu danych w ramach odzyskiwania. Umożliwia przywrócenie magazynu danych do punktu przywracania w regionie podstawowym kopii zapasowych magazynu danych, lub użyj geograficznie nadmiarowego kopii zapasowych do przywrócenia w innym regionie geograficznym. W tym artykule opisano specyfice przywrócenie magazynu danych.

## <a name="what-is-a-data-warehouse-restore"></a>Co to jest przywrócenie magazynu danych?
Przywrócenie magazynu danych jest nowy magazyn danych, która jest tworzona na podstawie istniejącej kopii zapasowej lub usunięte dane magazynu. W magazynie danych przywróconych ponownie tworzy w magazynie kopii zapasowej danych w określonym czasie. Ponieważ Magazyn danych SQL to Rozproszony system, przywracania magazynu danych jest tworzony z wielu plików kopii zapasowej, które są przechowywane w obiektach blob Azure. 

Przywracanie bazy danych jest integralną część każdej strategii odzyskiwania biznesowych, jak ciągłości i odzyskiwaniem po awarii, ponieważ ponownie tworzy dane po przypadkowym uszkodzenia lub usunięcia.

Aby uzyskać więcej informacji, zobacz:

* [Tworzenie kopii zapasowych SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Omówienie ciągłości działalności biznesowej](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Punkty przywracania magazynu danych
Jako korzyścią z używania usługi Azure Premium Storage SQL Data Warehouse używa migawki obiektu Blob magazynu Azure do kopii zapasowej w magazynie danych podstawowych. Każda migawka ma punkt przywracania, który reprezentuje uruchomienia migawki. Aby przywrócić magazynu danych, wybierz punkt przywracania i wydać polecenie restore.  

Usługi SQL Data Warehouse zawsze przywrócenie kopii zapasowej do nowego magazynu danych. Możesz zachować w magazynie danych przywróconych i bieżący lub usuń jedno z nich. Jeśli chcesz zamienić bieżącego magazynu danych z magazynem przywróconych danych, można ją zmienić.

Jeśli trzeba przywrócić hurtowni danych usuniętych lub wstrzymana, możesz [Utwórz bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Przywracanie geograficznie nadmiarowego
Magazyn danych można przywrócić na dowolny region obsługujący magazyn danych SQL Azure o takim samym poziomie wydajności wybrany. Należy pamiętać, że wartości DWU 9000 i 18000 nie są obsługiwane we wszystkich regionach w wersji zapoznawczej.

> [!NOTE]
> Aby wykonać przywracanie geograficznie nadmiarowego musi nie rezygnujesz z tej funkcji.
> 
> 

## <a name="restore-timeline"></a>Przywróć osi czasu
W ciągu ostatnich siedmiu dni, można przywrócić bazy danych do dowolnego punktu przywracania dostępne. Migawki Uruchom co cztery do ośmiu godzin i są dostępne na siedem dni. Gdy migawka jest starsza niż siedmiu dni, wygaśnie i jego punktu przywracania nie jest już dostępny.

## <a name="restore-costs"></a>Przywróć kosztów
Opłata magazynu dla magazynu danych przywróconych jest rozliczane według stawki Azure Premium Storage. 

Jeśli magazynu przywróconych danych zostanie wstrzymana, są naliczane opłaty za magazyn szybkością Azure Premium Storage. Zaletą wstrzymanie jest nie są naliczane opłaty za zasoby obliczeniowe DWU.

Aby uzyskać więcej informacji na temat cen usługi SQL Data Warehouse, zobacz [cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Używa do odtworzenia
Podstawowym zastosowaniem przywracania magazynu danych jest odzyskiwanie danych po przypadkowej utraty danych lub uszkodzenia.

Umożliwia także przywracania magazynu danych do zachowania kopii zapasowej przez ponad siedem dni. Po przywróceniu kopii zapasowej online ma w magazynie danych i rozwiązaniem przez nieokreślony czas w celu ograniczenia kosztów obliczeń. Opłaty za magazyn szybkością Azure Premium Storage wiąże się z wstrzymanej bazy danych. 

## <a name="related-topics"></a>Powiązane tematy
### <a name="scenarios"></a>Scenariusze
* Omówienie ciągłości działalności, można zobaczyć [omówienie ciągłości działalności biznesowej](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Aby wykonać przywracanie magazynu danych, Przywróć przy użyciu:

* Azure portalu, zobacz [przywrócić hurtowni danych przy użyciu portalu Azure](sql-data-warehouse-restore-database-portal.md)
* Polecenia cmdlet programu PowerShell, zobacz [przywrócić hurtowni danych przy użyciu poleceń cmdlet programu PowerShell](sql-data-warehouse-restore-database-powershell.md)
* Interfejsy API REST, zobacz [przywrócić hurtowni danych przy użyciu interfejsów API REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
