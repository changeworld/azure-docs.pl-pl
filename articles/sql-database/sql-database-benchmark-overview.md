---
title: Omówienie testów porównawczych jednostek dtu w warstwie bazy danych SQL Azure
description: W tym temacie opisano wzorca bazy danych SQL Azure używana w pomiaru wydajności bazy danych SQL Azure.
services: sql-database
author: jan-eng
manager: jhubbard
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 06/21/2016
ms.author: janeng
ms.openlocfilehash: 684ae62400adbde6f0624b802b79a926c06961c8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-sql-database-dtu-benchmark-overview"></a>Omówienie testów porównawczych jednostek dtu w warstwie bazy danych SQL Azure
## <a name="overview"></a>Przegląd
W podstawie jednostek dtu w warstwie model kupna Microsoft Azure SQL Database oferuje trzy [warstw usług](sql-database-service-tiers.md) wiele poziomów wydajności. Każdy poziom wydajności udostępnia zwiększanie zasobów lub "power" przeznaczona do dostarczania coraz wyższej przepustowości.

> [!IMPORTANT]
> Baza danych SQL Azure obsługuje teraz również na podstawie vCore model kupna (wersja zapoznawcza). Aby uzyskać informacje, zobacz [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers.md#vcore-based-purchasing-model-preview).

Jest ważne można było określić, jak zwiększa możliwości każdym poziomie wydajności przekłada się na wydajności zwiększona bazy danych. Aby zrobić to Microsoft opracowała testu wydajności bazy danych SQL Azure (ASDB). Testu porównawczego wykonuje podstawowe operacje, w przypadku wszystkich obciążeń OLTP. Firma Microsoft mierzenia przepływności uzyskuje baz danych uruchomionych w każdym poziomie wydajności.

Zasoby i power każda warstwa i poziom wydajności usługi są wyrażane w postaci liczby [jednostki transakcji bazy danych (Dtu)](sql-database-what-is-a-dtu.md). Liczba jednostek Dtu umożliwiają opisują relatywną pojemność poziomu wydajności oparte na mieszanych pomiarach Procesora, pamięci, Odczyt i zapis oferowane szybkości na każdym poziomie wydajności. Podwojenie klasyfikacji jednostek dtu w warstwie bazy danych jest równa podwojenie zasilania bazy danych. Testu porównawczego pozwala ocenić wpływ na wydajność bazy danych zwiększa możliwości oferowane przez każdy poziom wydajności wykorzystując operacji rzeczywistej bazy danych podczas skalowania rozmiar bazy danych, liczba użytkowników, i transakcji ocenia się proporcjonalnie zasobów w bazie danych.

Przez wyrażenie przepływności warstwy podstawowej usługi przy użyciu transakcji na godzinę, warstwie usług standardowa przy użyciu transakcji na minutę i warstwę Premium za pomocą transakcji na sekundę, jego ułatwia szybkie dotyczą wydajności możliwości poszczególnych warstw usługi do wymagań aplikacji.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelowanie wyniki testu, do rzeczywistych wydajności bazy danych
Należy dowiedzieć się, że ASDB, jak wszystkich testów porównawczych, reprezentatywny i świadczy tylko. Wskaźników transakcji do testu wydajności aplikacji, nie będą takie same jak te, które mogą być osiągnięte z innymi aplikacjami. Testu porównawczego składa się z kolekcją innej transakcji, uruchomienia typów ze schematem zawierający zakres tabel i typy danych. Podczas testu porównawczego skorzysta z tego samego podstawowe operacje, które są wspólne dla wszystkich obciążeń OLTP, nie reprezentuje dowolnej klasy określonej bazy danych lub aplikacji. Celem testu porównawczego jest zapewnienie uzasadnione przewodnik dotyczący względną wydajność bazy danych, które można oczekiwać podczas skalowania w górę lub w dół od poziomów wydajności. W rzeczywistości bazy danych o różnych rozmiarach i złożoność, występują różne kombinacje obciążeń i będzie odpowiadać na różne sposoby. Na przykład aplikacji intensywnie wykonujących operacje We/Wy wcześniej napotkać progi we/wy lub aplikacji użycie Procesora CPU napotkać limitów CPU wcześniej. Nie ma żadnej gwarancji, który dowolnej określonej bazy danych są skalowane w taki sam sposób jak testu porównawczego zgodnie z rosnącym obciążeniem.

Testu porównawczego i jego metody są opisane bardziej szczegółowo poniżej.

## <a name="benchmark-summary"></a>Podsumowanie testu porównawczego
ASDB mierzy wydajność operacje podstawowej bazy danych, które występują najczęściej w transakcji online (OLTP) obciążeń przetwarzania. Mimo że testu porównawczego zaprojektowano z rozwiązań w uwadze schematu bazy danych, populacji danych w chmurze, a transakcje zostały zaprojektowane, aby być szeroko reprezentatywny podstawowych elementów, które są najczęściej używane w obciążeń OLTP.

## <a name="schema"></a>Schemat
Schemat jest przeznaczony do ma za mało różnych i złożoności obsługuje szeroką gamę operacji. W bazie danych, które składają się z sześciu tabel uruchomione testy. Tabele dzielą się na trzy kategorie: stałym rozmiarze, skalowanie i powiększania. Istnieją dwie tabele stałym rozmiarze; trzy tabele skalowania; i jedna tabela rosnącym. Tabele o stałym rozmiarze ma stałą liczbę wierszy. Tabele skalowania mają Kardynalność, jest proporcjonalny do wydajności bazy danych, ale nie zmieniają się podczas testu wydajności. Rozmiar tabeli rosnącym określono jak tabelę skalowania obciążenia początkowej, ale zmiany Kardynalność w trakcie działania testu porównawczego jako wiersze są wstawiane i usunąć.

Schemat zawiera mieszane typy danych, w tym liczbę całkowitą z zakresu alfanumeryczne, znak i daty/godziny. Schemat zawiera klucze podstawowe i pomocnicze, ale nie wszystkie klucze obce — to nie istnieją żadne ograniczenia integralności referencyjnej między tabelami.

Program generowania danych generuje dane początkowej bazy danych. Liczba całkowita i numeryczne dane są generowane z różnych strategii. W niektórych przypadkach wartości są dystrybuowane losowo w zakresie. W pozostałych przypadkach zestaw wartości jest losowo cieniowania do zapewni, że określone dystrybucji. Pola tekstowe są generowane z długą listę słów do tworzenia rzeczywistych wyglądającej danych.

Bazy danych jest ustalać w oparciu "współczynnik skali". Współczynnik skali (skrót rz) określa Kardynalność skalowanie i powiększania tabel. Zgodnie z poniższym opisem w sekcji Użytkownicy i Pacing, rozmiar bazy danych wielu użytkowników oraz maksymalną wydajność wszystkich skalowania proporcjonalnie do siebie.

## <a name="transactions"></a>Transakcje
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

## <a name="workload-mix"></a>Mieszane obciążenia
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

## <a name="users-and-pacing"></a>Użytkownicy i tempo
Obciążenie testu porównawczego wynikają z narzędzia, który przesyła transakcji w zestawie połączeń, aby symulować liczba równoczesnych użytkowników. Mimo że wszystkie połączenia i transakcji są generowane maszyny, dla uproszczenia nazywamy te połączenia "Użytkownicy". Mimo że każdy użytkownik działa niezależnie od innych użytkowników, wszyscy użytkownicy wykonania tego samego cyklu kroki opisane poniżej:

1. Należy ustanowić połączenie z bazą danych.
2. Powtórz sygnalizuje, aby zakończyć działanie:
   * Wybierz transakcję losowo (z rozkładu ważoną).
   * Wykonaj wybranej transakcji i pomiar czasu odpowiedzi.
   * Poczekaj, aż pacing opóźnienia.
3. Zamknij połączenie z bazą danych.
4. Zakończ.

Losowo wybrano pacing opóźnienie (w kroku 2, c), ale z dystrybucji nie ma to wartość średnia drugiej 1.0. W związku z tym każdy użytkownik może średnio wygenerować co najwyżej jednej transakcji na sekundę.

## <a name="scaling-rules"></a>Skalowanie reguły
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
| Premium (P6) |800 |114 GB |

## <a name="measurement-duration"></a>Czas trwania pomiaru.
Nieprawidłowa uruchomienia testu porównawczego wymaga pomiaru stabilnego obowiązywania co najmniej jedną godzinę.

## <a name="metrics"></a>Metryki
Kluczowe metryki w testu porównawczego jest przepływności i odpowiedzi.

* Przepływność to miara wydajności niezbędne w testu porównawczego. Przepływność jest zgłaszana w transakcji na jednostkę elementu czasu, inwentaryzacji wszystkich typów transakcji.
* Czas odpowiedzi to miara wydajności, przewidywalności. Ograniczenia czasu odpowiedzi zależy od rodzaju usług o wyższych klas usługi o wprowadzenie bardziej rygorystycznych wymagań czasu odpowiedzi, jak pokazano poniżej.

| Klasa usługi | Miara przepływności | Wymagania dotyczące czasu odpowiedzi |
| --- | --- | --- |
| Premium |Transakcje na sekundę |95. percentyl w sekundach 0,5 |
| Standardowa (Standard) |Transakcje na minutę |90-procentowy w sekundach 1.0 |
| Podstawowa |Transakcje na godzinę |80. percentyl w sekundach 2.0 |

## <a name="conclusion"></a>Podsumowanie
Wzorzec bazy danych SQL Azure mierzy względną wydajność uruchomione w zakresie warstwy dostępność usług i poziomy wydajności bazy danych SQL Azure. Testu porównawczego wykonuje operacje podstawowej bazy danych, które występują najczęściej w transakcji online (OLTP) obciążeń przetwarzania. Mierząc faktyczną wydajnością testu porównawczego zapewnia bardziej zrozumiałej ocena wpływu na przepustowość zmiana poziomu wydajności niż przy tylko listę zasobów udostępnianych na każdym poziomie, takich jak szybkości procesora CPU, rozmiaru pamięci i IOPS. W przyszłości firma Microsoft będzie rozwijać testu porównawczego rozszerzenie zakresu i rozwiń dostarczonych danych.

## <a name="resources"></a>Zasoby
[Wprowadzenie do bazy danych SQL](sql-database-technical-overview.md)

[Warstwy usług i poziomy wydajności](sql-database-service-tiers.md)

[Wytyczne dotyczące wydajności dla pojedynczej bazy danych](sql-database-performance-guidance.md)
