---
title: Uaktualnienie do najnowszej biblioteki klienta elastycznej bazy danych
description: Użyj Nuget, aby uaktualnić bibliotekę klienta elastycznej bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: a1ab684f13c56698d4359f2bf74826f3dd696c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823505"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uaktualnianie aplikacji w celu korzystania z najnowszej biblioteki klienta elastycznej bazy danych

Nowe wersje [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) są dostępne za pośrednictwem nuget i interfejsu Menedżera pakietów NuGet w programie Visual Studio. Uaktualnienia zawierają poprawki błędów i obsługę nowych możliwości biblioteki klienta.

**Najnowsza wersja:** Przejdź do [witryny Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Przebuduj aplikację za pomocą nowej biblioteki, a także zmień istniejące metadane usługi Shard Map Manager przechowywane w bazach danych SQL platformy Azure w celu obsługi nowych funkcji.

Wykonanie tych kroków w celu zapewnienia, że stare wersje biblioteki klienta nie są już obecne w środowisku, gdy obiekty metadanych są aktualizowane, co oznacza, że obiekty metadanych starej wersji nie zostaną utworzone po uaktualnieniu.

## <a name="upgrade-steps"></a>Kroki uaktualnienia

**1. Uaktualnij swoje aplikacje.** W programie Visual Studio pobierz i odwołaj się do najnowszej wersji biblioteki klienta do wszystkich projektów deweloperskich, które używają biblioteki; następnie odbudować i wdrożyć.

* W rozwiązaniu programu Visual Studio wybierz pozycję **Narzędzia** --> **NuGet Package Manager** -->  **Manage Pakiety NuGet dla rozwiązania.**
* (Visual Studio 2013) W lewym panelu wybierz pozycję **Aktualizacje**, a następnie wybierz przycisk **Aktualizuj** w pakiecie **biblioteki klienta elastycznej skali bazy danych SQL,** który pojawia się w oknie.
* (Visual Studio 2015) Ustaw pole Filtruj, aby **uaktualnić dostępne**. Wybierz pakiet do aktualizacji i kliknij przycisk **Aktualizuj.**
* (Visual Studio 2017) U góry okna dialogowego wybierz pozycję **Aktualizacje**. Wybierz pakiet do aktualizacji i kliknij przycisk **Aktualizuj.**
* Tworzenie i wdrażanie.

**2. Uaktualnij swoje skrypty.** Jeśli do zarządzania fragmentami używasz skryptów **programu PowerShell,** [pobierz nową wersję biblioteki](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) i skopiuj ją do katalogu, z którego wykonujesz skrypty.

**3. Uaktualnij usługę scalania dzielonego.** Jeśli używasz narzędzia do scalania podzielonej bazy danych w celu reorganizacji podzielonych danych, [pobierz i wdrożysz najnowszą wersję narzędzia](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Szczegółowe kroki uaktualnienia usługi można znaleźć [tutaj](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Uaktualnij bazy danych Menedżera map odłamków.** Uaktualnij metadane obsługujące mapy niezależnego fragmentu w bazie danych SQL Azure.  Istnieją dwa sposoby, można to osiągnąć, za pomocą programu PowerShell lub C#. Obie opcje są pokazane poniżej.

***Opcja 1: Uaktualnianie metadanych przy użyciu programu PowerShell***

1. Pobierz najnowsze narzędzie wiersza polecenia dla NuGet [stąd](https://nuget.org/nuget.exe) i zapisz w folderze.
2. Otwórz wiersz polecenia, przejdź do tego samego folderu i wydaj polecenie:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Przejdź do podfolderu zawierającego nową wersję biblioteki DLL klienta, którą właśnie pobrano, na przykład:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Pobierz skrypt uaktualnienia klienta elastycznej bazy danych z [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)i zapisz go w tym samym folderze zawierającym bibliotekę DLL.
5. Z tego folderu uruchom program "PowerShell .\upgrade.ps1" w wierszu polecenia i postępuj zgodnie z instrukcjami.

***Opcja 2: Uaktualnianie metadanych przy użyciu języka C #***

Alternatywnie należy utworzyć aplikację programu Visual Studio, która otwiera ShardMapManager, iteruje wszystkie fragmenty i wykonuje uaktualnienie metadanych, wywołując metody [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) i [UpgradeGlobalStore,](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) jak w tym przykładzie:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Te techniki uaktualniania metadanych mogą być stosowane wiele razy bez szkody. Na przykład jeśli starsza wersja klienta przypadkowo tworzy niezależnego fragmentu po zaktualizowaniu, można uruchomić uaktualnienie ponownie we wszystkich fragmentów, aby upewnić się, że najnowsza wersja metadanych jest obecny w całej infrastruktury.

**Uwaga:**  Nowe wersje biblioteki klienta opublikowane do tej pory nadal pracować z wcześniejszych wersji metadanych menedżera map niezależnego fragmentu na usłudze Azure SQL DB i odwrotnie.   Jednak aby skorzystać z niektórych nowych funkcji w najnowszym kliencie, metadane muszą zostać uaktualnione.   Należy zauważyć, że uaktualnienia metadanych nie będą miały wpływu na żadne dane użytkownika lub dane specyficzne dla aplikacji, tylko obiekty utworzone i używane przez Menedżera map niezależnego fragmentu.  A aplikacje nadal działają za pośrednictwem sekwencji uaktualnienia opisanej powyżej.

## <a name="elastic-database-client-version-history"></a>Historia wersji klienta elastycznej bazy danych

Aby uzyskać historię wersji, przejdź do [witryny Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
