---
title: Zarządzanie danych historycznych w tabelach danych czasowych z zasadami przechowywania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zachować dane historyczne kontroli nad za pomocą zasad przechowywania danych czasowych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 09/25/2018
ms.date: 02/25/2019
ms.openlocfilehash: 62e88d912c55015f87cc00f21527010ad01ee00c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615670"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Zarządzanie danych historycznych w tabelach danych czasowych przy użyciu zasad przechowywania

Tabele danych czasowych może zwiększyć rozmiar bazy danych, więcej niż regularne tabel, zwłaszcza, jeśli zachować historyczne dane przez dłuższy czas. Dzięki temu zasady przechowywania dla danych historycznych jest istotnym elementem planowania i zarządzania cyklem życia w każdej tabeli danych czasowych. Tabele danych czasowych w bazie danych SQL Azure są dostarczane z mechanizmu przechowywania łatwy w użyciu, który pomaga wykonać to zadanie.

Czas przechowywania historii danych czasowych mogą być konfigurowane na poziomie pojedynczej tabeli, co pozwala użytkownikom na tworzenie przestarzałej elastyczne zasady. Stosowanie przechowywania danych czasowych jest prosty: wymaga, aby tylko jeden parametr należy ustawić podczas zmiany schematu lub tworzenia tabeli.

Po zdefiniowaniu zasad przechowywania, Azure SQL Database zacznie regularnie czy historyczne wierszy, które kwalifikują się do automatycznego oczyszczania. Identyfikacja zgodnych wierszy oraz ich usunięcia z tabeli historii występują w sposób niewidoczny dla użytkownika, w zadanie w tle, który jest zaplanowany i wykonywane przez system. Warunek wieku wiersze tabeli historii jest sprawdzany w oparciu o kolumnę reprezentujący koniec okresu SYSTEM_TIME. Jeśli okres przechowywania, na przykład, jest równa sześciu miesięcy, wiersze tabeli kwalifikuje się do oczyszczania spełniać następujący warunek:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

W poprzednim przykładzie, możemy przyjąć, że **ValidTo** kolumny odnosi się do końca okresu SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Jak skonfigurować zasady przechowywania

Przed skonfigurowaniem zasad przechowywania dla tabeli danych czasowych należy najpierw sprawdzić czy włączone jest przechowywanie historycznych danych czasowych *na poziomie bazy danych*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Baza danych flagi **is_temporal_history_retention_enabled** jest domyślnie włączone, ale użytkownicy mogą zmienić ją za pomocą instrukcji ALTER DATABASE. Automatycznie ustawiana jest na OFF po [punktu w czasie](sql-database-recovery-using-backups.md) operacji. Aby włączyć oczyszczanie przechowywania historii danych czasowych z bazą danych, wykonaj następującą instrukcję:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Można skonfigurować przechowywanie tabel danych czasowych nawet wtedy, gdy **is_temporal_history_retention_enabled** jest WYŁĄCZONY, ale automatycznego oczyszczania dla przestarzałych wierszy nie jest wyzwalany w takiej sytuacji.

Zasady przechowywania zostały skonfigurowane podczas tworzenia tabeli, określając wartość dla parametru wartości infinite:

```sql
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
```

Usługa Azure SQL Database pozwala określić okres przechowywania danych za pomocą różne jednostki: DNI, tygodnie, MIESIĄCE i lata. W przypadku pominięcia wartości infinite przyjmowana jest wartość NIESKOŃCZONA przechowywania. Umożliwia także NIESKOŃCZONEJ — słowo kluczowe jawnie.

W niektórych scenariuszach możesz chcieć skonfigurowanie okresu przechowywania po utworzeniu tabeli lub można zmienić wcześniej skonfigurowane wartości. W takim przypadku należy użyć instrukcji ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Ustawienie wartości OFF dla elementu SYSTEM_VERSIONING *nie zachowa* wartość okresu przechowywania. Ustawienie opcji SYSTEM_VERSIONING na ON, bez wartości infinite jawnie określone wyniki w NIEOGRANICZONY okres przechowywania.

Aby wyświetlić bieżący stan zasady przechowywania, użyj następującego zapytania, który łączy się Flaga włączenia przechowywania danych czasowych z okresów przechowywania dla poszczególnych tabel na poziomie bazy danych:

```sql
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
```


## <a name="how-sql-database-deletes-aged-rows"></a>Jak bazy danych SQL usuwa przestarzałe wiersze

Proces czyszczenia zależy od układ indeksu tabeli historii. Należy zauważyć, że *tylko do tabel historii z indeksem klastrowanym, (B-drzewa lub magazynu kolumn) może mieć zasady skończonym okresem przechowywania skonfigurowane*. Zadanie w tle jest utworzone w celu wykonania oczyszczania przestarzałych danych dla wszystkich tabel danych czasowych z skończonego okresu przechowywania.
Logika oczyszczania dla klastrowanego indeksu magazynu wierszy (B-drzewa) usuwa przestarzałe wiersz na mniejsze fragmenty (maksymalnie 10 kilobajtów) minimalizując wykorzystanie dziennika bazy danych i podsystem We/Wy. Mimo że oczyszczania logiki korzysta z wymaganym indeksie B-drzewa, kolejność operacji usunięcia dla starszych niż okres przechowywania danych nie można zagwarantować mocno wierszy. Dzięki temu *nie przyjmują żadnych zależności od kolejności oczyszczania w swoich aplikacjach*.

Zadanie oczyszczania dla klastrowanego magazynu kolumn spowoduje usunięcie całego [wiersz grup](https://msdn.microsoft.com/library/gg492088.aspx) jednocześnie (zwykle zawiera 1 mln wierszy w poszczególnych), co jest bardzo wydajny, szczególnie w przypadku, gdy dane historyczne są generowane w szybkim tempie.

![Przechowywanie klastrowanego magazynu kolumn](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Kompresji danych doskonałą i wydajne przechowywania oczyszczania sprawia, że klastrowane indeksu magazynu kolumn doskonałym rozwiązaniem dla scenariuszy, jeśli obciążenie generuje szybko dużej ilości danych historycznych. Ten wzorzec jest typowe dla dużych [obciążeń przetwarzania transakcyjnego, które tabele danych czasowych](https://msdn.microsoft.com/library/mt631669.aspx) dla śledzenia zmian i inspekcji, analizy trendu i przetwarzanie danych IoT.

## <a name="index-considerations"></a>Zagadnienia dotyczące indeksu

Zadanie oczyszczania dla tabel z klastrowanego indeksu magazynu wierszy wymaga indeksu zaczynać kolumnę odpowiadającą koniec okresu SYSTEM_TIME. Jeśli takie indeks nie istnieje, nie można skonfigurować skończony okres przechowywania:

*Msg 13765, poziom 16, stan 1 <br> </br> Ustawianie skończonego okresu przechowywania nie można w tabeli danych czasowych z systemową obsługą "temporalstagetestdb.dbo.WebsiteUserInfo", ponieważ tabela historii " temporalstagetestdb.dbo.WebsiteUserInfoHistory "nie zawiera wymaganego indeksu klastrowanego. Należy rozważyć utworzenie klastrowanego magazynu kolumn lub indeksu B-drzewa rozpoczynającego się od kolumny zgodnej z końcem SYSTEM_TIME period, w tabeli historii.*

Należy zauważyć, że tabela historii domyślnych utworzonych przez usługę Azure SQL Database jest już zawiera klastrowany indeks, który jest zgodny z zasad przechowywania. Jeśli spróbujesz usunąć indeksu dla tabeli z skończonego okresu przechowywania, operacja zakończy się niepowodzeniem z powodu następującego błędu:

*Msg 13766, poziom 16, stan 1 <br> </br> nie można porzucić indeksu klastrowanego "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory", ponieważ jest on używany do automatycznego czyszczenia danych przestarzałych danych. Rozważ ustawienie wartości infinite dla HISTORY_RETENTION_PERIOD w odpowiedniej tabeli danych czasowych wersjonowana przez system, jeśli chcesz usunąć ten indeks.*

Oczyszczanie na indeks klastrowany magazyn kolumn działa optymalnie, jeśli historycznych wiersze zostały wstawione w kolejności rosnącej (uporządkowane według koniec okresu kolumny), która zawsze ma miejsce w przypadku tabeli historii jest wypełniana wyłącznie przez mechanizm SYSTEM_VERSIONIOING. Wiersze w tabeli historii nie są uporządkowane według koniec okresu kolumny, (które mogą być wymagane, jeśli wykonano migrację istniejących danych historycznych), należy ponownie utworzyć klastrowanego indeksu magazynu kolumn na podstawie indeksu magazynu wierszy B-drzewa, że jest prawidłowo uporządkowane, aby osiągnąć optymalną wydajność.

Należy unikać odbudowanie indeksu klastrowanego magazynu kolumn w tabeli historii z okresem skończonym okresem przechowywania, ponieważ mogą ulec zmianie, porządkowanie w grupy wierszy naturalnie nałożone przez operację wersjonowania systemu. Jeśli trzeba odbudować klastrowanego indeksu magazynu kolumn w tabeli historii, to zrobić przez ponowne utworzenie go na podstawie zgodności indeksu B-drzewa, zachowywanie kolejności w rowgroups niezbędne do oczyszczania regularnych danych. To samo podejście należy podjąć w przypadku utworzenia tabeli danych czasowych z istniejącej tabeli historii, która zawiera klastrowany indeks kolumny bez gwarancji data zamówienia:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Po skonfigurowaniu skończonego okresu przechowywania w tabeli historii z indeksem klastrowanego magazynu kolumn nie można utworzyć dodatkowe nieklastrowanych indeksów B-drzewa, w tej tabeli:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Próba wykonania powyżej instrukcja kończy się niepowodzeniem z powodu następującego błędu:

*Msg 13772, poziom 16, stan 1 <br> </br> nie można utworzyć indeksu nieklastrowanego w tabeli historii danych czasowych "WebsiteUserInfoHistory", ponieważ ma skończony okres przechowywania i klastrowany indeks magazynu kolumn zdefiniowane.*

## <a name="querying-tables-with-retention-policy"></a>Tworzenie zapytań o tabele z zasadami przechowywania

Wszystkie zapytania w tabeli danych czasowych automatycznie filtrowanie wierszy historycznych dopasowania zasad skończonym okresem przechowywania, aby uniknąć nieprzewidywalnych i niespójne wyniki, ponieważ przestarzałych wierszy może zostać usunięta przez zadanie oczyszczania *w dowolnym momencie w czasie i na dowolne kolejność*.

Na poniższej ilustracji przedstawiono planu zapytania dla prostego zapytania:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

W planie zapytania zawiera dodatkowy filtr stosowane do końca okresu kolumny (ValidTo) w operatorze skanowania indeksu klastrowanego w tabeli historii (wyróżnione). W tym przykładzie przyjęto założenie, ustawiono tego MIESIĘCZNEGO okresu przechowywania w tabeli WebsiteUserInfo.

![Filtr zapytania przechowywania](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Jednak w tabeli historii zapytania bezpośrednio, może zostać wyświetlony wierszy, które są starsze niż okres przechowywania określony okres, ale bez żadnych gwarancji dla wyników zapytań powtarzalny. Na poniższej ilustracji przedstawiono planu wykonania zapytania dla zapytania w tabeli historii, bez dodatkowych zastosowane filtry:

![Trwa badanie historii bez przechowywania filtru](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nie należy polegać logikę biznesową na odczytywanie tabeli historii, po upływie okresu przechowywania, jak możesz uzyskać wyniki niespójne lub nieoczekiwany. Zalecamy użycie czasowych zapytań za pomocą klauzuli FOR SYSTEM_TIME do analizowania danych w tabelach danych czasowych.

## <a name="point-in-time-restore-considerations"></a>Punkt w czasie przywracania zagadnienia

Podczas tworzenia nowej bazy danych przez [Przywracanie istniejącą bazę danych do określonego punktu w czasie](sql-database-recovery-using-backups.md), ma ona przechowywania danych czasowych wyłączona na poziomie bazy danych. (**is_temporal_history_retention_enabled** ustawiona flaga off). Ta funkcja umożliwia zbadanie wszystkich wierszy historycznych podczas przywracania, nie martwiąc się, czy przestarzałych wiersze zostały usunięte przed można uzyskać dostęp do nich kwerendy. Możesz użyć go do *sprawdzanie danych historycznych, po przekroczeniu skonfigurowanego okresu przechowywania*.

Załóżmy, że wystąpił przechowywania jednego miesiąca określonego okresu w tabeli danych czasowych. Jeśli baza danych została utworzona w warstwie Premium systemu Azure, będzie można utworzyć kopię bazy danych o stanie bazy danych się do 35 dni w przeszłości. Który skutecznie będzie pozwalają analizować historycznych wierszy, które są maksymalnie 65 dni, badając tabelę historii bezpośrednio.

Jeśli chcesz aktywować wyczyszczenia danych czasowych przechowywanych, uruchom następującą instrukcję języka Transact-SQL po punkcie w czasie:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak używać tabel danych czasowych w aplikacjach, zapoznaj się z [wprowadzenie do tabel danych czasowych w usłudze Azure SQL Database](sql-database-temporal-tables.md).

Można znaleźć w witrynie Channel 9, aby usłyszeć [Historia sukcesu wdrożenia danych czasowych rzeczywiste](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) i obejrzyj [danych czasowych pokaz na żywo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Aby uzyskać szczegółowe informacje o tabelach danych czasowych, przejrzyj [dokumentacji MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
