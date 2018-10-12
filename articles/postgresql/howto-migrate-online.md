---
title: Migracja z minimalnym przestojem do usługi Azure Database for PostgreSQL
description: W tym artykule opisano sposób wykonywania migracja z minimalnym przestojem postgresql w warstwie bazy danych do usługi Azure Database for PostgreSQL za pomocą usługi Azure Database Migration Service.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093837"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migracja z minimalnym przestojem do usługi Azure Database for PostgreSQL
Można przeprowadzić migracji PostgreSQL do usługi Azure Database for PostgreSQL przy minimalnych przestojach przy użyciu nowo wprowadzonych **możliwości ciągłej synchronizacji** dla [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Ta funkcja ogranicza czas przestoju, który jest naliczany przez aplikację.

## <a name="overview"></a>Przegląd
Usługa DMS wykonuje wstępnego obciążenia lokalne do usługi Azure Database for PostgreSQL, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja jest uruchomiona. Po danych wyrównywane na docelowej stronie platformy Azure, Zatrzymaj aplikację na krótko (minimalnych przestojach), poczekaj, aż ostatniej partii danych (od czasu, Zatrzymaj aplikację, dopóki aplikacja jest skutecznie niedostępna do przyjmowania dowolnego nowego ruchu) do przechwytywania Konfigurowanie w elemencie docelowym, a następnie zaktualizuj parametry połączenia, aby wskazywała na platformie Azure. Gdy skończysz, Twoja aplikacja będzie na żywo na platformie Azure!

![Ciągła synchronizacja z usługą Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

Migracja DMS źródeł PostgreSQL jest obecnie w wersji zapoznawczej. Jeśli chcesz wypróbować usługę Migrowanie obciążeń programu PostgreSQL, zarejestruj się za pośrednictwem usługi Azure DMS [Podgląd strony](https://aka.ms/dms-preview) aby wyrazić zainteresowanie. Twoja opinia jest bezcenne w ułatwienia Aby dodatkowo poprawić usługę.

## <a name="next-steps"></a>Kolejne kroki
- Obejrzyj klip wideo [modernizacja aplikacji z platformą Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), który zawiera pokaz przedstawiający sposób Migrowanie postgresql w warstwie aplikacji do usługi Azure Database for PostgreSQL.
- Zapoznaj się z samouczkiem [PostgreSQL migracji do usługi Azure Database for PostgreSQL online przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
