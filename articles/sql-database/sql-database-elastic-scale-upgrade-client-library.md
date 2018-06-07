---
title: Uaktualnij do najnowszej biblioteki klienta elastycznej bazy danych | Dokumentacja firmy Microsoft
description: Użycie narzędzia Nuget do uaktualnienia elastycznej bazy danych klienta biblioteki.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 56500a21822f7c7122a20cb676b9ad307de49c87
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647014"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uaktualnij aplikację do korzystania z najnowszych biblioteki klienta elastycznej bazy danych
Nowe wersje [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) są dostępne za pośrednictwem NuGetand interfejsu Menedżera NuGetPackage w programie Visual Studio. Uaktualnień obsługę nowych funkcji biblioteki klienta oraz zawierać poprawki błędów.

**Aby uzyskać najnowszą wersję:** przejdź do [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Odbuduj aplikacji przy użyciu nowej biblioteki, a także zmienić istniejące metadanych Menedżera Map niezależnego fragmentu przechowywane w bazach danych SQL Azure do obsługi nowych funkcji.

Te kroki są wykonywane w kolejności gwarantuje, że starsze wersje biblioteki klienta nie są już dostępne w danym środowisku po zaktualizowaniu obiektów metadanych, co oznacza, że po uaktualnieniu nie można utworzyć obiektów stara wersja metadanych.   

## <a name="upgrade-steps"></a>Kroki uaktualniania
**1. Uaktualnienie aplikacji.** W programie Visual Studio Pobierz i odwołać się do wszystkich projektów programowanie korzystające z biblioteki; najnowszą wersję klienta w bibliotece następnie ponownie skompilować i wdrożyć. 

* W rozwiązaniu programu Visual Studio wybierz **narzędzia** --> **Menedżera pakietów NuGet** -->  **Zarządzaj pakietami NuGet dla rozwiązania**. 
* (Visual Studio 2013) W lewym panelu, wybierz **aktualizacje**, a następnie wybierz **aktualizacji** przycisk pakietu **SQL bazy danych elastyczne skalowanie biblioteki klienta usługi Azure** wyświetlany w oknie.
* (Visual Studio 2015) Wartość w polu filtru **uaktualnienia dostępne**. Wybierz pakiet aktualizacji, a następnie kliknij przycisk **aktualizacji** przycisku.
* (Visual Studio 2017) W górnej części okna dialogowego, wybierz **aktualizacje**. Wybierz pakiet aktualizacji, a następnie kliknij przycisk **aktualizacji** przycisku.
* Tworzenie i wdrażanie. 

**2. Uaktualnij skryptów.** Jeśli używasz **PowerShell** skryptów służących do zarządzania odłamków, [pobrać nową wersję biblioteki](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) i skopiować go do katalogu, z którego wykonywanie skryptów. 

**3. Uaktualnij usługę podziału scalania.** Jeśli za pomocą narzędzia scalania podziału elastycznej bazy danych można zreorganizować danych podzielonej [pobrać i zainstalować najnowszą wersję narzędzia](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Szczegółowe kroki uaktualniania można znaleźć usługi [tutaj](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Uaktualnienie bazy danych Menedżera Map niezależnego fragmentu**. Uaktualnij metadanych Obsługa map niezależnego fragmentu w bazie danych SQL Azure.  Istnieją dwa sposoby, można to zrobić, za pomocą programu PowerShell lub C#. Poniżej przedstawiono obie opcje.

***Opcja 1: Metadane aktualizacji przy użyciu programu PowerShell***

1. Pobierz najnowsze narzędzia wiersza polecenia programu NuGet z [tutaj](http://nuget.org/nuget.exe) i zapisać w folderze. 
2. Otwórz wiersz polecenia, przejdź do folderu i wydać polecenie: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Przejdź do podfolderu zawierający nową wersję klienta biblioteki DLL, po prostu pobrany, na przykład: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Pobierz skryptlet uaktualnienia klienta elastycznej bazy danych z [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)i zapisz go do folderu zawierającego plik DLL.
5. Z tego folderu Uruchom ".\upgrade.ps1 programu PowerShell" w wierszu polecenia, a następnie postępuj zgodnie z monitami.

***Opcja 2: Uaktualnienie metadanych za pomocą języka C#***

Można również utworzyć aplikację programu Visual Studio, która otwiera ShardMapManager Twojego, przechodzi przez wszystkie odłamków i przeprowadza uaktualnienie metadanych przez wywołanie metody [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) i [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) jak w poniższym przykładzie: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Techniki metadane uaktualnienia można zastosować wiele razy bez problemów. Na przykład jeśli starsza wersja klienta tworzy przypadkowo niezależnych po zaktualizowaniu już, można uruchomić uaktualnienia ponownie we wszystkich fragmentów, aby upewnić się, że znajduje się w całej infrastrukturze najnowsze wersja metadanych. 

**Uwaga:** nowych wersji biblioteki klienta opublikowana do daty nadal działać we wcześniejszych wersjach metadanych Menedżera Map niezależnego fragmentu bazy danych SQL Azure i na odwrót.   Jednak aby skorzystać z niektórych nowych funkcji w najnowszego klienta, metadane musi zostać uaktualniony.   Należy pamiętać, że metadane uaktualnienia nie wpłynie na wszystkie dane użytkownika lub dane specyficzne dla aplikacji, tylko obiekty tworzone i używane przez Menedżera Map niezależnego fragmentu.  I aplikacje w dalszym ciągu działać przez sekwencji uaktualniania opisany powyżej. 

## <a name="elastic-database-client-version-history"></a>Historia wersji klienta elastycznej bazy danych
Historia wersji, aby uzyskać [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

