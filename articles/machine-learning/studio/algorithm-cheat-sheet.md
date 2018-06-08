---
title: Machine learning algorytmu ściągawka | Dokumentacja firmy Microsoft
description: Maszynę do druku arkusz ze wskazówkami dotyczącymi Algorytm uczenia pomaga wybrać prawa algorytmu dla modelu predykcyjnego w usłudze Azure Machine Learning Studio.
keywords: Algorytm przydatną ściągawkę, przydatną ściągawkę, algorytmu uczenia maszynowego
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
ms.openlocfilehash: a448d6931330f7b2f0730add65473097bb2b5a57
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833567"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Ściągawka dotycząca algorytmu uczenia maszynowego w usłudze Microsoft Azure Machine Learning Studio
**Microsoft Azure Machine Learning algorytmu Cheat arkusza** pomaga wybrać prawa algorytmu dla modelu analizy predykcyjnej.

[Azure Machine Learning Studio](https://studio.azureml.net/) ma dużej biblioteki algorytmów z ***regresji***, ***klasyfikacji***, ***klastrowanie***, i  ***wykrywanie anomalii*** rodziny. Każda jest przeznaczona do innego typu machine learning problemu adresów.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Pobieranie: Machine learning algorytmu ściągawka
**Pobierz arkusz ze wskazówkami w tym miejscu: [Learning Algorithm Cheat arkuszu maszyny (11 x 17 cali)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Arkusz ze wskazówkami dotyczącymi Algorytm uczenia maszyny: Dowiedz się, jak wybrać algorytm uczenia maszynowego.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Pobrać i wydrukować maszyny Learning algorytmu Cheat arkusza w rozmiarze tabloidu schowaj przydatne w celu uzyskania pomocy w wyborze algorytmu.

> [!NOTE]
> Zapoznaj się z artykułem [Wybieranie algorytmów w Microsoft Azure Machine Learning](algorithm-choice.md) szczegółowy przewodnik dotyczący korzystania z tego arkusza wskazówkami.
> 
> 

## <a name="more-help-with-algorithms"></a>Więcej informacji o algorytmy
* Aby uzyskać pomoc przy użyciu tego arkusza cheat dotyczące wybierania algorytm prawo, a także bardziej omówienie różne typy algorytmów uczenia maszynowego i sposób ich używania, zobacz [Wybieranie algorytmów w Microsoft Azure Machine Learning](algorithm-choice.md).
* Aby infographic do pobrania, opisujący algorytmów i przykłady, zobacz [Infographic do pobrania: Machine learning podstawy wraz z przykładami algorytmu](basics-infographic-with-algorithm-examples.md).
* Aby uzyskać listę według kategorii wszystkich algorytmów uczenia maszynowego dostępnych w usłudze Machine Learning Studio, zobacz [zainicjować modelu] [ initialize-model] Machine Learning Studio algorytmu i pomóc w Module.
* Aby uzyskać alfabetyczną listę wszystkich algorytmów i modułów w usłudze Machine Learning Studio, zobacz [A — Z listy modułów usługi Machine Learning Studio] [ a-z-list] algorytmu Studio uczenia maszynowego i pomóc w Module.
* Aby pobrać i wydrukować diagram, który zawiera przegląd możliwości usługi Machine Learning Studio, zobacz [Diagram przeglądowy możliwości usługi Azure Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Uwagi i definicje terminów dla Algorytm uczenia maszynowego ściągawka

* Sugestie oferowana w tym arkuszu, wskazówkami algorytmu są przybliżonej reguły z podglądu. Niektóre mogą być wygięte, a niektóre mogą flagrantly naruszone. Ta wartość jest przeznaczona do Sugeruj punkt początkowy. Nie jest niejasne, należy uruchomić head-to-head konkurencji między kilka algorytmów na podstawie danych. Nie jest po prostu nie zastąpi zrozumienie zasad poszczególnych algorytmów i opis systemu, które wygenerowało dane.

* Każdy algorytmu uczenia maszynowego ma własnego stylu lub *indukcyjna odchylenia*. Dla konkretnego problemu odpowiednie może być kilka algorytmów i jeden algorytm może być lepszym rozwiązaniem niż inne. Ale nie zawsze jest możliwe wcześniej znać, co jest najlepszym rozwiązaniem. W takich przypadkach kilka algorytmy są wyświetlane razem w przydatną ściągawkę. Właściwej strategii byłoby jeden algorytm, a jeśli wyniki nie są jeszcze zadowalające, ponów innych. Oto przykład z [galerii Azure AI](http://gallery.cortanaintelligence.com/) eksperymentu, która próbuje kilka algorytmów na tych samych danych i porównanie wyników: [porównania klasyfikatory wielu klas: list rozpoznawania](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Istnieją trzy główne kategorie uczenia maszynowego: **uczenia nadzorowanego**, **uczenie nienadzorowane**, i **learning wzmocnienie**.

  * W **uczenia nadzorowanego**, etykietą lub skojarzone z kategorii lub wartości istotnej każdego punktu danych.  Przykład etykiety kategorii jest przypisywanie obrazu jako "kot" lub "dog".  Przykład etykieta wartości jest skojarzone z używanego samochodu cenie sprzedaży. Celem uczenia nadzorowanego jest zbadanie wiele przykładów etykietą, takich jak te, a następnie, aby mieć możliwość tworzenia prognoz dotyczących punktów danych w przyszłości. Na przykład identyfikowanie nowe fotografie z poprawną zwierzę lub przypisywanie cen sprzedaży dokładne do innych samochodów używanych. Jest to popularnych i przydatne typu uczenia maszynowego. Wszystkie moduły w usłudze Azure Machine Learning są uczenia nadzorowanego algorytmów z wyjątkiem [klastrowanie K-średnich][k-means-clustering].

  * W **uczenie nienadzorowane**, punkty danych mają bez etykiet skojarzonych z nimi. Zamiast tego celem Algorytm uczenia nienadzorowanych jest organizowania danych w określony sposób lub do opisania jego struktury. Oznacza to, grupowanie w klastrach, tak jak w przypadku K-średnich lub znajdowania różne sposoby patrzeć dane złożone, aby był wyświetlany prostsze.

  * W **learning wzmocnienie**, algorytm pobiera wybranie akcji w odpowiedzi na każdym punkcie danych. Jest typowym podejściem w robotics, gdzie zbiór odczyty czujników w jednym punkcie w czasie jest punkt danych, a algorytm należy wybrać robota następnej akcji. Istnieje również fizyczną nadające się do aplikacji Internetu rzeczy. Algorytm uczenia również odbiera sygnał opłatą przez krótki czas później, wskazującą, jaka była decyzji. Na podstawie tych, algorytm modyfikuje strategią aby osiągnąć najwyższą osób trzecich. Obecnie nie ma żadnych wzmocnienie uczenia moduły Algorytm uczenia Maszynowego Azure.

* **Metody Estymacja Bayesian** założono statystycznie niezależnych punktów danych. Oznacza to, że unmodeled zmienności jednego punktu danych jest nieskorelowane osobom, oznacza to, że nie można przewidzieć. Na przykład dane rejestrowane w przypadku liczbę minut, aż do następnego pociągu całość dociera, dwa pomiary dziennie od siebie są statystycznie niezależne. Jednak dwóch pomiarów kilka minut od siebie nie są one statystycznie niezależne — wartość jednego jest wysoce predykcyjnej wartości z drugiej strony.

* **Boosted regresji drzewa decyzyjnego** korzysta z funkcji nakładają się na siebie lub interakcji między funkcjami. Który oznacza, że, w dowolnym momencie podanych danych wartość jedną funkcję nieco predykcyjnej innej wartości. Na przykład w codziennych danych temperatury wysokiej/niskie, uprzedniego uzyskania informacji o niskiej temperatury dnia umożliwia upewnij uzasadnione wynik w wysokich. Informacje zawarte w obu tych funkcji jest nieco nadmiarowy.

* Klasyfikacji danych na więcej niż dwie kategorie może odbywać się przy użyciu klasyfikatora z założenia wielu klas, lub łącząc zestaw klasyfikatory dwuklasowych do **zespół**. W ujęciu zespół jest oddzielny klasyfikatora dwuklasowych dla każdej klasy — każdy z nich oddziela dane do dwóch kategorii: "tej klasy" i "nie tej klasy." Następnie te klasyfikatory głosowania poprawne przydziału punktu danych. Jest to zasada operacyjne za [Multiklasa jedna kontra wszystkie][one-vs-all-multiclass].

* Załóżmy kilka metod, w tym Regresja logistyczna i maszyna punktu Bayesa, **granice liniowej klasy**. Oznacza to ich zakłada, że granice między klasami są około proste wierszy (lub hyperplanes w przypadku bardziej ogólne). Często jest to cecha dane, które nie znasz dopiero po próbowano oddzielić go, ale to coś, co zwykle mogą być rozpoznawane przez wizualizacja wcześniej. Jeżeli granice klasy wygląda bardzo nieregularne, przestrzegaj drzew decyzyjnych, decyzja dżungle, obsługuje wektor maszyny lub sieci neuronowej.

* Sieci neuronowe mogą być używane z kategorii zmiennymi przez tworzenie **fikcyjny zmiennej** dla każdej kategorii, zostanie ustawiona na 1 w przypadkach, w których ma zastosowanie kategorii 0, w którym nie.


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
