---
title: Wprowadzenie do języka R
titleSuffix: ML Studio (classic) - Azure
description: Use this R programming tutorial to get started using the R language with Azure Machine Learning Studio (classic) to create a forecasting solution.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1b347707b3c656bd692a29f0fd748c1503be4fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218010"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Getting started with the R programming language in Azure Machine Learning Studio (classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Wprowadzenie

This tutorial helps you start extending Azure Machine Learning Studio (classic) by using the R programming language. Postępuj zgodnie z tym samouczka programowania Języka R, aby utworzyć, przetestować i wykonać kod R w Studio (classic). Podczas pracy z samouczka utworzysz kompletne rozwiązanie do prognozowania przy użyciu języka języka Języka R w Studio (klasyczny).  

Usługa Azure Machine Learning Studio (klasyczna) zawiera wiele zaawansowanych modułów uczenia maszynowego i manipulowania danymi. Potężny język R został opisany jako lingua franca analityki. Szczęśliwie, analizy i manipulowania danymi w Studio (classic) można rozszerzyć za pomocą języka R. Ta kombinacja zapewnia skalowalność i łatwość wdrażania studio (klasyczny) z elastycznością i dogłębnej analizy R.

### <a name="forecasting-and-the-dataset"></a>Prognozowanie i zestaw danych

Prognozowanie jest powszechnie stosowane i dość przydatna metoda analityczna. Typowe zastosowania obejmują zarówno przewidywanie sprzedaży produktów sezonowych, określanie optymalnych poziomów zapasów, jak i przewidywanie zmiennych makroekonomicznych. Prognozowanie odbywa się zazwyczaj za pomocą modeli szeregów czasowych.

Dane szeregów czasowych to dane, w których wartości mają indeks czasu. Wskaźnik czasu może być regularny, na przykład co miesiąc lub co minutę, lub nieregularny. Model szeregów czasowych jest oparty na danych szeregów czasowych. Język programowania Języka języka R zawiera elastyczną strukturę i obszerną analizę danych szeregów czasowych.

W tym przewodniku będziemy współpracować z kalifornijskimi danymi o produkcji mleka i cenach. Dane te obejmują miesięczne informacje na temat produkcji kilku produktów mlecznych oraz ceny tłuszczu mlecznego, towaru odniesienia.

Dane użyte w tym artykule, wraz ze skryptami R, można pobrać z [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Dane w `cadairydata.csv` pliku zostały pierwotnie zsyntetyzowane na podstawie [https://dairymarkets.com](https://dairymarkets.com)informacji dostępnych na Uniwersytecie Wisconsin pod adresem .

### <a name="organization"></a>Organizacja

Będziemy przechodzić przez kilka kroków, jak dowiedzieć się, jak tworzyć, testować i wykonywać analizy i manipulowania danymi kod R w środowisku Azure Machine Learning Studio (klasyczny).  

* First we will explore the basics of using the R language in the Azure Machine Learning Studio (classic) environment.
* Następnie przechodzimy do dyskusji na temat różnych aspektów we/wy dla danych, kodu języka R i grafiki w środowisku usługi Azure Machine Learning Studio (klasyczny).
* Następnie skonstruujemy pierwszą część naszego rozwiązania do prognozowania, tworząc kod do czyszczenia i przekształcania danych.
* Dzięki przygotowanym przez nas danym przeprowadzimy analizę korelacji między kilkoma zmiennymi w naszym zestawie danych.
* Na koniec stworzymy sezonowy model prognozowania szeregów czasowych dla produkcji mleka.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interakcja z językiem języka R w udiodzieczy maszynowych (klasyczny)

W tej sekcji znajdziesz kilka podstaw interakcji z językiem programowania Języka języka języka języka R w środowisku machine learning studio (klasycznym). Język języka Języka Języka R zapewnia zaawansowane narzędzie do tworzenia dostosowanych modułów analizy i manipulowania danymi w środowisku usługi Azure Machine Learning Studio (klasycznym).

Użyję RStudio do opracowania, przetestowania i debugowania kodu R na małą skalę. Ten kod jest następnie wycinane i wklejane do modułu [Wykonywanie skryptu języka R][execute-r-script] gotowego do uruchomienia w usłudze Azure Machine Learning Studio (klasyczny).  

### <a name="the-execute-r-script-module"></a>Moduł Wykonywanie skryptu R

W ramach machine learning studio (klasyczny), skrypty Języka R są uruchamiane w ramach [execute R Script][execute-r-script] module. Przykład modułu [Wykonywanie skryptu R][execute-r-script] w udiociepła Machine Learning (classic) jest pokazany na rysunku 1.

 ![Język programowania języka języka R: moduł Execute R Script wybrany w programie Machine Learning Studio (klasyczny)](./media/r-quickstart/fig1.png)

*Rysunek 1. Środowisko Machine Learning Studio (klasyczne) z wybranym modułem Wykonywanie skryptu R.*

Odnosząc się do rysunku 1, przyjrzyjmy się niektórym kluczowym częściom środowiska Machine Learning Studio (klasyczne) do pracy z modułem [Wykonywanie skryptu R.][execute-r-script]

* Moduły w eksperymencie są wyświetlane w środkowym okienku.
* Górna część prawego okienka zawiera okno do wyświetlania i edytowania skryptów języka R.  
* W dolnej części prawego okienka są wyświetlane niektóre właściwości [skryptu Wykonywanie języka R][execute-r-script]. Dzienniki błędów i danych wyjściowych można wyświetlić, wybierając odpowiednie miejsca tego okienka.

Będziemy, oczywiście, być omówienie [Wykonać skrypt R][execute-r-script] bardziej szczegółowo w pozostałej części tego artykułu.

Podczas pracy ze złożonymi funkcjami R, polecam edycji, testowania i debugowania w RStudio. Podobnie jak w przypadku każdego rozwoju oprogramowania, rozszerzaj kod przyrostowo i przetestuj go na małych prostych przypadkach testowych. Następnie wytnij i wklej swoje funkcje do okna skryptu R modułu [Wykonaj skrypt R.][execute-r-script] Takie podejście umożliwia wykorzystanie zarówno RStudio zintegrowane środowisko programistyczne (IDE) i możliwości usługi Azure Machine Learning Studio (klasyczny).  

#### <a name="execute-r-code"></a>Wykonanie kodu R

Każdy kod języka R w module [Wykonywanie skryptu R][execute-r-script] zostanie wykonany po uruchomieniu eksperymentu, wybierając przycisk **Uruchom.** Po zakończeniu wykonywania na ikonie [Wykonaj skrypt R][execute-r-script] pojawi się znacznik wyboru.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Kodowanie języka R defensywnego dla usługi Azure Machine Learning

Jeśli tworzysz kod języka R dla, powiedzmy, usługi sieci web przy użyciu usługi Azure Machine Learning Studio (classic), zdecydowanie należy zaplanować, jak kod poradzi sobie z nieoczekiwanych danych wprowadzania i wyjątków. Aby zachować przejrzystość, nie uwzględniłem wiele w sposobie sprawdzania lub obsługi wyjątków w większości pokazanych przykładów kodu. Jednak w miarę postępów podam kilka przykładów funkcji przy użyciu możliwości obsługi wyjątków języka R.  

Jeśli potrzebujesz bardziej kompletnego traktowania R obsługi wyjątków, polecam przeczytać odpowiednie sekcje książki Wickham wymienione poniżej w [dalszej lekturze](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Debugowanie i test R w studio machine learning (klasyczny)

Aby powtórzyć, polecam przetestować i debugować kod R na małą skalę w RStudio. Istnieją jednak przypadki, w których należy wyśledzić problemy z kodem Języka R w samym [skrypcie wykonywania języka R.][execute-r-script] Ponadto dobrą praktyką jest sprawdzenie wyników w udaniu maszynowym Studio (klasycznym).

Dane wyjściowe z wykonania kodu języka R i na platformie Azure Machine Learning Studio (klasycznej) znajduje się przede wszystkim w output.log. Niektóre dodatkowe informacje będą widoczne w error.log.  

Jeśli wystąpi błąd w uściślicie machine learning studio (klasyczny) podczas uruchamiania kodu języka R, pierwszym kursem działania powinno być spojrzenie na error.log. Ten plik może zawierać przydatne komunikaty o błędach, które pomogą Ci zrozumieć i poprawić błąd. Aby wyświetlić plik error.log, wybierz pozycję **Wyświetl dziennik błędów** w **okienku właściwości** [skryptu Wykonywanie języka R][execute-r-script] zawierającego ten błąd.

Na przykład uruchomiłem następujący kod języka R, z niezdefiniowanym zmienną y, w module [Wykonaj skrypt R:][execute-r-script]

```R
x <- 1.0
z <- x + y
```

Ten kod nie można wykonać, co powoduje warunek błędu. Wybranie **pola dziennika błędów widoku** w **okienku właściwości** powoduje wyświetlenie na rysunku 2.

  ![Popiął się komunikat o błędzie](./media/r-quickstart/fig2.png)

*Rysunek 2. Wyskakujące okienko komunikatu o błędzie.*

Wygląda na to, musimy szukać w output.log, aby zobaczyć komunikat o błędzie R. Wybierz [skrypt Wykonywanie R,][execute-r-script] a następnie wybierz element **Wyświetl plik output.log** w **okienku właściwości** po prawej stronie. Otworzy się nowe okno przeglądarki i widzę następujące.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ten komunikat o błędzie nie zawiera niespodzianek i wyraźnie identyfikuje problem.

Aby sprawdzić wartość dowolnego obiektu w R, można wydrukować te wartości do pliku output.log. Reguły badania wartości obiektów są zasadniczo takie same jak w interaktywnej sesji języka R. Na przykład po wpisaniu nazwy zmiennej w wierszu wartość obiektu zostanie wydrukowana w pliku output.log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakiety w udiod Machine Learning Studio (klasyczny)

Studio jest wyposażone w ponad 350 preinstalowanych pakietów językowych Języka R. Aby pobrać listę preinstalowanych pakietów, można użyć następującego kodu w module [Wykonywanie skryptu języka R.][execute-r-script]

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Jeśli nie rozumiesz w tej chwili ostatniego wiersza tego kodu, czytaj dalej. W dalszej części tego artykułu omówimy szeroko za pomocą języka R w środowisku Studio (klasycznym).

### <a name="introduction-to-rstudio"></a>Wprowadzenie do RStudio

RStudio jest powszechnie używanym IDE dla R. Użyję RStudio do edycji, testowania i debugowania niektórych kodu R używanego w tym przewodniku. Gdy kod języka R jest testowany i gotowy, można po prostu wyciąć i wkleić z edytora RStudio do machine learning studio (klasyczny) [Wykonać skrypt R][execute-r-script] modułu.  

Jeśli nie masz języka programowania R zainstalowany na komputerze stacjonarnym, polecam to zrobić teraz. Bezpłatne pobieranie języka R typu open source jest dostępne w kompleksowej sieci archiwum R (CRAN) pod adresem [https://www.r-project.org/](https://www.r-project.org/). Dostępne są pliki do pobrania dla systemów Windows, Mac OS i Linux/UNIX. Wybierz pobliskie lustro i postępuj zgodnie ze wskazówkami dotyczącymi pobierania. Ponadto CRAN zawiera wiele przydatnych pakietów analitycznych i manipulowania danymi.

Jeśli jesteś nowy w RStudio, należy pobrać i zainstalować wersję pulpitu. Pliki do pobrania rStudio dla systemów Windows, Mac OS http://www.rstudio.com/products/RStudio/i Linux/UNIX można znaleźć na poziomie . Postępuj zgodnie ze wskazówkami podanymi, aby zainstalować RStudio na komputerze stacjonarnym.  

Wprowadzenie samouczka do RStudio jest dostępne w [użyciu RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Podaję kilka dodatkowych informacji na temat korzystania z RStudio w [Przewodnik do dokumentacji RStudio](#appendixa) poniżej.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Wprowadzanie i wyjęcie danych z modułu Wykonywanie skryptu R

W tej sekcji omówimy sposób wprowadzania danych do i z modułu [Wykonywanie skryptu R.][execute-r-script] Przeanalizujemy sposób obsługi różnych typów danych odczytywanych do i z modułu [Wykonywanie skryptu R.][execute-r-script]

Pełny kod dla tej sekcji znajduje się w [machinelearningsamples-notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Ładowanie i sprawdzanie danych w umiań machine learningu (klasyczny)

#### <a name="load-the-dataset"></a><a id="loading"></a>Ładowanie zestawu danych

Rozpoczniemy od załadowania pliku **csdairydata.csv** do usługi Azure Machine Learning Studio (classic).

1. Uruchom środowisko usługi Azure Machine Learning Studio (klasyczne).
1. Wybierz **+ NOWOŚĆ** w lewym dolnym momencie ekranu i wybierz **pozycję Zestaw danych**.
1. Wybierz **opcję Z pliku lokalnego**, a następnie **przejdź** do wyboru pliku.
1. Upewnij się, że jako typ zestawu danych wybrano **ogólny plik CSV z nagłówkiem (csv).**
1. Zaznacz znacznik wyboru.
1. Po przekazaniu zestawu danych powinien zostać wyświetlony nowy zestaw danych, wybierając kartę **Zestawy danych.**  

#### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Teraz, gdy mamy pewne dane w Machine Learning Studio (klasyczny), musimy utworzyć eksperyment, aby wykonać analizę.  

1. Wybierz **+ NOWY** w lewym dolnym dolnym i wybierz opcję **Eksperyment**, a następnie pusty **eksperyment**.
1. Eksperyment można nazwać, wybierając i modyfikując **tytuł eksperymentu utworzonego na ...** w górnej części strony. Na przykład zmiana go na **CA Dairy Analysis**.
1. Po lewej stronie strony eksperymentu rozwiń pozycję **Zapisane zestawy danych**, a następnie pozycję Moje zestawy **danych**. Powinieneś zobaczyć **cadairydata.csv,** który został przesłany wcześniej.
1. Przeciągnij i upuść **zestaw danych csdairydata.csv** do eksperymentu.
1. W polu **Elementy eksperymentu wyszukiwania** u góry lewego okienka wpisz [Wykonaj skrypt R][execute-r-script]. Zobaczysz, że moduł pojawi się na liście wyszukiwania.
1. Przeciągnij i upuść moduł [Wykonaj skrypt R][execute-r-script] na paletę.  
1. Połącz dane wyjściowe **zestawu danych csdairydata.csv** z lewym wejściem **(Dataset1)** [skryptu Wykonywanie języka R][execute-r-script].
1. **Nie zapomnij wybrać "Zapisz"!**  

W tym momencie eksperyment powinien wyglądać mniej więcej jak rysunek 3.

![Eksperyment analizy mleczarskiej urzędu certyfikacji z zestawem danych i modułem Wykonywanie skryptu R](./media/r-quickstart/fig3.png)

*Rysunek 3. Eksperyment analizy mleczarskiej urzędu certyfikacji z zestawem danych i modułem Wykonaj skrypt R.*

#### <a name="check-on-the-data"></a>Sprawdź dane

Przyjrzyjmy się danym, które załadowaliśmy do naszego eksperymentu. W eksperymencie wybierz dane wyjściowe **zestawu danych cadairydata.csv** i wybierz opcję **wizualizacji**. Powinieneś zobaczyć coś takiego jak Rysunek 4.  

![Podsumowanie zestawu danych cadairydata.csv](./media/r-quickstart/fig4.png)

*Rysunek 4. Podsumowanie zestawu danych cadairydata.csv.*

W tym widoku widzimy wiele przydatnych informacji. Możemy zobaczyć kilka pierwszych wierszy tego zestawu danych. Jeśli wybierzemy kolumnę, sekcja Statystyka zawiera więcej informacji o kolumnie. Na przykład wiersz Typ funkcji pokazuje nam, jakie typy danych Usługi Azure Machine Learning Studio (klasyczne) przypisane do kolumny. O szybki wygląd jak to jest dobry kontroli poczytalności, zanim zaczniemy robić żadnych poważnych prac.

### <a name="first-r-script"></a>Pierwszy skrypt R

Utwórzmy prosty pierwszy skrypt języka R do eksperymentowania w usłudze Azure Machine Learning Studio (klasyczny). Stworzyłem i przetestowałem następujący skrypt w RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Teraz muszę przenieść ten skrypt do usługi Azure Machine Learning Studio (klasyczny). Mógłbym po prostu wyciąć i wkleić. Jednak w tym przypadku prześlę mój skrypt R za pośrednictwem pliku zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Wprowadzanie danych do modułu Wykonywanie skryptu R

Przyjrzyjmy się wejściom do modułu [Wykonywanie skryptu R.][execute-r-script] W tym przykładzie odczytamy dane mleczne kalifornijską w module [Wykonaj skrypt R.][execute-r-script]  

Istnieją trzy możliwe wejścia dla modułu [Execute R Script.][execute-r-script] W zależności od aplikacji można użyć dowolnego lub wszystkich tych danych wejściowych. Jest również całkowicie uzasadnione, aby użyć skryptu R, który nie wymaga żadnych danych wejściowych w ogóle.  

Spójrzmy na każdy z tych wejść, przechodząc od lewej do prawej. Nazwy każdego z danych wejściowych można wyświetlić, umieszczając kursor nad wejściem i czytając etykietkę narzędzia.  

#### <a name="script-bundle"></a>Pakiet skryptów

Wejście pakietu skryptów umożliwia przekazywanie zawartości pliku zip do modułu [Wykonywanie skryptu R.][execute-r-script] Można użyć jednego z następujących poleceń, aby odczytać zawartość pliku zip do kodu R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Usługa Azure Machine Learning Studio (klasyczna) traktuje pliki w zip tak, jakby były w katalogu src/, więc musisz prefiksować nazwy plików o tej nazwie katalogu. Na przykład, jeśli zip `yourfile.R` zawiera `yourData.rdata` pliki i w katalogu głównym zip, `src/yourData.rdata` należy `source` `load`rozwiązać te jak `src/yourfile.R` i podczas korzystania i .

Omówiliśmy już ładowanie zestawów danych w [załaduj zestaw danych](#loading). Po utworzeniu i przetestowaniu skryptu Języka R pokazanego w poprzedniej sekcji wykonaj następujące czynności:

1. Zapisz skrypt R w pliku . R. Nazywam mój plik skryptu "simpleplot. R". Oto zawartość.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Utwórz plik zip i skopiuj skrypt do tego pliku zip. W systemie Windows możesz kliknąć plik prawym przyciskiem myszy i wybrać polecenie **Wyślij do**, a następnie **skompresować folder**. Spowoduje to utworzenie nowego pliku zip zawierającego "simpleplot. R".

1. Dodaj plik do **zestawów danych** w usłudze Azure Machine Learning Studio (klasyczny), określając typ jako **zip**. Plik zip powinien być teraz widoczny w zestawach danych.

1. Przeciągnij i upuść plik zip z **zestawów danych** na **kanwa ML Studio (klasyczna).**

1. Podłącz dane wyjściowe ikony **danych zip** do wejścia **pakietu skryptów** modułu [Wykonywanie skryptu R.][execute-r-script]

1. Wpisz `source()` funkcję o nazwie pliku zip w oknie kodu modułu [Wykonywanie skryptu R.][execute-r-script] W moim przypadku `source("src/simpleplot.R")`wpisałem .  

1. Upewnij się, że wybrano **pozycję Zapisz**.

Po wykonaniu tych kroków moduł [Wykonaj skrypt R][execute-r-script] wykona skrypt Języka R w pliku zip po uruchomieniu eksperymentu. W tym momencie eksperyment powinien wyglądać mniej więcej jak rysunek 5.

![Eksperymentowanie przy użyciu spakowaego skryptu R](./media/r-quickstart/fig6.png)

*Rysunek 5. Eksperymentuj przy użyciu spakowany skrypt R.*

#### <a name="dataset1"></a>Zestaw danych1

Można przekazać prostokątną tabelę danych do kodu R przy użyciu danych wejściowych1. W naszym prostym `maml.mapInputPort(1)` skrypcie funkcja odczytuje dane z portu 1. Te dane są następnie przypisywane do nazwy zmiennej dataframe w kodzie. W naszym prostym skrypcie pierwszy wiersz kodu wykonuje przypisanie.

```R
cadairydata <- maml.mapInputPort(1)
```

Wykonaj eksperyment, wybierając przycisk **Uruchom.** Po zakończeniu wykonywania wybierz moduł [Wykonaj skrypt R,][execute-r-script] a następnie wybierz **pozycję Wyświetl dziennik danych wyjściowych** w okienku właściwości. W przeglądarce powinna pojawić się nowa strona przedstawiająca zawartość pliku output.log. Po przewinięciu w dół powinieneś zobaczyć coś podobnego do następujących.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Dalej w dół strony znajdują się bardziej szczegółowe informacje o kolumnach, które będą wyglądać mniej więcej tak.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Wyniki te są w większości zgodne z oczekiwaniami, z 228 obserwacji i 9 kolumn w dataframe. Możemy zobaczyć nazwy kolumn, typ danych R i próbkę każdej kolumny.

> [!NOTE]
> To samo wydrukowane wyjście jest wygodnie dostępne z wyjścia urządzenia R modułu [Execute R Script.][execute-r-script] Omówimy dane wyjściowe modułu [Wykonywanie skryptu R][execute-r-script] w następnej sekcji.  

#### <a name="dataset2"></a>Zestaw danych2

Zachowanie danych wejściowych2 jest identyczne z dataset1. Za pomocą tego wejścia można przekazać drugą prostokątną tabelę danych do kodu Języka R. Funkcja `maml.mapInputPort(2)`, z argumentem 2, służy do przekazywania tych danych.  

### <a name="execute-r-script-outputs"></a>Wykonywanie wyjść skryptu R

#### <a name="output-a-dataframe"></a>Wyprowadzanie ramki danych

Zawartość ramki danych R jako prostokątnej tabeli można wyprowadzić za pomocą portu `maml.mapOutputPort()` Wynik dataset1 przy użyciu tej funkcji. W naszym prostym skrypcie R jest to wykonywane przez następujący wiersz.

```
maml.mapOutputPort('cadairydata')
```

Po uruchomieniu eksperymentu wybierz port wyjściowy Wynikowy zestaw danych1, a następnie wybierz pozycję **Wizualizuj**. Powinieneś zobaczyć coś takiego jak Rysunek 6.

![Wizualizacja danych dotyczących produktów mlecznych w Kalifornii](./media/r-quickstart/fig7.png)

*Rys.6. Wizualizacja danych dotyczących mleka w Kalifornii.*

To wyjście wygląda identycznie do danych wejściowych, dokładnie tak, jak się spodziewaliśmy.  

### <a name="r-device-output"></a>R Wyjście urządzenia

Dane wyjściowe urządzenia modułu [Wykonywanie skryptu R][execute-r-script] zawiera komunikaty i dane wyjściowe grafiki. Zarówno standardowe dane wyjściowe, jak i standardowe komunikaty o błędach z R są wysyłane do portu wyjściowego urządzenia R.  

Aby wyświetlić dane wyjściowe urządzenia R, wybierz port, a następnie w **wizualizacji**. Widzimy standardowe dane wyjściowe i standardowy błąd ze skryptu R na rysunku 7.

![Standardowe wyjście i błąd standardowy z portu urządzenia R](./media/r-quickstart/fig8.png)

*Rysunek 7. Standardowe wyjście i standardowy błąd z portu urządzenia R.*

Przewijając w dół widzimy dane wyjściowe grafiki z naszego skryptu R na rysunku 8.  

![Dane wyjściowe grafiki z portu urządzenia R](./media/r-quickstart/fig9.png)

*Wykres 8. Dane wyjściowe grafiki z portu urządzenia R.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtrowanie i przekształcanie danych

W tej sekcji wykonamy kilka podstawowych operacji filtrowania i przekształcania danych mlecznych w Kalifornii. Do końca tej sekcji będziemy mieć dane w formacie odpowiednim do tworzenia modelu analitycznego.  

W szczególności w tej sekcji wykonamy kilka typowych zadań czyszczenia i przekształcania danych: przekształcanie typów, filtrowanie na obiektach danych, dodawanie nowych kolumn obliczeniowych i przekształcenia wartości. To tło powinno pomóc w radzeniu sobie z wieloma odmianami napotkanymi w rzeczywistych problemach.

Pełny kod R dla tej sekcji jest dostępny w [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Przekształcenia typu

Teraz, gdy możemy odczytać dane mleczne Kalifornii do kodu R w module [Wykonywanie skryptu R,][execute-r-script] musimy upewnić się, że dane w kolumnach mają zamierzony typ i format.  

R jest dynamicznie wpisywanym językiem, co oznacza, że typy danych są wymuszane z jednego do drugiego zgodnie z wymaganiami. Typy danych niepodzielny w R obejmują numeryczne, logiczne i znak. Typ współczynnika jest używany do kompaktowego przechowywania danych kategorycznych. Znacznie więcej informacji na temat typów danych można znaleźć w odwołaniach [do dalszej lektury](#appendixb) poniżej.

Gdy dane tabelaryczne są odczytywane do R ze źródła zewnętrznego, zawsze warto sprawdzić wynikowe typy w kolumnach. Możesz chcieć kolumny typu znak, ale w wielu przypadkach będzie to wyświetlane jako czynnik lub odwrotnie. W innych przypadkach kolumna, która uważasz, że powinna być liczbowa, jest reprezentowana przez dane znaków, na przykład "1.23", a nie 1.23 jako liczba zmiennoprzecinkowi.  

Na szczęście można łatwo przekonwertować jeden typ na inny, o ile możliwe jest mapowanie. Na przykład nie można przekonwertować "Nevada" na wartość liczbową, ale można ją przekonwertować na współczynnik (zmienną kategoryczną). Innym przykładem można przekonwertować cyfrę 1 na znak "1" lub współczynnik.  

Składnia każdej z tych konwersji jest `as.datatype()`prosta: . Te funkcje konwersji typu są następujące.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Patrząc na typy danych kolumn, które wprowadzamy w poprzedniej sekcji: wszystkie kolumny są typu numerycznego, z wyjątkiem kolumny oznaczonej jako "Miesiąc", która ma charakter typu. Przekonwertujmy to na czynnik i przetestujmy wyniki.  

Usunąłem wiersz, który utworzył macierz scatterplot i dodałem linię konwertującą kolumnę "Miesiąc" na współczynnik. W moim eksperymencie po prostu wyciąć i wkleić kod R do okna kodu [execute R Script][execute-r-script] Module. Można również zaktualizować plik zip i przekazać go do usługi Azure Machine Learning Studio (klasyczny), ale to wymaga kilku kroków.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Wykonajmy ten kod i przyjrzyjmy się dziennikowi danych wyjściowych dla skryptu R. Odpowiednie dane z dziennika przedstawiono na rysunku 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Wykres 9. Podsumowanie ramki danych ze zmienną współczynnika.*

Typ miesiąca powinien teraz powiedzieć "**Factor w / 14 poziomów**". Jest to problem, ponieważ w roku jest tylko 12 miesięcy. Można również sprawdzić, czy typ w **wizualizacji** portu zestawu danych wynik jest "**Kategoryczny**".

Problem polega na tym, że kolumna "Miesiąc" nie była kodowany systematycznie. W niektórych przypadkach miesiąc nazywa się kwiecień, a w innych jest skracany jako kwiecień. Możemy rozwiązać ten problem, przycinając ciąg do 3 znaków. Wiersz kodu wygląda teraz następująco:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Uruchom ponownie eksperyment i wyświetl dziennik danych wyjściowych. Oczekiwane wyniki przedstawiono na rysunku 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Wykres 10. Podsumowanie dataframe z prawidłową liczbą poziomów współczynnika.*

Nasza zmienna współczynnika ma teraz pożądane poziomy 12.

### <a name="basic-data-frame-filtering"></a>Podstawowe filtrowanie ramek danych

Ramki danych R obsługują zaawansowane funkcje filtrowania. Zestawy danych można podszelić za pomocą filtrów logicznych w wierszach lub kolumnach. W wielu przypadkach wymagane będą złożone kryteria filtrowania. Odwołania w [dalszej lektury](#appendixb) poniżej zawierają obszerne przykłady filtrowania ramek danych.  

Jest jeden bit filtrowania, który powinniśmy wykonać w naszym zestawie danych. Jeśli spojrzysz na kolumny w ramce danych cadairydata, zobaczysz dwie niepotrzebne kolumny. Pierwsza kolumna po prostu zawiera numer wiersza, co nie jest bardzo przydatne. Druga kolumna, Year.Month, zawiera zbędne informacje. Możemy łatwo wykluczyć te kolumny przy użyciu następującego kodu języka R.

> [!NOTE]
> Od teraz w tej sekcji, będę po prostu pokazać dodatkowy kod dodaję w execute [R Script][execute-r-script] modułu. Dodam każdą nową **before** linię `str()` przed funkcją. Używam tej funkcji, aby zweryfikować moje wyniki w usłudze Azure Machine Learning Studio (classic).

Dodaję następujący wiersz do mojego kodu R w module [Wykonywanie skryptu R.][execute-r-script]

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Uruchom ten kod w eksperymencie i sprawdź wynik z dziennika danych wyjściowych. Wyniki te przedstawiono na rysunku 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Wykres 11. Podsumowanie ramki danych z usuniętymi dwiema kolumnami.*

Dobra wiadomość! Otrzymujemy oczekiwane wyniki.

### <a name="add-a-new-column"></a>Dodawanie nowej kolumny

Aby utworzyć modele szeregów czasowych będzie wygodne, aby kolumna zawierająca miesiące od początku szeregów czasowych. Utworzymy nową kolumnę "Month.Count".

Aby pomóc w zorganizowaniu kodu, stworzymy naszą pierwszą prostą funkcję, `num.month()`. Następnie zastosujemy tę funkcję, aby utworzyć nową kolumnę w ramce dataframe. Nowy kod jest następujący.

```R
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Teraz uruchom zaktualizowany eksperyment i użyj dziennika danych wyjściowych, aby wyświetlić wyniki. Wyniki te przedstawiono na rysunku 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Wykres 12. Podsumowanie ramki danych z dodatkową kolumną.*

Wygląda na to, że wszystko działa. Mamy nową kolumnę z oczekiwanymi wartościami w naszym ramce danych.

### <a name="value-transformations"></a>Przekształcenia wartości

W tej sekcji wykonamy kilka prostych przekształceń wartości w niektórych kolumnach naszego dataframe. Język Języka Języka Języka R obsługuje prawie arbitralne przekształcenia wartości. Poniższe wzmianki zawierają obszerne przykłady. [Further reading](#appendixb)

Jeśli spojrzysz na wartości w podsumowaniach naszego dataframe powinieneś zobaczyć coś dziwnego tutaj. Czy więcej lodów niż mleko produkowane w Kalifornii? Nie, oczywiście nie, ponieważ nie ma to sensu, smutne, ponieważ ten fakt może być dla niektórych z nas miłośników lodów. Jednostki są różne. Cena jest w jednostkach funtów amerykańskich, mleko jest w jednostkach 1 M funtów amerykańskich, lody jest w jednostkach 1000 galonów AMERYKAŃSKICH, a twaróg jest w jednostkach 1000 funtów amerykańskich. Zakładając, że lody waży około 6,5 funtów na galon, możemy łatwo zrobić mnożenie do konwersji tych wartości, więc wszystkie są w równych jednostkach 1000 funtów.

W naszym modelu prognozowania używamy modelu mnożenia dla trendu i sezonowej korekty tych danych. Transformacja dziennika pozwala nam korzystać z modelu liniowego, upraszczając ten proces. Możemy zastosować transformację dziennika w tej samej funkcji, w której zastosowano mnożnik.

W poniższym kodzie definiuj `log.transform()`nową funkcję i stosuję ją do wierszy zawierających wartości liczbowe. Funkcja `Map()` R służy do `log.transform()` stosowania funkcji do wybranych kolumn ramki danych. `Map()`jest podobny `apply()` do ale pozwala na więcej niż jedną listę argumentów do funkcji. Należy zauważyć, że lista mnożników `log.transform()` dostarcza drugi argument do funkcji. Funkcja `na.omit()` jest używana jako trochę oczyszczania, aby upewnić się, że nie brakuje lub niezdefiniowane wartości w dataframe.

```R
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

W `log.transform()` funkcji dzieje się sporo. Większość tego kodu jest sprawdzanie potencjalnych problemów z argumentami lub radzenia sobie z wyjątkami, które mogą nadal wystąpić podczas obliczeń. Tylko kilka wierszy tego kodu faktycznie zrobić obliczeń.

Celem programowania obronnego jest zapobieganie awarii jednej funkcji, która uniemożliwia kontynuowanie przetwarzania. Nagła awaria długotrwałej analizy może być dość frustrująca dla użytkowników. Aby uniknąć tej sytuacji, należy wybrać domyślne wartości zwracane, które ograniczą uszkodzenia przetwarzania podrzędnego. Zostanie również wyświetlony komunikat, aby ostrzec użytkowników, że coś poszło nie tak.

Jeśli nie są używane do programowania obronnego w R, cały ten kod może wydawać się nieco przytłaczające. Pośejmę cię przez główne kroki:

1. Zdefiniowany jest wektor czterech komunikatów. Te komunikaty są używane do przekazywania informacji o niektórych możliwych błędów i wyjątków, które mogą wystąpić z tego kodu.
2. Zwracam wartość NA dla każdego przypadku. Istnieje wiele innych możliwości, które mogą mieć mniej skutków ubocznych. Mógłbym zwrócić wektor zer lub oryginalny wektor wejściowy, na przykład.
3. Kontrole są uruchamiane na argumenty funkcji. W każdym przypadku, jeśli zostanie wykryty błąd, zwracana jest wartość `warning()` domyślna i jest wywoływana przez funkcję. Używam, `warning()` a `stop()` nie jako ten ostatni zakończy wykonanie, dokładnie to, czego staram się unikać. Zauważ, że napisałem ten kod w stylu proceduralnym, ponieważ w tym przypadku podejście funkcjonalne wydawało się złożone i niejasne.
4. Obliczenia dziennika są zawijane w `tryCatch()` tak, że wyjątki nie spowoduje nagłego zatrzymania przetwarzania. Bez `tryCatch()` większości błędów wywoływanych przez funkcje R powoduje sygnał zatrzymania, który właśnie to robi.

Wykonaj ten kod języka R w eksperymencie i spójrz na wydrukowane dane wyjściowe w pliku output.log. Zostaną wyświetlone przekształcone wartości czterech kolumn w dzienniku, jak pokazano na rysunku 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Wykres 13. Podsumowanie przekształconych wartości w ramce danych.*

Widzimy, że wartości zostały przekształcone. Produkcja mleka znacznie przewyższa obecnie całą inną produkcję produktów mlecznych, przypominając, że obecnie patrzymy na skalę kłód.

W tym momencie nasze dane są czyszczone i jesteśmy gotowi do niektórych modelowania. Patrząc na podsumowanie wizualizacji dla danych wyjściowych zestawu danych wyników naszego [modułu Skrypt wykonywania R,][execute-r-script] zobaczysz kolumnę "Miesiąc" jest "Kategoryczny" z 12 unikalnymi wartościami, ponownie, tak jak chcieliśmy.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Obiekty szeregów czasowych i analiza korelacji

W tej sekcji zbadamy kilka podstawowych obiektów szeregów czasowych R i analizować korelacje między niektórymi zmiennymi. Naszym celem jest wysuwanie ramki danych zawierającej informacje o korelacji par z kilkoma opóźnieniami.

Pełny kod R dla tej sekcji znajduje się w [machinelearningsamples-notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Obiekty szeregów czasowych w R

Jak już wspomniano, szeregi czasowe są serią wartości danych indeksowanych według czasu. R szeregi czasowe obiekty są używane do tworzenia i zarządzania indeks czasu. Istnieje kilka zalet przy użyciu obiektów szeregów czasowych. Obiekty szeregów czasowych zwalniają cię z wielu szczegółów zarządzania wartościami indeksu szeregów czasowych, które są hermetyzowane w obiekcie. Ponadto obiekty szeregów czasowych umożliwiają korzystanie z wielu metod szeregów czasowych do drukowania, drukowania, modelowania itp.

Klasa szeregów czasowych POSIXct jest powszechnie używana i jest stosunkowo prosta. Ta klasa szeregów czasowych mierzy czas od początku epoki, 1 stycznia 1970. W tym przykładzie użyjemy obiektów szeregów czasowych POSIXct. Inne powszechnie używane klasy obiektów szeregów czasowych R obejmują zoo i xts, rozszerzalne szeregi czasowe.

### <a name="time-series-object-example"></a>Przykład obiektu szeregów czasowych

Zacznijmy od naszego przykładu. Przeciągnij i upuść **nowy** moduł [Wykonaj skrypt R][execute-r-script] do eksperymentu. Połącz port wyjściowy wynikowy zestawu danych1 istniejącego modułu [Wykonywanie skryptu R][execute-r-script] z portem wejściowym Zestawu danych1 nowego modułu [Wykonywanie skryptu R.][execute-r-script]

Tak jak w przypadku pierwszych przykładów, w miarę postępów w przykładzie, w niektórych punktach pokażę tylko przyrostowe dodatkowe wiersze kodu R na każdym kroku.  

#### <a name="reading-the-dataframe"></a>Czytanie ramki danych

W pierwszej kolejności przeczytajmy ramkę danych i upewnijmy się, że uzyskamy oczekiwane wyniki. Poniższy kod powinien wykonać zadanie.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Teraz uruchom eksperyment. Dziennik nowego kształtu Wykonaj skrypt R powinien wyglądać jak rysunek 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Wykres 14. Podsumowanie ramki danych w module Wykonywanie skryptu R.*

Te dane mają oczekiwane typy i format. Należy zauważyć, że kolumna "Miesiąc" jest współczynnikiem typu i ma oczekiwaną liczbę poziomów.

#### <a name="creating-a-time-series-object"></a>Tworzenie obiektu szeregów czasowych

Musimy dodać obiekt szeregów czasowych do naszego dataframe. Zastąp bieżący kod następującym, który dodaje nową kolumnę klasy POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teraz sprawdź dziennik. Powinien wyglądać jak rysunek 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Wykres 15. Podsumowanie ramki danych z obiektem szeregów czasowych.*

Z podsumowania wynika, że nowa kolumna jest w rzeczywistości klasy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Eksplorowanie i przekształcanie danych

Przyjrzyjmy się niektóre zmienne w tym zestawie danych. Matryca scatterplot to dobry sposób na szybkie spojrzenie. Zamieniam `str()` funkcję w poprzednim kodzie R na następujący wiersz.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Uruchom ten kod i zobacz, co się stanie. Wykres wyprodukowany w porcie urządzenia R powinien wyglądać jak rysunek 16.

![Macierz scatterplot wybranych zmiennych](./media/r-quickstart/fig17.png)

*Wykres 16. Macierz scatterplot wybranych zmiennych.*

Istnieje kilka dziwnie wyglądające struktury w relacjach między tymi zmiennymi. Być może wynika to z trendów w danych i z faktu, że nie znormalizowaliśmy zmiennych.

### <a name="correlation-analysis"></a>Analiza korelacji

Aby przeprowadzić analizę korelacji, musimy zarówno odkształcać, jak i standaryzować zmienne. Możemy po prostu `scale()` użyć funkcji R, która zarówno wyśrodkowa, jak i skaluje zmienne. Ta funkcja może działać szybciej. Chcę jednak pokazać przykład programowania obronnego w R.

Funkcja `ts.detrend()` pokazana poniżej wykonuje obie te operacje. Następujące dwa wiersze kodu de-trend danych, a następnie standaryzować wartości.

```R
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

W `ts.detrend()` funkcji dzieje się sporo. Większość tego kodu jest sprawdzanie potencjalnych problemów z argumentami lub radzenia sobie z wyjątkami, które mogą nadal wystąpić podczas obliczeń. Tylko kilka wierszy tego kodu faktycznie zrobić obliczeń.

Omówiliśmy już przykład programowania obronnego w transformacjach wartości. Oba bloki obliczeniowe `tryCatch()`są zawinięte w . W przypadku niektórych błędów warto zwrócić oryginalny wektor wejściowy, a w innych przypadkach zwracam wektor zer.  

Należy zauważyć, że regresja liniowa używana do usuwania trendów jest regresją szeregów czasowych. Zmienna predykcjów jest obiektem szeregów czasowych.  

Po `ts.detrend()` zdefiniowaniu stosujemy go do zmiennych interesujących w naszej ramce danych. Musimy wymuszać wynikową `lapply()` listę utworzoną `as.data.frame()`przez dataframe za pomocą . Ze względu na `ts.detrend()`defensywne aspekty , brak przetwarzania jednej ze zmiennych nie uniemożliwi prawidłowego przetwarzania innych.  

Końcowy wiersz kodu tworzy scatterplot pairwise. Po uruchomieniu kodu R wyniki scatterplot są pokazane na rysunku 17.

![Parowosy scatterplot de-trended i standaryzowanych szeregów czasowych](./media/r-quickstart/fig18.png)

*Wykres 17. Parowosy scatterplot de-trended i standaryzowanych szeregów czasowych.*

Można porównać te wyniki z wynikami pokazanymi na rysunku 16. Po usunięciu trendu i standaryzowanych zmiennych widzimy znacznie mniejszą strukturę w relacjach między tymi zmiennymi.

Kod do obliczania korelacji jako R ccf obiektów jest w następujący sposób.

```R
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Uruchomienie tego kodu tworzy dziennik pokazany na rysunku 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Wykres 18. Lista obiektów ccf z analizy korelacji pairwise.*

Istnieje wartość korelacji dla każdego opóźnienia. Żadna z tych wartości korelacji są wystarczająco duże, aby być znaczące. Możemy zatem stwierdzić, że każdą zmienną możemy modelować niezależnie.

### <a name="output-a-dataframe"></a>Wyprowadzanie ramki danych
Obliczyliśmy korelacje par jako listę obiektów R ccf. Stanowi to nieco problem, ponieważ wynikowy port wyjściowy zestawu danych naprawdę wymaga ramki danych. Ponadto obiekt ccf jest sam na liście i chcemy tylko wartości w pierwszym elemencie tej listy, korelacje w różnych opóźnieniach.

Poniższy kod wyodrębnia wartości opóźnień z listy obiektów ccf, które same są listami.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Pierwszy wiersz kodu jest nieco trudne, a niektóre wyjaśnienie może pomóc zrozumieć. Pracując od wewnątrz mamy następujące:

1. '**[[**' operator z argumentem '**1**' ' wybiera wektor korelacji w opóźnieniach z pierwszego elementu listy obiektów ccf.
2. Funkcja `do.call()` stosuje `rbind()` funkcję nad elementami listy zwraca `lapply()`przez .
3. Funkcja `data.frame()` wymusza wynik `do.call()` wytwarzany przez do dataframe.

Należy zauważyć, że nazwy wierszy znajdują się w kolumnie ramki danych. W ten sposób zachowuje nazwy wierszy, gdy są one dane wyjściowe z [execute r script][execute-r-script].

Uruchomienie kodu tworzy dane wyjściowe pokazane na rysunku 19, gdy **wizualizować** dane wyjściowe na portu dataset wynik. Nazwy wierszy znajdują się w pierwszej kolumnie zgodnie z przeznaczeniem.

![Wyniki uzyskane z analizy korelacji](./media/r-quickstart/fig20.png)

*Wykres 19. Wyniki pochodzą z analizy korelacji.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Przykład szeregów czasowych: prognozowanie sezonowe

Nasze dane są teraz w formie odpowiedniej do analizy i ustaliliśmy, że nie ma znaczących korelacji między zmiennymi. Przejdźmy dalej i utwórz model prognozowania szeregów czasowych. Korzystając z tego modelu, prognozujemy produkcję mleka w Kalifornii na okres 12 miesięcy 2013 roku.

Nasz model prognozowania będzie miał dwa składniki: składnik trendu i komponent sezonowy. Pełna prognoza jest wynikiem tych dwóch składników. Ten typ modelu jest znany jako model mnożenia. Alternatywą jest model addytywny. Zastosowaliśmy już przekształcenie dziennika do zmiennych będących przedmiotem zainteresowania, co sprawia, że analiza ta jest łagodna.

Pełny kod R dla tej sekcji znajduje się w [machinelearningsamples-notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Tworzenie ramki danych do analizy

Zacznij od dodania **nowego** modułu [Wykonaj skrypt R][execute-r-script] do eksperymentu. Połącz **dane wyjściowe zestawu danych wynikowych** istniejącego modułu [Wykonywanie skryptu R][execute-r-script] z **wejściem Dataset1** nowego modułu. Wynik powinien wyglądać mniej więcej jak rysunek 20.

![Dodano eksperyment z nowym modułem Execute R Script](./media/r-quickstart/fig21.png)

*Wykres 20. Eksperyment z nowym modułem Wykonywanie skryptu R dodano.*

Podobnie jak w analizie korelacji, którą właśnie ukończyliśmy, musimy dodać kolumnę z obiektem szeregów czasowych POSIXct. Poniższy kod zrobi właśnie to.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Uruchom ten kod i spójrz na dziennik. Wynik powinien wyglądać jak rysunek 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Wykres 21. Podsumowanie ramki danych.*

Dzięki temu wynikowi jesteśmy gotowi rozpocząć naszą analizę.

### <a name="create-a-training-dataset"></a>Tworzenie zestawu danych szkoleniowych

Po utworzeniu ramki danych musimy utworzyć zestaw danych szkoleniowych. Dane te będą obejmować wszystkie obserwacje z wyjątkiem ostatnich 12, roku 2013, który jest naszym zestawem danych testowych. Poniższy kod podsyci ramkę danych i tworzy wykresy produkcji mlecznej i zmiennych cenowych. Następnie tworzę działki czterech zmiennych produkcyjnych i cenowych. Funkcja anonimowa służy do definiowania niektórych ulepszeń dla wykresu, a następnie `Map()`iteracji na liście pozostałych dwóch argumentów za pomocą pliku . Jeśli myślisz, że for pętli byłoby dobrze tutaj, masz rację. Ale ponieważ R jest językiem funkcjonalnym, pokazuję ci funkcjonalne podejście.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Uruchomienie kodu tworzy serię wykresów szeregów czasowych z danych wyjściowych urządzenia R pokazanych na rysunku 22. Należy zauważyć, że oś czasu znajduje się w jednostkach dat, co jest dobrą zaletą metody wykresu szeregów czasowych.

![Pierwsze wykresy serii czasowych produkcji mlecznej w Kalifornii i dane cenowe](./media/r-quickstart/unnamed-chunk-161.png)

![Drugie z serii czasowych wykresów produkcji mlecznej w Kalifornii i danych cenowych](./media/r-quickstart/unnamed-chunk-162.png)

![Trzeci z wykresów szeregowych w Kalifornii produkcji mlecznej i danych cenowych](./media/r-quickstart/unnamed-chunk-163.png)

![Czwarte z serii czasowych wykresów produkcji mlecznej w Kalifornii i danych cenowych](./media/r-quickstart/unnamed-chunk-164.png)

*Wykres 22. Wykresy szeregów czasowych produkcji mlecznej w Kalifornii i dane cenowe.*

### <a name="a-trend-model"></a>Model trendu

Po utworzeniu obiektu szeregów czasowych i zapoznaniu się z danymi, zacznijmy konstruować model trendu dla danych dotyczących produkcji mleka w Kalifornii. Możemy to zrobić za pomocą regresji szeregów czasowych. Jednak z wykresu wynika, że będziemy potrzebować więcej niż nachylenia i przechwycić, aby dokładnie modelować obserwowany trend w danych szkoleniowych.

Biorąc pod uwagę małą skalę danych, zbuduję model trendu w RStudio, a następnie wytnij i wklejam wynikowy model do usługi Azure Machine Learning Studio (klasyczny). RStudio zapewnia interaktywne środowisko dla tego typu analizy interaktywnej.

Jako pierwsza próba spróbuję regresji wielomianowej o mocach do 3. Istnieje realne niebezpieczeństwo nadmiernego dopasowania tego rodzaju modeli. Dlatego najlepiej jest unikać warunków wysokiego rzędu. Funkcja `I()` hamuje interpretację zawartości (interpretuje zawartość "tak jak jest") i umożliwia zapisanie dosłownie interpretowanej funkcji w równaniu regresji.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Spowoduje to wygenerowanie następujących czynników.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Z wartości`Pr(>|t|)`P ( ) w tym wyjściu, widzimy, że kwadratowy termin może nie być znaczący. Użyję `update()` tej funkcji, aby zmodyfikować ten model, upuszczając termin kwadratowy.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Spowoduje to wygenerowanie następujących czynników.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Wygląda to lepiej. Wszystkie warunki są znaczące. Jednak wartość 2e-16 jest wartością domyślną i nie powinna być traktowana zbyt poważnie.  

Jako test poczytalności zróbmy wykres szeregów czasowych danych o produkcji mlecznej w Kalifornii z pokazano krzywą trendu. Dodałem następujący kod w usłudze Azure Machine Learning Studio (klasyczny) Wykonaj model [skryptu R][execute-r-script] (nie RStudio), aby utworzyć model i utworzyć wykres. Wynik jest pokazany na rysunku 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dane dotyczące produkcji mleka w Kalifornii z pokazanym modelem trendu](./media/r-quickstart/unnamed-chunk-18.png)

*Wykres 23. Dane dotyczące produkcji mleka w Kalifornii z pokazanym modelem trendu.*

Wygląda na to, że model trendu dość dobrze pasuje do danych. Co więcej, nie wydaje się być dowodów na zbyt dopasowane, takie jak dziwne wiggles w krzywej modelu.  

### <a name="seasonal-model"></a>Model sezonowy

Z modelem trendu w ręku, musimy naciskać i uwzględnić efekty sezonowe. Użyjemy miesiąca roku jako zmiennej manekina w modelu liniowym, aby uchwycić efekt miesiąc po miesiącu. Należy zauważyć, że po wprowadzeniu zmiennych czynników do modelu, przecięcie nie może być obliczane. Jeśli tego nie zrobisz, formuła jest przeterminowa, a R upuści jeden z pożądanych czynników, ale zachowa termin przecięcia.

Ponieważ mamy zadowalający model trendu, `update()` możemy użyć tej funkcji, aby dodać nowe terminy do istniejącego modelu. -1 w formule aktualizacji porzuca termin przecięcia. Kontynuując w RStudio na chwilę:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Spowoduje to wygenerowanie następujących czynników.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Widzimy, że model nie ma już terminu przechwycić i ma 12 istotnych czynników miesięcznych. To jest dokładnie to, co chcieliśmy zobaczyć.

Zróbmy kolejną fabułę serii czasowej danych z produkcji mlecznej w Kalifornii, aby zobaczyć, jak dobrze działa model sezonowy. Dodałem następujący kod w usłudze Azure Machine Learning Studio (klasyczny) [Wykonaj skrypt R,][execute-r-script] aby utworzyć model i utworzyć wykres.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Uruchomienie tego kodu w usłudze Azure Machine Learning Studio (classic) tworzy wykres pokazany na rysunku 24.

![Produkcja mleka w Kalifornii z modelem, w tym efektami sezonowymi](./media/r-quickstart/unnamed-chunk-20.png)

*Wykres 24. Produkcja mleka w Kalifornii z modelem, w tym efektami sezonowymi.*

Dopasowanie do danych przedstawionych na rysunku 24 jest raczej zachęcające. Zarówno trend, jak i efekt sezonowy (zmienność miesięczna) wyglądają rozsądnie.

Jako kolejny sprawdzić na nasz model, rajmy spojrzeć na resztki. Poniższy kod oblicza przewidywane wartości z naszych dwóch modeli, oblicza resztki dla modelu sezonowego, a następnie kreśli te pozostałości dla danych szkoleniowych.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Powierzchnia resztowa przedstawiona jest na rysunku 25.

![Pozostałości modelu sezonowego dla danych szkoleniowych](./media/r-quickstart/unnamed-chunk-21.png)

*Wykres 25. Pozostałości modelu sezonowego dla danych szkoleniowych.*

Te pozostałości wyglądają rozsądnie. Nie ma szczególnej struktury, z wyjątkiem skutków recesji w latach 2008-2009, której nasz model nie uwzględnia szczególnie dobrze.

Wykres pokazany na rysunku 25 jest przydatny do wykrywania dowolnych wzorców zależnych od czasu w pozostałościach. Wyraźne podejście do obliczania i drukowania pozostałości użyłem umieszcza resztki w kolejności czasu na wykresie. Gdybym natomiast wykreślił `milk.lm$residuals`, działka nie byłaby w porządku czasowym.

Można również `plot.lm()` użyć do wyprodukowania serii wykresów diagnostycznych.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ten kod tworzy serię wykresów diagnostycznych pokazanych na rysunku 26.

![Pierwsze wykresy diagnostyczne dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-221.png)

![Drugi z działek diagnostycznych dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-222.png)

![Trzecia powierzchnia diagnostyczna dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-223.png)

![Czwarty wykres diagnostyczny dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-224.png)

*Wykres 26. Wykresy diagnostyczne dla modelu sezonowego.*

Istnieje kilka bardzo wpływowych punktów zidentyfikowanych w tych działkach, ale nic nie powoduje wielkiego niepokoju. Co więcej, z wykresu Normalnego Q-Q widzimy, że resztki są zbliżone do normalnie rozłożonych, co jest ważnym założeniem dla modeli liniowych.

### <a name="forecasting-and-model-evaluation"></a>Prognozowanie i ocena modelu

Jest jeszcze jedna rzecz do zrobienia, aby uzupełnić nasz przykład. Musimy obliczyć prognozy i zmierzyć błąd w stosunku do rzeczywistych danych. Nasza prognoza będzie na 12 miesięcy 2013 roku. Możemy obliczyć miarę błędu dla tej prognozy do rzeczywistych danych, które nie są częścią naszego zestawu danych szkoleniowych. Ponadto możemy porównać wydajność danych z 18 lat szkolenia z 12-miesięcznych danych testowych.  

Liczba metryk są używane do pomiaru wydajności modeli szeregów czasowych. W naszym przypadku użyjemy błędu średniego kwadratu głównego (RMS). Następująca funkcja oblicza błąd RMS między dwiema seriami.  

```R
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Podobnie jak `log.transform()` w odniesieniu do funkcji omówione w sekcji "Transformacje wartości", istnieje sporo sprawdzania błędów i kod odzyskiwania wyjątków w tej funkcji. Stosowane zasady są takie same. Praca jest wykonywana w dwóch `tryCatch()`miejscach zawiniętych w . Po pierwsze, szeregi czasowe są wykładnicze, ponieważ pracujemy z dziennikami wartości. Po drugie, obliczany jest rzeczywisty błąd RMS.  

Wyposażony w funkcję do pomiaru błędu RMS, skompilujmy i wypujmy ramkę danych zawierającą błędy RMS. Będziemy zawierać warunki dla samego modelu trendu i kompletny model z czynnikami sezonowymi. Poniższy kod wykonuje zadanie przy użyciu dwóch modeli liniowych, które stworzyliśmy.

```R
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Uruchomienie tego kodu powoduje uzyskanie danych wyjściowych pokazanych na rysunku 27 na porcie wyjściowym zestawu danych wynikowych.

![Porównanie błędów RMS dla modeli](./media/r-quickstart/fig26.png)

*Wykres 27. Porównanie błędów RMS dla modeli.*

Z tych wyników widzimy, że dodanie czynników sezonowych do modelu znacznie zmniejsza błąd rms. Nic dziwnego, że błąd RMS dla danych szkoleniowych jest nieco mniejszy niż w przypadku prognozy.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Przewodnik po dokumentacji RStudio

RStudio jest dość dobrze udokumentowane. Oto kilka linków do kluczowych sekcji dokumentacji RStudio, aby rozpocząć pracę.

* **Tworzenie projektów** — można organizować i zarządzać kodem języka R w projektach przy użyciu RStudio. Zobacz [korzystanie z projektów,](https://support.rstudio.com/hc/articles/200526207-Using-Projects) aby uzyskać szczegółowe informacje. Zalecam, aby postępować zgodnie z tymi wskazówkami i utworzyć projekt dla przykładów kodu R w tym artykule.  
* **Edycja i wykonywanie kodu R** - RStudio zapewnia zintegrowane środowisko do edycji i wykonywania kodu R. Zobacz [edytowanie i wykonywanie kodu, aby](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) uzyskać szczegółowe informacje.
* **Debugowanie** — RStudio zawiera zaawansowane możliwości debugowania. Zobacz [Debugowanie za pomocą RStudio, aby](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) uzyskać więcej informacji na temat tych funkcji. Aby uzyskać informacje na temat funkcji rozwiązywania problemów z punktami przerwania, zobacz [Rozwiązywanie problemów z punktami przerwania](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Dodatkowe informacje

Ten samouczek programowania języka R obejmuje podstawy tego, czego potrzebujesz, aby używać języka języka Języka R w usłudze Azure Machine Learning Studio (klasyczny). Jeśli nie jesteś zaznajomiony z R, dwa wprowadzenia są dostępne na CRAN:

* [R dla początkujących](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) przez Emmanuel Paradis jest dobrym miejscem do rozpoczęcia.  
* [Wprowadzenie do R](https://cran.r-project.org/doc/manuals/R-intro.html) przez W. N. Venables et. Al. przechodzi nieco głębiej.

Istnieje wiele książek na R, które mogą pomóc ci zacząć. Oto kilka uważam za przydatne:

* **Sztuka programowania R: Tour of Statistical Software Design** Norman Matloff jest doskonałym wprowadzeniem do programowania w R.  
* **R Książka kucharska** Paula Teetora stanowi problem i rozwiązanie podejścia do korzystania z R.  
* **R w akcji** Robert Kabacoff to kolejna przydatna książka wprowadzająca. Towarzysząca [witryna quick r](https://www.statmethods.net/) jest użytecznym zasobem.
* **R Inferno** Patrick Burns to zaskakująco humorystyczny książki, która zajmuje się szereg trudnych i trudnych tematów, które można napotkać podczas programowania w R. Książka jest dostępna za darmo w [The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Jeśli chcesz głęboko zanurzyć się w zaawansowanych tematów w R, spójrz na książkę **Advanced R** Hadley Wickham. Wersja online tej książki jest dostępna [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)za darmo w .

Katalog pakietów szeregów czasowych R można znaleźć w [widoku zadań CRAN: Analiza szeregów czasowych](https://cran.r-project.org/web/views/TimeSeries.html). Aby uzyskać informacje na temat określonych pakietów obiektów szeregów czasowych, należy zapoznać się z dokumentacją dla tego pakietu.

Książka **Introductory Time Series** with R Paula Cowpertwaita i Andrew Metcalfe'a stanowi wprowadzenie do korzystania z R do analizy szeregów czasowych. Wiele innych tekstów teoretycznych podaje przykłady R.

Oto kilka świetnych zasobów internetowych:

* DataCamp uczy R w zaciszu przeglądarki z lekcji wideo i ćwiczeń kodowania. Istnieją interaktywne samouczki na temat najnowszych technik języka R i pakietów. Weź bezpłatny [interaktywny poradnik R](https://www.datacamp.com/courses/introduction-to-r).
* [Dowiedz się R Programming, The Definitive Guide](https://www.programiz.com/r-programming) z Programiz.
* Szybki [R Tutorial](https://www.cyclismo.org/tutorial/R/) Kelly Black z Clarkson University.
* Istnieje ponad 60 zasobów R wymienionych w [top r zasobów językowych, aby poprawić swoje umiejętności danych](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
