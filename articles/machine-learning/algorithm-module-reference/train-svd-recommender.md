---
title: 'Train SVD Recommender: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu train SVD Recommender w usłudze Azure Machine Learning do uczenia narzędzia bayesowskiego przy użyciu algorytmu SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477412"
---
# <a name="train-svd-recommender"></a>Trenowanie modułu poleceń SVD

W tym artykule opisano, jak używać modułu rekomendatora SVD pociągu w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Ten moduł służy do szkolenia modelu rekomendacji na podstawie algorytmu rozkładu pojedynczej wartości (SVD).  

Moduł Train SVD Recommender odczytuje zestaw danych potrójnych klasyfikacji przedmiotów użytkownika. Zwraca przeszkolony rekomendator SVD. Następnie można użyć przeszkolonego modelu do przewidywania ocen lub generowania zaleceń, przy użyciu [score SVD recommender](score-svd-recommender.md) modułu.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Więcej informacji o modelach rekomendacji i rekomendatorze SVD  

Głównym celem systemu rekomendacji jest polecanie *jednego* lub więcej *elementów* użytkownikom systemu. Przykładami elementu może być film, restauracja, książka lub utwór. Użytkownikiem może być osoba, grupa osób lub inna jednostka z preferencjami towaru.  

Istnieją dwa główne podejścia do systemów rekomendatorów: 

+ Podejście **oparte na zawartości** korzysta z funkcji zarówno dla użytkowników, jak i elementów. Użytkownicy mogą być opisane przez właściwości, takie jak wiek i płeć. Elementy mogą być opisane przez właściwości, takie jak autor i producent. Typowe przykłady systemów rekomendacji opartych na zawartości można znaleźć w witrynach dobierania graczy społecznościowych. 
+ **Wspólne filtrowanie** używa tylko identyfikatorów użytkowników i elementów. Pobiera niejawne informacje o tych jednostkach z (rozrzedzone) macierzy ocen podanych przez użytkowników do elementów. Możemy dowiedzieć się o użytkowniku z elementów, które ocenił i od innych użytkowników, którzy ocenili te same elementy.  

Polecający SVD używa identyfikatorów użytkowników i elementów oraz macierzy ocen nadanych przez użytkowników do elementów. Jest to *wspólny relecator.* 

Aby uzyskać więcej informacji na temat rekomendatora SVD, zobacz odpowiedni dokument badawczy: [Techniki faktoryzacji matrycy dla systemów rekomendacyjnych](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Jak skonfigurować train SVD Recommender  

### <a name="prepare-data"></a>Przygotowywanie danych

Przed użyciem modułu dane wejściowe muszą być w formacie, którego oczekuje model rekomendacji. Wymagany jest zestaw danych szkoleniowych potrójnych wartości elementu użytkownika.

+ Pierwsza kolumna zawiera identyfikatory użytkowników.
+ Druga kolumna zawiera identyfikatory elementów.
+ Trzecia kolumna zawiera klasyfikację dla pary element użytkownika. Wartości znamionowe muszą być typu numerycznego.  

Zestaw danych Oceny filmów w **projektancie** usługi Azure Machine Learning (wybierz **zestawy danych,** a następnie **przykłady)** pokazuje oczekiwany format:

![Oceny filmów](media/module/movie-ratings-dataset.png)

Z tego przykładu widać, że jeden użytkownik ocenił kilka filmów. 

### <a name="train-the-model"></a>Uczenie modelu

1.  Dodaj moduł Train SVD Recommender do potoku w projektancie i połącz go z danymi szkoleniowymi.  
   
2.  W przypadku **liczby czynników**należy określić liczbę czynników, które mają być używane z polecającym.  
    
    Każdy współczynnik mierzy, ile użytkownik jest związany z towarem. Liczba czynników jest również wymiarowość przestrzeni współczynnika utajonego. Wraz ze wzrostem liczby użytkowników i elementów lepiej ustawić większą liczbę czynników. Ale jeśli liczba jest zbyt duża, wydajność może spaść.
    
3.  **Liczba iteracji algorytmu rekomendacji** wskazuje, ile razy algorytm powinien przetwarzać dane wejściowe. Im wyższa jest ta liczba, tym dokładniejsze są prognozy. Jednak większa liczba oznacza wolniejsze szkolenie. Wartość domyślna to 30.

4.  W przypadku **wskaźnika uczenia się**wprowadź liczbę z 0,0 a 2,0, która określa rozmiar kroku do nauki.

    Szybkość uczenia się określa rozmiar kroku w każdej iteracji. Jeśli rozmiar kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt mały, szkolenie trwa dłużej, aby znaleźć najlepsze rozwiązanie. 
  
5.  Prześlij potok.  


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
