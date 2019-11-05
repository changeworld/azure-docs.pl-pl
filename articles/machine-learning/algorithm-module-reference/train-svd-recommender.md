---
title: 'Polecający ciąg SVD: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu polecania SVD "szkolenie" w usłudze Azure Machine Learning, aby szkolić Bayesowskiee z wykorzystaniem algorytmu SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515670"
---
# <a name="train-svd-recommender"></a>Polecający pociąg

W tym artykule opisano, jak używać modułu **polecania SVD** w programie Azure Machine Learning Designer (wersja zapoznawcza). Ten moduł służy do uczenia modelu rekomendacji na podstawie algorytmu **SVD** (Single Value dekompozycji).  

Moduł **polecający SVD pociąg** odczytuje zestaw danych potrójnych ocen elementów użytkownika. Zwraca zalecany polecający SVD. Można następnie użyć przeszkolonego modelu do przewidywania klasyfikacji lub wygenerowania zaleceń przy użyciu modułu [zalecanego do oceny SVD](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Więcej informacji na temat modeli rekomendacji i zalecenia dotyczącego SVD  

Głównym celem systemu rekomendacji jest zalecanie co najmniej jednego *elementu* *użytkownikom* systemu. Przykładami elementu może być film, restauracji, książka lub utwór. Użytkownik może być osobą, grupą osób lub inną jednostką z preferencjami elementu.  

Istnieją dwa główne podejścia do zalecanych systemów. 

+ Pierwszy jest podejściem **opartym na zawartości** , które umożliwia korzystanie z funkcji zarówno dla użytkowników, jak i elementów. Użytkownicy mogą być opisywany przez właściwości, takie jak wiek i płeć, a elementy mogą być opisane przez właściwości, takie jak autor i producent. Typowe przykłady systemów rekomendacji opartych na zawartości można znaleźć w witrynach społeczności Matchmaking. 
+ Drugim podejściem jest **filtrowanie do współpracy**, które używa tylko identyfikatorów użytkowników i elementów i uzyskuje niejawne informacje o tych jednostkach z macierzy (rozrzedzonej) ocen określonych przez użytkowników do elementów. Możemy dowiedzieć się więcej o użytkownikach z elementów, które zostały ocenione, oraz od innych użytkowników, którzy oceniali te same elementy.  

Polecający SVD używa identyfikatorów użytkowników i elementów oraz matrycy ocen określonych przez użytkowników do elementów. Jest to **polecana wspólnie**. 

Aby uzyskać więcej informacji na temat zalecenia SVD, zobacz odpowiedni dokument badawczy: [techniki factorization macierzy dla systemów zalecanych](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Jak skonfigurować zalecany polecający pociąg  

+ [Przygotuj dane szkoleniowe](#prepare-data)
+ [Uczenie modelu](#train-the-model)

### <a name="prepare-data"></a>Przygotowywanie danych

Przed próbą skorzystania z modułu należy koniecznie, aby dane były w formacie oczekiwanym przez model rekomendacji. Wymagany jest zestaw danych szkoleniowych o **trzech klasyfikacjach elementów użytkownika** .

#### <a name="required-dataset-of-user-item-ratings"></a>Wymagany zestaw danych dla klasyfikacji elementów użytkownika

Ważne jest, aby dane wejściowe używane do szkoleń zawierały dane odpowiedniego typu w poprawnym formacie: 

+ Pierwsza kolumna musi zawierać identyfikatory użytkowników.
+ Druga kolumna musi zawierać identyfikatory elementów.
+ Trzecia kolumna zawiera klasyfikację dla pary elementu User-Item. Wartości klasyfikacji muszą być typu liczbowego.  

                                                                                                                                                                                                           

Zestaw danych **klasyfikacji restauracji** w projektancie Azure Machine Learning (kliknij pozycję **zapisane zestawy** danych, a następnie **przykłady**) demonstruje oczekiwany format:

|Nazwa|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Z tego przykładu można zobaczyć, że jeden użytkownik ocenił dwa osobne Restauracje. 

### <a name="train-the-model"></a>Trenowanie modelu

1.  Dodaj moduł **polecający SVD (szkolenie** ) do potoku w Projektancie i podłącz go do danych szkoleniowych.  
   
2.  Dla **liczby czynników**wpisz liczbę określającą liczbę czynników do użycia z zaleceniem.  
    
    Każdy czynnik mierzy, jak dużo użytkownik jest powiązany z elementem. Liczba czynników jest również wymiarem obszaru ukryty obszar. W przypadku zwiększenia liczby użytkowników i elementów lepiej jest ustawić większą liczbę czynników. Jeśli jednak liczba jest zbyt duża, wydajność może się zmniejszyć.
    
3.  **Liczba iteracji algorytmu rekomendacji**wskazuje, ile razy algorytm powinien przetworzyć dane wejściowe. Im wyższy numer, tym dokładniejsze prognozy; szkolenie jest jednak wolniejsze. Wartość domyślna to 30.

4.  W polu **stawka szkoleniowa**wpisz liczbę z zakresu od 0,0 do 2,0, która definiuje rozmiar kroku podczas uczenia się.

    Szybkość uczenia określa rozmiar kroku podjętego w każdej iteracji. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie trwa dłużej niż w przypadku najlepszego rozwiązania. 
  
5.  Uruchamianie potoku.  


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
