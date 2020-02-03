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
Wstępne przetwarzanie i czyszczenie danych to ważne zadania, które należy wykonać, aby można było użyć zestawu danych do uczenia modelu. Nieprzetworzone dane są często hałas i zawodnych i może brakować wartości. Przy użyciu tych danych do modelowania może spowodować wyświetlenie nieprawdziwych wyników. Te zadania są dostępne w ramach procesu do nauki o danych zespołu (TDSP) i zwykle należy wykonać początkową eksploracji zestawu danych używane do odnajdywania i planowanie wstępnego przetwarzania wymagane. Więcej szczegółowych instrukcji dotyczących procesu przetwarzania TDSP można znaleźć w opisie kroków przedstawionych w [procesie nauka danych zespołu](overview.md).

Zadania wstępnego przetwarzania i czyszczenia, takie jak zadanie eksploracji danych, można wykonywać w różnych środowiskach, takich jak SQL lub Hive lub Azure Machine Learning Studio (klasyczny) oraz z różnymi narzędziami i językami, takimi jak R lub Python, w zależności od tego, gdzie dane jest przechowywany i jak jest sformatowany. Ponieważ przetwarzanie TDSP iteracyjne pochylone, te zadania mogą być wykonywane w różnych kroków w przepływie pracy procesu.

W tym artykule wprowadzono różne koncepcje przetwarzania danych i zadania, które można podjąć przed lub po pozyskaniu danych do Azure Machine Learning Studio (klasyczne).

Aby zapoznać się z przykładem eksploracji i wstępnego przetwarzania danych w ramach Azure Machine Learning Studio (klasyczny), zobacz wideo dotyczące [przetwarzania wstępnego danych](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Dlaczego wstępne przetwarzanie i czyszczenie danych?
Rzeczywiste dane są zbierane z różnych źródeł i procesy i może zawierać nieprawidłowości lub uszkodzone dane pogarszania jakości zestawu danych. Problemy z jakością typowych danych, które powstają są:

* **Niekompletne**: dane nie mają atrybutów lub zawierają brakujące wartości.
* **Zakłócenia**: dane zawierają błędne rekordy lub elementy odstające.
* **Niespójne**: dane zawierają rekordy powodujące konflikt lub niezgodności.

Dane dotyczące jakości jest wymaganiem wstępnym dla modeli predykcyjnych jakości. Aby uniknąć "wyrzucania elementów w pamięci out" i poprawić jakość danych i w związku z tym modelu wydajności, konieczne jest przeprowadzenie ekran kondycji danych wczesne wykrywanie problemów z danych i wybrać odpowiedniego przetwarzania danych i czynności czyszczenia.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jakie są niektóre ekrany kondycji typowych danych, które są wykorzystywane?
Możemy sprawdzić ogólnej jakości danych, sprawdzając:

* Liczba **rekordów**.
* Liczba **atrybutów** (lub **funkcji**).
* **Typy danych** atrybutu (nominalny, porządkowy lub ciągły).
* Liczba **brakujących wartości**.
* **Poprawnie sformułowane** dane.
  * Jeśli dane znajdują się w TSV lub CSV, sprawdź, czy kolumna separatory i separatory wierszy zawsze poprawnie oddzielić kolumn i wierszy.
  * Jeśli dane są w formacie HTML lub XML, sprawdź, czy dane jest poprawnie sformułowany oparte na ich odpowiednich standardów.
  * Analiza kodu mogą być również konieczne w celu wyodrębnienia informacji z danych z częściową strukturą lub bez struktury.
* **Niespójne rekordy danych**. Sprawdź zakres wartości są dozwolone. Na przykład, jeśli dane zawierają GPA uczniów (średnia punktu oceny), sprawdź, czy GPA znajduje się w wyznaczonam zakresie, powiedz 0 ~ 4.

Gdy znajdziesz problemy z danymi, **kroki przetwarzania** są konieczne, co często polega na czyszczeniu brakujących wartości, normalizacji danych, dyskretyzacji, przetwarzaniu tekstu, aby usunąć i/lub zastąpić osadzone znaki, które mogą mieć wpływ na wyrównanie danych, mieszane typy danych w wspólnych polach i innych.

**Azure Machine Learning wykorzystuje poprawnie sformułowane dane tabelaryczne**.  Jeśli dane są już w formie tabelarycznej, przetwarzanie wstępne danych może być wykonywane bezpośrednio z Azure Machine Learning Studio (klasyczne) w Machine Learning.  Jeśli dane nie są w formie tabelarycznej, powiedz, który jest w formacie XML, analizowania może być wymagane w celu konwersji danych w formie tabelarycznej.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jakie są niektóre z najważniejszych zadań wstępne przetwarzanie danych?
* **Czyszczenie danych**: Wypełnij brakujące wartości, Wykryj i Usuń dane o zakłóceniach i wartości odstające.
* **Przekształcanie danych**: normalizowanie danych w celu zmniejszenia wymiarów i szumów.
* **Zmniejszenie ilości danych**: Przykładowe rekordy danych lub atrybuty w celu ułatwienia obsługi danych.
* **Data dyskretyzacji**: konwertowanie atrybutów ciągłych na atrybuty kategorii w celu ułatwienia używania z określonymi metodami uczenia maszynowego.
* **Czyszczenie tekstu**: Usuwanie znaków osadzonych, które mogą spowodować niewłaściwe wyrównania danych, na przykład karty osadzone w pliku danych rozdzielanych kartami, osadzone nowe wiersze, które mogą przerywać rekordy, na przykład.

W poniższych sekcjach opisano niektóre z tych kroków przetwarzania danych.

## <a name="how-to-deal-with-missing-values"></a>Jak radzić sobie z brakującymi wartościami?
Aby poradzić sobie z brakującymi wartościami, najlepiej jest najpierw określić przyczynę brakujące wartości, aby lepiej obsługiwać ten problem. Typowe metody obsługi brakujące wartości to:

* **Usuwanie**: usuwanie rekordów z brakującymi wartościami
* **Podstawienie fikcyjne**: Zastąp brakujące wartości wartością fikcyjną: np. *nieznane* dla kategorii lub 0 dla wartości liczbowych.
* **Znaczenie znaczenia**: jeśli brakujące dane są liczbowe, Zastąp brakujące wartości średnią.
* **Częste podstawienie**: jeśli brakujące dane to kategorii, Zastąp brakujące wartości z najczęściej występującym elementem
* **Podstawienie regresji**: Użyj metody regresji, aby zastąpić brakujące wartości wartościami uległa pogorszeniu.  

## <a name="how-to-normalize-data"></a>Jak normalizacji danych?
Normalizacja danych przeskaluje wartości liczbowe do określonego zakresu. Metody normalizacji danych popularnych obejmują:

* **Minimalna-maksymalna normalizacja**: liniowo Przekształć dane w zakres, powiedzmy od 0 do 1, gdzie wartość minimalna jest skalowana do 0, a maksymalna wartość 1.
* **Normalizacja wyniku Z**: Skaluj dane na podstawie średniego i odchylenia standardowego: Podziel różnicę między danymi a średnią przez odchylenie standardowe.
* **Skalowanie dziesiętne**: Skaluj dane przez przeniesienie przecinka dziesiętnego wartości atrybutu.  

## <a name="how-to-discretize-data"></a>Jak dyskretyzowania danych?
Dane można zdyskretyzować konwertując ciągłe wartości atrybutów nominalna lub odstępach czasu. W ten sposób niektóre sposoby są następujące:

* **Pakowania o równej szerokości**: Podziel zakres wszystkich możliwych wartości atrybutu na N grup o tym samym rozmiarze i przypisz wartości, które znajdują się w pojemniku z numerem pojemnika.
* **Pakowania o równej wysokości**: Podziel zakres wszystkich możliwych wartości atrybutu na N grup, z których każdy zawiera tę samą liczbę wystąpień, a następnie przypisz wartości, które znajdują się w pojemniku z numerem pojemnika.  

## <a name="how-to-reduce-data"></a>W jaki sposób zmniejszenia ilości danych?
Istnieją różne metody, aby zmniejszyć rozmiar danych do obsługi danych łatwiejsze. W zależności od rozmiaru danych i domeny można zastosować następujących metod:

* **Rejestruj próbkowanie**: Przykładowe rekordy danych i wybierz tylko reprezentatywny podzbiór danych.
* **Próbkowanie atrybutów**: Wybierz tylko podzestaw najważniejszych atrybutów z danych.  
* **Agregacja**: Podziel dane na grupy i przechowuj numery dla każdej grupy. Na przykład przychód dziennej liczby sieć restauracji, w ciągu ostatnich 20 lat, może być agregowany dochodem miesięczny, aby zmniejszyć rozmiar danych.  

## <a name="how-to-clean-text-data"></a>Jak wyczyścić dane tekstowe?
**Pola tekstowe w danych tabelarycznych** mogą zawierać znaki, które mają wpływ na wyrównanie kolumn i/lub granice rekordów. Na przykład karty osadzone w pliku rozdzielonym tabulatorem powodują nieprawidłowe wyrównanie kolumn i osadzonych znaków nowego wiersza. Niewłaściwa obsługa kodowania tekstu podczas zapisywania lub odczytywania tekstu prowadzi do utraty informacji, przypadkowe wprowadzenie nieczytelnych znaków (takich jak null) i może mieć wpływ na analizowanie tekstu. Dokładnej analizy i edytowania może być wymagane, aby można było wyczyścić pola tekstowe do prawidłowego wyrównania i/lub dane wyodrębnione ze strukturą danych niestrukturyzowanych i strukturyzowanych tekstu.

**Eksploracja danych** oferuje wczesny wgląd w dane. Liczba problemów z danych może być niepokryty, w tym kroku i odpowiedniej metody mogą być stosowane w celu rozwiązania tych problemów.  Jest ważne zadawać pytania, takie jak co to jest źródłem problemu i jak problemu mogły zostać wprowadzone. Ten proces pomaga również w wyborze kroków przetwarzania danych, które należy podjąć w celu ich rozwiązania. Określenie ostatecznych przypadków użycia i osób może być również używane do określania priorytetów nakładu pracy przetwarzania danych.

## <a name="references"></a>Dokumentacja
> *Wyszukiwanie danych: koncepcje i techniki*, wydanie trzecie, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber i Jian PEI
> 
> 

