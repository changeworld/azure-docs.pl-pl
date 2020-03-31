---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu SMOTE w usłudze Azure Machine Learning, aby zwiększyć liczbę przykładów o niskiej częstości występowania w zestawie danych przy użyciu przesączenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477463"
---
# <a name="smote"></a>SMOTE

W tym artykule opisano, jak używać modułu SMOTE w projektancie usługi Azure Machine Learning (wersja zapoznawcza) w celu zwiększenia liczby niedostatecznie reprezentowanych przypadków w zestawie danych, który jest używany do uczenia maszynowego. SMOTE jest lepszym sposobem na zwiększenie liczby rzadkich przypadków niż zwykłe powielanie istniejących przypadków.  

Moduł SMOTE można podłączyć do zestawu danych, który jest *niezrównoważony*. Istnieje wiele powodów, dla których zestaw danych może być niezrównoważony. Na przykład kategoria, na którą kierowane jest kierowanie, może być rzadka w populacji lub dane mogą być trudne do zebrania. Zazwyczaj używasz SMOTE, gdy *klasa,* która ma być analizowana, jest niedostatecznie reprezentowana. 
  
Moduł zwraca zestaw danych, który zawiera oryginalne przykłady. Zwraca również liczbę próbek mniejszości syntetycznych, w zależności od określonego procentu.  
  
## <a name="more-about-smote"></a>SMOTE – więcej informacji

Technika oversamplingu mniejszości syntetycznej (SMOTE) jest techniką statystyczną zwiększającą liczbę przypadków w zestawie danych w zrównoważony sposób. Moduł działa poprzez generowanie nowych wystąpień z istniejących przypadków mniejszości, które są podane jako dane wejściowe. To wdrożenie SMOTE *nie* zmienia liczby spraw większościowych.

Nowe wystąpienia to nie tylko kopie istniejących spraw mniejszościowych. Zamiast tego algorytm pobiera próbki *miejsca obiektowego* dla każdej klasy docelowej i jej najbliższych sąsiadów. Algorytm następnie generuje nowe przykłady, które łączą funkcje sprawy docelowej z funkcjami sąsiadów. Takie podejście zwiększa funkcje dostępne dla każdej klasy i sprawia, że próbki bardziej ogólne.
  
SMOTE przyjmuje cały zestaw danych jako dane wejściowe, ale zwiększa procent tylko przypadków mniejszości. Załóżmy na przykład, że masz niezrównoważony zestaw danych, w którym tylko 1 procent przypadków ma wartość docelową A (klasa mniejszości), a 99 procent przypadków ma wartość B. Aby zwiększyć procent przypadków mniejszości do dwukrotności poprzedniego procentu, należy wprowadzić **200** dla **procentu SMOTE** we właściwościach modułu.  
  
## <a name="examples"></a>Przykłady  

Zaleca się, aby spróbować użyć SMOTE z małym zestawem danych, aby zobaczyć, jak to działa. W poniższym przykładzie użyto zestawu danych krwiodawstwa dostępnego w projektancie usługi Azure Machine Learning.
  
Jeśli dodasz zestaw danych do potoku i wybierzesz **Wizualizacja** na danych wyjściowych zestawu danych, zobaczysz, że z 748 wierszy lub przypadków w zestawie danych, 570 przypadków (76 procent) są klasy 0 i 178 przypadków (24 procent) są klasy 1. Chociaż ten wynik nie jest strasznie niezrównoważony, klasa 1 reprezentuje osoby, które oddały krew, więc te wiersze zawierają *przestrzeń funkcji,* którą chcesz modelować.
 
Aby zwiększyć liczbę przypadków, można ustawić wartość **procentu SMOTE,** używając wielokrotności 100, w następujący sposób:

||Klasa 0|Klasa 1|suma|  
|-|-------------|-------------|-----------|  
|Oryginalny zestaw danych<br /><br /> (odpowiednik =  **procentu SMOTE****0)**|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**Procent SMOTE** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Procent SMOTE** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|**Procent SMOTE** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Zwiększenie liczby przypadków za pomocą SMOTE nie jest gwarantowane do tworzenia bardziej dokładnych modeli. Spróbuj potoku z różnych wartości procentowych, różnych zestawów operacji i różnych liczb najbliższych sąsiadów, aby zobaczyć, jak dodawanie przypadków wpływa na model.  
  
## <a name="how-to-configure-smote"></a>Jak skonfigurować SMOTE
  
1.  Dodaj moduł SMOTE do potoku. Moduł można znaleźć w obszarze **Moduły transformacji danych**, w kategorii **Manipulacja.**

2. Połącz zestaw danych, który chcesz promować. Jeśli chcesz określić miejsce operacji do tworzenia nowych przypadków, używając tylko określonych kolumn lub wykluczając niektóre, użyj wybierz kolumny w module [Zestaw danych.](select-columns-in-dataset.md) Następnie można wyizolować kolumny, których chcesz użyć przed użyciem funkcji SMOTE.
  
    W przeciwnym razie tworzenie nowych przypadków za pośrednictwem SMOTE opiera się na *wszystkich* kolumnach, które są podasz jako dane wejściowe. Co najmniej jedna kolumna kolumn elementów jest numeryczna.
  
3.  Upewnij się, że jest zaznaczona kolumna zawierająca etykietę lub klasę docelową. SMOTE akceptuje tylko etykiety binarne.
  
4.  Moduł SMOTE automatycznie identyfikuje klasę mniejszości w kolumnie etykiety, a następnie pobiera wszystkie przykłady dla klasy mniejszości. Wszystkie kolumny nie mogą mieć wartości NaN.
  
5.  W opcji **procentowej SMOTE** wprowadź liczbę całkowitą, która wskazuje docelowy procent przypadków mniejszości w wyjściowym zestawie danych. Przykład:  
  
    - Wprowadź **0**. Moduł SMOTE zwraca dokładnie ten sam zestaw danych, który został podany jako dane wejściowe. Nie dodaje żadnych nowych spraw mniejszościowych. W tym zestawie danych proporcja klasy nie uległa zmianie.  
  
    - Wprowadź **100**. Moduł SMOTE generuje nowe przypadki mniejszości. Dodaje taką samą liczbę przypadków mniejszości, które były w oryginalnym zestawie danych. Ponieważ SMOTE nie zwiększa liczby przypadków większości, odsetek przypadków każdej klasy uległ zmianie.  
  
    - Wprowadź **200**. Moduł podwaja odsetek przypadków mniejszości w porównaniu z oryginalnym zestawem danych. *Nie powoduje* to posiadania dwa razy więcej spraw mniejszościowych niż wcześniej. Przeciwnie, rozmiar zestawu danych zwiększa się w taki sposób, że liczba przypadków większości pozostaje taka sama. Liczba spraw mniejszości jest zwiększana, dopóki nie będzie zgodna z żądaną wartością procentową.  
  
    > [!NOTE]
    > Użyj tylko wielokrotności 100 dla procentu SMOTE.

6.  Użyj **opcji Liczba najbliższych sąsiadów,** aby określić rozmiar przestrzeni operacji używanej przez algorytm SMOTE do tworzenia nowych przypadków. Najbliższy sąsiad to wiersz danych (przypadek), który jest podobny do sprawy docelowej. Odległość między dowolnymi dwoma przypadkami jest mierzona przez połączenie wektorów ważonych wszystkich operacji.  
  
    + Zwiększając liczbę najbliższych sąsiadów, otrzymujesz funkcje z większej liczby przypadków.
    + Utrzymując liczbę najbliższych sąsiadów na niskim poziomie, można użyć funkcji, które są bardziej podobne do tych w oryginalnej próbce.  
  
7. Wprowadź wartość w **losowym polu źródłowym,** jeśli chcesz zapewnić takie same wyniki w przebiegach tego samego potoku, z tymi samymi danymi. W przeciwnym razie moduł generuje losowy materiał siewny na podstawie wartości zegara procesora po wdrożeniu potoku. Generowanie losowego materiału siewnego może powodować nieco inne wyniki w przebiegach.

8. Prześlij potok.  
  
   Dane wyjściowe modułu to zestaw danych zawierający oryginalne wiersze oraz liczbę dodanych wierszy z przypadkami mniejszości.  

## <a name="technical-notes"></a>Uwagi techniczne

+ Podczas publikowania modelu, który używa modułu **SMOTE,** usuń **SMOTE** z potoku predykcyjnego, zanim zostanie opublikowany jako usługa sieci web. Powodem jest to, że SMOTE jest przeznaczony do poprawy modelu podczas szkolenia, a nie do punktacji. Może pojawić się błąd, jeśli opublikowany potok predykcyjny zawiera moduł SMOTE.

+ Często można uzyskać lepsze wyniki, jeśli wyczyścisz brakujące wartości lub zastosujesz inne przekształcenia, aby naprawić dane przed zastosowaniem funkcji SMOTE. 

+ Niektórzy badacze zbadali, czy SMOTE jest skuteczny w odniesieniu do danych o wysokiej wymiarze lub rzadkich, takich jak dane wykorzystywane w klasyfikacji tekstu lub zestawach danych genomiki. Niniejszy dokument zawiera dobre podsumowanie skutków i teoretycznej ważności stosowania SMOTE w takich przypadkach: [Blagus i Lusa: SMOTE dla danych o wysokiej wartości wymiarowej nierównowagi klasowej](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Jeśli SMOTE nie jest skuteczne w zestawie danych, inne podejścia, które można rozważyć obejmują:
  + Metody przesapanie spraw mniejszościowych lub niedostateczętne rozpatrywanie większości przypadków.
  + Technika ensemble, które pomagają uczniowi bezpośrednio za pomocą klastrowania, bagging, lub adaptacyjne zwiększenie.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 

