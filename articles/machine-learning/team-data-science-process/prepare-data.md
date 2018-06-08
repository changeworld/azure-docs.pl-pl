---
title: Czyszczenie i przygotowania danych do usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Wstępnie przetworzyć i czyszczenia danych, aby przygotować go do uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: bdf659ec-4881-4324-8b9c-747cbfa0c3cd
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 127c51b9a2617c6b8520d972a3cd4b6c3bbcddd1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837698"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego
Przetwarzanie wstępne i czyszczenia danych są ważne zadania, które zwykle należy przeprowadzić przed zestawu danych można skutecznie uczenia maszynowego. Dane pierwotne jest często zakłócenia i zawodnych i może brakować wartości. Przy użyciu tych danych do modelowania może wygenerować błędne wyniki. Te zadania należą do zespołu danych nauki procesu (TDSP) i zwykle wykonaj eksploracji początkowego zestawu danych używane do odnajdywania i planowanie przetwarzanie wstępne wymagane. Aby uzyskać szczegółowe instrukcje dotyczące procesu TDSP, zobacz czynności opisane w temacie [proces nauki danych zespołu](overview.md).

Przetwarzanie wstępne i czyszczenie zadania, takie jak zadanie Eksploracja danych, może zostać przeprowadzona w różnych środowiskach, takich jak SQL lub Hive lub Azure Machine Learning Studio oraz z różnych narzędzi i języków, takich jak R lub Python, w którym dane są przechowywane w zależności i sposób formatowania. Ponieważ TDSP ma charakter iteracyjny charakter, te zadania może odbywać się w różnych kroków w przepływie pracy procesu.

W tym artykule przedstawiono różne pojęcia dotyczące przetwarzania danych i zadań, które można podjąć przed lub po pozyskaniu danych do usługi Azure Machine Learning.

Na przykład Eksploracja danych i przetwarzanie wstępne wykonywane w usłudze Azure Machine Learning studio, zobacz [wstępne przetworzenie danych w usłudze Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) wideo.

## <a name="why-pre-process-and-clean-data"></a>Dlaczego wstępnie przetworzyć i czyszczenia danych?
Rzeczywistych dane są zbierane z różnych źródeł i procesy i może zawierać nieprawidłowości lub uszkodzone dane obniżania jakości zestawu danych. Problemy z jakości danych typowych nasuwające są:

* **Niekompletne**: danych nie ma atrybutów lub zawierające brakujące wartości.
* **Zakłócenia**: dane zawierają rekordy błędne lub odstające.
* **Niespójne**: dane zawierają rekordy powodujące konflikt lub niezgodności.

Jakości danych jest wymagane w przypadku modeli predykcyjnych jakości. Aby uniknąć "pamięci w pamięci out" i poprawy jakości danych i w związku z tym wzór wydajności, konieczne jest przeprowadzenie na wczesnym dodatkowe problemy danych i podejmowanie decyzji o odpowiednich przetwarzania danych i kroki czyszczenia ekranu kondycji danych.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Co to są niektóre ekrany kondycji typowych danych, które są wykorzystywane?
Sprawdzamy ogólnej jakości danych, sprawdzając:

* Liczba **rekordów**.
* Liczba **atrybuty** (lub **funkcje**).
* Atrybut **typy danych** (nominalnego, numer porządkowy lub ciągłe).
* Liczba **brakujące wartości**.
* **Składniowej** danych.
  * Jeśli dane są TSV lub CSV, sprawdź, czy kolumna separatorów i separatory wierszy zawsze poprawnie oddzielnych kolumn i wierszy.
  * Jeśli dane są w formacie HTML i XML, sprawdź, czy dane jest poprawnie sformułowany oparte na ich odpowiednich standardów.
  * Analiza kodu mogą być również konieczne w celu wyodrębnienia informacji o strukturze z częściową strukturą lub bez struktury danych.
* **Niespójne dane rekordów**. Sprawdź zakres wartości są dozwolone. np. Jeśli dane zawierają GPA studentów, sprawdź, czy GPA znajduje się w zasięgu wyznaczonym, powiedz 0 ~ 4.

Po znalezieniu problemy z danymi, **przetwarzania czynności** są niezbędne, który często pociąga za sobą czyszczenia brakujące wartości, normalizacji danych, dyskretyzacji, przetwarzanie tekstu do usunięcia i/lub Zastąp osadzonych znaków, które mogą wpływać na danych Wyrównanie mieszane typy danych wspólnych pól i inne.

**Usługa Azure Machine Learning zużywa danych tabelarycznych sformułowany**.  Jeśli dane są już w formie tabelarycznej, przetwarzanie wstępne danych można wykonać bezpośrednio z usługi Azure Machine Learning w Machine Learning Studio.  Jeśli dane nie są w formie tabelarycznej, że jest w formacie XML, analizy mogą być wymagane w celu Konwertuj dane w formie tabelarycznej.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jakie są najważniejsze zadania Przetwarzanie wstępne danych?
* **Czyszczenie danych**: wypełnić lub brakujące wartości, wykrywaniu i usuwaniu danych zakłócenia i wartości oddalonych.
* **Przekształcenia danych**: normalizacji danych, aby zmniejszyć wymiary i szumu.
* **Redukcja danych**: Przykładowe rekordów danych lub atrybutów w celu łatwiejszej obsługi danych.
* **Dyskretyzacji danych**: atrybuty ciągłe podzielone na kategorie atrybutów dla łatwość użycia polecenia Convert z niektórych machine learning metod.
* **Czyszczenie tekstu**: usunął osadzonych znaków, które mogą spowodować niespójność danych, np. kartach osadzone w pliku danych tabulatorem osadzonych nowe wiersze, które mogą być dzielone rekordów itp.

Poniższych sekcjach szczegółowo opisano niektóre z tych kroków przetwarzania danych.

## <a name="how-to-deal-with-missing-values"></a>Sposób postępowania w przypadku brakujących wartości?
Aby poradzić sobie z brakującymi wartościami, najlepiej najpierw zidentyfikować przyczynę brakujące wartości w celu lepszej obsługi problem. Typowe metody obsługi brakujące wartości są:

* **Usuwanie**: Usuń rekordy z brakującymi wartościami
* **Fikcyjny podstawienia**: Zamień brakujące wartości fikcyjnej wartości: np., *nieznany* dla kategorii lub równa 0 dla wartości liczbowe.
* **Oznacza podstawienia**: Jeśli brakujące dane liczbowe, Zamień brakujące wartości średniej.
* **Częste podstawienia**: Jeśli brakujące dane są podzielone na kategorie, Zastąp brakujące wartości najczęściej elementu
* **Podstawienie regresji**: Zamień brakujące wartości uwzględniona wartości przy użyciu metody regresji.  

## <a name="how-to-normalize-data"></a>Jak normalizacji danych?
Normalizacji danych skaluje ponownie wartości liczbowe do określonego zakresu. Metody normalizacji popularnych danych obejmują:

* **Min i Max normalizacji**: liniowo przekształcania danych do zakresu, co oznacza od 0 do 1, gdzie wartość minimalna skalowania 0 i maksymalną wartość na 1.
* **Wynik Z normalizacji**: skalować dane na podstawie średnią i odchylenie standardowe: dzielenie różnica między danymi i średniej przez odchylenie standardowe.
* **Skalowanie dziesiętną**: skalowania danych przez przeniesienie dziesiętnego wartości atrybutu.  

## <a name="how-to-discretize-data"></a>Jak mają być dyskretyzowane danych?
Dane można zdyskretyzować konwertując ciągłego wartości atrybutów nominalnego lub interwałów. W ten sposób niektóre sposoby są następujące:

* **Szerokość równości Binning**: podział zakresu wszystkich możliwych wartości atrybutu N grup ten sam rozmiar, a następnie przypisz wartości, które wchodzą w pojemniku o numerze bin.
* **Wysokość równości Binning**: podział zakresu wszystkich możliwych wartości atrybutu N grup, każda z nich zawiera taką samą liczbę wystąpień, a następnie przypisz wartości, które wchodzą w pojemniku o numerze bin.  

## <a name="how-to-reduce-data"></a>Jak zmniejszenia ilości danych?
Istnieją różne metody, aby zredukować rozmiar danych do obsługi danych, łatwiejsze. W zależności od rozmiaru danych i domeny można zastosować następujące metody:

* **Zarejestruj próbkowania**: Przykładowe rekordów danych i wybrać tylko reprezentatywnego podzestawu danych.
* **Atrybut próbkowania**: Wybierz tylko podzbiór atrybutów najważniejszych danych.  
* **Agregacja**: podział danych grup i przechowywania numerów dla każdej grupy. Na przykład codziennie numery przychodu łańcuch restauracji w ciągu ostatnich lat 20 można agregować do miesięcznych przychodów, aby zmniejszyć rozmiar danych.  

## <a name="how-to-clean-text-data"></a>Jak czyszczenie danych tekstowych?
**Pola tekstowe w danych tabelarycznych** może zawierać znaki, które wpływają na kolumny wyrównanie i/lub rekord granic. Np. osadzony karty w pliku tabulatorem niezgodność kolumny Przyczyna i osadzone znaki nowego wiersza Podziel rekordów wierszy. Obsługa kodowania tekstu niewłaściwy podczas zapisu/odczytu tekstu prowadzi do przypadkowego wprowadzenia znaki niemożliwe do odczytania, np. wartości null i może również wpływają na tekst analizowania utracie danych. Zachować ostrożność podczas analizowania i edytowania mogą być wymagane, aby wyczyścić pola tekstowe dla prawidłowego wyrównania i/lub strukturę wyodrębniania danych z danych bez struktury lub częściowo ustrukturyzowanych tekstu.

**Eksploracja danych** zapewnia wczesne wgląd w dane. Szereg problemów danych mogą być niepokrytego, w tym kroku i odpowiednie metody mogą być stosowane w celu rozwiązania tych problemów.  Należy zadać pytania, takie jak co to jest źródło problemu i jak problem mogły zostać wprowadzone. Pomaga to również zdecydować się na kroki przetwarzania danych, które należy podjąć w celu ich rozwiązania. Rodzaj szczegółowe informacje, które jedną zamierza pochodzić z danych można również priorytety nakładu pracy przetwarzania danych.

## <a name="references"></a>Dokumentacja
> *Wyszukiwania danych: Koncepcjach i technikach*, wydanie trzecie Morgan Kaufmann 2011 r. Jiawei Han, Micheline Kamber i Jian Pei
> 
> 

