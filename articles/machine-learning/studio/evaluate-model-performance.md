---
title: Ocena wydajności modelu
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak ocenić wydajność modelu w usłudze Azure Machine Learning Studio (klasyczny) i metryki dostępne dla tego zadania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3c041834b9ad191817cdf1380b0a75efc7639bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218156"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Jak ocenić wydajność modelu w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

W tym artykule pokazano, jak ocenić wydajność modelu w usłudze Azure Machine Learning Studio (klasyczny) i zawiera krótkie wyjaśnienie metryki dostępne dla tego zadania. Przedstawiono trzy typowe nadzorowane scenariusze uczenia się: 

* Regresji
* klasyfikacja binarna 
* klasyfikacja wieloklasowa



Ocena wydajności modelu jest jednym z podstawowych etapów procesu nauki o danych. Wskazuje, jak pomyślnie oceniania (prognoz) zestawu danych został przez przeszkolony model. 

Usługa Azure Machine Learning Studio (klasyczna) obsługuje ocenę modelu za pośrednictwem dwóch głównych modułów uczenia maszynowego: [Oceny modelu][evaluate-model] i modelu [weryfikacji krzyżowej.][cross-validate-model] Te moduły pozwalają zobaczyć, jak model działa pod względem liczby metryk, które są powszechnie używane w uczeniu maszynowym i statystykach.

## <a name="evaluation-vs-cross-validation"></a>Ocena a krzyżowa walidacja
Ocena i krzyżowe sprawdzanie poprawności to standardowe sposoby pomiaru wydajności modelu. Oba generują metryki oceny, które można sprawdzić lub porównać z metrykami innych modeli.

[Ocena modelu][evaluate-model] oczekuje, że zestaw danych oceniony jako dane wejściowe (lub dwa w przypadku, gdy chcesz porównać wydajność dwóch różnych modeli). W związku z tym należy trenować modelu przy użyciu [train model][train-model] modułu i dokonać prognoz na niektórych zestaw danych przy użyciu [wynik modelu][score-model] modułu, zanim będzie można ocenić wyniki. Ocena jest oparta na ocenione etykiety/prawdopodobieństwa wraz z true etykiety, z których wszystkie są dane wyjściowe przez [wynik modelu][score-model] modułu.

Alternatywnie można użyć sprawdzania poprawności krzyżowej, aby automatycznie wykonywać szereg operacji oceny wyników pociągu (10 razy) na różnych podzbiorach danych wejściowych. Dane wejściowe są podzielone na 10 części, gdzie jeden jest zarezerwowany do testowania, a drugi 9 do szkolenia. Ten proces jest powtarzany 10 razy, a metryki oceny są uśrednione. Pomaga to w określaniu, jak dobrze model będzie uogólniać do nowych zestawów danych. [Moduł Cross-Validate Model][cross-validate-model] przyjmuje w modelu nieprzeszkolonym i niektóre oznaczone zestaw danych i wyprowadza wyniki oceny każdego z 10 fałd, oprócz uśrednionych wyników.

W poniższych sekcjach będziemy tworzyć proste regresji i klasyfikacji modeli i ocenić ich wydajność, przy użyciu modelu [oceniania][evaluate-model] i [cross-validate modelu][cross-validate-model] modułów.

## <a name="evaluating-a-regression-model"></a>Ocena modelu regresji
Załóżmy, że chcemy przewidzieć cenę samochodu za pomocą funkcji, takich jak wymiary, moc, specyfikacja silnika i tak dalej. Jest to typowy problem regresji, gdzie zmienna docelowa (*cena*) jest ciągłą wartością liczbową. Możemy zamontować model regresji liniowej, który, biorąc pod uwagę wartości charakterystyczne danego samochodu, może przewidzieć cenę tego samochodu. Ten model regresji może służyć do oceny tego samego zestawu danych, na których trenowaliśmy. Po przewidywanych cenach samochodów możemy ocenić wydajność modelu, patrząc na to, jak bardzo prognozy odbiegają od rzeczywistych cen. Aby to zilustrować, używamy *zestawu danych dotyczących cen samochodów (Raw) dostępnego* w sekcji Zapisane zestawy **danych** w udziale Machine Learning Studio (klasyczny).

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego w usłudze Azure Machine Learning Studio (klasyczne):

* Dane o cenach samochodów (raw)
* [Regresja liniowa][linear-regression]
* [Train Model (Trenuj model)][train-model]
* [Klasyfikacja modelu][score-model]
* [Ocena modelu][evaluate-model]

Podłącz porty, jak pokazano poniżej na rysunku 1 i ustaw kolumnę Etykieta modułu [Model pociągu][train-model] na *cenę*.

![Ocena modelu regresji](./media/evaluate-model-performance/1.png)

Rysunek 1. Ocena modelu regresji.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po uruchomieniu eksperymentu, można kliknąć na port wyjściowy [modułu Oceniaj model][evaluate-model] i wybierz *wizualizuj,* aby wyświetlić wyniki oceny. Metryki oceny dostępne dla modeli regresji to: *Średni błąd bezwzględny,* *Średni błąd bezwzględny,* *Względny błąd bezwzględny,* *Względny kwadratowy błąd*i Współczynnik *oznaczania*.

Termin "błąd" w tym miejscu reprezentuje różnicę między przewidywaną wartością a wartością rzeczywistą. Wartość bezwzględna lub kwadrat tej różnicy jest zwykle obliczany w celu przechwycenia całkowitej wielkości błędu we wszystkich wystąpieniach, ponieważ różnica między przewidywaną i rzeczywistą wartością może być ujemna w niektórych przypadkach. Metryki błędów mierzą wydajność predykcyjną modelu regresji pod względem średniego odchylenia jego prognoz od wartości rzeczywistych. Niższe wartości błędów oznaczają, że model jest bardziej dokładne w dokonywaniu prognoz. Ogólna metryka błędu zero oznacza, że model idealnie pasuje do danych.

Współczynnik oznaczalności, który jest również znany jako R squared, jest również standardowym sposobem pomiaru, jak dobrze model pasuje do danych. Można ją interpretować jako proporcję zmienności objaśniona modelem. Wyższy odsetek jest lepszy w tym przypadku, gdzie 1 oznacza idealne dopasowanie.

![Metryki oceny regresji liniowej](./media/evaluate-model-performance/2.png)

Rysunek 2. Metryki oceny regresji liniowej.

### <a name="using-cross-validation"></a>Korzystanie z sprawdzania poprawności krzyżowej
Jak wspomniano wcześniej, można wykonywać powtarzające się szkolenia, oceniania i oceny automatycznie przy użyciu [cross-validate modelu][cross-validate-model] modułu. Wszystko, czego potrzebujesz w tym przypadku, to zestaw danych, nieprzeszkolony model i moduł [modelu krzyżowego sprawdzania poprawności][cross-validate-model] (patrz rysunek poniżej). Należy ustawić kolumnę etykiety na *cenę* we właściwościach modułu [Cross-Validate Model.][cross-validate-model]

![Krzyżowe sprawdzanie poprawności modelu regresji](./media/evaluate-model-performance/3.png)

Rysunek 3. Krzyżowe sprawdzanie poprawności modelu regresji.

Po uruchomieniu eksperymentu można sprawdzić wyniki oceny, klikając na prawym porcie wyjściowym [modułu Model krzyżowy sprawdzania poprawności.][cross-validate-model] Zapewni to szczegółowy widok metryk dla każdej iteracji (krotnie) i uśrednionych wyników każdej z metryk (Rysunek 4).

![Wyniki krzyżowego sprawdzania poprawności modelu regresji](./media/evaluate-model-performance/4.png)

Rysunek 4. Wyniki krzyżowego sprawdzania poprawności modelu regresji.

## <a name="evaluating-a-binary-classification-model"></a>Ocena modelu klasyfikacji binarnej
W scenariuszu klasyfikacji binarnej zmienna docelowa ma tylko dwa możliwe wyniki, na przykład: {0, 1} lub {false, true}, {negative, positive}. Załóżmy, że otrzymujesz zestaw danych dorosłych pracowników z pewnymi zmiennymi demograficznymi i zmiennymi zatrudnienia i że zostaniesz poproszony o przewidzenie poziomu dochodów, zmiennej binarnej o wartościach {"<=50 K", ">50 K"}. Innymi słowy klasa ujemna reprezentuje pracowników, którzy zarabiają mniej niż lub równo 50 K rocznie, a klasa dodatnia reprezentuje wszystkich innych pracowników. Podobnie jak w scenariuszu regresji, będziemy szkolić model, ocena niektórych danych i oceny wyników. Główną różnicą jest tutaj wybór metryk Azure Machine Learning Studio (klasyczne) obliczeń i wyjść. Aby zilustrować scenariusz przewidywania poziomu dochodów, użyjemy zestawu danych [dla dorosłych](https://archive.ics.uci.edu/ml/datasets/Adult) do utworzenia eksperymentu studio (klasycznego) i oceny wydajności dwuklasowego modelu regresji logistycznej, powszechnie używanego klasyfikatora binarnego.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego w usłudze Azure Machine Learning Studio (klasyczne):

* Zestaw danych klasyfikacji binarnej dochodów dla dorosłych
* [Dwuklasowa regresja logistyczna][two-class-logistic-regression]
* [Train Model (Trenuj model)][train-model]
* [Klasyfikacja modelu][score-model]
* [Ocena modelu][evaluate-model]

Podłącz porty, jak pokazano poniżej na rysunku 5 i ustaw kolumnę Etykieta modułu [Model pociągu][train-model] na *dochód*.

![Ocena modelu klasyfikacji binarnej](./media/evaluate-model-performance/5.png)

Rysunek 5. Ocena modelu klasyfikacji binarnej.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po uruchomieniu eksperymentu można kliknąć port wyjściowy modułu [Oceń model][evaluate-model] i wybrać *opcję Wizualizuj,* aby wyświetlić wyniki oceny (Rysunek 7). Metryki oceny dostępne dla modeli klasyfikacji binarnej to: *Dokładność,* *Precyzja,* *Przywołanie,* *Wynik F1*i *AUC*. Ponadto moduł wyprowadza macierz pomyłek pokazującą liczbę prawdziwych alarmów, fałszywych negatywów, fałszywych alarmów i prawdziwych negatywów, a także krzywe *ROC*, *Precision/Recall*i *Lift.*

Dokładność jest po prostu proporcją poprawnie sklasyfikowanych wystąpień. Zazwyczaj jest to pierwsza metryka, na którą patrzysz podczas oceny klasyfikatora. Jednak gdy dane testowe są niezrównoważone (gdzie większość wystąpień należy do jednej z klas) lub jesteś bardziej zainteresowany wydajnością jednej z klas, dokładność tak naprawdę nie przechwytuje skuteczności klasyfikatora. W scenariuszu klasyfikacji poziomu dochodów załóżmy, że testujesz niektóre dane, w których 99% wystąpień reprezentuje osoby, które zarabiają mniej niż lub równe 50K rocznie. Jest możliwe, aby osiągnąć dokładność 0,99, przewidując klasę "<= 50K" dla wszystkich wystąpień. Klasyfikator w tym przypadku wydaje się robić dobrą robotę ogólnie, ale w rzeczywistości, nie klasyfikuje żadnej z osób o wysokich dochodach (1%) Poprawnie.

Z tego powodu warto obliczyć dodatkowe metryki, które przechwytują bardziej szczegółowe aspekty oceny. Przed przejściem do szczegółów takich metryk, ważne jest, aby zrozumieć macierz zamieszania oceny klasyfikacji binarnej. Etykiety klas w zestawie treningowym mogą przyjmować tylko dwie możliwe wartości, które zwykle nazywamy dodatnimi lub negatywnymi. Dodatnie i ujemne wystąpienia, które klasyfikator przewiduje poprawnie są nazywane true positives (TP) i true negatywy (TN), odpowiednio. Podobnie niepoprawnie sklasyfikowane wystąpienia są nazywane fałszywymi alarmami (FP) i fałszywymi negatywami (FN). Macierz zamieszania jest po prostu tabelą pokazującą liczbę wystąpień, które należą do każdej z tych czterech kategorii. Usługa Azure Machine Learning Studio (classic) automatycznie decyduje, która z dwóch klas w zestawie danych jest klasą dodatnią. Jeśli etykiety klas są boolean lub liczby całkowite, a następnie "true" lub "1" oznaczone wystąpienia są przypisane do klasy dodatniej. Jeśli etykiety są ciągami znaków, na przykład z zestawem danych o dochodach, etykiety są sortowane alfabetycznie, a pierwszy poziom jest wybierany jako klasa ujemna, podczas gdy drugi poziom jest klasą dodatnią.

![Macierz pomyłek klasyfikacji binarnej](./media/evaluate-model-performance/6a.png)

Rysunek 6. Macierz pomyłek klasyfikacji binarnej.

Wracając do problemu klasyfikacji dochodów, chcielibyśmy zadać kilka pytań ewaluacyjnych, które pomogą nam zrozumieć wydajność zastosowanego klasyfikatora. Naturalne pytanie brzmi: "Z osób, które model przewidywał zarabiać >50 K (TP + FP), ile zostało poprawnie sklasyfikowanych (TP)?" Na to pytanie można odpowiedzieć, patrząc na **dokładność** modelu, który jest proporcją pozytywów, które są poprawnie klasyfikowane: TP/(TP+FP). Innym częstym pytaniem jest "Ze wszystkich wysoko zarabiających pracowników o dochodach >50k (TP + FN), ilu klasyfikator poprawnie sklasyfikować (TP)". Jest to rzeczywiście **Przypomnieć**, lub true dodatnia stopa: TP/(TP + FN) klasyfikatora. Można zauważyć, że istnieje oczywisty kompromis między precyzją a wycofaniem. Na przykład biorąc pod uwagę stosunkowo zrównoważony zestaw danych, klasyfikator, który przewiduje głównie dodatnie wystąpienia, będzie miał wysokie odwołanie, ale raczej niską precyzję, ponieważ wiele wystąpień negatywnych zostanie błędnie sklasyfikowanych, co spowoduje dużą liczbę fałszywych alarmów. Aby wyświetlić wykres, jak te dwa metryki różnią się, można kliknąć na krzywej **PRECYZJA/PRZYWOŁÓW** NA stronie wyjściowej wynik oceny (w lewym górnym rogu rysunek 7).

![Wyniki oceny klasyfikacji binarnej](./media/evaluate-model-performance/7.png)

Rysunek 7. Wyniki oceny klasyfikacji binarnej.

Innym powiązanym metryką, która jest często używana, jest **Wynik F1**, który uwzględnia zarówno precyzję, jak i odwołanie. Jest to średnia harmoniczna tych dwóch metryk i jest obliczany jako taki: F1 = 2 (precyzja x przypomnieć) / (precyzja + wycofanie). Wynik F1 jest dobrym sposobem na podsumowanie oceny w jednej liczbie, ale zawsze jest dobrą praktyką, aby spojrzeć zarówno na precyzję, jak i przypomnieć razem, aby lepiej zrozumieć, jak zachowuje się klasyfikator.

Ponadto można sprawdzić współczynnik prawdziwego dodatniego w porównaniu z współczynnikiem fałszywie dodatnim w krzywej **charakterystyki operacyjnej odbiornika (ROC)** i odpowiadającej jej wartości **area under the curve (AUC).** Im bliżej tej krzywej jest w lewym górnym rogu, tym lepsza jest wydajność klasyfikatora (czyli maksymalizacja wskaźnika prawdziwego dodatniego przy jednoczesnym zminimalizowaniu fałszywie dodatniego wskaźnika). Krzywe, które są zbliżone do przekątnej wykresu, wynikają z klasyfikatorów, które mają tendencję do przewidywania, które są zbliżone do losowego zgadywania.

### <a name="using-cross-validation"></a>Korzystanie z sprawdzania poprawności krzyżowej
Podobnie jak w przykładzie regresji, możemy wykonać krzyżowe sprawdzanie poprawności wielokrotnie trenować, oceniać i oceniać różne podzbiory danych automatycznie. Podobnie możemy użyć modułu [Cross-Validate Model,][cross-validate-model] nieprzeszkolonego modelu regresji logistycznej i zestawu danych. Kolumna etykieta musi być ustawiona na *dochód* we właściwościach modułu [Cross-Validate Model.][cross-validate-model] Po uruchomieniu eksperymentu i kliknięciu odpowiedniego portu wyjściowego modułu [Cross-Validate Model,][cross-validate-model] możemy zobaczyć wartości metryki klasyfikacji binarnej dla każdego krotnie, oprócz średniego i standardowego odchylenia każdego. 

![Krzyżowe sprawdzanie poprawności modelu klasyfikacji binarnej](./media/evaluate-model-performance/8.png)

Rysunek 8. Krzyżowe sprawdzanie poprawności modelu klasyfikacji binarnej.

![Wyniki krzyżowej weryfikacji klasyfikatora binarnego](./media/evaluate-model-performance/9.png)

Wykres 9. Wyniki krzyżowej weryfikacji klasyfikatora binarnego.

## <a name="evaluating-a-multiclass-classification-model"></a>Ocena wieloklasowego modelu klasyfikacji
W tym eksperymencie użyjemy popularnego zestawu danych [Iris,](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") który zawiera wystąpienia trzech różnych typów (klas) rośliny tęczówki. Istnieją cztery wartości operacji (długość/szerokość działki i długość/szerokość płatka) dla każdego wystąpienia. W poprzednich eksperymentach przeszkoliliśmy i przetestowaliśmy modele przy użyciu tych samych zestawów danych. W tym miejscu użyjemy modułu [Podziel dane,][split] aby utworzyć dwa podzbiory danych, trenować na pierwszym i oceniać i oceniać na drugim. Zestaw danych Iris jest publicznie dostępny w [repozytorium uczenia maszynowego UCI](https://archive.ics.uci.edu/ml/index.html)i można go pobrać za pomocą [modułu Importuj dane.][import-data]

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego w usłudze Azure Machine Learning Studio (klasyczne):

* [Importowanie danych][import-data]
* [Wieloklasowy las decyzyjny][multiclass-decision-forest]
* [Spil Data (Podziel dane)][split]
* [Train Model (Trenuj model)][train-model]
* [Klasyfikacja modelu][score-model]
* [Ocena modelu][evaluate-model]

Podłącz porty, jak pokazano poniżej na rysunku 10.

Ustaw indeks kolumny Label modułu [Model pociągu][train-model] na 5. Zestaw danych nie ma wiersza nagłówka, ale wiemy, że etykiety klas znajdują się w piątej kolumnie.

Kliknij moduł [Importuj dane][import-data] i ustaw właściwość *Źródło danych* na adres URL sieci Web za pośrednictwem *protokołu HTTP,* a *adres URL* na http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Ustaw ułamek wystąpień, które mają być używane do szkolenia w module [Podziel dane][split] (na przykład 0,7).

![Ocena klasyfikatora wieloklasowego](./media/evaluate-model-performance/10.png)

Wykres 10. Ocena klasyfikatora wieloklasowego

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Uruchom eksperyment i kliknij port wyjściowy [Oceń model][evaluate-model]. Wyniki oceny są przedstawione w formie matrycy pomyłek, w tym przypadku. Macierz pokazuje rzeczywiste vs przewidywane wystąpienia dla wszystkich trzech klas.

![Wyniki oceny klasyfikacji wieloklasowej](./media/evaluate-model-performance/11.png)

Wykres 11. Wyniki oceny klasyfikacji wieloklasowej.

### <a name="using-cross-validation"></a>Korzystanie z sprawdzania poprawności krzyżowej
Jak wspomniano wcześniej, można wykonywać powtarzające się szkolenia, oceniania i oceny automatycznie przy użyciu [cross-validate modelu][cross-validate-model] modułu. Potrzebny jest zestaw danych, nieprzeszkolony model i moduł [modelu krzyżowego sprawdzania poprawności][cross-validate-model] (patrz rysunek poniżej). Ponownie należy ustawić kolumnę etykiety [modułu Cross-Validate Model][cross-validate-model] (indeks kolumny 5 w tym przypadku). Po uruchomieniu eksperymentu i kliknięciu odpowiedniego portu wyjściowego [modelu krzyżowego sprawdzania poprawności][cross-validate-model]można sprawdzić wartości metryki dla każdego zagięcia, a także średnie i standardowe odchylenie. Metryki wyświetlane w tym miejscu są podobne do tych omówionych w przypadku klasyfikacji binarnej. Jednak w klasyfikacji wieloklasowej obliczanie prawdziwych pozytywów/negatywów i fałszywych alarmów/negatywów odbywa się przez liczenie na podstawie dla klasy, ponieważ nie ma ogólnej klasy dodatniej lub ujemnej. Na przykład podczas obliczania precyzji lub odwołania klasy "Iris-setosa", zakłada się, że jest to klasa dodatnia, a wszystkie inne jako negatywne.

![Krzyżowe sprawdzanie poprawności modelu klasyfikacji wieloklasowej](./media/evaluate-model-performance/12.png)

Wykres 12. Krzyżowe sprawdzanie poprawności modelu klasyfikacji wieloklasowej.

![Wyniki klasyfikacji krzyżowej modelu klasyfikacji wieloklasowej](./media/evaluate-model-performance/13.png)

Wykres 13. Wyniki krzyżowej weryfikacji modelu klasyfikacji wieloklasowej.

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
