---
title: Alokacja utajonego dirichletu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Alokacja utajonych dirichlet do grupowania tekstu niesklasyfikowanego w inny sposób na kilka kategorii.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109227"
---
# <a name="latent-dirichlet-allocation"></a>Alokacja utajonego dirichletu

W tym artykule opisano, jak używać modułu **Alokacja utajone dirichlet** w projektancie usługi Azure Machine Learning (wersja zapoznawcza), aby grupować tekst niesklasyfikowany w inny sposób do wielu kategorii. 

Utajona alokacja dirichlet (LDA) jest często używana w przetwarzaniu języka naturalnego (NLP) w celu znalezienia tekstów, które są podobne. Innym częstym terminem jest *modelowanie tematów*.

Ten moduł przyjmuje kolumnę tekstu i generuje następujące dane wyjściowe:

+ Tekst źródłowy wraz z punktacją dla każdej kategorii

+ Macierz charakterystyczna zawierająca wyodrębnione terminy i współczynniki dla każdej kategorii

+ Transformacja, którą można zapisać i ponownie zastosować do nowego tekstu używanego jako dane wejściowe

Ten moduł używa biblioteki scikit-learn. Aby uzyskać więcej informacji na temat scikit-learn, zobacz [Repozytorium GitHub, który zawiera samouczki i wyjaśnienie algorytmu.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Utajone Dirichlet Allocation (LDA) – więcej informacji

Ogólnie rzecz biorąc, LDA nie jest metodą klasyfikacji per se, ale stosuje podejście generatywne. Oznacza to, że nie trzeba podać znanych etykiet klas, a następnie wywnioskować wzorce.  Zamiast tego algorytm generuje model probabilistyczny, który jest używany do identyfikowania grup tematów. Model probabilistyczny służy do klasyfikowania istniejących przypadków szkolenia lub nowych przypadków, które są podaniem do modelu jako dane wejściowe.

Model generujący może być lepszy, ponieważ pozwala uniknąć podejmowania żadnych silnych założeń dotyczących relacji między tekstem a kategoriami i używa tylko rozkładu słów do matematycznie modelowych tematów.

+ Teoria jest omówiona w tym artykule, dostępne w formacie PDF do pobrania: [Utajony Dirichlet Alokacja: Blei, Ng i Jordanii](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ Implementacja w tym module opiera się na [bibliotece scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) dla LDA.

Aby uzyskać więcej informacji, zobacz sekcję [Uwagi techniczne.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Jak skonfigurować alokację utajonego dirichletu

Ten moduł wymaga zestawu danych zawierającego kolumnę tekstu, nieprzetworzonego lub wstępnie przetworzonego.

1. Dodaj **moduł alokacji utajonej dirichlet** do potoku.

2. Jako dane wejściowe dla modułu, podaj zestaw danych zawierający jedną lub więcej kolumn tekstowych.

3. W przypadku **kolumn docelowych**wybierz jedną lub więcej kolumn zawierających tekst do przeanalizowania.

    Można wybrać wiele kolumn, ale muszą one być typu danych ciągu.

    Ogólnie rzecz biorąc, ponieważ LDA tworzy dużą macierz operacji z tekstu, zazwyczaj analizować pojedynczą kolumnę tekstową.

4. W obszarze **Liczba tematów do modelowania**wpisz liczbę całkowitą z 1 do 1000, która wskazuje, ile kategorii lub tematów chcesz wyprowadzić z tekstu wejściowego.

    Domyślnie tworzonych jest 5 tematów.

5. W przypadku **N-gramów**należy określić maksymalną długość N-gramów generowanych podczas mieszania.

    Wartość domyślna to 2, co oznacza, że generowane są zarówno bigramy, jak i jednogramy.

6. Wybierz opcję **Normalizuj,** aby przekonwertować wartości wyjściowe na prawdopodobieństwa. W związku z tym zamiast reprezentować przekształcone wartości jako liczby całkowite, wartości w zestawie danych wyjściowych i elementów zostaną przekształcone w następujący sposób:

    + Wartości w zestawie danych będą reprezentowane jako `P(topic|document)`prawdopodobieństwo, gdzie .

    + Wartości w macierzy tematu funkcji będą `P(word|topic)`reprezentowane jako prawdopodobieństwo, gdzie .

    > [!NOTE] 
    > W projektancie usługi Azure Machine Learning (wersja zapoznawcza), ponieważ biblioteka, na podstawie, scikit-learn, nie obsługuje już nienormalizowanego *doc_topic_distr* danych wyjściowych z wersji 0.19, dlatego w tym module parametr **Normalize** można zastosować tylko do danych **wyjściowych macierzy tematu funkcji,** **dane wyjściowe przekształconego zestawu danych** są zawsze znormalizowane.

7. Wybierz opcję **Pokaż wszystkie opcje**, a następnie ustaw ją na WARTOŚĆ PRAWDA, jeśli chcesz wyświetlić, a następnie ustaw dodatkowe parametry zaawansowane.

    Parametry te są specyficzne dla scikit-learn implementacji LDA. Istnieje kilka dobrych tutoriali na temat LDA w scikit-learn, a także oficjalny [dokument scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parametr Rho**. Podaj wcześniejsze prawdopodobieństwo dla rozrzedu tematu. Odpowiada parametrowi sklearna. `topic_word_prior` Należy użyć wartości 1, jeśli oczekujesz, że rozkład słów jest płaski; wszystkie słowa są zakładane equiprobable. Jeśli uważasz, że większość słów wydaje się słabo, możesz ustawić je na znacznie niższą wartość.

    + **Parametr alfa**. Określ wcześniejsze prawdopodobieństwo dla sparsity wagi tematu dla dokumentu.  Odpowiada parametrowi sklearna. `doc_topic_prior`

    + **Szacowana liczba dokumentów**. Wpisz liczbę reprezentującą najlepszą szacunkową liczbę dokumentów (wierszy), które zostaną przetworzone. Dzięki temu moduł przydzielić tabelę mieszania o wystarczającym rozmiarze.  Odpowiada parametrowi `total_samples` w scikit-learn.

    + **Rozmiar partii**. Wpisz liczbę, która wskazuje, ile wierszy należy uwzględnić w każdej partii tekstu wysłanego do modelu LDA. Odpowiada parametrowi `batch_size` w scikit-learn.

    + **Wartość początkowa iteracji używanej w harmonogramie aktualizacji uczenia się**. Określ wartość początkową, która ma wartość początkową dla wczesnych iteracji w uczeniu się online. Odpowiada parametrowi `learning_offset` w scikit-learn.

    + **Moc zastosowana do iteracji podczas aktualizacji**. Wskaż poziom mocy zastosowanej do liczby iteracji, aby kontrolować szybkość uczenia się podczas aktualizacji online. Odpowiada parametrowi `learning_decay` w scikit-learn.

    + **Liczba przebiegów nad danymi**. Określ maksymalną liczbę razy algorytm będzie przełączać się nad danymi. Odpowiada parametrowi `max_iter` w scikit-learn.

8. Zaznacz tę opcję, **Utwórz słownik ngramów** lub **Zmień słownik ngramów przed LDA**, jeśli chcesz utworzyć listę n-gramów w przebiegu początkowym, przed sklasyfikowaniem tekstu.

    Jeśli wcześniej utworzysz słownik początkowy, można później użyć słownika podczas przeglądania modelu. Możliwość mapowania wyników na tekst, a nie indeksy liczbowe, jest na ogół łatwiejsza do interpretacji. Jednak zapisanie słownika potrwa dłużej i użyje dodatkowego magazynu.

9. W obszarze **Maksymalny rozmiar słownika ngram**wpisz całkowitą liczbę wierszy, które można utworzyć w słowniku n-gram.

    Ta opcja jest przydatna do kontrolowania rozmiaru słownika. Jeśli jednak liczba ngramów na danych wejściowych przekracza ten rozmiar, mogą wystąpić kolizje.

10. Prześlij potok. Moduł LDA używa przepolonowania Bayesa do określenia, jakie tematy mogą być skojarzone z poszczególnymi słowami. Słowa nie są związane wyłącznie z żadnymi tematami lub grupami; zamiast tego każdy n-gram ma wyuczone prawdopodobieństwo bycia skojarzonym z dowolną z odkrytych klas.

## <a name="results"></a>Wyniki

Moduł posiada dwa wyjścia:

+ **Przekształcony zestaw danych**: Zawiera tekst wejściowy i określoną liczbę odnalezionych kategorii, wraz z wynikami dla każdego przykładu tekstu dla każdej kategorii.

+ **Macierz tematu funkcji**: Kolumna po lewej stronie zawiera wyodrębniony element tekstowy i jest kolumna dla każdej kategorii zawierająca wynik dla tej funkcji w tej kategorii.


### <a name="lda-transformation"></a>Transformacja LDA

Ten moduł również wyprowadza *transformację LDA,* która stosuje LDA do zestawu danych.

Transformację można zapisać, rejestrując zestaw danych w obszarze **Karty Dane+dzienniki** w prawym okienku modułu i ponownie użyć go dla innych zestawów danych. Może to być przydatne, jeśli przeszkolony na dużym korpusie i chcesz ponownie użyć współczynników lub kategorii.

### <a name="refining-an-lda-model-or-results"></a>Udoskonalanie modelu LDA lub wyników

Zazwyczaj nie można utworzyć jednego modelu LDA, który spełni wszystkie potrzeby, a nawet model przeznaczony dla jednego zadania może wymagać wielu iteracji w celu zwiększenia dokładności. Zaleca się, aby wypróbować wszystkie te metody, aby poprawić swój model:

+ Zmiana parametrów modelu
+ Korzystanie z wizualizacji w celu zrozumienia wyników
+ Uzyskiwanie opinii ekspertów w danej dziedzinie, aby upewnić się, czy wygenerowane tematy są przydatne.

Środki jakościowe mogą być również przydatne do oceny wyników. Aby ocenić wyniki modelowania tematów, należy wziąć pod uwagę:

+ Dokładność - Czy podobne przedmioty są naprawdę podobne?
+ Różnorodność - Czy model może rozróżniać podobne elementy, gdy jest to wymagane dla problemu biznesowego?
+ Skalowalność — działa w szerokim zakresie kategorii tekstu lub tylko w wąskiej domenie docelowej?

Dokładność modeli opartych na LDA można często poprawić za pomocą przetwarzania języka naturalnego do czyszczenia, podsumowywania i upraszczania lub kategoryzowania tekstu. Na przykład następujące techniki, wszystkie obsługiwane w usłudze Azure Machine Learning, może poprawić dokładność klasyfikacji:

+ Zatrzymaj usuwanie wyrazów

+ Normalizacja przypadków

+ Lemmatyzacja lub

+ Rozpoznawanie jednostek nazwanych

Aby uzyskać więcej informacji, zobacz [Tekst przetwarzania wstępnego](preprocess-text.md).

W projektancie można również użyć bibliotek Języka R lub Python do przetwarzania tekstu: [Wykonywanie skryptu R](execute-r-script.md), [Wykonywanie skryptu Pythona](execute-python-script.md)



## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="implementation-details"></a>Szczegóły implementacji

Domyślnie rozkłady wyjść dla przekształconego zestawu danych i macierzy tematu funkcji są znormalizowane jako prawdopodobieństwa.

+ Przekształcony zestaw danych jest znormalizowany jako warunkowe prawdopodobieństwo tematów danego dokumentu. W takim przypadku suma każdego wiersza jest równa 1.

+ Macierz tematu funkcji jest znormalizowana jako warunkowe prawdopodobieństwo wyrazów danego tematu. W takim przypadku suma każdej kolumny jest równa 1.

> [!TIP]
> Od czasu do czasu moduł może zwrócić pusty temat, który jest najczęściej spowodowane przez pseudo-random inicjowania algorytmu.  W takim przypadku można spróbować zmienić powiązane parametry, takie jak maksymalny rozmiar słownika N-gram lub liczba bitów do użycia do mieszania funkcji.

### <a name="lda-and-topic-modeling"></a>LDA i modelowanie tematów

Utajona alokacja dirichlet (LDA) jest często używana do *modelowania tematów opartych na zawartości,* co w zasadzie oznacza kategorie uczenia się z niesklasyfikowanego tekstu. W modelowaniu tematów opartych na zawartości temat jest dystrybucją wyrazów.

Na przykład załóżmy, że dostarczyłeś korpus opinii klientów, który zawiera wiele, wiele produktów. Tekst recenzji, które zostały przesłane przez wielu klientów w czasie będzie zawierać wiele terminów, z których niektóre są używane w wielu tematach.

**Temat** identyfikowany przez proces LDA może reprezentować recenzje dla poszczególnych produktów A lub może reprezentować grupę recenzji produktów. Dla LDA sam temat jest tylko rozkładem prawdopodobieństwa w czasie dla zestawu słów.

Warunki rzadko są dostępne wyłącznie dla jednego produktu, ale mogą odnosić się do innych produktów lub być ogólnymi terminami, które mają zastosowanie do wszystkiego ("wielki", "okropny"). Inne terminy mogą być słowa hałasu.  Jednak ważne jest, aby zrozumieć, że metoda LDA nie ma na celu uchwycić wszystkie słowa we wszechświecie, lub zrozumieć, jak słowa są powiązane, oprócz prawdopodobieństwa współwystępowania. Może grupować tylko wyrazy, które były używane w domenie docelowej.

Po obliczeniu terminu indeksy poszczególne wiersze tekstu są porównywane przy użyciu miary podobieństwa opartego na odległości, aby ustalić, czy dwa elementy tekstu są podobne do siebie.  Na przykład może się okazać, że produkt ma wiele nazw, które są silnie skorelowane. Lub, może się okazać, że zdecydowanie negatywne terminy są zwykle związane z określonego produktu. Miara podobieństwa służy zarówno do identyfikowania powiązanych terminów, jak i tworzenia zaleceń.

###  <a name="module-parameters"></a>Parametry modułu

|Nazwa|Typ|Zakres|Optional (Opcjonalność)|Domyślne|Opis|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Kolumny docelowe|Wybór kolumny||Wymagany|StringFeature (Nieujmko napis)|Nazwa lub indeks kolumny docelowej|  
|Liczba tematów do modelowania|Liczba całkowita|[1;1000]|Wymagany|5|Modelowanie dystrybucji dokumentów względem tematów N|  
|N-gramy|Liczba całkowita|[1;10]|Wymagany|2|Kolejność N-gramów wygenerowanych podczas mieszania|  
|Normalizacji|Wartość logiczna|Prawda lub fałsz|Wymagany|true|Normalizuj dane wyjściowe do prawdopodobieństwa.  Przekształcony zestaw danych będzie P (temat&#124;dokumentu), a macierz tematu funkcji będzie P(słowo&#124;temat)|  
|Pokaż wszystkie opcje|Wartość logiczna|Prawda lub fałsz|Wymagany|False|Przedstawia dodatkowe parametry specyficzne dla scikit-learn online LDA|  
|Parametr Rho|Liczba zmiennoprzecinkowa|[0.00001;1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,01|Poprzednia dystrybucja słowa tematu|  
|Parametr Alfa|Liczba zmiennoprzecinkowa|[0.00001;1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,01|Wcześniejsza dystrybucja tematu dokumentu|  
|Szacowana liczba dokumentów|Liczba całkowita|[1;int. MaxValue]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|1000|Szacowana liczba dokumentów (odpowiada parametrowi total_samples)|  
|Wielkość partii|Liczba całkowita|[1;1024]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|32|Wielkość partii|  
|Wartość początkowa iteracji używana w harmonogramie aktualizacji szybkości uczenia się|Liczba całkowita|[0;int. MaxValue]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0|Wartość początkowa, która ma wartość downweights learning dla wczesnych iteracji. Odpowiada parametrowi learning_offset|  
|Moc zastosowana do iteracji podczas aktualizacji|Liczba zmiennoprzecinkowa|[0.0;1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,5|Moc zastosowana do liczby iteracji w celu kontrolowania szybkości uczenia się. Odpowiada parametrowi learning_decay |  
|Liczba iteracji szkoleniowych|Liczba całkowita|[1;1024]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|25|Liczba iteracji szkoleniowych|  
|Tworzenie słownika ngramów|Wartość logiczna|Prawda lub fałsz|Ma zastosowanie, gdy pole wyboru **Pokaż wszystkie opcje** *nie* jest zaznaczone|True|Tworzy słownik ngramów przed obliczeniem LDA. Przydatne do kontroli modelu i interpretacji|  
|Maksymalny rozmiar słownika ngram|Liczba całkowita|[1;int. MaxValue]|Ma zastosowanie, gdy opcja **Kompilacja słownika ngrams** jest True|20000|Maksymalny rozmiar słownika ngramów. Jeśli liczba tokenów na danych wejściowych przekracza ten rozmiar, mogą wystąpić kolizje|  
|Liczba bitów do użycia do mieszania funkcji|Liczba całkowita|[1;31]|Ma zastosowanie, gdy pole wyboru **Pokaż wszystkie opcje** *nie* jest zaznaczone, a **słownik kompilacji ngramów** jest fałszywy|12|Liczba bitów do użycia do mieszania funkcji| 
|Tworzenie słownika ngramów przed LDA|Wartość logiczna|Prawda lub fałsz|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|True|Tworzy słownik ngramów przed LDA. Przydatne do kontroli modelu i interpretacji|  
|Maksymalna liczba ngramów w słowniku|Liczba całkowita|[1;int. MaxValue]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje,** a opcja **Kompilacja słownika ngramów** jest True|20000|Maksymalny rozmiar słownika. Jeśli liczba tokenów na danych wejściowych przekracza ten rozmiar, mogą wystąpić kolizje|  
|Liczba bitów skrótu|Liczba całkowita|[1;31]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje,** a opcja **Kompilacja słownika ngramów** jest false|12|Liczba bitów do użycia podczas mieszania funkcji|   


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning.   
Aby uzyskać listę błędów specyficznych dla modułów, zobacz [Wyjątki i kody błędów dla projektanta](designer-error-codes.md).
