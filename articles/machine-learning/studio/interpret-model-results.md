---
title: Interpretowanie wyników modelu
titleSuffix: ML Studio (classic) - Azure
description: Jak wybrać optymalny zestaw parametrów dla algorytmu przy użyciu i wizualizacji wyników modelu wynik.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 9a0b855f48085138b28e02e0a5d01c5dd0f666be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218066"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretowanie wyników modelu w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
W tym temacie wyjaśniono, jak wizualizować i interpretować wyniki prognozowania w usłudze Azure Machine Learning Studio (klasyczny). Po przeszkoleniu modelu i wykonane prognoz na nim ("zdobył model"), należy zrozumieć i zinterpretować wynik prognozowania.



Istnieją cztery główne rodzaje modeli uczenia maszynowego w usłudze Azure Machine Learning Studio (klasyczne):

* Klasyfikacja
* Klastrowanie
* Regresja
* Systemy rekomendacyjne

Moduły używane do przewidywania na górze tych modeli są:

* [Moduł modelu wyników][score-model] dla klasyfikacji i regresji
* [Przypisywanie do modułu Klastry][assign-to-clusters] do klastrowania
* [Wynik Matchbox Recommender][score-matchbox-recommender] dla systemów rekomendacji

W tym dokumencie wyjaśniono, jak interpretować wyniki prognozowania dla każdego z tych modułów. Aby uzyskać omówienie tych modułów, zobacz [Jak wybrać parametry w celu optymalizacji algorytmów w usłudze Azure Machine Learning Studio (klasyczny)](algorithm-parameters-optimize.md).

Ten temat dotyczy interpretacji przewidywania, ale nie oceny modelu. Aby uzyskać więcej informacji na temat oceny modelu, zobacz [Jak ocenić wydajność modelu w usłudze Azure Machine Learning Studio (klasyczny)](evaluate-model-performance.md).

Jeśli jesteś nowym użytkownikiem usługi Azure Machine Learning Studio (classic) i potrzebujesz pomocy w tworzeniu prostego eksperymentu, aby rozpocząć, zobacz [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio (klasycznym).](create-experiment.md)

## <a name="classification"></a>Klasyfikacja
Istnieją dwie podkategorie problemów z klasyfikacją:

* Problemy tylko z dwiema klasami (klasyfikacja dwuklasowa lub binarna)
* Problemy z więcej niż dwiema klasami (klasyfikacja wieloklasowa)

Usługa Azure Machine Learning Studio (klasyczna) ma różne moduły do czynienia z każdym z tych typów klasyfikacji, ale metody interpretacji ich wyniki przewidywania są podobne.

### <a name="two-class-classification"></a>Klasyfikacja dwuklasowa
**Przykładowy eksperyment**

Przykładem problemu klasyfikacji dwóch klas jest klasyfikacja kwiatów tęczówki. Zadaniem jest klasyfikacja kwiatów tęczówki na podstawie ich cech. Zestaw danych Iris w usłudze Azure Machine Learning Studio (klasyczny) jest podzbiorem popularnego [zestawu danych Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) zawierającego wystąpienia tylko dwóch gatunków kwiatów (klasy 0 i 1). Istnieją cztery cechy dla każdego kwiatu (długość działki, szerokość działki, długość płatka i szerokość płatka).

![Zrzut ekranu przedstawiający eksperyment tęczówki](./media/interpret-model-results/1.png)

Rysunek 1. Eksperyment problemowy z klasyfikacją dwuklasową Iris

W celu rozwiązania tego problemu przeprowadzono eksperyment, jak pokazano na rysunku 1. Dwuklasowy model drzewa decyzyjnego został przeszkolony i oceniony. Teraz możesz wizualizować wyniki prognozowania z modułu [Score Model,][score-model] klikając port wyjściowy modułu [Score Model,][score-model] a następnie klikając pozycję **Wizualizuj**.

![Moduł modelu wyników](./media/interpret-model-results/1_1.png)

Spowoduje to wyświetlenie wyników punktacji, jak pokazano na rysunku 2.

![Wyniki eksperymentu klasyfikacji dwuklasowej tęczówki](./media/interpret-model-results/2.png)

Rysunek 2. Wizualizuj wynik modelu wyników w klasyfikacji dwuklasowej

**Interpretacja wyniku**

W tabeli wyników znajduje się sześć kolumn. Lewe cztery kolumny to cztery operacje. Po prawej dwie kolumny, Etykiety punktowane i Ocenione prawdopodobieństwa, są wyniki prognozowania. Kolumna Ocenione prawdopodobieństwo pokazuje prawdopodobieństwo, że kwiat należy do klasy dodatniej (klasa 1). Na przykład pierwsza liczba w kolumnie (0,028571) oznacza, że istnieje prawdopodobieństwo 0,028571, że pierwszy kwiat należy do klasy 1. Kolumna Ocenione etykiety pokazuje przewidywaną klasę dla każdego kwiatu. Jest to oparte na kolumnie Ocenione prawdopodobieństwa. Jeśli ocenione prawdopodobieństwo kwitnienia kwiatu jest większe niż 0,5, jest przewidywane jako klasa 1. W przeciwnym razie jest przewidywane jako klasa 0.

**Publikacja usługi sieci Web**

Po wyniki przewidywania zostały zrozumiane i oceniane dźwięku, eksperyment może być opublikowany jako usługi sieci web, dzięki czemu można wdrożyć go w różnych aplikacjach i wywołać go, aby uzyskać prognozy klasy na każdy nowy kwiat tęczówki. Aby dowiedzieć się, jak zmienić eksperyment szkoleniowy w eksperyment oceniania i opublikować go jako usługę sieci Web, zobacz [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md). Ta procedura zapewnia eksperyment oceniania, jak pokazano na rysunku 3.

![Zrzut ekranu przedstawiający eksperyment oceniania](./media/interpret-model-results/3.png)

Rysunek 3. Ocenianie eksperymentu problemowego klasyfikacji dwuklasowej tęczówki

Teraz musisz ustawić dane wejściowe i wyjściowe dla usługi sieci web. Dane wejściowe są właściwym portem wejściowym [Score Model][score-model], który jest wejściem funkcji kwiatu tęczówki. Wybór danych wyjściowych zależy od tego, czy jesteś zainteresowany przewidywaną klasą (etykieta punktowana), prawdopodobieństwem wyniku, czy obu. W tym przykładzie zakłada się, że jesteś zainteresowany w obu. Aby wybrać żądane kolumny wyjściowe, użyj modułu Wybierz kolumny w module [Zestaw danych.][select-columns] Kliknij [pozycję Wybierz kolumny w zestawie danych][select-columns], kliknij pozycję Uruchom selektor **kolumny**i wybierz pozycję **Ocenione etykiety** i **Ocenione prawdopodobieństwa**. Po ustawieniu portu wyjściowego [Wybierz kolumny w zestawie danych][select-columns] i ponownym uruchomieniu go, należy przystąpić do publikowania eksperymentu oceniania jako usługi sieci web, klikając pozycję **PUBLIKUJ USŁUGĘ SIECI WEB**. Ostateczny eksperyment wygląda jak rysunek 4.

![Eksperyment klasyfikacji dwuklasowej tęczówki](./media/interpret-model-results/4.png)

Rysunek 4. Ostateczny eksperyment punktacji problemu klasyfikacji dwuklasowej tęczówki

Po uruchomieniu usługi sieci web i wprowadzeniu niektórych wartości funkcji wystąpienia testu wynik zwraca dwie liczby. Pierwsza liczba to ocena, a druga to ocenione prawdopodobieństwo. Ten kwiat jest przewidywany jako klasa 1 z prawdopodobieństwem 0,9655.

![Testowanie modelu wyników interpretowania](./media/interpret-model-results/4_1.png)

![Wyniki testów oceniania](./media/interpret-model-results/5.png)

Rysunek 5. Wynik usługi sieci Web klasyfikacji dwuklasowej tęczówki

### <a name="multi-class-classification"></a>Klasyfikacja wieloklasowa
**Przykładowy eksperyment**

W tym eksperymencie należy wykonać zadanie rozpoznawania liter jako przykład klasyfikacji wieloklasowej. Klasyfikator próbuje przewidzieć pewną literę (klasę) na podstawie niektórych odręcznych wartości atrybutów wyodrębnionych z obrazów odręcznych.

![Przykład rozpoznawania liter](./media/interpret-model-results/5_1.png)

W danych szkoleniowych znajduje się 16 funkcji wyodrębnionych z odręcznych obrazów listów. 26 liter składa się z naszych 26 klas. Rysunek 6 przedstawia eksperyment, który będzie szkolić model klasyfikacji wieloklasowej do rozpoznawania liter i przewidywać na tym samym zestawie funkcji na zestawie danych testowych.

![Wieloklasowy eksperyment klasyfikacji rozpoznawania liter](./media/interpret-model-results/6.png)

Rysunek 6. Wieloklasowy eksperyment problemowy z klasyfikacją wieloklasową rozpoznawania liter

Wizualizacja wyników z modułu [Score Model,][score-model] klikając port wyjściowy modułu [Score Model,][score-model] a następnie klikając **przycisk Wizualizuj**, powinna zostać wyświetlona zawartość, jak pokazano na rysunku 7.

![Wyniki modelu wyników](./media/interpret-model-results/7.png)

Rysunek 7. Wizualizuj wyniki modelu wyników w klasyfikacji wieloklasowej

**Interpretacja wyniku**

Lewe 16 kolumn reprezentuje wartości elementów zestawu testów. Kolumny o nazwach takich jak Ocenione prawdopodobieństwa dla klasy "XX" są tak samo jak kolumna Ocenione prawdopodobieństwa w przypadku dwóch klas. Pokazują one prawdopodobieństwo, że odpowiednie wejście należy do określonej klasy. Na przykład dla pierwszego wpisu istnieje prawdopodobieństwo 0,003571, że jest to prawdopodobieństwo "A", 0,000451, że jest to "B" i tak dalej. Ostatnia kolumna (Ocenione etykiety) jest taka sama jak ocenione etykiety w przypadku dwóch klas. Wybiera klasę z największym prawdopodobieństwem ocenione jako przewidywane klasy odpowiedniego wpisu. Na przykład dla pierwszego wpisu oceniona etykieta jest "F", ponieważ ma największe prawdopodobieństwo bycia "F" (0.916995).

**Publikacja usługi sieci Web**

Można również uzyskać etykietę oceny dla każdego wpisu i prawdopodobieństwo etykiety punktowane. Podstawową logiką jest znalezienie największego prawdopodobieństwa spośród wszystkich ocenionych prawdopodobieństw. Aby to zrobić, należy użyć modułu [Wykonywanie skryptu R.][execute-r-script] Kod R jest pokazany na rysunku 8, a wynik eksperymentu jest pokazany na rysunku 9.

![Przykład kodu R](./media/interpret-model-results/8.png)

Rysunek 8. Kod R do wyodrębniania etykiet punktowanych i związanych z nimi prawdopodobieństw

![Wynik eksperymentu](./media/interpret-model-results/9.png)

Wykres 9. Ostateczny eksperyment punktacji problemu klasyfikacji wieloklasowej rozpoznawania liter

Po opublikowaniu i uruchomieniu usługi sieci web i wprowadzeniu niektórych wartości funkcji wejściowych zwrócony wynik wygląda jak rysunek 10. Ten odręczny list, z wyodrębnionych 16 funkcji, przewiduje się, że "T" z prawdopodobieństwem 0,9715.

![Moduł wyników interpretacji testowej](./media/interpret-model-results/9_1.png)

![Wynik testu](./media/interpret-model-results/10.png)

Wykres 10. Wynik usługi sieci Web klasyfikacji wieloklasowej

## <a name="regression"></a>Regresja
Problemy z regresją różnią się od problemów z klasyfikacją. W problem klasyfikacji próbujesz przewidzieć dyskretne klasy, takie jak klasa, do której należy kwiat tęczówki. Ale jak widać w poniższym przykładzie problemu regresji, próbujesz przewidzieć zmienną ciągłą, taką jak cena samochodu.

**Przykładowy eksperyment**

Użyj prognozowania cen samochodów jako przykład regresji. Próbujesz przewidzieć cenę samochodu na podstawie jego cech, w tym make, typu paliwa, typu nadwozia i koła napędowego. Eksperyment pokazano na rysunku 11.

![Eksperyment regresji cen samochodów](./media/interpret-model-results/11.png)

Wykres 11. Eksperyment problem z regresją cen samochodów

Wizualizując [wynik modelu][score-model] modułu, wynik wygląda rysunek 12.

![Wyniki punktacji problemu z przewidywaniem cen samochodów](./media/interpret-model-results/12.png)

Wykres 12. Wynik punktacji dla problemu przewidywania cen samochodów

**Interpretacja wyniku**

Ocena etykiety jest kolumna wynik w tym wynik punktacji. Liczby są przewidywaną ceną dla każdego samochodu.

**Publikacja usługi sieci Web**

Eksperyment regresji można opublikować w usłudze sieci web i wywołać go do przewidywania cen samochodów w taki sam sposób, jak w przypadku użycia klasyfikacji dwóch klas.

![Eksperyment punktacji dla problemu regresji cen samochodów](./media/interpret-model-results/13.png)

Wykres 13. Eksperyment punktacji problemu regresji cen samochodów

Uruchamianie usługi sieci web, zwrócony wynik wygląda rysunek 14. Przewidywana cena tego samochodu wynosi $15,085.52.

![Moduł oceniania interpretacji testowej](./media/interpret-model-results/13_1.png)

![Wyniki modułu oceniania](./media/interpret-model-results/14.png)

Wykres 14. Wynik usługi sieci Web problemu regresji cen samochodów

## <a name="clustering"></a>Klastrowanie
**Przykładowy eksperyment**

Użyjmy ponownie zestawu danych Iris do utworzenia eksperymentu klastrowania. W tym miejscu można odfiltrować etykiety klas w zestawie danych, tak aby miał tylko funkcje i może służyć do klastrowania. W tym przypadku użycia tęczówki określ liczbę klastrów, które mają być dwa podczas procesu szkolenia, co oznacza, że chcesz klastrować kwiaty w dwie klasy. Eksperyment pokazano na rysunku 15.

![Eksperyment z klastrem tęczówki](./media/interpret-model-results/15.png)

Wykres 15. Eksperyment z klastrem tęczówki

Klastrowanie różni się od klasyfikacji tym, że zestaw danych szkoleniowych nie ma etykiet prawdy gruntu. Grupowanie grupuje wystąpienia zestawu danych szkoleniowych w odrębne klastry. Podczas procesu szkolenia model etykiety wpisów, ucząc się różnic między ich funkcji. Następnie przeszkolony model może służyć do dalszej klasyfikacji przyszłych wpisów. Istnieją dwie części wyniku, który nas interesuje w ramach problemu klastrowania. Pierwsza część oznacza zestaw danych szkoleniowych, a druga klasyfikuje nowy zestaw danych z przeszkolonym modelem.

Pierwszą część wyniku można wizualizować, klikając lewy port wyjściowy [modelu klastrowania pociągu,][train-clustering-model] a następnie klikając pozycję **Wizualizuj**. Wizualizacja jest pokazana na rysunku 16.

![Wynik klastrowania](./media/interpret-model-results/16.png)

Wykres 16. Wizualizuj wynik klastrowania dla zestawu danych szkoleniowych

Wynik drugiej części, klastrowanie nowych wpisów z przeszkolonym modelem klastrowania, jest pokazany na rysunku 17.

![Wizualizuj wynik klastrowania](./media/interpret-model-results/17.png)

Wykres 17. Wizualizuj wynik klastrowania w nowym zestawie danych

**Interpretacja wyniku**

Chociaż wyniki obu części wynikają z różnych etapów eksperymentu, wyglądają tak samo i są interpretowane w ten sam sposób. Pierwsze cztery kolumny są funkcjami. Ostatnia kolumna, Przydziały, jest wynikiem prognozowania. Wpisy przypisane ten sam numer są przewidywane w tym samym klastrze, to jest, że mają podobieństwa w jakiś sposób (w tym eksperymencie używa domyślnej metryki odległości Euklidesa). Ponieważ określono liczbę klastrów do 2, wpisy w przydziałach są oznaczone jako 0 lub 1.

**Publikacja usługi sieci Web**

Eksperyment klastrowania można opublikować w usłudze sieci web i wywołać go do klastrowania prognoz w taki sam sposób, jak w przypadku użycia klasyfikacji dwóch klas.

![Eksperyment oceniania problemu klastrowania tęczówki](./media/interpret-model-results/18.png)

Wykres 18. Ocenianie eksperymentu problemu klastrowania tęczówki

Po uruchomieniu usługi sieci web zwrócony wynik wygląda jak rysunek 19. Przewiduje się, że ten kwiat znajdzie się w klastrze 0.

![Test interpretowania modułu oceniania](./media/interpret-model-results/18_1.png)

![Wynik modułu oceniania](./media/interpret-model-results/19.png)

Wykres 19. Wynik usługi sieci Web klasyfikacji dwuklasowej tęczówki

## <a name="recommender-system"></a>System polecający
**Przykładowy eksperyment**

W przypadku systemów polecającego możesz użyć problemu rekomendacji restauracji jako przykładu: możesz polecić restauracje dla klientów na podstawie ich historii klasyfikacji. Dane wejściowe składają się z trzech części:

* Oceny restauracji od klientów
* Dane funkcji klienta
* Dane dotyczące funkcji restauracji

Istnieje kilka rzeczy, które możemy zrobić z [train matchbox recommender][train-matchbox-recommender] moduł w usłudze Azure Machine Learning Studio (klasyczny):

* Przewidywanie ocen dla danego użytkownika i elementu
* Polecaj elementy dane dane dane dla danego użytkownika
* Znajdowanie użytkowników powiązanych z danym użytkownikiem
* Znajdowanie elementów związanych z danym elementem

Możesz wybrać, co chcesz zrobić, wybierając jedną z czterech opcji w menu **rodzaju przewidywania polecającego.** Tutaj można przejść przez wszystkie cztery scenariusze.

![Rekomendacja pudełka zapałek](./media/interpret-model-results/19_1.png)

Typowy eksperyment usługi Azure Machine Learning Studio (klasyczny) dla systemu polecania wygląda na rysunek 20. Aby uzyskać informacje na temat korzystania z tych modułów systemowych polecający, zobacz [Train matchbox recommender][train-matchbox-recommender] i [Score matchbox recommender][score-matchbox-recommender].

![Eksperyment z systemem polecający](./media/interpret-model-results/20.png)

Wykres 20. Eksperyment z systemem polecający

**Interpretacja wyniku**

**Przewidywanie ocen dla danego użytkownika i elementu**

Wybierając **prognozowanie klasyfikacji** w obszarze **Typ przewidywania rekomendującego,** prosisz system polecający o przewidywanie klasyfikacji dla danego użytkownika i elementu. Wizualizacja wynik [matchbox polecanie][score-matchbox-recommender] danych wygląda rysunek 21.

![Wynik systemu rekomendatora - przewidywanie klasyfikacji](./media/interpret-model-results/21.png)

Wykres 21. Wizualizuj wynik wyniku rekomendatora systemu — przewidywanie klasyfikacji

Pierwsze dwie kolumny są parami elementu użytkownika dostarczonymi przez dane wejściowe. Trzecia kolumna to przewidywana ocena użytkownika dla określonego elementu. Na przykład w pierwszym wierszu klient U1048 przewiduje się ocenić restauracji 135026 jako 2.

**Polecaj elementy dane dane dane dla danego użytkownika**

Wybierając **zalecenie elementu** w obszarze Rodzaj **przewidywania polecającego,** zwracasz się do systemu polecającego o polecanie elementów dane dane dla danego użytkownika. Ostatnim parametrem do wyboru w tym scenariuszu jest *wybór zalecanego elementu*. Opcja **Od elementów ocenionych (dla oceny modelu)** jest przede wszystkim dla oceny modelu podczas procesu szkolenia. Na tym etapie przewidywania wybieramy **spośród wszystkich elementów**. Wizualizacja wynik [matchbox polecanie][score-matchbox-recommender] danych wygląda rysunek 22.

![Wynik wyniku systemu polecacza -- rekomendacja pozycji](./media/interpret-model-results/22.png)

Wykres 22. Wizualizuj wynik wyniku rekomendujący system — zalecenie elementu

Pierwsza z sześciu kolumn reprezentuje podane identyfikatory użytkownika, dla które zalecają elementy, zgodnie z danymi wejściowymi. Pozostałe pięć kolumn reprezentuje elementy zalecane użytkownikowi w kolejności malejącej według istotności. Na przykład w pierwszym rzędzie najbardziej rekomendowana restauracja dla klienta U1048 to 134986, a następnie 135018, 134975, 135021 i 132862.

**Znajdowanie użytkowników powiązanych z danym użytkownikiem**

Wybierając **powiązanych użytkowników** w obszarze **Typ przewidywania polecacza,** zwracasz się do systemu polecającego o znalezienie powiązanych użytkowników dla danego użytkownika. Powiązani użytkownicy to użytkownicy, którzy mają podobne preferencje. Ostatnim parametrem do wybrania w tym scenariuszu jest *wybór użytkownika pokrewne*. Opcja **Od użytkowników, które oceniane elementy (dla oceny modelu)** jest przede wszystkim do oceny modelu podczas procesu szkolenia. Wybierz **spośród wszystkich użytkowników** dla tego etapu przewidywania. Wizualizacja wynik [matchbox polecanie][score-matchbox-recommender] danych wygląda rysunek 23.

![Wynik dla użytkowników systemu polecający](./media/interpret-model-results/23.png)

Wykres 23. Wizualizuj wyniki wyników użytkowników związanych z systemem polecacza

Pierwsza z sześciu kolumn zawiera podane identyfikatory użytkowników potrzebne do znalezienia powiązanych użytkowników, zgodnie z danymi wejściowymi. Pozostałe pięć kolumn przechowuje przewidywanych powiązanych użytkowników użytkownika w malejącej kolejności trafności. Na przykład w pierwszym wierszu najbardziej odpowiedni klient dla klienta U1048 jest U1051, a następnie U1066, U1044, U1017 i U1072.

**Znajdowanie elementów związanych z danym elementem**

Wybierając pozycje **pokrewne** w obszarze **Typ przewidywania polecającego**, prosisz system polecający o znalezienie powiązanych elementów do danego elementu. Powiązane elementy to elementy, które najprawdopodobniej będą lubione przez tego samego użytkownika. Ostatnim parametrem do wybrania w tym scenariuszu jest *Wybór elementu pokrewne*. Opcja **Od elementów ocenionych (dla oceny modelu)** jest przede wszystkim dla oceny modelu podczas procesu szkolenia. Wybieramy **spośród wszystkich elementów** dla tego etapu przewidywania. Wizualizacja wynik [matchbox polecanie][score-matchbox-recommender] danych wygląda rysunek 24.

![Wynik wyników elementów związanych z systemem polecający](./media/interpret-model-results/24.png)

Wykres 24. Wizualizuj wyniki wyników elementów związanych z systemem polecacza

Pierwsza z sześciu kolumn reprezentuje podane identyfikatory elementu potrzebne do znalezienia powiązanych elementów, zgodnie z danymi wejściowymi. Pozostałe pięć kolumn przechowuje przewidywane powiązane elementy towaru w porządku malejącym pod względem trafności. Na przykład w pierwszym wierszu najbardziej odpowiedni element dla pozycji 135026 to 135074, a następnie 135035, 132875, 135055 i 134992.

**Publikacja usługi sieci Web**

Proces publikowania tych eksperymentów jako usług sieci web, aby uzyskać prognoz jest podobny dla każdego z czterech scenariuszy. W tym miejscu bierzemy drugi scenariusz (polecam elementy do danego użytkownika) jako przykład. Możesz wykonać tę samą procedurę z pozostałymi trzema.

Zapisywanie wyszkolonego systemu polecającego jako przeszkolonego modelu i filtrowanie danych wejściowych do kolumny identyfikatora pojedynczego użytkownika zgodnie z żądaniem, można podłączyć eksperyment, jak na rysunku 25 i opublikować go jako usługę sieci web.

![Eksperyment punktacji problemu rekomendacji restauracji](./media/interpret-model-results/25.png)

Wykres 25. Eksperyment punktacji problemu rekomendacji restauracji

Uruchamianie usługi sieci web, zwrócony wynik wygląda rysunek 26. Pięć zalecanych restauracji dla użytkownika U1048 to 134986, 135018, 134975, 135021 i 132862.

![Przykład usługi systemu polecacza](./media/interpret-model-results/25_1.png)

![Przykładowe wyniki eksperymentu](./media/interpret-model-results/26.png)

Wykres 26. Wynik usługi sieci Web problemu z rekomendacją restauracji

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
