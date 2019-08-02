---
title: Azure SQL Database warstwy usług — model zakupu oparty na jednostkach DTU | Microsoft Docs
description: Informacje o warstwach usług w modelu zakupu opartego na jednostkach DTU dla baz danych o pojedynczej i puli w celu zapewnienia rozmiarów zasobów obliczeniowych i magazynowych.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c46913f24deffc7c4db95d8a77db1c49ae54b6ae
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566682"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Warstwy usług w modelu zakupu opartego na jednostkach DTU

Warstwy usług w modelu zakupu opartego na jednostkach DTU są zróżnicowane przez wiele rozmiarów obliczeń ze stałą ilością dołączonego magazynu, stałym okresem przechowywania kopii zapasowych i ustaloną ceną. Wszystkie warstwy usług w modelu zakupu opartego na jednostkach DTU zapewniają elastyczność zmiany rozmiarów obliczeń przy minimalnym [przestoju](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). Jednak istnieje przedział czasu, w którym połączenie jest tracone do bazy danych przez krótki czas, co można wyeliminować przy użyciu logiki ponawiania. Pojedyncze bazy danych i pul elastycznych są rozliczane godzinowo na podstawie warstwy usługi i obliczeń rozmiaru.

> [!IMPORTANT]
> SQL Database wystąpienie zarządzane nie obsługuje modelu zakupu opartego na jednostkach DTU. Aby uzyskać więcej informacji, zobacz [wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance.md).
> [!NOTE]
> Aby uzyskać informacji na temat warstw usług opartych na rdzeniach wirtualnych, zobacz [warstwy usług oparte na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). Aby dowiedzieć się, jak rozróżnianie warstwy usług oparte na jednostkach DTU i warstwy usług oparte na rdzeniach wirtualnych, zobacz [usługi Azure SQL Database, zakup modeli](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Porównaj warstwy usług oparte na jednostkach DTU

Wybieranie warstwy usług zależy przede wszystkim ciągłości biznesowej, magazynu i wymagań dotyczących wydajności.

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: |--:| --:|
|Docelowego obciążenia|Rozwoju i produkcji|Rozwoju i produkcji|Rozwoju i produkcji|
|Umowa SLA dotycząca czasu dostępności|99,99%|99,99%|99,99%|
|Przechowywanie kopii zapasowych|7 dni|35 dni|35 dni|
|Procesor CPU|Małe|Niska, średnia, wysoka|Średni i wysoki|
|Przepustowość operacji We/Wy (w przybliżeniu) |2.5 operacje We/Wy na jednostkach DTU| 2.5 operacje We/Wy na jednostkach DTU | 48 operacje We/Wy na jednostkach DTU|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 ms (odczyt), 10 ms (zapis)|5 ms (odczyt), 10 ms (zapis)|2 ms (odczyt/zapis)|
|Indeksowanie magazynu kolumn |ND|S3 i nowsze wersje|Obsługiwane|
|Przetwarzanie OLTP danych w pamięci|ND|ND|Obsługiwane|
|||||

> [!NOTE]
> Bezpłatna usługa Azure SQL Database jest oferowana w ramach podstawowej warstwy usług w połączeniu z bezpłatnym kontem platformy Azure, aby poznać platformę Azure. Aby uzyskać informacje, zobacz [utworzyć bazę danych zarządzana usługa w chmurze przy użyciu bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Limity liczby jednostek DTU i magazynu pojedynczej bazy danych

Obliczenia rozmiarów są wyrażone w jednostkach transakcji bazy danych (Dtu) dla pojedynczych baz danych i jednostek transakcji elastic Database (Edtu) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu i Edtu, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-purchase-models.md#dtu-based-purchasing-model)?

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: | --: | --: |
| Maksymalny rozmiar magazynu | 2 GB | 1 TB | 4 TB  |
| Maksymalna liczba jednostek Dtu | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastyczna pula — eDTU, magazynu i limity baza danych w puli

| | **Podstawowa** | **Standardowa** | **Premium** |
| :-- | --: | --: | --: |
| Maksymalny rozmiar magazynu na bazę danych  | 2 GB | 1 TB | 1 TB |
| Maksymalny rozmiar magazynu na pulę | 156 GB | 4 TB | 4 TB |
| Maksymalna liczba jednostek Edtu na bazę danych | 5 | 3000 | 4000 |
| Maksymalna liczba jednostek Edtu na pulę | 1600 | 3000 | 4000 |
| Maksymalna liczba baz danych na pulę | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach poza następującymi: Chiny Wschodnie, Chiny Północne, Niemcy Środkowe, Niemcy Północno-Wschodnie, Zachodnio-środkowe stany USA, regiony US DoD i Instytucje rządowe dla środkowych stanów USA. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Test porównawczy jednostek DTU

Właściwości fizyczne (procesor CPU, pamięć, we/wy) powiązanych z Każda miara jednostek DTU są kalibrowane testów porównawczych, która symuluje obciążenie bazy danych rzeczywistych.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelowanie wyników testów porównawczych wydajności bazy danych rzeczywistych

Jest ważne dowiedzieć się, że wszystkie testy porównawcze są reprezentatywne ale wskazuje tylko. Stawki za transakcje osiągane przy użyciu aplikacji testu porównawczego nie będzie taka sama, jak te, które mogą być osiągnięte z innymi aplikacjami. Testu porównawczego składa się z kolekcją innej transakcji, które typy uruchamiać schemat zawierający szeroką gamę tabele i typy danych. Podczas testu porównawczego skorzysta z tego samego podstawowe operacje, które są wspólne dla wszystkich obciążeń OLTP, nie reprezentuje dowolnej klasy określonej bazy danych lub aplikacji. Cel testu porównawczego jest zapewnienie uzasadnione przewodnik względnej wydajności bazy danych, która może być oczekiwany podczas skalowania w górę lub w dół od rozmiarów wystąpień obliczeniowych. W rzeczywistości bazy danych o różnych rozmiarach i złożoności, występują różne kombinacje obciążeń i będzie odpowiadać na różne sposoby. Na przykład aplikacji intensywnie korzystających z operacji We/Wy może wystąpić wcześniej progów we/wy lub aplikacji intensywnie korzystających z procesora CPU mogą występować limity procesora CPU, wcześniej. Nie ma żadnej gwarancji, które dowolnej określonej bazy danych będzie skalowane w taki sam sposób jak testów porównawczych, zgodnie z rosnącym obciążeniem.

Testu wydajności i jego metody są opisane bardziej szczegółowo poniżej.

### <a name="benchmark-summary"></a>Podsumowanie testu porównawczego

Test porównawczy mierzy wydajność różnych podstawowych operacji baz danych, które często występują w obciążeniach przetwarzania transakcji online (OLTP). Mimo że testu porównawczego zaprojektowano z chmury obliczeniowej w uwadze, schemat bazy danych, wypełnianie danymi, a transakcje zostały zaprojektowane, aby być szeroko reprezentatywne podstawowych elementów, które są najczęściej używane w obciążeń OLTP.

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
   - Wybierz transakcji (z ważona dystrybucja).
   - Przeprowadź wybraną transakcję i pomiar czasu odpowiedzi.
   - Poczekaj, aż pacing opóźnienia.
3. Zamknij połączenie z bazą danych.
4. Zakończ.

Pacing opóźnienie (w kroku 2c) jest wybranych losowo, ale z dystrybucją, która ma średniej wersji 1.0 sekundy. Ten sposób każdy użytkownik średnio wygenerować co najwyżej jednej transakcji na sekundę.

### <a name="scaling-rules"></a>Zasady skalowania

Liczba użytkowników, zależy od rozmiaru bazy danych (w jednostkach współczynnik skali). Brak jednego użytkownika dla każdej z pięciu jednostek współczynnik skali. Ze względu na opóźnienie pacing jeden użytkownik może generować co najwyżej jednej transakcji na sekundę, średnio.

Na przykład-współczynnik skali 500 (SF = 500) bazy danych będzie mieć 100 użytkowników i osiągnąć maksymalny stopień 100 TPS. Można dostarczać TPS wyższe szybkości wymaga większej liczby użytkowników i większe bazy danych.

### <a name="measurement-duration"></a>Pomiar czasu trwania

Nieprawidłowa uruchomienia testu porównawczego wymaga stabilnym pomiaru czas trwania wynoszący co najmniej jedną godzinę.

### <a name="metrics"></a>Metryki

Kluczowe metryki w uruchomionym teście są przepływności i czasu odpowiedzi.

- Przepływność jest miary wydajności podstawowych w uruchomionym teście. Przepływność jest zgłaszany w transakcji na jednostkę of-time, inwentaryzacji wszystkich typów transakcji.
- Czas odpowiedzi jest miarą przewidywalność wydajności. Ograniczenie czasu odpowiedzi zależy od klasy usług z wyższej klasy usługę, której bardziej rygorystyczne wymagania dotyczące czasu reakcji, jak pokazano poniżej.

| Klasa usługi | Pomiar przepływności | Wymagania dotyczące czasu odpowiedzi |
| --- | --- | --- |
| Premium |Transakcje na sekundę |95. percentyl na 0,5 sekund |
| Standardowa (Standard) |Transakcje na minutę |90. percentyla w wersji 1.0 w ciągu kilku sekund |
| Podstawowa |Transakcje na godzinę |80. percentylu, w sekundach 2.0 |

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępne dla pojedynczych baz danych, zobacz [limity zasobów na podstawie jednostek DTU bazy danych SQL dla pojedynczych baz danych](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępne dla pul elastycznych, zobacz [limity zasobów na podstawie jednostek DTU bazy danych SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
