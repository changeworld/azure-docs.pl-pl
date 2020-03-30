---
title: Technologie in-memory
description: Technologie usługi Azure SQL Database In-Memory znacznie zwiększają wydajność obciążeń transakcyjnych i analitycznych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810262"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optymalizuj wydajność przy użyciu technologii wbudowanych w bazie danych SQL

Technologie w pamięci w usłudze Azure SQL Database umożliwiają zwiększenie wydajności aplikacji i potencjalnie obniżenie kosztów bazy danych. 

## <a name="when-to-use-in-memory-technologies"></a>Kiedy używać technologii w pamięci

Korzystając z technologii w pamięci w bazie danych SQL azure, można osiągnąć poprawę wydajności przy różnych obciążeń:

- **Transakcyjne** (przetwarzanie transakcyjne online (OLTP)), gdzie większość żądań odczytuje lub aktualizuje mniejszy zestaw danych (na przykład operacje CRUD).
- Analityczne (przetwarzanie analityczne online (OLAP)), gdzie większość zapytań ma złożone obliczenia do celów **raportowania,** z pewną liczbą zapytań, które ładują i dołączają dane do istniejących tabel (tzw. zbiorcze ładowanie) lub usuwają dane z tabel. 
- **Mieszane** (transakcja hybrydowa/przetwarzanie analityczne (HTAP)), gdzie zarówno zapytania OLTP, jak i OLAP są wykonywane na tym samym zestawie danych.

Technologie w pamięci mogą poprawić wydajność tych obciążeń, przechowując dane, które powinny być przetwarzane w pamięci, używając kompilacji natywnej zapytań lub zaawansowanego przetwarzania, takiego jak przetwarzanie wsadowe i instrukcje SIMD, które są dostępne w sprzętu. 

## <a name="overview"></a>Omówienie

Usługa Azure SQL Database ma następujące technologie w pamięci:
- *[Oltp w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* zwiększa liczbę transakcji na sekundę i zmniejsza opóźnienia przetwarzania transakcji. Scenariusze, które korzystają z oltp w pamięci są: przetwarzanie transakcji o wysokiej przepływności, takich jak handel i gry, pozyskiwania danych ze zdarzeń lub urządzeń IoT, buforowanie, ładowanie danych i tymczasowe tabeli i tabeli zmiennych scenariuszy.
- *Indeksy klastrowanego magazynu kolumn* zmniejszają rozmiar magazynu (do 10 razy) i zwiększają wydajność zapytań raportowania i analizy. Można go używać z tabelami faktów w składkach danych, aby zmieścić więcej danych w bazie danych i zwiększyć wydajność. Ponadto można go używać z danymi historycznymi w operacyjnej bazie danych do archiwizacji i być w stanie zbadać do 10 razy więcej danych.
- *Indeksy magazynu kolumn nieklastrowanych* dla HTAP pomagają uzyskać wgląd w czasie rzeczywistym w firmę poprzez bezpośrednie wykonywanie zapytań o operacyjną bazę danych bez konieczności uruchamiania kosztownego procesu wyodrębniania, przekształcania i ładowania (ETL) i oczekiwania na zapełnienie magazynu danych. Indeksy magazynu kolumn nieklastrowanych umożliwiają szybkie wykonywanie zapytań analitycznych w bazie danych OLTP, jednocześnie zmniejszając wpływ na obciążenie operacyjne.
- *Zoptymalizowane pod kątem pamięci indeksy magazynu kolumn klastrowanych* dla HTAP umożliwia szybkie przetwarzanie transakcji i *jednocześnie* uruchamiać zapytania analityczne bardzo szybko na tych samych danych.

Zarówno indeksy magazynu kolumn, jak i OLTP w pamięci są częścią produktu SQL Server odpowiednio od 2012 i 2014 roku. Usługa Azure SQL Database i SQL Server współużytkuje tę samą implementację technologii w pamięci. W przyszłości nowe możliwości dla tych technologii są najpierw wydawane w usłudze Azure SQL Database, zanim zostaną wydane w programie SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Zalety technologii In-Memory

Ze względu na bardziej wydajne przetwarzanie zapytań i transakcji, technologie w pamięci pomagają również obniżyć koszty. Zazwyczaj nie trzeba uaktualniać warstwy cenowej bazy danych, aby osiągnąć wzrost wydajności. W niektórych przypadkach może nawet być w stanie zmniejszyć warstwę cenową, a jednocześnie widząc poprawę wydajności za pomocą technologii w pamięci.

Oto dwa przykłady tego, jak oltp w pamięci pomógł znacznie poprawić wydajność:

- Korzystając z oltpu w pamięci, [rozwiązania biznesowe Kworum były w stanie podwoić swoje obciążenie, jednocześnie poprawiając jednostki DTU o 70%.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

  - Jednostka DTU oznacza *jednostkę transakcji bazy danych*i obejmuje pomiar zużycia zasobów.
- Poniższy film pokazuje znaczną poprawę zużycia zasobów przy przykładowym obciążeniu: [OlTP w pamięci w usłudze Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Aby uzyskać więcej informacji, zobacz wpis w blogu: [In-Memory OLTP w usłudze Azure SQL Database Wpis w blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Technologie w pamięci są dostępne w bazach danych SQL platformy Azure i pul elastycznych premium w warstwie Premium i w warstwie Krytyczna dla firmy.

W poniższym klipie wideo wyjaśniono potencjalne wzrost wydajności za pomocą technologii w pamięci w bazie danych SQL azure. Należy pamiętać, że przyrost wydajności, który widzisz zawsze zależy od wielu czynników, w tym charakter obciążenia i danych, wzorzec dostępu do bazy danych i tak dalej.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

W tym artykule opisano aspekty indeksów OLTP w pamięci i magazynu kolumn, które są specyficzne dla usługi Azure SQL Database, a także zawiera przykłady:

- Zobaczysz wpływ tych technologii na limity rozmiaru magazynu i danych.
- Zobaczysz, jak zarządzać przenoszeniem baz danych korzystających z tych technologii między różnymi warstwami cenowymi.
- Zobaczysz dwa przykłady, które ilustrują użycie oltp w pamięci, a także indeksy magazynu kolumn w usłudze Azure SQL Database.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie oltp w pamięci i scenariusze użycia](https://msdn.microsoft.com/library/mt774593.aspx) (zawiera odwołania do studiów przypadku klienta i informacje, aby rozpocząć)
- [Dokumentacja dla oltp w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrydowe przetwarzanie transakcyjne/analityczne (HTAP), znane również jako [analiza operacyjna w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>Oltp w pamięci

Technologia OLTP w pamięci zapewnia niezwykle szybkie operacje dostępu do danych, utrzymując wszystkie dane w pamięci. Używa również wyspecjalizowanych indeksów, kompilacji natywnej zapytań i dostępu do danych bez zatrzasku, aby zwiększyć wydajność obciążenia OLTP. Istnieją dwa sposoby organizowania danych OLTP w pamięci:

- **Zoptymalizowany pod kątem pamięci format magazynu wierszy,** w którym każdy wiersz jest oddzielnym obiektem pamięci. Jest to klasyczny format OLTP w pamięci zoptymalizowany pod kątem wysokowydajnych obciążeń OLTP. Istnieją dwa typy tabel zoptymalizowanych pod kątem pamięci, które mogą być używane w formacie magazynu wierszy zoptymalizowanym pod kątem pamięci:
  - *Trwałe tabele* (SCHEMA_AND_DATA), w których wiersze umieszczone w pamięci są zachowywane po ponownym uruchomieniu serwera. Ten typ tabel zachowuje się jak tradycyjna tabela rowstore z dodatkowymi zaletami optymalizacji w pamięci.
  - *Nietrwałe tabele* (SCHEMA_ONLY), w których wiersze nie są zachowywane po ponownym uruchomieniu. Ten typ tabeli jest przeznaczony dla danych tymczasowych (na przykład zastąpienie tabel tymczasowych) lub tabel, w których należy szybko załadować dane przed przeniesieniem ich do niektórych utrwalonych tabel (tzw. tabele przejściowe).
- Zoptymalizowany pod kątem pamięci format **magazynu kolumn,** w którym dane są zorganizowane w formacie kolumnowym. Ta struktura jest przeznaczona dla scenariuszy HTAP, w których należy uruchamiać zapytania analityczne w tej samej strukturze danych, w której jest uruchomione obciążenie OLTP.

> [!Note]
> Technologia OLTP w pamięci jest przeznaczona dla struktur danych, które mogą w pełni przebywać w pamięci. Ponieważ danych w pamięci nie można odciążyć na dysku, upewnij się, że używasz bazy danych, która ma wystarczającą ilość pamięci. Zobacz [Rozmiar danych i limit magazynu dla oltp w pamięci,](#data-size-and-storage-cap-for-in-memory-oltp) aby uzyskać więcej informacji.

Szybki podkład na OLTP w pamięci: [Szybki start 1: W pamięci OLTP Technologie dla szybszej wydajności T-SQL](https://msdn.microsoft.com/library/mt694156.aspx) (inny artykuł, który pomoże Ci zacząć)

Szczegółowe filmy o technologiach:

- [Oltp w pamięci w bazie danych SQL Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (który zawiera pokaz korzyści wydajności i kroki, aby odtworzyć te wyniki samodzielnie)
- [Filmy OLTP w pamięci: Co to jest i kiedy / jak go używać](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Istnieje programowy sposób, aby zrozumieć, czy dana baza danych obsługuje oltp w pamięci. Można wykonać następującą kwerendę Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Jeśli kwerenda zwraca **1**, W pamięci OLTP jest obsługiwany w tej bazie danych. Następujące kwerendy identyfikują wszystkie obiekty, które muszą zostać usunięte, zanim baza danych może zostać obniżona do standardu/podstawowego:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Rozmiar danych i limit pamięci dla oltp w pamięci

Oltp w pamięci zawiera tabele zoptymalizowane pod kątem pamięci, które są używane do przechowywania danych użytkownika. Te tabele są wymagane, aby zmieścić się w pamięci. Ponieważ zarządzasz pamięcią bezpośrednio w usłudze bazy danych SQL, mamy pojęcie przydziału danych użytkownika. Ten pomysł jest określany jako *przechowywanie OLTP w pamięci.*

Każda obsługiwana warstwa cenowa pojedynczej bazy danych i każda warstwa cenowa puli elastycznej zawiera określoną ilość magazynu OLTP w pamięci. Zobacz [limity zasobów oparte na jednostce DTU — pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparte na jednostce DTU — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów oparte na ramach wersji vCore — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów oparte na parcie wirtualne — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Następujące elementy są wliczane do limitu magazynu OLTP w pamięci:

- Aktywne wiersze danych użytkownika w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabeli. Należy zauważyć, że stare wersje wierszy nie są wliczane do limitu.
- Indeksy w tabelach zoptymalizowanych pod kątem pamięci.
- Obciążenie operacyjne operacji ALTER TABLE.

Jeśli zostanie na to wpis, zostanie wyświetlony błąd braku przydziału i nie można już wstawiać ani aktualizować danych. Aby ograniczyć ten błąd, usuń dane lub zwiększ warstwę cenową bazy danych lub puli.

Aby uzyskać szczegółowe informacje na temat monitorowania wykorzystania pamięci OLTP w pamięci i konfigurowania alertów po prawie naciśnięciu limitu, zobacz [Monitorowanie pamięci masowej w pamięci](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Informacje o basenach elastycznych

W pulach elastycznych magazyn OLTP w pamięci jest współużytkowane przez wszystkie bazy danych w puli. W związku z tym użycie w jednej bazie danych może potencjalnie wpłynąć na inne bazy danych. Dwa środki zaradcze w tym celu to:

- Skonfiguruj `Max-eDTU` lub `MaxvCore` dla baz danych, która jest niższa niż liczba eDTU lub vCore dla puli jako całości. Ta maksymalna limity wykorzystania magazynu OLTP w pamięci w dowolnej bazie danych w puli do rozmiaru odpowiadającego liczbie eDTU.
- Skonfiguruj `Min-eDTU` lub `MinvCore` jest większa niż 0. To minimum gwarantuje, że każda baza danych w puli ma ilość dostępnego magazynu `Min-eDTU` `vCore`OLTP w pamięci, który odpowiada skonfigurowanej lub .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Zmienianie warstw usług baz danych korzystających z technologii OLTP w pamięci

Zawsze można uaktualnić bazy danych lub wystąpienia do wyższej warstwy, takich jak ogólnego przeznaczenia do krytycznych dla firmy (lub standard do premium). Dostępne funkcje i zasoby tylko zwiększyć.

Ale obniżenie poziomu może negatywnie wpłynąć na bazę danych. Wpływ jest szczególnie widoczne, gdy można obniżyć z business critical do ogólnego przeznaczenia (lub Premium do standardu lub podstawowego), gdy baza danych zawiera obiekty OLTP w pamięci. Tabele zoptymalizowane pod kątem pamięci są niedostępne po obniżeniu wersji (nawet jeśli pozostają widoczne). Te same zagadnienia mają zastosowanie podczas obniżania warstwy cenowej puli elastycznej lub przenoszenia bazy danych za pomocą technologii w pamięci do puli elastycznej Standardowej lub Podstawowej.

> [!Important]
> Oltp w pamięci nie jest obsługiwany w warstwie Ogólnego przeznaczenia, Standardowej lub Podstawowej. W związku z tym nie jest możliwe przeniesienie bazy danych, która ma żadnych obiektów OLTP w pamięci do warstwy Standardowa lub Podstawowa.

Przed przejściem na starszą wersję bazy danych do standardu/podstawowego usuń wszystkie tabele zoptymalizowane pod kątem pamięci i typy tabel, a także wszystkie natywnie skompilowane moduły T-SQL. 

*Skalowanie w dół zasobów w warstwie Krytyczne dla firmy:* Dane w tabelach zoptymalizowanych pod kątem pamięci muszą zmieścić się w magazynie OLTP w pamięci skojarzonym z warstwą bazy danych lub wystąpienia zarządzanego lub są dostępne w puli elastycznej. Jeśli spróbujesz skalować w dół warstwy lub przenieść bazę danych do puli, która nie ma wystarczającej ilości dostępnego magazynu OLTP w pamięci, operacja zakończy się niepowodzeniem.

## <a name="in-memory-columnstore"></a>Magazyn kolumn w pamięci

Technologia magazynu kolumn w pamięci umożliwia przechowywanie i wykonywanie zapytań o dużą ilość danych w tabelach. Technologia magazynu kolumn wykorzystuje format magazynu danych oparty na kolumnach i przetwarzanie zapytań wsadowych, aby uzyskać do 10-krotność wydajności zapytań w obciążeniach OLAP w porównaniu z tradycyjnym magazynem zorientowanym na wiersz. Można również osiągnąć zyski do 10 razy kompresji danych w rozmiarze danych nieskompresowanych.
Istnieją dwa typy modeli magazynu kolumn, których można używać do organizowania danych:

- **Klastrowany magazyn kolumn,** w którym wszystkie dane w tabeli są zorganizowane w formacie kolumnowym. W tym modelu wszystkie wiersze w tabeli są umieszczane w formacie kolumnowym, który bardzo kompresuje dane i umożliwia wykonywanie szybkich zapytań analitycznych i raportów w tabeli. W zależności od charakteru danych rozmiar danych może być zmniejszony 10x-100x. Model klastrowanego magazynu kolumn umożliwia również szybkie pozyskiwania dużej ilości danych (obciążenie zbiorcze), ponieważ duże partie danych większe niż 100K wierszy są kompresowane, zanim zostaną zapisane na dysku. Ten model jest dobrym wyborem dla klasycznych scenariuszy magazynu danych. 
- **Magazyn kolumn nieklastrowanych,** w którym dane są przechowywane w tradycyjnej tabeli magazynu wierszy i istnieje indeks w formacie magazynu kolumn, który jest używany dla zapytań analitycznych. Ten model umożliwia hybrydowe przetwarzanie transakcyjno-analityczne (HTAP): możliwość uruchamiania wydajnej analizy w czasie rzeczywistym na obciążeniu transakcyjnym. Zapytania OLTP są wykonywane w tabeli magazynu wierszy, która jest zoptymalizowana pod kątem uzyskiwania dostępu do małego zestawu wierszy, podczas gdy kwerendy OLAP są wykonywane w indeksie magazynu kolumn, który jest lepszym wyborem dla skanowania i analizy. Optymalizator zapytań bazy danych SQL azure dynamicznie wybiera format magazynu wierszy lub magazynu kolumn na podstawie kwerendy. Indeksy magazynu kolumn nie klastrowane nie zmniejszają rozmiaru danych, ponieważ oryginalny zestaw danych jest przechowywany w oryginalnej tabeli magazynu wierszy bez żadnych zmian. Jednak rozmiar indeksu magazynu kolumn dodatkowe powinny być w kolejności wielkości mniejsze niż równoważne indeksu drzewa B.

> [!Note]
> Technologia magazynu kolumn w pamięci przechowuje tylko dane, które są potrzebne do przetwarzania w pamięci, podczas gdy dane, które nie mogą zmieścić się w pamięci, są przechowywane na dysku. W związku z tym ilość danych w strukturach magazynu kolumn w pamięci może przekroczyć ilość dostępnej pamięci. 

Szczegółowy film o tej technologii:

- [Indeks magazynu kolumn: Filmy z analizy w pamięci z Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Rozmiar i magazyn danych dla indeksów magazynu kolumn

Indeksy magazynu kolumn nie są wymagane, aby zmieścić się w pamięci. W związku z tym jedynym ograniczeniem rozmiaru indeksów jest maksymalny całkowity rozmiar bazy danych, który jest udokumentowany w [modelach zakupu opartych na UTU](sql-database-service-tiers-dtu.md) i artykułach [modelu zakupów opartych na wynikach wirtualnych.](sql-database-service-tiers-vcore.md)

W przypadku używania indeksów magazynu kolumn klastrowanych kompresja kolumnowa jest używana do przechowywania tabeli bazowej. Ta kompresja może znacznie zmniejszyć rozmiar magazynu danych użytkownika, co oznacza, że można zmieścić więcej danych w bazie danych. A kompresję można dodatkowo zwiększyć za pomocą [kolumny kompresji archiwizacji](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Ilość kompresji, którą można osiągnąć, zależy od charakteru danych, ale 10-krotność kompresji nie jest rzadkością.

Na przykład jeśli masz bazę danych o maksymalnym rozmiarze 1 terabajta (TB) i osiągnąć 10 razy kompresji przy użyciu indeksów magazynu kolumn, można zmieścić łącznie 10 TB danych użytkownika w bazie danych.

W przypadku korzystania z indeksów magazynu kolumn nieklastrowanych tabela bazowa jest nadal przechowywana w tradycyjnym formacie magazynu wierszy. W związku z tym oszczędności magazynu nie są tak duże, jak w indeksach magazynu kolumn klastrowanych. Jednak jeśli zastępujesz wiele tradycyjnych indeksów nieklastrowanych indeksami z indeksem magazynu pojedynczego kolumn, nadal możesz zobaczyć ogólne oszczędności w footprintu magazynu dla tabeli.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Zmienianie warstw usług baz danych zawierających indeksy magazynu kolumn

*Obniżenie pojedynczej bazy danych do podstawowej lub standardowej* może nie być możliwe, jeśli warstwa docelowa jest niższa niż S3. Indeksy magazynu kolumn są obsługiwane tylko w warstwie cenowej Krytyczne/Premium firmy oraz w warstwie Standardowa, S3 i powyżej, a nie w warstwie Podstawowa. Po obniżeniu bazy danych do nieobsługiconej warstwy lub poziomu, indeks magazynu kolumn staje się niedostępny. System utrzymuje indeks magazynu kolumn, ale nigdy nie wykorzystuje indeksu. Jeśli później uaktualnić z powrotem do obsługiwanej warstwy lub poziomu, indeks magazynu kolumn jest natychmiast gotowy do ponownego wykorzystania.

Jeśli masz **indeks klastrowanego** magazynu kolumn, cała tabela staje się niedostępna po obniżeniu wersji. W związku z tym zaleca się upuścić wszystkie *indeksy klastrowanego* magazynu kolumn przed obniżeniem bazy danych do nieobsługiconej warstwy lub poziomu.

> [!Note]
> Wystąpienie zarządzane obsługuje indeksy ColumnStore we wszystkich warstwach.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Następne kroki

- [Szybki start 1: Technologie OLTP w pamięci dla szybszej wydajności T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Używanie protokołu OLTP w pamięci w istniejącej aplikacji SQL platformy Azure](sql-database-in-memory-oltp-migration.md)
- [Monitorowanie pamięci OLTP w pamięci](sql-database-in-memory-oltp-monitoring.md) dla oltp w pamięci
- [Wypróbuj funkcje w pamięci w bazie danych SQL usługi Azure](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

### <a name="deeper-information"></a>Głębsze informacje

- [Dowiedz się, jak kworum podwaja obciążenie bazy danych kluczy przy jednoczesnym obniżeniu jednostki DTU o 70% dzięki oltpowi w pamięci w bazie danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Wpis w blogu OTP w pamięci w programie Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Dowiedz się więcej o oltp w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Dowiedz się więcej o indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- [Dowiedz się więcej o analizie operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)
- Zobacz [typowe wzorce obciążeń i zagadnienia dotyczące migracji](https://msdn.microsoft.com/library/dn673538.aspx) (w którym opisano wzorce obciążeń, w których oltp w pamięci często zapewnia znaczny wzrost wydajności)

### <a name="application-design"></a>Projekt aplikacji

- [Oltp w pamięci (optymalizacja w pamięci)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Używanie protokołu OLTP w pamięci w istniejącej aplikacji SQL platformy Azure](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Narzędzia

- [Portal Azure](https://portal.azure.com/)
- [Sql Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
