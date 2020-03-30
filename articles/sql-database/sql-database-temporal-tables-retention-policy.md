---
title: Zarządzanie danymi historycznymi w tabelach czasowych
description: Dowiedz się, jak używać zasad przechowywania czasowego, aby zachować kontrolę nad danymi historycznymi.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820682"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Zarządzanie danymi historycznymi w tabelach czasowych za pomocą zasad przechowywania

Tabele czasowe mogą zwiększać rozmiar bazy danych bardziej niż zwykłe tabele, zwłaszcza jeśli dane historyczne są przechowywane przez dłuższy czas. W związku z tym zasady przechowywania danych historycznych jest ważnym aspektem planowania i zarządzania cyklem życia każdej tabeli czasowej. Tabele czasowe w usłudze Azure SQL Database są wyposażone w łatwy w użyciu mechanizm przechowywania, który pomaga wykonać to zadanie.

Przechowywanie historii czasowej można skonfigurować na poziomie poszczególnych tabel, co pozwala użytkownikom na tworzenie elastycznych zasad starzenia się. Stosowanie przechowywania czasowego jest proste: wymaga tylko jednego parametru, który ma być ustawiony podczas tworzenia tabeli lub zmiany schematu.

Po zdefiniowaniu zasad przechowywania usługa Azure SQL Database rozpoczyna regularne sprawdzanie, czy istnieją historyczne wiersze, które kwalifikują się do automatycznego oczyszczania danych. Identyfikacja pasujących wierszy i ich usunięcie z tabeli historii odbywa się w sposób przezroczysty w zadaniu w tle, które jest zaplanowane i uruchamiane przez system. Warunek wieku dla wierszy tabeli historii jest sprawdzany na podstawie kolumny reprezentującej koniec okresu SYSTEM_TIME. Jeśli na przykład okres przechowywania jest ustawiony na sześć miesięcy, wiersze tabeli kwalifikujące się do czyszczenia spełniają następujący warunek:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

W poprzednim przykładzie założyliśmy, że **kolumna ValidTo** odpowiada zakończeniu okresu SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Jak skonfigurować zasady przechowywania

Przed skonfigurowaniem zasad przechowywania dla tabeli czasowej należy najpierw sprawdzić, czy *czasowa*retencja historyczna jest włączona na poziomie bazy danych .

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Flaga bazy danych **is_temporal_history_retention_enabled** jest domyślnie ustawiona na ON, ale użytkownicy mogą ją zmienić za pomocą instrukcji ALTER DATABASE. Jest również automatycznie ustawiany na OFF po [operacji przywracania punktu w czasie.](sql-database-recovery-using-backups.md) Aby włączyć oczyszczanie przechowywania historii czasowej dla bazy danych, wykonaj następującą instrukcję:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Można skonfigurować retencji dla tabel czasowych, nawet jeśli **is_temporal_history_retention_enabled** jest wyłączony, ale automatyczne oczyszczanie dla przestarzałych wierszy nie jest wyzwalane w tym przypadku.

Zasady przechowywania są konfigurowane podczas tworzenia tabeli przez określenie wartości parametru HISTORY_RETENTION_PERIOD:

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

Usługa Azure SQL Database umożliwia określenie okresu przechowywania przy użyciu różnych jednostek czasu: DNI, TYGODNIE, MIESIĄCE i LATA. Jeśli HISTORY_RETENTION_PERIOD zostanie pominięty, zakłada się, że przechowywanie INFINITE. Można również użyć infinite słowa kluczowego jawnie.

W niektórych scenariuszach można skonfigurować retencji po utworzeniu tabeli lub zmienić wcześniej skonfigurowaną wartość. W takim przypadku należy użyć instrukcji ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Ustawienie SYSTEM_VERSIONING na OFF *nie zachowuje* wartości okresu przechowywania. Ustawienie SYSTEM_VERSIONING na ON bez HISTORY_RETENTION_PERIOD wyraźnie określa okres przechowywania INFINITE.

Aby przejrzeć bieżący stan zasad przechowywania, należy użyć następującej kwerendy, która łączy flagę włączania przechowywania czasowego na poziomie bazy danych z okresami przechowywania dla poszczególnych tabel:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Jak baza danych SQL usuwa przestarzałe wiersze

Proces oczyszczania zależy od układu indeksu tabeli historii. Ważne jest, aby zauważyć, że *tylko tabele historii z indeksem klastrowanym (B-drzewo lub magazyn kolumn) mogą mieć skonfigurowane zasady ograniczonego przechowywania.* Zadanie w tle jest tworzone w celu wykonywania oczyszczania przestarzałych danych dla wszystkich tabel czasowych z skończonego okresu przechowywania.
Logika oczyszczania indeksu klastrowanego magazynu wierszy (B-tree) usuwa przestarzały wiersz w mniejszych fragmentach (do 10 000) minimalizując presję na dziennik bazy danych i podsystem we/wy. Chociaż logika oczyszczania wykorzystuje wymagany indeks drzewa B, kolejność usuń dla wierszy starszych niż okres przechowywania nie może być mocno zagwarantowana. W związku z tym *nie należy przyjmować żadnych zależności od kolejności oczyszczania w aplikacjach*.

Zadanie oczyszczania dla klastrowanego magazynu kolumn usuwa całe [grupy wierszy](https://msdn.microsoft.com/library/gg492088.aspx) jednocześnie (zazwyczaj zawierają po 1 milion wierszy), co jest bardzo wydajne, szczególnie gdy dane historyczne są generowane w szybkim tempie.

![Retencja magazynu kolumn klastrowanych](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Doskonała kompresja danych i efektywne oczyszczanie przechowywania sprawia, że indeks klastrowanego magazynu kolumn jest idealnym wyborem dla scenariuszy, gdy obciążenie szybko generuje dużą ilość danych historycznych. Ten wzorzec jest typowy dla [intensywnych obciążeń przetwarzania transakcyjnego, które używają tabel czasowych](https://msdn.microsoft.com/library/mt631669.aspx) do śledzenia zmian i inspekcji, analizy trendów lub pozyskiwania danych IoT.

## <a name="index-considerations"></a>Zagadnienia dotyczące indeksu

Zadanie oczyszczania dla tabel z indeksem klastrowanym magazynu wierszy wymaga indeksu, aby rozpocząć od kolumny odpowiadającej na koniec okresu SYSTEM_TIME. Jeśli taki indeks nie istnieje, nie można skonfigurować skończonego okresu przechowywania:

*Msg 13765, Poziom 16, <br> </br> Stan 1 Ustawienie skończonego okresu przechowywania nie powiodło się w tabeli czasowej o wersji systemowej "temporalstagetestdb.dbo.WebsiteUserInfo", ponieważ tabela historii "temporalstagetestdb.dbo.WebsiteUserInfoHistory" nie zawiera wymaganego indeksu klastrowanego. Należy rozważyć utworzenie klastrowanego magazynu kolumn lub indeksu drzewa B, począwszy od kolumny, która pasuje do końca okresu SYSTEM_TIME, w tabeli historii.*

Należy zauważyć, że domyślna tabela historii utworzona przez usługę Azure SQL Database ma już indeks klastrowany, który jest zgodny z zasadami przechowywania. Jeśli spróbujesz usunąć ten indeks w tabeli z skończonego okresu przechowywania, operacja zakończy się niepowodzeniem z następującym błędem:

*Msg 13766, Poziom 16, <br> </br> Stan 1 Nie można usunąć indeks klastrowany "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory", ponieważ jest używany do automatycznego oczyszczania przestarzałych danych. Należy rozważyć ustawienie HISTORY_RETENTION_PERIOD infinite w odpowiedniej tabeli czasowej wersją systemu, jeśli chcesz upuścić ten indeks.*

Oczyszczanie w indeksie magazynu kolumn klastrowanych działa optymalnie, jeśli historyczne wiersze są wstawiane w kolejności rosnąco (uporządkowane na końcu kolumny okresu), co zawsze ma miejsce, gdy tabela historii jest wypełniana wyłącznie przez mechanizm SYSTEM_VERSIONIOING. Jeśli wiersze w tabeli historii nie są uporządkowane do końca kolumny okresu (co może mieć miejsce w przypadku migracji istniejących danych historycznych), należy ponownie utworzyć indeks klastrowanego magazynu kolumn na indeksie magazynu b-drzewo, który jest prawidłowo uporządkowany, aby osiągnąć optymalny Wydajności.

Należy unikać przebudowy indeksu magazynu kolumn klastrowanych w tabeli historii z skończonego okresu przechowywania, ponieważ może to zmienić kolejność w grupach wierszy naturalnie nałożone przez system-versioning operacji. Jeśli chcesz odbudować indeks magazynu kolumn klastrowanych w tabeli historii, zrób to, tworząc go ponownie na podstawie zgodnego indeksu drzewa B, zachowując kolejność w grupach wierszy niezbędnych do regularnego oczyszczania danych. Takie samo podejście należy podjąć, jeśli tworzysz tabelę czasową z istniejącą tabelą historii, która ma indeks kolumn klastrowanych bez gwarantowanej kolejności danych:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Gdy skończony okres przechowywania jest skonfigurowany dla tabeli historii z indeksem klastrowanego magazynu kolumn, nie można utworzyć dodatkowych indeksów drzewa B nieklastrowanych w tej tabeli:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Próba wykonania powyższej instrukcji kończy się niepowodzeniem z następującym błędem:

*Msg 13772, Poziom 16, <br> </br> Stan 1 Nie można utworzyć indeksu nieklastrowanego w tabeli historii czasowej "WebsiteUserInfoHistory", ponieważ ma ograniczony okres przechowywania i zdefiniowany indeks magazynu kolumn klastrowanych.*

## <a name="querying-tables-with-retention-policy"></a>Wykonywanie zapytań z zasadami przechowywania

Wszystkie kwerendy w tabeli czasowej automatycznie odfiltrowują historyczne wiersze pasujące do zasad ograniczonego przechowywania, aby uniknąć nieprzewidywalnych i niespójnych wyników, ponieważ przestarzałe wiersze mogą zostać usunięte przez zadanie oczyszczania, *w dowolnym momencie i w dowolnej kolejności*.

Na poniższej ilustracji przedstawiono plan kwerendy dla prostej kwerendy:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Plan kwerend zawiera dodatkowy filtr zastosowany do kolumny końca okresu (ValidTo) w operatorze skanowania indeksu klastrowanego w tabeli historii (wyróżniona). W tym przykładzie przyjęto założenie, że jeden miesiąc okres przechowywania został ustawiony na websiteUserInfo tabeli.

![Filtr kwerend retencyjny](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Jednak jeśli kwerendy tabeli historii bezpośrednio, mogą być widoczne wiersze, które są starsze niż określony okres przechowywania, ale bez żadnej gwarancji dla wyników kwerendy powtarzalne. Na poniższej ilustracji przedstawiono plan wykonania kwerendy dla kwerendy w tabeli historii bez zastosowania dodatkowych filtrów:

![Wykonywanie zapytań o historię bez filtru przechowywania](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nie należy polegać na logiki biznesowej na tabeli historii czytania po okresie przechowywania, ponieważ mogą pojawić się niespójne lub nieoczekiwane wyniki. Zaleca się użycie zapytań czasowych z klauzulą FOR SYSTEM_TIME do analizowania danych w tabelach czasowych.

## <a name="point-in-time-restore-considerations"></a>Zagadnienia dotyczące przywracania punktu w czasie

Podczas tworzenia nowej bazy danych przez [przywrócenie istniejącej bazy danych do określonego punktu w czasie,](sql-database-recovery-using-backups.md)ma czasowe przechowywania wyłączone na poziomie bazy danych. **(is_temporal_history_retention_enabled** flaga ustawiona na OFF). Ta funkcja umożliwia zbadanie wszystkich wierszy historycznych po przywróceniu, nie martwiąc się, że przestarzałe wiersze są usuwane przed uzyskaniem ich zapytania. Można go używać do *sprawdzania danych historycznych poza skonfigurowanym okresem przechowywania*.

Załóżmy, że tabela czasowa ma określony okres przechowywania w ciągu jednego miesiąca. Jeśli baza danych została utworzona w warstwie usługi Premium, można utworzyć kopię bazy danych ze stanem bazy danych do 35 dni wstecz w przeszłości. To skutecznie pozwoliłoby na analizowanie wierszy historycznych, które są do 65 dni, bezpośrednio przez zapytanie tabeli historii bezpośrednio.

Jeśli chcesz aktywować oczyszczanie przechowywania czasowego, uruchom następującą instrukcję Transact-SQL po przywróceniu punktu w czasie:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać tabel czasowych w aplikacjach, zapoznaj się [z wprowadzeniem do tabel czasowych w bazie danych SQL Azure](sql-database-temporal-tables.md).

Odwiedź Kanał 9, aby usłyszeć [prawdziwy sukces wdrożenia czasowego klienta](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) i obejrzeć na żywo pokaz [czasowy.](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)

Aby uzyskać szczegółowe informacje na temat tabel czasowych, zapoznaj się z [dokumentacją MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
