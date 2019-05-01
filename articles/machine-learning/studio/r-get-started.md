---
title: Wprowadzenie do języka R
titleSuffix: Azure Machine Learning Studio
description: Użyj tego samouczka programowania R, aby rozpocząć tworzenie rozwiązania do prognozowania, przy użyciu języka R za pomocą usługi Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 5c4fa2260b00043e016748010528926b1b9d74a3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726543"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio"></a>Wprowadzenie do języka R, w języku programowania w usłudze Azure Machine Learning Studio

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Wprowadzenie

Ten samouczek ułatwia rozpoczynanie rozszerzania usługi Azure Machine Learning Studio, używając języka programowania R. Postępuj zgodnie z tego samouczka programowania R do tworzenia, testowania i wykonywanie kodu języka R w programie Studio. Podczas pracy z samouczkiem utworzysz to kompletne rozwiązanie do prognozowania przy użyciu języka R Studio.  

Microsoft Azure Machine Learning Studio zawiera wiele zaawansowanych machine learning i modułów manipulowania danymi. Zaawansowany język R został opisany jako franca język analizy. Trafem korzysta manipulowania analizy i danych w programie Studio można rozszerzyć za pomocą języka R. Ta kombinacja zapewnia skalowalność i łatwość wdrażania Studio elastyczność i szczegółowe analizy języka r.

### <a name="forecasting-and-the-dataset"></a>Prognozowanie i zestaw danych

Prognozowanie jest metodą analitycznych powszechnie wykorzystywane i bardzo użyteczne. Typowe zastosowania w zakresie od Prognozowanie sprzedaż elementów sezonowe, określanie poziomy zapasów optymalne przewidzenia makroekonomicznej zmiennych. Prognozowanie zwykle odbywa się przy użyciu modeli szeregów czasowych.

Dane szeregów czasowych to dane, w której wartości mają indeks czasu. Indeks czasu może być regularnie, np. co miesiąc lub co minutę lub nieprawidłowo. Model szeregów czasowych opiera się na danych szeregów czasowych. Język programowania R zawiera obszerne analizy danych szeregów czasowych i elastyczną architekturę.

W tym przewodniku Zapoznamy się praca z produkcji mleka Kalifornia i danych dotyczących cen. Te dane obejmują miesięczne informacji na temat produkcji mleka produktów kilka i cena mleka systemu plików fat, asortymentu testów porównawczych.

Dane używane w tym artykule, wraz ze skryptów języka R można pobrać z [MachineLearningSamples-notesów/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Dane w pliku `cadairydata.csv` pierwotnie został przekształcony z informacji dostępnych w uniwersytecie Wisconsin na [ https://dairymarkets.com ](https://dairymarkets.com).

### <a name="organization"></a>Organizacja

Firma Microsoft przyszłego postępu przez kilka kroków, jak omówiono sposób tworzenia, testowania i wykonanie kodu manipulowania R analizy i danych w środowisku usługi Azure Machine Learning Studio.  

* Najpierw przeanalizujemy podstawy używania języka R w środowisku usługi Azure Machine Learning Studio.
* Następnie będziemy omawianie różnych aspektów operacji We/Wy danych, kod R i grafiki w środowisku usługi Azure Machine Learning Studio.
* Firma Microsoft będzie konstruowania pierwsza część naszego rozwiązania do prognozowania, tworząc kod czyszczenia i transformacji danych.
* Z naszych danych przygotowane firma Microsoft będzie wykonywać analizę korelacji między kilka zmiennych w zestawie danych.
* Na koniec utworzymy model prognozowania szeregów czasowych sezonowych do produkcji mleka.

## <a id="mlstudio"></a>Interakcja z językiem R w usłudze Machine Learning Studio

Ta sekcja przeprowadzi Cię przez pewne podstawowe informacje o interakcji z językiem programowania R w środowisku usługi Machine Learning Studio. Język R zapewnia zaawansowane narzędzia do tworzenia dostosowanych analizy i modułów manipulowania danych w środowisku usługi Azure Machine Learning Studio.

Będę używać programu RStudio opracowywanie, testowanie i debugowanie kodu języka R na małą skalę. Ten kod jest następnie Wytnij i Wklej w [wykonanie skryptu języka R] [ execute-r-script] modułu w usłudze Machine Learning Studio gotowe do uruchomienia.  

### <a name="the-execute-r-script-module"></a>Moduł wykonywania skryptu języka R

W usłudze Machine Learning Studio, skryptów języka R są uruchamiane w ramach [wykonanie skryptu języka R] [ execute-r-script] modułu. Przykładem [wykonanie skryptu języka R] [ execute-r-script] modułu w usłudze Machine Learning Studio jest pokazany na rysunku 1.

 ![Język programowania R: Moduł wykonywania skryptu języka R, wybranego w usłudze Machine Learning Studio](./media/r-quickstart/fig1.png)

*Rysunek 1. Środowiska usługi Machine Learning Studio przedstawiający wybrano modułu wykonywania skryptu języka R.*

Odwołujące się na rysunku 1, Spójrzmy na niektóre z kluczowych części w środowisku usługi Machine Learning Studio do pracy z [wykonanie skryptu języka R] [ execute-r-script] modułu.

* Moduły w eksperymencie są wyświetlane w środkowym okienku.
* Górna część w okienku po prawej stronie zawiera okno, aby wyświetlić i edytować Twoje skrypty języka R.  
* Dolna część w okienku po prawej stronie pokazuje niektóre właściwości [wykonanie skryptu języka R][execute-r-script]. Dzienniki błędów i danych wyjściowych można wyświetlić, wybierając odpowiednie punkty tego okienka.

Firma Microsoft będzie oczywiście, w niniejszym dokumencie [wykonanie skryptu języka R] [ execute-r-script] bardziej szczegółowo w dalszej części tego artykułu.

Podczas pracy z złożone funkcje języka R, zalecamy edytowanie, testowanie i debugowanie w programu RStudio. Podobnie jak w przypadku dowolnego Wytwarzanie oprogramowania, stopniowo rozszerzyć swój kod i je przetestować na małych proste przypadki testowe. Następnie wycinanie i wklejanie do okna skryptu języka R [wykonanie skryptu języka R] [ execute-r-script] modułu. To podejście pozwala wykorzystać możliwości usługi Azure Machine Learning Studio i programu RStudio zintegrowanego środowiska programistycznego (IDE).  

#### <a name="execute-r-code"></a>Wykonanie kodu języka R

Każdy kod R [wykonanie skryptu języka R] [ execute-r-script] moduł zostanie wykonana po uruchomieniu eksperymentu, wybierając **Uruchom** przycisk. Po zakończeniu wykonywania znacznik wyboru pojawi się na [wykonanie skryptu języka R] [ execute-r-script] ikony.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Obrony R kodowania dla usługi Azure Machine Learning

Jeśli tworzysz kod R na przykład usługi sieci web za pomocą usługi Azure Machine Learning Studio, należy zdecydowanie zaplanować, jak Twój kod zajmuje się nieoczekiwane dane wejściowe i wyjątków. Aby zachować przejrzystość, I nie została uwzględniona znacznie utrudnień sprawdzania lub obsługi wyjątków w Większość przykładów kodu. Jednak ponieważ będziemy kontynuować I zapewni Ci kilka przykładów funkcji przy użyciu możliwości obsługi wyjątków, R.  

Jeśli potrzebujesz bardziej szczegółowy traktowania Obsługa wyjątków języka R, zalecam, przeczytanie odpowiednich części książka autorstwa Wickham wymienione w poniższej [dalsze informacje](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Debugowanie i testowanie języka R w usłudze Machine Learning Studio

Aby przypomnę zalecam, testowanie i debugowanie kodu języka R na małą skalę w programu RStudio. Jednakże istnieją przypadki, w którym konieczne będzie można wykrywać problemy kodu języka R w [wykonanie skryptu języka R] [ execute-r-script] sam. Ponadto jest dobrą praktyką, aby sprawdzić wyniki w usłudze Machine Learning Studio.

Dane wyjściowe wykonywania kodu języka R i na platformie Azure Machine Learning Studio znajduje się głównie w dane_wyjściowe.log. Dodatkowe informacje będą widoczne w error.log.  

W usłudze Machine Learning Studio wystąpi błąd podczas uruchamiania kodu języka R, powinno być Przyjrzyj się error.log pierwszego przebiegu działań. Ten plik może zawierać komunikaty o błędach przydatne, aby pomóc Ci zrozumieć i poprawić błędu. Zaznacz, aby wyświetlić error.log **błędu w dzienniku** na **w okienku właściwości** dla [wykonanie skryptu języka R] [ execute-r-script] zawierające błąd.

Na przykład został uruchomiony poniższy kod języka R z Niezdefiniowana zmienna y w [wykonanie skryptu języka R] [ execute-r-script] modułu:

```R
x <- 1.0
z <- x + y
```

Ten kod wykonanie nie powiedzie się, co spowoduje wystąpienie błędu. Wybieranie **błędu w dzienniku** na **w okienku właściwości** generuje wyświetlaną pokazano na rysunku 2.

  ![Komunikat o błędzie wyskakujące okienko](./media/r-quickstart/fig2.png)

*Rysunek 2. Komunikat o błędzie wyskakujących.*

Wygląda na to, należy szukać w dane_wyjściowe.log, aby wyświetlić komunikat o błędzie R. Wybierz [wykonanie skryptu języka R] [ execute-r-script] , a następnie wybierz **wyświetlić dane_wyjściowe.log** elementu na **w okienku właściwości** po prawej stronie. Zostanie otwarte nowe okno przeglądarki i można znaleźć w następujących tematach.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ten komunikat o błędzie zawiera żadnych niespodzianek i wyraźnie określa problem.

Aby sprawdzić wartość dowolnego obiektu w języku R, można wydrukować te wartości do pliku dane_wyjściowe.log. Badanie wartości obiektu zasady są zasadniczo takie same jak interaktywnych sesji języka R. Na przykład jeśli wpiszesz nazwę zmiennej w wierszu do pliku dane_wyjściowe.log będą wypisywane wartość obiektu.  

#### <a name="packages-in-machine-learning-studio"></a>Pakiety w usłudze Machine Learning Studio

Studio jest dostarczany z ponad 350 pakietów języka R wstępnie zainstalowane. Można użyć następującego kodu w [wykonanie skryptu języka R] [ execute-r-script] modułu, aby pobrać listę wstępnie zainstalowane pakiety.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Jeśli w tej chwili nie rozumiesz ostatni wiersz kodu, Czytaj dalej. W dalszej części tego artykułu często omówimy przy użyciu języka R w środowisku Studio.

### <a name="introduction-to-rstudio"></a>Wprowadzenie do programu RStudio

RStudio to powszechnie używane środowisko IDE dla języka R. Będę używać programu RStudio edytowanie, testowanie i debugowanie część kodu języka R używanych w tym przewodniku. Gdy kod R jest przetestowany i przygotowany, można po prostu wycinania i wklejania w edytorze programu RStudio do Machine Learning Studio [wykonanie skryptu języka R] [ execute-r-script] modułu.  

Jeśli nie masz języka programowania R zainstalowane na komputerze stacjonarnym, zalecamy, możesz to zrobić teraz. Bezpłatne materiały do pobrania z językiem R typu open source dostępnych w kompleksowe R archiwum sieci (CRAN) na [ https://www.r-project.org/ ](https://www.r-project.org/). Pliki do pobrania są dostępne dla Windows, Macos i Linux/UNIX. Wybierz pobliskich dublowania i postępuj zgodnie z instrukcjami pobierania. Ponadto CRAN zawiera mnóstwo przydatne pakietów manipulowania analizy i danych.

Jeśli jesteś nowym użytkownikiem programu RStudio, należy pobrać i zainstalować klasycznej wersji. Można znaleźć programu RStudio pliki do pobrania dla Windows, Mac OS i systemu Linux/UNIX w http://www.rstudio.com/products/RStudio/. Wykonaj instrukcje podane w instalacji programu RStudio na komputerze stacjonarnym.  

Samouczek Wprowadzenie do programu rstudio przy znajduje się w temacie [za pomocą środowiska IDE RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Podać dodatkowe informacje na temat korzystania z programu RStudio w [Przewodnik po dokumentacji programu RStudio](#appendixa) poniżej.  

## <a id="scriptmodule"></a>Pobieranie danych i modułu wykonywania skryptu języka R

W tej sekcji omówimy, jak pobrać dane do i z [wykonanie skryptu języka R] [ execute-r-script] modułu. Omówimy sposób obsługi różnych typów danych odczytu do i z [wykonanie skryptu języka R] [ execute-r-script] modułu.

Kompletny kod dla tej sekcji znajduje się w [MachineLearningSamples-notesów/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio"></a>Ładowanie i sprawdź dane w usłudze Machine Learning Studio

#### <a id="loading"></a>Ładowanie zestawu danych

Zaczniemy od załadowania **csdairydata.csv** pliku do usługi Azure Machine Learning Studio.

1. Uruchom środowiska usługi Azure Machine Learning Studio.
1. Wybierz **+ nowy** w lewym dolnym rogu ekranu i wybierz **zestawu danych**.
1. Wybierz **z lokalnego pliku**, a następnie **Przeglądaj** aby wybrać plik.
1. Upewnij się, że wybrano **pliku CSV ogólnego z nagłówkiem (.csv)** jako typ zestawu danych.
1. Kliknij znacznik wyboru.
1. Po przekazaniu zestaw danych powinien zostać wyświetlony nowy zestaw danych, wybierając **zestawów danych** kartę.  

#### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Teraz, gdy niektóre dane w usłudze Machine Learning Studio, musimy utworzyć eksperyment, aby wykonywać analizę danych.  

1. Wybierz **+ nowy** w dolnym po lewej stronie i wybierz **eksperymentu**, następnie **pusty eksperyment**.
1. Można określić nazwę eksperymentu wybierając i modyfikowanie **eksperymentu utworzony na...** tytuł w górnej części strony. Na przykład, zmieniając go na **analizy Dairy urzędu certyfikacji**.
1. Po lewej stronie eksperymentu, rozwiń węzeł **zapisane zestawów danych**, a następnie **Moje zestawy danych**. Powinien zostać wyświetlony **cadairydata.csv** który został wcześniej przekazany.
1. Przeciąganie i upuszczanie **csdairydata.csv dataset** na eksperyment.
1. W **wyszukiwania eksperymentować elementów** polu w górnej części okienka po lewej stronie, a typ [wykonanie skryptu języka R][execute-r-script]. Zostanie wyświetlony modułu są wyświetlane na liście wyszukiwania.
1. Przeciąganie i upuszczanie [wykonanie skryptu języka R] [ execute-r-script] modułu na z palety.  
1. Połącz wyjście **csdairydata.csv dataset** wejściem skrajnie po lewej stronie (**Dataset1**) z [wykonanie skryptu języka R][execute-r-script].
1. **Nie zapomnij wybierz opcję "Zapisz"!**  

W tym momencie eksperymentu powinien wyglądać jak rysunek 3.

![Analiza Dairy urzędu certyfikacji eksperymentować z zestawu danych i modułu wykonywania skryptu języka R](./media/r-quickstart/fig3.png)

*Rysunek 3. Analiza Dairy urzędu certyfikacji eksperymentować z zestawu danych i modułu wykonywania skryptu języka R.*

#### <a name="check-on-the-data"></a>Sprawdzanie danych

Przyjrzyjmy się przyjrzeć się dane, które możemy zostały załadowane do naszych eksperymentu. Z eksperymentu, wybierz dane wyjściowe **cadairydata.csv dataset** i wybierz **wizualizacji**. Powinien zostać wyświetlony podobny rysunek 4.  

![Podsumowanie cadairydata.csv zestawu danych](./media/r-quickstart/fig4.png)

*Rysunek 4. Podsumowanie cadairydata.csv zestawu danych.*

W tym widoku widzimy mnóstwo przydatnych informacji. Możemy zobaczyć pierwsze kilka wierszy tego zestawu danych. Gdy wybierzemy kolumny, w sekcji Statystyki zawiera więcej informacji na temat kolumny. Na przykład wiersz typu funkcji pokazuje, nam jakiego typu dane z usługi Azure Machine Learning Studio przypisane do kolumny. Krótkie omówienie, jak to jest wyboru dobre poprawnością Zanim zaczniemy wykonać poważne pracę.

### <a name="first-r-script"></a>Pierwszy skrypt języka R

Utwórzmy prosty pierwszy skrypt języka R do eksperymentowania w usłudze Azure Machine Learning Studio. Mam utworzone i przetestowane poniższy skrypt w programu RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('cadairydata')
```

Teraz chcę przenieść ten skrypt do usługi Azure Machine Learning Studio. Czy można po prostu wyciąć i wkleić. Jednak w takim przypadku można transferować mojego skryptu języka R przy użyciu pliku zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Dane wejściowe do modułu wykonywania skryptu języka R

Przyjrzyjmy się jej przyjrzeć dane wejściowe [wykonanie skryptu języka R] [ execute-r-script] modułu. W tym przykładzie mamy dane zostaną odczytane Kalifornia mleka do [wykonanie skryptu języka R] [ execute-r-script] modułu.  

Istnieją trzy możliwe dane wejściowe dla [wykonanie skryptu języka R] [ execute-r-script] modułu. W zależności od aplikacji, mogą używać jednego lub wszystkie te dane wejściowe. Jest również całkowicie uzasadnione użyć skryptu R, który nie wymaga danych wejściowych w ogóle.  

Przyjrzyjmy się każdej z tych danych wejściowych, przechodząc od lewej do prawej. Zobacz z nazwy każdego z wejść, umieszczając kursor za pośrednictwem danych wejściowych i odczytywania etykietki narzędzia.  

#### <a name="script-bundle"></a>Pakiet skryptu

Pakiet skryptu wejściowych umożliwia przekazywanie zawartości pliku zip do [wykonanie skryptu języka R] [ execute-r-script] modułu. Odczytać zawartość pliku zip do kodu języka R, można użyć jednego z następujących poleceń.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Usługa Azure Machine Learning Studio traktuje pliki w pliku zip, tak, jakby znajdują się w src / katalog, więc należy jako prefiks nazwy pliku o tej nazwie katalogu. Na przykład, jeśli plik zip zawiera pliki `yourfile.R` i `yourData.rdata` w katalogu głównym pliku zip, czy ich jako rozwiązywania `src/yourfile.R` i `src/yourData.rdata` przy użyciu `source` i `load`.

Omówiliśmy już zestawów podczas ładowania danych w [załadowania zestawu danych](#loading). Po utworzeniu i przetestowane skrypt języka R, pokazano w poprzedniej sekcji, wykonaj następujące czynności:

1. Zapisz skrypt języka R do. Plik języka R. Czy mogę wywołać Mój plik skryptu "simpleplot. "R". Poniżej przedstawiono zawartość.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio
   maml.mapOutputPort('cadairydata')
   ```

1. Tworzenie pliku zip, a następnie skopiuj skrypt do tego pliku zip. W Windows, kliknij prawym przyciskiem myszy plik i wybierz **wysyłać**, a następnie **folder skompresowany**. Spowoduje to utworzenie nowego pliku zip zawierającego "simpleplot. Plik języka R".

1. Dodaj plik do **zestawów danych** w usłudze Machine Learning Studio, określając typ jako **zip**. Powinien zostać wyświetlony plik zip w swoich zestawów danych.

1. Przeciągnij i upuść plik zip z **zestawów danych** na **obszaru roboczego usługi ML Studio**.

1. Połącz wyjście **zip danych** ikonę, aby **pakietu skryptu** danych wejściowych z [wykonanie skryptu języka R] [ execute-r-script] modułu.

1. Typ `source()` funkcję z nazwy pliku zip do okna kodu dla [wykonanie skryptu języka R] [ execute-r-script] modułu. W moim przypadku po wpisaniu `source("src/simpleplot.R")`.  

1. Upewnij się, możesz wybrać **Zapisz**.

Po zakończeniu tych kroków [wykonanie skryptu języka R] [ execute-r-script] moduł powoduje uruchomienie skryptu języka R w pliku zip, po uruchomieniu eksperymentu. W tym momencie eksperyment powinien wyglądać jak rysunek 5.

![Eksperymentu przy użyciu plików z rozszerzeniem zip skrypt języka R](./media/r-quickstart/fig6.png)

*Rysunek 5. Eksperymentu przy użyciu skryptu języka R zip.*

#### <a name="dataset1"></a>Dataset1

Tabelę prostokątnym zestawem danych można przekazać do kodu języka R, za pomocą Dataset1 danych wejściowych. W naszym prosty skrypt `maml.mapInputPort(1)` funkcja odczytuje dane z portu 1. Tych danych jest przypisywany do elementów dataframe nazwy zmiennej w kodzie. W naszym prosty skrypt pierwszy wiersz kodu wykonuje przypisania.

```R
cadairydata <- maml.mapInputPort(1)
```

Wykonaj eksperymentu, wybierając **Uruchom** przycisku. Po zakończeniu wykonywania, wybierz [wykonanie skryptu języka R] [ execute-r-script] modułu, a następnie wybierz **danych wyjściowych w dzienniku** w okienku właściwości. Nowa strona powinna zostać wyświetlona w przeglądarce, wyświetlanie zawartość pliku dane_wyjściowe.log. Gdy przewiń w dół powinien zostać wyświetlony podobny do poniższego.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Oddalone niżej na tej stronie jest bardziej szczegółowe informacje dotyczące kolumn, które będą wyglądać następująco.

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

Te wyniki są w przeważającej części, zgodnie z oczekiwaniami, obserwacje 228 i 9 kolumn w ramki danych. Możemy zobaczyć nazwy kolumn, typ danych języka R i przykładowym każdej kolumny.

> [!NOTE]
> To ten sam wynik w drukowanej wygodnie znajduje się z danych wyjściowych urządzenia R [wykonanie skryptu języka R] [ execute-r-script] modułu. Omówimy dane wyjściowe [wykonanie skryptu języka R] [ execute-r-script] modułu w następnej sekcji.  

#### <a name="dataset2"></a>Dataset2

Zachowanie danych wejściowych Dataset2 jest taka sama jak w przypadku Dataset1. Przy użyciu tych danych wejściowych można przekazać drugą tabelę prostokątnym zestawem danych w kodzie R. Funkcja `maml.mapInputPort(2)`, z argumentem 2 jest używany do przekazywania tych danych.  

### <a name="execute-r-script-outputs"></a>Wykonywanie skryptu języka R w danych wyjściowych

#### <a name="output-a-dataframe"></a>Dane wyjściowe ramkę danych

Można wyprowadzić zawartość ramki danych języka R jako tabelę prostokątny za pośrednictwem portu Dataset1 wynik za pomocą `maml.mapOutputPort()` funkcji. W naszym prosty skrypt języka R to jest wykonywane przez następujący wiersz.

```
maml.mapOutputPort('cadairydata')
```

Po uruchomieniu eksperymentu, wybierz port wyjściowy Dataset1 wyników, a następnie wybierz **Visualize**. Powinien zostać wyświetlony podobny rysunek 6.

![Wizualizacja danych wyjściowych danych mleka Kalifornia](./media/r-quickstart/fig7.png)

*Rysunek 6. Wizualizacja danych wyjściowych danych mleka Kalifornia.*

Te dane wyjściowe wyglądają identyczne w danych wejściowych, dokładnie tak, jak oczekiwano.  

### <a name="r-device-output"></a>Dane wyjściowe urządzenia R

Dane wyjściowe urządzenia [wykonanie skryptu języka R] [ execute-r-script] moduł zawiera dane wyjściowe komunikaty i grafiki. Standardowe Wyjście i standardowy błąd komunikaty z języka R są wysyłane do portu wyjściowego R urządzenia.  

Aby wyświetlić dane wyjściowe urządzenia języka R, wybierz port, a następnie na **Visualize**. Widzimy wyjście standardowe i błąd standardowy w skrypcie języka R na rysunku 7.

![Wyjście standardowe i błąd standardowy z portu urządzenia R](./media/r-quickstart/fig8.png)

*Rysunek 7. Wyjście standardowe i błąd standardowy z portu urządzenia R.*

Przewijając w dół możemy wyświetlić dane wyjściowe grafiki z naszego skryptu języka R na rysunku 8.  

![Graficzne dane wyjściowe port urządzenia R](./media/r-quickstart/fig9.png)

*Rysunek 8. Graficzne dane wyjściowe z portu urządzenia R.*  

## <a id="filtering"></a>Filtrowanie danych i transformacji

W tej sekcji możemy wykonać niektóre podstawowe dane, filtrowanie i operacjami transformacji danych mleka Kalifornia. Na koniec w tej sekcji zostanie mamy dane w formacie odpowiednim do tworzenia modeli analitycznych.  

W szczególności w tej sekcji kolejności wykonamy kilka typowych danych czyszczenia i transformacji zadań: Typ przekształcenia, filtrowanie elementy dataframe, dodając nowe kolumny obliczane, a wartość przekształcenia. To tło powinny pomóc w obsługi wielu odmian w rzeczywistych problemów.

Kompletny kod języka R w tej sekcji jest dostępna w [MachineLearningSamples-notesów/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Typ przekształcenia

Teraz, gdy firma Microsoft może odczytywać dane mleka Kalifornia do kodu języka R w [wykonanie skryptu języka R] [ execute-r-script] modułu, należy upewnić się, że dane w kolumnach ma zamierzony typ i format.  

R jest dynamicznie typizowanym językiem, co oznacza, że typy danych są zmuszone z jednego do drugiego zgodnie z potrzebami. Typy danych atomic w języku R obejmują numeryczne, logiczne i znaków. Typ usługi jest używany do bardziej kompaktowy przechowywać dane podzielone na kategorie. Znacznie więcej informacji na temat typów danych można znaleźć w odwołaniach w [dalsze informacje](#appendixb) poniżej.

Podczas odczytywania danych tabelarycznych w R ze źródła zewnętrznego, zawsze jest dobrze Sprawdź typy wynikowy w kolumnach. Może być kolumną znaku typu, ale w wielu przypadkach to pojawią się jako składnik lub na odwrót. W innych przypadkach kolumny, które Twoim zdaniem powinny być liczbowych jest reprezentowany przez znak danych, np. "1,23", a nie 1,23 jako zmiennoprzecinkowy numer punktu.  

Na szczęście jest łatwe do konwertowania jednego typu na inny, tak długo, jak mapowanie jest możliwe. Na przykład "Nevada" nie można przekonwertować na wartość liczbową, ale można przekonwertować Authentication (zmienna kategorii). Inny przykład 1 liczbowe można przekonwertować na znak "1" lub czynnika.  

Składnia dla każdej takiej konwersji jest prosty: `as.datatype()`. Te funkcje konwersji typu, m.in.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Spojrzenie na typy danych kolumn, firma Microsoft dane wejściowe w poprzedniej sekcji: wszystkie kolumny są typu liczbowego, z wyjątkiem kolumnę z etykietą "Miesiąc", czyli znaku typu. Możemy przekonwertować to czynnik i wyniki testu.  

Usunięto wiersz macierzy wykres punktowy i dodawane linię współczynnik Konwertowanie kolumny "Miesiąc". W moim eksperymencie I będzie po prostu Wytnij i Wklej kod języka R do okna kodu [wykonanie skryptu języka R] [ execute-r-script] modułu. Można również zaktualizować pliku zip i przekaż go do usługi Azure Machine Learning Studio, ale trwa to kilka kroków.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('cadairydata')
```

Umożliwia wykonanie tego kodu i poszukaj w dzienniku danych wyjściowych skryptu języka R. Na rysunku nr 9 przedstawiono odpowiednie dane z dziennika.

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

*Rysunek 9. Podsumowanie ramkę danych za pomocą zmiennej współczynnik.*

Typ w miesiącu, teraz powinna być widoczna nazwa "**współczynnik z 14 poziomów w**". Jest to problem, ponieważ istnieją tylko przez 12 miesięcy w roku. Możesz również sprawdzić, czy typ w **Visualize** zestawu wyników jest port "**podzielone**".

Problem polega na tym, czy kolumny "Miesiąc" nie został skonfigurował systematycznie. W niektórych przypadkach miesiąca nazywa kwietnia, a w innych jest skracana jako kwi. Aby rozwiązać ten problem, firma Microsoft przycinania ciągu do 3 znaków. W wierszu kodu wygląda teraz następująco:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Ponowne uruchomienie eksperymentu oraz przeglądanie dziennika danych wyjściowych. Oczekiwane wyniki są wyświetlane na rysunku nr 10.  

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

*Na rysunku nr 10. Podsumowanie ramki danych z poprawną liczbę poziomów usługi.*

Nasza zmienna współczynnik ma teraz odpowiednich poziomach 12.

### <a name="basic-data-frame-filtering"></a>Filtrowanie ramki danych podstawowych

Elementy dataframe R obsługuje zaawansowane funkcje filtrowania. Zestawy danych mogą być podzbiory przy użyciu filtrów logicznych według wierszy lub kolumn. W wielu przypadkach będzie wymagana kryteria filtrowania złożonych. Odwołania w [dalsze informacje](#appendixb) poniżej zawierają przykłady rozbudowane elementy dataframe filtrowania.  

Istnieje jeden bit filtrowania, należy wykonać w naszym zestawie danych. Jeśli przyjrzymy się kolumn w cadairydata dataframe, zobaczysz dwa zbędnych kolumn. Pierwsza kolumna zawiera tylko numer wiersza, który nie jest bardzo przydatne. Druga kolumna Year.Month, zawiera nadmiarowe informacje. Firma Microsoft jest łatwo wykluczyć te kolumny przy użyciu następującego kodu języka R.

> [!NOTE]
> Od teraz w tej sekcji właśnie pokażę dodatkowy kod dodaję w [wykonanie skryptu języka R] [ execute-r-script] modułu. Dodam każdego nowego wiersza **przed** `str()` funkcji. Aby zweryfikować Moje wyniki w usłudze Azure Machine Learning Studio I użyć tej funkcji.

Czy mogę dodać następujący wiersz do mojego kodu języka R w [wykonanie skryptu języka R] [ execute-r-script] modułu.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Uruchom ten kod w eksperymencie i sprawdź wynik z dziennika danych wyjściowych. Te wyniki są wyświetlane w rysunek 11.

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

*Rysunek 11. Podsumowanie ramkę danych zawierającą dwie kolumny usunięte.*

Dobra wiadomość! Uzyskujemy oczekiwanych wyników.

### <a name="add-a-new-column"></a>Dodaj nową kolumnę

Do tworzenia modeli szeregów czasowych będzie wygodnie ma kolumnę zawierającą miesięcy od momentu rozpoczęcia szeregów czasowych. Utworzymy nową kolumnę "Month.Count".

Aby ułatwić organizowanie kodu, utworzymy naszej pierwszej prostej funkcji `num.month()`. Firma Microsoft zastosuje następnie tę funkcję, aby utworzyć nową kolumnę w ramki danych. Nowy kod jest w następujący sposób.

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

Teraz uruchom eksperyment zaktualizowane i korzystanie z dziennika danych wyjściowych, aby wyświetlić wyniki. Te wyniki są wyświetlane w rysunek 12.

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

*Rysunek 12. Podsumowanie ramki danych z dodatkową kolumnę.*

Wygląda na to, że wszystko działa. Mamy nową kolumnę z oczekiwanych wartości w naszej ramki danych.

### <a name="value-transformations"></a>Wartość przekształcenia

W tej sekcji kolejności wykonamy pewne proste transformacje na podstawie wartości w niektórych kolumn naszych ramki danych. Język R obsługuje przekształcenia niemal dowolną wartość. Odwołania w [dalsze informacje](#appendixb) poniżej zawierają przykłady rozbudowane.

Jeśli przyjrzymy się wartości podsumowania naszych dataframe powinna zostać wyświetlona zawartość nieparzyste w tym miejscu. Czy więcej lodów niż mleka jest generowany w Kalifornii? Nie, oczywiście, jak to ma sensu sad jako ten fakt może być niektóre z nami lovers ice cream. Jednostki są różne. Cena jest w jednostkach nam funtach, mleka jest w jednostkach 1 mln funtach Stanów Zjednoczonych, ice cream znajduje się w jednostkach 1000 nam galonach i ser chałupniczej znajduje się w jednostkach 1000 funtach Stanów Zjednoczonych. Przy założeniu, że ice cream zadowalająco uwzględni wagi około 6,5 funtach na galon paliwa, można łatwo robimy mnożenia, aby przekonwertować te wartości, dzięki czemu można je wszystkie w taki sam jednostkach funtach 1000.

Dla modelu prognozowania używamy modelu mnożenia trendów i sezonowym dostosowania tych danych. Przekształcenie dziennika pozwala nam korzystać modelu liniowego, co upraszcza ten proces. Można zastosować przekształcenie dziennika w tej samej funkcji, w której stosowana jest mnożnik.

W poniższym kodzie zdefiniować nową funkcję `log.transform()`i zastosować je do wierszy zawierających wartości liczbowe. R `Map()` funkcja służy do stosowania `log.transform()` funkcji do wybranych kolumn ramki danych. `Map()` jest podobny do `apply()` , ale pozwala na więcej niż jednej liście argumentów funkcji. Należy pamiętać, że lista mnożników dostarcza drugiego argumentu `log.transform()` funkcji. `na.omit()` Funkcja jest używana jako bitu oczyszczania aby upewnić się, nie mamy brakujące lub niezdefiniowane wartości w ramki danych.

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

Występuje dość występuje bit w `log.transform()` funkcji. Większość tego kodu jest sprawdzanie potencjalne problemy z argumentami lub obsłudze wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy kodu w rzeczywistości korzystają obliczeń.

Celem obrony programowania jest zapobiec awarii jednej funkcji, który uniemożliwia przetwarzania za kontynuację. Wystąpił błąd nagłego analizy długotrwałych może być dość irytujące dla użytkowników. Aby uniknąć tej sytuacji, można wybrać wartości zwracane domyślnego, który ograniczy szkody przetwarzania transmisji dla klientów. Komunikat jest także generowany ostrzegać użytkowników, że Niestety, wystąpił problem.

Jeśli nie są używane do obrony programowania w języku R, ten kod może wydawać się nieco trudne. Czy mogę przeprowadzi Cię przez najważniejsze czynności:

1. Wektor czterech wiadomości jest zdefiniowana. Te komunikaty są używane do przekazywania informacji o możliwych błędów i wyjątków, które mogą wystąpić przy użyciu tego kodu.
2. Czy mogę zwrócić wartość MS.REVIEWER w każdym przypadku. Istnieje wiele innych możliwości, które może być mniej efekty uboczne. Czy mogę może zwrócić wektor wartości zerowe lub oryginalnego wektora danych wejściowych, na przykład.
3. Testy są uruchamiane na argumenty do funkcji. W każdym przypadku, jeśli zostanie wykryty błąd, jest zwracana wartość domyślna, a komunikat jest generowany przez `warning()` funkcji. Używam `warning()` zamiast `stop()` zgodnie z jego spowoduje przerwanie działania wykonywania, dokładnie co próbuję uniknąć. Należy pamiętać, że pismo ten kod w stylu procedurach, tak jak w tym przypadku podejście funkcjonalności sprawiał złożone i zasłoniętej.
4. Obliczenia dziennika są opakowane w `tryCatch()` tak, aby wyjątki nie spowoduje zatrzymania nagłego przetwarzania. Bez `tryCatch()` większość błędów zgłaszanych przez wyniku funkcji języka R w sygnał zatrzymania, który właśnie to robi.

Wykonanie tego kodu języka R w eksperymencie i się jej przyjrzeć wydruku w pliku dane_wyjściowe.log. Zostanie wyświetlona po przekształceniu wartości czterech kolumn w dzienniku, jak pokazano na rysunku 13.

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

*Rysunek 13. Podsumowanie wartości przekształcone w ramki danych.*

Widzimy, że wartości zostały przekształcone. Teraz produkcji mleka przekracza znacznie wszystkich innych produkcji mleka produktu, odwołując się, że teraz czekamy na skali logarytmicznej.

W tym momencie nasze dane jest wyczyszczone, a firma Microsoft będzie gotowa do niektórych modelowania. Spojrzenie na wizualizacji podsumowania dla produktu wyjściowego zestawu danych w wyniku z naszych [wykonanie skryptu języka R] [ execute-r-script] modułu, zobaczysz, "Miesiąc" kolumna jest "Podzielone" wystąpienia o unikatowych wartościach 12, ponownie tak samo jak Chcieliśmy.

## <a id="timeseries"></a>Obiekty serii czasu i analiza korelacji

W tej sekcji Rozpoczniemy zapoznaj się z kilku podstawowych obiektów serii czasu R i analizowanie korelacji między niektórych zmiennych. Naszym celem jest zawierający informacje o korelacji parowania w kilku spowolnienia ramkę danych wyjściowych.

Kompletny kod języka R w tej sekcji znajduje się w [MachineLearningSamples-notesów/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Czas serii obiektów w języku R

Jak już wspomniano, czas serii to zbiór wartości danych indeksowane według czasu. Obiekty serii czasu języka R są używane do tworzenia i zarządzania nimi indeks czasu. Ma kilka zalet przy użyciu obiektów serii czasu. Obiekty serii czasu pozbawione wiele szczegółów zarządzania wartości indeksu serii czasu, które są hermetyzowane w obiekcie. Ponadto czas serii obiektów umożliwiają używanie wiele metod serii czasu do kreślenia, drukowanie, modelowanie itp.

Klasa serii czasu POSIXct jest często używane i jest stosunkowo prosta. Ta szeregów czasowych klasy miar czasu od początku epoki, 1 stycznia 1970. W tym przykładzie będziemy używać POSIXct czas serii obiektów. Inne powszechnie używanych klas obiektów serii czasu R obejmują zoo i xts, szeregów czasowych rozszerzonego.

### <a name="time-series-object-example"></a>Przykład obiektu serii czasu

Zacznijmy od naszego przykładu. Przeciąganie i upuszczanie **nowe** [wykonanie skryptu języka R] [ execute-r-script] modułu do eksperymentu. Połącz port wyjściowy wyniku Dataset1 istniejącej [wykonanie skryptu języka R] [ execute-r-script] nowy portem wejściowym modułu moduł Dataset1 [wykonanie skryptu języka R] [ execute-r-script] modułu.

Tak jak dla pierwszych przykładów miarę postępów za pomocą przykładu, w niektórych punktach pokażę tylko przyrostowe dodatkowe wiersze kodu języka R w każdym kroku.  

#### <a name="reading-the-dataframe"></a>Odczytywanie ramki danych

Pierwszym krokiem teraz przeczytać ramkę danych i upewnij się, że uzyskujemy oczekiwanych wyników. Poniższy kod, należy wykonać zadanie.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Teraz uruchom eksperyment. Dziennik nowy kształt wykonanie skryptu języka R powinien wyglądać jak rysunek 14.

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

*Rysunek 14. Podsumowanie ramkę danych w module wykonywania skryptu języka R.*

Te dane są oczekiwanych typów i format. Należy pamiętać, że kolumny "Miesiąc" jest typem wskaźnika i ma oczekiwanej liczby poziomów.

#### <a name="creating-a-time-series-object"></a>Tworzenie obiektów serii czasu

Musimy dodać obiekt serii czasu do naszych ramki danych. Zastąp bieżący kod poniższych składników, które dodaje nową kolumnę klasy POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teraz zajrzyj do dziennika. Powinien on wyglądać jak rysunek 15.

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

*Rysunek 15. Podsumowanie ramki danych z obiektów serii czasu.*

Możemy zobaczyć podsumowanie nowej kolumny, która jest w rzeczywistości klasy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Eksplorowanie i przekształcanie danych

Przyjrzyjmy się zmiennych w tym zestawie danych. Wykres punktowy macierzy jest dobrym sposobem na krótkie omówienie tworzenia. Czy mogę mnie zastępowania `str()` funkcji w poprzednim kodzie R o następujący wiersz.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Uruchom ten kod i zobacz, co się dzieje. Wykres przedstawiony na port urządzenia R powinien wyglądać jak rysunek 16.

![Wykres punktowy macierz wybrane zmienne](./media/r-quickstart/fig17.png)

*Rysunek 16. Wykres punktowy macierzy wybranych zmiennych.*

Relacje między tymi zmiennymi jest odd-looking niektórych elementów struktury. Być może się tak zdarzyć z trendy w danych i z faktu, że firma Microsoft nie ustalić jako zmienne.

### <a name="correlation-analysis"></a>Analiza korelacji

Aby przeprowadzić analizę korelacji musimy cofnąć trendów i standaryzacji zmienne. Firma Microsoft może po prostu użyć języka R `scale()` funkcji, która skaluje się zmienne i centrów. Ta funkcja może być również wykonywane szybciej. Jednak warto Pokaż przykład obrony programistyczne w języku R.

`ts.detrend()` Funkcja poniżej wykonuje oba te operacje. Następujące dwa wiersze kodu cofnąć trendów danych, a następnie standaryzacji wartości.

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

Występuje dość występuje bit w `ts.detrend()` funkcji. Większość tego kodu jest sprawdzanie potencjalne problemy z argumentami lub obsłudze wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy kodu w rzeczywistości korzystają obliczeń.

Omówiliśmy już przykładem obrony programowania w przekształceniach wartość. Bloki zarówno obliczeń są opakowane w `tryCatch()`. Niektóre błędy dobrym pomysłem będzie zwracać oryginalnego wektora danych wejściowych, a w innych przypadkach wrócić wektor zer.  

Należy pamiętać, że regresji liniowej używane cofnąć umożliwia analizę trendów regresji serii czasu. Zmienna predykcyjne jest obiekt serii czasu.  

Raz `ts.detrend()` zdefiniowano możemy zastosować do zmiennych zainteresowanie naszym ramki danych. Firma Microsoft musi wymusić wynikowej listy utworzone przez `lapply()` do dataframe danych przy użyciu `as.data.frame()`. Ze względu na obrony aspektów `ts.detrend()`, niepowodzenie procesu, jedna ze zmiennych nie uniemożliwia poprawne przetwarzanie pozostałych.  

Ostatni wiersz kodu tworzy parowania wykres punktowy. Po uruchomieniu kodu języka R, wyniki wykres punktowy są wyświetlane w rysunek 17.

![Pairwise wykres punktowy z szeregu czasowego cofnąć trend i standardowy](./media/r-quickstart/fig18.png)

*Ilustracja 17. Pairwise wykres punktowy z szeregu czasowego cofnąć trend i standardowy.*

Możesz porównać te wyniki tymi, które przedstawiono na rysunku 16. Trend usunięte i zmienne, standaryzowane widzimy znacznie mniej struktury w relacji między tymi zmiennymi.

Kod, aby obliczyć korelacji jako obiekty KKS języka R jest w następujący sposób.

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

Uruchomienie tego kodu tworzy dziennik pokazano na rysunku 18.

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

*Ilustracja 18. Lista KKS obiektów z analizy parowania korelacji.*

Istnieje korelacja wartość dla każdego opóźnienie. Żadna z tych wartości korelacji są wystarczająco duży, aby być znaczące. Firma Microsoft w związku z tym stwierdzi, że firma Microsoft niezależnie modelu każdej zmiennej.

### <a name="output-a-dataframe"></a>Dane wyjściowe ramkę danych
Firma Microsoft ma obliczona parowania korelacji jako listę obiektów KKS R. Przedstawia znacznej liczby problem, jako port wyjściowy zestawu danych w wyniku naprawdę wymaga ramkę danych. Ponadto obiektu KKS jest lista i chcemy, aby tylko wartości w pierwszym elemencie listy, korelacji w różnych spowolnienia.

Poniższy kod wyodrębnia wartości lag z listy KKS obiektów, które są także listy.

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
## The following line works only in Azure Machine Learning Studio
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Pierwszy wiersz kodu jest nieco trudne i opisy mogą pomóc Ci zrozumieć jej. Praca ze środka na zewnątrz mamy dysponować następującymi elementami:

1. "**[[**"Operator z argumentem"**1**" wybiera wektor korelacji na spowolnienia od pierwszego elementu listy obiektów KKS.
2. `do.call()` Funkcja ma zastosowanie `rbind()` nad elementami listy gettotalsize() zwróciło przez `lapply()`.
3. `data.frame()` Funkcja wymusza traktowanie wyniku zwracany przez `do.call()` do ramkę danych.

Należy pamiętać, że wiersz nazwy znajdują się w kolumnie ramki danych. Ten wiersz tak zachowuje nazw, gdy są danymi wyjściowymi przekazywanymi z [wykonanie skryptu języka R][execute-r-script].

Uruchamiając kod generuje dane wyjściowe, pokazano na rysunku 19 po I **Visualize** dane wyjściowe w porcie wynik zestawu danych. W pierwszej kolumnie są nazwy wierszy, zgodnie z oczekiwaniami.

![Dane wyjściowe wyniki analizy korelacji](./media/r-quickstart/fig20.png)

*Ilustracja 19. Dane wyjściowe z analizy korelacji wyników.*

## <a id="seasonalforecasting"></a>Przykład serii czasu: prognozowanie sezonowych

Nasze dane, jest teraz w postaci odpowiednie do analizy i Ustaliliśmy, że nie ma żadnych znaczących korelacji między zmienne. Przejdźmy dalej i tworzenie modelu prognozowania szeregów czasowych. Przy użyciu tego modelu firma Microsoft będzie prognozy produkcji mleka Kalifornia na okres 12 miesięcy 2013.

Nasz model prognozowania ma dwa składniki, składnik trend i sezonowym składnika. Pełne Prognoza jest wynikiem te dwa składniki. Ten typ modelu jest znany jako model mnożenia. Alternatywą jest model dodatku. Przekształcenie dziennika zostały zastosowane już do zmiennych zainteresowania, co sprawia, że ta analiza tractable.

Kompletny kod języka R w tej sekcji znajduje się w [MachineLearningSamples-notesów/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Tworzenie elementów dataframe na potrzeby analizy

Rozpocznij od dodania **nowe** [wykonanie skryptu języka R] [ execute-r-script] modułu do eksperymentu. Połącz **zestaw wyników danych** istniejące dane wyjściowe [wykonanie skryptu języka R] [ execute-r-script] moduł **Dataset1** wprowadzania nowego modułu. Wynik powinien wyglądać jak rysunek 20.

![Doświadczenia z modułem wykonanie skryptu języka R dodane](./media/r-quickstart/fig21.png)

*Ilustracja 20. Doświadczenia z modułem wykonanie skryptu języka R dodane.*

Jako za pomocą analizy korelacji, który właśnie zakończoną, musimy dodać kolumnę z obiektem POSIXct serii czasu. Poniższy kod, będą po prostu to zrobić.

```R
# If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Uruchom ten kod i poszukaj w dzienniku. Wynik powinien wyglądać jak rysunek 21.

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

*Ilustracja 21. Podsumowanie ramki danych.*

Z tym wynikiem jesteśmy gotowi rozpocząć naszej analizy.

### <a name="create-a-training-dataset"></a>Utwórz zestaw danych szkoleniowych

Dzięki temu ramka danych skonstruowany, musimy utworzyć zestaw danych szkoleniowych. Tych danych będzie zawierać wszystkie uwagi z wyjątkiem ostatnich 12 roku 2013 r., czyli nasze zestawy danych testowych. Następujące kodu podzestawy ramki danych i tworzy wykresy mleka zmienne produkcyjne i ceny. Następnie tworzyć wykresy cztery produkcji i cena zmiennych. Funkcja anonimowa służy do definiowania niektórych rozszerzają dla powierzchni i następnie iteracji po liście dwa argumenty z `Map()`. Jeśli myślisz, że dla pętli będzie mieć działały prawidłowo w tym miejscu, są poprawne. Jednak ponieważ R jest językiem funkcjonalnym I jestem przedstawiający funkcjonalne podejście.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Uruchamiając kod tworzy serię serii, które z danych wyjściowych urządzenia R pokazano na rysunku 22 godziny. Należy pamiętać, że oś czasu w jednostkach dat, nieuprzywilejowany zaletą czas serii wykresu metody.

![Pierwszy wykresów serii czasu danych Kalifornia mleka produkcji i ceny](./media/r-quickstart/unnamed-chunk-161.png)

![Drugi wykresów serii czasu danych Kalifornia mleka produkcji i ceny](./media/r-quickstart/unnamed-chunk-162.png)

![Innych wykresów serii czasu danych Kalifornia mleka produkcji i ceny](./media/r-quickstart/unnamed-chunk-163.png)

![Czwarty wykresów serii czasu danych Kalifornia mleka produkcji i ceny](./media/r-quickstart/unnamed-chunk-164.png)

*Ilustracja 22. Wykresy serii czasu Kalifornia produkcji mleka oraz ceny danych.*

### <a name="a-trend-model"></a>Wzór trendu

Jeśli utworzono obiekt serii czasu i miała przyjrzeć się danym, Zacznijmy do konstruowania wzór trendu danych produkcji mleka Kalifornia. Możemy to zrobić za pomocą regresji serii czasu. To jasne z wykresu, który firma Microsoft potrzebujesz więcej niż wartość zerowa i przechwytywać dokładniej modelować obserwowanych trendów w danych szkoleniowych.

Biorąc pod uwagę małą skalę, danych, czy będzie Budowanie modelu potrzeby trend RStudio i następnie wyciąć i wkleić modelu wynikowego do usługi Azure Machine Learning Studio. Program RStudio zapewnia interaktywne środowisko dla tego typu tworzenia interaktywnych analiz.

Jako pierwsza próba próbuję wielomianowej regresji przy użyciu uprawnień nie więcej niż 3. Istnieje zagrożenie rzeczywistych nadmiernie dopasowanie tych rodzajów modeli. W związku z tym najlepiej uniknąć warunków najwyższego rzędu. `I()` Funkcja powstrzymuje interpretacji zawartości (interpretuje zawartość "as is") i umożliwia pisanie funkcji interpretowany dosłownie w równaniu regresji.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Spowoduje to wygenerowanie poniżej.

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

Z wartości P (`Pr(>|t|)`) w poniższych danych wyjściowych widać, że kwadratów termin nie mogą być istotne. Będę używać `update()` funkcji, aby zmodyfikować ten model przez upuszczenie termin kwadratów.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Spowoduje to wygenerowanie poniżej.

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

To wygląda lepiej. Wszystkie warunki są znaczące. Jednak wartość 2e 16 jest wartość domyślna, a nie powinny być uwzględniane w zbyt poważnie.  

Jako test poprawnością stwórzmy kreślenia serii czasu danych produkcji mleka Kalifornia z krzywej trend pokazano. Po dodaniu następujący kod w usłudze Azure Machine Learning Studio [wykonanie skryptu języka R] [ execute-r-script] modelu (nie RStudio), utworzyć model i wykres. Wynik jest wyświetlany w ilustracja 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kalifornia mleka produkcji danych za pomocą modelu trend pokazano](./media/r-quickstart/unnamed-chunk-18.png)

*Ilustracja 23. Kalifornia mleka danych produkcyjnych przy użyciu modelu trend wyświetlane.*

Wygląda na to, dość dobrze dopasowania modelu trendów danych. Dodatkowo istnieje wydaje się być dowód nadmiernego dopasowywania, takich jak nieparzysta wierci się krzywej modelu.  

### <a name="seasonal-model"></a>Sezonowych modelu

Dzięki modelowi trend w kasie potrzebujemy zawiera efekty sezonowych i wypychania. Użyjemy miesiąca roku jako zmienną fikcyjnego w modelu liniowego do przechwytywania efekt kolejnych miesięcy. Należy pamiętać, że po wprowadzeniu współczynnik zmienne do modelu punkt przecięcia musi nie zostać obliczony. Jeśli to nie to formuła jest nadmiernie określony i języka R zostanie Porzuć jedną odpowiednią czynników, ale pozostaw termin intercept.

Ponieważ mamy modelu zadowalające trend możemy użyć `update()` funkcję, aby dodać nowe warunki do istniejącego modelu. -1 w formule aktualizacji porzuca termin intercept. Kontynuując w RStudio w chwili obecnej:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Spowoduje to wygenerowanie poniżej.

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

Widzimy, że model nie jest już ma termin intercept i ma 12 miesiąca istotne czynniki. Jest to dokładnie, co chcemy wyświetlić.

Upewnijmy się innym kreślenia serii czasu danych produkcji mleka Kalifornia aby zobaczyć, jak dobrze działa sezonowych modelu. Po dodaniu następujący kod w usłudze Azure Machine Learning Studio [wykonanie skryptu języka R] [ execute-r-script] utworzyć model i wykres.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Uruchomienie tego kodu w usłudze Azure Machine Learning Studio tworzy wykres pokazano na rysunku 24.

![Kalifornia produkcji mleka przy użyciu modelu, w tym sezonowych wpływ](./media/r-quickstart/unnamed-chunk-20.png)

*Rysunek 24. Kalifornia produkcji mleka przy użyciu modelu, w tym sezonowych wpływ.*

Dopasuj do danych wyświetlanych w rysunek 24 jest raczej wspieranie. Trend i sezonowy wpływ (odmiany miesięczne) wyglądać uzasadnione.

Jako innego wyboru na nasz model Przyjrzyjmy się przyjrzeć się reszty. Poniższy kod oblicza przewidywane wartości z naszych dwóch modeli, oblicza reszty sezonowych modelu i pozostałości te danych szkoleniowych, które następnie.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Pozostałe wykres jest wyświetlany w rysunek 25.

![Reszty sezonowych modelu danych szkoleniowych](./media/r-quickstart/unnamed-chunk-21.png)

*Ilustracja 25. Reszty sezonowych modelu danych szkoleniowych.*

Pozostałości te wyglądają uzasadnione. Nie określonej struktury, z wyjątkiem efekt recesja 2008 2009, który nie uwzględniać nasz model jest szczególnie dobrze.

Wykres pokazano na rysunku 25 przydaje się do wykrywania wzorców wszelkie zależne od czasu w reszty. Jawne podejście obliczeń i rysowanie reszty, czego użyłem umieszcza reszty w porządku czasowym na wykres. Jeśli z drugiej strony, czy miał wykreślić `milk.lm$residuals`, nie byłby wykreślania w porządku czasowym.

Można również użyć `plot.lm()` do produkcji serii wykresów diagnostycznych.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ten kod tworzy serii wykresów diagnostycznych pokazano na rysunku 26.

![Pierwszego dnia diagnostycznych powierzchni sezonowych modelu](./media/r-quickstart/unnamed-chunk-221.png)

![Drugi diagnostycznych powierzchni sezonowych modelu](./media/r-quickstart/unnamed-chunk-222.png)

![Trzecia diagnostycznych powierzchni sezonowych modelu](./media/r-quickstart/unnamed-chunk-223.png)

![Czwarty diagnostycznych powierzchni sezonowych modelu](./media/r-quickstart/unnamed-chunk-224.png)

*Ilustracja 26. Diagnostyczne, które sezonowych modelu.*

Kilka punktów wysoce wpływowi zidentyfikowanego w tych powierzchni, ale nie ma niczego do spowodować, że nas bardzo ważne jest. Ponadto firma Microsoft można zobaczyć wykres normalny Q-Q czy najbliżej zwykle jest to rozproszone, ważne założenia modeli liniowych reszty.

### <a name="forecasting-and-model-evaluation"></a>Ocena prognozowania i modeli

Ma tylko jedną rzecz do ukończenia przykładu. Musimy obliczenia prognoz i pomiar błąd względem rzeczywistych danych. Nasze prognozy będzie na okres 12 miesięcy 2013. Firma Microsoft jest obliczeniowych miary błąd prognozy na rzeczywistych danych, który nie jest częścią naszego zestawu danych szkoleniowych. Ponadto możemy porównać wydajność 18 lat danych szkoleniowych przez 12 miesięcy danych testowych.  

Różne metryki są używane do mierzenia wydajności modeli szeregów czasowych. W tym przypadku użyjemy błąd głównego średniej kwadratowej (RMS). Poniższa funkcja oblicza błędu RMS między dwóch serii.  

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

Podobnie jak w przypadku `log.transform()` funkcji, które Omówiliśmy w sekcji "Wartość przekształcenia" jest bardzo dużo kod błędu sprawdzania i wyjątków odzyskiwania w tej funkcji. Zasady stosowane są takie same. Praca odbywa się w dwóch miejscach w `tryCatch()`. Po pierwsze szeregów czasowych są exponentiated, ponieważ pracujemy w dziennikach wartości. Po drugie jest obliczana błędu usługi RMS.  

Teraz wyposażony w funkcję do pomiaru błąd usługi RMS, twórz i danych wyjściowych ramkę danych zawierającą błędy usługi RMS. Firma Microsoft będzie zawierać postanowienia dotyczące modelu trend samodzielnie i pełnego modelu współczynników sezonowym. Poniższy kod wykonuje zadania przy użyciu dwa modele liniowy, który możemy zostały zbudowane.

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
## Azure Machine Learning Studio
maml.mapOutputPort('RMS.df')
```

Uruchomienie tego kodu tworzy wyświetlanego w rysunek 27 na port wyjściowy zestawu danych w wyniku.

![Porównanie błędów usługi RMS dla modeli](./media/r-quickstart/fig26.png)

*Ilustracja 27. Porównanie błędów usługi RMS dla modeli.*

Z tymi wynikami widzimy, że dodawanie sezonowych czynników do modelu zmniejsza błędu RMS znacznie. Nie za zaskakująco błąd usługi RMS, aby uzyskać dane szkoleniowe jest nieco mniej niż prognozy.

## <a id="appendixa"></a>Przewodnik po dokumentacji programu RStudio

Program RStudio jest dość dobrze udokumentowane. Poniżej wymieniono niektóre kluczowe części dokumentacji programu RStudio ułatwią Ci rozpoczęcie pracy.

* **Tworzenie projektów wykorzystujących interfejs** — można organizować i zarządzać kodem R do projektów za pomocą programu RStudio. Zobacz [projektów przy użyciu](https://support.rstudio.com/hc/articles/200526207-Using-Projects) Aby uzyskać szczegółowe informacje. Zalecam, wykonaj następujące czynności, a następnie utwórz projekt dla przykładów kodu języka R, w tym artykule.  
* **Edytowanie i wykonywanie kodu języka R** -RStudio zapewnia zintegrowane środowisko do edytowania i wykonywania kodu języka R. Zobacz [edycji i wykonywania kodu](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) Aby uzyskać szczegółowe informacje.
* **Debugowanie** -RStudio obejmuje zaawansowane funkcje debugowania. Zobacz [debugowania przy użyciu programu RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) Aby uzyskać więcej informacji o tych funkcjach. Aby uzyskać informacji na temat rozwiązywania problemów z funkcji punktu przerwania, zobacz [Rozwiązywanie problemów z punktu przerwania](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a id="appendixb"></a>Dalsze informacje

Ten samouczek programowania R obejmuje podstawy, co jest potrzebne do języka R za pomocą usługi Azure Machine Learning Studio. Jeśli nie jesteś zaznajomiony z języka R, dwa wprowadzeń są dostępne w sieci CRAN:

* [R dla początkujących](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) Emmanuel Paradis jest dobrym miejscem do rozpoczęcia.  
* [Wprowadzenie do języka R](https://cran.r-project.org/doc/manuals/R-intro.html) przez W. N. Venables et. al. Przechodzi do bardziej szczegółowe informacje.

Istnieje wiele książek na R, które mogą pomóc Ci rozpocząć pracę. Poniżej przedstawiono kilka, które mogę być przydatne:

* **Grafikę programowania w języku R: Samouczek z oprogramowania statystycznie** to Normanowi Matloff znakomite wprowadzenie do programowania w języku R.  
* **Podręcznik języka R** , Paul Teetor dostarcza rozwiązanie problemu i rozwiązania do używania języka R.  
* **Język R w akcji** innej przydatne książki wprowadzające to Robert Kabacoff. Pomocnik [szybkie R witryny sieci Web](https://www.statmethods.net/) jest użytecznym zasobem.
* **R Inferno** Patrick Burns jest zdumiewająco humorystyczną książki, który zajmuje się liczba trudne i trudne tematów, które można napotkać podczas programowania w języku R. Książki jest dostępna bezpłatnie w [Inferno R](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Jeśli chcesz, aby szczegółowo omówi zaawansowanych tematów w języku R, się jej przyjrzeć książki **zaawansowane R** przez Hadley Wickham. Wersję tej książki online jest dostępna bezpłatnie w [ http://adv-r.had.co.nz/ ](http://adv-r.had.co.nz/).

Wykaz pakietów serii czasu języka R można znaleźć w [CRAN widok zadania: Czas analizy serii](https://cran.r-project.org/web/views/TimeSeries.html). Informacje w określonym czasie serii obiektu pakietów powinni zapoznać się z dokumentacją tego pakietu.

Książki **wprowadzające szeregów czasowych** przy użyciu języka R, Paul Cowpertwait i Andrew Metcalfe zawiera wprowadzenie do przy użyciu języka R do analizy serii czasowych. Wiele więcej teoretycznych teksty zawierają przykłady języka R.

Poniżej przedstawiono kilka przydatnych zasobów Internetu:

* DataCamp uczy języka R w przy użyciu przeglądarki z wideo — lekcje i ćwiczenia kodowania. Dostępne są interaktywne samouczki na temat najnowszych technik R i pakietów. Weź udział w BEZPŁATNEJ [interaktywnego samouczka języka R](https://www.datacamp.com/courses/introduction-to-r).
* [Dowiedz się programowania w języku R, ostateczne przewodnik](https://www.programiz.com/r-programming) z Programiz.
* Szybkie [samouczek języka R](https://www.cyclismo.org/tutorial/R/) przez czarny Kelly Clarkson University.
* Ponad 60 zasobów języka R, opisane w temacie [zasoby języka R górnej aby udoskonalić swoje umiejętności danych](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
