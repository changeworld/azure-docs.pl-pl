---
title: Usługa Azure SQL Database zakupu modeli | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o kupowaniu modelu usługi Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: f40fe3da6874d8656c9c0a0ddce9fed602cb25f9
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091919"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Usługa Azure SQL Database zakupu modeli i zasoby 

Serwerami logicznymi w [usługi Azure SQL Database](sql-database-technical-overview.md) oferuje dwa modele zakupu zasobów obliczeniowych, magazynu i zasoby we/wy: model zakupu jednostek DTU i modelu zakupu opartego na rdzeniach wirtualnych. 

> [!NOTE]
> [Wystąpienia zarządzane](sql-database-managed-instance.md) tylko usługi Azure SQL Database oferują modelu zakupu opartego na rdzeniach wirtualnych.

Następujących tabel i wykresów porównania i porównać te dwa modele zakupu.

> [!IMPORTANT]
> Dla modelu zakupu opartego na rdzeniach wirtualnych, zobacz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md)

|**Model zakupu**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Model oparty na jednostkach DTU|Ten model opiera się na powiązane miary obliczeniowe, magazynu i zasobów we/wy. Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu i Edtu, zobacz [co to są jednostki Dtu i Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|Najlepsze dla klientów chcących zasobów proste, wstępnie skonfigurowanych opcji.| 
|Model oparty na rdzeniach wirtualnych|Ten model umożliwia niezależne skalowanie zasobów obliczeniowych i przestrzeni dyskowej. Umożliwia on również używać korzyści użycia hybrydowego platformy Azure dla programu SQL Server w celu uzyskania oszczędności kosztów.|Najlepsze dla klientów, którzy wartości elastyczności, kontroli i przejrzystości.|
||||  

![model cen](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>model zakupu w oparciu o rdzeń wirtualny 

Rdzeń wirtualny reprezentuje logiczny Procesor CPU z opcją wyboru generacji sprzętu. Model zakupu opartego na rdzeniach wirtualnych zapewnia Twojej elastyczności, kontroli, przejrzystości użycia poszczególnych zasobów i prostą metodę tłumaczenia wymagań obciążenia w chmurze lokalnie. Ten model umożliwia skalowanie zasobów obliczeniowych, pamięci i magazynu, w zależności od ich potrzeb obciążenia. W opartych na rdzeniach wirtualnych model zakupu, klienci mogą wybierać między ogólnego przeznaczenia krytyczne warstwy usługi i Business dla obu [pojedyncze bazy danych](sql-database-single-database-scale.md) i [pul elastycznych](sql-database-elastic-pool.md). 

Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny. Jeśli bazy danych lub elastycznej puli zajmuje ponad 300 Konwersja jednostek DTU na rdzeniach wirtualnych może zmniejszyć koszt. Możesz również przekonwertować przy użyciu wybranego interfejsu API lub portalu Azure, bez przestojów. Jednak konwersja nie jest wymagana. Jeśli model zakupu jednostek DTU spełnia swoje wymagania biznesowe i wydajności, można nadal jej używać. Jeśli zdecydujesz się przekonwertować modelu rdzenia wirtualnego z modelu jednostek DTU, należy wybrać poziom wydajności, za pomocą następujące reguły akceptacji: każdy 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny w warstwie przeznaczenie ogólne; Każdy 125 jednostek DTU w warstwie Premium wymaga co najmniej 1 rdzeń wirtualny w warstwie krytyczne dla działania firmy.

Oparty na rdzeniach wirtualnych model zakupu klienci płacą za zasoby:
- Obliczenia (warstwy usług i liczba rdzeni wirtualnych i generowanie sprzętu) *
- Typ i ilość miejsca w magazynie danych i dziennika 
- Liczba operacji We-Wy **
- Kopii zapasowej magazynu (RA-GRS) ** 

\* W początkowej publicznej wersji zapoznawczej Gen 4 logiczne procesory CPU są oparte na Intel E5-2673 v3 (Haswell) 2,4 GHz procesorów.

\*\* W trakcie okresu zapoznawczego 7 dni dla kopii zapasowych oraz IOs są bezpłatne.

> [!IMPORTANT]
> Moc obliczeniowa, IOs, dane i Magazyn dzienników są naliczane zgodnie z bazy danych lub elastycznej puli. Magazyn kopii zapasowych jest rozliczane na każdej bazy danych. Aby uzyskać szczegółowe informacje o opłaty za wystąpienia zarządzanego, zapoznaj się [wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance.md).
> **Ograniczenia dotyczące regionów:** modelu zakupu opartego na rdzeniach wirtualnych nie jest jeszcze dostępna w następujących regionach: Europa Zachodnia, Francja środkowa, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo i Australia południowo-wschodnia.

## <a name="dtu-based-purchasing-model"></a>Model zakupu w oparciu o jednostki DTU

Jednostki przepływności bazy danych (DTU) reprezentuje mieszany pomiar procesora CPU, pamięci, odczytuje i zapisuje. Model zakupu opartego na jednostkach DTU oferuje zestaw wstępnie skonfigurowane pakiety zasobów obliczeniowych i magazynu dostosowane do różnych poziomów wydajności aplikacji w pakiecie. Klienci, którzy preferują prostotę wstępnie skonfigurowanego pakietu i płatności w stałej kwocie każdego miesiąca, może się okazać modelu opartego na jednostkach DTU bardziej odpowiednie do ich potrzeb. W oparty na jednostkach DTU model zakupu, klienci mogą wybierać między **podstawowe**, **standardowa**, i **Premium** warstwy usług dla obu [pojedyncze bazy danych](sql-database-single-database-scale.md) i [pul elastycznych](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Co to są jednostki transakcji bazy danych (Dtu)?
Dla pojedynczej bazy danych Azure SQL na określonym poziomie wydajności w ramach [warstwy usług](sql-database-single-database-scale.md), firma Microsoft gwarantuje więc pewnego poziomu zasobów dla tej bazy danych (niezależnie od innej bazy danych w chmurze platformy Azure), zapewniając poziom przewidywalnej wydajności. Ilość zasobów jest obliczany jako liczba jednostek transakcji bazy danych lub liczby jednostek Dtu i jest miarą powiązane obliczeń, magazynu i zasobów we/wy. Współczynnik obejmujący te zasoby był pierwotnie określany za [obciążenia porównawczego OLTP](sql-database-benchmark-overview.md), jest przeznaczona do typowym, rzeczywistym obciążeniom OLTP. Gdy obciążenie przekracza ilość dowolnego z następujących zasobów, przepływność jest ograniczone — wynikowy niska wydajność i przekroczenia limitu czasu. Zasoby używane przez obciążenie nie miało wpływu na zasoby dostępne dla innych baz danych SQL w chmurze platformy Azure, a zasoby używane przez inne obciążenia nie miało wpływu na zasoby dostępne dla usługi SQL database.

![obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Liczba jednostek Dtu są najbardziej przydatne do zrozumienia względna ilość zasobów między warstwy usługi i baz danych SQL Azure na różne poziomy wydajności. Na przykład podwojenie liczby jednostek Dtu przez zwiększenie poziomu wydajności bazy danych odpowiada dwukrotnemu zwiększeniu zestawu zasobów dostępnych dla tej bazy danych. Na przykład baza danych Premium P11 z 1750 jednostkami DTU zapewnia 350 razy więcej mocy obliczeniowej DTU niż podstawowa baza danych z 5 jednostkami DTU.  

Aby uzyskać lepszy wgląd w użycie zasobów (DTU), obciążenia, należy użyć [usługi Azure SQL Database Query Performance Insight](sql-database-query-performance.md) do:

- Zidentyfikuj najpopularniejsze zapytania według liczby Procesor/czas trwania/wykonywania, które potencjalnie mogą być dostosowane w celu zwiększenia wydajności. Na przykład, zapytanie intensywnie korzystających z operacji We/Wy organizowanych przez użycie [techniki optymalizacji w pamięci](sql-database-in-memory.md) skuteczniej wykorzystać ilość dostępnej pamięci w niektórych usług warstwy i poziomu wydajności.
- Przechodzenie do szczegółów kwerendy, wyświetlanie historii wykorzystania zasobów i tekstem.
- Zalecenia dotyczące, które pokazują akcje wykonywane przez dostrajania wydajności dostępu [SQL Database Advisor](sql-database-advisor.md).

Możesz zmienić [warstwy usługi jednostki DTU](sql-database-service-tiers-dtu.md) w dowolnym momencie przy minimalnych przestojach do Twojej aplikacji (zwykle poniżej czterech sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu należy użyć puli elastycznej z określoną liczbą jednostek Edtu, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do usługi SQL Database: jednostki DTU z jedną bazą danych według warstwy i poziomu.](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Co to są jednostki transakcji elastic Database (Edtu)?
Raczej niż zapewniają dedykowany zestaw zasobów (Dtu), które może nie zawsze być wymagane dla usługi SQL Database, która jest zawsze dostępna, można umieścić bazy danych [puli elastycznej](sql-database-elastic-pool.md) na serwerze bazy danych SQL, który udostępnia pulę zasobów między te bazy danych. Współdzielone zasoby w puli elastycznej są mierzone w elastycznych jednostkach transakcji bazy danych lub Edtu. Pule elastyczne zapewniają proste i ekonomiczne rozwiązanie umożliwiające zarządzanie celami wydajności dla wielu baz danych o znacznie zróżnicowanych i nieprzewidywalnych wzorcach użycia. Pula elastyczna gwarantuje, że zasoby nie mogą być używane przez jedną bazę danych w puli, gdy zawsze zapewnienie każdej bazy danych w puli ma minimalną ilość wymaganych zasobów dostępnych. 

![Wprowadzenie do usługi SQL Database: jednostki eDTU według warstwy i poziomu](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Puli jest przydzielana określona liczba jednostek Edtu za określoną cenę. Poszczególne bazy danych w ramach puli elastycznej mają możliwość elastycznego skalowania automatycznego w skonfigurowanych granicach. Bazy danych przy większych obciążeniach zajmie więcej jednostek Edtu w celu spełnienia określonych wymagań. Bazy danych pod obciążeniem jaśniejszy zajmie mniej jednostek Edtu. Bazy danych bez obciążenia będą nie zużywają jednostek Edtu. Aprowizowanie zasobów dla całej puli, zamiast na bazę danych, upraszcza zadania zarządzania, zapewniając przewidywalny budżet dla puli.

Dodatkowe jednostki eDTU można dodać do istniejącej puli bez przerwy w działaniu baz danych ani wpływu na bazy danych w puli. Podobnie jeśli dodatkowe jednostki eDTU nie są już potrzebne, można je usunąć z istniejącej puli w dowolnej chwili. Możesz dodawać lub odejmować baz danych do puli lub ograniczyć liczbę jednostek Edtu bazy danych można używać pod dużym obciążeniem, aby zarezerwować je dla innych baz danych. Jeśli bazy danych jest przewidywalnie niewystarczająco wykorzystuje zasoby, możesz przenieść ją z puli i skonfigurować go jako pojedynczą bazę danych z wymaganą ilością wymaganych zasobów.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak określić wymaganą liczbę jednostek DTU dla mojego obciążenia?
Jeśli chcesz zmigrować istniejące obciążenie lokalne lub obciążenie maszyny wirtualnej programu SQL Server do usługi Azure SQL Database, możesz skorzystać z [Kalkulatora jednostek DTU ](http://dtucalculator.azurewebsites.net/), aby obliczyć przybliżoną liczbę wymaganych jednostek DTU. W przypadku istniejących obciążeń usługi Azure SQL Database, możesz użyć [SQL Database Query Performance Insight](sql-database-query-performance.md) Aby poznać użycie zasobów bazy danych (Dtu), aby uzyskać lepszy wgląd w celu optymalizacji działalności obciążenie. Można również użyć [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV, aby wyświetlić zużycie zasobów w ciągu ostatniej godziny. Alternatywnie widoku wykazu [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) przedstawia użycie zasobów w ciągu ostatnich 14 dni, ale o mniejszej dokładności pięciu minut.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak sprawdzić, czy elastyczna pula zasobów jest dla mnie korzystnym rozwiązaniem?
Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się średnio o niskim zapotrzebowaniu, przy użyciu stosunkowo rzadkimi okresami zwiększonego. Usługa SQL Database automatycznie ocenia historyczne użycie zasobów przez bazy danych na istniejącym serwerze usługi SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co się stanie po osiągnięciu limitu jednostek Dtu??
Poziomy wydajności są kalibrowane i celu zapewnienia zasobów wymaganych do uruchomienia obciążenia baz danych maksymalnie dozwoloną dla Twojego poziomu warstwy wydajności wybranej usługi. Jeśli obciążenie powoduje osiągnięcie jednego z limitów Procesora/danych we/wy/Wy dziennika, nadal będą podlegały maksymalny poziom dopuszczalny rozmiar zasobów, ale także prawdopodobnie doświadczy opóźnienia zwiększone zapytania. Te limity nie powodują występowania błędów, ale raczej spowalniają działanie obciążenia, o ile obciążenie nie stanie się tak duże, by powodować przekroczenie limitu czasu zapytań. W przypadku osiągnięcia maksymalnej dozwolonych równoczesnych sesji/żądań użytkowników (wątków roboczych) będzie występują jawne błędy. Zobacz [limity zasobów usługi Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) uzyskać informacji na temat limitów zasobów, które nie są związane z procesora CPU, pamięci, we/wy danych lub dziennika transakcji we/wy.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelowanie wyników testów porównawczych wydajności bazy danych rzeczywistych
Jest ważne dowiedzieć się, że wszystkie testy porównawcze są reprezentatywne ale wskazuje tylko. Stawki za transakcje osiągane przy użyciu aplikacji testu porównawczego nie będzie taka sama, jak te, które mogą być osiągnięte z innymi aplikacjami. Testu porównawczego składa się z kolekcją innej transakcji, które typy uruchamiać schemat zawierający szeroką gamę tabele i typy danych. Podczas testu porównawczego skorzysta z tego samego podstawowe operacje, które są wspólne dla wszystkich obciążeń OLTP, nie reprezentuje dowolnej klasy określonej bazy danych lub aplikacji. Cel testu porównawczego jest zapewnienie uzasadnione przewodnik względnej wydajności można oczekiwać podczas skalowania w górę lub w dół od poziomów wydajności bazy danych. W rzeczywistości bazy danych o różnych rozmiarach i złożoności, występują różne kombinacje obciążeń i będzie odpowiadać na różne sposoby. Na przykład aplikacji intensywnie korzystających z operacji We/Wy może wystąpić wcześniej progów we/wy lub aplikacji intensywnie korzystających z procesora CPU mogą występować limity procesora CPU, wcześniej. Nie ma żadnej gwarancji, które dowolnej określonej bazy danych będzie skalowane w taki sam sposób jak testów porównawczych, zgodnie z rosnącym obciążeniem.

Testu wydajności i jego metody są opisane bardziej szczegółowo poniżej.

### <a name="benchmark-summary"></a>Podsumowanie testu porównawczego
ASDB mierzy wydajność różnych operacji podstawowej bazy danych, które występują najczęściej w obciążeń OLTP przetwarzania transakcji online. Mimo że testu porównawczego zaprojektowano z chmury obliczeniowej w uwadze, schemat bazy danych, wypełnianie danymi, a transakcje zostały zaprojektowane, aby być szeroko reprezentatywne podstawowych elementów, które są najczęściej używane w obciążeń OLTP.

### <a name="schema"></a>Schemat
Schemat jest przeznaczona do ma za mało różnorodność i złożoność do obsługi szerokiej gamy operacji. Testu porównawczego działa na bazie danych składające się z sześciu tabel. Tabele można podzielić na trzy kategorie: stałym rozmiarze, skalowanie i powiększania. Istnieją dwie tabele stałym rozmiarze; trzy tabele skalowania; i jedna tabela rosnącą. Tabele o stałym rozmiarze ma stałą liczbę wierszy. Skalowanie tabele mają Kardynalność, jest proporcjonalna do wydajności bazy danych, ale nie zmienia się podczas testów porównawczych. Rosnąca tabeli ma rozmiar tak, jak skalowanie tabeli na ładowania początkowego, ale zmiany Kardynalność w trakcie uruchamiania testu porównawczego wiersze są wstawiane i usunięte.

Schemat obejmuje różne typy danych, w tym liczba całkowita, numeryczne, znak i daty/godziny. Schemat zawiera klucze podstawowe i pomocnicze, ale nie ma żadnych kluczy obcych — oznacza to, że istnieją nie ograniczenia integralności referencyjnej między tabelami.

Program generowania danych generuje dane dla początkowej bazy danych. Liczba całkowita i numeryczne dane są generowane przy użyciu różnych strategii. W niektórych przypadkach wartości są rozprowadzone w losowo zakresu. W innych przypadkach zbiór wartości jest losowo cieniowania do zapewnienia utrzymania określonych dystrybucji. Pola tekstowe są generowane na podstawie długą listę wyrazów do tworzenia realistycznych danych wyglądających.

Baza danych ma rozmiar, oparte na "współczynnik skali". Współczynnik skali (skrót SF) określa Kardynalność skalowanie i rozwijaniu tabel. Zgodnie z poniższym opisem w sekcji Użytkownicy i Pacing, rozmiaru bazy danych, liczby użytkowników i maksymalną wydajność, wszystkie skalowanie proporcjonalnie do siebie nawzajem.

### <a name="transactions"></a>Transakcje
Obciążenia składa się z dziewięciu typów transakcji, jak pokazano w poniższej tabeli. Każda transakcja jest przeznaczony do wyróżnienia z określonym zestawem właściwości systemu w bazie danych systemu i aparat sprzęt, wysoki kontrast z innych transakcji. Takie podejście ułatwia ocenę wpływu różnych składników na ogólną wydajność. Na przykład "Odczytu Heavy" transakcji tworzy znaczna liczba operacji odczytu z dysku.

| Typ transakcji | Opis |
| --- | --- |
| Przeczytaj Lite |WYBÓR; w pamięci. tylko do odczytu |
| Średni odczyt |WYBÓR; przede wszystkim w pamięci; tylko do odczytu |
| Duże odczytu |WYBÓR; przede wszystkim nie w pamięci; tylko do odczytu |
| Aktualizacja Lite |AKTUALIZACJI. w pamięci. odczytu / zapisu |
| Duże aktualizacji |AKTUALIZACJI. przede wszystkim nie w pamięci; odczytu / zapisu |
| Wstaw Lite |WSTAW; w pamięci. odczytu / zapisu |
| Wstaw obciążenie |WSTAW; przede wszystkim nie w pamięci; odczytu / zapisu |
| Usuwanie |USUŃ; Kombinacja w pamięci, a nie w pamięci; odczytu / zapisu |
| Obciążenie procesora CPU |WYBÓR; w pamięci. stosunkowo duże obciążenie procesora CPU; tylko do odczytu |

### <a name="workload-mix"></a>Różne obciążenia
Transakcje są wybierane losowo w ważona dystrybucji przy użyciu następujących mieszanego ogólnej. Ogólny mieszanego ma współczynnik odczyt/zapis w około 2:1.

| Typ transakcji | % mieszanego |
| --- | --- |
| Przeczytaj Lite |35 |
| Średni odczyt |20 |
| Duże odczytu |5 |
| Aktualizacja Lite |20 |
| Duże aktualizacji |3 |
| Wstaw Lite |3 |
| Wstaw obciążenie |2 |
| Usuwanie |2 |
| Obciążenie procesora CPU |10 |

### <a name="users-and-pacing"></a>Użytkownicy i rozkład
Obciążenia porównawczego jest uzyskiwana z narzędziem, które prześle transakcji w zestawie połączeń, aby symulować liczba równoczesnych użytkowników. Mimo że wszystkie połączenia i transakcji są generowane maszyny, dla uproszczenia nazywamy tych połączeń "Użytkownicy". Mimo że każdy użytkownik działa niezależnie od innych użytkowników, wszyscy użytkownicy wykonania tego samego cyklu kroki opisane poniżej:

1. Nawiązać połączenia z bazą danych.
2. Powtórz sygnalizowane, aby zakończyć pracę:
   * Wybierz transakcji (z ważona dystrybucja).
   * Przeprowadź wybraną transakcję i pomiar czasu odpowiedzi.
   * Poczekaj, aż pacing opóźnienia.
3. Zamknij połączenie z bazą danych.
4. Zakończ.

Pacing opóźnienie (w kroku 2c) jest wybranych losowo, ale z dystrybucją, która ma średniej wersji 1.0 sekundy. Ten sposób każdy użytkownik średnio wygenerować co najwyżej jednej transakcji na sekundę.

### <a name="scaling-rules"></a>Zasady skalowania
Liczba użytkowników, zależy od rozmiaru bazy danych (w jednostkach współczynnik skali). Brak jednego użytkownika dla każdej z pięciu jednostek współczynnik skali. Ze względu na opóźnienie pacing jeden użytkownik może generować co najwyżej jednej transakcji na sekundę, średnio.

Na przykład-współczynnik skali 500 (SF = 500) bazy danych będzie mieć 100 użytkowników i osiągnąć maksymalny stopień 100 TPS. Można dostarczać TPS wyższe szybkości wymaga większej liczby użytkowników i większe bazy danych.

W poniższej tabeli przedstawiono liczbę użytkowników, w rzeczywistości trwającym nieprzerwanie przez każdy usługi warstwy i poziomu wydajności.

| Warstwy usług (poziomu wydajności) | Użytkownicy | Rozmiar bazy danych |
| --- | --- | --- |
| Podstawowa |5 |720 MB |
| Standardowa (S0) |10 |1 GB |
| Standardowa (S1) |20 |2.1 GB |
| Standard (warstwa S2) |50 |7.1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Pomiar czasu trwania
Nieprawidłowa uruchomienia testu porównawczego wymaga stabilnym pomiaru czas trwania wynoszący co najmniej jedną godzinę.

### <a name="metrics"></a>Metryki
Kluczowe metryki w uruchomionym teście są przepływności i czasu odpowiedzi.

* Przepływność jest miary wydajności podstawowych w uruchomionym teście. Przepływność jest zgłaszany w transakcji na jednostkę of-time, inwentaryzacji wszystkich typów transakcji.
* Czas odpowiedzi jest miarą przewidywalność wydajności. Ograniczenie czasu odpowiedzi zależy od klasy usług z wyższej klasy usługę, której bardziej rygorystyczne wymagania dotyczące czasu reakcji, jak pokazano poniżej.

| Klasa usługi | Pomiar przepływności | Wymagania dotyczące czasu odpowiedzi |
| --- | --- | --- |
| Premium |Transakcje na sekundę |95. percentyl na 0,5 sekund |
| Standardowa (Standard) |Transakcje na minutę |90. percentyla w wersji 1.0 w ciągu kilku sekund |
| Podstawowa |Transakcje na godzinę |80. percentylu, w sekundach 2.0 |

## <a name="next-steps"></a>Kolejne kroki

- Dla modelu zakupu opartego na rdzeniach wirtualnych, zobacz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md)
- Oparte na jednostkach DTU model zakupu, można zobaczyć [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md).
