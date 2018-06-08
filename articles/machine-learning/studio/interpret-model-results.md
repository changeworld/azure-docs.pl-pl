---
title: Interpretowanie powoduje modelu uczenia maszynowego | Dokumentacja firmy Microsoft
description: Jak wybrać optymalne zestawu parametrów dla algorytmu przy użyciu i wizualizacja danych wyjściowych modelu wynik.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: e62f4716a95a6c1840afde27361eeb756abff335
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837008"
---
# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpretowanie wyników modelu w usłudze Azure Machine Learning
W tym temacie wyjaśniono, jak wizualizacji i interpretować wyniki prognozowania w usłudze Azure Machine Learning Studio. Po uczony model i gotowe prognoz na nim ("oceniane modelu"), należy zrozumieć i interpretować wyniki prognozowania.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Istnieją cztery główne rodzaje modeli w usłudze Azure Machine Learning uczenia maszynowego:

* Klasyfikacja
* Klastrowanie
* Regresja
* Systemy polecania

Moduły używane do prognozowania na podstawie tych modeli są:

* [Score Model] [ score-model] modułu dla klasyfikacji i regresji
* [Przypisz do klastrów] [ assign-to-clusters] modułu do klastrowania
* [Wynik polecania Matchbox] [ score-matchbox-recommender] dla systemów zalecenia

Tym dokumencie opisano sposób interpretowania przewidywania wyników dla każdego z tych modułów. Omówienie tych modułów, zobacz [Wybieranie parametry w celu zoptymalizowania algorytmy w usłudze Azure Machine Learning](algorithm-parameters-optimize.md).

Ten temat dotyczy interpretacji prognozowania, ale nie oceny modelu. Aby uzyskać więcej informacji na temat oceny modelu, zobacz [jak do oceny wydajności modelu w usłudze Azure Machine Learning](evaluate-model-performance.md).

Jeśli dopiero zaczynasz korzystać z usługi Azure Machine Learning i potrzebujesz pomocy, tworzenie prostego eksperymentu, aby rozpocząć pracę, zobacz [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md) w usłudze Azure Machine Learning Studio.

## <a name="classification"></a>Klasyfikacja
Istnieją dwie podkategorie klasyfikacji problemów:

* Problemy z klasami tylko dwa (Klasyfikacja dwuklasowych lub binarny)
* Problemy z więcej niż dwóch klas (Klasyfikacja wielu klas)

Usługa Azure Machine Learning ma różnych modułach radzenia sobie z każdego z tych typów klasyfikacji, ale metody interpretowanie ich wyniki prognozowania są podobne.

### <a name="two-class-classification"></a>Klasyfikacja dwa — klasa
**Przykład eksperymentu**

Przykład problemu klasyfikacji dwuklasowych to klasyfikacji kwiatów iris. Zadanie jest do klasyfikowania kwiatów iris na podstawie ich funkcji. Iris zestawu danych w usłudze Azure Machine Learning jest podzbiorem popularnych [zestawu danych Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) zawierający wystąpień tylko dwa kwiaty określa (klasy 0 i 1). Istnieją cztery funkcje dla każdego kwiat (długość sepal, szerokość sepal Motyw Płatek długości i szerokości Motyw Płatek).

![Zrzut ekranu przedstawiający iris eksperymentu](./media/interpret-model-results/1.png)

Rysunek 1. Iris dwuklasowych klasyfikacji problemu eksperymentu

Wykonano eksperymentu Aby rozwiązać ten problem, jak pokazano na rysunku 1. Model drzewa decyzji boosted dwuklasowych został uczenia i oceny. Teraz można wizualizować wyniki prognozowania z [Score Model] [ score-model] modułu, klikając portem wyjściowym [Score Model] [ score-model] modułu, a następnie klikając pozycję **Visualize**.

![Moduł score model](./media/interpret-model-results/1_1.png)

Spowoduje to wyświetlenie wyników oceniania jak pokazano na rysunku 2.

![Wyniki iris doświadczenia klasyfikacji dwa — klasa](./media/interpret-model-results/2.png)

Rysunek 2. Wizualizuj wynik score model klasyfikacji dwa — klasa

**Interpretacja wyników**

Brak sześć kolumn w tabeli wyników. Po lewej stronie cztery kolumny są cztery funkcje. Prawo dwie kolumny, etykiety oceniane i oceniane prawdopodobieństwa są wyniki prognozowania. Kolumna wynik prawdopodobieństwa zawiera prawdopodobieństwo kwiatu należącą do klasy dodatnią (Klasa 1). Na przykład pierwszy numer kolumny (0.028571) oznacza, że jest 0.028571 prawdopodobieństwo, że pierwszy kwiat należy do klasy 1. Kolumna oceniane etykiety zawiera przewidywane klasy dla każdego kwiat. To jest oparta na kolumnie wynik prawdopodobieństwa. W przypadku większych niż 0,5 scored prawdopodobieństwo kwiatu jest obliczana jako klasy 1. W przeciwnym razie wartość jest obliczana jako klasa 0.

**Publikowania usług sieci Web**

Po uwzględnione wyniki prognozowania i ocenić dźwięku eksperyment mogą być publikowane jako usługę sieci web tak, aby można go wdrożyć w różnych aplikacji i nadaj mu uzyskanie prognoz klasy na nowe kwiatem iris. Aby dowiedzieć się, jak zmienić eksperyment uczenia w eksperymencie oceniania i opublikujesz je jako usługi sieci web, zobacz [opublikować usługi sieci web Azure Machine Learning](walkthrough-5-publish-web-service.md). Ta procedura umożliwia eksperyment oceniania jak pokazano na rysunku 3.

![Zrzut ekranu przedstawiający oceniania eksperymentu](./media/interpret-model-results/3.png)

Rysunek 3. Ocenianie eksperymentu problemu klasyfikacji dwuklasowych iris

Teraz należy ustawić dane wejściowe i wyjściowe dla usługi sieci web. Dane wejściowe są prawy port wejściowy z [Score Model][score-model], czyli kwiat Iris funkcji danych wejściowych. Wybór danych wyjściowych zależy od tego, czy myślisz przewidywanych klasy (etykieta scored) i scored prawdopodobieństwa. W tym przykładzie zakłada się, że jesteś zainteresowany zarówno w. Aby zaznaczyć kolumny, żądanego wyniku, użyj [Wybieranie kolumn w zestawie danych] [ select-columns] modułu. Kliknij przycisk [Wybieranie kolumn w zestawie danych][select-columns], kliknij przycisk **Uruchom selektor kolumn**i wybierz **oceniane etykiety** i **wynik prawdopodobieństwa**. Po ustawieniu portem wyjściowym [Wybieranie kolumn w zestawie danych] [ select-columns] i uruchomić ponownie, możesz przystąpić do publikowania oceniania eksperymentu jako usługę sieci web, klikając **OPUBLIKOWAĆ usługi sieci WEB**. Końcowy eksperyment wygląda jak rysunek 4.

![Iris eksperymentu klasyfikacji dwa — klasa](./media/interpret-model-results/4.png)

Rysunek 4. Końcowy eksperyment oceniania iris dwuklasowych klasyfikacji problemu

Po uruchomieniu usługi sieci web i wprowadź wartości niektórych funkcji wystąpienia testu, wynik zwraca dwóch liczb. Pierwsza liczba scored etykiety, a drugim scored prawdopodobieństwa. Ta kwiat jest przewidzieć jako klasy 1 z 0.9655 prawdopodobieństwa.

![Model interpretowania wynik testu](./media/interpret-model-results/4_1.png)

![Ocenianie wyników testu](./media/interpret-model-results/5.png)

Rysunek 5. Wynik usługi sieci Web iris dwuklasowych klasyfikacji

### <a name="multi-class-classification"></a>Klasa usługi klasyfikacji
**Przykład eksperymentu**

W tym eksperymencie możesz wykonać zadania rozpoznawania litery jako przykład wieloklasowej klasyfikacji. Klasyfikator próbuje przewidzieć określonej litery (klasa) na podstawie niektóre wartości atrybutu odręcznego wyodrębniony z odręcznego obrazów.

![Przykład rozpoznawania list](./media/interpret-model-results/5_1.png)

W dane szkoleniowe są funkcje 16 wyodrębniony z odręcznego list obrazów. 26 liter tworzą naszych 26 klasy. Rysunek 6 przedstawia eksperymentu, która będzie uczenie modelu przy użyciu wieloklasowej klasyfikacji rozpoznawania litery i prognozowania na tej samej funkcji ustawiać testowego zestawu danych.

![Litera rozpoznawania wieloklasowej klasyfikacji eksperymentu](./media/interpret-model-results/6.png)

Rysunek 6. Litera rozpoznawania wieloklasowej klasyfikacji problemu eksperymentu

Wizualizacja wyników z [Score Model] [ score-model] modułu, klikając portem wyjściowym [Score Model] [ score-model] modułu, a następnie klikając pozycję **Visualize**, jak pokazano na rysunku 7 powinna zostać wyświetlona zawartość.

![Score model wyników](./media/interpret-model-results/7.png)

Rysunek 7. Wizualizacja wyników klasyfikacji modelu w klasyfikacji wielu — klasa

**Interpretacja wyników**

Po lewej stronie 16 kolumn reprezentują wartości funkcji zestawu testowego. Kolumny o nazwach takich jak wynik prawdopodobieństwa dla klasy "XX", są tak, takich jak kolumny prawdopodobieństwa oceniane w przypadku dwóch klasy. Pokazują one prawdopodobieństwo wykraczające odpowiadający mu wpis w niektórych klasy. Na przykład dla pierwszej pozycji istnieje 0.003571 prawdopodobieństwo, że jest "A," 0.000451 prawdopodobieństwo, że jest "B" i tak dalej. Ostatnia kolumna (etykiety wynik) jest taka sama jak oceniane etykiet w przypadku dwóch klasy. Klasy z największych prawdopodobieństwo scored zostanie wybrana jako przewidywane klasy odpowiedniego zapisu. Na przykład dla pierwszej pozycji scored etykiety jest "F", ponieważ ma ona największą prawdopodobieństwo "F" (0.916995).

**Publikowania usług sieci Web**

Można również uzyskać scored etykiety dla każdego wpisu i prawdopodobieństwo scored etykiety. Podstawowa logika jest teraz znaleźć największą prawdopodobieństwo wśród wszystkich scored probabilities. Aby to zrobić, należy użyć [wykonanie skryptu języka R] [ execute-r-script] modułu. Kod języka R jest pokazano na rysunku 8, a wynik eksperyment przedstawiono na rysunku 9.

![Przykład kodu języka R](./media/interpret-model-results/8.png)

Rysunek 8. Kod języka R wyodrębniania oceniane etykiety i prawdopodobieństwa etykiet

![Wynikiem eksperymentu](./media/interpret-model-results/9.png)

Rysunek 9. Końcowy eksperyment oceniania rozpoznawania litery wieloklasowej klasyfikacji problemu

Po opublikowaniu i uruchamiania usługi sieci web oraz wprowadzić niektóre wartości wejściowe funkcji prawdopodobnie zwróconych wyników na rysunku nr 10. Ta litera odręcznego, z wyodrębnionego funkcje 16, jest przewidzieć jako "T" z 0.9715 prawdopodobieństwa.

![Moduł interpretowania wynik testu](./media/interpret-model-results/9_1.png)

![Wynik testu](./media/interpret-model-results/10.png)

Rysunek 10. Wynik usługi sieci Web wieloklasowej klasyfikacji

## <a name="regression"></a>Regresja
Regresja problemów różnią się od klasyfikacji problemów. Problem klasyfikacji próbujesz odrębny klas, takie jak klasa kwiat iris należy przewidzieć. Jednak jak widać w poniższym przykładzie problem regresji próbujesz prognozowania ciągłego zmienną, np. ceny samochodu.

**Przykład eksperymentu**

Używanie Prognozowanie cen samochodów jako przykład Twoje regresji. Chcesz przewidzieć cenę samochodu oparte na jej funkcje, takie jak upewnij, typu paliwa typem treści i pokrętła stacji. Rysunek 11 pokazano eksperymentu.

![Eksperymentu opartego na regresji cen samochodów](./media/interpret-model-results/11.png)

Rysunek 11. Cen samochodów regresji problem eksperymentu

Wizualizacja [Score Model] [ score-model] modułu, wynik wygląda jak rysunek 12.

![Wyników oceniania problemu prognozowania cen samochodów](./media/interpret-model-results/12.png)

Rysunek 12. Ocenianie wyniku problemu prognozowania cen samochodów

**Interpretacja wyników**

Scored etykiety jest kolumną wyników oceniania wynik. Liczby są przewidywane ceny dla każdego samochodu.

**Publikowania usług sieci Web**

Można opublikować eksperymentu opartego na regresji do usługi sieci web i wywołać go do prognozowania cen samochodów w taki sam sposób jak w przypadku użycia dwuklasowych klasyfikacji.

![Ocenianie eksperymentu cen samochodów regresji problemu](./media/interpret-model-results/13.png)

Rysunek 13. Ocenianie eksperymentu problem regresji cen samochodów

Uruchomiona usługa sieci web, zwrócony wynik wygląda na rysunku 14. Przewidywane cenę samochodu ten jest 15,085.52 $.

![Moduł interpretowania wyników testu](./media/interpret-model-results/13_1.png)

![Wyników oceniania modułu](./media/interpret-model-results/14.png)

Rysunek 14. Wynik usługi sieci Web wystąpił problem regresji cen samochodów

## <a name="clustering"></a>Klastrowanie
**Przykład eksperymentu**

Użyjmy Iris zestawu danych ponownie do tworzenia klastrów eksperymentu. W tym miejscu można odfiltrować klasy etykiet w zestawie danych tak, aby tylko ma funkcje i może służyć do klastra. W tym iris przypadek użycia, określ liczbę klastrów, aby być dwa podczas procesu szkolenia, co oznacza, że należy umieścić klastrze kwiaty na dwie klasy. Eksperyment pokazano na rys. 15.

![Problem klastrowania Iris eksperymentu](./media/interpret-model-results/15.png)

Rysunek 15. Problem klastrowania Iris eksperymentu

Klastrowanie różni się od klasyfikacji, w tym szkoleniowy zestaw danych nie ma podstaw prawdy etykiety samodzielnie. Klastrowanie grup wystąpień zestawu danych szkoleniowych w różnych klastrach. W trakcie uczenia modelu etykiet wpisy przez uczenia różnice między ich funkcje. Po wykonaniu tej trenowanego modelu może służyć do dalszej klasyfikacji przyszłych wpisów. Istnieją dwie części wynik, który NAS interesuje w ramach klastrowania problem. Pierwsza część jest etykietowania szkoleniowy zestaw danych, a drugi jest klasyfikowanie nowy zestaw danych z trenowanego modelu.

Pierwsza część wynik może wizualizowane przez kliknięcie lewym portem wyjściowym [Train Model klastrowanie] [ train-clustering-model] , a następnie klikając polecenie **Visualize**. Wizualizacja przedstawiono na rysunku 16.

![Wynik klastrowania](./media/interpret-model-results/16.png)

Rysunek 16. Wizualizowanie klastra wynik szkoleniowy zestaw danych

Wynik drugiej części, nowe wpisy uczonego modelu klastrowania z klastra jest wyświetlana w rysunek 17.

![Wizualizowanie klastra wyników](./media/interpret-model-results/17.png)

Rysunek 17. Wizualizowanie klastra wyników na nowym zestawie danych

**Interpretacja wyników**

Mimo że wyniki z dwóch części jest wynikiem eksperymentu różne etapy, wygląd i interpretowania w taki sam sposób. Pierwsze cztery kolumny są funkcje. Ostatnia kolumna przydziałów, jest wynikiem prognozowania. Wpisy przypisany ten sam numer oczekuje się w tym samym klastrze, oznacza to, że mają podobieństwa w jakiś sposób (odległość euklidesowa metrykę używa tego eksperymentu). Ponieważ określonej liczby klastrów 2 wpisów w przypisaniach są oznaczone 0 lub 1.

**Publikowania usług sieci Web**

Można opublikować klastrowania eksperymentu w usłudze sieci web i wywołać ją do klastrowania prognoz tak samo jak Klasyfikacja dwuklasowych przypadek użycia.

![Ocenianie eksperymentu iris klastrowania problemu](./media/interpret-model-results/18.png)

Rysunek 18. Ocenianie eksperymentu iris problem klastrowania

Po uruchomieniu usługi sieci web zwrócony wynik wygląda jak rysunek 19. Kwiat ta jest przydatna w klastrze 0.

![Moduł oceny zinterpretować testu](./media/interpret-model-results/18_1.png)

![Ocenianie wynik modułu](./media/interpret-model-results/19.png)

Rysunek 19. Wynik usługi sieci Web iris dwuklasowych klasyfikacji

## <a name="recommender-system"></a>System polecania
**Przykład eksperymentu**

Dla systemów polecania problem zalecenie restauracji można użyć na przykład: można zalecić restauracji dla klientów w oparciu o historię ich klasyfikacji. Dane wejściowe składa się z trzech części:

* Klasyfikacje restauracji od klientów
* Dane funkcji klienta
* Dane funkcji restauracji

Istnieje kilka sposobów, możemy z [polecania Matchbox pociągu] [ train-matchbox-recommender] modułu w usłudze Azure Machine Learning:

* Przewidywanie oceny dla określonego użytkownika i elementu
* Zaleca się elementy do danego użytkownika
* Znajdź użytkowników, związane z danego użytkownika
* Znajdź elementy powiązane z danego elementu

Można wybrać, co chcesz zrobić, wybierając z czterech opcji **rodzaj prognozowania polecania** menu. W tym miejscu można przeprowadzenie wszystkie cztery scenariusze.

![Matchbox polecania](./media/interpret-model-results/19_1.png)

Typowy eksperymentu uczenia maszynowego Azure dla systemu polecania wygląda na rysunku 20. Aby uzyskać informacje o sposobie używania moduły polecania systemu, zobacz [polecania matchbox pociągu] [ train-matchbox-recommender] i [polecania matchbox wynik][score-matchbox-recommender].

![Polecania systemu eksperymentu](./media/interpret-model-results/20.png)

Rysunek 20. Polecania systemu eksperymentu

**Interpretacja wyników**

**Przewidywanie oceny dla określonego użytkownika i elementu**

Wybierając **prognozowania klasyfikacji** w obszarze **rodzaj prognozowania polecania**, prośbę o system polecania do prognozowania wartości dla określonego użytkownika i elementu. Obraz [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco rysunek 21.

![Wynik wynik systemu polecania — ocena prognozowania](./media/interpret-model-results/21.png)

Rysunek 21. Wizualizacja wyników oceny systemu polecania — ocena prognozowania

Pierwsze dwie kolumny są par element użytkownika w danych wejściowych. Trzecia kolumna jest przewidywane klasyfikacja użytkownika dla danego elementu. Na przykład w pierwszym wierszu klienta U1048 jest przydatna do restauracji szybkość 135026 2.

**Zaleca się elementy do danego użytkownika**

Wybierając **zalecenie elementu** w obszarze **rodzaj prognozowania polecania**, jest wysyłane żądanie systemu polecania zaleca elementów do danego użytkownika. Ostatni parametr, aby wybrać w tym scenariuszu jest *zaleca wybór elementu*. Opcja **z oceną elementy (ocena modelu)** jest przeznaczone głównie dla oceny modelu podczas procesu szkolenia. Dla tego etapu prognozowania wybieramy opcję **z wszystkich elementów**. Obraz [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco rysunek 22.

![Wynik oceny systemu polecania — zalecenia elementu](./media/interpret-model-results/22.png)

Rysunek 22. Wizualizacja wyników oceny systemu polecania — zalecenia elementu

Pierwszy sześć kolumn reprezentuje dany użytkownik identyfikatorów zaleca elementów, zgodnie z danych wejściowych. Pięć kolumn reprezentują elementy zalecając użytkownikowi w kolejności malejącej zgodności. Na przykład w pierwszym wierszu restauracji zalecany dla klienta U1048 jest 134986, a następnie 135018, 134975 135021 i 132862.

**Znajdź użytkowników, związane z danego użytkownika**

Wybierając **powiązanych użytkowników** w obszarze **rodzaj prognozowania polecania**, jest wysyłane żądanie systemu polecania można znaleźć powiązanych użytkowników do danego użytkownika. Powiązanych użytkowników są użytkownicy, którzy mają podobne preferencje. Ostatni parametr, aby wybrać w tym scenariuszu jest *powiązane wybór użytkownika*. Opcja **od użytkowników czy oceną elementy (ocena modelu)** jest przeznaczone głównie dla oceny modelu podczas procesu szkolenia. Wybierz **od wszystkich użytkowników** etap prognozowania. Obraz [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco rysunku 23.

![Wynik oceny systemu polecania — powiązanych użytkowników](./media/interpret-model-results/23.png)

Rysunek 23. Wizualizacja wyników klasyfikacji systemu polecania — powiązanych użytkowników

Pierwszy sześć kolumn pokazuje danego użytkownika identyfikatorów musiała znaleźć powiązanych użytkowników określonych danych wejściowych. Pięć kolumn przechowywać przewidywane powiązanych użytkowników użytkownika w porządku malejącym istotności. Na przykład w pierwszym wierszu klienta najbardziej odpowiednie dla klienta U1048 jest U1051, a następnie U1066, U1044 U1017 i U1072.

**Znajdź elementy powiązane z danego elementu**

Wybierając **elementy pokrewne** w obszarze **rodzaj prognozowania polecania**, prośbę o system polecania, aby znaleźć elementy pokrewne do danego elementu. Powiązane elementy są elementy najczęściej być łączone przez tego samego użytkownika. Ostatni parametr, aby wybrać w tym scenariuszu jest *powiązane Wybór elementu*. Opcja **z oceną elementy (ocena modelu)** jest przeznaczone głównie dla oceny modelu podczas procesu szkolenia. Wybrany **z wszystkich elementów** dla tego etapu prognozowania. Obraz [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco 24 rysunku.

![Wynik oceny systemu polecania — elementy pokrewne](./media/interpret-model-results/24.png)

Rysunek 24. Wizualizacja wyników klasyfikacji systemu polecania — elementy pokrewne

Pierwszy sześć kolumn reprezentuje danego elementu identyfikatorów potrzebne do odnalezienia elementy pokrewne określone dane wejściowe. Pięć kolumn przechowywać przewidywane elementy pokrewne elementu w porządku malejącym pod względem istotności. Na przykład w pierwszym wierszu element najbardziej odpowiednie dla elementu 135026 jest 135074, a następnie 135035, 132875 135055 i 134992.

**Publikowania usług sieci Web**

Proces publikowania tych eksperymenty jako usługi sieci web, aby uzyskać prognoz przypomina dla każdego z czterech scenariuszy. W tym miejscu traktujemy drugi scenariusz (zalecamy elementów do danego użytkownika), na przykład. Można użyć tej samej procedury z trzech pozostałych.

Zapisywanie systemu przeszkolone polecania jako uczonego modelu i filtrowanie danych wejściowych z kolumną Identyfikatora pojedynczego użytkownika, zgodnie z wymaganiami, można Podłączanie eksperymentu jak rysunek 25 i opublikujesz je jako usługi sieci web.

![Ocenianie eksperymentu problem zalecenie restauracji](./media/interpret-model-results/25.png)

Rysunek 25. Ocenianie eksperymentu problem zalecenie restauracji

Uruchomiona usługa sieci web, zwrócony wynik wygląda jak 26 rysunku. Pięć restauracji zalecane dla użytkownika U1048 są 134986, 135018 134975, 135021 i 132862.

![Przykładowe usługa systemowa polecania](./media/interpret-model-results/25_1.png)

![Przykładowe wyniki eksperymentu](./media/interpret-model-results/26.png)

Rysunek 26. Wynikiem usługi sieci Web restauracji zalecenie problemu

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
