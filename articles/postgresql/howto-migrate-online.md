---
title: Migracja minimalnym czasem przestojów PostgreSQL bazą danych Azure
description: W tym artykule opisano, jak przeprowadzić migrację minimalnym czasem przestojów bazy danych programu PostgreSQL do bazy danych platformy Azure dla PostgreSQL za pomocą usługi Azure migracji bazy danych.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9ab5d4615a8baf763d0b7ee47bf0890124f8665c
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292546"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migracja minimalnym czasem przestojów PostgreSQL bazą danych Azure
Można wykonać migracji PostgreSQL bazą danych Azure dla PostgreSQL z minimalnym czasem przestojów przy użyciu nowo wprowadzonych **możliwość synchronizacji ciągłego** dla [usługi migracji bazy danych Azure](https://aka.ms/get-dms) (DMS) . Ta funkcja ogranicza przestoje związane z aplikacji.

## <a name="overview"></a>Przegląd
Usługa DMS przeprowadza PostgreSQL ładowania początkowego lokalną bazą danych Azure, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja jest uruchomiona. Po danych wyrównania na docelowej stronie platformy Azure, Zatrzymaj aplikację na krótko (minimalny czas przestoju), poczekaj, aż ostatniej partii danych (od momentu zatrzymania aplikacji do momentu aplikacja jest skutecznie niedostępne do wykonania dowolnego natężenia ruchu) do catch Konfigurowanie w miejscu docelowym, a następnie zaktualizuj parametry połączenia, aby wskazywały na platformie Azure. Gdy skończysz, aplikacja będzie na żywo na platformie Azure!

![Ciągłe synchronizacji z usługą Azure bazy danych migracji](./media/howto-migrate-online/ContinuousSync.png)

Usługa DMS migracji źródeł PostgreSQL jest obecnie w przeglądzie. Jeśli chcesz wypróbować usługę, aby przeprowadzić migrację obciążeń PostgreSQL, zarejestruj się za pośrednictwem Azure DMS [strony podglądu](https://aka.ms/dms-preview) Express zainteresowanie. Opinie użytkowników są cenne użytkownikom dalsze usprawnienia świadczonej usługi.

## <a name="next-steps"></a>Kolejne kroki
- Obejrzyj klip wideo [modernizacji aplikacji na platformie Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), który zawiera pokaz przedstawiający przeprowadzanie migracji aplikacji PostgreSQL bazą danych Azure dla PostgreSQL.
- Załóż ograniczony podglądu z minimalnym czasem przestojów migracji PostgreSQL bazą danych Azure dla PostgreSQL za pośrednictwem Azure DMS [strony podglądu](https://aka.ms/dms-preview).
