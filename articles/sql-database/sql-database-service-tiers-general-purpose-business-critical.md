---
title: Usługa Azure SQL Database — ogólnego przeznaczenia i krytyczne dla działania | Dokumentacja firmy Microsoft
description: W artykule omówiono ogólnego przeznaczenia i warstwy krytyczne usługi biznesowej w rdzeniach wirtualnych zakupem modelu.
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
ms.openlocfilehash: e2230bc8adf13825692f93b1e2bc33a4b79076e2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574372"
---
# <a name="azure-sql-database-service-tiers"></a>Warstwy usługi w usłudze Azure SQL Database

Usługa Azure SQL Database jest oparty na architekturę aparatu bazy danych programu SQL Server, która jest uwzględniany w środowisku chmury w celu zapewnienia dostępności 99,99%, nawet w przypadku wystąpienia awarii infrastruktury. Istnieją trzy modele architektury, które są używane w usłudze Azure SQL Database:

- [Ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) przeznaczone dla większości obciążeń ogólnego.
- [Krytyczne dla działania firmy](sql-database-service-tier-business-critical.md) przeznaczone dla obciążeń o małym opóźnieniu za pomocą jednej z replik do odczytu.
- [W Hiperskali](sql-database-service-tier-hyperscale.md) przeznaczony dla bardzo dużych baz danych (maksymalnie 100 TB) przy użyciu wielu replik z możliwością odczytu.

W tym artykule omówiono zagadnienia magazynu i kopii zapasowych dla warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy w modelu zakupu opartego na rdzeniach wirtualnych.

> [!NOTE]
> Szczegółowe informacje na temat warstwy usług na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych, [warstwy usługi w Hiperskali](sql-database-service-tier-hyperscale.md). Dla porównania modelu zakupu opartego na rdzeniach wirtualnych za pomocą modelu zakupu opartego na jednostkach DTU, zobacz [zakupu modeli i zasobów bazy danych SQL Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Magazyn danych i dziennika

Rozważ następujące źródła:

- Przydzielenia pamięci, która jest używana przez pliki danych (MDF) i plików (LDF) dziennika.
- Każdej pojedynczej bazy danych zasobów obliczeniowych obsługuje rozmiar maksymalny rozmiar bazy danych, za pomocą domyślny maksymalny rozmiar 32 GB.
- Po skonfigurowaniu rozmiar wymagany pojedynczej bazy danych (rozmiar MDF), 30% dodatkowego miejsca do magazynowania jest automatycznie dodawany do obsługi LDF
- Określony rozmiar magazynu w wystąpieniu zarządzanym usługi musi być wielokrotność 32 GB.
- Można wybrać żadnych rozmiar pojedynczej bazy danych między 10 GB i maksymalnej obsługiwanej
  - W przypadku usługi storage w warstwach usług standardowa lub ogólnego przeznaczenia należy zwiększyć lub zmniejszyć rozmiar w przyrostach co 10 GB
  - Dla magazynu w warstwie premium lub krytyczne dla warstwy usług, zwiększania lub zmniejszania rozmiaru w partiach po 250 GB
- W przypadku warstwy usług ogólnego przeznaczenia `tempdb` używa dołączone dyski SSD i ten koszt przechowywania jest uwzględniona w cenie — rdzeń wirtualny.
- W warstwie krytyczne dla działalności `tempdb` udziałów dołączonych dysków SSD przy użyciu pliki MDF i LDF i kosztach magazynowania w bazie danych tempDB jest uwzględniona w cenie — rdzeń wirtualny.

> [!IMPORTANT]
> Opłaty są naliczane za całkowita ilość miejsca, które są przydzielone dla plików MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Tworzenie kopii zapasowych i magazynu

Magazyn kopii zapasowych bazy danych jest przydzielany do obsługi punktu w czasie przywracania (Odzyskiwanie) i [długi okres przechowywania (LTR)](sql-database-long-term-retention.md) możliwości bazy danych SQL. Ten magazyn jest przydzielany osobno dla każdej bazy danych i rozliczane jako dwie osobne bazy danych opłaty.

- **PITR**: Kopie zapasowe poszczególnych baz danych są kopiowane do [magazynu RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatycznie. Rozmiar magazynu zwiększa dynamicznie w miarę tworzenia nowych kopii zapasowych.  Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Użycie magazynu zależy od szybkości zmian bazy danych i okres przechowywania. Można skonfigurować okres przechowywania osobne dla każdej bazy danych od 7 do 35 dni. Minimalna wielkość magazynu równy 1 x rozmiaru danych znajduje się bez dodatkowych opłat. Większość baz danych ta wartość jest wystarczający, aby przechowywać kopie zapasowe z 7 dni.
- **LTR**: Usługa SQL Database oferuje opcję Konfigurowanie długoterminowego przechowywania pełnych kopii zapasowych dla maksymalnie 10 lat. Jeśli od lewej do prawej jest włączona, te kopie zapasowe są przechowywane w magazynach RA-GRS automatycznie, ale można kontrolować, jak często kopie zapasowe są kopiowane. Aby spełnić wymagania zgodności w różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczny lub roczny. Ta konfiguracja określi, ile pamięci masowej, będzie używany do tworzenia kopii zapasowych LTR. Aby oszacować koszt magazynu od lewej do prawej, można użyć Kalkulator cen od lewej do prawej. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępna dla pojedynczej bazy danych w warstwach ogólnego przeznaczenia i krytyczne usługa biznesowa, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępne dla elastycznych pul w warstwach ogólnego przeznaczenia i krytyczne usługa biznesowa, zobacz [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
