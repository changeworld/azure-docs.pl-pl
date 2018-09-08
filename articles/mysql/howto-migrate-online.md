---
title: Migracja z minimalnym przestojem do usługi Azure Database for MySQL
description: W tym artykule opisano sposób przeprowadzenia migracji minimalnych przestojach bazy danych MySQL do usługi Azure Database for MySQL za pomocą usługi Azure Database Migration Service.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 55d5cf97225508d6c7c490347cfe21ced832300e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091723"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migracja z minimalnym przestojem do usługi Azure Database for MySQL
Można przeprowadzić migracji MySQL do usługi Azure Database for MySQL przy minimalnych przestojach przy użyciu nowo wprowadzonych **możliwości ciągłej synchronizacji** dla [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Ta funkcja ogranicza czas przestoju, który jest naliczany przez aplikację.

## <a name="overview"></a>Przegląd
Usługa DMS wykonuje wstępnego obciążenia lokalne do usługi Azure Database for MySQL, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja jest uruchomiona. Po danych wyrównywane na docelowej stronie platformy Azure, Zatrzymaj aplikację na krótko (minimalnych przestojach), poczekaj, aż ostatniej partii danych (od czasu, Zatrzymaj aplikację, dopóki aplikacja jest skutecznie niedostępna do przyjmowania dowolnego nowego ruchu) do przechwytywania Konfigurowanie w elemencie docelowym, a następnie zaktualizuj parametry połączenia, aby wskazywała na platformie Azure. Gdy skończysz, Twoja aplikacja będzie na żywo na platformie Azure!

![Ciągła synchronizacja z usługą Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

- Migracja usługi DMS źródeł MySQL jest obecnie w publicznej wersji zapoznawczej. Jeśli chcesz wypróbować usługę Migrowanie obciążeń programu MySQL, przejdź dalej, w portalu. Twoja opinia jest bezcenne w ułatwienia Aby dodatkowo poprawić usługę.

## <a name="next-steps"></a>Kolejne kroki
- Obejrzyj klip wideo [łatwe migrowanie MySQL/postgresql w warstwie usługi zarządzanej aplikacji do platformy Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), który zawiera pokaz przedstawiający przeprowadzanie migracji aplikacji MySQL do usługi Azure Database for MySQL.
- [Migrowanie MySQL do usługi Azure Database for MySQL online przy użyciu usługi DMS] (https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
