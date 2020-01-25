---
title: Przygotowywanie danych dla ML Studio (klasyczny) — proces nauki o danych zespołowych
description: Wstępne przetwarzanie i czyszczenie danych, aby przygotować go do efektywnie używać uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720048"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego
Wstępne przetwarzanie i czyszczenie danych to ważne zadania, które należy wykonać, aby można było użyć zestawu danych do uczenia modelu. Nieprzetworzone dane są często hałas i zawodnych i może brakować wartości. Przy użyciu tych danych do modelowania może spowodować wyświetlenie nieprawdziwych wyników. Te zadania są dostępne w ramach procesu do nauki o danych zespołu (TDSP) i zwykle należy wykonać początkową eksploracji zestawu danych używane do odnajdywania i planowanie wstępnego przetwarzania wymagane. Aby uzyskać szczegółowe instrukcje na temat procesu przetwarzania TDSP, zobacz kroki opisane w temacie [zespołu danych dla celów naukowych](overview.md).

Zadania wstępnego przetwarzania i czyszczenia, takie jak zadanie eksploracji danych, można wykonywać w różnych środowiskach, takich jak SQL lub Hive lub Azure Machine Learning Studio (klasyczny) oraz z różnymi narzędziami i językami, takimi jak R lub Python, w zależności od tego, gdzie dane jest przechowywany i jak jest sformatowany. Ponieważ przetwarzanie TDSP iteracyjne pochylone, te zadania mogą być wykonywane w różnych kroków w przepływie pracy procesu.

W tym artykule wprowadzono różne koncepcje przetwarzania danych i zadania, które można podjąć przed lub po pozyskaniu danych do Azure Machine Learning Studio (klasyczne).

Aby zapoznać się z przykładem eksploracji i wstępnego przetwarzania danych w ramach Azure Machine Learning Studio (klasyczny), zobacz wideo dotyczące [przetwarzania wstępnego danych](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Dlaczego wstępne przetwarzanie i czyszczenie danych?
Rzeczywiste dane są zbierane z różnych źródeł i procesy i może zawierać nieprawidłowości lub uszkodzone dane pogarszania jakości zestawu danych. Problemy z jakością typowych danych, które powstają są:

* **Niekompletne**: danych nie ma atrybutów lub zawierające brakujące wartości.
* **Hałas**: dane zawierają błędne rekordów lub elementy odstające.
* **Niespójne**: dane zawierają rekordy powodujące konflikt lub niezgodności.

Dane dotyczące jakości jest wymaganiem wstępnym dla modeli predykcyjnych jakości. Aby uniknąć "wyrzucania elementów w pamięci out" i poprawić jakość danych i w związku z tym modelu wydajności, konieczne jest przeprowadzenie ekran kondycji danych wczesne wykrywanie problemów z danych i wybrać odpowiedniego przetwarzania danych i czynności czyszczenia.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jakie są niektóre ekrany kondycji typowych danych, które są wykorzystywane?
Możemy sprawdzić ogólnej jakości danych, sprawdzając:

* Liczba **rekordów**.
* Liczba **atrybuty** (lub **funkcji**).
* Ten atrybut **typy danych** (nominalna, numer porządkowy lub ciągłe).
* Liczba **brakujących wartości**.
* **Poprawnie sformułowane** dane.
  * Jeśli dane znajdują się w TSV lub CSV, sprawdź, czy kolumna separatory i separatory wierszy zawsze poprawnie oddzielić kolumn i wierszy.
  * Jeśli dane są w formacie HTML lub XML, sprawdź, czy dane jest poprawnie sformułowany oparte na ich odpowiednich standardów.
  * Analiza kodu mogą być również konieczne w celu wyodrębnienia informacji z danych z częściową strukturą lub bez struktury.
* **Niespójne dane rekordów**. Sprawdź zakres wartości są dozwolone. Na przykład, jeśli dane zawierają GPA uczniów (średnia punktu oceny), sprawdź, czy GPA znajduje się w wyznaczonam zakresie, powiedz 0 ~ 4.

Gdy znajdziesz problemy z danymi, **kroki przetwarzania** są konieczne, co często polega na czyszczeniu brakujących wartości, normalizacji danych, dyskretyzacji, przetwarzaniu tekstu, aby usunąć i/lub zastąpić osadzone znaki, które mogą mieć wpływ na wyrównanie danych, mieszane typy danych w wspólnych polach i innych.

**Usługa Azure Machine Learning wykorzystuje dane tabelaryczne z dobrze sformułowany**.  Jeśli dane są już w formie tabelarycznej, przetwarzanie wstępne danych może być wykonywane bezpośrednio z Azure Machine Learning Studio (klasyczne) w Machine Learning.  Jeśli dane nie są w formie tabelarycznej, powiedz, który jest w formacie XML, analizowania może być wymagane w celu konwersji danych w formie tabelarycznej.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jakie są niektóre z najważniejszych zadań wstępne przetwarzanie danych?
* **Czyszczenie danych**: Wypełnij brakujące wartości, Wykryj i Usuń dane o zakłóceniach i wartości odstające.
* **Przekształcanie danych**: normalizacji danych, aby zmniejszyć wymiary i szumu.
* **Redukcja danych**: przykładowa rekordów danych lub atrybutów w celu łatwiejszej obsługi danych.
* **Dane dyskretyzacji**: przekonwertować atrybutów ciągłych podzielonych na kategorie atrybutów w celu ułatwienia za pomocą niektórych metod learning maszyny.
* **Czyszczenie tekstu**: Usuwanie znaków osadzonych, które mogą spowodować niewłaściwe wyrównania danych, na przykład karty osadzone w pliku danych rozdzielanych kartami, osadzone nowe wiersze, które mogą przerywać rekordy, na przykład.

W poniższych sekcjach opisano niektóre z tych kroków przetwarzania danych.

## <a name="how-to-deal-with-missing-values"></a>Jak radzić sobie z brakującymi wartościami?
Aby poradzić sobie z brakującymi wartościami, najlepiej jest najpierw określić przyczynę brakujące wartości, aby lepiej obsługiwać ten problem. Typowe metody obsługi brakujące wartości to:

* **Usuwanie**: usuwania rekordów z brakującymi wartościami
* **Fikcyjny podstawienia**: Zastąp brakujące wartości z wartości: np. *nieznany* dla kategorii lub równa 0 dla wartości liczbowych.
* **Oznacza podstawienia**: Jeśli brakujące dane liczbowe, Zamień brakujące wartości średniej.
* **Częste odzyskiwanie pamięci podstawienia**: Jeśli brakujące dane są podzielone na kategorie, Zamień brakujące wartości najczęściej występujących elementów
* **Podstawianie regresji**: metodą regresji Aby zastąpić wartości pogorszonej brakujące wartości.  

## <a name="how-to-normalize-data"></a>Jak normalizacji danych?
Normalizacja danych przeskaluje wartości liczbowe do określonego zakresu. Metody normalizacji danych popularnych obejmują:

* **Normalizacji minimum-maksimum**: liniowo przekształcić je do zakresu, na przykład od 0 do 1, w którym wartość minimalna jest skalowany 0 i maksymalna wartość na 1.
* **Wynik Z normalizacji**: skalowanie danych na podstawie średniej i odchylenie standardowe: dzielenie różnicy między danymi a średnia, odchylenie standardowe.
* **Skalowanie dziesiętna**: skalowanie danych, przenosząc punktu dziesiętnego, wartość atrybutu.  

## <a name="how-to-discretize-data"></a>Jak dyskretyzowania danych?
Dane można zdyskretyzować konwertując ciągłe wartości atrybutów nominalna lub odstępach czasu. W ten sposób niektóre sposoby są następujące:

* **Szerokość równe pakowania**: podzielona zakresu wszystkich możliwych wartości atrybutu N grup taki sam rozmiar, a następnie przypisz wartości, które mieszczą się w pojemniku numerem bin.
* **Wysokość równe pakowania**: podzielona zakresu wszystkich możliwych wartości atrybutu N grup, każdy z nich zawierający tę samą liczbę wystąpień, a następnie przypisz wartości, które mieszczą się w pojemniku numerem bin.  

## <a name="how-to-reduce-data"></a>W jaki sposób zmniejszenia ilości danych?
Istnieją różne metody, aby zmniejszyć rozmiar danych do obsługi danych łatwiejsze. W zależności od rozmiaru danych i domeny można zastosować następujących metod:

* **Zarejestruj próbkowania**: Przykładowe rekordów danych i wybrać tylko reprezentatywnego podzestawu danych.
* **Atrybut próbkowania**: wybierać tylko podzbiór atrybutów najważniejszych danych.  
* **Agregacja**: dzielenie danych na grupy i przechowujące liczby, dla każdej grupy. Na przykład przychód dziennej liczby sieć restauracji, w ciągu ostatnich 20 lat, może być agregowany dochodem miesięczny, aby zmniejszyć rozmiar danych.  

## <a name="how-to-clean-text-data"></a>Jak wyczyścić dane tekstowe?
**Pola tekstowe w danych tabelarycznych** mogą zawierać znaki, które mają wpływ na wyrównanie kolumn i/lub granice rekordów. Na przykład karty osadzone w pliku rozdzielonym tabulatorem powodują nieprawidłowe wyrównanie kolumn i osadzonych znaków nowego wiersza. Niewłaściwa obsługa kodowania tekstu podczas zapisywania lub odczytywania tekstu prowadzi do utraty informacji, przypadkowe wprowadzenie nieczytelnych znaków (takich jak null) i może mieć wpływ na analizowanie tekstu. Dokładnej analizy i edytowania może być wymagane, aby można było wyczyścić pola tekstowe do prawidłowego wyrównania i/lub dane wyodrębnione ze strukturą danych niestrukturyzowanych i strukturyzowanych tekstu.

**Eksploracja danych** oferuje wczesne wgląd w dane. Liczba problemów z danych może być niepokryty, w tym kroku i odpowiedniej metody mogą być stosowane w celu rozwiązania tych problemów.  Jest ważne zadawać pytania, takie jak co to jest źródłem problemu i jak problemu mogły zostać wprowadzone. Ten proces pomaga również w wyborze kroków przetwarzania danych, które należy podjąć w celu ich rozwiązania. Określenie ostatecznych przypadków użycia i osób może być również używane do określania priorytetów nakładu pracy przetwarzania danych.

## <a name="references"></a>Informacje
> *Wyszukiwanie danych: Pojęcia i techniki*, wydanie trzecie Morgan Kaufmann, 2011 roku Hanowi Jiawei Micheline Kamber i Jian Pei
> 
> 

