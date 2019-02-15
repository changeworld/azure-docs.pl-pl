---
title: Jak wybrać algorytmy
titleSuffix: Azure Machine Learning Studio
description: Jak wybrać algorytmy Azure Machine Learning Studio uczenie nadzorowane i nienadzorowane w eksperymentach klastrowania, klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 12/18/2017
ms.openlocfilehash: 6442facf680c934d976e7fa8008f2f3c3551045f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268100"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Jak wybierać algorytmy w usłudze Azure Machine Learning Studio

Odpowiedź na pytanie "Jaki algorytmu uczenia maszynowego należy użyć?" jest zawsze "To zależy." To zależy od rozmiaru, jakości i charakter danych. To zależy od co chcesz zrobić z odpowiedzią. To zależy od sposobu matematyczne algorytmu zostało przetłumaczone na instrukcje dla komputera, którego używasz. I to zależy na czas, jaki masz. Nawet najbardziej doświadczonych analityków danych nie wiadomo, który algorytm będzie wykonywać najlepiej przed podjęciem próby ich.

Usługa Machine Learning Studio udostępnia najnowocześniejsze algorytmy, takie jak skalowalne wzmocnione drzewa decyzyjne, bayesowskie systemy rekomendacji, głębokie sieci neuronowe i dżungle decyzyjne opracowywane w dziale badań firmy Microsoft. Dostępne są również skalowalne pakiety uczenia maszynowego typu open source, takie jak Vowpal Wabbit. Usługa Machine Learning Studio obsługuje algorytmy uczenia maszynowego na potrzeby binarnej i wieloklasowej klasyfikacji i regresji oraz klastrowania. Zobacz pełną listę [modułów usługi Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/index).
W dokumentacji udostępniono pewne informacje o poszczególnych algorytmów i jak można dostrajanie parametrów w celu optymalizacji algorytmu do użycia.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Algorytm uczenia maszynowego Ściągawka

**Microsoft Azure uczenie algorytmu da się oszukać arkuszu maszyny** pomaga wybrać właściwą machine algorytmu uczenia dla rozwiązań analizy predykcyjnej z biblioteki usługi Azure Machine Learning Studio algorytmów.
W tym artykule przedstawiono sposób użycia.

> [!NOTE]
> Aby pobrać cheat-sheet i skorzystać z tego artykułu, przejdź do [usługi Machine learning algorithm cheat-sheet for Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Ta ściągawka zawiera bardzo określonej grupy odbiorców na uwadze: wydobywania danych początku przy użyciu uczenia maszynowego poziom Absolwenci bez tytułu, próby wybieranie algorytmu zaczynać w usłudze Azure Machine Learning Studio. Oznacza to, że to sprawia, że niektóre generalizacji i oversimplifications, ale jego punktów w kierunku bezpieczne. Oznacza to również, że dostępnych jest wiele algorytmów niewymienione w tym miejscu. Wraz z rozwojem obejmowania bardziej kompletny zestaw dostępnych metod znajduje się w usłudze Azure Machine Learning polega na dodaniu je.

Te zalecenia stanowią skompilowany opinie i porady od wielu analityków danych i machine learning ekspertów. Nie zgadzamy się wszystkiego, co jednak podejmowano próby ujednolicenie naszych opinie do nierównej zgodne. Większość instrukcji zastrzeżeń zaczynają się od "Jest to uzależnione..."

### <a name="how-to-use-the-cheat-sheet"></a>Jak używać — ściągawka

Przeczytaj ścieżkę i algorytmu etykiet na wykresie jako "dla  *&lt;etykieta ścieżki&gt;*, użyj  *&lt;algorytm&gt;*." Na przykład "dla *szybkość*, użyj *dwie klasy regresji logistycznej*." Czasami może być więcej niż jedna gałąź ma zastosowanie.
Czasami żadna z nich są idealnym rozwiązaniem. Są one przeznaczone do reguły z thumb zaleceń, więc nie martw się o nim są dokładne.
Wielu analityków danych, które I Rozmawialiśmy o wspomniane, które jedynym pewnym sposobem znalezienia bardzo najlepszy algorytm jest do wypróbowania wszystkich z nich.

Oto przykład z [galerii Azure AI](http://gallery.azure.ai/) z eksperymentu, który próbuje kilku algorytmów na tych samych danych i porównanie wyników: [Porównaj klasyfikatorów wieloklasowej: Litera rozpoznawania](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Aby pobrać omówienie informacyjna łatwych do zrozumienia podstawy uczenia maszyny, aby dowiedzieć się więcej na temat popularnych algorytmów odpowiedzi na często zadawane pytania dotyczące machine learning, zobacz [podstawy uczenia maszynowego z algorytmami](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Odmian usługi machine learning

### <a name="supervised"></a>W trybie nadzorowanym

Algorytmy uczenia nadzorowanego prognozowania na podstawie zestawu przykładów. Na przykład historycznych cen akcji może służyć do zagrożenia prób w przyszłości. Każdy przykład używane na potrzeby szkolenia ma etykietę o wartości zainteresowania — w tym przypadku cena akcji. Algorytm uczenia nadzorowanego szuka wzorce w tych etykiety wartości. Można użyć, wszystkie informacje, które mogą być istotne — dzień tygodnia, porą roku, danych finansowych firmy, Typ branży, obecności szkodliwe zdarzenia geopolitycznych — i dla różnych typów deseni każdego algorytmu. Po algorytm wykryła wzorzec najlepsze jest to możliwe, używa tego wzorca do tworzenia prognoz dotyczących bez etykiety danych testowych — ceny w przyszłości.

Uczenia nadzorowanego jest popularne i przydatne rodzaj usługi machine learning. Z jednym wyjątkiem są nadzorowane wszystkie moduły w usłudze Azure Machine Learning, algorytmów uczenia. Istnieje kilka określonych typów uczenia nadzorowanego, które są reprezentowane w ramach usługi Azure Machine Learning: klasyfikacji, regresji i wykrywanie anomalii.

* **Klasyfikacja**. Gdy dane są używane do prognozowania kategorii, uczenia nadzorowanego jest również nazywany klasyfikacji. Dotyczy to podczas przypisywania obrazu jako obraz dog lub cat. W przypadku tylko dwie opcje jest nazywany **dwuklasowych** lub **dwumianowego klasyfikacji**. Jeśli ma więcej kategorii, jako podczas Prognozowanie zwycięzca TURNIEJU March Madness ncaa, ten problem jest znany jako **klasyfikacji wieloklasowej**.
* **Regresja**. Wartość przewiduje się, że, podobnie jak w przypadku cen akcji, uczenia nadzorowanego jest nazywany regresji.
* **Wykrywanie anomalii**. Czasami celem jest zidentyfikowanie punktów danych, które są po prostu niezwykłe. W wykrywaniu oszukańczych transakcji na przykład wszystkie wzorce wydatków wyjątkowej karty kredytowej są podejrzane. Ewentualne zmiany są tak wiele, a więc kilku, że nie jest to możliwe, aby dowiedzieć się, jakie działanie fałszywe przykłady szkolenia wygląda następująco. Podejście, które przyjmuje wykrywania anomalii jest po prostu Dowiedz się, jakie normalne działanie wygląda jak (przy użyciu transakcji — fałszywe historii) i zidentyfikować wszystko, co znacznie różni się.

### <a name="unsupervised"></a>Nienadzorowane

Uczenie nienadzorowane punktów danych, musi bez etykiet skojarzonych z nimi. Zamiast tego celem algorytmu uczenia nienadzorowanych jest do organizowania danych, które w jakiś sposób, lub do opisania jego struktury. Oznacza to grupowanie go w klastrach lub znajdowania spojrzenie na złożonych danych, tak aby była wyświetlana, prostsze lub bardziej zorganizowane na różne sposoby.

### <a name="reinforcement-learning"></a>Uczenia przez wzmacnianie

W przypadku uczenia przez wzmacnianie algorytm pobiera wybierz akcję w odpowiedzi na każdy punkt danych. Algorytm uczenia otrzymuje również sygnał nagradzania przez krótki czas później, wskazującą, jak dobra była decyzji.
Na tej podstawie algorytm modyfikuje podstawę strategii w celu uzyskania najwyższej nagrody. Aktualnie nie istnieją żadne wzmocnienie uczenie algorytmu moduły w usłudze Azure Machine Learning. Uczenia przez wzmacnianie jest często używany w związane z robotyką, gdzie zestaw odczyty czujników w jednym punkcie w czasie jest punkt danych, a algorytm należy wybrać następne działanie robota. Jest to również fizyczną dopasowania dla Internetu rzeczy aplikacji.

## <a name="considerations-when-choosing-an-algorithm"></a>Zagadnienia dotyczące wybierania algorytmów

### <a name="accuracy"></a>Dokładność

Pobieranie możliwych najdokładniejszą odpowiedź nie zawsze jest konieczne.
Czasami przybliżeniem jest odpowiednia, w zależności od tego, co chcesz stosować. Jeśli tak jest rzeczywiście, może być znacznie Wytnij czasu przetwarzania przez wyobrazić metodami przybliżony więcej. Inną zaletą więcej metod przybliżony jest, że naturalnie charakteryzują się uniknąć [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Czas szkolenia

Liczba minut lub godzin niezbędne do nauczenia modelu różni się znacznie między algorytmów. Szkolenie czasu jest często ściśle związane z tym dokładność — jeden dołączony zazwyczaj drugiego. Ponadto niektóre algorytmy są bardziej wrażliwe na liczbę punktów danych od innych.
Gdy czas jest ograniczona pomogą rozwinąć wybór algorytmu, szczególnie w przypadku, gdy zestaw danych jest duży.

### <a name="linearity"></a>Liniowości

Wiele algorytmów uczenia maszynowego, należy użyć liniowości. Algorytmy liniowej klasyfikacji przyjęto założenie, że klasy mogą być oddzielone prostej (lub jego wyższej wymiarowej analogowy). Te obejmują regresji logistycznej i obsługują maszyny wektora (co jest zaimplementowane w usłudze Azure Machine Learning).
Algorytmy regresji liniowej przyjęto założenie, wykonać prostą trendów danych. Tych założeń są negatywnie wpływać na niektórych problemów, ale na innych użytkowników mogą obniżyć dokładności.

![Klasy liniowe bez granic][1]

***Klasa niż liniowy granic*** *— opierając się na to algorytm klasyfikacji liniowej mogłoby spowodować niski dokładności*

![Dane z trendem nieliniowych][2]

***Dane z trendem nieliniowych*** *-metodą regresji liniowej wygeneruje wiele błędów większe niż to konieczne*

Niezależnie od ich zagrożeniami liniowej algorytmy są bardzo popularne jako pierwsza linia ataku. Mają zwykle algorithmically prosty i szybki do nauczenia.

### <a name="number-of-parameters"></a>Liczba parametrów

Parametry są pokrętła, które pobiera analitykiem danych, aby włączyć podczas konfigurowania algorytm. Są one numery, które mają wpływ na zachowanie tego algorytmu, takie jak błąd uszkodzenia lub liczba iteracji lub opcje między warianty zachowania algorytm. Czas szkolenia i dokładność algorytmu czasami może być bardzo czułe na wprowadzenie odpowiednich ustawień. Zazwyczaj algorytmów przy użyciu parametrów dużą wymagają większość prób i błędów można znaleźć dobre połączenie.

Alternatywnie ma [zaczynają parametr](algorithm-parameters-optimize.md) bloku modułu w usłudze Azure Machine Learning, która automatycznie próbuje wszystkie kombinacje parametrów na poziom szczegółowości, niezależnie od wybranej. To doskonały sposób upewnij się, że zostały objęte miejsca parametr, czas wymagany do nauczenia modelu zwiększa się wykładniczo wraz z liczbą parametrów.

Wzrost jest, że zwykle o wiele parametrów oznacza, że algorytm ma większą elastyczność. Często można to osiągnąć bardzo dobre dokładności. Pod warunkiem, że można znaleźć odpowiedniej kombinacji ustawień parametrów.

### <a name="number-of-features"></a>Wiele funkcji

W przypadku niektórych typów danych, wiele funkcji mogą być bardzo duże w porównaniu do liczby punktów danych. Jest to często w przypadku danych tekstowych lub genetyka. Dużej liczby funkcji może przeciążyć pewnych algorytmów uczenia, dzięki czemu czas unfeasibly długich szkoleń. Obsługa wektor maszyny są szczególnie dobrze nadają się do tego przypadku (patrz poniżej).

### <a name="special-cases"></a>Specjalne przypadki

Niektóre algorytmów uczenia zakładają określonej struktury danych lub zakładanych wyników. Jeśli znajdziesz taki, który spełnia Twoje potrzeby, oferuje więcej przydatnych wyników, dokładniejszych prognoz lub krótszy czas szkolenia.

| **Algorytm** | **Accuracy** | **Czas szkolenia** | **Liniowości** | **Parametry** | **Uwagi** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klasyfikacja dwuklasowych** | | | | | |
| [Regresja logistyczna](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [las decyzyjny](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [Dżungla decyzji](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Zużycie małej ilości pamięci |
| [wzmocnione drzewo decyzyjnego](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Zużycie pamięci |
| [sieć neuronowa](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Możliwe jest dostosowanie dodatkowe](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [uśrednionej perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [Obsługa maszyny wektora](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Dobre dla funkcji dużych zestawów |
| [lokalnie dogłębnej obsłudze wektor maszyny](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Dobre dla funkcji dużych zestawów |
| [Maszyna punktu Bayesa](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Wieloklasowej klasyfikacji** | | | | | |
| [Regresja logistyczna](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [las decyzyjny](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [Dżungla decyzji ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Zużycie małej ilości pamięci |
| [sieć neuronowa](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Możliwe jest dostosowanie dodatkowe](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Zobacz właściwości wybrano metodę dwuklasowych |
| **Regression** | | | | | |
| [Liniowy](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayesowskie liniowy](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [las decyzyjny](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [wzmocnione drzewo decyzyjnego](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Zużycie pamięci |
| [kwantyl szybkie lasu](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Zamiast prognozy punktu dystrybucji |
| [sieć neuronowa](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Możliwe jest dostosowanie dodatkowe](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Z technicznego punktu widzenia dziennika liniowych. Do prognozowania liczby |
| [Liczba porządkowa](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Do przewidywania porządkowanie ranga |
| **Wykrywanie anomalii** | | | | | |
| [Obsługa maszyny wektora](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Szczególnie przydatne podczas zestawy funkcji duże |
| [Wykrywanie anomalii oparte na analizie PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Algorytmu klastrowania |

**Właściwości algorytmu:**

**●** — pokazuje doskonałą dokładności, szybkie szkolenia razy i użytkowania liniowości

**○** — pokazuje dobrą dokładność i szkolenia Średni czas

## <a name="algorithm-notes"></a>Uwagi dotyczące algorytmu

### <a name="linear-regression"></a>Regresja liniowa

Jak wspomniano wcześniej, [regresji liniowej](https://msdn.microsoft.com/library/azure/dn905978.aspx) pasuje do zestawu danych wiersza (lub płaszczyzny lub hyperplane). Jest najważniejszą metodą roboczą, prosty i szybki, ale może być zbyt uproszczony dla niektórych problemów.
Sprawdź tutaj [regresji liniowej w ramach samouczka](linear-regression-in-azure.md).

![Dane za pomocą trendów liniowych][3]

***Dane za pomocą trendów liniowych***

### <a name="logistic-regression"></a>Regresja logistyczna

Mimo że obejmuje złudzenia "regresji" w nazwie, regresję logistyczną jest faktycznie zaawansowane narzędzie do [dwuklasowych](https://msdn.microsoft.com/library/azure/dn905994.aspx) i [kontra](https://msdn.microsoft.com/library/azure/dn905853.aspx) klasyfikacji. Jest szybka i prosta. Fakt, że używa firmy "-ukształtowane krzywej zamiast prostej sprawia, że naturalnym wyborem w przypadku dzielenia danych pomiędzy grup. Granice liniowej klasy zapewnia regresji logistycznej, więc gdy jest używany, upewnij się, że zbliżenia liniowy jest coś, co może współdziałać z.

![Regresja logistyczna do dwóch klas danych za pomocą tylko jednej funkcji][4]

***Regresja logistyczna do dwóch klas danych za pomocą tylko jednej funkcji*** *-granica klasy jest logistycznej krzywej jest po prostu maksymalnie zbliżone do obu klas moment*

### <a name="trees-forests-and-jungles"></a>Drzewa, lasów i dżungle

Decyzja w lasach ([regresji](https://msdn.microsoft.com/library/azure/dn905862.aspx), [dwuklasowych](https://msdn.microsoft.com/library/azure/dn906008.aspx), i [kontra](https://msdn.microsoft.com/library/azure/dn906015.aspx)), decision dżungle ([dwuklasowych](https://msdn.microsoft.com/library/azure/dn905976.aspx) i [ kontra](https://msdn.microsoft.com/library/azure/dn905963.aspx)) i wzmocnione drzewa decyzyjne ([regresji](https://msdn.microsoft.com/library/azure/dn905801.aspx) i [dwuklasowych](https://msdn.microsoft.com/library/azure/dn906025.aspx)) są wszystkie na podstawie drzew decyzyjnych, podstawowe usługi machine learning koncepcji. Istnieje wiele wariantów drzewa decyzyjne, ale wszystkie te same czynności wykonasz — podziału miejsca funkcji na regiony z przede wszystkim tę samą etykietę. Mogą to być regionów spójne kategorii lub stałą wartością, w zależności od tego, czy macie klasyfikacji lub regresji.

![Drzewo decyzyjne dzieli obszar funkcji][5]

***Drzewo decyzyjne dzielący regionów około jednolitego wartości obszar funkcji***

Ponieważ obszar funkcji może zostać podzielona na arbitralnie małej regionów, to proste wyobrazić sobie wystarczająco precyzyjnie podzielenie go mieć jeden punkt danych na region. To jest przykładem extreme overfitting. Aby tego uniknąć, szerokiej gamy drzewa są konstruowane przy użyciu szczególną matematyczne wykonywane nie skorelowanych drzewa. Średnia to "las decyzyjny z" jest drzewa, która pozwala uniknąć overfitting. Decyzja lasów można używać dużej ilości pamięci. Dżungle decyzyjne są wariant, który zużywa mniej pamięci kosztem nieco dłużej szkolenia.

Aby uniknąć overfitting drzewa decyzyjnego ograniczenie ile razy oni podzielić i jak najmniejszej liczby punktów danych są dozwolone w każdym regionie. Algorytm tworzy sekwencję drzewa, z których każdy uczy się kompensacji błąd w drzewie przed. Wynik jest bardzo dokładny uczeń, która zwykle używa dużej ilości pamięci. Aby uzyskać pełny opis techniczny, zapoznaj się [oryginalny dokument firmy Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Szybkie regresji kwantyl lasu](https://msdn.microsoft.com/library/azure/dn913093.aspx) jest odmianą drzew decyzyjnych w specjalnym przypadku, w którym chcesz wiedzieć, nie tylko Typowa wartość (Mediana) danych w regionie, ale również jego dystrybucji postać quantiles.

### <a name="neural-networks-and-perceptrons"></a>Sieci neuronowych i perceptrons

Sieci neuronowych są mózg inspirowanej obejmujące algorytmów uczenia [kontra](https://msdn.microsoft.com/library/azure/dn906030.aspx), [dwuklasowych](https://msdn.microsoft.com/library/azure/dn905947.aspx), i [regresji](https://msdn.microsoft.com/library/azure/dn905924.aspx) problemów. Pochodzą one w najróżniejsze, ale są sieci neuronowych, w ramach usługi Azure Machine Learning, wszystko w postaci ukierunkowanych grafy acykliczne. Oznacza to, że funkcje wejściowe są przekazywane do przodu (nigdy nie do tyłu) za pośrednictwem sekwencji warstwy przed są przekształcane w danych wyjściowych. W każdej warstwie dane wejściowe są liczone w różnych kombinacjach, sumy i przekazywane do następnej warstwy. Ta kombinacja prostych obliczeń powoduje możliwość klasy złożone granic i dane trendów pozornie przez magic. Wielowarstwowe z wielu sieci tego rodzaju wykonaj "uczenia głębokiego" który paliwa bardzo tech raportowania oraz science-fiction.

Ta o wysokiej wydajności nie darmowe, mimo że. Sieci neuronowych może zająć dużo czasu na nauczenie, szczególnie w przypadku dużych zestawów danych z dużą liczbą funkcji. Mają również więcej parametrów niż większość algorytmy, które oznacza, że parametr zaczynają znacznie rozszerza czasu szkoleń.
I dla tych overachievers, którzy chcą [określić własne struktury sieci](https://go.microsoft.com/fwlink/?LinkId=402867), możliwości są inexhaustible.

![Granice rozpoznawane przez sieci neuronowych][6]
***granice rozpoznawane przez sieci neuronowych mogą być złożone i nieregularne***

[Dwuklasowych uśredniane perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) jest odpowiedź sieci neuronowych ogromne razy szkolenia. Używa on strukturę sieci, która zapewnia granice liniowej klasy. Jest prawie pierwotnych przez obowiązujących obecnie standardów, ale od dawna pracy niezawodnie i jest wystarczająco mała, aby szybko dowiedzieć się więcej.

### <a name="svms"></a>SVMs

Maszyny wektor pomocy technicznej (SVMs) znaleźć granic, oddzielający klasy według jako szerokiego zakresu margines, jak to możliwe. Gdy dwie klasy nie może być wyraźnie rozdzielone, algorytmy Znajdź najlepsze granicę, którą mogą oni. Podczas zapisywania w usłudze Azure Machine Learning [SVM dwuklasowych](https://msdn.microsoft.com/library/azure/dn905835.aspx) robi to za pomocą prostej tylko. (W SVM mowy, używa ona liniowej jądra). Ponieważ sprawia, że zbliżaniu liniowego, jest w stanie przeprowadzić dość szybko. Gdzie go ujawniają jest intensywna funkcji danych, takich jak tekst lub genomu. W takich przypadkach SVMs są możliwe do rozdzielania klas, szybciej i z mniejszą overfitting niż większość inne algorytmy oprócz wymagające niewielkie ilości pamięci.

![Wektor granicę klasy pomocy technicznej][7]

***Granicę techniczną wektor maszyny klasy maksymalizuje margines oddzielenie dwóch klas***

Inny produkt Microsoft Research [SVM lokalnie głębokiego dwuklasowych](https://msdn.microsoft.com/library/azure/dn913070.aspx) jest inny niż liniowy wariant SVM, które zachowuje większość efektywności szybkość i pamięć liniowej wersji. To idealne rozwiązanie w przypadkach, gdzie podejście liniowy nie zapewnia wystarczająco dokładnych odpowiedzi. Deweloperzy przechowywane szybko dzieląc problem na wiele małych liniowej problemów SVM. Odczyt [pełny opis](http://proceedings.mlr.press/v28/jose13.html) Aby uzyskać szczegółowe informacje w sposób pobierany off ta procedura.

Przy użyciu Sprytne rozszerzenia nieliniowych SVMs [SVM jednej klasy](https://msdn.microsoft.com/library/azure/dn913103.aspx) rysuje granic, zawierający ściśle całego zestawu danych. Jest to przydatne do wykrywania anomalii. Wszystkie nowe punkty danych mieszczące się daleko poza tę granicę są nietypowe być warte zauważenia.

### <a name="bayesian-methods"></a>Metody Bayesowskie

Metody Bayesowskie mają bardzo pożądane jakości: Unikaj ich overfitting. One to robić przez założenie wcześniej o prawdopodobnie dystrybucji odpowiedzi. Inny byproduct tego podejścia jest bardzo mało parametrów. Usługa Azure Machine Learning ma obu algorytmów Bayesowskie zarówno klasyfikacji ([maszyna punktu Bayesa Two-class](https://msdn.microsoft.com/library/azure/dn905930.aspx)) i regresji ([regresji liniowej Bayesowskie](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Pamiętaj, że te przyjęto założenie, że dane mogą być podziału lub mieści się przy użyciu prostej.

Na notatkę historycznych maszyn punktu Bayesa zostały opracowywane w dziale Microsoft Research. Mają one część wyjątkowo piękne prac teoretyczne za ich. Zainteresowane uczniów zostaje skierowany do [oryginalny artykuł w JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) i [wnikliwe blog, Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Dołączanie specjalizowanych algorytmów
Jeśli masz bardzo specyficznym celem może być szczęście. W tej kolekcji usługi Azure Machine Learning są algorytmy, które specjalizują się w:

- prognoz porządkowały ([regresji porządkowej](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- Liczba prognozowania ([regresji Poissona](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- wykrywanie anomalii (jedną na podstawie [analizy głównych komponentów](https://msdn.microsoft.com/library/azure/dn913102.aspx) i jeden na podstawie [obsługuje wektor maszyn](https://msdn.microsoft.com/library/azure/dn913103.aspx))
- klastrowanie ([K-średnich](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Wykrywanie anomalii oparte na analizie PCA][8]

***Wykrywanie anomalii oparte na analizie PCA*** *— większość danych uczciwy stereotypical dystrybucji; punktów różniących się znacznie od dystrybucji są podejrzane*

![Zestaw danych, grupowane, za pomocą K-średnich][9]

***Zestaw danych są pogrupowane w pięciu klastrów za pomocą K-średnich***

Istnieje również zespołu [klasyfikatora wieloklasowej jeden v wszystkie](https://msdn.microsoft.com/library/azure/dn905887.aspx), która dzieli problemu klasyfikacji N klasy na problemy klasyfikacji dwuklasowych n-1. Dokładność, czasu szkoleń i właściwości liniowości są określane przez klasyfikatorów dwuklasowych używane.

![Klasyfikatorów dwuklasowych połączone, tworząc klasyfikatora trzy — klasa][10]

***Para klasyfikatorów dwuklasowych są łączone w celu tworzą klasyfikatora trzy — klasa***

Usługę Azure Machine Learning obejmuje również dostęp do struktury uczenia maszynowego zaawansowane pod tytułem [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW powinny kategoryzacji, w tym miejscu, ponieważ można znaleźć klasyfikacji i regresji problemy, a nawet można uzyskać z poziomu danych częściowo bez etykiety. Można skonfigurować, aby użyć jednego z wielu funkcji utraty, algorytmy optymalizacji i algorytmów uczenia. Została zaprojektowana od samych podstaw się to wydajne, równoległe i bardzo szybkie. Obsługuje on zestawy funkcji bardzo duża przy minimalnym nakładzie pracy jawnego.
Pracę i prowadzone przez Microsoft Research własnych John Langford, VW jest formuły jednego wpisu w polu samochodu stock algorytmów. Nie każdy problem pasuje VW, ale jeśli należy do Ciebie, może być zdobycie pokonywania krzywą uczenia się na interfejsie. Jest również dostępny jako [kodu autonomicznego typu open source](https://github.com/JohnLangford/vowpal_wabbit) w kilku językach.

## <a name="next-steps"></a>Następne kroki

* Aby pobrać omówienie informacyjna łatwych do zrozumienia podstawy uczenia maszyny, aby dowiedzieć się więcej na temat popularnych algorytmów odpowiedzi na często zadawane pytania dotyczące machine learning, zobacz [podstawy uczenia maszynowego z algorytmami](basics-infographic-with-algorithm-examples.md).

* Aby uzyskać listę według kategorii wszystkich algorytmów uczenia maszynowego dostępnych w usłudze Machine Learning Studio, zobacz [zainicjować modelu](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) algorytmu Studio uczenia maszynowego i pomóc w Module.

* Aby uzyskać pełną listę alfabetyczną algorytmów i modułów w usłudze Machine Learning Studio, zobacz [A-Z listy modułów usługi Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list) algorytmu Studio uczenia maszynowego i pomóc w Module.

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
