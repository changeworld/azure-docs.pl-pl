---
title: Ściągawka dotycząca algorytmów uczenia maszynowego
titleSuffix: Azure Machine Learning Studio
description: Ściągawka dotycząca algorytmów uczenia maszynowego, drukowalnych pomaga w wyborze prawo algorytmu dla modelu predykcyjnego w usłudze Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 51a743e7578ea5bbc2acb9094bbf704a09f3cd6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752103"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Machine learning ściągawka dotycząca algorytmów dla usługi Azure Machine Learning Studio

**Azure Machine Learning Studio algorytm da się oszukać arkusza** pomaga w wyborze prawo algorytmu dla modelu analizy predykcyjnej.

[Usługa Azure Machine Learning Studio](https://studio.azureml.net/) ma dużej biblioteki algorytmów z ***regresji***, ***klasyfikacji***, ***klastrowania***, i  ***wykrywanie anomalii*** rodziny. Każdy jest przeznaczony do adresu innego typu maszyny nauczanym problemem.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Pobieranie: Ściągawka dotycząca algorytmów uczenia maszynowego

**Pobierz ściągawka tutaj: [W usłudze Machine Learning Algorithm Cheat-Sheet (11 x 17 cali)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Usługi Machine learning algorithm cheat-sheet: Dowiedz się, jak wybrać algorytm uczenia maszynowego.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png)

Pobrać i wydrukować Machine Learning Studio algorytm da się oszukać arkusza w rozmiarze tabloidu Utrzymaj wygodny, i uzyskać pomoc, wybieranie algorytmu.

> [!NOTE]
> Aby uzyskać pomoc przy użyciu ta ściągawka dotyczące wybierania prawo algorytm, a także bardziej dyskusję na temat różnych rodzajów algorytmów uczenia maszynowego i sposoby ich używania, zobacz [jak wybór algorytmów dla usługi Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Uwagi i definicji dla algorytmu usługi Machine Learning Studio ściągawka

* Sugestie, oferowana w tym ściągawka dotycząca algorytmów są przybliżone reguły z thumb. Niektóre mogą być wygięte, a niektóre mogą flagrantly naruszone. Ta wartość jest przeznaczona do zasugerować punkt początkowy. Nie jest niejasne, należy uruchomić head-to-head konkurencji między kilka algorytmów na podstawie posiadanych danych. Występuje po prostu nie zastąpi zrozumienie zasad poszczególnych algorytmów i system, które wygenerowało dane.

* Algorytmu uczenia maszynowego, co ma swój własny styl lub *indukcyjna odchylenie*. Dla konkretnego problemu odpowiednie może być kilku algorytmów i jeden algorytm może być lepszym rozwiązaniem niż inne. Ale nie zawsze jest możliwe, należy wcześniej wiedzieć, co jest najlepszym rozwiązaniem. W takich przypadkach kilka algorytmy są wyświetlane razem w cheat-sheet. Odpowiednią strategię byłoby jeden algorytm, a jeśli wyniki nie są jeszcze zadowalające, ponów innych. Oto przykład z [galerii Azure AI](https://gallery.azure.ai/) z eksperymentu, który próbuje kilku algorytmów na tych samych danych i porównanie wyników: [Porównaj klasyfikatorów wieloklasowej: Litera rozpoznawania](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

* Istnieją trzy główne kategorie usługi machine learning: **uczenia nadzorowanego**, **uczenie nienadzorowane**, i **uczenia przez wzmacnianie**.

  * W **uczenia nadzorowanego**, każdego punktu danych jest oznaczone lub skojarzone z kategorii lub wartość zainteresowania.  Przykład etykiety kategorii jest przypisywanie obrazu jako dog lub cat.  Przykład etykiety wartość to cena sprzedaży, skojarzone z używanych samochodów. Celem uczenia nadzorowanego jest zbadanie wiele przykładów etykietą, takie jak te, a następnie, aby można było tworzyć prognozy dotyczące punktów danych w przyszłości. Na przykład identyfikuje nowe zdjęcia z poprawną zwierzę lub przypisywanie sprzedaży dokładne ceny do innych używanych samochodów. Jest to typ popularnych i przydatne uczenia maszynowego. Wszystkie moduły w usłudze Azure Machine Learning Studio są uczenia nadzorowanego algorytmów, z wyjątkiem [klastrowania K-średnich][k-means-clustering].

  * W **uczenie nienadzorowane**, punkty danych mają bez etykiet skojarzonych z nimi. Zamiast tego celem algorytmu uczenia nienadzorowanych jest do organizowania danych, które w jakiś sposób, lub do opisania jego struktury. Oznacza to grupowanie w klastrach, tak jak K-średnich lub znajdowania różne sposoby spojrzenie na złożonych danych, tak aby była wyświetlana prostsze.

  * W **uczenia przez wzmacnianie**, pobiera algorytm w celu wybrania akcji w odpowiedzi na każdy punkt danych. Jest typowym podejściem w związane z robotyką, gdzie zestaw odczyty czujników w jednym punkcie w czasie jest punkt danych, a algorytm należy wybrać następne działanie robota. Również jest kluczem naturalnym rozwiązaniem dla aplikacji Internetu rzeczy. Algorytm uczenia otrzymuje również sygnał nagradzania przez krótki czas później, wskazującą, jak dobra była decyzji. Na tej podstawie algorytm modyfikuje podstawę strategii w celu uzyskania najwyższej nagrody. Aktualnie nie istnieją żadne wzmocnienie uczenie algorytmu moduły w usłudze Azure Machine Learning studio.

* **Metody Bayesowskie** przyjmujemy statystycznie niezależnych punktów danych. Oznacza to, nieskorelowane innym osobom niemodelowanych zmienności w jeden punkt danych, oznacza to, że nie można przewidzieć. Na przykład jeśli dane rejestrowane jest to liczba minut, po odebraniu dalej train całość, dwa pomiary jeden dzień od siebie są statystycznie niezależne. Jednak dwa pomiary minutę od siebie nie są statystycznie niezależne — wartość jednego jest wysoce predykcyjne wartości z drugiej strony.

* **Wzmocnione regresji drzewa decyzyjnego** korzysta z takich samych funkcji lub interakcji między funkcjami. Oznacza to, że wartość jedną funkcję w dowolnym punkcie danych jest nieco predykcyjne wartości innego. Na przykład w danych dotyczących dziennego temperatury wysoka/niska, wiedząc temperatura w ciągu dnia umożliwia zgadnąć uzasadnione w wysokich. Informacje zawarte w tych dwóch funkcji jest nieco nadmiarowe.

* Klasyfikowanie danych na więcej niż dwie kategorie może odbywać się przy użyciu klasyfikatora natury wielu klas lub łącząc zestaw klasyfikatorów dwuklasowych do **zespołu**. W przypadku zastosowania podejścia zespołu jest oddzielnym klasyfikatora dwuklasowych dla każdej klasy — każdy z nich oddziela dane na dwie kategorie: "Ta klasa" i "nie tej klasy." Następnie te klasyfikatorów głosować na prawidłowe przypisanie punktu danych. Jest to zasada operacyjnej za [jedna vs kontra wszystkie][one-vs-all-multiclass].

* Przyjęto założenie, kilka metod, takich jak regresji logistycznej i maszyna punktu Bayesa, **granice liniowej klasy**. Oznacza to zakładają też, że granice między klasami są około proste wierszy (lub hyperplanes w przypadku bardziej ogólne). Często jest to cecha dane, które nie wiesz, aż po sprawdzone oddzielić go, ale jest coś, co zwykle mogą być rozpoznawane przez wizualizację wcześniej. Spojrzeć bardzo nieregularne granice klasy trzymaj się drzewa decyzyjne, decision dżungle, pomocy technicznej maszyn wektor lub sieci neuronowych.

* Sieci neuronowych mogą być używane z kategorii zmiennych, tworząc **fikcyjnego zmiennej** dla każdej kategorii, zostanie ustawiona na 1 w przypadkach, w których ma zastosowanie kategorii 0, w którym nie.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacyjna do pobrania, opisujący algorytmów i przykłady, zobacz [grafika informacyjna do pobrania: Podstawy uczenia maszynowego z algorytmami](basics-infographic-with-algorithm-examples.md).

* Aby uzyskać listę według kategorii wszystkich algorytmów uczenia maszynowego dostępnych w usłudze Machine Learning Studio, zobacz [zainicjować modelu] [ initialize-model] algorytmu Studio uczenia maszynowego i pomóc w Module.

* Aby uzyskać pełną listę alfabetyczną algorytmów i modułów w usłudze Machine Learning Studio, zobacz [A-Z listy modułów usługi Machine Learning Studio] [ a-z-list] algorytmu Studio uczenia maszynowego i pomóc w Module.



<!-- Module References -->
[a-z-list]: /azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: /azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: /azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: /azure/machine-learning/studio-module-reference/one-vs-all-multiclass
