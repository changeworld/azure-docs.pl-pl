---
title: Azure SQL Database zakupu modeli | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zakupu modelu bazy danych SQL Azure.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: dda37600dffaf1e8e1c85c9757696f2d8219febb
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751674"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Azure SQL Database zakupu modeli i zasoby 

Logiczne serwerów w [bazy danych SQL Azure](sql-database-technical-overview.md) oferuje dwa modele zakupów dla zasobów obliczeniowych, magazynu i zasobów we/wy: model kupna DTU i na podstawie vCore model kupna (wersja zapoznawcza). 

> [!NOTE]
> [Zarządzane wystąpienia](sql-database-managed-instance.md) w bazie danych SQL Azure tylko oferują model kupna vCore.

Następujących tabel i wykresów porównania i kontrastu te dwa modele zakupów.

> [!IMPORTANT]
> Na podstawie vCore model kupna (wersja zapoznawcza), zobacz [na podstawie vCore model kupna](sql-database-service-tiers-vcore.md)

|**Model kupna**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Model oparty na jednostkach DTU|Ten model jest oparty na powiązane miary obliczeniowej, magazynu i zasobów we/wy. Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu a Edtu, zobacz [co to są Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|Najlepsze dla klientów, którzy zasobów proste, wstępnie skonfigurowane opcje.| 
|Model oparty na rdzeniach wirtualnych|Ten model umożliwia niezależnie skalowanie zasobów obliczeniowych i magazynu. Umożliwia on również używać korzyści hybrydowe platformy Azure dla programu SQL Server w celu uzyskania oszczędności.|Najlepsze dla klientów, którzy wartość elastyczność, kontroli i przejrzystości.|
||||  

![model cenowy](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>na podstawie vCore model kupna (wersja zapoznawcza)

Wirtualne podstawowe reprezentuje Procesora logicznego oferowany z opcją wybór między generacje sprzętu. Na podstawie vCore model kupna (wersja zapoznawcza) zapewnia elastyczność, przezroczystość zużycia pojedynczego zasobu, formantowi i łatwe do tłumaczenia lokalne wymagania obciążenia w chmurze. Ten model umożliwia skalowanie możliwości obliczeniowych, pamięci oraz Magazyn ustalane na podstawie jego potrzeb obciążenia. W oparciu vCore model kupna (wersja zapoznawcza), klienci można wybrać ogólnego przeznaczenia i warstwy krytycznych usług biznesowych (wersja zapoznawcza) dla obu [baz danych z jednym](sql-database-single-database-scale.md) i [pule elastyczne](sql-database-elastic-pool.md). 

HE na podstawie vCore model kupna (wersja zapoznawcza) umożliwia niezależnie skalowania zasobów obliczeniowych i magazynu, zgodne lokalnymi wydajności i zoptymalizowania cen. Jeśli bazy danych lub puli elastycznej zużywa ponad 300 Konwersja jednostek dtu w warstwie vCore może zmniejszyć koszt. Możesz przekonwertować przy użyciu interfejsu API wyboru lub przy użyciu portalu Azure, bez przestojów. Jednak konwersja nie jest wymagana. Jeśli model kupna jednostek dtu w warstwie spełnia wymagania biznesowe i wydajności, na których, można nadal używać. Jeśli zdecydujesz się do konwersji z modelu jednostek dtu w warstwie vCore modelu, należy wybrać poziom wydajności przy użyciu następujących zasadą: każdego 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 vCore w warstwie ogólnego przeznaczenia; Każdy 125 jednostek DTU w warstwie Premium wymaga co najmniej 1 vCore w warstwie Business krytyczne.

Na podstawie vCore zakupów modelu (wersja zapoznawcza) klienci płacić za:
- Obliczeniowe (warstwy usług + liczba vCores + generowania sprzętu) *
- Typ i ilość miejsca w magazynie danych i dziennika 
- Liczba operacji We-Wy **
- Kopia zapasowa magazynu (RA-GRS) ** 

\* W początkowej publicznej wersji zapoznawczej procesorów logicznych 4 Gen są oparte na Intel E5-2673 v3 procesorów 2,4 GHz (Haswell).

\*\* Podczas udostępniania wersji zapoznawczej mogą 7 dni i IOs.

> [!IMPORTANT]
> Obliczeniowe, IOs, danych i przechowywanie dziennika są naliczane na bazę danych lub puli elastycznej. Magazyn kopii zapasowych jest pobierana dla każdej bazy danych. Szczegółowe opłaty za wystąpienia zarządzane znaleźć [wystąpienia zarządzane bazy danych SQL Azure](sql-database-managed-instance.md).
> **Ograniczenia regionu:** na podstawie vCore model kupna (wersja zapoznawcza) nie jest jeszcze dostępna w następujących regionach: Europa Zachodnia, Francja centralnej, Wielka Brytania Południowa, Wielka Brytania Zachodnia i Australia południowo-wschodnia.

## <a name="dtu-based-purchasing-model"></a>Model kupna na podstawie jednostek dtu w warstwie

Jednostki przepływności bazy danych (DTU) reprezentuje mieszanych pomiarach procesora CPU, pamięci, odczytuje i zapisuje. Model kupna jednostek dtu w warstwie oferuje zestaw wstępnie skonfigurowane pakietów zasobów obliczeniowych i uwzględniony magazynu na dysku różne poziomy wydajności aplikacji. Klienci, którzy preferowane prostotę wstępnie skonfigurowane pakietu i płatności w stałej kwocie każdego miesiąca, może się okazać modelu na podstawie jednostek dtu w warstwie bardziej odpowiednie do potrzeb. W podstawie jednostek dtu w warstwie model kupna, klienci mogą wybrać między **podstawowe**, **standardowe**, i **Premium** warstw usług dla obu [pojedynczybazdanych](sql-database-single-database-scale.md) i [pule elastyczne](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Co to są jednostki transakcji bazy danych (Dtu)?
Dla jednej bazy danych Azure SQL w konkretnego poziomu wydajności w ramach [warstwy usług](sql-database-single-database-scale.md), Microsoft gwarantuje pewnego poziomu zasobów dla tej bazy danych (niezależnie od innych bazy danych w chmurze Azure), zapewniając przewidywalna poziom wydajności. Ilość zasobów jest obliczany jako liczba jednostek transakcji bazy danych lub Dtu i powiązane miary obliczeniowej, magazynu i zasobów we/wy. Stosunek między tymi zasobami pierwotnie został określony poprzez [obciążenia OLTP testu porównawczego](sql-database-benchmark-overview.md), zaprojektowany jako typowe dla obciążeń OLTP rzeczywistych. Gdy obciążenie przekracza ilość żadnego z tych zasobów, przepustowość sieci jest ograniczeniem przepustowości — wynikowe w niska wydajność i przekroczeń limitu czasu. Zasoby używane przez obciążenie będzie mieć wpływu na zasoby dostępne dla innych baz danych w chmurze Azure oraz zasoby używane przez inne obciążenia nie wpływają na zasoby dostępne dla Twojej bazy danych SQL.

![obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Liczba jednostek Dtu są najbardziej przydatny w przypadku zrozumieć względną ilość zasobów między bazami danych SQL Azure na różne poziomy wydajności i warstwy usług. Na przykład podwajając jednostek Dtu po podniesieniu poziomu wydajności bazy danych jest równa podwojenie zestaw zasobów, które są dostępne dla tej bazy danych. Na przykład baza danych Premium P11 z 1750 jednostkami DTU zapewnia 350 razy więcej mocy obliczeniowej DTU niż podstawowa baza danych z 5 jednostkami DTU.  

Aby uzyskać lepszy wgląd w zużycie zasobów (bazy danych DTU), obciążenia, należy użyć [szczegółowe informacje o usłudze Azure SQL bazy danych zapytań wydajności](sql-database-query-performance.md) do:

- Identyfikowanie najważniejszych zapytań według liczby wykonywania-Procesor/czas trwania, który może potencjalnie dostroić zwiększonej wydajności. Na przykład kwerendy znacznym We/Wy mogą korzystać z użycia [techniki optymalizacji w pamięci](sql-database-in-memory.md) lepsze wykorzystanie dostępnej pamięci w niektórych warstwę i poziom wydajności usługi.
- Przechodzenie do szczegółów zapytania, Wyświetl tekst i historii wykorzystania zasobów.
- Dostosowywanie zaleceń, które zawierają akcje wykonywane przez wydajności dostępu [doradcy bazy danych SQL](sql-database-advisor.md).

Możesz zmienić [DTU warstwy usług](sql-database-service-tiers-dtu.md) w dowolnym momencie z minimalnym czasem przestojów w aplikacji (zwykle uśrednianie poniżej cztery sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu należy użyć puli elastycznej z określonej liczby jednostek Edtu, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do usługi SQL Database: jednostki DTU z jedną bazą danych według warstwy i poziomu.](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Co to są jednostek transakcji elastycznej bazy danych (Edtu)?
Raczej nie udostępniają dedykowany zestaw zasobów (Dtu), które może nie zawsze być wymagane dla bazy danych SQL, która jest zawsze dostępna, możesz umieścić baz danych do [puli elastycznej](sql-database-elastic-pool.md) na serwerze bazy danych SQL, który udostępnia puli zasobów między te bazy danych. Udostępnione zasoby w puli elastycznej są mierzone w elastycznych jednostkach transakcji bazy danych lub Edtu. Pule elastyczne zapewniają prosty ekonomiczne rozwiązanie do zarządzania cele wydajności dla wielu baz danych o różnymi i nieprzewidywalnych wzorców. Pula elastyczna gwarantuje, że zasoby nie mogą być używane przez jedną bazę danych w puli, podczas sprawdzeniu każdej bazy danych w puli, zawsze ma minimalna ilość niezbędne dostępnych zasobów. 

![Wprowadzenie do usługi SQL Database: jednostki eDTU według warstwy i poziomu](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Pulę otrzymuje przez liczbę jednostek Edtu ustalonej cenie. Poszczególne bazy danych w ramach puli elastycznej mają możliwość elastycznego skalowania automatycznego w skonfigurowanych granicach. Większe obciążenie bazy danych zajmie więcej jednostek Edtu do zapotrzebowania. Bazy danych w ramach obciążeń jaśniejszy zajmie mniej Edtu. Baz danych bez obciążenia będą korzystać z nie jednostek Edtu. Przez Inicjowanie obsługi zasobów dla całej puli, a nie na bazę danych, zadania zarządzania są uproszczone, zapewnienie przewidywalnej budżetu puli.

Dodatkowe jednostki eDTU można dodać do istniejącej puli bez przerwy w działaniu baz danych ani wpływu na bazy danych w puli. Podobnie jeśli dodatkowe jednostki eDTU nie są już potrzebne, można je usunąć z istniejącej puli w dowolnej chwili. Można dodać lub odjąć baz danych w puli lub limit ilości Edtu bazy danych mogą być duże obciążenie zarezerwować jednostek Edtu dla innych baz danych. Jeśli bazy danych jest jednoznacznie w obszarze wykorzystaniem zasobów, można wychodzenia puli i skonfigurować go jako pojedynczej bazy danych z przewidywalną ilość wymaganych zasobów.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak określić wymaganą liczbę jednostek DTU dla mojego obciążenia?
Jeśli chcesz zmigrować istniejące obciążenie lokalne lub obciążenie maszyny wirtualnej programu SQL Server do usługi Azure SQL Database, możesz skorzystać z [Kalkulatora jednostek DTU ](http://dtucalculator.azurewebsites.net/), aby obliczyć przybliżoną liczbę wymaganych jednostek DTU. Istniejące obciążenia pracą bazy danych SQL Azure, można użyć [Insight wydajności kwerendy bazy danych SQL](sql-database-query-performance.md) zrozumienie użycia zasobów bazy danych (Dtu), aby uzyskać bardziej szczegółowe informacje o optymalizacji obciążenie. Można również użyć [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV, aby wyświetlić zużycie zasobów w ciągu ostatniej godziny. Alternatywnie widoku wykazu [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) Wyświetla zużycia zasobów przez ostatnie 14 dni, ale także w niższych wierności średnich 5 minutową.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak sprawdzić, czy elastyczna pula zasobów jest dla mnie korzystnym rozwiązaniem?
Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Ten wzorzec określonej bazy danych, charakteryzuje się to średnia niskiego poziomu wykorzystania z wzrostów użycia stosunkowo rzadko. Usługa SQL Database automatycznie ocenia historyczne użycie zasobów przez bazy danych na istniejącym serwerze usługi SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co się stanie po naciśnięciu I Moje maksymalna liczba jednostek Dtu?
Poziomy wydajności są kalibrować i postanowieniom dostarczać zasoby potrzebne do uruchomienia obciążenie bazy danych do maksymalny dozwolony poziom warstwy wydajności wybranej usługi. Jeżeli obciążenie jest naciśnięcie jednego z ograniczeń we/wy procesora CPU/dane we/wy/Log, nadal będziesz otrzymywać maksymalny poziom dopuszczalny zasobów, ale również prawdopodobnie doświadczy opóźnienia zwiększona zapytania. Te limity nie powodują występowania błędów, ale raczej spowalniają działanie obciążenia, o ile obciążenie nie stanie się tak duże, by powodować przekroczenie limitu czasu zapytań. Jeśli osiągnąć maksymalną dozwoloną użytkownika równoczesnych sesji/żądań (wątków roboczych), zostanie wyświetlony jawne błędy. Zobacz [limity zasobów bazy danych SQL Azure]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) dla informacji na temat limitów zasobów nie są powiązane z Procesora, pamięci, we/wy danych lub dziennika transakcji we/wy.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelowanie wyniki testu, do rzeczywistych wydajności bazy danych
Jest ważne dowiedzieć się, że wszystkie testy są reprezentatywny i świadczy tylko. Wskaźników transakcji do testu wydajności aplikacji, nie będą takie same jak te, które mogą być osiągnięte z innymi aplikacjami. Testu porównawczego składa się z kolekcją innej transakcji, uruchomienia typów ze schematem zawierający zakres tabel i typy danych. Podczas testu porównawczego skorzysta z tego samego podstawowe operacje, które są wspólne dla wszystkich obciążeń OLTP, nie reprezentuje dowolnej klasy określonej bazy danych lub aplikacji. Celem testu porównawczego jest zapewnienie uzasadnione przewodnik dotyczący względną wydajność bazy danych, które można oczekiwać podczas skalowania w górę lub w dół od poziomów wydajności. W rzeczywistości bazy danych o różnych rozmiarach i złożoność, występują różne kombinacje obciążeń i będzie odpowiadać na różne sposoby. Na przykład aplikacji intensywnie wykonujących operacje We/Wy wcześniej napotkać progi we/wy lub aplikacji użycie Procesora CPU napotkać limitów CPU wcześniej. Nie ma żadnej gwarancji, który dowolnej określonej bazy danych są skalowane w taki sam sposób jak testu porównawczego zgodnie z rosnącym obciążeniem.

Testu porównawczego i jego metody są opisane bardziej szczegółowo poniżej.

### <a name="benchmark-summary"></a>Podsumowanie testu porównawczego
ASDB mierzy wydajność operacje podstawowej bazy danych, które występują najczęściej w transakcji online (OLTP) obciążeń przetwarzania. Mimo że testu porównawczego zaprojektowano z rozwiązań w uwadze schematu bazy danych, populacji danych w chmurze, a transakcje zostały zaprojektowane, aby być szeroko reprezentatywny podstawowych elementów, które są najczęściej używane w obciążeń OLTP.

### <a name="schema"></a>Schemat
Schemat jest przeznaczony do ma za mało różnych i złożoności obsługuje szeroką gamę operacji. W bazie danych, które składają się z sześciu tabel uruchomione testy. Tabele dzielą się na trzy kategorie: stałym rozmiarze, skalowanie i powiększania. Istnieją dwie tabele stałym rozmiarze; trzy tabele skalowania; i jedna tabela rosnącym. Tabele o stałym rozmiarze ma stałą liczbę wierszy. Tabele skalowania mają Kardynalność, jest proporcjonalny do wydajności bazy danych, ale nie zmieniają się podczas testu wydajności. Rozmiar tabeli rosnącym określono jak tabelę skalowania obciążenia początkowej, ale zmiany Kardynalność w trakcie działania testu porównawczego jako wiersze są wstawiane i usunąć.

Schemat zawiera mieszane typy danych, w tym liczbę całkowitą z zakresu alfanumeryczne, znak i daty/godziny. Schemat zawiera klucze podstawowe i pomocnicze, ale nie wszystkie klucze obce — to nie istnieją żadne ograniczenia integralności referencyjnej między tabelami.

Program generowania danych generuje dane początkowej bazy danych. Liczba całkowita i numeryczne dane są generowane z różnych strategii. W niektórych przypadkach wartości są dystrybuowane losowo w zakresie. W pozostałych przypadkach zestaw wartości jest losowo cieniowania do zapewni, że określone dystrybucji. Pola tekstowe są generowane z długą listę słów do tworzenia rzeczywistych wyglądającej danych.

Bazy danych jest ustalać w oparciu "współczynnik skali". Współczynnik skali (skrót rz) określa Kardynalność skalowanie i powiększania tabel. Zgodnie z poniższym opisem w sekcji Użytkownicy i Pacing, rozmiar bazy danych wielu użytkowników oraz maksymalną wydajność wszystkich skalowania proporcjonalnie do siebie.

### <a name="transactions"></a>Transakcje
Obciążenie składa się z dziewięciu typów transakcji, jak pokazano w poniższej tabeli. Każdą transakcję zaprojektowano w celu zaznacz określony zestaw właściwości bazy danych systemu i aparat sprzętu, wysoki kontrast przez inne transakcje. Takie podejście ułatwia ocenić wpływ różne składniki ogólną wydajność. Na przykład "Odczytu duże" transakcji tworzy duża liczba operacji odczytu z dysku.

| Typ transakcji | Opis |
| --- | --- |
| Odczyt Lite |WYBIERZ; w pamięci. tylko do odczytu |
| Średnia liczba godzin odczytu |WYBIERZ; przede wszystkim w pamięci; tylko do odczytu |
| Ciężki odczytu |WYBIERZ; przede wszystkim nie w pamięci; tylko do odczytu |
| Aktualizacja Lite |AKTUALIZACJA; w pamięci. Odczyt i zapis |
| Ciężki aktualizacji |AKTUALIZACJA; przede wszystkim nie w pamięci; Odczyt i zapis |
| Wstaw Lite |WSTAW; w pamięci. Odczyt i zapis |
| Wstaw ciężkie |WSTAW; przede wszystkim nie w pamięci; Odczyt i zapis |
| Usuwanie |USUŃ; mieszane w pamięci i nie w pamięci; Odczyt i zapis |
| Silnie procesor CPU |WYBIERZ; w pamięci. stosunkowo duże obciążenie procesora CPU; tylko do odczytu |

### <a name="workload-mix"></a>Mieszane obciążenia
Transakcje są wybierane losowo w ważoną dystrybucji o następujących mieszanego ogólnej. Mieszane ogólną ma stosunek odczytu/zapisu około 2:1.

| Typ transakcji | % mieszanego |
| --- | --- |
| Odczyt Lite |35 |
| Średnia liczba godzin odczytu |20 |
| Ciężki odczytu |5 |
| Aktualizacja Lite |20 |
| Ciężki aktualizacji |3 |
| Wstaw Lite |3 |
| Wstaw ciężkie |2 |
| Usuwanie |2 |
| Silnie procesor CPU |10 |

### <a name="users-and-pacing"></a>Użytkownicy i tempo
Obciążenie testu porównawczego wynikają z narzędzia, który przesyła transakcji w zestawie połączeń, aby symulować liczba równoczesnych użytkowników. Mimo że wszystkie połączenia i transakcji są generowane maszyny, dla uproszczenia nazywamy te połączenia "Użytkownicy". Mimo że każdy użytkownik działa niezależnie od innych użytkowników, wszyscy użytkownicy wykonania tego samego cyklu kroki opisane poniżej:

1. Należy ustanowić połączenie z bazą danych.
2. Powtórz sygnalizuje, aby zakończyć działanie:
   * Wybierz transakcję losowo (z rozkładu ważoną).
   * Wykonaj wybranej transakcji i pomiar czasu odpowiedzi.
   * Poczekaj, aż pacing opóźnienia.
3. Zamknij połączenie z bazą danych.
4. Zakończ.

Losowo wybrano pacing opóźnienie (w kroku 2, c), ale z dystrybucji nie ma to wartość średnia drugiej 1.0. W związku z tym każdy użytkownik może średnio wygenerować co najwyżej jednej transakcji na sekundę.

### <a name="scaling-rules"></a>Skalowanie reguły
Liczba użytkowników zależy od rozmiaru bazy danych (w jednostkach współczynnik skali). Brak jednego użytkownika, co pięć jednostek współczynnik skali. Ze względu na opóźnienie pacing jeden użytkownik może wygenerować co najwyżej jednej transakcji na sekundę, średnia.

Na przykład — współczynnik skali 500 (rz = 500) bazy danych będą mieli 100 użytkowników i może osiągnąć maksymalną szybkość 100 TPS. Do kierowania TPS wyższej szybkości wymaga więcej użytkowników i większą bazę danych.

W poniższej tabeli przedstawiono liczbę użytkowników, w rzeczywistości przez dłuższy czas dla każdej warstwy i poziom wydajności usługi.

| Warstwy usług (poziom wydajności) | Użytkownicy | Rozmiar bazy danych |
| --- | --- | --- |
| Podstawowa |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standardowa (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| — Warstwa Premium (P6.) |800 |114 GB |

### <a name="measurement-duration"></a>Czas trwania pomiaru.
Nieprawidłowa uruchomienia testu porównawczego wymaga pomiaru stabilnego obowiązywania co najmniej jedną godzinę.

### <a name="metrics"></a>Metryki
Kluczowe metryki w testu porównawczego jest przepływności i odpowiedzi.

* Przepływność to miara wydajności niezbędne w testu porównawczego. Przepływność jest zgłaszana w transakcji na jednostkę elementu czasu, inwentaryzacji wszystkich typów transakcji.
* Czas odpowiedzi to miara wydajności, przewidywalności. Ograniczenia czasu odpowiedzi zależy od rodzaju usług o wyższych klas usługi o wprowadzenie bardziej rygorystycznych wymagań czasu odpowiedzi, jak pokazano poniżej.

| Klasa usługi | Miara przepływności | Wymagania dotyczące czasu odpowiedzi |
| --- | --- | --- |
| Premium |Transakcje na sekundę |95. percentyl w sekundach 0,5 |
| Standardowa (Standard) |Transakcje na minutę |90-procentowy w sekundach 1.0 |
| Podstawowa |Transakcje na godzinę |80. percentyl w sekundach 2.0 |

## <a name="next-steps"></a>Kolejne kroki

- Na podstawie vCore model kupna (wersja zapoznawcza), zobacz [na podstawie vCore model kupna](sql-database-service-tiers-vcore.md)
- Na podstawie jednostek dtu w warstwie model kupna dla [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md).
