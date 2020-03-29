---
title: Prep danych dla ML Studio (classic) - Team Data Science Process
description: Wstępne przetwarzanie i czyszczenie danych w celu przygotowania ich do skutecznego wykorzystania do uczenia maszynowego.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720048"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego
Wstępne przetwarzanie i czyszczenie danych są ważne zadania, które muszą być wykonywane przed zestaw danych może być używany do szkolenia modelu. Nieprzetworzone dane są często hałaśliwe i zawodne i mogą brakować wartości. Używanie takich danych do modelowania może spowodować wprowadzające w błąd wyniki. Te zadania są częścią procesu nauki o danych zespołu (TDSP) i zazwyczaj wykonaj początkową eksplorację zestawu danych używanego do odnajdowania i planowania wymaganego przetwarzania wstępnego. Aby uzyskać bardziej szczegółowe instrukcje dotyczące procesu TDSP, zobacz kroki opisane w [procesie nauki o danych zespołu.](overview.md)

Zadania przetwarzania wstępnego i czyszczenia, takie jak zadanie eksploracji danych, mogą być wykonywane w wielu różnych środowiskach, takich jak SQL lub Hive lub Azure Machine Learning Studio (klasyczny) oraz za pomocą różnych narzędzi i języków, takich jak R lub Python, w zależności od tego, gdzie dane jest przechowywany i jak jest sformatowany. Ponieważ TDSP ma charakter iteracyjny, zadania te mogą odbywać się na różnych etapach przepływu pracy procesu.

W tym artykule przedstawiono różne pojęcia przetwarzania danych i zadania, które można podjąć przed lub po spożyciu danych do usługi Azure Machine Learning Studio (klasyczny).

Na przykład eksploracji danych i przetwarzania wstępnego wykonane w usłudze Azure Machine Learning Studio (klasyczny), zobacz [wideo przetwarzania wstępnego danych.](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)

## <a name="why-pre-process-and-clean-data"></a>Dlaczego wstępne przetwarzanie i czyszczenie danych?
Dane ze świata rzeczywistego są zbierane z różnych źródeł i procesów i mogą zawierać nieprawidłowości lub uszkodzone dane naruszające jakość zestawu danych. Typowe problemy z jakością danych, które powstają są:

* **Niekompletne**: Dane nie mają atrybutów lub zawierają brakujące wartości.
* **Hałaśliwe**: Dane zawierają błędne rekordy lub odstające.
* **Niespójne**: Dane zawierają sprzeczne rekordy lub rozbieżności.

Dane dotyczące jakości są warunkiem wstępnym modeli predykcyjnych jakości. Aby uniknąć "śmieci, śmieci" i poprawić jakość danych, a tym samym wydajność modelu, konieczne jest przeprowadzenie ekranu kondycji danych, aby wcześnie wykryć problemy z danymi i zdecydować o odpowiednich krokach przetwarzania i czyszczenia danych.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jakie są typowe ekrany kondycji danych, które są stosowane?
Możemy sprawdzić ogólną jakość danych, sprawdzając:

* Liczba **rekordów**.
* Liczba **atrybutów** (lub **funkcji**).
* **Typy danych atrybutów** (nominalne, porządkowe lub ciągłe).
* Liczba **brakujących wartości**.
* **Dobrze sformułowane** dane.
  * Jeśli dane są w TSV lub CSV, sprawdź, czy separatory kolumn i separatory linii zawsze poprawnie oddzielają kolumny i linie.
  * Jeśli dane są w formacie HTML lub XML, sprawdź, czy dane są dobrze sformułowane na podstawie ich odpowiednich standardów.
  * Analizowanie może być również konieczne w celu wyodrębnienia informacji strukturalnych z danych półstrukturalnych lub niestrukturalnych.
* **Niespójne rekordy danych**. Sprawdź zakres wartości są dozwolone. Na przykład, jeśli dane zawierają gpa studenta (średnia punkt klasy), sprawdź, czy GPA znajduje się w wyznaczonym zakresie, powiedzmy 0 ~ 4.

W przypadku znalezienia problemów z danymi konieczne są **kroki przetwarzania,** które często obejmują czyszczenie brakujących wartości, normalizację danych, dyskretyzację, przetwarzanie tekstu w celu usunięcia i/lub zastąpienia osadzonych znaków, które mogą mieć wpływ na wyrównanie danych, mieszane typy danych w typowych polach i inne.

**Usługa Azure Machine Learning zużywa dobrze uformowane dane tabelaryczne.**  Jeśli dane są już w formie tabelarycznej, wstępne przetwarzanie danych można wykonać bezpośrednio za pomocą usługi Azure Machine Learning Studio (klasyczny) w usłudze Machine Learning.  Jeśli dane nie są w formie tabelarycznej, powiedzmy, że jest w XML, analizowanie może być wymagane w celu przekonwertowania danych na formularz tabelaryczny.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jakie są główne zadania w przetwarzaniu wstępnym danych?
* **Czyszczenie danych**: Wypełnianie brakujących wartości, wykrywanie i usuwanie hałaśliwych danych i wartości odstających.
* **Transformacja danych**: Znormalizuj dane, aby zmniejszyć wymiary i hałas.
* **Redukcja danych:** Przykładowe rekordy danych lub atrybuty ułatwiające obsługę danych.
* **Discretization danych:** Konwertuj atrybuty ciągłe na atrybuty kategoryczne, aby ułatwić korzystanie z niektórych metod uczenia maszynowego.
* **Czyszczenie tekstu:** usuń osadzone znaki, które mogą powodować niewspółosiowość danych, na przykład osadzone karty w pliku danych oddzielonym kartą, osadzone nowe linie, które mogą na przykład bić rekordy.

W poniższych sekcjach opisano niektóre z tych kroków przetwarzania danych.

## <a name="how-to-deal-with-missing-values"></a>Jak radzić sobie z brakującymi wartościami?
Aby poradzić sobie z brakującymi wartościami, najlepiej najpierw zidentyfikować przyczynę brakujących wartości, aby lepiej obsługiwać problem. Typowe brakujące metody obsługi wartości to:

* **Usuwanie**: Usuwanie rekordów z brakującymi wartościami
* **Substytucja manekina**: Zastąp brakujące *unknown* wartości wartością manekina: np.
* **Średnie podstawienie**: Jeśli brakujące dane są numeryczne, zastąp brakujące wartości średnią.
* **Częste zastępowanie**: Jeśli brakujące dane są kategoryczne, zastąp brakujące wartości najczęstszym elementem
* **Podstawianie regresji**: Użyj metody regresji, aby zastąpić brakujące wartości wartości regressed.  

## <a name="how-to-normalize-data"></a>Jak normalizować dane?
Normalizacja danych ponownie skaluje wartości liczbowe do określonego zakresu. Popularne metody normalizacji danych obejmują:

* **Normalizacja min-Max:** Liniowo przekształcaj dane w zakres, powiedzmy od 0 do 1, gdzie wartość min jest skalowana do 0, a wartość maksymalna do 1.
* **Normalizacja z wynikiem:** Skaluj dane na podstawie średniej i odchylenia standardowego: podziel różnicę między danymi a średnią przez odchylenie standardowe.
* **Skalowanie dziesiętne:** Skaluj dane, przesuwając dziesiętny punkt wartości atrybutu.  

## <a name="how-to-discretize-data"></a>Jak dyskretnie dyskretnie ująć dane?
Dane można dyskretyzowane przez konwertowanie wartości ciągłych na atrybuty nominalne lub interwały. Oto kilka sposobów na to:

* **Binning o równej szerokości:** Podziel zakres wszystkich możliwych wartości atrybutu na grupy N o tym samym rozmiarze i przypisz wartości, które wchodzą do pojemnika z numerem pojemnika.
* **Równej wysokości Binning:** Podziel zakres wszystkich możliwych wartości atrybutu na grupy N, z których każda zawiera taką samą liczbę wystąpień, a następnie przypisz wartości, które wchodzą do pojemnika z numerem pojemnika.  

## <a name="how-to-reduce-data"></a>Jak ograniczyć dane?
Istnieją różne metody, aby zmniejszyć rozmiar danych w celu łatwiejszej obsługi danych. W zależności od rozmiaru danych i domeny można zastosować następujące metody:

* **Próbkowanie rekordów:** próbkowanie rekordów danych i wybieranie tylko reprezentatywnego podzbioru z danych.
* **Próbkowanie atrybutów:** Wybierz tylko podzbiór najważniejszych atrybutów z danych.  
* **Agregacja**: Podziel dane na grupy i przechowuj liczby dla każdej grupy. Na przykład dzienne przychody sieci restauracji w ciągu ostatnich 20 lat można zagregować z miesięcznymi przychodami, aby zmniejszyć rozmiar danych.  

## <a name="how-to-clean-text-data"></a>Jak czyścić dane tekstowe?
**Pola tekstowe w danych tabelaryczne** mogą zawierać znaki wpływające na wyrównanie kolumn i/lub granice rekordów. Na przykład osadzone karty w pliku oddzielonym tabulatorem powodują niewspółosiowość kolumny, a osadzone nowe znaki wiersza pobijają linie rekordów. Niewłaściwa obsługa kodowania tekstu podczas pisania lub czytania tekstu prowadzi do utraty informacji, niezamierzonego wprowadzenia nieczytelnych znaków (takich jak wartości null), a także może mieć wpływ na analizowanie tekstu. Staranne analizowanie i edytowanie może być wymagane w celu oczyszczenia pól tekstowych w celu prawidłowego wyrównania i/lub wyodrębnienia danych strukturalnych z danych tekstowych bez struktury lub półstrukturacyjnych.

**Eksploracja danych** oferuje wczesny wgląd w dane. Podczas tego kroku można odkryć szereg problemów z danymi i można zastosować odpowiednie metody w celu rozwiązania tych problemów.  Ważne jest, aby zadawać pytania, takie jak to, co jest źródłem problemu i jak problem mógł zostać wprowadzony. Ten proces pomaga również zdecydować o krokach przetwarzania danych, które należy podjąć, aby je rozwiązać. Określenie ostatecznych przypadków użycia i personas może również służyć do priorytetów nakładu przetwarzania danych.

## <a name="references"></a>Dokumentacja
> *Eksploracja danych: Koncepcje i techniki*, Edycja trzecia, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber i Jian Pei
> 
> 

