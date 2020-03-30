---
title: Tworzenie kopii zapasowych i przywracanie — migawki, geobeksy
description: Dowiedz się, jak działa tworzenie kopii zapasowych i przywracanie w puli SQL usługi Azure Synapse Analytics. Użyj kopii zapasowych, aby przywrócić magazyn danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych, aby przywrócić do innego regionu geograficznego.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: ae53380572e753a8bcfa20fcd165fa015766263e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349268"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Tworzenie kopii zapasowych i przywracanie w puli SQL synapse platformy Azure

Dowiedz się, jak używać kopii zapasowych i przywracania w puli SQL Usługi Azure Synapse. Punkty przywracania puli SQL umożliwia odzyskiwanie lub kopiowanie magazynu danych do poprzedniego stanu w regionie podstawowym. Użyj geograficznych kopii zapasowych magazynu danych, aby przywrócić je do innego regionu geograficznego.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co to jest migawka magazynu danych

*Migawka magazynu danych* tworzy punkt przywracania, który można wykorzystać do odzyskania lub skopiowania magazynu danych do poprzedniego stanu.  Ponieważ pula SQL jest systemem rozproszonym, migawka magazynu danych składa się z wielu plików, które znajdują się w magazynie platformy Azure. Migawki przechwytują przyrostowe zmiany z danych przechowywanych w magazynie danych.

*Przywracanie magazynu danych* to nowy magazyn danych, który jest tworzony z punktu przywracania istniejącego lub usuniętego magazynu danych. Przywracanie magazynu danych jest istotną częścią każdej strategii ciągłości biznesowej i odzyskiwania po awarii, ponieważ ponownie tworzy dane po przypadkowym zepsuniu lub usunięciu. Magazyn danych jest również zaawansowanym mechanizmem do tworzenia kopii magazynu danych do celów testowych lub rozwojowych.  Szybkość przywracania puli SQL może się różnić w zależności od rozmiaru bazy danych i lokalizacji źródłowego i docelowego magazynu danych. 

## <a name="automatic-restore-points"></a>Automatyczne punkty przywracania

Migawki są wbudowaną funkcją usługi, która tworzy punkty przywracania. Nie trzeba włączać tej funkcji. Jednak pula SQL powinna być w stanie aktywnym do tworzenia punktu przywracania. Jeśli pula SQL jest często wstrzymywana, automatyczne punkty przywracania mogą nie zostać utworzone, więc upewnij się, że utworzono punkt przywracania zdefiniowany przez użytkownika przed wstrzymaniem puli SQL. Automatyczne przywracanie punktów obecnie nie mogą być usunięte przez użytkowników, jak usługa używa tych punktów przywracania do obsługi SLA do odzyskiwania.

Migawki magazynu danych są pobierane przez cały dzień, tworząc punkty przywracania, które są dostępne przez siedem dni. Tego okresu przechowywania nie można zmienić. Pula SQL obsługuje ośmiogodzinny cel punktu odzyskiwania (RPO). Magazyn danych w regionie podstawowym można przywrócić z jednej z migawek wykonanych w ciągu ostatnich siedmiu dni.

Aby zobaczyć, kiedy została uruchomiona ostatnia migawka, uruchom tę kwerendę w puli SQL online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

Ta funkcja umożliwia ręczne wyzwalanie migawek w celu utworzenia punktów przywracania magazynu danych przed i po dużych modyfikacjach. Ta funkcja zapewnia, że punkty przywracania są logicznie spójne, co zapewnia dodatkową ochronę danych w przypadku przerw w obciążeniu lub błędów użytkownika w celu szybkiego czasu odzyskiwania. Zdefiniowane przez użytkownika punkty przywracania są dostępne przez siedem dni i są automatycznie usuwane w Twoim imieniu. Nie można zmienić okresu przechowywania punktów przywracania zdefiniowanych przez użytkownika. **42 zdefiniowane przez użytkownika punkty przywracania** są gwarantowane w dowolnym momencie, więc muszą zostać [usunięte](https://go.microsoft.com/fwlink/?linkid=875299) przed utworzeniem innego punktu przywracania. Migawki można wyzwolić, aby utworzyć zdefiniowane przez użytkownika punkty przywracania za pośrednictwem programu [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) lub witryny Azure portal.

> [!NOTE]
> Jeśli potrzebujesz punktów przywracania dłuższych niż 7 dni, zagłosuj na tę możliwość [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Można również utworzyć zdefiniowany przez użytkownika punkt przywracania i przywrócić z nowo utworzonego punktu przywracania do nowego magazynu danych. Po przywróceniu masz pulę SQL w trybie online i można wstrzymać ją na czas nieokreślony, aby zaoszczędzić koszty obliczeniowe. Wstrzymana baza danych wiąże się z opłatami za magazyn po kursie usługi Azure Premium Storage. Jeśli potrzebujesz aktywnej kopii przywróconego magazynu danych, możesz wznowić, co powinno potrwać tylko kilka minut.

### <a name="restore-point-retention"></a>Przywracanie przechowywania punktów

Poniżej wymieniono szczegóły dotyczące okresów przechowywania punktów przywracania:

1. Pula SQL usuwa punkt przywracania, gdy osiągnie 7-dniowy okres przechowywania **i** gdy istnieją co najmniej 42 całkowite punkty przywracania (w tym zdefiniowane przez użytkownika i automatyczne).
2. Migawki nie są pobierane, gdy pula SQL jest wstrzymana.
3. Wiek punktu przywracania jest mierzona przez bezwzględne dni kalendarzowe od czasu, gdy punkt przywracania jest pobierana, w tym gdy pula SQL jest wstrzymana.
4. W dowolnym momencie pula SQL jest gwarantowana, aby móc przechowywać do 42 zdefiniowanych przez użytkownika punktów przywracania i 42 automatyczne punkty przywracania, o ile te punkty przywracania nie osiągnęły 7-dniowego okresu przechowywania
5. Jeśli zostanie podjęta migawka, pula SQL jest następnie wstrzymana na dłużej niż 7 dni, a następnie wznawia, punkt przywracania będzie się powtarzał, dopóki nie będzie 42 całkowitych punktów przywracania (w tym zdefiniowanych przez użytkownika i automatycznych)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Przechowywanie migawek po upuszczeniu puli SQL

Po upuszczeniu puli SQL, ostateczna migawka jest tworzony i zapisywany przez siedem dni. Pulę SQL można przywrócić do końcowego punktu przywracania utworzonego podczas usuwania. Jeśli pula SQL zostanie upuszczona w stanie wstrzymania, nie jest pobierana migawka. W tym scenariuszu upewnij się, że utworzysz punkt przywracania zdefiniowany przez użytkownika przed upuszczeniem puli SQL.

> [!IMPORTANT]
> Jeśli usuniesz logiczne wystąpienie serwera SQL, wszystkie bazy danych, które należą do wystąpienia są również usuwane i nie można odzyskać. Nie można przywrócić usuniętego serwera.

## <a name="geo-backups-and-disaster-recovery"></a>Kopie geograficzne i odzyskiwanie po awarii

Geograficzna kopia zapasowa jest tworzona raz dziennie do [sparowanego centrum danych](../../best-practices-availability-paired-regions.md). Punkt ochrony danych dla przywracania geograficznego trwa 24 godziny. Można przywrócić kopię zapasową geograficzną na serwerze w dowolnym innym regionie, w którym jest obsługiwana pula SQL. Geo-kopia zapasowa zapewnia, że można przywrócić magazyn danych w przypadku, gdy nie można uzyskać dostępu do punktów przywracania w regionie podstawowym.

> [!NOTE]
> Jeśli potrzebujesz krótszego celu ochrony danych rpo dla kopii zapasowych geograficznych, zagłosuj na tę funkcję [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse). Można również utworzyć zdefiniowany przez użytkownika punkt przywracania i przywrócić z nowo utworzonego punktu przywracania do nowego magazynu danych w innym regionie. Po przywróceniu masz magazyn danych w trybie online i można wstrzymać go na czas nieokreślony, aby zaoszczędzić koszty obliczeń. Wstrzymana baza danych wiąże się z opłatami za magazyn po kursie usługi Azure Premium Storage. Jeśli potrzebujesz aktywnej kopii magazynu danych, możesz wznowić, co powinno potrwać tylko kilka minut.

## <a name="backup-and-restore-costs"></a>Koszty tworzenia kopii zapasowych i przywracania

Można zauważyć, że rachunek platformy Azure ma element zamówienia dla magazynu i element zamówienia dla magazynu odzyskiwania po awarii. Opłata za magazyn to całkowity koszt przechowywania danych w regionie podstawowym wraz ze zmianami przyrostowymi przechwyconymi przez migawki. Aby uzyskać bardziej szczegółowe wyjaśnienie sposobu ładowania migawek, zobacz [Opis naliczania opłat za migawki.](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios) Opłata geograficznie nadmiarowa pokrywa koszty przechowywania kopii zapasowych geograficznych.  

Całkowity koszt podstawowego magazynu danych i siedem dni zmian migawki jest zaokrąglana do najbliższej tb. Na przykład jeśli magazyn danych jest 1,5 TB i migawki przechwytuje 100 GB, są naliczane opłaty za 2 TB danych według stawek usługi Azure Premium Storage.

Jeśli używasz magazynu geograficznie nadmiarowego, otrzymasz oddzielną opłatę za magazyn. Magazyn geograficznie nadmiarowy jest rozliczany według standardowej szybkości dostępu do odczytu geograficznie nadmiarowej (RA-GRS).

Aby uzyskać więcej informacji na temat cen usługi Azure Synapse, zobacz [Cennik usługi Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Nie są naliczane opłaty za wyjście danych podczas przywracania w różnych regionach.

## <a name="restoring-from-restore-points"></a>Przywracanie z punktów przywracania

Każda migawka tworzy punkt przywracania, który reprezentuje czas rozpoczęcia migawki. Aby przywrócić magazyn danych, należy wybrać punkt przywracania i wydać polecenie przywracania.  

Można zachować przywrócony magazyn danych i bieżący lub usunąć jeden z nich. Jeśli chcesz zastąpić bieżący magazyn danych przywróconym magazynem danych, możesz zmienić jego nazwę za pomocą [alter database (puli SQL)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) z opcją MODYFIKUJ NAZWĘ.

Aby przywrócić magazyn danych, zobacz [Przywracanie puli SQL](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Aby przywrócić usunięty lub wstrzymany magazyn danych, można [utworzyć bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Przywracanie subskrypcji krzyżowej

Jeśli chcesz bezpośrednio przywrócić subskrypcję, zagłosuj na tę funkcję [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Przywróć na inny serwer logiczny i ["Przenieś"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) serwer w ramach subskrypcji, aby wykonać przywracanie subskrypcji krzyżowej. 

## <a name="geo-redundant-restore"></a>Przywracanie geograficznie nadmiarowe

[Pulę SQL](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) można przywrócić do dowolnego regionu obsługującego pulę SQL na wybranym poziomie wydajności.

> [!NOTE]
> Aby wykonać przywracanie geograficznie nadmiarowe, nie można zrezygnować z tej funkcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat planowania awarii, zobacz [Omówienie ciągłości biznesowej](../../sql-database/sql-database-business-continuity.md)
