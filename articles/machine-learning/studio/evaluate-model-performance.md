---
title: Ocena wydajności modelu w uczeniu maszynowym | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak do oceny wydajności modelu w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: bb49fd2fe7f72e211fbbda7cffdd2308c2c36fba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834237"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Ocenianie wydajności modelu w usłudze Azure Machine Learning
W tym artykule przedstawiono sposób oceny wydajności modelu w usłudze Azure Machine Learning Studio i zawiera krótki opis dostępnych metryk dla tego zadania. Dostępne są trzy typowe scenariusze uczenia nadzorowanego: 

* Regresja
* klasyfikacji binarnej 
* wieloklasowej klasyfikacji

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Ocena wydajności modelu jest jednym z podstawowych etapów w procesie nauki danych. Wskazuje on, jak pomyślnie oceniania (prognoz) zestaw danych został uczonego modelu. 

Azure Machine Learning obsługuje oceny modelu za pomocą dwóch jego głównej maszynie modułów szkoleniowych: [Evaluate Model] [ evaluate-model] i [krzyżowa Weryfikacja modelu][cross-validate-model]. Te moduły umożliwiają można zobaczyć, jak model wykonuje pod względem liczba metryki, które są często używane w uczeniu maszynowym i statystyki.

## <a name="evaluation-vs-cross-validation"></a>Ocena programu vs. Krzyżowe sprawdzanie poprawności
Ocena i krzyżowego sprawdzania poprawności metodami standardowa do pomiaru wydajności modelu. Oba generować metryki oceny można sprawdzić lub porównać tych innych modeli.

[Ocena modelu] [ evaluate-model] oczekuje scored zestawu danych jako dane wejściowe (lub 2 w przypadku chcesz porównać wydajność różne modele 2). To oznacza, że konieczne w celu przeszkolenia przy użyciu modelu [Train Model] [ train-model] modułu i dokonywać prognoz na niektórych zestawu danych za pomocą [Score Model] [ score-model] modułu, zanim będziesz w stanie ocenić wyniki. Obliczanie opiera się na scored etykiety/prawdopodobieństwa wraz z true etykiety, które są danymi wyjściowymi przygotowane przez [Score Model] [ score-model] modułu.

Alternatywnie służy krzyżowego sprawdzania poprawności do wykonywania wielu ocenić train wynik operacji (10 złożeń) automatycznie na różnych podzbiór danych wejściowych. Dane wejściowe jest podzielony na 10 części, w których jeden jest zastrzeżona na potrzeby testowania, a inne 9 szkolenia. Ten proces jest powtarzany 10 razy, a metryki oceny zostały uśrednione. Pomaga to w określeniu, jak model czy generalize nowych obiektów DataSet. [Krzyżowa Weryfikacja modelu] [ cross-validate-model] moduł przyjmuje nieprzeszkolonych modelu i niektóre etykietą zestawu danych i zapisuje wyniki oceny każdego 10 złożeń, oprócz uśrednionej wyników.

W poniższych sekcjach, firma Microsoft będzie utworzyć prosty modele regresji i klasyfikacji i oceny ich wydajności, za pomocą obu [Evaluate Model] [ evaluate-model] i [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułów.

## <a name="evaluating-a-regression-model"></a>Ocena modelu regresji
Załóżmy, że chcemy przewidzieć cenę samochodu korzystanie z niektórych funkcji, takich jak wymiary, moc, aparat specyfikacji i tak dalej. Jest to problem typowe regresji, gdzie zmienna docelowa (*cen*) ciągłego wartość liczbową. Firma Microsoft może składać się modelu regresji liniowej proste, który podane wartości funkcji samochodu można przewidzieć cenę samochodu tego. Ten model regresji może służyć do tego samego zestawu danych, które firma Microsoft uczony na wynik. Gdy mamy prognozowane ceny dla wszystkich samochodów, firma Microsoft oceny wydajności modelu, analizując ile prognozy różni się od rzeczywistej ceny średnio. Na przykład używamy *Automobile price data (Raw) dataset* dostępne w **zapisane zestawów danych** sekcji w usłudze Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do swojego obszaru roboczego w usłudze Azure Machine Learning Studio:

* Cen samochodów, data (Raw)
* [Regresja liniowa][linear-regression]
* [Train Model][train-model]
* [Score Model][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku 1 i zestawu kolumn etykiety z [Train Model] [ train-model] moduł *cen*.

![Ocena modelu regresji](./media/evaluate-model-performance/1.png)

Rysunek 1. Ocena modelu regresji.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po zakończeniu eksperymentu, możesz kliknąć portem wyjściowym [Evaluate Model] [ evaluate-model] moduł i zaznacz *wizualizacja* Aby wyświetlić wyniki oceny. Jest dostępna dla modeli regresji metryki oceny: *oznacza błąd absolutny*, *głównego oznacza błąd absolutny*, *względny błąd absolutny*, *względny błąd kwadrat*i *współczynnika determinacji*.

Termin "błąd" w tym miejscu reprezentuje różnicę między wartością prognozowaną a wartością true. Wartość bezwzględna lub kwadrat tej różnicy zwykle są obliczane do przechwytywania całkowita wielkość błędu we wszystkich wystąpieniach, jako różnica między wartością przewidywane i wartość true, może być liczbą ujemną, w niektórych przypadkach. Błąd metryki pomiaru wydajności predykcyjnej modelu regresji pod względem odchylenie średniej jego prognoz z wartości true. Niższe wartości błąd oznacza, że model jest dokładniejszych prognoz zgłoszenia. Ogólny błąd metrykę 0 oznacza, że modelu dopasowany danych.

Współczynnik determinacji, który jest również nazywany R-kwadrat, jest również standardowy sposób pomiaru, jak również dopasowanie do danych w modelu. Mogą być interpretowane jako część odmiany wyjaśnione przy użyciu modelu. Wyższy udział jest lepszym rozwiązaniem w przypadku gdzie 1 oznacza, dokładne dopasowanie.

![Metryki oceny regresji liniowej](./media/evaluate-model-performance/2.png)

Rysunek 2. Metryki oceny regresji liniowej.

### <a name="using-cross-validation"></a>Za pomocą innej sprawdzania poprawności
Jak wspomniano wcześniej, można wykonać powtarzane szkolenia, ocenianie i oceny automatycznie za pomocą [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu. W takim przypadku wystarczy zestawu danych, model nieprzeszkolonych i [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu (zobacz rysunek poniżej). Uwaga: należy ustawić etykiety kolumn *cen* w [krzyżowa Weryfikacja modelu] [ cross-validate-model] właściwości modułu.

![Krzyżowe sprawdzanie modelu regresji](./media/evaluate-model-performance/3.png)

Rysunek 3. Weryfikowanie między modelu regresji.

Po zakończeniu eksperymentu, możesz sprawdzić wyniki oceny, kliknij port wyjściowy prawo [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu. Zapewni to szczegółowy widok metryki dla każdej iteracji (złożenia) i uśrednionej wyniki każdego z metryki (4 rysunek).

![Wyników krzyżowego sprawdzania poprawności modelu regresji](./media/evaluate-model-performance/4.png)

Rysunek 4. Wyników krzyżowego sprawdzania poprawności modelu regresji.

## <a name="evaluating-a-binary-classification-model"></a>Ocena modelu klasyfikacji binarnej
W przypadku klasyfikacji binarnej Zmienna docelowa ma tylko dwa możliwe wyniki, na przykład: {0, 1} lub {false, wartość true}, {ujemną, dodatnią}. Załóżmy, można skorzystać z zestawu danych dla dorosłych pracowników z niektórych demograficznych i zmienne zatrudnienia oraz prośba do prognozowania poziomu dochodu binarne zmiennej o wartości {"< = 50K", "> 50K"}. Innymi słowy ujemna klasy reprezentuje pracowników, którzy tworzą mniejsza lub równa 50K rocznie, a klasa dodatnią — wszystkich innych pracowników. Jak w scenariuszu regresji firma Microsoft będzie nauczenia modelu, wynik niektóre dane i ocena wyników. Główną różnicą jest wybór metryki, który oblicza uczenie maszynowe Azure i danych wyjściowych. Aby zilustrować scenariusz poziomu prognozowania przychodów, będziemy używać [dla dorosłych](http://archive.ics.uci.edu/ml/datasets/Adult) zestawu danych do tworzenia eksperymentu uczenia maszynowego Azure i oceny wydajności modelu Regresja logistyczna dwuklasowych, często używane klasyfikatora binarnego.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do swojego obszaru roboczego w usłudze Azure Machine Learning Studio:

* Dla dorosłych klasyfikacji binarnej dochodu spisu zestawu danych.
* [Regresja logistyczna Two-Class][two-class-logistic-regression]
* [Train Model][train-model]
* [Score Model][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku 5 i zestawu kolumn etykiety z [Train Model] [ train-model] moduł *dochodu*.

![Ocena modelu klasyfikacji binarnej](./media/evaluate-model-performance/5.png)

Rysunek 5. Ocena modelu klasyfikacji binarnej.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po zakończeniu eksperymentu, możesz kliknąć portem wyjściowym [Evaluate Model] [ evaluate-model] moduł i zaznacz *wizualizacja* Aby wyświetlić wyniki oceny (rysunek 7). Jest dostępna dla modeli klasyfikacji binarnej metryki oceny: *dokładność*, *dokładności*, *odwołania*, *F1 oceny*, i *AUC*. Ponadto moduł generuje macierzy pomyłek, wyświetlana jest liczba alarmów wartość true, fałszywych wyników negatywnych fałszywych alarmów i negatywów wartość true, a także *ROC*, *dokładności/wycofania*, i *Podnieś* krzywych.

Dokładność jest po prostu część poprawnie klasyfikowane wystąpień. Zazwyczaj jest pierwszym metrykę, które można przyjrzeć się podczas obliczania klasyfikatora. Jednakże, gdy dane testowe jest niezrównoważone (gdzie większość wystąpień należą do jednej z klas) lub więcej planuje w czasie wykonywania na jedną z klas, dokładność naprawdę nie przechwytuje skuteczności klasyfikatora. W tym scenariuszu poziomu klasyfikacji przychodów przyjęto założenie, że podczas testowania niektórych danych, w którym 99% wystąpień reprezentują osoby, tym mniejsza lub równa 50 K rocznie. Istnieje możliwość uzyskania 0.99 dokładność przez klasę "< = 50K" dla wszystkich wystąpień. Klasyfikator w takim przypadku wydaje się być wysoki poziom ogólnej, ale w rzeczywistości nie jest on klasyfikowania tych osób high-income (1%) poprawnie.

Z tego powodu warto obliczeniowe dodatkowe metryki przechwytywania dokładniej aspektów oceny. Przed przejściem do szczegółów tych metryk, ważne jest zrozumienie macierzy pomyłek oceny klasyfikacji binarnej. Klasy etykiet w zestawie szkolenia może zająć tylko 2 możliwych wartości, które są zazwyczaj zwane jako dodatnią lub ujemną. Dodatnie i ujemne wystąpienia, które klasyfikatora prognozuje poprawnie są nazywane odpowiednio alarmów true (TP) i true negatywów (TN). Podobnie nazywane są niepoprawnie niejawnych wystąpień fałszywych alarmów (FP) i fałszywych wyników negatywnych (FN). Macierz pomyłek jest po prostu tabelę przedstawiającą liczba wystąpień, które są objęte każdej z tych kategorii 4. Usługa Azure Machine Learning automatycznie decyduje, które dwie klasy w zestawie danych jest dodatnią klasy. W przypadku typu Boolean lub liczb całkowitych etykiety klasy wystąpień etykietą "prawda" lub "1" są przypisywane dodatnią klasy. Jeśli etykiety są ciągami, tak jak w przypadku zestawu danych przychodów, etykiety są posortowane alfabetycznie i pierwszy poziom jest wybierany jest ujemny klasy, podczas gdy drugi poziom jest dodatnią klasy.

![Macierz pomyłek klasyfikacji binarnej](./media/evaluate-model-performance/6a.png)

Rysunek 6. Macierz pomyłek klasyfikacji binarnej.

Po powrocie do dochodu problemu klasyfikacji, czy chcemy z pytaniem, czy kilka oceny pytania, które pomagają nam zrozumieć wydajności klasyfikatora używane. Jest bardzo fizycznych zapytania: ' poza osób, którym modelu przewidzieć z uzyskaniem można > 50 K (TP + FP), ile zostały poprawnie klasyfikowane (TP)? " To pytanie można udzielić odpowiedzi, analizując **dokładności** modelu, który jest część alarmów, które są poprawnie klasyfikowane: TP/(TP+FP). Jest inny, często zadawane pytania "poza wszystkich wysokiej zdobywanie pracownikom dochodu > 50 k (TP + FN), ile Klasyfikator klasyfikowania poprawnie (TP)". To w rzeczywistości **odwołania**, lub wartość true, szybkość dodatnią: TP/(TP+FN) klasyfikatora. Może się okazać, czy znajduje się oczywiste kompromis między precision i odwołania. Na przykład podana stosunkowo zrównoważonym zestawu danych, klasyfikatora, który prognozuje przede wszystkim dodatnią wystąpień byłyby wysokiej odwołania, ale raczej niski dokładności jako wiele wystąpień ujemna będzie można nieprawidłowo klasyfikowana powodujące dużą liczbę fałszywych alarmów. Aby zobaczyć, jak te dwie metryki w zależności od wykres, możesz kliknąć krzywą "Dokładności/ODWOŁAŃ" w danych wyjściowych strony wyników oceny (u góry po lewej części rysunek 7).

![Wyniki oceny klasyfikacji binarnej](./media/evaluate-model-performance/7.png)

Rysunek 7. Wyniki oceny klasyfikacji binarnej.

Inny powiązane metryki, która jest często używana jest **F1 oceny**, który przyjmuje zarówno precision i odwołania pod uwagę. Jest harmoniczną tych metryk 2 i jest obliczana tak: F1 = 2 (precyzja x odwołania) / (precyzja + odwołania). Wynik F1 jest dobrym sposobem podsumowanie oceny w jeden numer, ale zawsze jest dobrym rozwiązaniem, aby przyjrzeć się zarówno precision i odwołania ze sobą, aby lepiej zrozumieć, jak ma zachowywać się klasyfikatora.

Ponadto jedną sprawdzić stopa dodatnią wartość true, a szybkości dodatnią wartość false w **odbiornika operacyjnego cechy (ROC)** krzywej i odpowiadający mu **obszar w krzywej (AUC)** wartość. Bliższe krzywej lewym górnym rogu, tym lepsza wydajność klasyfikatora jest (który jest maksymalizacja stopa dodatnią wartość true, przy jednoczesnym zmniejszeniu stopa dodatnią wartość false). Krzywe zbliża się przekątnej wykresu, w wyniku klasyfikatory, które mają na celu tworzenia prognoz, które wkrótce odgadnięcie losowych.

### <a name="using-cross-validation"></a>Za pomocą innej sprawdzania poprawności
Jak pokazano w przykładzie regresji oferujemy krzyżowego sprawdzania poprawności wielokrotnie uczenia, oceny i automatycznie oceny różnych podzbiór danych. Podobnie, możemy użyć [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu, model nieprzeszkolonych Regresja logistyczna i zestawu danych. Kolumna etykiety musi być ustawiona na *dochodu* w [krzyżowa Weryfikacja modelu] [ cross-validate-model] właściwości modułu. Po wykonać eksperyment, a następnie klikając polecenie po prawej stronie output port [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu, widać, wartości metryki klasyfikacji binarnej dla każdego złożenia dodatkowo średnią i odchylenie standardowe wszystkich. 

![Cross-sprawdzanie poprawności modelu klasyfikacji binarnej](./media/evaluate-model-performance/8.png)

Rysunek 8. Weryfikowanie między Model klasyfikacji binarnej.

![Krzyżowe sprawdzanie poprawności wyniki klasyfikatora binarne](./media/evaluate-model-performance/9.png)

Rysunek 9. Krzyżowe sprawdzanie poprawności wyniki binarne klasyfikatora.

## <a name="evaluating-a-multiclass-classification-model"></a>Ocena modelu Wieloklasowej klasyfikacji
W tym eksperymencie używamy popularnych [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") zestawu danych zawierającego wystąpienia 3 różne rodzaje (klasy) roślin iris. Istnieją 4 wartości funkcji (sepal długości do szerokości i długości Motyw Płatek do szerokości) dla każdego wystąpienia. W poprzednich eksperymenty możemy uczenia i przetestowane modeli, korzystając z tego samego zestawów danych. W tym miejscu użyjemy [podziału danych] [ split] modułu do tworzenia 2 podzestawy danych, uczenia pierwszego i wynik i ocenę na sekundę. Zestaw danych Iris jest ogólnie dostępna w [UCI Machine Learning repozytorium](http://archive.ics.uci.edu/ml/index.html)i może zostać pobrany przy użyciu [i zaimportuj dane] [ import-data] modułu.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do swojego obszaru roboczego w usłudze Azure Machine Learning Studio:

* [Importowanie danych][import-data]
* [Multiklasa decyzji lasu][multiclass-decision-forest]
* [Podział danych][split]
* [Train Model][train-model]
* [Score Model][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku nr 10.

Ustaw etykietę indeks kolumny [Train Model] [ train-model] modułu do 5. Element dataset nie zawiera żadnego wiersza nagłówka, ale wiemy, że etykiety klasy znajdują się w kolumnie piątej.

Polecenie [Importuj dane] [ import-data] modułu i zestaw *źródła danych* właściwości *adres URL sieci Web za pośrednictwem protokołu HTTP*i *adresURL* do http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Ustaw ułamek wystąpień można użyć do trenowania w [podziału danych] [ split] modułu (0,7 na przykład).

![Ocena Wieloklasowej klasyfikatora](./media/evaluate-model-performance/10.png)

Rysunek 10. Ocena Wieloklasowej klasyfikatora

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Uruchom eksperyment, a następnie kliknij port wyjściowy [Evaluate Model][evaluate-model]. Wyniki oceny są prezentowane w postaci macierzy pomyłek, w tym przypadku. Porównanie przewidywane wystąpień dla wszystkich klas 3 rzeczywistych pokazuje macierzy.

![Wyniki oceny wieloklasowej klasyfikacji](./media/evaluate-model-performance/11.png)

Rysunek 11. Wyniki oceny wieloklasowej klasyfikacji.

### <a name="using-cross-validation"></a>Za pomocą innej sprawdzania poprawności
Jak wspomniano wcześniej, można wykonać powtarzane szkolenia, ocenianie i oceny automatycznie za pomocą [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu. Będzie potrzebny zestawu danych, model nieprzeszkolonych i [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu (zobacz rysunek poniżej). Ponownie należy ustawić kolumnie Etykieta [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu (indeks kolumny 5 w tym przypadku). Po wykonać eksperyment, a następnie klikając polecenie prawo output port [krzyżowa Weryfikacja modelu][cross-validate-model], możesz sprawdzić wartości metryki dla każdej złożenia, a także odchylenie średnią i standard. Metryki wyświetlane w tym miejscu są podobne do tych omówione w przypadku klasyfikacji binarnej. Jednak należy pamiętać, że w wieloklasowej klasyfikacji, obliczeniowych alarmów true/negatywów i fałszywych alarmów/wyników negatywnych polega na zliczania na podstawie według klasy, ponieważ nie ma żadnych klasy ogólnej dodatnią lub ujemną. Na przykład przy obliczaniu dokładności lub odwołania klasy "Iris setosa", zakłada się, to jest dodatnią klasy i wszystkie inne jako ujemne.

![Krzyżowe sprawdzanie modelu Wieloklasowej klasyfikacji](./media/evaluate-model-performance/12.png)

Rysunek 12. Weryfikowanie między modelu Wieloklasowej klasyfikacji.

![Wyników krzyżowego sprawdzania poprawności modelu Wieloklasowej klasyfikacji](./media/evaluate-model-performance/13.png)

Rysunek 13. Wyników krzyżowego sprawdzania poprawności modelu Wieloklasowej klasyfikacji.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

