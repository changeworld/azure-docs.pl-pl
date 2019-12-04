---
title: Migracja z minimalnym przestojem — Azure Database for MySQL
description: W tym artykule opisano, jak przeprowadzić migrację bazy danych MySQL o minimalnym przestoju do Azure Database for MySQL przy użyciu Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f166323cc72cd22cc4dd28babdfd056100a32e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774147"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migracja z minimalnym przestojem do Azure Database for MySQL
Migracje MySQL można przeprowadzić w celu Azure Database for MySQL z minimalnym przestojem przy użyciu nowo wprowadzonej **funkcji ciągłej synchronizacji** dla [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Ta funkcja ogranicza czas przestoju nanoszony przez aplikację.

## <a name="overview"></a>Przegląd
Usługa Azure DMS wykonuje początkowe ładowanie danych lokalnych w celu Azure Database for MySQL, a następnie nieustannie synchronizuje wszelkie nowe transakcje z platformą Azure, gdy aplikacja pozostaje uruchomiona. Po przechwyceniu danych na docelowej stronie platformy Azure aplikacja zostanie zatrzymana przez krótki czas (minimalny czas przestoju). Poczekaj na ostatnią partię danych (od momentu zatrzymania aplikacji do momentu, gdy aplikacja nie będzie efektywnie dostępna do przechwycenia nowego ruchu) w górę w miejscu docelowym, a następnie zaktualizuj parametry połączenia tak, aby wskazywały platformę Azure. Gdy skończysz, Twoja aplikacja będzie aktywna na platformie Azure!

![Ciągła synchronizacja z Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Następne kroki
- Obejrzyj wideo z [łatwością Migruj aplikacje MySQL/PostgreSQL do usługi zarządzanej platformy Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), która zawiera demonstrację prezentującą sposób migrowania aplikacji mysql do Azure Database for MySQL.
- Zapoznaj się z samouczkiem [Migrowanie bazy danych MySQL do Azure Database for MySQL online przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
