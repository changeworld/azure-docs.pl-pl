---
title: Stan scenariusza migracji bazy danych | Dokumentacja firmy Microsoft
description: Informacje dotyczące stanu scenariuszy migracji, obsługiwane przez usługi Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: 4159b2e7af83030f46d5aca150ef99a1380e711f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473012"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stan scenariuszy migracji obsługiwanych przez usługi Azure Database Migration Service
Azure Database Migration Service jest przeznaczona do obsługi różnych scenariuszy migracji (źródło i cel pary) zarówno w trybie offline (jednorazowy) i migracji online (ciągłej synchronizacji). Pokrycie scenariusza dostarczane przez usługi Azure Database Migration Service jest rozszerzany wraz z upływem czasu. Udostępnione nowe scenariusze są dodawane na bieżąco. W tym artykule identyfikuje scenariusze migracji, które są obecnie obsługiwane przez usługi Azure Database Migration Service i stan (prywatna wersja zapoznawcza, publicznej wersji zapoznawczej lub ogólnie dostępne) dla każdego scenariusza.

## <a name="offline-versus-online-migrations"></a>W trybie offline i online migracji
Azure Database Migration Service można wykonać migracji online lub offline. Za pomocą *offline* migracji, w tym samym czasie, który rozpoczyna się migracja rozpoczyna się przerwy w działaniu aplikacji. Aby ograniczyć czas przestoju do czasu wymaganego do jednorazową do nowego środowiska po zakończeniu migracji, należy użyć *online* migracji. Zaleca się testowania migracji w trybie offline w celu ustalenia, czy przestój jest dopuszczalny; Jeśli nie, czy migracja online.

## <a name="migration-scenario-status"></a>Stan scenariusza migracji
Stan scenariuszy migracji obsługiwanych przez usługi Azure Database Migration Service zależy od czasu. Ogólnie rzecz biorąc, scenariusze są wydawane najpierw w **prywatnej wersji zapoznawczej**. Uczestnictwa w prywatnej wersji zapoznawczej wymaga od klientów Prześlij nominację za pośrednictwem [witryny w wersji zapoznawczej usługi DMS](https://aka.ms/dms-preview). Po prywatnej wersji zapoznawczej, scenariusz stan zmieni się na **publicznej wersji zapoznawczej**. Użytkownicy usługi Azure Database Migration Service można wypróbować scenariusze migracji w publicznej wersji zapoznawczej bezpośrednio z poziomu interfejsu użytkownika. Nie rejestracji jest wymagana.  Jednak scenariusze migracji w publicznej wersji zapoznawczej mogą nie być dostępne we wszystkich regionach i mogą być poddawane dodatkowe zmiany przed ostateczną. Po publicznej wersji zapoznawczej, scenariusz stan zmieni się na **ogólnie dostępności**. Ogólna dostępność (GA) jest stanem ostatecznej wersji i funkcje są kompletne i dostępne dla wszystkich użytkowników.

## <a name="migration-scenario-support"></a>Obsługa scenariuszy migracji
W poniższych tabelach przedstawiono scenariuszy migracji, do których są obsługiwane w przypadku korzystania z usługi Azure Database Migration Service.

> [!NOTE]
> Jeśli scenariusz wymieniony jako obsługiwany poniżej nie jest wyświetlany w interfejsie użytkownika, skontaktuj się z pomocą [zespołem migracji danych](mailto:datamigrationteam@microsoft.com) Aby uzyskać dodatkowe informacje.

> [!IMPORTANT]
> Aby wyświetlić wszystkie scenariusze, które są obecnie obsługiwane przez usługi Azure Database Migration Service w prywatnej wersji zapoznawczej, zobacz [witryny w wersji zapoznawczej usługi DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Obsługa migracji w trybie offline (jednorazowy)
W poniższej tabeli przedstawiono usługi Azure Database Migration Service obsługę migracji w trybie offline.

| Środowisko docelowe  | Element źródłowy | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Warstwa bazy danych Azure SQL** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Maszyna wirtualna Azure SQL** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Publiczna wersja zapoznawcza |
| **Azure DB dla MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB dla PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Obsługa migracji online (ciągłej synchronizacji)
W poniższej tabeli przedstawiono usługi Azure Database Migration Service obsługę migracji online.

| Środowisko docelowe  | Element źródłowy | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | ✔ | Ogólna dostępność |
|   | Oracle |  |  |
| **Warstwa bazy danych Azure SQL** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Prywatna wersja zapoznawcza |
| **Maszyna wirtualna Azure SQL** | Oprogramowanie SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Publiczna wersja zapoznawcza |
| **Azure DB dla MySQL** | MySQL | ✔ | Ogólna dostępność |
|   | RDS MySQL | ✔ | Ogólna dostępność |
| **Azure DB dla PostgreSQL** | PostgreSQL | ✔ | Ogólna dostępność |
|   | RDS PostgreSQL | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Prywatna wersja zapoznawcza |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać omówienie usługi Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
