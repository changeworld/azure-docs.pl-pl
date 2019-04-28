---
title: Usługa Azure SQL Database w pamięci technologii | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database w pamięci technologii znacznie poprawić wydajności transakcyjnej oraz obciążeń wynikających z analizy.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/19/2019
ms.openlocfilehash: d2c852b48c219283bba2304a993dd26e802b3252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036511"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optymalizowanie wydajności przy użyciu technologii w pamięci w bazie danych SQL

Technologie w pamięci w usłudze Azure SQL Database pozwalają zwiększyć wydajność aplikacji i zmniejszyć koszt bazy danych. 

## <a name="when-to-use-in-memory-technologies"></a>Kiedy należy używać technologii w pamięci

Przy użyciu technologii w pamięci w usłudze Azure SQL Database, można osiągnąć ulepszenia wydajności z różnych obciążeniach:

- **Transakcyjne** (online przetwarzanie transakcyjne (OLTP)) gdzie większość żądań odczytać lub zaktualizować mniejszy zestaw danych (na przykład operacje CRUD).
- **Analityczne** (przetwarzania analitycznego online (OLAP)) gdzie większość zapytań ma złożonych obliczeń do raportowania do celów, z określoną liczbą zapytań, które obciążenia i dołączyć dane do istniejące tabele (tzw. ładowanie zbiorcze) lub usunięcie dane z tabel. 
- **Mieszane** (hybrydowe przetwarzanie analityczne/transakcji (HTAP)) gdzie OLTP i OLAP zapytania są wykonywane względem tego samego zestawu danych.

Technologiom pamięci może zwiększyć wydajność tych obciążeń, przechowując dane, które mają być przetwarzane do pamięci, za pomocą natywnej kompilacji zapytań lub zaawansowanego przetwarzania, takich jak usługi batch przetwarzania i instrukcje SIMD, które są dostępne na podstawowy sprzęt. 

## <a name="overview"></a>Omówienie

Usługa Azure SQL Database zawiera następujące technologie w pamięci:
- *[Przetwarzanie OLTP danych w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)*  zwiększa się liczba transakcji na sekundę i zmniejsza opóźnienia i przetwarzania transakcji. Scenariusze, które korzystają z OLTP w pamięci są: przetwarzania, takich jak handlowych i gier, pozyskiwania danych ze zdarzeń lub urządzenia IoT, buforowanie, ładowanie danych i tabeli tymczasowej i scenariuszy zmiennej tabeli transakcji o wysokiej przepływności.
- *Klastrowane indeksy magazynu kolumn* zmniejszyć zmniejszenie śladu magazynu (maksymalnie 10 razy) i zwiększyć wydajność raportowania i zapytań analiz. Umożliwia ona z tabel faktów w składnice danych Dopasuj większej ilości danych w bazie danych i zwiększyć wydajność. Ponadto możesz użyć jej przy użyciu danych historycznych w operacyjnej bazie danych do zarchiwizowania i jest możliwość wykonywania zapytań do 10 razy większej ilości danych.
- *Klastrowanych indeksów magazynu kolumn* HTAP ułatwia wgląd w czasie rzeczywistym w firmie za pomocą zapytań operacyjnej bazy danych bezpośrednio, bez konieczności uruchamiania do wyodrębniania kosztowne, przekształcanie i ładowanie (ETL) proces i poczekaj, aż Magazyn danych do wypełnienia. Klastrowanych indeksów magazynu kolumn umożliwiają szybkie wykonywanie zapytań analitycznych w bazie danych OLTP, przy jednoczesnym zmniejszeniu wymaganych wpływu na obciążenia operacyjnego.
- *Zoptymalizowane pod kątem pamięci klastrowane indeksy magazynu kolumn* dla HTAP umożliwia przetwarzanie transakcji szybko, a *jednocześnie* bardzo szybkie uruchamianie analitycznych zapytań na tych samych danych.

Indeksy magazynu kolumn i OLTP w pamięci zostały częścią produktu SQL Server od 2012 i 2014 r., odpowiednio. Usługa Azure SQL Database i programu SQL Server udostępnianie tego samego wdrożenia technologii w pamięci. Idąc dalej, nowe możliwości dla tych technologii są wydawane w usłudze Azure SQL Database, przed ich wydaniem w programie SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Korzystać z zalet technologii w pamięci

Ze względu na bardziej efektywne zapytań i przetwarzania transakcji w pamięci technologii również pomóc zmniejszyć koszt. Zazwyczaj nie trzeba uaktualnić z warstwy cenowej bazy danych, aby osiągnąć wzrost wydajności. W niektórych przypadkach może nawet być możliwe obniżenie warstwy cenowej, jednocześnie nadal się Wyświetla poprawa wydajności dzięki technologiom pamięci.

Poniżej przedstawiono dwa przykłady sposobu przetwarzania OLTP w pamięci brały udział w celu znacznego podniesienia wydajności:

- Za pomocą OLTP w pamięci [rozwiązań biznesowych kworum można było dwukrotnie obciążenia przy jednoczesnym zwiększeniu liczby jednostek Dtu o 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - Oznacza, że jednostka DTU *jednostek transakcji bazy danych*, i zawiera Pomiar zużycia zasobów.
- Poniższy film wideo przedstawia znacznej poprawy zużycia zasobów z obciążeniem próbki: [OLTP w pamięci w usługi Azure SQL Database wideo](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Aby uzyskać więcej informacji zobacz wpis w blogu: [OLTP w pamięci z wpisu w blogu bazy danych Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Technologie w pamięci są dostępne w bazach danych Azure SQL warstwy Premium i krytyczne dla działania firmy i pul elastycznych Premium.

Poniższy klip wideo wyjaśnia, potencjalne zwiększenie wydajności dzięki technologiom pamięci w usłudze Azure SQL Database. Należy pamiętać, że przyrost wydajności, które pojawi się zawsze zależy od wielu czynników, w tym o naturze obciążenia i dane, wzorzec dostępu do bazy danych i tak dalej.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

W tym artykule opisano aspekty indeksy OLTP w pamięci i magazynu kolumn, które są specyficzne dla usługi Azure SQL Database i zawiera także przykłady:

- Zobaczysz wpływ tych technologii na limity rozmiaru magazynu i danych.
- Pokazano, jak zarządzać przenoszenia baz danych, które przy użyciu tych technologii między różnych warstw cenowych.
- Zostaną wyświetlone dwa przykłady ilustrujące użycie OLTP w pamięci, a także indeksach magazynu kolumn w usłudze Azure SQL Database.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie OLTP w pamięci i scenariusze użycia](https://msdn.microsoft.com/library/mt774593.aspx) (zawiera odwołania do analizy przypadków klientów i informacji, aby rozpocząć)
- [Dokumentacja dla OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrydowe transakcyjnych/przetwarzanie analityczne (HTAP), nazywany także [analizy operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>Przetwarzanie OLTP danych w pamięci

Technologia OLTP w pamięci zapewnia bardzo krótkie czasy operacji dostępu przez przechowywanie wszystkich danych w pamięci. Korzysta również specjalistyczne indeksy, kompilacja kodu natywnego, zapytań i niekorzystającemu z zatrzaśnięć dostępu do danych zwiększa wydajność obciążeń OLTP. Istnieją dwa sposoby organizowania danych OLTP w pamięci:

- **Zoptymalizowane pod kątem pamięci magazynu wierszy** format, w którym każdy wiersz jest obiektem osobną pamięć. Jest klasycznego format OLTP w pamięci, zoptymalizowane pod kątem wysokiej wydajności obciążeń OLTP. Istnieją dwa typy tabel zoptymalizowanych pod kątem pamięci, które mogą być używane w formacie zoptymalizowane pod kątem pamięci magazynu wierszy:
  - *Trwałe tabel* (SCHEMA_AND_DATA) gdzie wierszy umieszczane w pamięci są zachowywane po ponownym uruchomieniu serwera. Tego rodzaju tabel zachowuje się jak tradycyjnego magazynu wierszy tabeli z dodatkowych zalet optymalizacje w pamięci.
  - *Tabele nietrwałe* (SCHEMA_ONLY) gdzie wiersze są zachowywane nie po ponownym uruchomieniu. Ten typ tabeli jest przeznaczona dla danych tymczasowych (np. wymiana tabele tymczasowe) lub tabele, których potrzebujesz, aby szybko ładowania danych, przed przejściem do niektórych utrwalonych tabeli (tzw. Tabele przemieszczania).
- **Zoptymalizowane pod kątem pamięci magazynu kolumn** format, w którym dane są organizowane w formacie kolumnowym. Ta struktura jest przeznaczona dla scenariuszy HTAP wymagających uruchamianie zapytań analitycznych na tę samą strukturę danych, gdzie jest uruchomione Twoje obciążenie OLTP.

> [!Note]
> Przetwarzanie OLTP danych w pamięci technologii jest przeznaczona dla struktur danych, które w pełni mogą znajdować się w pamięci. Ponieważ dane w pamięci nie może być przenoszona do dysku, upewnij się, że używasz bazy danych, który ma wystarczającą ilość pamięci. Zobacz [limit rozmiaru i magazynu danych dla OLTP w pamięci](#data-size-and-storage-cap-for-in-memory-oltp) Aby uzyskać więcej informacji.

Szybkie Elementarz na OLTP w pamięci: [Szybki Start 1: Technologie OLTP w pamięci szybciej języka T-SQL wydajności](https://msdn.microsoft.com/library/mt694156.aspx) (inny artykuł, aby pomóc Ci rozpocząć pracę)

Szczegółowe materiały wideo o technologii:

- [OLTP w pamięci w usłudze Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (który zawiera pokaz korzyści wydajności i kroki umożliwiające odtworzenie tych wyników, samodzielnie)
- [Przetwarzanie OLTP danych w pamięci wideo: Co to jest i gdy/jak z niej korzystać](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Brak programowy sposób, aby dowiedzieć się, czy określona baza danych obsługuje przetwarzanie OLTP danych w pamięci. Można wykonać następujące zapytanie Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Jeżeli zapytanie zwraca **1**, OLTP w pamięci jest obsługiwana w tej bazie danych. Następujące zapytania zidentyfikować wszystkie obiekty, które muszą zostać usunięte przed bazę danych można zmienić na podstawowe/standardowe:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limit rozmiaru i magazynu danych dla OLTP w pamięci

Przetwarzanie OLTP danych w pamięci zawiera tabele zoptymalizowane pod kątem pamięci, które są używane do przechowywania danych użytkownika. Te tabele są wymagane, aby zmieścić ją w pamięci. Ponieważ zarządzasz pamięci bezpośrednio w usłudze SQL Database, mamy koncepcji limit przydziału dla danych użytkownika. Ten pomysł nazywa się *pojemność magazynu OLTP w pamięci*.

Każdy obsługiwany pojedynczej bazy danych warstwa cenowa i poszczególnych pul elastycznych w warstwie cenowej obejmuje pewien pojemność magazynu OLTP w pamięci. Zobacz [limity zasobów oparty na jednostkach DTU — Pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md), [limity zasobów oparty na jednostkach DTU - pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md),[limity zasobów opartych na rdzeniach wirtualnych - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).

Następujące elementy są wliczane do dumy magazynu OLTP w pamięci:

- Aktywny użytkownik wierszy danych w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabel. Należy pamiętać, że starsze wersje wiersza nie są wliczane do limitu.
- Indeksów w tabelach zoptymalizowanych pod kątem pamięci.
- Nakłady operacyjne operacji ALTER TABLE.

Jeśli zostanie osiągnięty limit, otrzymasz komunikat o błędzie "limit przydziału" i nie jesteś już możliwość wstawiania lub aktualizacji danych. Aby uniknąć tego błędu, Usuń dane, lub zwiększ warstwy cenowej bazy danych lub puli.

Aby uzyskać szczegółowe informacje dotyczące monitorowania użycia magazynu OLTP w pamięci i konfigurowania alertów, gdy naciśniesz prawie zakończenie, zobacz [monitorowanie w pamięci, magazynu](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>O elastycznych pulach

Dzięki pulom elastycznym magazynu OLTP w pamięci jest współużytkowana przez wszystkie bazy danych w puli. W związku z tym użycie w jednej bazie danych może wpłynąć na innych baz danych. Są dwa środki zaradcze w tym:

- Konfigurowanie `Max-eDTU` lub `MaxvCore` dla baz danych, które jest mniejsza niż liczba jednostek eDTU lub generacji — rdzeń wirtualny dla pulę jako całość. Ta wartość maksymalna caps wykorzystanie magazynu OLTP w pamięci, w dowolnej bazie danych w puli, rozmiar, który odnosi się do liczby jednostek eDTU.
- Konfigurowanie `Min-eDTU` lub `MinvCore` jest większa niż 0. To minimum gwarantuje, że każda baza danych w puli ma ilość dostępnego magazynu OLTP w pamięci, która odnosi się do skonfigurowanych `Min-eDTU` lub `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Zmienianie warstw usług baz danych korzystających z technologii przetwarzania OLTP w pamięci

Można zawsze uaktualnić bazy danych lub wystąpienia do wyższego poziomu, takie jak z ogólnego przeznaczenia na krytyczne dla działania firmy (lub standardowa do warstwy Premium). Dostępne funkcje i zasoby tylko zwiększyć.

Ale obniżenie warstwy może niekorzystnie wpłynąć na bazie danych. Wpływ jest jasne, szczególnie w przypadku, gdy można obniżyć wersję usługi z krytyczne dla działania firmy na ogólnego przeznaczenia (lub Premium na Standard lub Basic) Jeśli baza danych zawiera obiekty OLTP w pamięci. Tabele zoptymalizowane pod kątem pamięci są niedostępne po obniżania (nawet jeśli pozostały widoczne). To samo odnosi się podczas obniżania warstwy cenowej puli elastycznej, lub przenoszenia bazy danych przy użyciu technologii w pamięci, Standard lub Basic puli elastycznej.

> [!Important]
> Przetwarzanie OLTP danych w pamięci nie jest obsługiwany w warstwie ogólnego przeznaczenia, Standard lub Basic. W związku z tym nie można przenieść bazę danych, który zawiera wszystkie obiekty OLTP w pamięci do warstwy Standard lub Basic.

Przed obniżanie poziomu bazy danych Basic/Standard, należy usunąć wszystkie tabele zoptymalizowane pod kątem pamięci i typy tabel, a także wszystkich natywnie kompilowane moduły języka T-SQL. 

*Skalowanie w dół zasobów w warstwie krytyczne dla działania firmy*: Dane w tabelach zoptymalizowanych pod kątem pamięci musi mieścić się w magazynie OLTP w pamięci, który jest skojarzony z warstwą bazy danych lub wystąpienia zarządzanego, lub jest dostępny w puli elastycznej. Jeśli spróbujesz dół warstwę lub przenieść bazę danych do puli, która nie ma wystarczająco dużo dostępnego magazynu OLTP w pamięci, operacja zakończy się niepowodzeniem.

## <a name="in-memory-columnstore"></a>Magazyn kolumn przechowywany w pamięci

Jest to włączenie do przechowywania i wykonywania zapytań względem dużych ilości danych w tabelach w pamięci technologii magazynu kolumn. Format magazynu danych oparta na kolumnie korzysta z technologii magazynu kolumn i przetwarzania zapytań usługi batch do osiągnięcia uzyskać maksymalnie 10 razy wydajności zapytań w obciążeń OLAP porównaniu z tradycyjnym zorientowanym na wiersze magazynem. Możesz również uzyskać zyski maksymalnie 10 razy kompresji danych za pośrednictwem rozmiar nieskompresowanych danych.
Istnieją dwa typy modeli magazynu kolumn, które umożliwiają organizowanie danych:

- **Klastrowany magazyn kolumn** gdzie wszystkie dane w tabeli jest zorganizowana w formacie kolumnowym. W tym modelu wszystkie wiersze w tabeli są umieszczane w tabelarycznego wysoce kompresuje dane i umożliwia wykonywanie szybki analityczny zapytań i raportów w tabeli. W zależności od charakteru danych, rozmiaru danych może być 10 obniżenie x-100 x. Klastrowany magazyn kolumn modelu umożliwia także szybkie pozyskiwanie dużych ilości danych (ładowanie zbiorcze) od dużych partii danych jest większa niż 100 tysięcy wierszy są kompresowane przed są przechowywane na dysku. Ten model jest dobrym wyborem dla scenariuszy magazynu klasycznego danych. 
- **Inne niż takiego** gdy dane są przechowywane w tabeli magazynu tradycyjnych wierszy, i ma indeksu w formacie magazynu kolumn, który jest używany dla zapytań analitycznych. Ten model umożliwia hybrydowego transakcyjna analitycznego przetwarzania (HTAP): możliwość uruchamiania wydajny mechanizm analityczny w czasie rzeczywistym na obciążeniach transakcyjnych. Przetwarzanie OLTP danych zapytania są wykonywane w tabeli magazynu wierszy, który jest zoptymalizowany do uzyskiwania dostępu do niewielkiego zestawu wierszy, podczas gdy OLAP zapytania są wykonywane względem indeksu magazynu kolumn, który jest lepszym rozwiązaniem skanowania i analizy. Azure Optymalizator zapytań bazy danych SQL wybiera dynamicznie format magazynu wierszy lub magazynu kolumn na podstawie zapytania. Indeksy klastrowane bez magazynu kolumn nie zmniejszyć rozmiar danych, ponieważ oryginalnego zestawu danych są przechowywane w oryginalnej tabeli magazynu wierszy bez zmian. Rozmiar indeksu magazynu kolumn dodatkowe należy jednak w o rząd wielkości mniejszy niż równoważna indeksu B-drzewa.

> [!Note]
> W pamięci technologii magazynu kolumn przechowuje dane potrzebne do przetwarzania w pamięci, gdy dane nie mieści się w pamięci są przechowywane na dysku. W związku z tym ilości danych w strukturach magazynu kolumn w pamięci może przekroczyć ilość dostępnej pamięci. 

Szczegółowe wideo na temat technologii:

- [Indeks magazynu kolumn: Analizowania w pamięci wideo z konferencji Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Rozmiar danych i magazynu dla indeksów magazynu kolumn

Indeksy magazynu kolumn nie są wymagane, aby zmieścić ją w pamięci. W związku z tym, tylko limit na rozmiar indeksy jest maksymalny całkowity rozmiar bazy danych, które opisano w [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) artykułów.

Gdy używasz klastrowane indeksy magazynu kolumn, kolumnowy kompresji jest używane do przechowywania tabeli podstawowej. Kompresja ta mogą znacznie zmniejszyć zużycie pamięci masowej dane użytkownika, co oznacza, że można umieścić więcej danych w bazie danych. I kompresji można go zwiększyć za pomocą [kolumnowych kompresji archiwizacji](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Stopień kompresji, który można osiągnąć zależy od charakteru danych, ale nie jest niczym niezwykłym 10 razy kompresji.

Na przykład jeśli masz bazę danych o maksymalnym rozmiarze 1 terabajt (TB) i osiągnąć 10 razy kompresji za pomocą indeksów magazynu kolumn, można umieścić w sumie 10 TB danych użytkownika w bazie danych.

Korzystając z klastrowanych indeksów magazynu kolumn tabeli podstawowej jest nadal przechowywane w formacie tradycyjnego magazynu wierszy. W związku z tym oszczędności pojemności magazynu nie są tak dużego jak za pomocą klastrowane indeksy magazynu kolumn. Jednak jeśli liczba indeksów nieklastrowanych tradycyjnych jest zamieniany na indeks magazynu kolumn w jednym, można wyświetlić ogólnej oszczędności zmniejszenie śladu magazynu dla tabeli.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Zmiana warstwy usług baz danych zawierających indeksy magazynu kolumn

*Starszą wersję pojedynczej bazy danych Basic lub Standard* może nie być możliwe w przypadku warstwę docelowy znajduje się poniżej S3. Indeksy magazynu kolumn są obsługiwane tylko w warstwie cenowej krytyczne biznesowych — wersja Premium i w warstwie standardowa S3 i powyżej, a nie w warstwie podstawowa. Obniżanie poziomu bazy danych do warstwy nieobsługiwana lub na poziomie indeksu magazynu kolumn staje się niedostępny. System przechowuje indeksu magazynu kolumn, ale nigdy nie wykorzystuje indeks. Jeśli później uaktualnić do obsługiwanej warstwie lub na poziomie indeksu magazynu kolumn jest natychmiast gotowy do można ponownie wykorzystać.

Jeśli masz **klastra** indeksu magazynu kolumn po obniżania niedostępny całej tabeli. W związku z tym firma Microsoft zaleca umieszczeniu wszystkich *klastra* indeksy magazynu kolumn, zanim obniżanie poziomu bazy danych do warstwy nieobsługiwana lub poziom.

> [!Note]
> Zarządzane wystąpienie obsługuje indeksów magazynu kolumn we wszystkich warstwach.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Kolejne kroki

- [Szybki Start 1: Technologie OLTP w pamięci, aby zwiększyć wydajność języka T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Użyj OLTP w pamięci w istniejącej aplikacji usługi Azure SQL](sql-database-in-memory-oltp-migration.md)
- [Pojemność magazynu OLTP w pamięci Monitor](sql-database-in-memory-oltp-monitoring.md) dla OLTP w pamięci
- [Wypróbuj funkcje w pamięci w usłudze Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

### <a name="deeper-information"></a>Bardziej szczegółowych informacji

- [Dowiedz się, jak kworum rozwiązanie quorum zwiększa dwukrotnie obciążenie klucza bazy danych przy zmniejszeniu liczby jednostek DTU o 70% z OLTP w pamięci w bazie danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [OLTP w pamięci z wpisu w blogu bazy danych Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Dowiedz się więcej o OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Dowiedz się więcej o indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- [Więcej informacji na temat analizy operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)
- Zobacz [typowych wzorców obciążenia i zagadnienia dotyczące migracji](https://msdn.microsoft.com/library/dn673538.aspx) (która opisuje wzorce obciążenia, w którym OLTP w pamięci zapewnia często znaczący wzrost wydajności)

### <a name="application-design"></a>Projekt aplikacji

- [(Optymalizacja w pamięci) OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Użyj OLTP w pamięci w istniejącej aplikacji usługi Azure SQL](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Narzędzia

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
