---
title: Usługa Azure SQL Database — ogólnego przeznaczenia i krytyczne dla działania | Dokumentacja firmy Microsoft
description: W artykule omówiono ogólnego przeznaczenia i warstwy usług krytycznych biznesowych w modelu zakupu opartego na rdzeniach wirtualnych.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431331"
---
# <a name="azure-sql-database-service-tiers"></a>Warstwy usługi w usłudze Azure SQL Database

Usługa Azure SQL Database jest oparty na architekturę aparatu bazy danych programu SQL Server, która jest uwzględniany w środowisku chmury zapewnić dostępność 99,99%, nawet jeśli występuje awaria infrastruktury. Trzy warstwy usługi są używane w usłudze Azure SQL Database, każdy z innego modelu architektury. Te warstwy usługi są:

- [Ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md), która jest przeznaczona do obciążeń najbardziej ogólny.
- [Krytyczne dla działania](sql-database-service-tier-business-critical.md), która jest przeznaczona dla obciążeń o małych opóźnieniach przy użyciu jednej z replik do odczytu.
- [W Hiperskali](sql-database-service-tier-hyperscale.md), który jest przeznaczony dla bardzo dużych baz danych (maksymalnie 100 TB) przy użyciu wielu replik z możliwością odczytu.

W tym artykule omówiono zagadnienia dotyczące ogólnego przeznaczenia i warstwy usług krytycznych biznesowych w modelu zakupu opartego na rdzeniach wirtualnych magazynu i kopii zapasowej.

> [!NOTE]
> Aby uzyskać informacji na temat warstwy usług na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych, zobacz [warstwy usługi w hiperskali](sql-database-service-tier-hyperscale.md). Dla porównania modelu zakupu opartego na rdzeniach wirtualnych za pomocą modelu zakupu opartego na jednostkach DTU, zobacz [zakupu modeli i zasobów bazy danych SQL Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Magazyn danych i dziennika

Następujące czynniki mają wpływ na ilość miejsca dla plików danych i dziennika:

- Przydzielenia pamięci, która jest używana przez pliki danych (MDF) i pliki dziennika (LDF).
- Każdej pojedynczej bazy danych zasobów obliczeniowych obsługuje rozmiar maksymalny rozmiar bazy danych, z domyślnym maksymalnym rozmiarze 32 GB.
- Po skonfigurowaniu rozmiar wymagany pojedynczej bazy danych (rozmiar plików MDF), 30 procent więcej dodatkowego miejsca do magazynowania jest automatycznie dodawany do obsługi plików LDF.
- Określony rozmiar magazynu dla zarządzanego wystąpienia bazy danych SQL musi być wielokrotność 32 GB.
- Można wybrać dowolnego rozmiaru pojedynczej bazy danych między 10 GB i maksymalna obsługiwana wartość.
  - W przypadku usługi storage w warstwach usług standardowa lub ogólnego przeznaczenia zwiększyć lub zmniejszyć rozmiar w przyrostach co 10 GB.
  - Usługi storage w wersji premium lub warstwy krytyczne usługi biznesowe, spadek lub wzrost rozmiaru w partiach po 250 GB.
- W przypadku warstwy usług ogólnego przeznaczenia `tempdb` używa dołączone dyski SSD, a ten koszt przechowywania jest uwzględniona w cenie — rdzeń wirtualny.
- W warstwie krytyczne usługi biznesowe `tempdb` udostępnia pliki MDF i LDF dołączonych dysków SSD i `tempdb` koszt magazynowania jest uwzględniona w cenie — rdzeń wirtualny.

> [!IMPORTANT]
> Opłaty są naliczane za całkowita ilość miejsca, które są przydzielone pliki MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Tworzenie kopii zapasowych i magazynu

Magazyn kopii zapasowych bazy danych jest przydzielany do obsługują funkcję przywracania do punktu w czasie (Odzyskiwanie) i [długoterminowego przechowywania danych (od lewej do prawej)](sql-database-long-term-retention.md) możliwości bazy danych SQL. Ten magazyn jest przydzielany osobno dla każdej bazy danych i rozliczane jako dwie osobne bazy danych opłaty.

- **PITR**: Kopie zapasowe poszczególnych baz danych są kopiowane do [magazynu geograficznie nadmiarowego (RA-GRS) Magazyn odczytu](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatycznie. Rozmiar magazynu zwiększa dynamicznie w miarę tworzenia nowych kopii zapasowych. Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Użycie magazynu zależy od szybkości zmian bazy danych i okres przechowywania kopii zapasowych. Można skonfigurować okres przechowywania osobne dla każdej bazy danych od 7 do 35 dni. Minimalna wielkość magazynu wynosi 100 procent (1 x) rozmiaru bazy danych znajduje się bez dodatkowych opłat. Większość baz danych ta wartość jest wystarczający, aby przechowywać kopie zapasowe z 7 dni.
- **LTR**: SQL Database oferuje możliwość Konfigurowanie długoterminowego przechowywania pełnych kopii zapasowych dla maksymalnie 10 lat. Po skonfigurowaniu zasad pisowni LTR te kopie zapasowe są przechowywane w magazynach RA-GRS automatycznie, ale można kontrolować, jak często kopie zapasowe są kopiowane. Spełnić wymagania dotyczące różnych zgodności, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczny lub roczny. Wybranej konfiguracji określa, ile pamięci masowej, będzie używany do tworzenia kopii zapasowych LTR. Aby oszacować koszt magazynu od lewej do prawej, można użyć Kalkulator cen od lewej do prawej. Aby uzyskać więcej informacji, zobacz [długoterminowe przechowywanie bazy danych SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o konkretnym obliczenia rozmiarów i rozmiaru magazynu dostępne dla pojedynczej bazy danych ogólnego przeznaczenia i warstwy usług krytycznych firm, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby uzyskać szczegółowe informacje o konkretnym obliczenia rozmiarów i rozmiaru magazynu są dostępne dla elastycznych pul w warstwy krytyczne usługi biznesowe i ogólnego przeznaczenia, zobacz [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
