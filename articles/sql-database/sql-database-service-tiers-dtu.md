---
title: Warstwy usług — model zakupu oparty na usłudze DTU
description: Dowiedz się więcej o warstwach usług w modelu zakupu opartym na jednostuchna dla jednostek i puli baz danych w celu zapewnienia rozmiarów obliczeniowych i magazynowych.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75562123"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Warstwy usług w modelu zakupów opartym na jednostkach DTU

Warstwy usług w modelu zakupu opartym na jednostce DTU są zróżnicowane według zakresu rozmiarów obliczeń ze stałą ilością dołączonego magazynu, stałym okresem przechowywania kopii zapasowych i ceną ustaloną. Wszystkie warstwy usług w modelu zakupu opartym na jednostce DTU zapewniają elastyczność zmiany rozmiarów obliczeń przy minimalnych [przestojach;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) jednak istnieje przełącznik w okresie, w którym łączność jest tracona do bazy danych na krótki czas, które mogą być złagodzone przy użyciu logiki ponawiania. Pojedyncze bazy danych i pule elastyczne są rozliczane co godzinę na podstawie warstwy usług i rozmiaru obliczeń.

> [!IMPORTANT]
> Wystąpienie zarządzane bazy danych SQL nie obsługuje modelu zakupu opartego na jednostkach DTU. Aby uzyskać więcej informacji, zobacz [Wystąpienie zarządzane usługi Azure SQL Database](sql-database-managed-instance.md).
> [!NOTE]
> Aby uzyskać informacje na temat warstw usług opartych nacorach wirtualnych, zobacz [warstwy usług oparte nacorach wirtualnych](sql-database-service-tiers-vcore.md). Aby uzyskać informacje dotyczące rozróżniania warstw usług opartych na UTU i warstwach usług opartych na językach wirtualnych, zobacz [Modele zakupu usługi Azure SQL Database](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Porównanie warstw usług opartych na USŁUDZE DTU

Wybór warstwy usług zależy przede wszystkim od ciągłości biznesowej, magazynu i wymagań dotyczących wydajności.

||Podstawowa (Basic)|Standardowa|Premium|
| :-- | --: |--:| --:|
|Docelowe obciążenie pracą|Rozwój i produkcja|Rozwój i produkcja|Rozwój i produkcja|
|Umowy SLA w czasie pracy bez przestojów|99,99%|99,99%|99,99%|
|Maksymalne przechowywanie kopii zapasowych|7 dni|35 dni|35 dni|
|Procesor CPU|Małe|Niski, Średni, Wysoki|Średni, Wysoki|
|Przepustowość we/wy (przybliżona) |1-5 IOPS na DTU| 1-5 IOPS na DTU | 25 IOPS na DTU|
|Opóźnienie we/wy (przybliżone)|5 ms (odczyt), 10 ms (zapis)|5 ms (odczyt), 10 ms (zapis)|2 ms (odczyt/zapis)|
|Indeksowanie magazynu kolumn |Nie dotyczy|S3 i powyżej|Obsługiwane|
|Oltp w pamięci|Nie dotyczy|Nie dotyczy|Obsługiwane|
|||||

> [!IMPORTANT]
> Warstwy usług Basic, Standard S0, S1 i S2 zapewniają mniej niż jeden procesor wirtualny(CPU).  W przypadku obciążeń intensywnie korzystających z procesora CPU zalecana jest warstwa usług S3 lub większa. 
>
>Jeśli chodzi o przechowywanie danych, warstwy usług Basic, Standard S0 i S1 są umieszczane w obiektach blob strony standardowej. Standardowe obiekty blob strony używają nośników pamięci masowej opartych na dysku twardym (HDD) i najlepiej nadają się do tworzenia, testowania i innych rzadko używanych obciążeń, które są mniej wrażliwe na zmienność wydajności.
>

> [!NOTE]
> Możesz uzyskać bezpłatną bazę danych SQL platformy Azure w warstwie usług Basic w połączeniu z bezpłatnym kontem platformy Azure w celu eksplorowania platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie zarządzanej bazy danych w chmurze za pomocą bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Pojedyncza baza danych Jednostanu I limitów magazynowania

Rozmiary obliczeń są wyrażone w kategoriach jednostek transakcyjnych bazy danych (DTU) dla pojedynczych baz danych i elastycznych jednostek transakcyjnych bazy danych (eDTU) dla pul elastycznych. Aby uzyskać więcej informacji na temat procesorów DTU i eDTU, zobacz [model zakupu oparty na UTU](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Podstawowa (Basic)|Standardowa|Premium|
| :-- | --: | --: | --: |
| Maksymalny rozmiar pamięci masowej | 2 GB | 1 TB | 4 TB  |
| Maksymalna liczba procesorów DTU | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Limity eDTU, magazynu i puli puli danych

| | **Podstawowa (Basic)** | **Standardowa** | **Premium** |
| :-- | --: | --: | --: |
| Maksymalny rozmiar magazynu na bazę danych  | 2 GB | 1 TB | 1 TB |
| Maksymalny rozmiar magazynu na pulę | 156 GB | 4 TB | 4 TB |
| Maksymalna liczba eDTU na bazę danych | 5 | 3000 | 4000 |
| Maksymalna liczba eDTU na pulę | 1600 | 3000 | 4000 |
| Maksymalna liczba baz danych w puli | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Ponad 1 TB pamięci masowej w warstwie Premium jest obecnie dostępna we wszystkich regionach z wyjątkiem: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regiony i us Government Central. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzanie przestrzenią plików w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU Benchmark

Charakterystyki fizyczne (CPU, memory, IO) skojarzone z każdą miarą jednostki DTU są kalibrowane przy użyciu testu porównawczego, który symuluje obciążenie bazy danych w świecie rzeczywistym.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelowanie wyników testów porównawczych z rzeczywistą wydajnością baz danych

Ważne jest, aby zrozumieć, że wszystkie wskaźniki są reprezentatywne i orientacyjne. Stopy transakcji osiągnięte za pomocą aplikacji porównawczej nie będą takie same jak te, które można osiągnąć w przypadku innych aplikacji. Wskaźnik porównawczy obejmuje kolekcję różnych typów transakcji uruchamianych względem schematu zawierającego zakres tabel i typów danych. Podczas gdy wskaźnik wykonuje te same podstawowe operacje, które są wspólne dla wszystkich obciążeń OLTP, nie reprezentuje żadnej określonej klasy bazy danych lub aplikacji. Celem testu porównawczego jest zapewnienie rozsądnego przewodnika po względnej wydajności bazy danych, jakiej można oczekiwać podczas skalowania w górę lub w dół między rozmiarami obliczeń. W rzeczywistości bazy danych mają różne rozmiary i złożoność, napotykają różne kombinacje obciążeń i będą reagować na różne sposoby. Na przykład aplikacja intensywnie korzystających z operacji we/wy może wcześniej przekroczyć progi we/wy lub aplikacja intensywnie korzystająca z procesora CPU może wcześniej przekroczyć limity procesora CPU. Nie ma gwarancji, że jakakolwiek konkretna baza danych będzie skalowana w taki sam sposób, jak wskaźnik przy rosnącym obciążeniu.

Benchmark i jego metodologia zostały szczegółowo opisane poniżej.

### <a name="benchmark-summary"></a>Podsumowanie benchmarku

Wskaźnik mierzy wydajność kombinacji podstawowych operacji bazy danych, które występują najczęściej w obciążeniach przetwarzania transakcji online (OLTP). Mimo że benchmark został zaprojektowany z myślą o przetwarzaniu w chmurze, schemat bazy danych, populacja danych i transakcje zostały zaprojektowane tak, aby były szeroko reprezentatywne dla podstawowych elementów najczęściej używanych w obciążeniach OLTP.

### <a name="schema"></a>Schemat

Schemat został zaprojektowany tak, aby mieć wystarczającą różnorodność i złożoność do obsługi szerokiego zakresu operacji. Wskaźnik jest wzorowany na bazie danych składającej się z sześciu tabel. Tabele dzielą się na trzy kategorie: o stałym rozmiarze, skalowanie i rozwijanie. Istnieją dwie tabele o stałym rozmiarze; trzy tabele skalowania; i jeden stół uprawy. Tabele o stałym rozmiarze mają stałą liczbę wierszy. Skalowanie tabel mają kardynalność, która jest proporcjonalna do wydajności bazy danych, ale nie zmienia się podczas testu porównawczego. Rosnąca tabela jest wielkości tabeli skalowania na obciążenie początkowe, ale następnie zmiany kardynalność w trakcie uruchamiania benchmarku jako wiersze są wstawiane i usuwane.

Schemat zawiera kombinację typów danych, w tym liczby całkowitej, liczbowe, znak i datę/godzinę. Schemat zawiera klucze podstawowe i pomocnicze, ale nie żadnych kluczy obcych — oznacza to, że nie ma żadnych więzów integralności między tabelami.

Program do generowania danych generuje dane dla początkowej bazy danych. Dane liczbowe i liczbowe są generowane przy różnych strategiach. W niektórych przypadkach wartości są rozdzielane losowo w zakresie. W innych przypadkach zestaw wartości jest losowo permuted w celu zapewnienia, że określony rozkład jest zachowywany. Pola tekstowe są generowane z ważonych listy słów w celu uzyskania realistycznie wyglądających danych.

Rozmiar bazy danych jest oparty na "współczynniku skali". Współczynnik skali (w skrócie SF) określa kardynalność skalowania i rosnących tabel. Jak opisano poniżej w sekcji Użytkownicy i tempo, rozmiar bazy danych, liczba użytkowników i maksymalna wydajność wszystkie skalują się proporcjonalnie do siebie.

### <a name="transactions"></a>Transakcje

Obciążenie składa się z dziewięciu typów transakcji, jak pokazano w poniższej tabeli. Każda transakcja ma na celu podświetlenie określonego zestawu charakterystyk systemu w silniku bazy danych i sprzęcie systemowym, z wysokim kontrastem z innymi transakcjami. Takie podejście ułatwia ocenę wpływu różnych komponentów na ogólną wydajność. Na przykład transakcja "Odczyt ciężki" tworzy znaczną liczbę operacji odczytu z dysku.

| Typ transakcji | Opis |
| --- | --- |
| Czytaj Lite |WYBIERZ; w pamięci; tylko do odczytu |
| Przeczytaj medium |WYBIERZ; głównie w pamięci; tylko do odczytu |
| Przeczytaj ciężkie |WYBIERZ; w większości nie w pamięci; tylko do odczytu |
| Aktualizacja Lite |AKTUALIZACJA; w pamięci; odczyt i zapis |
| Aktualizacja Gruba |AKTUALIZACJA; w większości nie w pamięci; odczyt i zapis |
| Wstaw Lite |WSTAWIĆ; w pamięci; odczyt i zapis |
| Wkładka ciężka |WSTAWIĆ; w większości nie w pamięci; odczyt i zapis |
| Usuń |USUŃ; mieszanka w pamięci, a nie w pamięci; odczyt i zapis |
| Ciężki procesor |WYBIERZ; w pamięci; stosunkowo duże obciążenie procesora; tylko do odczytu |

### <a name="workload-mix"></a>Połączenie obciążeń

Transakcje są wybierane losowo z rozkładu ważonego z następującym ogólnym miksem. Ogólna mieszanka ma stosunek odczytu/zapisu około 2:1.

| Typ transakcji | % mieszanki |
| --- | --- |
| Czytaj Lite |35 |
| Przeczytaj medium |20 |
| Przeczytaj ciężkie |5 |
| Aktualizacja Lite |20 |
| Aktualizacja Gruba |3 |
| Wstaw Lite |3 |
| Wkładka ciężka |2 |
| Usuń |2 |
| Ciężki procesor |10 |

### <a name="users-and-pacing"></a>Użytkownicy i tempo

Obciążenie testu porównawczego jest oparte na narzędziu, które przesyła transakcje w ramach zestawu połączeń w celu symulacji zachowania wielu równoczesnych użytkowników. Chociaż wszystkie połączenia i transakcje są generowane przez komputer, dla uproszczenia nazywamy te połączenia "użytkownikami". Mimo że każdy użytkownik działa niezależnie od wszystkich innych użytkowników, wszyscy użytkownicy wykonują ten sam cykl kroków pokazany poniżej:

1. Ustanawianie połączenia z bazą danych.
2. Powtarzaj, aż zasygnalizuje wyjście:
   - Wybierz transakcję losowo (z rozkładu ważonego).
   - Wykonaj wybraną transakcję i zmierz czas odpowiedzi.
   - Poczekaj na opóźnienie tempa.
3. Zamknij połączenie z bazą danych.
4. Wyjścia.

Opóźnienie tempa (w kroku 2c) jest wybierane losowo, ale z rozkładem, który ma średnio 1,0 sekundy. W ten sposób każdy użytkownik może średnio generować co najwyżej jedną transakcję na sekundę.

### <a name="scaling-rules"></a>Reguły skalowania

Liczba użytkowników jest określana przez rozmiar bazy danych (w jednostkach współczynnika skali). Istnieje jeden użytkownik dla każdego pięciu jednostek współczynnika skali. Ze względu na opóźnienie tempa jeden użytkownik może generować co najwyżej jedną transakcję na sekundę, średnio.

Na przykład współczynnik skali 500 (SF = 500) bazy danych będzie miał 100 użytkowników i może osiągnąć maksymalną szybkość 100 TPS. Aby zwiększyć szybkość TPS wymaga większej liczby użytkowników i większej bazy danych.

### <a name="measurement-duration"></a>Czas trwania pomiaru

Prawidłowy przebieg testu porównawczego wymaga czasu pomiaru w stanie ustalonym wynoszącym co najmniej jedną godzinę.

### <a name="metrics"></a>Metryki

Kluczowe metryki w benchmarku to przepływność i czas odpowiedzi.

- Przepustowość jest podstawową miarą wydajności w benchmarku. Przepływność jest raportowana w transakcjach na jednostkę czasu, zliczając wszystkie typy transakcji.
- Czas reakcji jest miarą przewidywalności wydajności. Ograniczenie czasu odpowiedzi różni się w zależności od klasy usługi, przy czym wyższe klasy usług mają bardziej rygorystyczne wymagania dotyczące czasu reakcji, jak pokazano poniżej.

| Klasa serwisu | Miara przepływności | Wymaganie czasu reakcji |
| --- | --- | --- |
| Premium |Transakcje na sekundę |95 percentyl w 0,5 sekundy |
| Standardowa |Transakcje na minutę |90 percentyl w 1,0 sekundy |
| Podstawowa (Basic) |Transakcje na godzinę |80 percentyl w 2,0 sekundy |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczych baz danych, zobacz [Limity zasobów oparte na jednostce DTU oparte na bazie danych SQL dla pojedynczych baz danych](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pul elastycznych, zobacz [Limity zasobów oparte na jednostce DTU bazy danych SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
