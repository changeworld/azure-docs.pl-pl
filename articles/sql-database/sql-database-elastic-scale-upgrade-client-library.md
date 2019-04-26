---
title: Uaktualnianie do najnowszej Biblioteka kliencka elastic database | Dokumentacja firmy Microsoft
description: Należy użyć Nuget, aby biblioteka kliencka uaktualnienia elastic database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 02c09e5a2ad0c3e23fe641bf445a646477f7db2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335053"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uaktualnianie aplikacji przy użyciu najnowszych Biblioteka kliencka elastic database

Nowe wersje [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md) są dostępne za pośrednictwem NuGetand interfejsu Menedżera NuGetPackage w programie Visual Studio. Uaktualnienia zawiera poprawki błędów i obsługa nowych funkcji z biblioteki klienta.

**Aby uzyskać najnowszą wersję:** Przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Ponowne kompilowanie aplikacji za pomocą nowej biblioteki, a także zmienić swoje istniejące metadane menedżera mapowań fragmentów przechowywanych w bazach danych Azure SQL do obsługi nowych funkcji.

Te kroki są wykonywane w kolejności zapewnia, że starsze wersje biblioteki klienta usługi nie są już obecne w danym środowisku podczas aktualizacji metadanych obiektów, co oznacza, że obiekty metadanych stara wersja nie zostaną utworzone po uaktualnieniu.

## <a name="upgrade-steps"></a>Procedura uaktualniania

**1. Uaktualnienie aplikacji.** W programie Visual Studio Pobierz i odwoływać się do najnowszej wersji biblioteki klienta do wszystkich swoich projektach deweloperskich, korzystające z biblioteki; następnie należy ponownie skompilować i wdrożyć.

* W rozwiązaniu programu Visual Studio wybierz **narzędzia** --> **Menedżera pakietów NuGet** -->  **Zarządzaj pakietami NuGet dla rozwiązania**.
* (Visual Studio 2013) W panelu po lewej stronie wybierz **aktualizacje**, a następnie wybierz pozycję **aktualizacji** przycisk na pakiet **Azure SQL Database elastycznej biblioteki klienckiej skalowania** wyświetlany w oknie.
* (Visual Studio 2015) Ustaw pole filtru **dostępną aktualizację**. Wybierz pakiet aktualizacji, a następnie kliknij przycisk **aktualizacji** przycisku.
* (Visual Studio 2017) W górnej części okna dialogowego wybierz **aktualizacje**. Wybierz pakiet aktualizacji, a następnie kliknij przycisk **aktualizacji** przycisku.
* Tworzenie i wdrażanie.

**2. Uaktualnij skryptów.** Jeśli używasz **PowerShell** skrypty do zarządzania fragmentami, [Pobierz nową wersję biblioteki](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) i skopiować go do katalogu, w którym uruchamiany jest skrypt.

**3. Uaktualnij usługi dzielenia i scalania.** Jeśli używasz narzędzia do dzielenia i scalania elastycznej bazy danych do reorganizowanie danych podzielonych na fragmenty [pobrać i zainstalować najnowszą wersję narzędzia](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Szczegółowe kroki uaktualnienia można znaleźć usługi [tutaj](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Uaktualnianie bazy danych Menedżera mapowań fragmentów**. Uaktualnij metadanych, obsługa map fragmentów w usłudze Azure SQL Database.  Istnieją dwa sposoby, można to zrobić, za pomocą programu PowerShell lub C#. Poniżej przedstawiono obie opcje.

***Opcja 1: Uaktualnianie metadanych przy użyciu programu PowerShell***

1. Pobierz najnowsze narzędzia wiersza polecenia dla NuGet z [tutaj](https://nuget.org/nuget.exe) i Zapisz w folderze.
2. Otwórz wiersz polecenia, przejdź do folderu i wydać polecenie: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Przejdź do podfolderu, zawierający nową wersję klienta biblioteki DLL, po prostu pobrany, na przykład: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Pobierz skrypt uaktualnienia klienta elastycznej bazy danych z [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)i zapisz go do folderu zawierającego bibliotekę DLL.
5. Z tego folderu Uruchom ".\upgrade.ps1 programu PowerShell" w wierszu polecenia, a następnie postępuj zgodnie z monitami.

***Opcja 2: Uaktualnianie za pomocą metadanychC#***

Alternatywnie, tworzenie aplikacji programu Visual Studio otwiera ShardMapManager użytkownika, który wykonuje iterację na wszystkich fragmentów i wykonuje Uaktualnianie metadanych przez wywołanie metody [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) i [UpgradeGlobalStore ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) jak w poniższym przykładzie:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Techniki metadane uaktualnienia można stosować wiele razy bez szkody. Na przykład jeśli starsza wersja klienta przypadkowo tworzy fragment, po dokonaniu aktualizacji, możesz uruchomić uaktualnianie ponownie między wszystkimi fragmentami, aby upewnić się, że najnowsza wersja metadanych jest obecny w całej infrastrukturze.

**Uwaga:**  Nowe wersje biblioteki klienta opublikowana do chwili kontynuować pracę z wcześniejszych wersji metadanych Menedżera mapowań fragmentów w usłudze Azure SQL DB i na odwrót.   Jednak aby móc korzystać z niektórych nowych funkcji w najnowszego klienta, metadanych musi zostać uaktualniony.   Należy pamiętać, że metadane uaktualnienia nie wpłynie na wszystkie dane użytkownika lub dane specyficzne dla aplikacji, tylko obiekty tworzone i używane przez Menedżera mapowań fragmentów.  I aplikacje w dalszym ciągu działać przy użyciu sekwencji uaktualniania opisany powyżej.

## <a name="elastic-database-client-version-history"></a>Historia wersji klienta elastycznej bazy danych

Aby uzyskać historii wersji, przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png