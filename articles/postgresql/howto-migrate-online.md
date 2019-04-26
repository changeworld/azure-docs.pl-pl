---
title: Migracja z minimalnym przestojem do usługi Azure Database for PostgreSQL
description: W tym artykule opisano sposób wykonywania migracja z minimalnym przestojem postgresql w warstwie bazy danych do usługi Azure Database for PostgreSQL za pomocą usługi Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421236"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migracja z minimalnym przestojem do usługi Azure Database for PostgreSQL
Można przeprowadzić migracji PostgreSQL do usługi Azure Database for PostgreSQL przy minimalnych przestojach przy użyciu nowo wprowadzonych **możliwości ciągłej synchronizacji** dla [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Ta funkcja ogranicza czas przestoju, który jest naliczany przez aplikację.

## <a name="overview"></a>Omówienie
Usługa Azure DMS wykonuje wstępnego obciążenia lokalne do usługi Azure Database for PostgreSQL, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja jest uruchomiona. Po danych wyrównywane na docelowej stronie platformy Azure, Zatrzymaj aplikację na krótko (minimalnych przestojach), poczekaj, aż ostatniej partii danych (od czasu, Zatrzymaj aplikację, dopóki aplikacja jest skutecznie niedostępna do przyjmowania dowolnego nowego ruchu) do przechwytywania Konfigurowanie w elemencie docelowym, a następnie zaktualizuj parametry połączenia, aby wskazywała na platformie Azure. Gdy skończysz, Twoja aplikacja będzie na żywo na platformie Azure!

![Ciągła synchronizacja z usługą Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Kolejne kroki
- Obejrzyj klip wideo [modernizacja aplikacji z platformą Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), który zawiera pokaz przedstawiający sposób Migrowanie postgresql w warstwie aplikacji do usługi Azure Database for PostgreSQL.
- Zapoznaj się z samouczkiem [PostgreSQL migracji do usługi Azure Database for PostgreSQL online przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
