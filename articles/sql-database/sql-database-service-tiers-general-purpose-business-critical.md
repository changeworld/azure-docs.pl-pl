---
title: Cel ogólny i krytyczne dla biznesu
description: W artykule omówiono ogólne i biznesowe warstwy usług o krytycznym znaczeniu dla firmy w modelu zakupów opartym na czacie wirtualnym.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937839"
---
# <a name="azure-sql-database-service-tiers"></a>Warstwy usług Azure SQL Database

Usługa Azure SQL Database jest oparta na architekturze aparatu bazy danych programu SQL Server, która jest dostosowana do środowiska w chmurze, aby zapewnić dostępność 99,99 procent, nawet jeśli występuje awaria infrastruktury. W usłudze Azure SQL Database są używane trzy warstwy usług, z których każda ma inny model architektury. Te warstwy usług są następujące:

- [Cel ogólny](sql-database-service-tier-general-purpose.md), który jest przeznaczony dla obciążeń budżetowych.
- [Hiperskala](sql-database-service-tier-hyperscale.md), który jest przeznaczony dla większości obciążeń biznesowych, zapewniając wysoce skalowalne przechowywanie, odczyt w poziomie i szybkie przywracanie bazy danych.
- [Krytyczne dla firmy](sql-database-service-tier-business-critical.md), które jest przeznaczone do obciążeń o małych opóźnieniach z wysoką odpornością na awarie i szybkie tryb failover.

W tym artykule omówiono różnice be·tween warstwy usług, magazyn i tworzenie kopii zapasowych zagadnienia dotyczące ogólnego przeznaczenia i warstwy usług krytycznych dla firmy w modelu zakupów opartym na czacie wirtualnym.

## <a name="service-tier-comparison"></a>Porównanie warstw usług

W poniższej tabeli opisano kluczowe różnice między warstwami usług dla najnowszej generacji (Gen5). Należy zauważyć, że właściwości warstwy usługi mogą być różne w pojedynczej bazie danych i wystąpieniu zarządzanym.

| | Typ zasobu | Ogólnego przeznaczenia |  Hiperskala | Krytyczne dla biznesu |
|:---:|:---:|:---:|:---:|:---:|
| **Najlepsze dla** | |  Oferuje opcje zrównoważonego obliczeń i pamięci masowej zorientowane na budżet. | Większość obciążeń biznesowych. Automatyczne skalowanie pamięci masowej o rozmiarze do 100 TB, płynne skalowanie w pionie i poziome, szybkie przywracanie bazy danych. | Aplikacje OLTP z wysoką szybkością transakcji i niskim opóźnieniem we/wy. Oferuje najwyższą odporność na awarie i szybkie pracy awaryjnej przy użyciu wielu synchronicznie zaktualizowanych replik.|
|  **Dostępne w typie zasobu:** ||Pojedyncza baza danych / pula elastyczna / wystąpienie zarządzane | Pojedyncza baza danych | Pojedyncza baza danych / pula elastyczna / wystąpienie zarządzane |
| **Rozmiar obliczeń**|Pojedyncza baza danych / pula elastyczna | Od 1 do 80 vCorów | Od 1 do 80 vCorów | Od 1 do 80 vCorów |
| | Wystąpienie zarządzane | 4, 8, 16, 24, 32, 40, 64, 80 vCores | Nie dotyczy | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Pule wystąpień zarządzanych | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | Nie dotyczy | Nie dotyczy |
| **Typ magazynu** | Wszystkie | Magazyn zdalny w wersji premium (na wystąpienie) | Magazyn oddzielony od pary z lokalną pamięcią podręczną SSD (na wystąpienie) | Superszybki lokalny magazyn dysków SSD (na wystąpienie) |
| **Rozmiar bazy danych** | Pojedyncza baza danych / pula elastyczna | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Rozmiar magazynu** | Pojedyncza baza danych / pula elastyczna | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Rozmiar bazy danych TempDB** | Pojedyncza baza danych / pula elastyczna | [32 GB na r/r](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB na r/r](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB na r/r](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Wystąpienie zarządzane  | [24 GB na r/r](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Nie dotyczy | Do 4 TB - [ograniczona wielkością pamięci masowej](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Przepływność zapisu dziennika** | Pojedyncza baza danych | [1,875 MB/s na r/s na r/r (maks. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s na r/s (maks. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Wystąpienie zarządzane | [3 MB/s na r/s (maks. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Nie dotyczy | [4 MB/s na rów wirtualny (maks. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Dostępność**|Wszystkie| 99,99% |  [99,95% z jedną repliką pomocniczą, 99,99% z większą liczesz replikami](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% ze strefą nadmiarową pojedynczą bazą danych](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Tworzenie kopii zapasowych**|Wszystkie|RA-GRS, 7-35 dni (domyślnie 7 dni)| RA-GRS, 7 dni, stałe odzyskiwanie punktu czasu (PITR) | RA-GRS, 7-35 dni (domyślnie 7 dni) |
|**Oltp w pamięci** | | Nie dotyczy | Nie dotyczy | Dostępne |
|**Repliki tylko do odczytu**| | 0 wbudowany <br> 0 - 4 przy użyciu [replikacji geograficznej](sql-database-active-geo-replication.md) | 0 - 4 wbudowane | 1 wbudowany, wliczony w cenę <br> 0 - 4 przy użyciu [replikacji geograficznej](sql-database-active-geo-replication.md) |
|**Ceny/rozliczenia** | Pojedyncza baza danych | Naliczane są opłaty [za pamięć wirtualną, zarezerwowany magazyn i magazyn kopii zapasowych.](https://azure.microsoft.com/pricing/details/sql-database/single/) <br/>IOPS nie jest naliczana. | [Rypek wirtualny dla każdej repliki i używanego magazynu](https://azure.microsoft.com/pricing/details/sql-database/single/) jest naliczany. <br/>IOPS nie jest jeszcze naładowany. | Naliczane są opłaty [za pamięć wirtualną, zarezerwowany magazyn i magazyn kopii zapasowych.](https://azure.microsoft.com/pricing/details/sql-database/single/) <br/>IOPS nie jest naliczana. |
|| Wystąpienie zarządzane | Jest naliczana opłata [za pamięć wirtualną, zarezerwowany magazyn i magazyn kopii zapasowych.](https://azure.microsoft.com/pricing/details/sql-database/managed/) <br/>IOPS nie jest naliczana| Nie dotyczy | Jest naliczana opłata [za pamięć wirtualną, zarezerwowany magazyn i magazyn kopii zapasowych.](https://azure.microsoft.com/pricing/details/sql-database/managed/) <br/>IOPS nie jest naliczana.| 
|**Modele rabatowe**| | [Wystąpienia zarezerwowane](sql-database-reserved-capacity.md)<br/>[Korzyść hybrydowa platformy Azure](sql-database-azure-hybrid-benefit.md) (niedostępna w przypadku subskrypcji deweloperskich/testowych)<br/>Subskrypcje deweloperów/testów dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i płatności zgodnie [z rzeczywistymi pogoń](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Korzyść hybrydowa platformy Azure](sql-database-azure-hybrid-benefit.md) (niedostępna w przypadku subskrypcji deweloperskich/testowych)<br/>Subskrypcje deweloperów/testów dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i płatności zgodnie [z rzeczywistymi pogoń](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Wystąpienia zarezerwowane](sql-database-reserved-capacity.md)<br/>[Korzyść hybrydowa platformy Azure](sql-database-azure-hybrid-benefit.md) (niedostępna w przypadku subskrypcji deweloperskich/testowych)<br/>Subskrypcje deweloperów/testów dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i płatności zgodnie [z rzeczywistymi pogoń](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Aby uzyskać więcej informacji, zobacz szczegółowe różnice między warstwami usług w [pojedynczej bazie danych (vCore),](sql-database-vcore-resource-limits-single-databases.md) [pulach pojedynczej bazy danych (vCore),](sql-database-dtu-resource-limits-single-databases.md) [pojedynczej bazie danych (DTU),](sql-database-dtu-resource-limits-single-databases.md) [pulach pojedynczej bazy danych (DTU)](sql-database-dtu-resource-limits-single-databases.md)i stronach [wystąpienia zarządzanego.](sql-database-managed-instance-resource-limits.md)

> [!NOTE]
> Aby uzyskać informacje na temat warstwy usług opartych na technologii vCore w modelu zakupu opartym na [wynikach, zobacz warstwę usług opartych na hiperskali](sql-database-service-tier-hyperscale.md). Aby zapoznać się z porównaniem modelu zakupów opartego na procesorach vCore z modelem zakupów opartym na USŁUDZE DTU, zobacz [Tworzenie modeli i zasobów w bazie danych SQL usługi Azure .](sql-database-purchase-models.md)

## <a name="data-and-log-storage"></a>Przechowywanie danych i dzienników

Następujące czynniki wpływają na ilość miejsca używanego do plików danych i dzienników i ma zastosowanie do ogólnego przeznaczenia i krytycznych dla firmy. Aby uzyskać szczegółowe informacje na temat przechowywania danych i dzienników w hiperskali, zobacz [warstwa usług hiperskali](sql-database-service-tier-hyperscale.md).

- Przydzielony magazyn jest używany przez pliki danych (MDF) i pliki dziennika (LDF).
- Każdy rozmiar obliczeń pojedynczej bazy danych obsługuje maksymalny rozmiar bazy danych, z domyślnym maksymalnym rozmiarem 32 GB.
- Podczas konfigurowania wymaganego pojedynczego rozmiaru bazy danych (rozmiar pliku MDF), 30 procent więcej dodatkowego magazynu jest automatycznie dodawany do obsługi plików LDF.
- Rozmiar magazynu dla wystąpienia zarządzanego bazy danych SQL musi być określony w wielokrotności 32 GB.
- Można wybrać dowolny pojedynczy rozmiar bazy danych między 10 GB a obsługiwanym maksimum.
  - W przypadku przechowywania w warstwach usług standardowego lub ogólnego przeznaczenia zwiększ lub zmniejsz rozmiar w przyrostach o 10 GB.
  - W przypadku magazynu w warstwach usług o krytycznym znaczeniu dla wersji premium lub biznesowej zwiększ lub zmniejsz rozmiar w przyrostach o 250 GB.
- W warstwie usług `tempdb` ogólnego przeznaczenia używa dołączonego ssd, a ten koszt magazynu jest wliczony w cenę vCore.
- W warstwie usług `tempdb` o krytycznym znaczeniu dla firmy współużytkuje dołączony `tempdb` dysk SSD z plikami MDF i LDF, a koszt magazynu jest wliczony w cenę vCore.

> [!IMPORTANT]
> Pobierana jest opłata za całkowitą ilość miejsca przydzielonego dla plików MDF i LDF.

Aby monitorować bieżący całkowity rozmiar plików MDF i LDF, należy użyć [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [pliku sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Kopie zapasowe i pamięć masowa

Magazyn kopii zapasowych bazy danych jest przydzielany do obsługi możliwości przywracania punktu w czasie (PITR) i [długoterminowego przechowywania (LTR)](sql-database-long-term-retention.md) bazy danych SQL. Ten magazyn jest przydzielany oddzielnie dla każdej bazy danych i rozliczany jako dwa oddzielne opłaty za bazę danych.

- **PITR**: Pojedyncze kopie zapasowe bazy danych są automatycznie kopiowane do [magazynu geob nadmiarowego dostępu do odczytu (RA-GRS).](../storage/common/storage-designing-ha-apps-with-ragrs.md) Rozmiar magazynu zwiększa się dynamicznie w miarę tworzenia nowych kopii zapasowych. Magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Zużycie magazynu zależy od szybkości zmiany bazy danych i okresu przechowywania kopii zapasowych. Można skonfigurować oddzielny okres przechowywania dla każdej bazy danych od 7 do 35 dni. Minimalna ilość miejsca do magazynowania równa 100 procent (1x) rozmiaru bazy danych jest dostarczana bez dodatkowych opłat. W przypadku większości baz danych ta kwota wystarcza do przechowywania 7 dni kopii zapasowych.
- **LTR**: Baza danych SQL oferuje możliwość skonfigurowania długoterminowego przechowywania pełnych kopii zapasowych przez okres do 10 lat. Jeśli skonfigurujesz zasady LTR, te kopie zapasowe są automatycznie przechowywane w magazynie RA-GRS, ale można kontrolować, jak często kopie zapasowe są kopiowane. Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, miesięcznych i/lub rocznych kopii zapasowych. Wybrana konfiguracja określa, ile miejsca będzie używane do tworzenia kopii zapasowych LTR. Aby oszacować koszt magazynu LTR, można użyć kalkulatora cen LTR. Aby uzyskać więcej informacji, zobacz [długoterminowa retencja bazy danych SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i rozmiarów magazynu dostępnych dla pojedynczej bazy danych w warstwach usług o krytycznym znaczeniu dla działalności i biznesowej, zobacz [Limity zasobów oparte na wirtualnych polach baz danych SQL dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i rozmiarów magazynu dostępnych dla pul elastycznych w warstwach usług o krytycznym znaczeniu dla ogólnego przeznaczenia i biznesowych, zobacz [limity zasobów opartych na polach elastycznych oparte na bazach danych SQL.](sql-database-vcore-resource-limits-elastic-pools.md)
