---
title: Stan scenariusza migracji bazy danych
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o stanie scenariuszy migracji obsługiwanych przez usługę migracji bazy danych Platformy Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254931"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stan scenariuszy migracji obsługiwanych przez usługę migracji bazy danych Platformy Azure

Usługa migracji bazy danych Azure została zaprojektowana do obsługi różnych scenariuszy migracji (par źródłowych/docelowych) zarówno dla migracji offline (jednorazowe), jak i online (synchronizacja ciągła). Zakres scenariusza zapewniany przez usługę migracji bazy danych platformy Azure jest rozszerzany w czasie. Nowe scenariusze są dodawane regularnie. W tym artykule określono scenariusze migracji obecnie obsługiwane przez usługę migracji usługi Azure Database i stan (prywatna wersja zapoznawcza, publiczna wersja zapoznawcza lub ogólna dostępność) dla każdego scenariusza.

## <a name="offline-versus-online-migrations"></a>Migracje offline a online

Dzięki usłudze migracji bazy danych azure można wykonać migrację w trybie offline lub online. W przypadku migracji *w trybie offline* przestoje aplikacji rozpoczynają się w tym samym czasie, w tym co rozpoczyna się migracja. Aby ograniczyć przestoje do czasu wymaganego do przekrocia do nowego środowiska po zakończeniu migracji, użyj migracji *online.* Zaleca się przetestowanie migracji w trybie offline, aby ustalić, czy czas przestoju jest dopuszczalny; jeśli nie, wykonaj migrację online.

## <a name="migration-scenario-status"></a>Stan scenariusza migracji

Stan scenariuszy migracji obsługiwanych przez usługę migracji bazy danych Azure różni się w zależności od czasu. Ogólnie rzecz biorąc scenariusze są najpierw wydawane w **prywatnej wersji zapoznawczej**. Udział w prywatnej wersji zapoznawczej wymaga od klientów przesłania nominacji za pośrednictwem [witryny DMS Preview.](https://aka.ms/dms-preview) Po prywatnej wersji zapoznawczej stan scenariusza zmienia się na **publiczną wersję zapoznawczą**. Użytkownicy usługi migracji bazy danych Azure mogą wypróbować scenariusze migracji w publicznej wersji zapoznawczej bezpośrednio z interfejsu użytkownika. Rejestracja nie jest wymagana.  Jednak scenariusze migracji w publicznej wersji zapoznawczej mogą nie być dostępne we wszystkich regionach i mogą ulec dodatkowym zmianom przed ostateczną wersją. Po publicznej wersji zapoznawczej stan scenariusza zmienia się **na ogólnie dostępność**. Ogólna dostępność (GA) jest stanem ostatecznej wersji, a funkcja jest kompletna i dostępna dla wszystkich użytkowników.

## <a name="migration-scenario-support"></a>Obsługa scenariuszy migracji

W poniższych tabelach pokazano, które scenariusze migracji są obsługiwane podczas korzystania z usługi migracji bazy danych Azure.

> [!NOTE]
> Jeśli scenariusz wymieniony jako obsługiwany poniżej nie pojawia się w interfejsie użytkownika, skontaktuj się z [aliasem Ask Azure Database Migrations,](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aby uzyskać dodatkowe informacje.

> [!IMPORTANT]
> Aby wyświetlić wszystkie scenariusze obsługiwane obecnie przez usługę migracji usługi Azure Database w prywatnej wersji zapoznawczej, zobacz [witrynę DMS Preview](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Obsługa migracji w trybie offline (jednorazowa)

W poniższej tabeli przedstawiono obsługę usługi migracji bazy danych Azure dla migracji w trybie offline.

| Środowisko docelowe  | Element źródłowy | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Obiekt DB usługi Azure SQL** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Maszyna wirtualna Azure SQL** | SQL Server | ✔ | Ogólna dostępność |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Ogólna dostępność |
| **Azure DB dla MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB dla PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Obsługa migracji online (synchronizacji ciągłej)

W poniższej tabeli przedstawiono obsługę usługi migracji bazy danych Azure dla migracji online.

| Środowisko docelowe  | Element źródłowy | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | ✔ | Ogólna dostępność |
|   | Oracle |  |  |
| **Obiekt DB usługi Azure SQL** | SQL Server | ✔ | Ogólna dostępność |
|   | RDS SQL | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Prywatny podgląd |
| **Maszyna wirtualna Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Ogólna dostępność |
| **Azure DB dla MySQL** | MySQL | ✔ | Ogólna dostępność |
|   | RDS MySQL | ✔ | Ogólna dostępność |
| **Azure DB dla PostgreSQL** | PostgreSQL | ✔ | Ogólna dostępność |
|   | RDS PostgreSQL | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Publiczna wersja zapoznawcza |

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem usługi migracji bazy danych platformy Azure i dostępności regionalnej, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure](dms-overview.md).
