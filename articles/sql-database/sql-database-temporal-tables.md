---
title: Wprowadzenie do tabel danych czasowych w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć pracę z usługi Azure SQL Database przy użyciu tabel danych czasowych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: fbb2458e73330a09124c00cebe3eb7bcaba5408d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951501"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Wprowadzenie do tabel danych czasowych w bazie danych Azure SQL

Tabele danych czasowych są nową funkcją programowalności usługi Azure SQL Database, która pozwala śledzić i analizować pełną historię zmian w danych, bez konieczności pisania kodu niestandardowego. Tabele danych czasowych Zachowaj dane ściśle powiązane z kontekstu czasu, tak aby przechowywanych fakty, które mogą być interpretowane w jako prawidłowa tylko w obrębie określonego okresu. Ta właściwość tabele danych czasowych umożliwia wydajne analizy na podstawie czasu i uzyskiwania szczegółowych informacji z ewolucji danych.

## <a name="temporal-scenario"></a>Scenariusz danych czasowych

W tym artykule przedstawiono kroki umożliwiające korzystanie z tabel danych czasowych w scenariuszu aplikacji. Załóżmy, że chcesz śledzić aktywność użytkowników na nową witrynę sieci Web, która jest obecnie sporządzana od podstaw lub na istniejącej witryny sieci Web, którą chcesz rozszerzyć z analizą aktywności użytkownika. W tym przykładzie uproszczona przyjęto założenie, że liczba odwiedzanych stron sieci web w przedziale czasu jest wskaźnik, który musi być przechwytywane i monitorowane w bazie danych witryny sieci Web, która jest hostowana w usłudze Azure SQL Database. Celem analizy historycznej aktywności użytkownika jest pobrać dane wejściowe do przeprojektowania witryny sieci Web i zapewnić lepsze środowisko dla użytkowników zewnętrznych.

Model bazy danych dla tego scenariusza jest bardzo proste — pomiar aktywności użytkownika jest reprezentowana z polem pojedyncze liczby całkowite **PageVisited**i są przechwytywane oraz podstawowe informacje na temat profilu użytkownika. Ponadto na potrzeby analiz na podstawie czasu Zachowaj serii wierszy dla każdego użytkownika, gdzie każdy wiersz reprezentuje liczbę stron, dany użytkownik odwiedzi w określonym okresie czasu.

![Schemat](./media/sql-database-temporal-tables/AzureTemporal1.png)

Na szczęście nie trzeba umieścić wszelkie nakład pracy w swojej aplikacji, aby zachować informacje o działaniach. Tabele danych czasowych ten proces jest automatycznego — zapewnia pełną elastyczność podczas projektowania witryny sieci Web i więcej czasu na skoncentrowanie się na analizy danych, sam. Jedyną czynnością, należy wykonać, jest zapewnienie, że **WebSiteInfo** tabela została skonfigurowana jako [danych czasowych wersjonowana przez system](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Konkretne kroki korzystanie z tabel danych czasowych w tym scenariuszu opisano poniżej.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Skonfigurować tabele jako danych czasowych
W zależności od tego, czy uruchamianie nowych wdrożeń lub uaktualnienie istniejącej aplikacji będzie Tworzenie tabel danych czasowych lub zmodyfikuj istniejące przez dodanie atrybutów danych czasowych. Ogólnie rzecz biorąc przypadku Twojego scenariusza może być kombinacja tych dwóch opcji. Wykonaj te akcję przy użyciu [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) lub innego narzędzia do programowania języka Transact-SQL.

> [!IMPORTANT]
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Tworzenie nowej tabeli
Umożliwia w menu kontekstowym "Nową tabelę systemową" w Eksploratorze obiektów programu SSMS Otwórz Edytor zapytań przy użyciu skryptu szablonu tabeli danych czasowych, a następnie wypełnij szablonu za pomocą "Określ wartości dla parametrów szablonu" (Ctrl + Shift + M):

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

W programie SSDT wybierz szablon "(systemową) tabeli danych czasowych", podczas dodawania nowych elementów do projektu bazy danych. Który będzie Otwórz projektanta tabel i umożliwiają łatwe określenie układu tabeli:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Można również utworzyć tabeli danych czasowych bezpośrednio, określając instrukcji języka Transact-SQL, jak pokazano w poniższym przykładzie. Należy zauważyć, że obowiązkowe elementy tabeli danych czasowych z każdej klauzuli SYSTEM_VERSIONING z odwołaniem do innej tabeli użytkownika, który będzie przechowywać wersje wierszy historycznych i definicji okresu:

```
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
```

Podczas tworzenia tabeli danych czasowych z systemową obsługą towarzyszący tabeli historii w przypadku domyślnej konfiguracji jest tworzony automatycznie. Tabela historii domyślny zawiera klastrowany indeks B-drzewa, w kolumny okresu (koniec, start) z włączoną kompresją strony. Ta konfiguracja jest optymalna dla większości scenariuszy, w których są używane tabele danych czasowych, szczególnie w przypadku [inspekcji danych](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

W tym przypadku możemy mają na celu wykonywania analizy trendu na podstawie czasu dłuższego historii danych i większe zestawy danych, więc opcji magazynu dla tabeli historii jest klastrowany indeks magazynu kolumn. Klastrowanego magazynu kolumn zawiera bardzo dobre kompresji i wydajności zapytań analitycznych. Tabele danych czasowych zapewniają elastyczność konfigurowania indeksów w tabelach bieżące i danych czasowych całkowicie niezależne. 

> [!NOTE]
> Indeksy magazynu kolumn są dostępne w warstwie Premium i w warstwie standardowa S3 oraz powyżej.
>

Poniższy skrypt pokazuje, jak można zmienić domyślny indeks w tabeli historii klastrowanego magazynu kolumn:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Tabele danych czasowych są reprezentowane w Eksploratorze obiektów przy użyciu określonej ikony w celu łatwiejszej identyfikacji podczas swojej tabeli historii jest wyświetlany jako węzeł podrzędny.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Instrukcja ALTER istniejącej tabeli danych czasowych
Teraz obejmować alternatywny scenariusz, w którym tabeli WebsiteUserInfo już istnieje, ale nie jest przeznaczone do przechowywania historii zmian. W takim przypadku możesz po prostu rozszerzyć istniejącej tabeli, aby stać się danych czasowych, jak pokazano w poniższym przykładzie:

```
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
```

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: Regularnego uruchamiania obciążenia
Główną zaletą tabele danych czasowych jest, nie trzeba zmienić lub dostosować witryny sieci Web w jakikolwiek sposób przeprowadzić śledzenie zmian. Po utworzeniu tabele danych czasowych przezroczyste zachować poprzednie wersje wiersza za każdym razem, gdy wykonujesz modyfikacji danych w. 

Aby można było korzystać z automatycznego śledzenia zmian dla tego scenariusza, po prostu zaktualizujmy kolumny **PagesVisited** za każdym razem, gdy użytkownik kończy sesję w witrynie internetowej:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Należy zauważyć, zapytanie aktualizujące nie trzeba znać dokładny czas podczas bieżącej operacji wystąpił, ani w jaki sposób dane historyczne zostaną zachowane dla przyszłej analizy. Aspekty obu automatycznie są obsługiwane przez usługę Azure SQL Database. Na poniższym diagramie przedstawiono, jak dane historii jest generowany po każdej aktualizacji.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: Analizowanie danych historycznych
Teraz po włączeniu danych czasowych system-versioning analiza danych historycznych jest tylko jedno zapytanie od użytkownika. W tym artykule, firma Microsoft zapewnia kilka przykładów, które dotyczą typowych scenariuszy analizy — Poznaj wszystkie szczegóły, Poznaj różne opcje wprowadzone w programie [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) klauzuli.

Aby wyświetlić najważniejsze 10 użytkowników, uporządkowane według liczby odwiedzanych witryn sieci web, począwszy od godzinę temu, uruchom to zapytanie:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Można łatwo modyfikować to zapytanie do analizowania odwiedzin witryny, począwszy od wczoraj, miesiąc temu lub w dowolnym momencie w przeszłości chcesz.

Aby wykonać podstawowe analizy statystycznej z poprzedniego dnia, skorzystaj z następującego przykładu:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Aby wyszukać działania określonego użytkownika w danym okresie czasu, użyj klauzuli zawarte w:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Graficzna wizualizacja jest szczególnie wygodne w przypadku czasowych zapytań, ponieważ można pokazać trendów i wzorców użytkowania w intuicyjny sposób bardzo łatwe:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Stale rosnących potrzeb schematu tabeli
Zazwyczaj należy zmienić schemat tabeli danych czasowych, gdy robią opracowywania aplikacji. W tym wystarczy uruchomić regularne instrukcji ALTER TABLE instrukcji i Azure SQL Database zostaną odpowiednio propagujące zmiany do tabeli historii. Poniższy skrypt pokazuje, jak dodać dodatkowe atrybutu dla śledzenia:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Podobnie można zmienić definicji kolumny, gdy obciążenie jest aktywna:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Na koniec można usunąć kolumny, które nie są już potrzebne.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Alternatywnie można użyć najnowszej [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) zmiany schematu tabeli danych czasowych, gdy są połączone z bazą danych (tryb online) lub jako część projektu bazy danych (tryb offline).

## <a name="controlling-retention-of-historical-data"></a>Kontrolowanie przechowywaniem danych historycznych
Za pomocą tabelach danych czasowych z systemową obsługą tabeli historii może zwiększyć rozmiar bazy danych, więcej niż regularne tabel. Tabelę historii duże i nieustannie rosnących zasobów może stać się problemem zarówno ze względu na koszty magazynowania czyste, a także nakładające wydajności podatek od danych czasowych zapytań. W związku z tym tworzenie zasad przechowywania danych do zarządzania danymi w tabeli historii jest istotnym elementem planowania i zarządzania cyklem życia w każdej tabeli danych czasowych. Usługa Azure SQL Database masz następujących podejść do zarządzania danych historycznych w tabeli danych czasowych:

* [Partycjonowanie tabel](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Skrypt czyszczący niestandardowe](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje w tabelach danych czasowych, zapoznaj się z [dokumentacji MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Można znaleźć w witrynie Channel 9, aby usłyszeć [Historia sukcesu wdrożenia danych czasowych rzeczywiste](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) i obejrzyj [danych czasowych pokaz na żywo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

