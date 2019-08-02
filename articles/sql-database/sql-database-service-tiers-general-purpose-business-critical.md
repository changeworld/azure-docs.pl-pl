---
title: Azure SQL Database — znaczenie ogólne i krytyczne dla działania firmy | Microsoft Docs
description: W tym artykule omówiono warstwy usług ogólnych i krytycznych dla firmy w modelu zakupu opartego na rdzeń wirtualny.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566709"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database warstw usług

Azure SQL Database jest oparta na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Trzy warstwy usług są używane w Azure SQL Database, z których każdy ma inny model architektury. Następujące warstwy usług:

- [Ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md), który jest przeznaczony dla większości obciążeń zwykłych.
- [Krytyczne](sql-database-service-tier-business-critical.md)dla działania firmy, które jest przeznaczone do obsługi obciążeń o małym opóźnieniu z jedną repliką z możliwością odczytu.
- [Skalowanie](sql-database-service-tier-hyperscale.md), które jest przeznaczone dla bardzo dużych baz danych (do 100 TB) z wieloma replikami z możliwością odczytu.

W tym artykule omówiono zagadnienia związane z magazynowaniem i tworzeniem kopii zapasowych w ramach modeli zakupu opartych na rdzeń wirtualny.

> [!NOTE]
> Aby uzyskać informacje na temat warstwy usługi na potrzeby skalowania w modelu zakupu opartego na rdzeń wirtualny, zobacz podskalowanie [warstwy usług](sql-database-service-tier-hyperscale.md). Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Magazyn danych i dzienników

Poniżej przedstawiono czynniki wpływające na ilość miejsca do magazynowania używanego dla plików danych i dziennika:

- Przydzielone magazyny są używane przez pliki danych (MDF) i pliki dziennika (LDF).
- Każdy rozmiar obliczeń pojedynczej bazy danych obsługuje maksymalny rozmiar bazy danych, a domyślny maksymalny rozmiar 32 GB.
- W przypadku skonfigurowania wymaganego pojedynczego rozmiaru bazy danych (rozmiaru pliku MDF) do obsługi plików LDF zostanie automatycznie dodany dodatkowy magazyn o rozmiarze 30%.
- Rozmiar magazynu dla SQL Database wystąpienia zarządzanego musi być określony w wielokrotnościach 32 GB.
- Można wybrać dowolny rozmiar bazy danych z zakresu od 10 GB do obsługiwanej wartości maksymalnej.
  - W przypadku magazynu w warstwach usług standardowych lub ogólnych należy zwiększyć lub zmniejszyć rozmiar w przyrostach 10 GB.
  - W przypadku magazynu w warstwach usługi krytycznej w wersji Premium lub biznesowej Zwiększ lub Zmniejsz rozmiar w 250 GB.
- W warstwie usług ogólnego przeznaczenia program `tempdb` używa dołączonego dysku SSD, a koszt magazynu jest uwzględniany w cenie rdzeń wirtualny.
- W warstwie `tempdb` usługi krytycznej dla firm udostępnia dołączony dysk SSD z plikami MDF i ldf, `tempdb` a koszt magazynu jest uwzględniany w cenie rdzeń wirtualny.

> [!IMPORTANT]
> Opłaty są naliczane za łączny magazyn przeznaczony dla plików MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [widoku sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Kopie zapasowe i magazyn

Magazyn dla kopii zapasowych bazy danych jest przypisywany do obsługi funkcji przywracania do punktu w czasie (kopie) oraz [długoterminowego przechowywania (LTR)](sql-database-long-term-retention.md) SQL Database. Ten magazyn jest przypisywany osobno dla każdej bazy danych i rozliczany jako dwa oddzielne opłaty za bazę danych.

- **KOPIE**: Kopie zapasowe poszczególnych baz danych są kopiowane do magazynu geograficznie nadmiarowego do [odczytu (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Rozmiar magazynu jest zwiększany dynamicznie w miarę tworzenia nowych kopii zapasowych. Magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Użycie magazynu zależy od szybkości zmiany bazy danych i okresu przechowywania kopii zapasowych. Dla każdej bazy danych można skonfigurować oddzielny okres przechowywania od 7 do 35 dni. Minimalna wielkość magazynu równa 100% (1x) rozmiaru bazy danych jest zapewniana bez dodatkowych opłat. W przypadku większości baz danych ta kwota jest wystarczająca do przechowywania 7 dni wykonywania kopii zapasowych.
- **LTR**: SQL Database oferuje możliwość skonfigurowania długoterminowego przechowywania pełnych kopii zapasowych przez maksymalnie 10 lat. W przypadku skonfigurowania zasad LTR te kopie zapasowe są przechowywane w magazynie RA-GRS automatycznie, ale można kontrolować, jak często są kopiowane kopie zapasowe. Aby spełnić inne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Wybrana konfiguracja określa, ile miejsca do magazynowania będzie używany do tworzenia kopii zapasowych. Aby oszacować koszt magazynu LTR, możesz użyć kalkulatora cen. Aby uzyskać więcej informacji, zobacz [SQL Database okres przechowywania długoterminowego](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje o określonych rozmiarach obliczeniowych i rozmiarach magazynu dostępnych dla pojedynczej bazy danych w warstwach ogólnych i krytycznych dla działania firmy, zobacz [SQL Database limitów zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby uzyskać szczegółowe informacje o określonych rozmiarach obliczeniowych i rozmiarach magazynów dostępnych dla pul elastycznych w warstwach usług ogólnych i krytycznych dla firmy, zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
