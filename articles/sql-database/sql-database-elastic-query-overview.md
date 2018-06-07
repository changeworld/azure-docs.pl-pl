---
title: Omówienie zapytania elastycznej bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Elastyczne zapytania umożliwia uruchomienie zapytania języka Transact-SQL, obejmującej wiele baz danych.
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: 43a25d30fbec9327e24230165abd73260397ff14
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646477"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Omówienie usługi Azure zapytania elastycznej bazy danych SQL (wersja zapoznawcza)
Funkcja elastycznej zapytania (w wersji zapoznawczej) umożliwia uruchamianie zapytania języka Transact-SQL, obejmującej wiele baz danych w bazie danych SQL Azure. Umożliwia wykonywanie kwerend bazy danych między dostęp do tabel zdalnych, a także łączenie narzędzi firmy Microsoft i innych firm (Excel, usługi Power BI, Tableau, itp.), zapytanie między warstwami danych z wielu baz danych. Za pomocą tej funkcji, można skalować w poziomie zapytania do warstw dużej ilości danych w bazie danych SQL i wizualizacja wyników business intelligence (BI) raportów.


## <a name="why-use-elastic-queries"></a>Dlaczego warto używać elastycznej zapytania?

**Azure SQL Database**

Zapytanie dla baz danych Azure SQL całkowicie w T-SQL. Dzięki temu tylko do odczytu wysyłania zapytań do zdalnej bazy danych. Zapewnia to opcja dla bieżącego lokalnych klientów programu SQL Server do migracji aplikacji przy użyciu trzech i czterech niepełnym nazw lub połączonego serwera do bazy danych SQL.

**Dostępne w warstwie standardowa**

Elastyczne zapytania jest obsługiwana w warstwie standardowa wydajności, oprócz wydajności warstwy Premium. Zobacz sekcję dotyczącą ograniczenia wersji zapoznawczej poniżej na ograniczeń wydajności niższe poziomy wydajności.

**Wypychanie do zdalnej bazy danych**

Elastyczne zapytania można teraz Wypchnij parametry SQL do zdalnej bazy danych do wykonania.

**Wykonywanie procedury składowanej**

Wykonywanie wywołań zdalnej procedury składowanej lub funkcji zdalnego przy użyciu [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714).

**Elastyczność**

Tabele zewnętrzne z zapytaniem elastycznej teraz mogą odwoływać się do tabel zdalnych z innym schematem lub nazwę tabeli.

## <a name="elastic-query-scenarios"></a>Scenariusze elastycznej zapytania

Celem jest ułatwienie podczas badania scenariuszy, gdzie wiele baz danych współtworzenia wierszy w jeden wynik ogólny. Zapytania mogą być składane, albo przez użytkownika lub aplikacji bezpośrednio lub pośrednio za pomocą narzędzia, które są połączone z bazą danych. Jest to szczególnie przydatne podczas tworzenia raportów, za pomocą narzędzia integracji komercyjnego BI lub dane — lub dowolnej aplikacji, nie można jej zmienić. Z kwerendy elastycznej mogą wysyłać zapytania dla wielu baz danych przy użyciu znanych obsługi łączności serwera SQL w narzędzi, takich jak program Excel, usługa Power BI, Tableau lub Cognos.
Elastyczne zapytanie umożliwia łatwy dostęp do całej kolekcji baz danych za pomocą zapytań wystawiony przez program SQL Server Management Studio lub Visual Studio i ułatwia badanie między bazami danych z programu Entity Framework lub innych środowisk ORM. Rysunek 1 pokazuje scenariusz, w którym istniejące chmury aplikacji (które używa [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md)) warstwy oparte na danych skalowalnych w poziomie i elastyczny zapytania jest używana do raportowania między bazami danych.

**Rysunek 1** elastycznej Zapytanie używane w warstwie danych skalowalnych w poziomie

![Elastyczne Zapytanie używane w warstwie danych skalowalnych w poziomie][1]

Scenariusze klienta dla elastycznej zapytania charakteryzują się następujących topologii:

* **Partycjonowanie pionowe - zapytań bazy danych między** (topologia 1): dane są podzielone na partycje w pionie między wiele baz danych w warstwie danych. Zazwyczaj różne zestawy tabel znajdują się na różnych baz danych. Oznacza to, że schemat jest różne na różnych baz danych. Na przykład wszystkie tabele spisu są na jedną bazę danych, gdy wszystkie tabele powiązane ewidencjonowania aktywności znajdują się w drugiej bazy danych. Typowe przypadki użycia z tą topologią wymagać wysyłanie zapytań na lub skompilować raporty między tabelami w kilku baz danych.
* **Partycjonowanie poziome - dzielenia na fragmenty** (topologia 2): danych jest podzielona na partycje w poziomie do rozpowszechniają wiersze danych skalowanej warstwy. Z tej metody schemat jest takie same na wszystkich uczestniczących baz danych. Takie podejście jest również nazywany "dzielenia na fragmenty". Mogą być wykonywane dzielenia na fragmenty i zarządzane (1 elastycznej bazy danych za pomocą narzędzi, bibliotek lub (2) samoobsługowego-dzielenia na fragmenty. Elastyczne zapytania służy do zapytań, lub skompilować raporty w wielu fragmentów.

> [!NOTE]
> Elastyczne zapytania najlepiej pasuje do okazjonalne scenariuszy raportowania, gdzie można wykonać większość przetwarzania w warstwie danych. Obciążenia wymagające raportowania lub magazynowania scenariuszy z bardziej złożonych kwerend danych, również należy rozważyć użycie [magazyn danych SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Partycjonowanie pionowe - między bazami danych zapytań

Aby rozpocząć, kodowania, zobacz [wprowadzenie do korzystania z bazy danych między kwerendy (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).

Elastycznej zapytania można udostępnić dane znajdujące się w bazie danych SQL dostępnych w innych bazach danych SQL. Dzięki temu zapytania z jednej bazy danych do odwoływania się do tabel w żadnych innych zdalnej bazy danych SQL. Pierwszym krokiem jest określenie zewnętrznego źródła danych dla każdej zdalnej bazy danych. Zewnętrzne źródło danych jest zdefiniowany w lokalnej bazie danych, z którego chcesz uzyskać dostęp do tabel znajdujących się na zdalnej bazy danych. Żadne zmiany nie jest niezbędna w zdalnej bazy danych. Typowy pionowy partycjonowania scenariusz, w której różnych baz danych mają różne schematów elastycznej zapytania może służyć do wdrożenia typowe przypadki użycia, takich jak dostęp do danych referencyjnych i między bazami danych zapytań.

> [!IMPORTANT]
> Musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączany uprawnienie ALTER DATABASE. Aby odwołać się do źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.
>

**Danych referencyjnych**: Topologia jest używany w celu zarządzania danymi odwołania. Na poniższej ilustracji dwie tabele (T1 a T2) w przypadku danych referencyjnych są przechowywane na dedykowany bazy danych. Przy użyciu elastycznej kwerendy, teraz są dostępne tabele T1 a T2 zdalnie innych baz danych, jak pokazano na rysunku. Użyj topologii 1 w przypadku tabel odwołań małych lub zdalnego zapytań do tabeli referencyjnej ma selektywnego predykatów.

**Rysunek 2** partycjonowanie pionowe — przy użyciu danych elastycznej odwołanie do zapytania

![Partycjonowanie pionowe — przy użyciu danych elastycznej odwołanie do zapytania][3]

**Zapytanie bazy danych między**: elastyczna odpytuje przypadki użycia Włącz wymagających wykonywanie zapytań w kilku baz danych. Rysunek 3 przedstawia czterech różnych baz danych: CRM, spisu i HR produktów. Zapytania wykonywane w jednej bazy danych będą również potrzebować dostępu do jednego lub wszystkich innych baz danych. Przy użyciu elastycznej kwerendy, można skonfigurować bazy danych dla tego przypadku uruchamiając kilka prostych instrukcji DDL na każdej z czterech baz danych. Po tym jednorazowej konfiguracji dostępu do tabeli zdalnej jest tak proste, jak odwołujących się do lokalnej tabeli z zapytania T-SQL lub narzędzi do analizy Biznesowej. Takie podejście jest zalecane, jeśli zapytań zdalnych nie zwracają dużych wyników.

**Rysunek 3** partycjonowanie pionowe — elastyczna do zapytania przy użyciu różnych baz danych

![Partycjonowanie pionowe — elastyczna do zapytania przy użyciu różnych baz danych][4]

Zapytania elastycznej bazy danych dla pionowego partycjonowania scenariusze, które wymagają dostępu do tabeli znajduje się na zdalnym baz danych z tego samego schematu należy skonfigurować następujące kroki:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Utwórz i UPUŚĆ zewnętrzne źródło danych](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **RDBMS**
* [Utwórz i UPUŚĆ tabeli zewnętrznej](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po uruchomieniu instrukcji DDL, jakby był lokalnej tabeli można uzyskać dostępu do tabeli zdalnej "mytable". Baza danych SQL Azure automatycznie otwiera połączenie zdalnej bazy danych, przetwarza żądanie w zdalnej bazy danych i zwraca wyniki.

## <a name="horizontal-partitioning---sharding"></a>Poziomy partycjonowania - dzielenia na fragmenty
Do wykonywania zadań raportowania za pośrednictwem podzielonej, tj., poziomo podzielona na partycje, przy użyciu elastycznej zapytania wymaga warstwy danych [mapy niezależnego fragmentu elastycznej bazy danych](sql-database-elastic-scale-shard-map-management.md) do reprezentowania baz danych w warstwie danych. Zazwyczaj mapy jednego niezależnego fragmentu jest używany w tym scenariuszu, a dedykowany bazy danych z zapytania elastyczne możliwości (węzła głównego) służy jako punkt wejścia dla raportowania zapytań. Tylko ten dedykowany bazy danych musi mieć dostęp do map niezależnego fragmentu. Rysunek 4 przedstawia tej topologii i jego konfiguracja z mapą zapytania elastycznej bazy danych i niezależnego fragmentu. Baz danych w warstwie danych może mieć wersji lub wersji bazy danych SQL Azure. Aby uzyskać więcej informacji na temat biblioteki klienta elastycznej bazy danych i tworzenie map niezależnego fragmentu, zobacz [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).

**Rysunek 4** poziome partycjonowania — przy użyciu elastycznej zapytania dla raportowania za pośrednictwem warstw podzielonej danych

![Poziomy partycjonowania — przy użyciu elastycznej zapytania dla raportowania za pośrednictwem warstw podzielonej danych][5]

> [!NOTE]
> Elastyczne zapytanie do bazy danych (węzła głównego) może być oddzielnej bazy danych lub może być tej samej bazy danych obsługuje mapy niezależnego fragmentu. Niezależnie od konfiguracji wybierzesz, upewnij się, że warstwy usług i wydajności tej bazy danych jest wystarczająco duża, do obsługi żądań logowania/zapytań oczekiwana wartość.

Poniższe kroki należy skonfigurować zapytania elastycznej bazy danych na poziomie partycjonowania scenariusze, które wymagają dostępu do zestawu tabeli znajdujące się na (zwykle) kilka zdalnej bazy danych SQL:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* Utwórz [mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) reprezentujące warstwę danych za pomocą biblioteki klienta elastycznej bazy danych.   
* [Utwórz i UPUŚĆ zewnętrzne źródło danych](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **SHARD_MAP_MANAGER**
* [Utwórz i UPUŚĆ tabeli zewnętrznej](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po wykonaniu tych czynności możesz uzyskać dostęp tabeli partycjonowanej poziomo "mytable" tak, jakby była lokalnej tabeli. Baza danych SQL Azure automatycznie otwiera wielu równoległych połączeń zdalnych baz danych, gdzie fizycznie przechowywane są tabele, przetwarza żądania dotyczące zdalnego baz danych i zwraca wyniki.
Więcej informacji na temat kroków wymaganych dla poziomych partycjonowania scenariusza można znaleźć w [elastycznej zapytania poziome partycjonowania](sql-database-elastic-query-horizontal-partitioning.md).

Aby rozpocząć, kodowania, zobacz [wprowadzenie elastycznej zapytania poziome partycjonowania (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Wykonywanie zapytania T-SQL
Po zdefiniowaniu z zewnętrznych źródeł danych i tabel zewnętrznych można umożliwiają nawiązanie baz danych gdzie są zdefiniowane z tabel zewnętrznych prawidłowe parametry połączenia SQL Server. Następnie możesz uruchomić instrukcje T-SQL w tabelach zewnętrznych dla tego połączenia z ograniczeniami opisane poniżej. Dodatkowe informacje i przykłady zapytania T-SQL można znaleźć w tematach dokumentacji dla [poziomych partycji](sql-database-elastic-query-horizontal-partitioning.md) i [partycjonowanie pionowe](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Połączenie narzędzi
Prawidłowe parametry połączenia SQL Server umożliwia połączyć aplikacje i narzędzia integracji BI lub danych do baz danych, które mają tabel zewnętrznych. Upewnij się, czy program SQL Server jest obsługiwana jako źródło danych dla własnych narzędzi. Po nawiązaniu połączenia dotyczą kwerendy elastycznej bazy danych i tabel zewnętrznych w tej bazie danych tak samo, jak należy wszelkie inne bazy danych SQL Server nawiązywania połączenia z własnych narzędzi.

> [!IMPORTANT]
> Uwierzytelnianie przy użyciu usługi Azure Active Directory z zapytaniami elastyczne nie jest obecnie obsługiwane.
> 
> 

## <a name="cost"></a>Koszty
Elastyczne zapytania jest uwzględniony w koszt baz danych z bazy danych SQL Azure. Należy pamiętać, że obsługiwanych topologii, gdzie zdalnej bazy danych znajdują się w centrum danych innego niż punkt końcowy elastycznej kwerendy, ale wyjście danych ze zdalnych baz danych są naliczane regular [Azure stawki](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej
* Uruchamianie pierwszej kwerendy elastycznej może potrwać kilka minut w warstwie standardowa wydajności. Ten czas jest niezbędne, aby załadować funkcjonalności elastycznej zapytania. Ładowanie wydajności poprawia z warstwy wyższej wydajności.
* Obsługa skryptów z zewnętrznych źródeł danych lub tabel zewnętrznych z SSMS lub program SSDT nie jest jeszcze obsługiwana.
* Narzędzie importu/eksportu dla bazy danych serwera SQL nie obsługuje jeszcze zewnętrznych źródeł danych i tabel zewnętrznych. Jeśli musisz użyć importu/eksportu, usunąć przed wyeksportowaniem te obiekty, a następnie ponownie utwórz je po zaimportowaniu.
* Elastyczne zapytania aktualnie obsługuje tylko dostęp tylko do odczytu do tabel zewnętrznych. Jednak można używać pełnej funkcjonalności T-SQL w bazie danych której zdefiniowano tabeli zewnętrznej. Może to być przydatne, np. zachować tymczasowych wyników za pomocą, np., wybierz < column_list > w < local_table > lub określenie procedur składowanych na kwerendy elastycznej bazy danych, które odnoszą się do tabel zewnętrznych.
* Z wyjątkiem nvarchar(max) typy obiektów LOB nie są obsługiwane w definicji tabeli zewnętrznej. Jako rozwiązanie alternatywne można utworzyć widoku zdalnej bazy danych, która rzutuje typu LOB do nvarchar(max), definiowania tabeli zewnętrznej za pośrednictwem widoku zamiast tabeli podstawowej i rzutować go wrócić do oryginalnego typu LOB zapytania.
* Kolumna statystyki dotyczące tabel zewnętrznych nie są obecnie obsługiwane. Tabele statystyki są obsługiwane, ale muszą być utworzone ręcznie.

## <a name="feedback"></a>Opinia
Udostępnij opinii na temat korzystania z zapytaniami elastycznej z nami Livefyre poniżej, fora MSDN lub w witrynie Stackoverflow. Firma Microsoft interesuje wszelkiego rodzaju opinię na temat usługi (wady, niedociągnięcia luki funkcji).

## <a name="next-steps"></a>Kolejne kroki

* Samouczek partycjonowania pionowego, zobacz [wprowadzenie do korzystania z bazy danych między kwerendy (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Dla zapytań dotyczących danych pionowo podzielonym na partycje i składnię i przykład, zobacz [zapytań w pionie na partycje danych)](sql-database-elastic-query-vertical-partitioning.md)
* Samouczek poziomych partycji (dzielenia na fragmenty), zobacz [wprowadzenie elastycznej zapytania poziome partycjonowania (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Dla zapytań dotyczących danych poziomie podzielonym na partycje i składnię i przykład, zobacz [zapytań w poziomie na partycje danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL w jednym zdalnej bazy danych SQL Azure lub zestaw baz danych, służąc jako odłamków w poziomie schemat partycjonowania.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
