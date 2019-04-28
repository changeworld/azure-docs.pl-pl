---
title: Ocena wydajności modelu
titleSuffix: Azure Machine Learning Studio
description: Ten artykuł pokazuje, jak ocena wydajności modelu w usłudze Azure Machine Learning Studio i zawiera krótki opis dostępnych metryk dla tego zadania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 37ab56c377bc53a7300b51ffc709ea8d1b9d6f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750661"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio"></a>Ocenianie wydajności modelu w usłudze Azure Machine Learning Studio

Ten artykuł pokazuje, jak ocena wydajności modelu w usłudze Azure Machine Learning Studio i zawiera krótki opis dostępnych metryk dla tego zadania. Przedstawiono trzy typowe scenariusze uczenia nadzorowanego: 

* Regresji
* Klasyfikacja binarna 
* Wieloklasowej klasyfikacji



Ocena wydajności modelu jest jednym z podstawowych etapów w procesie nauki o danych. Oznacza to, jak pomyślnie oceniania (prognozy) zestaw został przez uczonego modelu. 

Usługa Azure Machine Learning Studio obsługuje oceny modelu dwie jego główne maszyny uczenia modułów: [Ocena modelu] [ evaluate-model] i [krzyżowa Weryfikacja modelu][cross-validate-model]. Zezwalaj na te moduły, można zobaczyć, jak model działa pod względem liczby metryk, które są często używane podczas uczenia maszynowego i statystyki.

## <a name="evaluation-vs-cross-validation"></a>Ocena programu vs. Krzyżowe sprawdzanie poprawności
Ocena i krzyżowego sprawdzania poprawności są standardowe metody zadaniem jest mierzenie wydajności modelu. Zarówno generują metryki oceny, które można sprawdzić lub porównać te z innymi modelami.

[Ocena modelu] [ evaluate-model] oczekuje ocenami zestawu danych jako dane wejściowe (lub 2 w przypadku chcesz porównać wydajność dwa różne modele). Oznacza to konieczność uczenie modelu przy użyciu [uczenie modelu] [ train-model] marki i moduł prognozy na niektórych zestawu danych za pomocą [Score Model] [ score-model]modułu, zanim będziesz w stanie ocenić wyniki. Obliczanie opiera się na ocenami etykiety/prawdopodobieństwa wraz z etykiety wartość true, które są generowane przez [Score Model] [ score-model] modułu.

Alternatywnie służy krzyżowego sprawdzania poprawności do wykonania wielu operacji obliczyć ocenę train (10 złożeń) automatycznie na różne podzbiory danych wejściowych. Dane wejściowe jest dzielony na 10 części, w której jedna jest zarezerwowana do testowania, a inne 9 na potrzeby szkolenia. Ten proces jest powtarzany 10 razy, a metryki oceny są uśredniane. Pomaga to w określeniu, jak dobrze modelu będzie generalize nowych zestawów danych. [Krzyżowa Weryfikacja modelu] [ cross-validate-model] moduł przyjmuje nieprzeszkolonych modelu i niektóre etykietami zestaw danych i wyniki oceny każdego 10 złożeń, oprócz uśrednionej wyników.

W poniższych sekcjach opracowanie proste modele regresji i klasyfikację i oceniać ich wydajności, przy użyciu zarówno [Evaluate Model] [ evaluate-model] i [krzyżowa Weryfikacja modelu ] [ cross-validate-model] modułów.

## <a name="evaluating-a-regression-model"></a>Ocenianie modelu regresji
Załóżmy, że chcemy przewidzieć cenę samochodu, korzystanie z niektórych funkcji, takich jak wymiarów, moc, aparat dane techniczne i tak dalej. Jest to problem regresji typowe, gdzie zmienna docelowa (*cena*) jest wartością liczbową ciągłe. Firma Microsoft może składać się modelu prostego regresji liniowej, który w podanej wartości funkcji samochodu można przewidzieć cenę samochodu, że. Ten model regresji, może służyć do wyniku tego samego zestawu danych, które szkoliliśmy na. Gdy będziemy już mieć dostęp do przewidywanych ceny dla wszystkich samochodów, firma Microsoft oceny wydajności modelu, patrząc na ile prognozy są tym różni się od rzeczywiste ceny średniej. Na przykład używamy *zestawu danych data (Raw) cen samochodów* dostępne w **zapisane zestawów danych** sekcji w usłudze Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego usługi Azure Machine Learning Studio:

* Cen samochodów, data (Raw)
* [Regresja liniowa][linear-regression]
* [Train Model (Trenuj model)][train-model]
* [Score Model][score-model]
* [Ocena modelu][evaluate-model]

Połączyć porty, jak pokazano poniżej na rysunku 1 i ustaw kolumny etykiety [Train Model] [ train-model] moduł *cena*.

![Ocenianie modelu regresji](./media/evaluate-model-performance/1.png)

Rysunek 1. Oceniania modelu regresji.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po uruchomieniu eksperymentu, kliknij port wyjściowy [Evaluate Model] [ evaluate-model] modułu, a następnie wybierz pozycję *Visualize* aby zobaczyć wyniki oceny. Są dostępne dla modele regresji metryk oceny: *Średni bezwzględny błąd*, *Root Mean błąd absolutny*, *względny błąd absolutny*, *względem kwadrat błąd*i *współczynnik Oznaczanie*.

Termin "error" w tym miejscu reprezentuje różnicy między wartością prognozowaną a wartością true. Wartość bezwzględna lub kwadrat różnica ta zazwyczaj obliczany jest do przechwytywania całkowita wielkość błąd we wszystkich wystąpieniach jako różnicy między wartością przewidywane i true może być ujemna w niektórych przypadkach. Metryki błąd zadaniem jest mierzenie wydajności predykcyjnego modelu regresji pod względem odchylenie oznacza jego przewidywań wartości true. Niższe wartości błąd oznacza, że model, który jest bardziej precyzyjne w podejmowaniu prognozy. Metrykę ogólny błąd zero oznacza, że model doskonałe dopasowanie danych.

Współczynnik oznaczania, która jest również znana jako R kwadrat, jest również standardowy sposób pomiaru, jak dobrze pasuje do modelu danych. Mogą być interpretowane jako część odmiany wyjaśnione przy użyciu modelu. Uzyskanie lepszej udział w tym przypadku jest lepiej gdzie 1 oznacza dokładne dopasowanie.

![Regresja liniowa metryk oceny](./media/evaluate-model-performance/2.png)

Rysunek 2. Metryki oceny regresji liniowej.

### <a name="using-cross-validation"></a>Za pomocą krzyżowego sprawdzania poprawności
Jak wspomniano wcześniej, można przeprowadzić szkolenia powtarzane, oceniania i ocen automatycznie przy użyciu [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu. Wszystko, czego potrzebujesz w takiej sytuacji jest zestaw nieprzeszkolonych modelu i [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu (zobacz rysunek poniżej). Należy ustawić kolumny etykiety *cena* w [krzyżowa Weryfikacja modelu] [ cross-validate-model] właściwości modułu.

![Krzyżowe sprawdzanie modelu regresji](./media/evaluate-model-performance/3.png)

Rysunek 3. Cross-sprawdzania poprawności modelu regresji.

Po uruchomieniu eksperymentu, można sprawdzić wyniki oceny, kliknij port wyjściowy prawo [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu. Zapewni to szczegółowy wgląd w metryki dla każdej iteracji (zwijania) i uśrednionej wyniki każdego z metryk (rysunek 4).

![Wyników krzyżowego sprawdzania poprawności modelu regresji](./media/evaluate-model-performance/4.png)

Rysunek 4. Wyników krzyżowego sprawdzania poprawności modelu regresji.

## <a name="evaluating-a-binary-classification-model"></a>Ocena Model klasyfikacji binarnej
W przypadku klasyfikacji binarnej Zmienna docelowa ma tylko dwa możliwe wyniki, na przykład: {0, 1} lub {FAŁSZ, PRAWDA}, {dodatnie, ujemne}. Przyjęto założenie, otrzymują zestaw treści dla dorosłych pracowników z niektórymi demograficznych i zatrudnienia zmienne i zostanie wyświetlony monit do prognozowania poziomu dochodu binarne zmiennej o wartości {"< = 50 K", "> 50 K"}. Innymi słowy ujemna klasa reprezentuje pracowników, którzy tworzą mniejsza lub równa 50 K rocznie i dodatnią klasa reprezentuje innym pracownikom. Tak jak w scenariuszu regresji firma Microsoft będzie wytrenuj model, oceniać niektóre dane i ocena wyników. Główną różnicą jest wybór metryk, które oblicza Azure Machine Learning Studio i danych wyjściowych. Aby zilustrować scenariusza prognozowania poziomu przychodów, użyjemy [treści dla dorosłych](https://archive.ics.uci.edu/ml/datasets/Adult) zestawu danych, aby utworzyć eksperyment Studio i ocena wydajności modelu regresji logistycznej dwuklasowych, powszechnie używane Klasyfikator binarny.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego usługi Azure Machine Learning Studio:

* Treści dla dorosłych klasyfikacji binarnej dochodu spisu zestawu danych.
* [Regresja logistyczna Two-Class][two-class-logistic-regression]
* [Train Model (Trenuj model)][train-model]
* [Score Model][score-model]
* [Ocena modelu][evaluate-model]

Połączyć porty, jak pokazano poniżej na rysunku 5 i ustaw kolumny etykiety [Train Model] [ train-model] moduł *dochodu*.

![Ocena Model klasyfikacji binarnej](./media/evaluate-model-performance/5.png)

Rysunek 5. Ocena Model klasyfikacji binarnej.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po uruchomieniu eksperymentu, kliknij port wyjściowy [Evaluate Model] [ evaluate-model] modułu, a następnie wybierz pozycję *Visualize* aby zobaczyć wyniki oceny (rysunek 7). Są dostępne dla modeli klasyfikacji binarnej metryki oceny: *Dokładność*, *dokładności*, *Odwołaj*, *wynik F1*, i *AUC*. Ponadto moduł generuje macierz pomyłek, w którym wyświetlana jest liczba prawdziwie dodatnie, fałszywych wyników negatywnych, liczbę wyników fałszywie dodatnich i negatywów wartość true, a także *ROC*, *dokładności/odwołania*, i  *Przenoszenie* krzywych.

Dokładność jest po prostu część wystąpień poprawnie sklasyfikowane. Zazwyczaj jest pierwszym metrykę, które przyjrzymy się podczas oceny klasyfikatora. Jednakże, gdy dane testowe jest niezrównoważone (gdzie większość wystąpień należą do jednej z klas) lub interesuje Cię bardziej w czasie wykonywania w jednej z klas, dokładność naprawdę nie odzwierciedla skuteczność klasyfikatora. W scenariuszu poziomu Klasyfikacja dochodu przyjęto założenie, że testujesz niektórych danych, gdzie 99% wystąpień reprezentują osób, które kwota zobowiązania, tym mniejsza lub równa 50 K rocznie. Jest możliwe uzyskanie 0.99 dokładności, przewidując klasy "< = 50K" dla wszystkich wystąpień. Klasyfikator w tym przypadku wydaje się być wysoki poziom ogólną, ale w rzeczywistości nie jest on klasyfikowania dowolnego osobom high-income (1%) poprawnie.

Z tego powodu warto obliczenia dodatkowe metryki, które przechwycić bardziej konkretnych aspektów oceny. Przed przejściem do szczegółów tych metryk, ważne jest zrozumienie macierz pomyłek oceny klasyfikacji binarnej. Etykiety klasy w zestawie szkolenia można wykonać na tylko 2 możliwych wartości, które zazwyczaj nazywamy, jak dodatnie lub ujemne. Dodatnie i ujemne wystąpień, które klasyfikatora przewiduje poprawnie są nazywane odpowiednio prawdziwie dodatnie (TP) i PRAWDA negatywów (TN). Podobnie niepoprawnie niejawnych wystąpienia są nazywane wyników fałszywie dodatnich (FP) i fałszywych wyników negatywnych (FN). Macierz pomyłek jest po prostu tabelę przedstawiającą liczbę wystąpień, które są objęte każdej z tych kategorii 4. Usługa Azure Machine Learning Studio automatycznie decyduje, dwóch klas w zestawie danych jest klasa dodatnią. W przypadku wartości logicznych lub liczby całkowite etykiety klasy wystąpienia etykietą "prawda" lub "1" są przypisywane dodatnią klasy. Jeśli etykiety są ciągami, tak jak w przypadku zestawu danych dochodu etykiety są sortowane alfabetycznie, a pierwszy poziom jest wybierany jako ujemny klasy, podczas gdy drugi poziom jest dodatnia klasy.

![Macierz pomyłek Klasyfikacja binarna](./media/evaluate-model-performance/6a.png)

Rysunek 6. Macierz pomyłek klasyfikacji binarnej.

Wracając do problemu klasyfikacji przychodów, firma Microsoft będzie chciała zadawać na kilka pytań oceny, które pomagają nam zrozumieć wydajność klasyfikatora używane. Bardzo naturalnych pytaniem jest: "Poza osób, których model przewiduje się być zarabiać > 50 K (TP + FP), jak wiele zostały poprawnie klasyfikowane (TP)?" To pytanie, można uzyskać, analizując **dokładności** modelu, który jest część alarmów, które są poprawnie klasyfikowane: TP/(TP+FP). Jest inny, często zadawane pytania "poza wszystkich wysokiej zarabiać pracownikom dochodu > 50 k (TP + FN), ile klasyfikatora klasyfikowania poprawnie (TP)". Jest to rzeczywiście **Odwołaj**, lub wartość true, wartości dodatnich: TP/(TP+FN) klasyfikatora. Może się okazać, czy jest oczywisty kompromisu między dokładności i odwołania. Na przykład biorąc pod uwagę zestaw stosunkowo o zrównoważonym obciążeniu, klasyfikatora, który prognozuje przede wszystkim dodatnią wystąpień miałby wysokiej odwołania, ale raczej niski dokładności tyle wystąpień ujemna może być źle zakwalifikowane skutkuje dużą liczbę wyników fałszywie dodatnich. Aby zobaczyć, jak te dwie metryki w zależności od wykres, możesz kliknąć **dokładności/odwołania** krzywą na stronie dane wyjściowe wyniku oceny (część lewym górnym rogu rysunek 7).

![Wyniki oceny Klasyfikacja binarna](./media/evaluate-model-performance/7.png)

Rysunek 7. Wyniki oceny klasyfikacji binarnej.

Innego powiązane metryki, która jest często używana jest **wynik F1**, który przyjmuje dokładności i wycofaniu pod uwagę. Jest harmoniczną tych metryk 2 i jest obliczana w związku z tym: F1 = 2 (wycofaniu dokładności x) / (dokładności + odwołania). Wynik F1 jest dobrym sposobem na podsumowanie oceny w liczbie pojedynczej, ale zawsze jest dobrym rozwiązaniem, Przyjrzyj się dokładności i wycofaniu ze sobą, aby lepiej zrozumieć sposób działania klasyfikatora.

Ponadto jeden sprawdzić wartość true, zawartość dodatnich a wyników fałszywie dodatnich w **odbiorcy operacyjnego cechy (ROC)** krzywej i odpowiedni **powierzchni pod krzywą (AUC)** wartość. Im bliżej krzywej na lewym górnym rogu, tym lepiej wydajności klasyfikatora (który jest maksymalizowanie true dodatnich przy jednoczesnym zmniejszeniu fałszywie dodatnich). Krzywe, które znajdują się blisko przekątnej wykres, wynikiem klasyfikatorów, które przeważnie do przewidywania przyszłych zdarzeń znajdujących się blisko zgadywania losowych.

### <a name="using-cross-validation"></a>Za pomocą krzyżowego sprawdzania poprawności
Tak jak w przykładzie regresji można wykonać krzyżowego sprawdzania poprawności wielokrotnie uczenie, ocenę i automatycznie obliczyć różne podzbiory danych. Podobnie, możemy użyć [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu, model regresji logistycznej nieprzeszkolonych i zestawu danych. Kolumna etykiety musi być równa *dochodu* w [krzyżowa Weryfikacja modelu] [ cross-validate-model] właściwości modułu. Po uruchomienie eksperymentu spowodowało i klikając przycisk po prawej stronie wyjściowe port [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu, widać wartości metryk klasyfikacji binarnej dla poszczególnych etapów dodatkowo do średniej i Odchylenie standardowe każdego z nich. 

![Krzyżowe sprawdzanie Model klasyfikacji binarnej](./media/evaluate-model-performance/8.png)

Rysunek 8. Weryfikowanie między Model klasyfikacji binarnej.

![Wyników krzyżowego sprawdzania poprawności Klasyfikator binarny](./media/evaluate-model-performance/9.png)

Rysunek 9. Wyników krzyżowego sprawdzania poprawności Klasyfikator binarny.

## <a name="evaluating-a-multiclass-classification-model"></a>Ocenianie modeli klasyfikacji Wieloklasowej
W tym eksperymencie użyjemy popularnej [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") zestawu danych zawierającego wystąpienia 3 różne rodzaje (grupy) roślin iris. Istnieją 4 wartości funkcji (słupka długość/szerokość i długość/szerokość płatka) dla każdego wystąpienia. W poprzednich doświadczeń możemy przeszkolonych i przetestowane modele przy użyciu tych samych zestawów danych. W tym miejscu użyjemy [podziału danych] [ split] modułu do tworzenia 2 podzbiorów danych, uczenie się na pierwszym i ocenianie i oceny w drugiej. Zestawu danych Iris jest publicznie dostępny w [UCI Machine Learning repozytorium](https://archive.ics.uci.edu/ml/index.html)i można je pobrać za pomocą [importu danych] [ import-data] modułu.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego usługi Azure Machine Learning Studio:

* [Importowanie danych][import-data]
* [Las decyzyjny kontra][multiclass-decision-forest]
* [Spil Data (Podziel dane)][split]
* [Train Model (Trenuj model)][train-model]
* [Score Model][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku nr 10.

Ustaw indeks kolumny etykiety [Train Model] [ train-model] modułu do 5. Zestaw danych zawiera wiersz nie nagłówka, ale wiemy, że etykiety klasy znajdują się w piąta kolumna.

Kliknij pozycję [importu danych] [ import-data] modułu i ustaw *źródła danych* właściwości *adres URL sieci Web za pośrednictwem protokołu HTTP*i *adresuURL* do http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Ustawianie wystąpień służący do szkolenia w [podziału danych] [ split] modułu (0,7, na przykład).

![Ocena Wieloklasowej klasyfikatora](./media/evaluate-model-performance/10.png)

Rysunek 10. Ocena Wieloklasowej klasyfikatora

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Uruchom eksperyment, kliknij port wyjściowy [Evaluate Model][evaluate-model]. Wyniki oceny są prezentowane w postaci macierz pomyłek, w tym przypadku. Rzeczywiste a przewidywane wystąpienia dla wszystkich klas 3 pokazuje macierzy.

![Wyniki oceny wieloklasowej klasyfikacji](./media/evaluate-model-performance/11.png)

Rysunek 11. Wyniki oceny klasyfikacji wieloklasowej.

### <a name="using-cross-validation"></a>Za pomocą krzyżowego sprawdzania poprawności
Jak wspomniano wcześniej, można przeprowadzić szkolenia powtarzane, oceniania i ocen automatycznie przy użyciu [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu. Będziesz potrzebować zestawu danych, model nieprzeszkolonych i [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu (zobacz rysunek poniżej). Ponownie należy ustawić kolumny etykiety [krzyżowa Weryfikacja modelu] [ cross-validate-model] modułu (indeks kolumny 5 w tym przypadku). Po uruchomienie eksperymentu spowodowało i klikając przycisk po prawej stronie wyjściowe port [krzyżowa Weryfikacja modelu][cross-validate-model], można sprawdzić wartości metryk dla poszczególnych etapów, jak również odchylenie mean i standard. Metryki wyświetlane w tym miejscu są podobne do tych omówionych w tym przypadku klasyfikacji binarnej. Jednak należy pamiętać, że wieloklasowej klasyfikacji, obliczeniowych true alarmów/negatywów i fałszywych alarmów/wyników negatywnych jest wykonywane przez zliczania na podstawie klasy, ponieważ brak klasy ogólnej dodatnia lub ujemna. Na przykład podczas obliczania precyzję lub wycofaniu klasy "Iris-setosa", zakłada się, to dodatnia klasy i wszystkie inne jako ujemny.

![Krzyżowe sprawdzanie Model klasyfikacji Wieloklasowej](./media/evaluate-model-performance/12.png)

Rysunek 12. Weryfikowanie wielu modeli klasyfikacji Wieloklasowej.

![Wyników krzyżowego sprawdzania poprawności modelu klasyfikacji Wieloklasowej](./media/evaluate-model-performance/13.png)

Rysunek 13. Wyników krzyżowego sprawdzania poprawności modelu klasyfikacji Wieloklasowej.

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

