---
title: Stan scenariusza migracji bazy danych | Microsoft Docs
description: Dowiedz się więcej na temat stanu scenariuszy migracji obsługiwanych przez Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/13/2019
ms.openlocfilehash: 44ac290a471fd0099b6589f84fea604249818432
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868612"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stan scenariuszy migracji obsługiwanych przez Azure Database Migration Service

Azure Database Migration Service zaprojektowano w celu obsługi różnych scenariuszy migracji (par Source/Target) zarówno w przypadku migracji w trybie offline, jak i w trybie online (synchronizacja ciągła). Pokrycie scenariusza zapewniane przez Azure Database Migration Service jest rozszerzane z upływem czasu. Nowe scenariusze są dodawane regularnie. Ten artykuł zawiera zidentyfikowane scenariusze migracji obsługiwane przez Azure Database Migration Service i stan (prywatna wersja zapoznawcza, publiczna wersja zapoznawcza lub Ogólna dostępność) dla każdego scenariusza.

## <a name="offline-versus-online-migrations"></a>Offline a migracja online

Za pomocą Azure Database Migration Service można przeprowadzić migrację w trybie offline lub w trybie online. W przypadku migracji w *trybie offline* przestoje aplikacji zaczynają się w tym samym czasie, gdy migracja rozpocznie się. Aby ograniczyć czas przestoju do czasu wymaganego do pocięcia z nowym środowiskiem po zakończeniu migracji, należy przeprowadzić migrację w *trybie online* . Zaleca się przetestowanie migracji w trybie offline w celu ustalenia, czy przestój jest akceptowalny; Jeśli nie, wykonaj migrację w trybie online.

## <a name="migration-scenario-status"></a>Stan scenariusza migracji

Stan scenariuszy migracji obsługiwanych przez Azure Database Migration Service różni się w zależności od czasu. Ogólnie rzecz biorąc, scenariusze są najpierw udostępniane w **prywatnej wersji**zapoznawczej. Uczestnictwo w prywatnej wersji zapoznawczej wymaga od klientów przedstawienia nominacji za pośrednictwem [witryny DMS Preview](https://aka.ms/dms-preview). Po prywatnej wersji zapoznawczej stan scenariusza zmieni się na publiczną wersję zapoznawczą. Azure Database Migration Service użytkownicy mogą wypróbować scenariusze migracji w publicznej wersji zapoznawczej bezpośrednio z poziomu interfejsu użytkownika. Rejestracja nie jest wymagana.  Jednak scenariusze migracji w publicznej wersji zapoznawczej mogą nie być dostępne we wszystkich regionach i mogą zostać wprowadzone dodatkowe zmiany przed ostateczną wersją. Po publicznej wersji zapoznawczej stan scenariusza zmieni się na **ogólnie dostępny**. Ogólna dostępność (GA) to końcowy stan wersji, a funkcje są kompletne i dostępne dla wszystkich użytkowników.

## <a name="migration-scenario-support"></a>Obsługa scenariusza migracji

W poniższych tabelach przedstawiono, które scenariusze migracji są obsługiwane w przypadku korzystania z Azure Database Migration Service.

> [!NOTE]
> Jeśli opisany poniżej scenariusz nie jest wyświetlany w interfejsie użytkownika, skontaktuj się z aliasem [usługi Azure Database migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) , aby uzyskać dodatkowe informacje.

> [!IMPORTANT]
> Aby wyświetlić wszystkie scenariusze aktualnie obsługiwane przez Azure Database Migration Service w prywatnej wersji zapoznawczej, zobacz [witrynę DMS w wersji](https://aka.ms/dms-preview)zapoznawczej.

### <a name="offline-one-time-migration-support"></a>Obsługa migracji w trybie offline (jednorazowej)

W poniższej tabeli przedstawiono Azure Database Migration Service obsługi migracji w trybie offline.

| Cel  | Source | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Baza danych SQL Azure** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | SQL RDS |  |  |
|   | Oracle |  |  |
| **Baza danych Azure SQL** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | SQL RDS |  |  |
|   | Oracle |  |   |
| **Maszyna wirtualna Azure SQL** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | Oracle |   |   |
| **Usługi Azure Cosmos DB** | MongoDB | ✔ | Ogólna dostępność |
| **Azure DB dla MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB dla PostgreSQL** | PostgreSQL |  |
|  | PostgreSQL RDS |   |   |

### <a name="online-continuous-sync-migration-support"></a>Obsługa migracji w trybie online (ciągła synchronizacja)

W poniższej tabeli przedstawiono Azure Database Migration Service obsługi migracji w trybie online.

| Cel  | Source | Pomoc techniczna | Stan |
| ------------- | ------------- |:-------------:|:-------------:|
| **Baza danych SQL Azure** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | SQL RDS | ✔ | Ogólna dostępność |
|   | Oracle |  |  |
| **Baza danych Azure SQL** | Oprogramowanie SQL Server | ✔ | Ogólna dostępność |
|   | SQL RDS | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Prywatna wersja zapoznawcza |
| **Maszyna wirtualna Azure SQL** | Oprogramowanie SQL Server |   |   |
|   | Oracle  |  |  |
| **Usługi Azure Cosmos DB** | MongoDB | ✔ | Ogólna dostępność |
| **Azure DB dla MySQL** | MySQL | ✔ | Ogólna dostępność |
|   | RDS MySQL | ✔ | Ogólna dostępność |
| **Azure DB dla PostgreSQL** | PostgreSQL | ✔ | Ogólna dostępność |
|   | PostgreSQL RDS | ✔ | Ogólna dostępność |
|   | Oracle | ✔ | Prywatna wersja zapoznawcza |

## <a name="next-steps"></a>Kolejne kroki

Aby zapoznać się z omówieniem Azure Database Migration Service i regionalnej dostępności, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
