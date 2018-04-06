---
title: Zarządzanie danych historycznych w tabelach danych czasowych z zasadami przechowywania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zasad przechowywania danych tymczasowych do przechowywania danych historycznych pod kontrolą.
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 10/12/2016
ms.author: bonova
ms.openlocfilehash: 1ebfab93c94c27de8e765ac3f8278372c8f0690f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Zarządzanie danych historycznych w tabelach danych czasowych z zasady przechowywania
Tabele danych czasowych może zwiększyć baz danych o rozmiarze ponad zwykłych tabelach, zwłaszcza jeśli przechowywanie danych historycznych przez dłuższy okres. W związku z tym zasady przechowywania danych historycznych jest istotnym elementem planowania i zarządzanie cyklem życia każda tabela danych czasowych. Tabele danych czasowych w bazie danych SQL Azure pochodzą z mechanizmem przechowywania łatwy w użyciu, który ułatwia wykonywanie tego zadania.

Przechowywania historii danych czasowych może być skonfigurowana na poziomie pojedynczej tabeli, która umożliwia użytkownikom tworzenie elastycznych przedawnienia zasady. Stosowanie przechowywania danych czasowych jest prosty: wymaga tylko jeden parametr należy ustawić podczas zmiany schematu lub tworzenia tabeli.

Po zdefiniowaniu zasad przechowywania bazy danych SQL Azure rozpoczyna regularnie sprawdzanie, czy istnieją historyczne wierszy, które kwalifikują się do czyszczenia danych. Identyfikacja pasujących wierszy i ich usunięcia z tabeli historii występować w sposób niewidoczny dla użytkownika, w zadania w tle, który jest zaplanowane i uruchamiane przez system. Warunek wieku wiersze tabeli historii zaznaczono oparte na kolumnie reprezentujący koniec okresu SYSTEM_TIME. Jeśli okres przechowywania, na przykład ustawiono sześciu miesięcy, kwalifikuje się do oczyszczania wiersze tabeli spełniają następujący warunek:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

W powyższym przykładzie mamy przyjąć, że **ValidTo** kolumn odnosi się do końca okresu SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Jak skonfigurować zasady przechowywania?
Przed skonfigurowaniem zasad przechowywania dla tabeli danych czasowych należy sprawdzić w pierwszej kolejności czy włączone jest przechowywanie historycznych danych czasowych *na poziomie bazy danych*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Baza danych flagi **is_temporal_history_retention_enabled** jest ustawiona na wartość Domyślnie, ale użytkownicy można go zmienić za pomocą instrukcji ALTER DATABASE. Automatycznie ma wartość OFF po [punktu w czasie przywracania](sql-database-recovery-using-backups.md) operacji. Aby włączyć oczyszczanie przechowywania historii danych czasowych dla bazy danych, należy wykonać następującą instrukcję:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Możesz skonfigurować czas przechowywania w przypadku tabel czasowych, nawet jeśli **is_temporal_history_retention_enabled** jest wyłączona, ale automatycznego oczyszczania przestarzałych wierszy jest nie są uruchamiane w takiej sytuacji.
> 
> 

Zasady przechowywania jest skonfigurowane podczas tworzenia tabeli, określając wartość parametru HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Baza danych SQL Azure pozwala określić przy użyciu różne jednostki okresu przechowywania: dni, tygodnie, MIESIĄCE i lata. W przypadku pominięcia HISTORY_RETENTION_PERIOD przyjęto NIESKOŃCZONE przechowywania. Umożliwia także NIESKOŃCZONE — słowo kluczowe jawnie.

W niektórych scenariuszach warto skonfigurować przechowywania po utworzeniu tabeli lub zmienić wcześniej skonfigurowane wartości. W takim przypadku należy użyć instrukcji ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> Ustawienie opcji SYSTEM_VERSIONING na OFF *nie zachowa* wartość okresu przechowywania. Ustawienie opcji SYSTEM_VERSIONING na ON, bez HISTORY_RETENTION_PERIOD jawnie określone wyniki w NIEOGRANICZONY okres przechowywania.
> 
> 

Aby wyświetlić bieżący stan zasady przechowywania, użyj następującego zapytania, której jest przyłączany Flaga włączenia przechowywania danych czasowych na poziomie bazy danych z okresów przechowywania dla poszczególnych tabel:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


## <a name="how-sql-database-deletes-aged-rows"></a>Jak baza danych SQL usuwa przestarzałe wierszy?
Układ indeksu tabeli historii zależy od procesu czyszczenia. Należy zauważyć, że *tylko do tabel historii z indeksem klastrowanym, (B-drzewa lub magazynu kolumn) może mieć skonfigurowanych zasad przechowywania skończoną*. Zadanie w tle jest utworzone w celu wykonania oczyszczania przestarzałych danych dla wszystkich tabel danych czasowych z okresu przechowywania ograniczone.
Logika oczyszczania dla klastrowanego indeksu magazynu wierszy (B-drzewa) usuwa przestarzałe wiersza w mniejsze fragmenty (do 10 kilobajtów) minimalizując nacisk na dziennika bazy danych i podsystem We/Wy. Mimo że logiki oczyszczania wykorzystuje wymagane indeksu B-drzewa, kolejność usuwania starsze niż okres przechowywania nie może zagwarantować mocno wierszy. W związku z tym *nie przyjmują wszelkie zależności w kolejności Oczyszczanie aplikacji*.

Zadanie oczyszczania dla klastrowanego magazynu kolumn spowoduje usunięcie całego [wiersz grup](https://msdn.microsoft.com/library/gg492088.aspx) jednocześnie (zwykle zawierają 1 milion wierszy każdego), który jest bardzo wydajny, szczególnie w przypadku, gdy dane historyczne zostanie wygenerowany w szybkim tempie.

![Przechowywania klastrowanego magazynu kolumn](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Kompresji danych doskonała i wydajne przechowywania oczyszczania sprawia, że klastrowany indeks magazynu kolumn doskonałym rozwiązaniem dla scenariuszy jeśli obciążenie generuje szybko dużej ilości danych historycznych. Ten wzorzec jest typowa dla intensywnego [obciążenia przetwarzania transakcyjnego, które tabele danych czasowych](https://msdn.microsoft.com/library/mt631669.aspx) śledzenie zmian i inspekcji, analiza trendu lub wprowadzanie danych IoT.

## <a name="index-considerations"></a>Zagadnienia dotyczące indeksu
Zadanie oczyszczania tabel klastrowanego indeksu magazynu wierszy wymaga indeksu do uruchomienia z kolumny odpowiadającego koniec okresu SYSTEM_TIME. Jeśli takie indeks nie istnieje, nie można skonfigurować okres przechowywania ograniczone:

*Msg 13765, poziom 16, stan 1 <br> </br> ustawienie okresu przechowywania ograniczone nie można w tabeli danych czasowych z systemową kontrolą wersji "temporalstagetestdb.dbo.WebsiteUserInfo", ponieważ tabela historii " temporalstagetestdb.dbo.WebsiteUserInfoHistory "nie zawiera wymaganego indeksu klastrowanego. Należy rozważyć utworzenie klastrowanego magazynu kolumn lub indeksu B-drzewa, począwszy od kolumny, która odpowiada koniec SYSTEM_TIME period w tabeli historii.*

Należy zauważyć, że tabeli historii domyślny utworzony przez bazę danych SQL Azure już ma indeks, który jest zgodny z zasad przechowywania w klastrze. Jeśli próbujesz usunąć tego indeksu dla tabeli z okresu przechowywania ograniczone, operacja kończy się niepowodzeniem z powodu następującego błędu:

*Msg 13766, poziom 16, stan 1 <br> </br> nie można porzucić indeksu klastrowanego "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory", ponieważ jest on używany do automatycznego usuwania przestarzałych danych. Rozważ ustawienie HISTORY_RETENTION_PERIOD infinite na odpowiedniej systemową kontrolą wersji tabeli danych czasowych, jeśli musisz porzucić ten indeks.*

Oczyszczanie klastrowany indeks magazynu kolumn działa optymalnie, jeśli umieszczanych historycznych wierszy w kolejności rosnącej (uporządkowane do końca okresu kolumny), co jest zawsze sytuacją, gdy w tabeli historii jest wypełniana wyłącznie przy użyciu mechanizmu SYSTEM_VERSIONIOING. Wiersze w tabeli historii nie są uporządkowane według koniec kolumnie okresu (która może się po migracji istniejących danych historycznych), należy ponownie utworzyć klastrowanego indeksu magazynu kolumn na górze indeksu magazynu wierszy B-drzewa, który jest prawidłowo uporządkowanych, aby osiągnąć optymalną wydajność.

Należy unikać odbudowywania klastrowany indeks magazynu kolumn w tabeli historii od okresu przechowywania ograniczone, ponieważ mogą ulec zmianie, kolejność w grupy wierszy naturalnie narzucone przez operację wersjonowania systemu. Jeśli musisz odbudować indeksu klastrowanego magazynu kolumn w tabeli historii, to zrobić przez ponowne utworzenie u góry zgodne indeksu B-drzewa, zachowując kolejnością rowgroups niezbędne oczyszczania regularnych danych. Te same podejście należy podjąć w przypadku utworzenia tabeli danych czasowych z istniejącą tabelę historii, która ma klastrowany indeks kolumny bez kolejność gwarantowane danych:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Po skonfigurowaniu ograniczone przechowywanie tabeli historii z klastrowanego indeksu magazynu kolumn nie można utworzyć dodatkowe nieklastrowanych indeksów B-drzewa w tej tabeli:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Próba wykonania powyżej instrukcji nie powiodło się z powodu następującego błędu:

*Msg 13772, poziom 16, stan 1 <br> </br> nie można utworzyć indeksu nieklastrowanego dla tabeli historii danych czasowych "WebsiteUserInfoHistory" ponieważ ma ona okres przechowywania skończona i klastrowany indeks magazynu kolumn zdefiniowane.*

## <a name="querying-tables-with-retention-policy"></a>Tworzenie zapytań o tabele z zasady przechowywania
Wszystkie zapytania w tabeli danych czasowych automatycznie odfiltrowywania historycznych wierszy pasujących zasad przechowywania ograniczone, aby uniknąć nieprzewidywalne i niespójne wyniki, ponieważ przestarzałych wierszy może zostać usunięta przez zadanie oczyszczania *w dowolnym momencie w czasie i dowolnego kolejność*.

Na poniższej ilustracji przedstawiono planu zapytania dla prostego zapytania:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

Plan zapytania zawiera dodatkowy filtr stosowane do końca okresu kolumny (ValidTo) w operatorze skanowania indeksu klastrowanego w tabeli historii (wyróżniony). W tym przykładzie przyjęto założenie, w tabeli WebsiteUserInfo ustawiono tego okresu przechowywania jeden miesiąc.

![Filtr kwerendy przechowywania](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Jednak w tabeli historii są zapytania bezpośrednio, może wystąpić wierszy, które są starsze niż przechowywania określony okres, ale bez żadnych gwarancji dla powtarzalne wyniki. Na poniższej ilustracji przedstawiono planu wykonania zapytania dla zapytania w tabeli historii bez dodatkowych filtrów:

![Trwa badanie historii bez filtru przechowywania](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nie należy polegać logiki biznesowej na odczytywania tabeli historii poza okresem przechowywania, jak możesz uzyskać niespójne lub nieoczekiwane wyniki. Zalecane jest użycie czasowych zapytań z klauzulą FOR SYSTEM_TIME do analizowania danych w tabelach danych czasowych.

## <a name="point-in-time-restore-considerations"></a>Punktu w czasie przywracania uwagi
Podczas tworzenia nowej bazy danych przez [Przywracanie istniejącą bazę danych do określonego punktu w czasie](sql-database-recovery-using-backups.md), ma przechowywania danych czasowych wyłączona na poziomie bazy danych. (**is_temporal_history_retention_enabled** ustawiona flaga off). Ta funkcja pozwala sprawdzić wszystkie wiersze historycznych podczas przywracania, nie martwiąc się, że przestarzałych wiersze zostaną usunięte przed uzyskanie do nich kwerendy. Możesz użyć jej do *sprawdzić historycznych danych poza okresem przechowywania skonfigurowanych*.

Załóżmy, że tabela danych czasowych ma podanego okresu przechowywania jeden miesiąc. Jeśli baza danych została utworzona w warstwie Premium usługi, czy można utworzyć kopię bazy danych o stanie bazy danych się do 35 dni w przeszłości. Które skutecznie umożliwi analizowanie historycznych wierszy, które są do 65 dni przez bezpośrednie wysyłanie zapytań w tabeli historii.

Jeśli chcesz aktywować oczyszczania przechowywania danych tymczasowych, uruchom następującą instrukcję Transact-SQL po punkcie w czasie przywracania:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak używać tabel danych czasowych w aplikacji, zapoznaj się [wprowadzenie do tabel danych czasowych w bazie danych SQL Azure](sql-database-temporal-tables.md).

Odwiedź Channel 9, aby usłyszeć [klienta rzeczywistych danych czasowych implementacji Powodzenie wątku](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) i obejrzyj [live pokaz danych czasowych](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Aby uzyskać szczegółowe informacje o tabelach danych czasowych, przejrzyj [dokumentacji MSDN](https://msdn.microsoft.com/library/dn935015.aspx).

