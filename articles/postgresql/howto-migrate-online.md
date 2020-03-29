---
title: Minimalna migracja przestojów do bazy danych usługi Azure dla postgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób przeprowadzania migracji minimalnego przestoju bazy danych PostgreSQL do bazy danych Azure Database for PostgreSQL — single server przy użyciu usługi migracji bazy danych Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65067509"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimalna migracja przestojów do bazy danych usługi Azure dla postgreSQL — pojedynczy serwer
Można wykonać migracje PostgreSQL do bazy danych Azure dla PostgreSQL przy minimalnym przestoju przy użyciu nowo **wprowadzonej funkcji ciągłej synchronizacji** dla [usługi migracji bazy danych Azure](https://aka.ms/get-dms) (DMS). Ta funkcja ogranicza ilość przestojów, które są ponoszone przez aplikację.

## <a name="overview"></a>Omówienie
Usługa Azure DMS wykonuje początkowe obciążenie lokalnego do usługi Azure Database for PostgreSQL, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja pozostaje uruchomiona. Po dogonieniu danych po stronie docelowej platformy Azure, możesz zatrzymać aplikację na krótką chwilę (minimalny czas przestoju), poczekaj na ostatnią partię danych (od momentu zatrzymania aplikacji, aż aplikacja będzie skutecznie niedostępna do podjęcia nowego ruchu) do połowu) w celu docelowych, a następnie zaktualizuj parametry połączenia, aby wskazywały platformę Azure. Po zakończeniu aplikacja będzie dostępna na platformie Azure!

![Ciągła synchronizacja z usługą migracji bazy danych platformy Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Następne kroki
- Zobacz wideo [Modernizacja aplikacji za pomocą platformy Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), który zawiera pokaz pokazujący, jak przeprowadzić migrację aplikacji PostgreSQL do bazy danych Platformy Azure dla PostgreSQL.
- Zobacz samouczek [Migrowanie postgreSQL do bazy danych Platformy Azure dla postgreSQL online przy użyciu DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
