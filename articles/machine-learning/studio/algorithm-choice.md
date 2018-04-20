---
title: Wybieranie algorytmów uczenia maszynowego | Dokumentacja firmy Microsoft
description: Jak wybrać Azure Machine Learning algorytmów uczenie nadzorowane i nienadzorowane w eksperymentach klaster, klasyfikacji lub regresji.
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
tags: ''
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/18/2017
ms.openlocfilehash: e1c7030a5f0c6e13653b302fcb48e7d4efa232c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Wybór algorytmów dla usługi Microsoft Azure Machine Learning
Odpowiedź na pytanie "Jakie Algorytm uczenia maszynowego należy używać?" jest zawsze "Zależy." To zależy od rozmiaru, jakości i charakter danych. To zależy co chcesz zrobić z odpowiedzią. To zależy jak matematyczne algorytmu był przekształcić instrukcje dla komputera, którego używasz. I jest ono zależne na czas, jaki ma. Nawet najbardziej doświadczeni analityków danych nie wiadomo, który algorytm będzie wykonywać najlepiej przed podjęciem próby je.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Algorytm uczenia maszynowego Ściągawka
**Microsoft Azure Machine Learning algorytmu Cheat arkusza** pomaga wybrać prawa komputera Algorytm uczenia dla rozwiązań analizy predykcyjnej z biblioteki Microsoft Azure Machine Learning algorytmów.
W tym artykule przedstawiono sposób jego użycia.

> [!NOTE]
> Aby pobrać przydatną ściągawkę i skorzystać z tego artykułu, przejdź do [Machine learning arkusz ze wskazówkami dotyczącymi algorytmu dla programu Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Ten arkusz ze wskazówkami dotyczącymi ma odbiorców bardzo na uwadze: początku danych naukowca przy użyciu poziomu Absolwenci bez tytułu machine learning, próby wybrać algorytm zacząć w usłudze Azure Machine Learning Studio. Oznacza to ułatwia niektóre generalizacji i oversimplifications, że jego punktów w kierunku bezpieczne. Oznacza to również, że istnieje wiele algorytmów niewymienione w tym miejscu. Wraz z rozwojem usługi Azure Machine Learning na pełniejsze zbiór dostępnych metod, dodamy je.

Te zalecenia są skompilowane opinii i porad z wielu analityków danych, a machine learning ekspertów. Nie możemy zaakceptować wszystkie, ale podejmowano próby ujednolicenie opinie naszych do nierównej konsensu. Większość instrukcji zastrzeżeń rozpoczynać się od "Zależy..."

### <a name="how-to-use-the-cheat-sheet"></a>Jak używać arkusz ze wskazówkami
Przeczytaj ścieżkę i algorytmu etykiet na wykresie jako "dla  *&lt;etykieta ścieżki&gt;*, użyj  *&lt;algorytm&gt;*." Na przykład "dla *szybkości*, użyj *dwie klasy Regresja logistyczna*." Czasami więcej niż jedna gałąź ma zastosowanie.
Czasami żaden z nich nie jest dokładne dopasowanie. Są one przeznaczone do można reguły z thumb zalecenia, nie martw się o dokładnie.
Kilka analityków danych, które I zawsze mówię z wymienionych które tylko się sposobem znaleźć bardzo najlepsze algorytm jest spróbuj je wszystkie.

Oto przykład z [galerii Azure AI](http://gallery.cortanaintelligence.com/) eksperymentu, która próbuje kilka algorytmów na tych samych danych i porównanie wyników: [porównania klasyfikatory wielu klas: list rozpoznawania](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Aby pobrać i wydrukować diagram, który zawiera przegląd możliwości usługi Machine Learning Studio, zobacz [Diagram przeglądowy możliwości usługi Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Odmian uczenia maszynowego
### <a name="supervised"></a>Nadzorowane
Algorytmy uczenia nadzorowanego tworzenia prognoz na podstawie zestawu przykładów. Na przykład historycznych giełdowych może służyć do zagrożenia prób w przyszłości. Każdy przykład używany do trenowania etykietą wartości istotnej — w takim przypadku giełdowy. Algorytm uczenia nadzorowanego wyszukuje wzorce w etykiety tych wartości. Można używać żadnych informacji, które mogą być użyteczne — dzień tygodnia, sezonu, danych finansowych firmy, Typ branży, destrukcyjne zdarzenia geograficznymi — oraz każdy algorytm szuka różnego rodzaju wzorce. Po algorytm znalazł najlepsze wzorzec można go, używa tego wzorca do tworzenia prognoz dotyczących bez etykiety danych testowych — ceny w przyszłości.

Uczenia nadzorowanego jest typem popularnych i przydatne uczenia maszynowego. Z jednym wyjątkiem wszystkie moduły w usłudze Azure Machine Learning są uczenia nadzorowanego algorytmów. Istnieje kilka określonych typów uczenia nadzorowanego, które są przedstawiane w ramach usługi Azure Machine Learning: wykrywania anomalii, klasyfikacji i regresji.

* **Klasyfikacja**. Gdy dane są używane do prognozowania kategorii, uczenia nadzorowanego jest również nazywany klasyfikacji. Dotyczy to podczas przypisywania obrazu jako obraz "kot" lub "dog". Gdy istnieją tylko dwa wybory, jest nazywany **dwuklasowych** lub **dwumianu klasyfikacji**. Jeśli ma więcej kategorii jako podczas przewidywania wygrał użytkownik z turnieju Madness marca NCAA, ten problem jest nazywany **klasyfikacji wielu klasy**.
* **Regresja**. Gdy jest on przewidzieć wartości podobnie jak w przypadku giełdowych, uczenia nadzorowanego nosi nazwę regresji.
* **Wykrywanie anomalii**. Czasami celem jest zidentyfikowanie punktów danych, które są po prostu niezwykłe. W wykrywaniu oszukańczych transakcji na przykład wszystkie wzorce wydatków wyjątkowa karty kredytowej są podejrzane. Ewentualne zmiany są tak wiele i wygląda tak kilka, że nie jest to możliwe, aby dowiedzieć się, jakie działanie fałszywych przykłady szkolenia. Podejście, które przyjmuje wykrywania anomalii jest po prostu Dowiedz się, jakie normalne działanie wygląda (przy użyciu transakcji fałszywych historii) i zidentyfikować wszystko, co znacznie różni się.

### <a name="unsupervised"></a>Nienadzorowane
Uczenie nienadzorowane punktów danych, musi bez etykiet skojarzonych z nimi. Zamiast tego celem Algorytm uczenia nienadzorowanych jest organizowania danych w określony sposób lub do opisania jego struktury. Oznacza to, grupowanie go w klastrach lub znajdowania różne sposoby patrzeć dane złożone, aby był wyświetlany prostszy i bardziej zorganizowany.

### <a name="reinforcement-learning"></a>Wzmocnienie learning
W wzmocnienie uczenia algorytm pobiera wybierz akcję w odpowiedzi na każdym punkcie danych. Algorytm uczenia również odbiera sygnał opłatą przez krótki czas później, wskazującą, jaka była decyzji.
Na podstawie tych, algorytm modyfikuje strategią aby osiągnąć najwyższą osób trzecich. Obecnie nie ma żadnych wzmocnienie learning algorithm moduły w usłudze Azure Machine Learning. Wzmocnienie learning jest typowe w przypadku robotics, gdzie zbiór odczyty czujników w jednym punkcie w czasie jest punkt danych, a algorytm należy wybrać robota następnej akcji. Jest to również fizyczną nadające się do Internet rzeczy aplikacji.

## <a name="considerations-when-choosing-an-algorithm"></a>Zagadnienia dotyczące wybierania algorytmów
### <a name="accuracy"></a>Dokładność
Pobieranie możliwe najdokładniejszych odpowiedzi nie zawsze jest konieczne.
Czasami zbliżenia jest odpowiednia, w zależności od tego, co ma być używany dla. Jeśli tak jest, może być znacznie Wytnij czas przetwarzania przez spełni więcej metod przybliżonej. Inną zaletą więcej metod przybliżonej jest, że naturalnie charakteryzują się uniknąć [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Czas szkolenia
Liczba minut lub godziny niezbędne do uczenia modelu znacznie różni się między algorytmów. Uczenie czasu jest często ściśle związany dokładność — jeden zwykle dołączony innych. Ponadto niektóre algorytmy są bardziej podatna na liczbę punktów danych niż inne.
Gdy czas jest ograniczona można dysku wybór algorytmu, szczególnie w przypadku, gdy zestaw danych jest duży.

### <a name="linearity"></a>Liniowości
Wiele algorytmów uczenia maszynowego, należy użyć liniowości. Algorytmy liniowej klasyfikacji założono, że klasy mogą być oddzielone prostej (lub jej wielowymiarowego analogowy). Te obejmują Regresja logistyczna i obsługuje wektor maszyny (zgodnie z implementacją w usłudze Azure Machine Learning).
Algorytmy regresji liniowej przyjęto założenie, skorzystaj z prostej trendów w danych. Te wartości domyślne nie są nieprawidłowe dla niektórych problemów, ale na innych dokładność one Przenieś w dół.

![Klasa inne niż liniowy granic][1]

***Granicą liniową inne niż klasa*** *-zależne algorytm klasyfikacji liniowej spowodowałoby niski dokładności*

![Dane z rożne trend][2]

***Dane z rożne trend*** *— za pomocą metody regresji liniowej wygenerowanie dużo błędów większe niż to konieczne*

Niezależnie od ich zagrożenia liniowej algorytmy są bardzo popularny jako pierwszy wiersz ataku. Mają zwykle algorithmically proste i szybkie do uczenia.

### <a name="number-of-parameters"></a>Liczba parametrów
Parametry są pokręteł, które pobiera naukowca danych, aby włączyć podczas konfigurowania algorytmu. Są one numery, które wpływają na działanie tego algorytmu, na przykład błąd uszkodzenia lub liczby iteracji lub opcji między wariantów zachowania algorytm. Czas szkolenia i dokładność algorytmu czasami mogą być bardzo czułe na wprowadzenie odpowiednich ustawień. Zwykle algorytmów z dużą liczbą parametrów wymagają większości prób i błędów można znaleźć dobrej kombinacji.

Alternatywnie istnieje [kominów parametru](algorithm-parameters-optimize.md) blok modułu z usługi Azure Machine Learning, która automatycznie podejmie wszystkie kombinacje parametrów o dowolnym możesz wybrać. Jest to dobry sposób na upewnij się, że zostały objęte miejsca parametr, czas wymagany do uczenia modelu zwiększa wykładniczo z liczbą parametrów.

Odwróć jest, że mających wiele parametrów zwykle oznacza, że algorytm ma większą elastyczność. Często można uzyskać bardzo dobre dokładności. Pod warunkiem, że można znaleźć odpowiednie połączenie ustawienia parametru.

### <a name="number-of-features"></a>Wiele funkcji
Dla niektórych typów danych liczbę funkcji mogą być bardzo duże w porównaniu do liczby punktów danych. Jest to często w przypadku danych tekstowych lub genetyka. Duża liczba funkcji można bog dół niektórych algorytmów uczenia, co szkolenia czasu unfeasibly długo. Obsługa wektor maszyny są szczególnie dobrze nadają się do tego przypadku (patrz poniżej).

### <a name="special-cases"></a>Specjalne przypadki
Niektóre algorytmów uczenia zakładają określonej struktury danych lub zakładanych wyników. Jeśli znajdziesz taki, który odpowiada potrzebom użytkownika, jego możesz nadać bardziej przydatne wyniki, dokładniejszych prognoz lub szybsze szkolenia.

| **Algorytm** | **Accuracy** | **Czas szkolenia** | **Liniowości** | **Parametry** | **Uwagi** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klasyfikacja dwa — klasa** | | | | | |
| [Regresja logistyczna](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [decyzja lasu](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [Dżungla decyzji](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Mało pamięci |
| [drzewo decyzyjne boosted](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Zużycie pamięci |
| [sieć neuronowa](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Możliwe jest dodatkowe dostosowanie](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [perceptron uśrednionej](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [Obsługa maszyny wektora](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Nadaje się do funkcji dużych zestawów |
| [Obsługa lokalnie głębokie wektor maszyny](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Nadaje się do funkcji dużych zestawów |
| [Maszyna punktu Bayesa](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Klasa usługi klasyfikacji** | | | | | |
| [Regresja logistyczna](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [decyzja lasu](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [Dżungla decyzji ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Mało pamięci |
| [sieć neuronowa](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Możliwe jest dodatkowe dostosowanie](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Zobacz właściwości wybrano metodę dwa — klasa |
| **Regression** | | | | | |
| [liniowy](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Estymacja Bayesian liniowy](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [decyzja lasu](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [drzewo decyzyjne boosted](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Zużycie pamięci |
| [kwantyl szybkiego lasu](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Zamiast prognoz punktu dystrybucji |
| [sieć neuronowa](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Możliwe jest dodatkowe dostosowanie](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poissona](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Technicznie dziennika liniowej. Do przewidywania liczby |
| [{numer porządkowy](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Do przewidywania porządkowanie ranga |
| **Wykrywanie anomalii** | | | | | |
| [Obsługa maszyny wektora](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Szczególnie przydatne dla funkcji dużych zestawów |
| [Wykrywanie anomalii oparte na PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-średnich](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Algorytm klastrowanie |

**Właściwości algorytmu:**

**●** -pokazuje znakomity dokładność, szkolenia szybkiego godziny i użyj liniowości

**(○)** — przedstawia dobrą dokładność i czasy umiarkowane szkolenia

## <a name="algorithm-notes"></a>Uwagi dotyczące algorytmu
### <a name="linear-regression"></a>Regresja liniowa
Jak wspomniano wcześniej, [regresji liniowej](https://msdn.microsoft.com/library/azure/dn905978.aspx) dopasowuje linii (lub płaszczyzny lub hyperplane) do zestawu danych. Jest najważniejszą metodą roboczą, proste i szybkie, ale może być nadmiernie simplistic dla niektórych problemów.
Wyszukaj tutaj [samouczek regresji liniowej](linear-regression-in-azure.md).

![Dane z trendu liniowego][3]

***Dane z trendu liniowego***

### <a name="logistic-regression"></a>Regresja logistyczna
Mimo że uwzględnia złudzenia "regresji" w nazwie, Regresja logistyczna jest rzeczywiście zaawansowane narzędzie [dwuklasowych](https://msdn.microsoft.com/library/azure/dn905994.aspx) i [multiklasa](https://msdn.microsoft.com/library/azure/dn905853.aspx) klasyfikacji. To szybki i prosty. Fakt, że używa tego "-kształcie krzywej zamiast prostej ułatwia fizycznych dopasowanie rozdzielania danych w grupach. Regresja logistyczna zapewnia liniowej klasy granice, więc gdy jest używany, upewnij się, że liniowej zbliżenia się coś, co może współdziałać z.

![Regresja logistyczna dwuklasowych danych z tylko jedną funkcję][4]

***Regresja logistyczna dwuklasowych danych z tylko jedną funkcję*** *-granic klasy jest punkt, w którym logistyczna krzywej jest właśnie maksymalnie zbliżony obie klasy*

### <a name="trees-forests-and-jungles"></a>Drzewa, lasów i dżungle
Decyzja lasów ([regresji](https://msdn.microsoft.com/library/azure/dn905862.aspx), [dwuklasowych](https://msdn.microsoft.com/library/azure/dn906008.aspx), i [multiklasa](https://msdn.microsoft.com/library/azure/dn906015.aspx)), decyzja dżungle ([dwuklasowych](https://msdn.microsoft.com/library/azure/dn905976.aspx) i [ multiklasa](https://msdn.microsoft.com/library/azure/dn905963.aspx)), a boosted drzew decyzyjnych ([regresji](https://msdn.microsoft.com/library/azure/dn905801.aspx) i [dwuklasowych](https://msdn.microsoft.com/library/azure/dn906025.aspx)) są wszystkie na podstawie drzew decyzyjnych, maszynę podstawowych koncepcji learning. Istnieje wiele odmian drzew decyzyjnych, ale wszystkie one tak samo postąpić — podzielić miejsca funkcji w regionach przede wszystkim taką samą etykietę. Mogą to być regionów spójne kategorii lub stałą wartość, w zależności od tego, czy podczas klasyfikacji lub regresji.

![Drzewo decyzyjne dzieli obszar funkcji][5]

***Drzewo decyzyjne dzieli miejsca funkcji w regionach około uniform wartości***

Ponieważ miejsca funkcji można podzielić na arbitralnie małych regionów, to proste nam sobie wyobrazić, dzielenie go wystarczająco precyzyjne, aby mieć jeden punkt danych na region. Jest to przykład extreme overfitting. Aby tego uniknąć, dużych zestawów drzew opracowano przy szczególną matematyczne podjęte nie skorelowanych drzewa. Średnia tego "lasu decyzji" jest drzewa, który pozwala uniknąć overfitting. Lasy decyzji mogą za pomocą dużej ilości pamięci. Dżungle decyzyjne są variant, który zużywa mniej pamięci kosztem nieco dłużej szkolenia.

Drzewa decyzyjne boosted uniknąć overfitting poprzez ograniczenie, ile razy można podzielić one i jak najmniejszej liczby punktów danych są dozwolone w każdym regionie. Algorytm tworzy sekwencję drzew, z których każdy uzyskuje informacje o kompensacji błąd w drzewie przed. Wynik jest bardzo dokładne uczeń, który zwykle używać dużej ilości pamięci. Aby uzyskać pełny opis techniczny, zapoznaj się z [Friedman w oryginalnym papieru](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Szybkie regresji kwantyl lasu](https://msdn.microsoft.com/library/azure/dn913093.aspx) jest odmianą drzew decyzyjnych w przypadku specjalne, które chcesz wiedzieć, nie tylko typowe wartość (mediany) danych w regionie, ale jej dystrybucji w postaci quantiles.

### <a name="neural-networks-and-perceptrons"></a>Sieci neuronowe i perceptrons
Sieci neuronowe są inteligencji inspirowana obejmujące algorytmów uczenia [multiklasa](https://msdn.microsoft.com/library/azure/dn906030.aspx), [dwuklasowych](https://msdn.microsoft.com/library/azure/dn905947.aspx), i [regresji](https://msdn.microsoft.com/library/azure/dn905924.aspx) problemów. Pochodzą nieskończone różne, ale sieci neuronowe w usłudze Azure Machine Learning znajdują się w postaci ukierunkowanej acykliczne wykresy. Oznacza to, że wejściowy funkcji są przekazywane do przodu (nigdy nie wstecz) sekwencję warstwy przed są przekształcane w danych wyjściowych. W każdej warstwie dane wejściowe są liczone w różnych kombinacjach, sumowane i przekazywane do następnej warstwy. Możliwość informacje pozornie magic klasy zaawansowane trendów granic i danych, powoduje to połączenie prostych obliczeń. Wielu warstw sieci tego rodzaju wykonania "głębokiego learning" który paliwa w związku z czym duża techniczna raportowania i fikcja nauki.

Ten wysokiej wydajności nie zawiera bezpłatnie, ale. Sieci neuronowe może zająć dużo czasu do uczenia, szczególnie w przypadku dużych zestawów danych z dużą ilością funkcji. Mają one również więcej parametrów niż większość algorytmy, które oznacza, że parametr kominów znacznie rozszerza czasu szkolenia.
I dla tych overachievers, którzy chcą [określić własne struktury sieci](http://go.microsoft.com/fwlink/?LinkId=402867), możliwości są inexhaustible.

![Granice rozpoznawane przez sieci neuronowe][6]
***granice rozpoznawane przez sieci neuronowe może być złożona i nieregularne***

[Dwuklasowych średnio perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) jest odpowiedzi sieci neuronowe niebotycznych godziny szkolenia. Struktura sieci, która zapewnia granice liniowej klasy jest używany. Jest prawie pierwotnych przez obecnie standardów, ale w przeszłości dużo pracy niezawodnie i jest mały, aby szybko dowiedzieć się więcej.

### <a name="svms"></a>SVMs
Obsługa wektor maszyny (SVMs) znaleźć granicy oddziela klasy przez jako szeroki margines, jak to możliwe. Gdy dwie klasy nie może być wyraźnie oddzielone, algorytmy znaleźć najlepsze granicę, którą można ich. Podczas zapisywania w usłudze Azure Machine Learning [SVM dwuklasowych](https://msdn.microsoft.com/library/azure/dn905835.aspx) robi to tylko prosta linia. (W SVM mowy używa on liniowej jądra). Ponieważ pozwala to zbliżenia liniowego, jest w stanie uruchomić stosunkowo szybko. Gdzie naprawdę źródło jest za pomocą funkcji intensywny danych, takich jak tekst lub genomicznej. W takich przypadkach mogą SVMs do rozdzielania klas szybciej i przy overfitting mniej niż większość inne algorytmy, oprócz wymagające niewielkie ilości pamięci.

![Wektor granicę klasy pomocy technicznej][7]

***Margin oddzielenie dwie klasy maksymalizuje granicę techniczną wektor maszyny — klasa***

Innego produktu Microsoft Research [SVM lokalnie głębokie dwuklasowych](https://msdn.microsoft.com/library/azure/dn913070.aspx) jest-liniowej wariant SVM, który zachowuje większość szybkość i pamięci wydajności liniowej wersji. Jest idealny dla przypadków gdzie liniowej podejście nie zapewniają wystarczająco dokładne odpowiedzi. Deweloperzy przechowywane fast dzieląc problem do licznych drobne problemy SVM liniowej. Odczyt [pełny opis](http://proceedings.mlr.press/v28/jose13.html) szczegółowe informacje dotyczące sposobu pobierane poza ta procedura.

Przy użyciu inteligentne rozszerzenia rożne SVMs [SVM jednej klasy](https://msdn.microsoft.com/library/azure/dn913103.aspx) rysuje obramowanie ściśle przedstawiono cały zestaw danych. Jest to przydatne w przypadku wykrywania anomalii. Nietypowe za warte wymienienia są wszystkie nowe punkty danych należących do tej pory poza tę granicę.

### <a name="bayesian-methods"></a>Metody Estymacja Bayesian
Metody Estymacja Bayesian mają pożądane wysokiej jakości: eliminują overfitting. One to robić przez założenie wcześniej o prawdopodobnie dystrybucji odpowiedzi. Inny byproduct tego podejścia jest, że bardzo mało parametrów. Usługa Azure Machine Learning zawiera oba algorytmy Estymacja Bayesian zarówno klasyfikacji ([maszyna punktu Bayesa Two-class](https://msdn.microsoft.com/library/azure/dn905930.aspx)) i regresji ([regresji liniowej Estymacja Bayesian](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Należy pamiętać, że te wniosku, że dane można podzielić lub dopasowania z prostej.

Na notatkę historycznych maszyny punktu Bayesa zostały opracowane w Microsoft Research. Mają one niektóre wyjątkowo doskonałych Praca teoretyczna za nimi. Planuje uczniów zostaje skierowany do [oryginalny artykuł w JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) i [interesującego blogu przez Bishop Krzysztof](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algorytmy specjalne
Jeśli masz bardzo określonego celu, który może być szczęście. W kolekcji usługi Azure Machine Learning istnieją algorytmów specialize w:

- Rank — prognozowania ([Regresja porządkowa](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- Liczba prognozowania ([regresji Poissona](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- wykrywanie anomalii (jedną na podstawie [analizy głównych składników](https://msdn.microsoft.com/library/azure/dn913102.aspx) i jeden na podstawie [maszyny wektorowa obsługa](https://msdn.microsoft.com/library/azure/dn913103.aspx)s)
- klaster ([K-średnich](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Wykrywanie anomalii oparte na PCA][8]

***Wykrywanie anomalii oparte na PCA*** *-większość danych należy do dystrybucji stereotypical; punkty różniących się znacząco od dystrybucji są podejrzane*

![Zestaw danych zgrupowane za pomocą K-średnich][9]

***Zestaw danych są grupowane w pięciu klastrów za pomocą K-średnich***

Jest również zespół [wieloklasowej klasyfikatora jedna v wszystkie](https://msdn.microsoft.com/library/azure/dn905887.aspx), która dzieli problemu klasyfikacji N klasa na N-1 dwuklasowych klasyfikacji problemów. Dokładność, czas szkolenia i właściwości liniowości są określane przez klasyfikatory dwie klasy używane.

![Klasyfikatory klasy dwa połączone tworzą klasyfikatora trzech — klasa][10]

***Para klasyfikatory dwuklasowych połączenie umożliwia tworzą klasyfikatora trzech — klasa***

Azure Machine Learning obejmuje również dostęp do uczenia maszynowego zaawansowaną strukturę pod tytułem [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW powinny kategoryzacji, ponieważ mogą dowiedzieć się, zarówno klasyfikacji i regresji problemów, a nawet można uzyskać z częściowo bez etykiety danych. Można skonfigurować, aby zastosować dowolną liczbę szkoleniowe dotyczące algorytmów, funkcje utraty i algorytmy optymalizacji. Została zaprojektowana od podstaw się być wydajne, równoległe i bardzo szybkiego działania. Obsługuje on funkcji było bardzo dużych zestawów przy minimalnym nakładzie pracy oczywista.
Uruchomiona i przez Microsoft Research jego własnej Jan Langford, VW jest formuła jednego wpisu w polu algorytmów samochodu zapasów. Nie każdy problem pasujące VW, ale jeśli nie należy do Ciebie, może być zdobycie pokonać nauki na interfejsie. Jest również dostępny jako [kod autonomicznej typu open source](https://github.com/JohnLangford/vowpal_wabbit) w wielu językach.

## <a name="more-help-with-algorithms"></a>Więcej informacji o algorytmy
* Aby infographic do pobrania, opisujący algorytmów i przykłady, zobacz [Infographic do pobrania: Machine learning podstawy wraz z przykładami algorytmu](basics-infographic-with-algorithm-examples.md).
* Aby uzyskać listę według kategorii wszystkich algorytmów uczenia maszynowego dostępnych w usłudze Azure Machine Learning Studio, zobacz [zainicjować modelu] [ initialize-model] Machine Learning Studio algorytmu i pomóc w Module.
* Aby uzyskać alfabetyczną listę wszystkich algorytmów i modułów w usłudze Azure Machine Learning Studio, zobacz [A — Z listy modułów usługi Machine Learning Studio] [ a-z-list] algorytmu Studio uczenia maszynowego i pomóc w Module.
* Aby pobrać i wydrukować diagram, który zawiera przegląd możliwości usługi Azure Machine Learning Studio, zobacz [diagram przeglądowy możliwości usługi Azure Machine Learning Studio](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

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
