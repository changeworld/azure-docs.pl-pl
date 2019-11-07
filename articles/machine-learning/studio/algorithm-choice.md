---
title: Jak wybrać algorytmy
titleSuffix: ML Studio (classic) Azure
description: Jak wybierać algorytmy Azure Machine Learning Studio (klasyczne) dla nadzorowanych i nienadzorowanych uczenia się w ramach obsługi klastrów, klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: e8d296f8752e06e6e47c349be9c900b9d0489ec5
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619759"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Jak wybrać algorytmy dla Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Odpowiedź na pytanie "jakiego algorytmu uczenia maszynowego należy użyć?" zawsze "jest to zależne". Zależy to od rozmiaru, jakości i rodzaju danych. Jest to zależne od tego, co chcesz zrobić z odpowiedzią. Jest to zależne od tego, jak matematyczny algorytm został przetłumaczony na instrukcje dotyczące komputera, którego używasz. Jest to zależne od ilości czasu. Nawet najbardziej doświadczeni analityków danych nie mogą stwierdzić, który algorytm będzie najlepiej działać przed podjęciem próby.

Machine Learning Studio (klasyczny) zapewnia najnowocześniejsze algorytmy, takie jak skalowalne drzewa decyzyjne, systemy rekomendacji bayesowskie, sieci głębokiej neuronowych i decyzje dżungle opracowywane opracowane w ramach badań firmy Microsoft. Dostępne są również skalowalne pakiety uczenia maszynowego typu open source, takie jak Vowpal Wabbit. Klasyczna wersja Machine Learning Studio obsługuje algorytmy uczenia maszynowego dla wieloklasowych i binarnych klasyfikacji, regresji i klastrowania. Zapoznaj się z pełną listą [modułów Machine Learning](/azure/machine-learning/studio-module-reference/index).
Dokumentacja zawiera pewne informacje o każdym z algorytmów i sposobach dostrajania parametrów w celu optymalizacji algorytmu do użycia.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Arkusz Machine Learning Ściągawka algorytmu

**[Arkusz Microsoft Azure Machine Learning algorytm Ściągawka](../algorithm-cheat-sheet.md)** umożliwia wybranie odpowiedniego algorytmu uczenia maszynowego dla rozwiązań analizy predykcyjnej z biblioteki Azure Machine Learning algorytmów.
W tym artykule przedstawiono sposób korzystania z tego arkusza Ściągawka.

> [!NOTE]
> Aby pobrać arkusz Ściągawka i postępować zgodnie z tym artykułem, przejdź do [strony algorytm uczenia maszynowego (Ściągawka](../algorithm-cheat-sheet.md)).
> 
> 

Te zalecenia stanowią skompilowane Opinie i porady dotyczące wielu analityków danych i ekspertów ds. uczenia maszynowego. Nie wyrażamy zgody na wszystko, ale próbowaliśmymy zharmonizować opinie w niedalekiej konsensusie. Większość zestawień umów zaczyna się od "zależnie od"... "


> [!TIP]
> Aby uzyskać informacje na temat popularnych algorytmów służących do odpowiedzi na często zadawane pytania dotyczące uczenia maszynowego, zapoznaj się z artykułami dotyczącymi uczenia maszynowego, które zawierają [przykłady algorytmów](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Typy uczenia maszynowego

### <a name="supervised"></a>Nadzorowane

Nadzorowane algorytmy uczenia czynią przewidywania na podstawie zestawu przykładów. Na przykład historyczne ceny giełdowe mogą służyć do odgadnięcia przyszłych cen. Każdy przykład używany do szkolenia jest oznaczony wartością zainteresowania — w tym przypadku cenę giełdową. Algorytm nadzorowanej nauki szuka wzorców w tych etykietach wartości. Może on korzystać z dowolnych informacji, które mogą być istotne — dnia tygodnia, sezonu, danych finansowych firmy, rodzaju branży, obecności zdarzeń geopolitycznych, a każdy algorytm szuka różnych typów wzorców. Po znalezieniu najlepszego wzorca przez algorytm, ten wzorzec jest używany do prognozowania danych testowych bez etykiet — ceny na jutro.

Uczenie nadzorowane to popularny i przydatny typ uczenia maszynowego. Po jednym wyjątku wszystkie moduły w klasycznej wersji Azure Machine Learning Studio są nadzorowanymi algorytmami uczenia. Istnieje kilka rodzajów nadzorowanych szkoleń, które są reprezentowane w Azure Machine Learning Studio (klasyczny): Klasyfikacja, regresja i wykrywanie anomalii.

* **Klasyfikacja**. Gdy dane są używane do przewidywania kategorii, nadzorowane uczenie jest również nazywane klasyfikacją. Dzieje się tak w przypadku przypisywania obrazu jako obrazu "Cat" lub "Dog". Gdy dostępne są tylko dwie opcje, są one nazywane klasyfikacją **dwuklasową** lub **dwumianową**. Jeśli istnieje więcej kategorii, jak w przypadku przewidywania zwycięzcy turnieju marca Madness turniejów, ten problem jest znany jako **Klasyfikacja wieloklasowa**.
* **Regresja**. Gdy wartość jest przewidywana, podobnie jak w przypadku cen akcji, uczenie nadzorowane jest nazywane regresją.
* **Wykrywanie anomalii**. Czasami celem jest zidentyfikowanie punktów danych, które są po prostu nietypowe. Na przykład w przypadku wykrywania oszustw są podejrzane wszystkie wysoce nietypowe wzorce wydatków kartą kredytową. Możliwe wahania są takie same, jak wiele przykładów szkoleniowych, ponieważ nie jest to możliwe, aby dowiedzieć się, jak wygląda fałszywa aktywność. Zastosowanie wykrywania anomalii ma na celu po prostu dowiedzieć się, jak wygląda normalne działanie (przy użyciu historii nieoszukańczych transakcji) i zidentyfikować wszystko, co znacznie się różni.

### <a name="unsupervised"></a>Nienadzorowanych

W przypadku nienadzorowanej uczenia punkty danych nie mają skojarzonych z nimi etykiet. Zamiast tego celem nienadzorowanego algorytmu uczenia jest organizowanie danych w jakiś sposób lub opisywanie jej struktury. Może to oznaczać, że jest to zgrupowanie do klastrów lub znalezienie różnych sposobów przeglądania złożonych danych, tak aby były prostsze lub bardziej zorganizowane.

### <a name="reinforcement-learning"></a>Uczenie przez wzmacnianie

W nauce wzmacniania algorytm pozwala wybrać akcję w odpowiedzi na każdy punkt danych. Algorytm uczenia również otrzymuje sygnał zarobku krótki czas później, wskazując na to, jak jest to dobre.
Na podstawie tego algorytm modyfikuje swoją strategię w celu osiągnięcia najwyższej nagrody. Obecnie nie ma modułów algorytmu nauczania wzmacniania w Azure Machine Learning Studio (klasyczny). Uczenie wzmacniające jest wspólne w zapłonie, gdzie zestaw odczytów czujników w jednym punkcie czasu jest punktem danych, a algorytm musi wybrać następną akcję. Jest to również naturalna dopasowanie do Internet rzeczy aplikacji.

## <a name="considerations-when-choosing-an-algorithm"></a>Zagadnienia dotyczące wybierania algorytmu

### <a name="accuracy"></a>Odpowiedni

Uzyskanie najdokładniejszej możliwej odpowiedzi nie zawsze jest konieczne.
Czasami przybliżenie jest wystarczające, w zależności od tego, czego chcesz używać. W takim przypadku może być możliwe skrócenie czasu przetwarzania przez naklejenie do bardziej przybliżonych metod. Inną zaletą bardziej przybliżonych metod jest to, że w naturalny sposób zaproponuje się uniknięcie zamontowania.

### <a name="training-time"></a>Czas uczenia

Liczba minut lub godzin koniecznych do uczenia modelu różni się bardzo znaczącą różnicą między algorytmami. Czas uczenia jest często ściśle związany z dokładnością — jeden zazwyczaj towarzyszy drugiej. Ponadto niektóre algorytmy są bardziej czułe na liczbę punktów danych niż inne.
W przypadku ograniczenia czasu można wybrać algorytm, szczególnie w przypadku dużego zestawu danych.

### <a name="linearity"></a>Liniowość

Wiele algorytmów uczenia maszynowego wykorzystuje liniowość. Algorytmy klasyfikacji liniowej zakładają, że klasy mogą być oddzielone linią prostą (lub bardziej trójwymiarową metodą analogową). Obejmują one regresję logistyczną i obsługę maszyn wektorowych (zgodnie z implementacją w Azure Machine Learning Studio (klasyczny)).
Algorytmy regresji liniowej zakładają, że trendy danych są zgodne z prostą linią. Te założenia nie są błędne w przypadku niektórych problemów, ale nie są w niej ustawiane.

![Granica klasy nieliniowej](./media/algorithm-choice/image1.png)

***Nieliniowa granica klasy*** *— poleganie na algorytm klasyfikacji liniowej spowodowałaby obniżenie niskiej dokładności*

![Dane z trendem nieliniowym](./media/algorithm-choice/image2.png)

***Dane z trendem nieliniowym*** *— użycie metody regresji liniowej spowoduje wygenerowanie znacznie większych błędów niż jest to konieczne* .

Pomimo ich zagrożeń algorytmy liniowe są bardzo popularne jako pierwszy wiersz ataku. Są one algorithmically proste i szybkie do uczenia się.

### <a name="number-of-parameters"></a>Liczba parametrów

Parametry są pokrętłami, które są włączane przez analityka danych podczas konfigurowania algorytmu. Są to liczby, które mają wpływ na zachowanie algorytmu, takie jak odporność na błędy lub liczba iteracji, lub opcje między wariantami zachowania algorytmu. Czas uczenia i dokładność algorytmu mogą czasami być bardzo poufne, aby uzyskać tylko odpowiednie ustawienia. Zazwyczaj algorytmy o dużej liczbie parametrów wymagają największej wersji próbnej i błędu, aby znaleźć dobrą kombinację.

Alternatywnie istnieje blok modułu służący do [czyszczenia parametrów](algorithm-parameters-optimize.md) w klasycznej wersji Azure Machine Learning Studio, która automatycznie próbuje wszystkie kombinacje parametrów przy wybranej szczegółowości. Chociaż jest to świetny sposób, aby upewnić się, że połączono miejsce parametru, czas wymagany do uczenia modelu rośnie wykładniczo z liczbą parametrów.

Do góry jest, że wiele parametrów zwykle wskazuje, że algorytm ma większą elastyczność. Często można uzyskać bardzo dobrą dokładność, pod warunkiem znalezienia odpowiedniej kombinacji ustawień parametrów.

### <a name="number-of-features"></a>Liczba funkcji

W przypadku niektórych typów danych liczba funkcji może być bardzo duża w porównaniu do liczby punktów danych. Często dzieje się tak w przypadku danych genetycznych lub tekstowych. Duża liczba funkcji może ją przeciążyć niektóre algorytmy uczenia, co wydłuża czas uczenia unfeasibly. Maszyny wektorowe obsługi są szczególnie odpowiednie dla tego przypadku (zobacz poniżej).

### <a name="special-cases"></a>Specjalne przypadki

Niektóre algorytmy uczenia składają szczególne założenia dotyczące struktury danych lub żądanych wyników. Jeśli znajdziesz je, która odpowiada Twoim potrzebom, może to dać bardziej przydatne wyniki, dokładniejsze przewidywania lub szybszy czas uczenia się.

| **Algorytm** | **Odpowiedni** | **Czas uczenia** | **Liniowość** | **Parametry** | **Uwagi** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klasyfikacja dwuklasowa** | | | | | |
| [Regresja logistyczna](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [Las decyzyjny](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [Decyzja Jungle](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Mało pamięci |
| [drzewo podwyższanych decyzji](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Duże rozmiary pamięci |
| [Sieć neuronowych](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Możliwe jest dodatkowe dostosowanie](azure-ml-netsharp-reference-guide.md) |
| [Średnia Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [Obsługa maszyny wektorowej](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Dobre dla dużych zestawów funkcji |
| [Maszyna wektorowa o dużej obsłudze głębokiej](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Dobre dla dużych zestawów funkcji |
| [Maszyna punktu Bayesa](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Klasyfikacja wieloklasowa** | | | | | |
| [Regresja logistyczna](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [Las decyzyjny](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [Decyzja Jungle](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Mało pamięci |
| [Sieć neuronowych](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Możliwe jest dodatkowe dostosowanie](azure-ml-netsharp-reference-guide.md) |
| [jeden-v — wszystko](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Zobacz właściwości wybranej metody dwuklasowej |
| **Ubytk** | | | | | |
| [liniowej](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayesowskie liniowy](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [Las decyzyjny](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [drzewo podwyższanych decyzji](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Duże rozmiary pamięci |
| [szybki quantile lasów](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Dystrybucje zamiast prognoz punktów |
| [Sieć neuronowych](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Możliwe jest dodatkowe dostosowanie](azure-ml-netsharp-reference-guide.md) |
| [Funkcja](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Techniczne rejestrowanie liniowe. Na potrzeby przewidywania liczby |
| [liczbą](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Na potrzeby przewidywania porządku rangi |
| **Wykrywanie anomalii** | | | | | |
| [Obsługa maszyny wektorowej](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Szczególnie dobre w przypadku dużych zestawów funkcji |
| [Wykrywanie anomalii oparte na UPW](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K — oznacza](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Algorytm klastrowania |

**Właściwości algorytmu:**

**●** — pokazuje doskonałą dokładność, krótszy czas uczenia i użycie liniowości

**○** — pokazuje dobrą dokładność i średni czas uczenia

## <a name="algorithm-notes"></a>Uwagi dotyczące algorytmu

### <a name="linear-regression"></a>Regresja liniowa

Jak wspomniano wcześniej, [regresja liniowa](/azure/machine-learning/studio-module-reference/linear-regression) dopasowuje linię (lub płaszczyznę lub plan) do zestawu danych. Jest to WorkHorse, prosta i szybka, ale może być nadmiernie uproszczony w przypadku niektórych problemów.

![Dane z trendem liniowym](./media/algorithm-choice/image3.png)

***Dane z trendem liniowym***

### <a name="logistic-regression"></a>Regresja logistyczna

Chociaż zawiera "regresję" w nazwie, regresja logistyczna jest w rzeczywistości zaawansowanym narzędziem do klasyfikacji [dwuklasowej](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) i [wieloklasowej](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) . Jest to szybkie i proste. Fakt, że używa krzywej "-kształtowej" zamiast prostej, sprawia, że jest to naturalne dopasowanie do dzielenia danych na grupy. Regresja logistyczna zapewnia granice klas liniowych, więc w przypadku korzystania z niej należy upewnić się, że jest to coś, czego można używać.

![Regresja logistyczna do danych z dwóch klas za pomocą tylko jednej funkcji](./media/algorithm-choice/image4.png)

***Regresja logistyczna dla danych dwuklasowych z tylko jedną funkcją*** *— granica klasy to punkt, w którym krzywa logistyczna jest blisko obydwu klas*

### <a name="trees-forests-and-jungles"></a>Drzewa, lasy i dżungle opracowywane

Lasy decyzyjne[(regresja](/azure/machine-learning/studio-module-reference/decision-forest-regression), [dwie klasy](/azure/machine-learning/studio-module-reference/two-class-decision-forest)i [wieloklasowe](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), decyzja dżungle opracowywane ([dwie klasy](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) i [wieloklasowe](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) oraz podwyższane drzewa decyzyjne ([regresja](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) i [dwie klasy](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) są wszystkie w oparciu o decyzję drzewa, podstawowe koncepcje uczenia maszynowego. Istnieje wiele wariantów drzew decyzyjnych, ale wszystkie te czynności wykonują tę samą czynność — należy podzielić miejsce funkcji na regiony o tej samej etykiecie. Mogą to być regiony spójnej kategorii lub wartości stałej, w zależności od tego, czy jest to klasyfikacja czy regresja.

![Drzewo decyzyjne dzieli przestrzeń funkcji](./media/algorithm-choice/image5.png)

***Drzewo decyzyjne dzieli przestrzeń funkcji na regiony o niejednorodnych wartościach.***

Ze względu na to, że przestrzeń funkcji może zostać podzielona na wolne regiony, można łatwo ją wzmusić, aby mieć jeden punkt danych na region. Jest to skrajny przykład zamontowania. Aby tego uniknąć, duży zestaw drzew jest skonstruowany z uwzględnieniem specjalnego charakteru matematycznego, aby upewnić się, że drzewa nie są skorelowane. Średnia z tego "lasu decyzyjnego" to drzewo, które pozwala uniknąć nadużycia. Lasy decyzyjne mogą korzystać z dużej ilości pamięci. Decyzja dżungle opracowywane jest wariantem, który zużywa mniej pamięci w kosztach o nieco dłuższym czasie szkoleniowym.

Podwyższane drzewa decyzyjne unikają zamontowania przez ograniczenie, ile razy można je podzielić i ile punktów danych jest dozwolonych w każdym regionie. Algorytm konstruuje sekwencję drzew, z których każda uczy się wyrównać, aby zrekompensować błąd pozostawiony przez drzewo przed. W wyniku tego powstaje bardzo precyzyjne informacje, które mogą korzystać z dużej ilości pamięci. Aby zapoznać się z pełnym opisem technicznym, sprawdź [oryginalny papier Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Funkcja szybkiego regresji quantile w lesie](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) jest odmianą drzew decyzyjnych dla szczególnych przypadków, gdy chcesz wiedzieć, że nie tylko typowa (mediana) wartość danych w danym regionie, ale również jego rozkład w postaci quantiles.

### <a name="neural-networks-and-perceptrons"></a>Sieci neuronowych i Perceptrons

Sieci neuronowych są opartymi na mózgach algorytmach edukacyjnych obejmujących [wieloklasowe](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [dwuklasowe](/azure/machine-learning/studio-module-reference/two-class-neural-network)i [regresje](/azure/machine-learning/studio-module-reference/neural-network-regression) . Znajdują się one w nieskończonej odmianie, ale sieci neuronowych w klasycznej wersji Azure Machine Learning Studio są wszystkie ukierunkowane wykresy acykliczne. Oznacza to, że funkcje wejściowe są przekazywane do przodu (nigdy nie wstecz) przez sekwencję warstw przed wprowadzeniem do danych wyjściowych. W każdej warstwie dane wejściowe są ważone w różnych kombinacjach, sumowane i przenoszone do następnej warstwy. Ta kombinacja prostych obliczeń skutkuje możliwością uczenia się zaawansowanych granic klas i trendów danych, pozornie przez magiczną. Sieci z wieloma warstwami tego sortowania wykonują "głębokie uczenie", które są paliwami do raportowania i fikcyjnej wiedzy.

Ta wysoka wydajność nie jest dostępna bezpłatnie, chociaż. Szkolenia sieci neuronowych mogą trwać długo, szczególnie w przypadku dużych zestawów danych za pomocą wielu funkcji. Mają również więcej parametrów niż większość algorytmów, co oznacza, że czyszczenie parametrów rozszerza czas uczenia.
W przypadku osób, które chcą [określić własną strukturę sieci](azure-ml-netsharp-reference-guide.md), możliwości są inexhaustible.

![Granice nauczyne przez sieci neuronowych](./media/algorithm-choice/image6.png)

***Granice neuronowych sieci mogą być złożone i nieregularne***

[Średnia dwuklasowa Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) to odpowiedź sieci neuronowych na czas uczenia skyrocketing. Używa struktury sieci, która zapewnia granice klas liniowych. Jest prawie pierwotne według współczesnych standardów, ale ma obszerną historię pracy niezawodnej i jest wystarczająco mały, aby szybko się dowiedzieć.

### <a name="svms"></a>SVMs

Obsługa maszyn wektorowych (SVMs) Znajdź granicę, która oddziela klasy o jak najszerszym marginesie. Gdy dwie klasy nie mogą być wyraźnie oddzielone, algorytmy Znajdź najlepszą granicę. Tak jak w Azure Machine Learning Studio (klasyczny), [dwie klasy SVM](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) robią to tylko z linią prostą (w SVM-mówić, używa jądra liniowego).
Ponieważ sprawia to przybliżenie liniowe, może działać dość szybko. Tam, gdzie naprawdę jest to dane intensywne, takie jak dane tekstowe lub genomika. W takich przypadkach SVMs mogą szybciej oddzielić klasy i z mniejszym nadmiernym obmontem niż większość innych algorytmów, a oprócz tego wymaga tylko niepewnej ilości pamięci.

![Granica klasy maszyny wektorowej obsługi](./media/algorithm-choice/image7.png)

***Typowy ogranicznik klasy maszyny wektorowej pomocy technicznej maksymalizuje margines oddzielający dwie klasy***

Innym produktem Microsoft Research, [dwie klasy lokalnie głębokie SVM](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) to nieliniowa odmiana SVM, która zachowuje większość szybkości i wydajności pamięci w przypadku wersji liniowej. Jest to idealne rozwiązanie w przypadkach, gdy podejście liniowe nie zapewnia dokładnej wystarczającej liczby odpowiedzi. Deweloperzy szybko ją podzielą, dzieląc problem na kilka małych liniowych problemów SVM. Zapoznaj się z [pełnym opisem](http://proceedings.mlr.press/v28/jose13.html) , aby uzyskać szczegółowe informacje na temat sposobu ich ściągania.

Przy użyciu rozszerzenia sprytne nieliniowej SVMs, [jedna Klasa SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) rysuje granicę, która ściśle pokreśla cały zestaw danych. Jest to przydatne w przypadku wykrywania anomalii. Wszelkie nowe punkty danych, które są poza tą granicą, są nietypowe.

### <a name="bayesian-methods"></a>Metody bayesowskie

Metody bayesowskie mają wysoce pożądaną jakość: unikają zamontowania. W tym celu należy przede wszystkim założeń na temat możliwie najłatwiejszej dystrybucji odpowiedzi. Innym ubocznym tego podejścia jest to, że mają one kilka parametrów. Klasyczna wersja Azure Machine Learning Studio ma algorytmy bayesowskie dla obu klasyfikacji ([maszyna Bayesa "Point-Class"](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) i regresji ([bayesowskie regresja liniowa](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Należy pamiętać, że zakłada się, że dane mogą być podzielone lub dopasowane przy użyciu prostej linii.

W notatce historycznej maszyny Bayesa "punktowe zostały opracowane w badaniach firmy Microsoft. Mają pewne wyjątkowo atrakcyjne teoretyczne prace. Zainteresowani uczniowie są kierowani do [oryginalnego artykułu w JMLR i w](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) [blogu z wglądem w szczegółowe dane przez Krzysztof Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Wyspecjalizowane algorytmy
Jeśli masz bardzo konkretny cel, możesz się na szczęście. W ramach kolekcji Azure Machine Learning Studio (klasycznej) istnieją algorytmy, które są wyspecjalizowane w:

- prognozowanie rangi ([regresja porządkowa](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- przewidywanie liczby ([regresja Poissona](/azure/machine-learning/studio-module-reference/poisson-regression)),
- wykrywanie anomalii (jedna oparta na [analizie głównych składników](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) i jedna oparta na [maszynach wektorowych pomocy technicznej](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- klastrowanie ([K-oznacza](/azure/machine-learning/studio-module-reference/k-means-clustering))

![Wykrywanie anomalii oparte na UPW](./media/algorithm-choice/image8.png)

***Wykrywanie anomalii oparte na UPW*** *— ogromna większość danych znajduje się w dystrybucji Stereotypical; punkty odbiegające od tej dystrybucji są podejrzane*

![Zestaw danych zgrupowany przy użyciu K-oznacza](./media/algorithm-choice/image9.png)

***Zestaw danych jest pogrupowany w pięć klastrów przy użyciu K-oznacza***

Istnieje również kompletna [jednoklasowa klasyfikatora](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), która przerywa problem klasyfikacji n-klasowej w przypadku problemów klasyfikacji klasy n-1 2. Właściwości dokładność, czas uczenia i liniowość są określane przez używane klasyfikatory dwuklasowe.

![Klasyfikatory dwuklasowe połączone do tworzenia klasyfikatora trzech klas](./media/algorithm-choice/image10.png)

***Para klasyfikatorów składających się z dwóch klas, które łączą się, aby utworzyć klasyfikator składający się z trzech klas***

Klasyczna wersja Azure Machine Learning Studio obejmuje również dostęp do zaawansowanej platformy uczenia maszynowego pod tytułem [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW Defies tutaj kategoryzację, ponieważ może ona poznać zarówno problemy z klasyfikacją i regresją, jak i można nawet poznać nieoznaczone częściowo dane. Można skonfigurować go tak, aby korzystał z jednej z wielu algorytmów uczenia, funkcji utraty i algorytmów optymalizacji. Została zaprojektowana od podstaw, aby była wydajna, równoległa i niezwykle szybka. Obsługuje ona duże zestawy funkcji przesadnie z nieznacznie widocznym nakładem pracy.
Uruchomiono i przeprowadzisz przez firmę Microsoft Research Jan Langford, VW jest formułą jeden wpis w polu z algorytmów samochodu giełdowego. Nie każdy problem pasuje do VW, ale jeśli tak się stanie, może być cenny w czasie, aby uzyskać krzywą uczenia się w jej interfejsie. Jest on również dostępny jako [autonomiczny kod Open Source](https://github.com/JohnLangford/vowpal_wabbit) w kilku językach.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat popularnych algorytmów służących do odpowiedzi na często zadawane pytania dotyczące uczenia maszynowego, zapoznaj się z artykułami dotyczącymi uczenia maszynowego, które zawierają [przykłady algorytmów](basics-infographic-with-algorithm-examples.md).

* Aby uzyskać listę według kategorii wszystkich algorytmów uczenia maszynowego dostępnych w Machine Learning Studio (klasyczny), zobacz temat [Inicjowanie modelu](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) w algorytmie Machine Learning Studio (klasycznym) i pomocy modułu.

* Aby zapoznać się z pełną listą algorytmów i modułów w klasycznej wersji Machine Learning Studio, zobacz sekcję [a-z Machine Learning Studio (klasyczne) modułów](/azure/machine-learning/studio-module-reference/a-z-module-list) w Machine Learning Studio (klasycznym) algorytm i pomoc dotyczącą modułów.
