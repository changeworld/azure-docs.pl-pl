---
title: Migracja z minimalnym przestojem — usługa Azure Database for MySQL
description: W tym artykule opisano sposób przeprowadzania migracji minimalnego przestoju bazy danych MySQL do bazy danych Azure Database dla MySQL przy użyciu usługi migracji bazy danych Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063338"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimalna migracja przestojów do bazy danych usługi Azure database dla mysql
Migracje MySQL do bazy danych Platformy Azure dla mysql przy minimalnym przestoju przy użyciu nowo **wprowadzonej funkcji ciągłej synchronizacji** usługi [migracji bazy danych (DMS).](https://aka.ms/get-dms) Ta funkcja ogranicza ilość przestojów, które są ponoszone przez aplikację.

## <a name="overview"></a>Omówienie
Usługa Azure DMS wykonuje początkowe obciążenie lokalnego do usługi Azure Database for MySQL, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja pozostaje uruchomiona. Po dogonieniu danych po stronie docelowej platformy Azure, możesz zatrzymać aplikację na krótką chwilę (minimalny czas przestoju), poczekaj na ostatnią partię danych (od momentu zatrzymania aplikacji, aż aplikacja będzie skutecznie niedostępna do podjęcia nowego ruchu) do połowu) w celu docelowych, a następnie zaktualizuj parametry połączenia, aby wskazywały platformę Azure. Po zakończeniu aplikacja będzie dostępna na platformie Azure!

![Ciągła synchronizacja z usługą migracji bazy danych platformy Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Następne kroki
- Zobacz klip wideo [Łatwo migruj aplikacje MySQL/PostgreSQL do usługi zarządzanej platformy Azure,](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)która zawiera pokaz pokazujący, jak migrować aplikacje MySQL do usługi Azure Database for MySQL.
- Zobacz samouczek [Migrowanie mysql do usługi Azure Database dla MySQL online przy użyciu DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
