---
title: Stan scenariusza migracji bazy danych | Dokumentacja firmy Microsoft
description: Informacje dotyczące stanu scenariuszy migracji, obsługiwane przez usługi Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: e9f1b1a3df92c08b913a56aadc8eb8a9e80c7fdf
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320059"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Stan scenariuszy migracji obsługiwanych przez usługi Azure Database Migration Service
Azure Database Migration Service jest przeznaczona do obsługi różnych scenariuszy migracji (źródło i cel pary) zarówno w trybie offline (jednorazowy) i migracji online (ciągłej synchronizacji). Pokrycie scenariusza dostarczane przez usługi Azure Database Migration Service jest rozszerzany wraz z upływem czasu. Udostępnione nowe scenariusze są dodawane na bieżąco. W tym artykule identyfikuje scenariusze migracji, są obecnie obsługiwane przez usługi Azure Database Migration Service i stan (prywatnej [lub ograniczone] (wersja zapoznawcza), publiczną wersję zapoznawczą lub jest ogólnie dostępna) lub każdego scenariusza.

## <a name="offline-versus-online-migrations"></a>W trybie offline i online migracji
Przeprowadzając migrację baz danych na platformie Azure przy użyciu usługi Azure Database Migration Service, można wykonać migracji online lub offline. Za pomocą *offline* migracji, w tym samym czasie, który rozpoczyna się migracja rozpoczyna się przerwy w działaniu aplikacji. Aby uzyskać *online* migracji, okres przestoju jest ograniczony do czasu wymaganego do jednorazową do nowego środowiska po zakończeniu migracji. Zaleca się testowania migracji w trybie offline w celu ustalenia, czy przestój jest dopuszczalny; Jeśli nie, należy dokonać migracji online.

## <a name="migration-scenario-status"></a>Stan scenariusza migracji
Stan każdego scenariusza migracji, obsługiwane przez usługi Azure Database Migration Service zależy od czasu. Ogólnie rzecz biorąc, scenariusze są wydawane najpierw w **prywatnej wersji zapoznawczej**, korzystając z zalet funkcji wymaga, że klient Prześlij nominację za pośrednictwem [witryny w wersji zapoznawczej usługi DMS](https://aka.ms/dms-preview). Po zakończeniu prywatnej wersji zapoznawczej scenariuszu stan zmieni się na **publicznej wersji zapoznawczej**. Wszyscy użytkownicy usługi Azure Database Migration Service korzystać z zalet scenariusze migracji jest dostępne w publicznej wersji zapoznawczej. Jednak scenariusz migracji nie mogą być dostępne we wszystkich regionach i funkcje mogą być poddawane dodatkowe zmiany przed ostateczną. Jeśli scenariusz migracji staje się **jest ogólnie dostępna**, stan końcowy, wydanej funkcji są kompletne i dostępne dla wszystkich użytkowników usługi Azure Database Migration Service. 

## <a name="migration-scenario-support"></a>Obsługa scenariuszy migracji

W poniższych tabelach przedstawiono scenariuszy migracji, do których są obsługiwane w przypadku korzystania z usługi Azure Database Migration Service.

> [!NOTE]
> Jeśli scenariusz wymieniony jako obsługiwany poniżej nie jest wyświetlany w interfejsie użytkownika, skontaktuj się z pomocą [zespołem migracji danych](mailto:datamigrationteam@microsoft.com) Aby uzyskać dodatkowe informacje.

### <a name="offline-one-time-migration-support"></a>Obsługa migracji w trybie offline (jednorazowy)
W poniższej tabeli przedstawiono usługi Azure Database Migration Service obsługę migracji w trybie offline.

| Środowisko docelowe  | Element źródłowy | Pomoc techniczna |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | Oprogramowanie SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Warstwa bazy danych Azure SQL**  | Oprogramowanie SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Maszyna wirtualna Azure SQL**  | Oprogramowanie SQL Server | ✔ |
|   | Oracle  |   |
| **Cosmos DB**  | MongoDB | ✔ |
| **Azure DB dla MySQL**  | MySQL |  |
|   | RDS MySQL  |  |
| **Azure DB dla PostgresSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Obsługa migracji online (ciągłej synchronizacji)
W poniższej tabeli przedstawiono usługi Azure Database Migration Service obsługę migracji online.

| Środowisko docelowe  | Element źródłowy | Pomoc techniczna |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | Oprogramowanie SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Warstwa bazy danych Azure SQL**  | Oprogramowanie SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Maszyna wirtualna Azure SQL**  | Oprogramowanie SQL Server  |   |
|   | Oracle  | ✔  |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB dla MySQL**  | MySQL | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB dla PostgresSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać omówienie usługi Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md). 
