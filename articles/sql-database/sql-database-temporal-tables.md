---
title: Wprowadzenie do tabel czasowych
description: Dowiedz się, jak rozpocząć korzystanie z tabel czasowych w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 98fd2658f3fbcb0e7e29114d29f8dc6ed39eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820719"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Wprowadzenie do tabel czasowych w bazie danych SQL platformy Azure

Tabele czasowe to nowa funkcja programowalności usługi Azure SQL Database, która umożliwia śledzenie i analizowanie pełnej historii zmian w danych bez konieczności kodowania niestandardowego. Tabele czasowe przechowują dane ściśle związane z kontekstem czasu, dzięki czemu przechowywane fakty mogą być interpretowane jako prawidłowe tylko w określonym okresie. Ta właściwość tabel czasowych umożliwia efektywną analizę opartą na czasie i uzyskiwanie szczegółowych informacji z ewolucji danych.

## <a name="temporal-scenario"></a>Scenariusz czasowy

W tym artykule przedstawiono kroki, aby wykorzystać tabele czasowe w scenariuszu aplikacji. Załóżmy, że chcesz śledzić aktywność użytkowników w nowej witrynie sieci Web, która jest opracowywana od podstaw lub w istniejącej witrynie sieci Web, którą chcesz rozszerzyć za pomocą analizy aktywności użytkowników. W tym uproszczonym przykładzie przyjęto założenie, że liczba odwiedzonych stron sieci web w okresie czasu jest wskaźnikiem, który musi zostać przechwycony i monitorowany w bazie danych witryny sieci Web, która jest hostowana w bazie danych SQL Azure. Celem historycznej analizy aktywności użytkowników jest uzyskanie danych wejściowych w celu przeprojektowania strony internetowej i zapewnienia lepszego doświadczenia dla odwiedzających.

Model bazy danych dla tego scenariusza jest bardzo prosty — metryka aktywności użytkownika jest reprezentowana za pomocą jednego pola całkowitej **PageVisited**i jest przechwytywany wraz z podstawowymi informacjami w profilu użytkownika. Ponadto w przypadku analizy opartej na czasie należy zachować serię wierszy dla każdego użytkownika, gdzie każdy wiersz reprezentuje liczbę stron odwiedzonych przez określonego użytkownika w określonym okresie czasu.

![Schemat](./media/sql-database-temporal-tables/AzureTemporal1.png)

Na szczęście nie trzeba wkładać żadnych wysiłków w aplikacji, aby zachować te informacje o aktywności. Dzięki tabelom czasowym proces ten jest zautomatyzowany , co zapewnia pełną elastyczność podczas projektowania stron internetowych i więcej czasu na skupienie się na samej analizie danych. Jedyną rzeczą, którą musisz zrobić, to upewnić się, że tabela **WebSiteInfo** jest skonfigurowany jako [system czasowy-versioned](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Dokładne kroki, aby korzystać z tabel czasowych w tym scenariuszu są opisane poniżej.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Konfigurowanie tabel jako czasowych
W zależności od tego, czy rozpoczynasz nowe programowaniu, czy uaktualniasz istniejącą aplikację, utworzysz tabele czasowe lub zmodyfikujesz istniejące, dodając atrybuty czasowe. Ogólnie rzecz biorąc scenariusz może być kombinacją tych dwóch opcji. Wykonaj te działania przy użyciu [programu SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), sql server data [tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) lub innego narzędzia do programowania Transact-SQL.

> [!IMPORTANT]
> Zalecane jest używanie najnowszej wersji programu Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Tworzenie nowej tabeli
Użyj elementu menu kontekstowego "Nowa tabela wersjona systemowa" w Eksploratorze obiektów SSMS, aby otworzyć edytor zapytań za pomocą skryptu szablonu tabeli czasowej, a następnie użyj opcji "Określ wartości dla parametrów szablonu" (Ctrl+Shift+M), aby wypełnić szablon:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

W SSDT wybierz szablon "Tabela czasowa (wersja systemowa)" podczas dodawania nowych elementów do projektu bazy danych. Spowoduje to otwarcie projektanta tabel i umożliwienie łatwego określenia układu tabeli:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Można również utworzyć tabelę czasową, określając instrukcje Transact-SQL bezpośrednio, jak pokazano w poniższym przykładzie. Należy zauważyć, że obowiązkowe elementy każdej tabeli czasowej są definicja PERIOD i SYSTEM_VERSIONING klauzuli z odwołaniem do innej tabeli użytkownika, która będzie przechowywać historyczne wersje wierszy:

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

Podczas tworzenia tabeli czasowej o wersji systemowej automatycznie tworzona jest towarzysząca mu tabela historii z konfiguracją domyślną. Domyślna tabela historii zawiera indeks klastrowanego drzewa B w kolumnach okresu (koniec, początek) z włączoną kompresją strony. Ta konfiguracja jest optymalna dla większości scenariuszy, w których używane są tabele czasowe, szczególnie do [inspekcji danych.](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0) 

W tym konkretnym przypadku staramy się przeprowadzać analizę trendów opartą na czasie w dłuższej historii danych i z większymi zestawami danych, więc wybór magazynu dla tabeli historii jest indeksem klastrowanego magazynu kolumn. Klastrowany magazyn kolumn zapewnia bardzo dobrą kompresję i wydajność dla zapytań analitycznych. Tabele czasowe zapewniają elastyczność konfigurowania indeksów w tabelach bieżących i czasowych całkowicie niezależnie. 

> [!NOTE]
> Indeksy magazynu kolumn są dostępne w warstwie Premium i w warstwie Standardowa, S3 i wyższej.
>

Poniższy skrypt pokazuje, jak domyślny indeks w tabeli historii można zmienić na klastrowany magazyn kolumn:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Tabele czasowe są reprezentowane w Eksploratorze obiektów z określoną ikoną ułatwiającą identyfikację, podczas gdy jego tabela historii jest wyświetlana jako węzeł podrzędny.

![Altertable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Zmienianie istniejącej tabeli na czasowe
Przyjmijmy alternatywny scenariusz, w którym websiteuserInfo tabela już istnieje, ale nie został zaprojektowany, aby zachować historię zmian. W takim przypadku można po prostu rozszerzyć istniejącą tabelę, aby stała się czasowa, jak pokazano w poniższym przykładzie:

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

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: Regularnie uruchamiaj obciążenie pracą
Główną zaletą tabel czasowych jest to, że nie trzeba zmieniać ani dostosowywać witryny w żaden sposób, aby wykonać śledzenie zmian. Po utworzeniu tabele czasowe w sposób przezroczysty utrzymują poprzednie wersje wierszy za każdym razem, gdy wykonujesz modyfikacje danych. 

Aby wykorzystać automatyczne śledzenie zmian w tym konkretnym scenariuszu, po prostu zaktualizujmy kolumnę **PagesVisited** za każdym razem, gdy użytkownik kończy sesję w witrynie sieci Web:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Należy zauważyć, że kwerenda aktualizacji nie musi znać dokładnego czasu, kiedy wystąpiła rzeczywista operacja ani jak dane historyczne zostaną zachowane do przyszłej analizy. Oba aspekty są automatycznie obsługiwane przez usługę Azure SQL Database. Na poniższym diagramie przedstawiono sposób generowania danych historii przy każdej aktualizacji.

![DoczesnaArchitektura](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: Przeprowadzanie analizy danych historycznych
Teraz, gdy czasowe przechowywanie wersji systemu jest włączone, analiza danych historycznych jest tylko jedno zapytanie od Ciebie. W tym artykule przedstawimy kilka przykładów, które dotyczą typowych scenariuszy analizy - aby dowiedzieć się wszystkich szczegółów, zbadać różne opcje wprowadzone za pomocą klauzuli [FOR SYSTEM_TIME.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)

Aby zobaczyć 10 najlepszych użytkowników uporządkowanych według liczby odwiedzonych stron internetowych od godziny temu, uruchom tę kwerendę:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Możesz łatwo zmodyfikować to zapytanie, aby przeanalizować wizyty na stronie dzień temu, miesiąc temu lub w dowolnym momencie w przeszłości, który chcesz.

Aby przeprowadzić podstawową analizę statystyczną za poprzedni dzień, użyj następującego przykładu:

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

Aby wyszukać działania określonego użytkownika, w określonym czasie należy użyć klauzuli ZAWARTEJ W:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Wizualizacja graficzna jest szczególnie wygodna w przypadku zapytań czasowych, ponieważ bardzo łatwo można pokazać trendy i wzorce użytkowania w intuicyjny sposób:

![Wykres czasowy](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Ewoluujący schemat tabeli
Zazwyczaj należy zmienić schemat tabeli czasowej podczas tworzenia aplikacji. W tym celu wystarczy uruchomić regularne instrukcje ALTER TABLE i usługi Azure SQL Database odpowiednio propagują zmiany w tabeli historii. Poniższy skrypt pokazuje, jak można dodać dodatkowy atrybut do śledzenia:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Podobnie można zmienić definicję kolumny, gdy obciążenie jest aktywne:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Na koniec możesz usunąć kolumnę, która nie jest już potrzebna.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Alternatywnie należy użyć najnowszego [narzędzia SSDT,](https://msdn.microsoft.com/library/mt204009.aspx) aby zmienić schemat tabeli czasowej podczas połączenia z bazą danych (tryb online) lub jako część projektu bazy danych (tryb offline).

## <a name="controlling-retention-of-historical-data"></a>Kontrolowanie przechowywania danych historycznych
W przypadku tabel czasowych o wersji systemowej tabela historii może zwiększyć rozmiar bazy danych bardziej niż zwykłe tabele. Duża i stale rosnąca tabela historii może stać się problemem zarówno ze względu na koszty czystego przechowywania, jak i nakładanie podatku od wydajności na zapytania czasowe. W związku z tym opracowanie zasad przechowywania danych do zarządzania danymi w tabeli historii jest ważnym aspektem planowania i zarządzania cyklem życia każdej tabeli czasowej. Za pomocą usługi Azure SQL Database masz następujące podejścia do zarządzania danymi historycznymi w tabeli czasowej:

* [Partycjonowanie tabel](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Niestandardowy skrypt oczyszczania](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tabel czasowych, zobacz wyewidencjonowanie [tabel czasowych](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).
- Odwiedź Kanał 9, aby usłyszeć [prawdziwy sukces wdrożenia czasowego klienta](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) i obejrzeć na żywo pokaz [czasowy.](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)

