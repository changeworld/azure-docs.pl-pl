---
title: Usługi Machine learning ściągawka dotycząca algorytmów — Azure | Dokumentacja firmy Microsoft
description: Ściągawka dotycząca algorytmów uczenia maszynowego, drukowalnych pomaga w wyborze prawo algorytmu dla modelu predykcyjnego w usłudze Azure Machine Learning Studio.
keywords: ściągawka dotycząca algorytmów, cheat-sheet, algorytmu uczenia maszynowego
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: b080a739aa73e3c8ef95c7db9a6358d942e94bba
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238390"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Ściągawka dotycząca algorytmu uczenia maszynowego w usłudze Microsoft Azure Machine Learning Studio
**Microsoft Azure uczenie algorytmu da się oszukać arkuszu maszyny** pomaga w wyborze prawo algorytmu dla modelu analizy predykcyjnej.

[Usługa Azure Machine Learning Studio](https://studio.azureml.net/) ma dużej biblioteki algorytmów z ***regresji***, ***klasyfikacji***, ***klastrowania***, i  ***wykrywanie anomalii*** rodziny. Każdy jest przeznaczony do adresu innego typu maszyny nauczanym problemem.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Pobieranie: Usługi Machine learning ściągawka dotycząca algorytmów
**Pobierz ściągawkę tutaj: [Machine Learning algorytm da się oszukać arkusz (11 x 17 cali)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Ściągawka dotycząca algorytmów uczenia maszyny: Dowiedz się, jak wybrać algorytm uczenia maszynowego.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Pobrać i wydrukować Machine Learning algorytm da się oszukać arkusza w rozmiarze tabloidu Utrzymaj wygodny, i uzyskać pomoc, wybieranie algorytmu.

> [!NOTE]
> Zapoznaj się z artykułem [jak wybór algorytmów dla usługi Microsoft Azure Machine Learning](algorithm-choice.md) szczegółowy przewodnik dotyczący przy użyciu ta ściągawka.
> 
> 

## <a name="more-help-with-algorithms"></a>Więcej pomocy przy użyciu algorytmów
* Aby uzyskać pomoc przy użyciu ta ściągawka dotyczące wybierania prawo algorytm, a także bardziej dyskusję na temat różnych rodzajów algorytmów uczenia maszynowego i sposoby ich używania, zobacz [jak wybór algorytmów dla usługi Microsoft Azure Machine Learning](algorithm-choice.md).
* Aby uzyskać informacyjna do pobrania, opisujący algorytmów i przykłady, zobacz [grafika informacyjna do pobrania: podstawy uczenia maszynowego z algorytmami](basics-infographic-with-algorithm-examples.md).
* Aby uzyskać listę według kategorii wszystkich algorytmów uczenia maszynowego dostępnych w usłudze Machine Learning Studio, zobacz [zainicjować modelu] [ initialize-model] algorytmu Studio uczenia maszynowego i pomóc w Module.
* Aby uzyskać pełną listę alfabetyczną algorytmów i modułów w usłudze Machine Learning Studio, zobacz [A-Z listy modułów usługi Machine Learning Studio] [ a-z-list] algorytmu Studio uczenia maszynowego i pomóc w Module.
* Aby pobrać i wydrukować diagram, który zawiera przegląd możliwości usługi Machine Learning Studio, zobacz [Diagram przeglądowy możliwości usługi Azure Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Uwagi i definicji dla algorytmu uczenia maszynowego ściągawka

* Sugestie, oferowana w tym ściągawka dotycząca algorytmów są przybliżone reguły z thumb. Niektóre mogą być wygięte, a niektóre mogą flagrantly naruszone. Ta wartość jest przeznaczona do zasugerować punkt początkowy. Nie jest niejasne, należy uruchomić head-to-head konkurencji między kilka algorytmów na podstawie posiadanych danych. Występuje po prostu nie zastąpi zrozumienie zasad poszczególnych algorytmów i opis systemu, które wygenerowało dane.

* Algorytmu uczenia maszynowego, co ma swój własny styl lub *indukcyjna odchylenie*. Dla konkretnego problemu odpowiednie może być kilku algorytmów i jeden algorytm może być lepszym rozwiązaniem niż inne. Ale nie zawsze jest możliwe, należy wcześniej wiedzieć, co jest najlepszym rozwiązaniem. W takich przypadkach kilka algorytmy są wyświetlane razem w cheat-sheet. Odpowiednią strategię byłoby jeden algorytm, a jeśli wyniki nie są jeszcze zadowalające, ponów innych. Oto przykład z [Galeria sztucznej Inteligencji platformy](http://gallery.cortanaintelligence.com/) z eksperymentu, który próbuje kilku algorytmów na tych samych danych i porównanie wyników: [porównania klasyfikatorów wieloklasowej: litera rozpoznawania](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Istnieją trzy główne kategorie usługi machine learning: **uczenia nadzorowanego**, **uczenie nienadzorowane**, i **uczenia przez wzmacnianie**.

  * W **uczenia nadzorowanego**, każdego punktu danych jest oznaczone lub skojarzone z kategorii lub wartość zainteresowania.  Przykład etykiety kategorii jest przypisywanie obrazu jako dog lub cat.  Przykład etykiety wartość to cena sprzedaży, skojarzone z używanych samochodów. Celem uczenia nadzorowanego jest zbadanie wiele przykładów etykietą, takie jak te, a następnie, aby można było tworzyć prognozy dotyczące punktów danych w przyszłości. Na przykład identyfikuje nowe zdjęcia z poprawną zwierzę lub przypisywanie sprzedaży dokładne ceny do innych używanych samochodów. Jest to typ popularnych i przydatne uczenia maszynowego. Wszystkie moduły w usłudze Azure Machine Learning są uczenia nadzorowanego algorytmów, z wyjątkiem [klastrowania K-średnich][k-means-clustering].

  * W **uczenie nienadzorowane**, punkty danych mają bez etykiet skojarzonych z nimi. Zamiast tego celem algorytmu uczenia nienadzorowanych jest do organizowania danych, które w jakiś sposób, lub do opisania jego struktury. Oznacza to grupowanie w klastrach, tak jak K-średnich lub znajdowania różne sposoby spojrzenie na złożonych danych, tak aby była wyświetlana prostsze.

  * W **uczenia przez wzmacnianie**, pobiera algorytm w celu wybrania akcji w odpowiedzi na każdy punkt danych. Jest typowym podejściem w związane z robotyką, gdzie zestaw odczyty czujników w jednym punkcie w czasie jest punkt danych, a algorytm należy wybrać następne działanie robota. Również jest kluczem naturalnym rozwiązaniem dla aplikacji Internetu rzeczy. Algorytm uczenia otrzymuje również sygnał nagradzania przez krótki czas później, wskazującą, jak dobra była decyzji. Na tej podstawie algorytm modyfikuje podstawę strategii w celu uzyskania najwyższej nagrody. Aktualnie nie istnieją żadne wzmocnienie uczenie algorytmu moduły w usłudze Azure ML.

* **Metody Bayesowskie** przyjmujemy statystycznie niezależnych punktów danych. Oznacza to, nieskorelowane innym osobom niemodelowanych zmienności w jeden punkt danych, oznacza to, że nie można przewidzieć. Na przykład jeśli dane rejestrowane jest to liczba minut, po odebraniu dalej train całość, dwa pomiary jeden dzień od siebie są statystycznie niezależne. Jednak dwa pomiary minutę od siebie nie są statystycznie niezależne — wartość jednego jest wysoce predykcyjne wartości z drugiej strony.

* **Wzmocnione regresji drzewa decyzyjnego** korzysta z takich samych funkcji lub interakcji między funkcjami. Oznacza to, że wartość jedną funkcję w dowolnym punkcie danych jest nieco predykcyjne wartości innego. Na przykład w danych dotyczących dziennego temperatury wysoka/niska, wiedząc temperatura w ciągu dnia umożliwia zgadnąć uzasadnione w wysokich. Informacje zawarte w tych dwóch funkcji jest nieco nadmiarowe.

* Klasyfikowanie danych na więcej niż dwie kategorie może odbywać się przy użyciu klasyfikatora natury wielu klas lub łącząc zestaw klasyfikatorów dwuklasowych do **zespołu**. W przypadku zastosowania podejścia zespołu jest oddzielnym klasyfikatora dwuklasowych dla każdej klasy — każdy z nich oddziela dane na dwie kategorie: "Ta klasa" i "nie tej klasy." Następnie te klasyfikatorów głosować na prawidłowe przypisanie punktu danych. Jest to zasada operacyjnej za [jedna vs kontra wszystkie][one-vs-all-multiclass].

* Przyjęto założenie, kilka metod, takich jak regresji logistycznej i maszyna punktu Bayesa, **granice liniowej klasy**. Oznacza to zakładają też, że granice między klasami są około proste wierszy (lub hyperplanes w przypadku bardziej ogólne). Często jest to cecha dane, które nie wiesz, aż po sprawdzone oddzielić go, ale jest coś, co zwykle mogą być rozpoznawane przez wizualizację wcześniej. Spojrzeć bardzo nieregularne granice klasy trzymaj się drzewa decyzyjne, decision dżungle, pomocy technicznej maszyn wektor lub sieci neuronowych.

* Sieci neuronowych mogą być używane z kategorii zmiennych, tworząc **fikcyjnego zmiennej** dla każdej kategorii, zostanie ustawiona na 1 w przypadkach, w których ma zastosowanie kategorii 0, w którym nie.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
