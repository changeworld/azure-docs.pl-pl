---
title: 'Split Data: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu podziału danych w usłudze Azure Machine Learning do dzielenia zestawu danych na dwa różne zestawy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029403"
---
# <a name="split-data-module"></a>Moduł podziału danych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do dzielenia zestawu danych na dwa różne zestawy.

Ten moduł jest szczególnie przydatne, gdy trzeba dzielenie danych szkoleniowych i zestawów testów. Można dostosować sposób, jak również podziału danych. Niektóre opcje obsługuje funkcję randomizacji danych. inne są dostosowane do określonych typu danych lub typ modelu.

## <a name="how-to-configure"></a>Jak skonfigurować

> [!TIP]
> Przed wybraniem Tryb podziału, przeczytaj wszystkie opcje, aby określić typ podziału, których potrzebujesz.
> Jeśli zmienisz Tryb podziału, można zresetować wszystkie inne opcje.

1. Dodaj **podziału danych** modułu do eksperymentu w interfejsie. Można znaleźć w tym module w ramach **przekształcania danych**w **próbki i podziału** kategorii.

2. **Tryb podziału**: Wybierz jedną z następujących trybów, w zależności od typu danych i jak chcesz podzielić ją. Każdy tryb podziału ma inne opcje. Kliknij poniższe tematy zawierają szczegółowe instrukcje i przykłady. 

    - **Dzielenie wierszy**: Użyj tej opcji, jeśli chcesz podzielić dane na dwie części. Można określić wartość procentowa danych należy umieścić w każdym podziału, ale domyślnie dane są dzielone na 50 – 50.

        Możesz również losowo wybranych wierszy w każdej grupie i używać stratyfikowana próbkowania. W przypadku próbkowania stratyfikowana należy wybrać pojedynczą kolumnę danych, dla którego chcesz wartości, które mają być równomiernie rozdzielona między wyniku dwa zestawy danych.  

    - **Regularne wyrażenia podziału** wybierz tę opcję, jeśli chcesz podzielić zestawu danych, testując pojedynczej kolumny dla wartości.

        Na przykład jeśli analizujesz opinii, można sprawdzić obecność określonego produktu nazwę w polu tekstowym, a następnie podzielić zestawu danych na wiersze, dla których nazwa produktu docelowego, a użytkownicy bez.

    - **Podziel wyrażenia względne**:  Ta opcja służy do zastosowania warunku, aby kolumna liczb. Liczba może być polem daty/godziny, w wieku zawierający kolumny lub dolarach lub nawet procent. Na przykład można podzielić zestawu danych w zależności od kosztu elementów grupy osób według zakresów wiek lub osobne dane przez datę w kalendarzu.

### <a name="split-rows"></a>Dzielenie wierszy
1.  Dodaj [podziału danych](./split-data.md) modułu do eksperymentu w interfejsie i Połącz z zestawu danych, którą chcesz podzielić.
  
2.  Aby uzyskać **tryb rozdzielenie**, wybierz **podziału wierszy**. 

3.  **Ułamek wierszy w pierwszym zestawie danych wyjściowych**. Użyj tej opcji, aby określić, ile wierszy są przekazywane do pierwszego (po lewej stronie) danych wyjściowych. Wszystkie pozostałe wiersze przejdzie do drugiego zestawu danych wyjściowych (po prawej stronie).

    Współczynnik reprezentuje odsetek wierszy wysyłane do pierwszym zestawie danych wyjściowych, dlatego należy wpisać liczbą dziesiętną od 0 do 1.
     
     Na przykład jeśli wpiszesz wartość 0,75 jako wartość będzie można podzielić zestawu danych przy użyciu współczynnika 75:25 75% wiersze wysyłane do pierwszego wyjściowy zestaw danych i 25% wysyłane do drugiego wyjściowy zestaw danych.
  
4. Wybierz **podziału Randomized** opcję, jeśli chcesz losowo ustawiać wybór danych na dwie grupy. Jest preferowaną opcją, podczas tworzenia zestawów danych szkoleniowych i testów.

5.  **Inicjator losowy**: Wpisz wartość nieujemną liczbą całkowitą, zainicjować pseudolosowych sekwencję wystąpień, które ma być używany. Ten domyślny inicjator jest używany we wszystkich modułach, które generują liczby losowe. 

     Określanie inicjatora sprawia, że wyniki ogólnie do odtworzenia. Jeśli należy powtórzyć wyniki operacji podziału, należy określić inicjatora dla generator liczb losowych. W przeciwnym razie Inicjator losowy jest domyślnie ustawiany na 0, co oznacza, że wartości początkowej są uzyskiwane z zegara systemowego. W wyniku rozkład danych może być nieco inne w każdym razem po wykonaniu podziału. 

6. **Podziel stratyfikowana**: Ustaw tę opcję na **True** aby upewnić się, że dwie wyjściowe zestawy danych zawierają została przeanalizowana reprezentatywna próbka wartości w *kolumny strata* lub *kolumny klucza stratyfikacji*. 

    Z włączonym próbkowaniem stratyfikowana, dane są dzielone w taki sposób, że każdy wyjściowy zestaw danych pobiera około samą wartość procentową poszczególnych wartości docelowej. Na przykład aby upewnić się, szkolenia i testowania zestawy są mniej więcej równoważone w odniesieniu do wyniku, lub z uwzględnieniem ot niektóre kolumny, takie jak płci.

7. Uruchom eksperyment.


## <a name="regular-expression-split"></a>Podziel wyrażeń regularnych

1.  Dodaj [podziału danych](./split-data.md) modułu do eksperymentu i połączyć ją jako dane wejściowe do zestawu danych, którą chcesz podzielić.  
  
2.  Dla **tryb rozdzielenie**, wybierz opcję **podziału wyrażenia regularnego**.

3. W **wyrażenia regularnego** wpisz prawidłowe wyrażenie regularne. 
  
   Wyrażenie regularne, należy stosować składni wyrażeń regularnych języka Python.


4. Uruchom eksperyment.

    Oparte na wyrażenie regularne, należy podać, zestaw danych jest podzielony na dwa zestawy wierszy: wierszy z wartościami, które odpowiadają wyrażenie i wszystkich pozostałych wierszy. 

## <a name="relative-expression-split"></a>Podziel wyrażenia względne.

1. Dodaj [podziału danych](./split-data.md) modułu do eksperymentu i połączyć ją jako dane wejściowe do zestawu danych, którą chcesz podzielić.
  
2. Dla **tryb rozdzielenie**, wybierz opcję **podziału wyrażenia względne**.
  
3. W **wyrażenie relacyjne** wpisz wiadomość wyrażenie, które wykonuje operację porównania w jednej kolumnie:


 - Kolumny liczbowej:
    - Kolumna zawiera cyfry dowolnego typu dane liczbowe, w tym typów danych daty/godziny.

    - Wyrażenie może odwoływać się maksymalnie jedna kolumna nazwy.

    - Użyj znaku handlowego "i" (&) dla operacji i użyj potoku znaków dla operacji OR (|).

    - Obsługiwane są następujące operatory: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Nie można grupować operacje przy użyciu `(` i `)`.

 - Kolumny ciągów: 
    - Obsługiwane są następujące operatory: `==`, `!=`



4. Uruchom eksperyment.

    Wyrażenie zestawu danych są podzielone na dwa zestawy wierszy: wierszy z wartościami, które spełniają warunek, a wszystkie pozostałe wiersze.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 