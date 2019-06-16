---
title: Interpretacja wyników modelu
titleSuffix: Azure Machine Learning Studio
description: Jak wybrać optymalną parametrem ustawionym dla algorytmu przy użyciu i wizualizowanie danych wyjściowych modelu wynik.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: c46f22fb5c906aaffa48f39a0c643ca2a48573f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60867305"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio"></a>Interpretacja wyników modelu w usłudze Azure Machine Learning Studio
W tym temacie wyjaśniono, jak wizualizować i interpretacji wyników przewidywań w usłudze Azure Machine Learning Studio. Po uczony model i gotowe prognozy na nim ("oceniane modelu"), musisz zrozumieć i zinterpretować wyniki prognozy.



Istnieją cztery główne rodzaje modele uczenia maszynowego w usłudze Azure Machine Learning Studio:

* Klasyfikacja
* Klastrowanie
* Regresji
* Systemy polecania

Moduły używane w celu prognozowania na podstawie tych modeli są:

* [Ocenianie modelu] [ score-model] modułu dla klasyfikacji i regresji
* [Przypisz do klastrów] [ assign-to-clusters] modułu dla klastra
* [Ocena polecania Matchbox] [ score-matchbox-recommender] systemów rekomendacji

W tym dokumencie opisano sposób interpretacji wyników przewidywań dla każdego z tych modułów. Aby uzyskać omówienie tych modułów, zobacz [wybór parametrów w celu optymalizacji algorytmów w usłudze Azure Machine Learning Studio](algorithm-parameters-optimize.md).

Ten temat dotyczy interpretacji prognoz, ale nie oceny modelu. Aby uzyskać więcej informacji na temat oceny modelu, zobacz [Ocenianie wydajności modelu w usłudze Azure Machine Learning Studio](evaluate-model-performance.md).

Jeśli jesteś nowym użytkownikiem usługi Azure Machine Learning Studio i potrzebujesz pomocy, obejmujący tworzenie prostego eksperymentu, aby rozpocząć pracę, zobacz [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md) w usłudze Azure Machine Learning Studio.

## <a name="classification"></a>Klasyfikacja
Istnieją dwie podkategorie klasyfikacji problemów:

* Problemy z tylko dwoma klasami (Klasyfikacja dwuklasowych lub binarny)
* Problemy z więcej niż dwóch klas (wieloklasowej klasyfikacji)

Usługa Azure Machine Learning Studio ma różnych modułów, aby poradzić sobie z każdą z tych typów klasyfikacji, ale metody do interpretacji ich wyniki prognozy są podobne.

### <a name="two-class-classification"></a>Klasyfikacja dwuklasowych
**Przykładowy eksperyment**

Przykładem problem klasyfikacji dwuklasowych jest klasyfikacji irysów kwiatów. Zadanie jest do klasyfikowania irysów kwiatów na podstawie ich funkcji. Zestaw danych irysów w usłudze Azure Machine Learning Studio jest podzbiorem popularnej [zestawu danych Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) zawierającą wystąpienia tylko dwa kwiaty gatunków (klasy 0 i 1). Istnieją cztery funkcje dla każdego zbioru (długość słupka, szerokość słupka, płatka długość i szerokość płatka).

![Zrzut ekranu przedstawiający iris eksperymentu](./media/interpret-model-results/1.png)

Rysunek 1. Eksperyment problem dwuklasowych klasyfikacji irysów

Eksperyment została wykonana Aby rozwiązać ten problem, jak pokazano na rysunku 1. Modelu drzewa decyzyjnego dwuklasowych został przeszkolony i oceniane. Teraz możesz wizualizować wyniki przewidywań z [Score Model] [ score-model] moduł, kliknij port wyjściowy [Score Model] [ score-model] Moduł, a następnie klikając polecenie **Visualize**.

![Moduł score model](./media/interpret-model-results/1_1.png)

To powoduje wyświetlenie wyników oceny, jak pokazano na rysunku 2.

![Wyniki doświadczenia dwuklasowych klasyfikacji irysów](./media/interpret-model-results/2.png)

Rysunek 2. Wizualizuj wynik score model klasyfikacji dwuklasowych

**Interpretacja wyników**

Istnieje sześć kolumn w tabeli wyników. Po lewej stronie cztery kolumny są cztery funkcje. Prawo dwie kolumny, etykiety oceniane i prawdopodobieństwa oceniane, są przewidywane wyniki. Kolumna jest oceniane prawdopodobieństwa Wyświetla prawdopodobieństwo należącą Kwiatek dodatnią klasy (klasy 1). Na przykład pierwszy numer w kolumnie (0.028571) oznacza, że jest 0.028571 prawdopodobieństwo, że pierwszy Kwiatek należy do klasy 1. W kolumnie sklasyfikowane etykiety widoczne to przewidywana klasa, dla każdego zbioru. Jest to oparty na kolumnie oceniane prawdopodobieństwa. W przypadku większych niż 0,5 ocenami prawdopodobieństwo kwiatka przewiduje się klasy 1. W przeciwnym razie jest przewidzieć jako klasa 0.

**Publikacja usług sieci Web**

Po uwzględnione wyników przewidywań i ocenić dźwięku doświadczenia mogą być publikowane jako usługi sieci web tak, aby można go wdrożyć w różnych aplikacjach i wywołać go uzyskać klasy prognozy na wszelkie nowe kwiat irysa. Aby dowiedzieć się, jak zmienić eksperymentu szkolenia na eksperyment oceniania i opublikujesz je jako usługi sieci web, zobacz [Tutorial 3: Wdrażanie modelu ryzyko kredytowe](tutorial-part3-credit-risk-deploy.md). Ta procedura umożliwia oceniania eksperymentu jak pokazano na rysunku 3.

![Zrzut ekranu przedstawiający oceny eksperymentu](./media/interpret-model-results/3.png)

Rysunek 3. Ocenianie eksperymentu problem dwuklasowych klasyfikacji irysów

Teraz musisz ustawić wejściowe i wyjściowe dla usługi sieci web. Dane wejściowe są prawy port wejściowy z [Score Model][score-model], czyli kwiat Irysa funkcje danych wejściowych. Wybór danych wyjściowych zależy od tego, czy interesuje Cię przewidywanej klasy (etykieta ocenami) i/lub ocenami prawdopodobieństwa. W tym przykładzie zakłada się, że jesteś zainteresowany zarówno. Aby wybrać kolumny żądanego wyniku, użyj [Wybieranie kolumn w zestawie danych] [ select-columns] modułu. Kliknij przycisk [Wybieranie kolumn w zestawie danych][select-columns], kliknij przycisk **uruchamianie selektora kolumn**i wybierz **sklasyfikowane etykiety** i **Scored Prawdopodobieństwa**. Po ustawieniu port wyjściowy [Wybieranie kolumn w zestawie danych] [ select-columns] i uruchomiona ponownie, powinno być gotowe do opublikowania oceniania eksperymentu jako usługę sieci web, klikając **OPUBLIKOWAĆ usługi sieci WEB** . Eksperyment końcowy wygląda jak rysunek 4.

![Eksperyment dwuklasowych klasyfikacji irysów](./media/interpret-model-results/4.png)

Rysunek 4. Końcowy eksperyment oceniania problemu dwuklasowych klasyfikacji irysów

Po uruchomieniu usługi sieci web i wprowadzić inne wartości funkcji wystąpienia testu, wynik zwraca dwóch liczb. Pierwsza liczba jest ocenami etykiety, a drugą jest wartość prawdopodobieństwa ocenami. Klasy 1 z prawdopodobieństwem 0.9655 przewiduje się tego zbioru.

![Interpretowania modelu wynik testu](./media/interpret-model-results/4_1.png)

![Ocenianie wyników testu](./media/interpret-model-results/5.png)

Rysunek 5. Wynik usługi sieci Web iris dwuklasowych klasyfikacji

### <a name="multi-class-classification"></a>Wieloklasowej klasyfikacji
**Przykładowy eksperyment**

W tym eksperymencie w przypadku zadań rozpoznawania litery na przykład klasyfikacji wieloklasowej. Klasyfikator próbuje przewidzieć określonej litery (klasa) na podstawie niektórych wartości atrybutu odręcznej wyodrębnione z odręcznej obrazów.

![Przykład rozpoznawanie list](./media/interpret-model-results/5_1.png)

Dane szkoleniowe są funkcje 16 wyodrębnione z odręcznej list obrazów. 26 liter tworzą naszych zajęć 26. Rysunek 6 przedstawia eksperyment, który będzie wytrenuj model klasyfikacji wieloklasowej rozpoznawania litery i przewidywanie na tej samej funkcji nastavit testowego zestawu danych.

![Litera rozpoznawanie klasyfikacji wieloklasowej eksperymentu](./media/interpret-model-results/6.png)

Rysunek 6. Eksperyment problem klasyfikacji wieloklasowej rozpoznawania list

Wizualizacja wyników z [Score Model] [ score-model] moduł, kliknij port wyjściowy [Score Model] [ score-model] modułu, a następnie klikając polecenie **Visualize**, powinna zostać wyświetlona zawartość, jak pokazano na rysunku 7.

![Ocena wyników modelu](./media/interpret-model-results/7.png)

Rysunek 7. Wizualizacja wyników model klasyfikacji wieloklasowej klasyfikacji

**Interpretacja wyników**

Po lewej stronie 16 kolumn reprezentują wartości funkcji z zestawu testowego. Kolumny z nazwami, takie jak czy oceniane prawdopodobieństwa dla klasy "XX", takich jak kolumny prawdopodobieństwa oceniane w przypadku dwóch klas. Pokazują one prawdopodobieństwo ją odpowiadający mu wpis zaliczyć do niektórych klas. Na przykład do pierwszej pozycji ma 0.003571 prawdopodobieństwo, że jest "A," 0.000451 prawdopodobieństwo, że jest "B" i tak dalej. Ostatnia kolumna (sklasyfikowane etykiety) jest taka sama jak sklasyfikowane etykiety w przypadku dwóch klas. Wybiera klasę za pomocą największej prawdopodobieństwo ocenami jako to przewidywana klasa odpowiadający mu wpis. Na przykład do pierwszej pozycji ocenami etykiety jest "F", ponieważ ma ona największą prawdopodobieństwo "F" (0.916995).

**Publikacja usług sieci Web**

Możesz także uzyskać ocenami etykiety dla każdego wpisu oraz prawdopodobieństwo ocenami etykiety. Podstawowa logika ma na celu znalezienie największych prawdopodobieństwo, że wśród wszystkich scored probabilities. Aby to zrobić, należy użyć [wykonanie skryptu języka R] [ execute-r-script] modułu. Na rysunku nr 8 przedstawiono kod R, a na rysunku nr 9 przedstawiono wynikiem eksperymentu.

![Przykładowy kod języka R](./media/interpret-model-results/8.png)

Rysunek 8. Kod R dla wyodrębnianie sklasyfikowane etykiety i prawdopodobieństwa etykiet

![Wynik eksperymentu](./media/interpret-model-results/9.png)

Rysunek 9. Końcowy eksperyment oceniania programu rozpoznawania literę problem klasyfikacji wieloklasowej

Po zakończeniu publikowania i uruchom usługę sieci web i wprowadzić inne wartości wejściowych funkcji zwracany wynik wygląda na rysunku nr 10. Niniejszy odręcznej list, z wyodrębnionych funkcjami 16, przewiduje się jako "T" z prawdopodobieństwem 0.9715.

![Moduł score interpretowania testu](./media/interpret-model-results/9_1.png)

![Wynik testu](./media/interpret-model-results/10.png)

Rysunek 10. Wynik usługi sieci Web wieloklasowej klasyfikacji

## <a name="regression"></a>Regresji
Problemy z regresji różnią się od klasyfikacji problemów. W klasyfikacji problemu ma być realizowany przewidzieć dyskretnych klasy, takie jak, które należy klasa kwiat irysa na. Ale jak widać w poniższym przykładzie problem regresji próbujesz się przewidzieć w zmiennej ciągłej, takie jak ceny samochodu.

**Przykładowy eksperyment**

Na użytek Prognozowanie cen samochodów jako przykładu regresji. Próbujesz prognozować cenę samochodu na podstawie jej funkcji, w tym upewnij, typu paliwa, typ treści i pokrętła stacji. Rysunek 11 przedstawiono eksperymentu.

![Eksperymentu opartego na regresji cen samochodów](./media/interpret-model-results/11.png)

Rysunek 11. Eksperyment problem regresji cen samochodów

Wizualizacja [Score Model] [ score-model] modułu, wynik wygląda jak rysunek 12.

![Wyniki oceny dla problemu Prognozowanie cen samochodów](./media/interpret-model-results/12.png)

Rysunek 12. Wynik oceny dla problemu Prognozowanie cen samochodów

**Interpretacja wyników**

Scored etykiety jest kolumna wyników, w tym wyniku oceny. Liczby są przewidywane cena dla każdego samochodu.

**Publikacja usług sieci Web**

Możesz publikować eksperymentu opartego na regresji do usługi sieci web i wywołać go do prognozowania cen samochodów w taki sam sposób jak w przypadku użycia dwuklasowych klasyfikacji.

![Ocenianie eksperymentu problem regresji cen samochodów](./media/interpret-model-results/13.png)

Rysunek 13. Ocenianie eksperymentu problem regresji cen samochodów

Uruchomiona usługa sieci web, zwracany wynik wygląda jak rysunek 14. Przewidywane cena dla tego samochodu wynosi $15,085.52.

![Moduł oceniania interpretowania testu](./media/interpret-model-results/13_1.png)

![Ocenianie wyniki modułu](./media/interpret-model-results/14.png)

Rysunek 14. Wynik usługi sieci Web problem regresji cen samochodów

## <a name="clustering"></a>Klastrowanie
**Przykładowy eksperyment**

Użyjemy zestawu danych Iris ponownie do tworzenia eksperymentu klastrowania. W tym miejscu można odfiltrować etykiety klasy w zestawie danych, aby tylko ma funkcje i może służyć do klastra. W tym iris przypadek użycia, określ liczbę klastrów do dwóch podczas procesu uczenia, co oznacza, że będzie klastra kwiatów na dwie klasy. Eksperyment jest wyświetlany w rysunek 15.

![Problem klastrowania Iris eksperymentów](./media/interpret-model-results/15.png)

Rysunek 15. Problem klastrowania Iris eksperymentów

Klastrowanie różni się od klasyfikacji, w tym szkoleniowy zestaw danych nie ma etykiety prawdziwych danych podstaw samodzielnie. Klastrowanie grup wystąpień zestaw danych szkoleniowych w różnych klastrach. W trakcie szkolenia modelu etykiet wpisy poznając różnice między ich funkcje. Po tym uczonego modelu może służyć do dalszego klasyfikowania przyszłych wpisów. Istnieją dwie części wyniku, który jesteśmy zainteresowani w ramach klastrowania problem. Pierwsza część jest etykietowania szkoleniowy zestaw danych, a drugi jest klasyfikowania nowy zestaw danych za pomocą uczonego modelu.

Pierwsza część wyniki mogą być wizualizowane, klikając w lewy port wyjściowy [Train Model klastrowania] [ train-clustering-model] , a następnie klikając polecenie **Visualize**. Wizualizacja jest wyświetlana w rysunek 16.

![Wynik klastrowania](./media/interpret-model-results/16.png)

Rysunek 16. Wizualizowanie klastra wynik szkoleniowy zestaw danych

Wynik części drugiej klastrowania nowe wpisy z uczony model klastrowania jest wyświetlany w rysunek 17.

![Wizualizowanie klastra wynik](./media/interpret-model-results/17.png)

Ilustracja 17. Wizualizowanie klastra wynik na nowy zestaw danych

**Interpretacja wyników**

Mimo że wyniki z dwóch części jest wynikiem eksperymentu różnych etapach, wygląd i są interpretowane w taki sam sposób. Pierwsze cztery kolumny są funkcje. Ostatnia kolumna przydziałów, jest wynikiem prognozy. Wpisy przypisany ten sam numer oczekuje się znajdować się w tym samym klastrze, oznacza to, współużytkują one podobieństwa w jakiś sposób (tego eksperymentu używa domyślnej metryki odległość euklidesowa). Ponieważ określono liczbę klastrów, 2, wpisów w przypisaniach są oznaczone jako 0 lub 1.

**Publikacja usług sieci Web**

Możesz publikować klastrowania eksperymentu do usługi sieci web i wywołać go dla klastra prognozy samo jak w przypadku klasyfikacji dwuklasowych przypadek użycia.

![Ocenianie eksperymentu iris klastrowania problemu](./media/interpret-model-results/18.png)

Ilustracja 18. Ocenianie eksperymentu problem klastrowania irysów

Po uruchomieniu usługi sieci web zwracany wynik wygląda jak rysunek 19. Ta Kwiatek przewiduje się znajdować się w klastrze 0.

![Test interpretacji moduł oceny](./media/interpret-model-results/18_1.png)

![Ocenianie wynik modułu](./media/interpret-model-results/19.png)

Ilustracja 19. Wynik usługi sieci Web iris dwuklasowych klasyfikacji

## <a name="recommender-system"></a>System polecania
**Przykładowy eksperyment**

Systemów polecania można użyć problem zalecenie restauracji, na przykład: zaleca się restauracji dla klientów na podstawie ich klasyfikacji historii. Dane wejściowe składa się z trzech części:

* Klasyfikacje restauracji od klientów
* Dane funkcji odbiorcy
* Dane funkcji restauracji

Kilka rzeczy, które można wykonać [polecania Matchbox Train] [ train-matchbox-recommender] modułu w usłudze Azure Machine Learning Studio:

* Przewidywanie klasyfikację dla danego użytkownika i elementów
* Zaleca się elementy do danego użytkownika
* Znajdź użytkowników skojarzoną z danym użytkownikiem
* Znajdować elementy związane z danego elementu

Można wybrać, co chcesz zrobić, wybierając z czterech opcji dostępnych w **rodzaj prognozowania polecania** menu. W tym miejscu możesz zapoznać się wszystkie cztery scenariusze.

![Matchbox polecania](./media/interpret-model-results/19_1.png)

Typowe eksperymentu usługi Azure Machine Learning Studio dla systemu polecania wygląda jak rysunek 20. Aby uzyskać informacje o sposobie używania moduły polecania systemu, zobacz [polecania matchbox Train] [ train-matchbox-recommender] i [polecania matchbox wynik] [ score-matchbox-recommender].

![Polecania system eksperymentu](./media/interpret-model-results/20.png)

Ilustracja 20. Polecania system eksperymentu

**Interpretacja wyników**

**Przewidywanie klasyfikację dla danego użytkownika i elementów**

Wybierając **prognozowania oceny** w obszarze **rodzaj prognozowania polecania**, prośbę o systemie polecania do prognozowania oceny dla danego użytkownika, a element. Wizualizację [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco rysunek 21.

![Ocena wynik system polecania — ocena prognoz](./media/interpret-model-results/21.png)

Ilustracja 21. Wizualizacja wyników oceny systemu polecania — ocena prognoz

Pierwsze dwie kolumny to pary element użytkownika, dostarczone przez dane wejściowe. Trzecia kolumna jest ocena przewidywane użytkownika dla danego elementu. Na przykład w pierwszym wierszu klienta U1048 przewiduje się częstotliwość restauracji 135026 jako 2.

**Zaleca się elementy do danego użytkownika**

Wybierając **zalecenie elementu** w obszarze **polecania prognozowania rodzaj**, jest wysyłane żądanie systemu polecania polecisz elementy danego użytkownika. Ostatni parametr, aby wybrać w tym scenariuszu jest *zaleca się zaznaczenie elementu*. Opcja **z oceniono jako elementy (ocena modelu)** jest przeznaczone głównie dla oceny modelu w procesie szkolenia. Etap prognozowania, wybraliśmy **z wszystkich elementów**. Wizualizację [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco rysunek 22.

![Wynik oceny systemu polecania — element zaleceń](./media/interpret-model-results/22.png)

Ilustracja 22. Wizualizacja wyników oceny systemu polecania — zalecenie elementu

Pierwsze sześć kolumn reprezentuje danego użytkownika identyfikatory elementy, zgodnie z informacjami od danych wejściowych. Pięciu kolumnach reprezentują elementy zalecając użytkownikowi malejąco według istotności. Na przykład w pierwszym wierszu restauracji zalecany dla klienta U1048 jest 134986, a następnie 135018, 134975, 135021 i 132862.

**Znajdź użytkowników skojarzoną z danym użytkownikiem**

Wybierając **powiązanych użytkowników** w obszarze **rodzaj prognozowania polecania**, jest wysyłane żądanie systemu polecania w poszukiwaniu powiązanych użytkowników do danego użytkownika. Powiązanych użytkowników są użytkowników, którzy mają podobne preferencje. Ostatni parametr, aby wybrać w tym scenariuszu jest *powiązane wybór użytkownika*. Opcja **od użytkowników czy oceniono jako elementy (ocena modelu)** jest przeznaczone głównie dla oceny modelu w procesie szkolenia. Wybierz **od wszystkich użytkowników** etap prognozy. Wizualizację [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco ilustracja 23.

![Wynik oceny systemu polecania — powiązanych użytkowników](./media/interpret-model-results/23.png)

Ilustracja 23. Wizualizacja wyników klasyfikacji systemu polecania — powiązanych użytkowników

Pierwsze sześć kolumn pokazuje danego użytkownika identyfikatory potrzebne do odnalezienia powiązanych użytkowników, zgodnie z informacjami od danych wejściowych. Pięciu kolumnach przechowywać przewidywane powiązanych użytkowników użytkownika malejąco według zgodności. Na przykład w pierwszym wierszu najistotniejsze klienta dla klienta U1048 jest U1051, a następnie U1066, U1044, U1017 i U1072.

**Znajdować elementy związane z danego elementu**

Wybierając **powiązane elementy** w obszarze **rodzaj prognozowania polecania**, prośbę o system polecania, aby znaleźć elementy powiązane z danym elementem. Powiązane elementy są najczęściej być polubionych przez tego samego użytkownika elementów. Ostatni parametr, aby wybrać w tym scenariuszu jest *powiązane zaznaczenie elementu*. Opcja **z oceniono jako elementy (ocena modelu)** jest przeznaczone głównie dla oceny modelu w procesie szkolenia. Wybraliśmy **z wszystkich elementów** etap prognozy. Wizualizację [polecania Matchbox wynik] [ score-matchbox-recommender] dane wyjściowe wyglądają następująco rysunek 24.

![Wynik oceny systemu polecania — elementy pokrewne](./media/interpret-model-results/24.png)

Rysunek 24. Wizualizacja wyników klasyfikacji systemu polecania — elementy pokrewne

Pierwsze sześć kolumn reprezentuje danego elementu identyfikatory potrzebne do odnalezienia elementy pokrewne, zgodnie z informacjami od danych wejściowych. Pięciu kolumnach przechowywać przewidywane powiązane elementy elementu w porządku malejącym pod kątem istotności. Na przykład w pierwszym wierszu najbardziej istotnych elementów dla elementu 135026 jest 135074, a następnie 135035, 132875, 135055 i 134992.

**Publikacja usług sieci Web**

Proces publikowania tych doświadczeń jako usługi sieci web, aby uzyskiwać prognozy jest podobny dla każdego z czterech scenariuszy. W tym miejscu podejmujemy drugi scenariusz (zaleca się elementy do danego użytkownika), na przykład. Aby wykonać tę samą procedurę z pozostałych trzech.

Zapisywanie systemu uczonego polecania jako uczonego modelu i filtrowanie danych wejściowych z kolumną identyfikator pojedynczego użytkownika, zgodnie z żądaniem, możesz podpiąć eksperymentu, tak jak rysunek 25 i opublikujesz je jako usługi sieci web.

![Ocenianie eksperymentu restauracji zalecenie dotyczące problemu](./media/interpret-model-results/25.png)

Ilustracja 25. Ocenianie eksperymentu restauracji zalecenie dotyczące problemu

Uruchomiona usługa sieci web, zwracany wynik wygląda jak rysunek 26. Pięć restauracji zalecane dla użytkownika U1048 są 134986 135018, 134975, 135021 i 132862.

![Przykładowe polecania usługę systemową](./media/interpret-model-results/25_1.png)

![Przykładowe wyniki eksperymentu](./media/interpret-model-results/26.png)

Ilustracja 26. Wynik usługi sieci Web restauracji zalecenie dotyczące problemu

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
