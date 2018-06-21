---
title: Minimalizująca przestoje migracji do bazy danych Azure dla programu MySQL
description: W tym artykule opisano sposób przeprowadzenia migracji minimalnym czasem przestojów bazy danych programu MySQL do bazy danych platformy Azure dla programu MySQL za pomocą usługi Azure migracji bazy danych.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: ecbd35bd45bd11292bbe4a032329d704858d4c77
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293925"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimalizująca przestoje migracji do bazy danych Azure dla programu MySQL
Można wykonać migracji MySQL bazą danych Azure dla programu MySQL z minimalnym czasem przestojów przy użyciu nowo wprowadzonych **możliwość synchronizacji ciągłego** dla [usługi migracji bazy danych Azure](https://aka.ms/get-dms) (DMS). Ta funkcja ogranicza przestoje związane z aplikacji.

## <a name="overview"></a>Przegląd
Usługa DMS wykonuje ładowania początkowego lokalną bazą danych Azure dla programu MySQL, a następnie stale synchronizuje wszystkie nowe transakcje na platformie Azure, gdy aplikacja jest uruchomiona. Po danych wyrównania na docelowej stronie platformy Azure, Zatrzymaj aplikację na krótko (minimalny czas przestoju), poczekaj, aż ostatniej partii danych (od momentu zatrzymania aplikacji do momentu aplikacja jest skutecznie niedostępne do wykonania dowolnego natężenia ruchu) do catch Konfigurowanie w miejscu docelowym, a następnie zaktualizuj parametry połączenia, aby wskazywały na platformie Azure. Gdy skończysz, aplikacja będzie na żywo na platformie Azure!

![Ciągłe synchronizacji z usługą Azure bazy danych migracji](./media/howto-migrate-online/ContinuousSync.png)

Usługa DMS migracji źródeł MySQL jest obecnie w przeglądzie. Jeśli chcesz wypróbować usługę, aby przeprowadzić migrację obciążeń MySQL, zarejestruj się za pośrednictwem Azure DMS [strony podglądu](https://aka.ms/dms-preview) Express zainteresowanie. Opinie użytkowników są cenne użytkownikom dalsze usprawnienia świadczonej usługi.

## <a name="next-steps"></a>Kolejne kroki
- Obejrzyj klip wideo [łatwo przeprowadzić migrację MySQL/PostgreSQL aplikacje na platformie Azure zarządzane usługi](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), który zawiera pokaz przedstawiający przeprowadzanie migracji aplikacji MySQL bazą danych Azure dla programu MySQL.
- Załóż ograniczony podglądu z minimalnym czasem przestojów migracji MySQL bazą danych Azure dla programu MySQL za pośrednictwem Azure DMS [strony podglądu](https://aka.ms/dms-preview).
