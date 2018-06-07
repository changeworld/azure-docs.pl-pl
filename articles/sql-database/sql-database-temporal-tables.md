---
title: Wprowadzenie do tabel danych czasowych w bazie danych Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć używanie tabel danych czasowych w bazie danych SQL Azure.
services: sql-database
author: bonova
ms.date: 03/21/2018
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.author: bonova
ms.openlocfilehash: 140d2c9f6c334cec7d2761d05d7b20eb7106b9fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649044"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Wprowadzenie do tabel danych czasowych w bazie danych Azure SQL
Tabele danych czasowych są nową funkcją programowalności bazy danych SQL Azure, umożliwiające śledzić i analizować pełną historię zmian danych, bez konieczności pisania kodu niestandardowego. Tabele danych czasowych Zachowaj dane ściśle związane z kontekstu czasu, aby przechowywane dane mogą być interpretowane jako prawidłowy tylko w określonym okresie. Ta właściwość tabel danych czasowych umożliwia wydajne analizy na podstawie czasu i pobierania wgląd w dane dotyczące zmiany danych.

## <a name="temporal-scenario"></a>Scenariusz danych czasowych
W tym artykule przedstawiono kroki, aby korzystać z tabel danych czasowych w scenariuszu aplikacji. Załóżmy, że chcesz śledzić aktywność użytkownika nowej witryny sieci Web, który jest obecnie opracowywane od początku lub w istniejącej witrynie internetowej, który ma zostać rozszerzony o analizowanie aktywności użytkowników. W tym przykładzie uproszczony przyjęto założenie, że liczba odwiedzane strony sieci web w okresie czasu jest wskaźnik musi zostać przechwycone i monitorowane w bazie danych witryny sieci Web, który znajduje się w bazie danych SQL Azure. Celem historycznej analizy aktywności użytkownika jest uzyskanie danych wejściowych do przeprojektowania witryny sieci Web i zapewnia lepsze środowisko dla gości.

Model bazy danych dla tego scenariusza jest bardzo prosty — pomiar aktywności użytkownika odpowiada za pomocą pola jeden **PageVisited**i są przechwytywane oraz podstawowe informacje o profilu użytkownika. Ponadto do analizy na podstawie czasu można zachowa serii wierszy dla każdego użytkownika, której każdy wiersz reprezentuje liczbę stron dany użytkownik odwiedzi w określonym okresie czasu.

![Schemat](./media/sql-database-temporal-tables/AzureTemporal1.png)

Na szczęście nie trzeba umieścić wszelkich starań w aplikacji, aby zachować informacje o działaniach. W tabelach danych czasowych ten proces jest automatyczne — umożliwiając pełną elastyczność podczas projektowania witryny sieci Web i więcej czasu skupić się na analizy danych, do samej siebie. Jedyną operacją, musisz wykonać jest zapewnienie, że **WebSiteInfo** tabeli jest skonfigurowany jako [danych czasowych systemową kontrolą wersji](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Dokładne kroki mogą korzystać z tabel danych czasowych w tym scenariuszu opisano poniżej.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Konfigurowanie tabel jako danych czasowych
W zależności od tego, czy są uruchamianie nowych aplikacji lub uaktualnienia istniejącej aplikacji zostanie Tworzenie tabel danych czasowych lub modyfikować istniejące przez dodanie atrybutów danych czasowych. Ogólnie rzecz biorąc przypadku danego scenariusza można kombinacja tych dwóch opcji. Wykonaj te za pomocą akcji [programu SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) lub inne narzędzie do projektowania języka Transact-SQL.

> [!IMPORTANT]
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Utwórz nową tabelę
Użyj elementu menu kontekstowego "Nową tabelę systemową kontrolą wersji" w Eksploratorze obiektów w programie SSMS, Otwórz Edytor zapytań przy użyciu skryptu szablonu tabeli danych czasowych, a następnie użyć "Określ wartości dla parametrów szablonu" (Ctrl + Shift + M) do wypełniania szablonu:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Programu SSDT wybierz szablon "Tabela danych czasowych (systemową)", podczas dodawania nowych elementów do projektu bazy danych. Które Otwórz projektanta tabel i umożliwiają łatwe określenie układu tabeli:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Można również tworzenie tabeli danych czasowych, określając instrukcji języka Transact-SQL bezpośrednio, jak pokazano w poniższym przykładzie. Należy zauważyć, że elementy obowiązkowe każda tabela danych czasowych definicji okresu i klauzuli SYSTEM_VERSIONING z odwołaniem do innej tabeli użytkownika, który będzie przechowywać wersje wierszy historycznych:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Towarzyszący tabeli historii z domyślnej konfiguracji jest tworzony automatycznie podczas tworzenia tabeli danych czasowych z systemową kontrolą wersji. Tabela historii domyślny zawiera B-drzewa indeksu klastrowanego na kolumny okresu (koniec, start) z włączoną kompresją strony. Ta konfiguracja jest optymalna dla większości scenariuszy, w których są używane tabele danych czasowych, szczególnie w przypadku [inspekcja danych](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

W tym przypadku firma Microsoft Staraj się wykonywać analizy trendów na podstawie czasu za pośrednictwem dłużej historii danych i większych zestawów danych, dlatego wybór magazynu dla tabeli historii jest klastrowany indeks magazynu kolumn. Klastrowanego magazynu kolumn zawiera bardzo dobre kompresji i wydajności dla zapytań analitycznych. Tabele danych czasowych umożliwiają skonfigurowanie indeksów w tabelach bieżących i danych czasowych całkowicie niezależnie. 

> [!NOTE]
> Indeksy magazynu kolumn są dostępne w warstwie Premium i w warstwie standardowa S3 i powyżej.
>

Poniższy skrypt pokazuje, jak można zmienić domyślny indeks w tabeli historii do klastrowanego magazynu kolumn:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tabele danych czasowych są reprezentowane w Eksploratorze obiektów z określonym ikonę ułatwiają identyfikację podczas jego tabeli historii jest wyświetlany jako węzeł podrzędny.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Instrukcja ALTER istniejącej tabeli danych czasowych
Teraz obejmuje alternatywnych scenariusz, w którym tabeli WebsiteUserInfo już istnieje, ale nie została zaprojektowana w celu przechowywania historii zmian. W takim przypadku można po prostu rozszerzyć istniejącą tabelę jako tymczasowa, jak pokazano w poniższym przykładzie:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: Regularnego uruchamiania obciążenia
Główną zaletą tabel danych czasowych jest, nie trzeba zmienić lub dostosować witryny sieci Web w dowolny sposób, aby przeprowadzić śledzenie zmian. Po utworzeniu tabel danych czasowych niewidocznie utrwalić poprzednie wersje wiersza za każdym razem, gdy wykonywania modyfikacji danych w. 

Aby korzystać z automatycznego śledzenia zmian dla tego scenariusza, umożliwia tylko zaktualizować kolumny **PagesVisited** za każdym razem, gdy użytkownik kończy się jego sesji w witrynie sieci Web:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Należy zauważyć zapytanie update nie trzeba znać dokładny czas podczas bieżącej operacji wystąpił ani w jaki sposób dane historyczne zostaną zachowane dla przyszłych analizy. Zarówno aspekty automatycznie są obsługiwane przez bazę danych SQL Azure. Na poniższym diagramie przedstawiono, jak dane historii jest generowany na każdej aktualizacji.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: Wykonanie analizy danych historycznych
Teraz po włączeniu danych czasowych system-versioning analizy danych historycznych jest tylko jedno zapytanie od użytkownika. W tym artykule, firma Microsoft udostępni kilka przykładów, które rozwiązują typowe scenariusze analizy — Aby poznać wszystkie szczegóły, zapoznaj się z różnymi opcjami wprowadzone w systemie [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) klauzuli.

Aby wyświetlić najaktywniejszych użytkowników 10 uporządkowanych według liczby odwiedzane strony sieci web na godzinę temu, uruchom to zapytanie:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

To zapytanie do analizowania wizyt lokacji dzień temu miesiąc temu można łatwo zmodyfikować lub w dowolnym momencie w ciągu ostatnich ma.

Aby wykonać podstawowe analiz statystycznych z poprzedniego dnia, skorzystaj z następującego przykładu:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Aby wyszukać działania określonego użytkownika, w określonym przedziale czasu, użyj klauzuli zawarte w:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Graficzne wizualizacji jest szczególnie wygodne czasowych zapytań, jak można wyświetlić trendów i wzorców użytkowania w intuicyjny sposób bardzo łatwo:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Schemat tabeli zmieniające się
Zazwyczaj należy zmienić schemat tabeli danych czasowych podczas operacji tworzenia aplikacji. W tym wystarczy uruchomić regularne instrukcji ALTER TABLE instrukcje i bazy danych SQL Azure odpowiednio rozpropaguje zmiany do tabeli historii. Poniższy skrypt pokazuje, jak dodać dodatkowe atrybut śledzenia:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Podobnie można zmienić definicji kolumny, gdy obciążenie jest aktywna:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Na koniec można usunąć kolumny, które nie są już potrzebne.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

Można również użyć najnowszej [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) zmiany schematu tabeli danych czasowych podczas połączenia z bazą danych (w trybie online) lub w ramach projektu bazy danych (w trybie offline).

## <a name="controlling-retention-of-historical-data"></a>Kontrolowanie przechowywania danych historycznych
Tabela historii może zwiększyć tabelach danych czasowych z systemową kontrolą wersji baz danych o rozmiarze ponad zwykłych tabelach. Tabel historii duży i coraz dłuższej może stać się problemem zarówno z powodu kosztów czystego magazynu, a także nakładające wydajności podatek danych czasowych zapytań. W związku z tym tworzenie zasad przechowywania danych zarządzania danymi w tabeli historii jest istotnym elementem planowania i zarządzanie cyklem życia każda tabela danych czasowych. Z bazy danych SQL Azure masz następujące podejścia do zarządzania danych historycznych w tabeli danych czasowych:

* [Partycjonowanie tabel](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Skrypt czyszczący niestandardowych](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje w tabelach danych czasowych, zapoznaj się z [dokumentacji MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Odwiedź Channel 9, aby usłyszeć [klienta rzeczywistych danych czasowych implementacji Powodzenie wątku](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) i obejrzyj [live pokaz danych czasowych](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

