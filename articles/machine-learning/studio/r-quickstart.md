---
title: Samouczek Szybki Start dla języka R Machine Learning | Dokumentacja firmy Microsoft
description: Użyj tego samouczka programowania R, aby rozpocząć, szybko przy użyciu języka R w usłudze Azure Machine Learning Studio tworzenie rozwiązań prognozowania.
keywords: Szybki Start, języka r, r język programowania, samouczek programowania r
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 99a3a0fd-b359-481a-b236-66868deccd96
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 97107bb5ca1a598906cac9adbf508b2d15668e7d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227244"
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Samouczek szybkiego startu dotyczący języka programowania R dla usługi Azure Machine Learning

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Wprowadzenie
Ten samouczek Szybki Start pomaga szybko rozpocząć rozszerzanie uczenia maszynowego Azure przy użyciu języka programowania R. Postępuj zgodnie z tym samouczkiem programowania R, aby tworzenie, testowanie i wykonywanie kodu języka R w usłudze Azure Machine Learning. Podczas pracy z samouczkiem utworzysz kompletnego rozwiązania prognozowania przy użyciu języka R w usłudze Azure Machine Learning.  

Microsoft Azure Machine Learning zawiera wiele modułów wydajny komputer uczenie i danych manipulowanie. Zaawansowane język R został opisany jako franca język usługi analytics. Happily manipulowanie analizy i danych w usłudze Azure Machine Learning można rozszerzyć za pomocą R. Ta kombinacja zapewnia skalowalność i łatwość wdrażania usługi Azure Machine Learning elastyczność i głębokie analytics r.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="forecasting-and-the-dataset"></a>Funkcja prognozowania i zestawie danych
Funkcja prognozowania jest powszechnie pracowników i bardzo przydatne metody analitycznej. Wspólne używa zakresu z przewidywania sprzedaży elementów okresach Określanie optymalnego zapasów do przewidywania makroekonomicznej zmiennych. Funkcja prognozowania jest zazwyczaj wykonywane z modeli szeregów czasowych.

Czas serii danych jest danych, w której wartości mają indeks czasu. Indeks czasu może być regularnie, np. co miesiąc lub co minutę lub nieprawidłowo. Model szeregów czasowych jest na podstawie czasu serii danych. R język programowania zawiera elastyczną architekturę i szeroką gamę analiza czasu serii danych.

W tym przewodniku Szybki Start możemy zostanie praca z mleczarskie Kalifornijskiej i cenach danych. Te dane obejmują miesięczne informacji na temat produkcji kilka mleczarskich i ceny mleka fat zwykłych stawek.

Dane używane w tym artykule, wraz ze skryptami R, mogą być [pobrana w tym miejscu](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/blob/master/studio-samples/cadairydata.csv). Te dane został pierwotnie syntezatora z informacji dostępnych w uniwersytecie Wisconsin na https://dairymarkets.com.

### <a name="organization"></a>Organizacja
Firma Microsoft będzie postęp kilku kroków zdobędziesz sposobu tworzenia, testowania i wykonanie kodu manipulowania R analizy i danych w środowisku Azure Machine Learning.  

* Najpierw przeanalizujemy podstawy przy użyciu języka R w środowisku Azure Machine Learning Studio.
* Następnie możemy postępu dyskutować różnych aspektów we/wy dla danych, kodu języka R i grafiki w środowisku Azure Machine Learning.
* Firma Microsoft będzie skonstruować pierwsza część naszego prognozowania rozwiązania przez utworzenie kodu do czyszczenia danych i przekształcania.
* Z naszych danych przygotowane zostaną wykonane analizy korelacji między kilka zmiennych w naszym zestawie danych.
* Na koniec utworzymy model prognozowania szeregów czasowych okresach do produkcji mleka.

## <a id="mlstudio"></a>Interakcje z języka R w usłudze Machine Learning Studio
W tej sekcji przedstawia niektóre podstawowe informacje o interakcji z języka programowania R w środowisku usługi Machine Learning Studio. Język R udostępnia zaawansowane narzędzia do tworzenia dostosowanych analizy i moduły manipulacji danych w środowisku Azure Machine Learning.

Użyję programu RStudio na tworzenie, testowanie i debugowanie kodu języka R na małą skalę. Ten kod jest następnie wycinanie i wklejanie w [wykonanie skryptu języka R] [ execute-r-script] modułu w usłudze Machine Learning Studio gotowy do uruchomienia.  

### <a name="the-execute-r-script-module"></a>Moduł wykonywania skryptu języka R
W usłudze Machine Learning Studio, R skrypty są uruchamiane w ramach [wykonanie skryptu języka R] [ execute-r-script] modułu. Przykład [wykonanie skryptu języka R] [ execute-r-script] modułu w usłudze Machine Learning Studio jest pokazany na rysunku 1.

 ![R język programowania: modułu wykonania skryptu języka R wybrane w usłudze Machine Learning Studio][1]

*Rysunek 1. Środowiska usługi Machine Learning Studio przedstawiający wybrano modułu wykonania skryptu języka R.*

Odwołanie do rysunku 1, Przyjrzyjmy się niektóre z kluczowych części środowiska usługi Machine Learning Studio do pracy z [wykonanie skryptu języka R] [ execute-r-script] modułu.

* Moduły w eksperymencie są wyświetlane w środkowym okienku.
* Górna część okienku po prawej stronie zawiera okno, aby wyświetlić i edytować skrypty R.  
* Po prawej dolnej części przedstawiono niektóre właściwości [wykonanie skryptu języka R][execute-r-script]. Dzienniki błędów i dane wyjściowe można wyświetlić, klikając odpowiednie punkty tego okienka.

Firma Microsoft będzie oczywiście w niniejszym dokumencie [wykonanie skryptu języka R] [ execute-r-script] bardziej szczegółowo w pozostałej części tego dokumentu.

Podczas pracy z złożonych funkcji R, zalecamy edytować, testowania i debugowania w programu RStudio. Podobnie jak w przypadku dowolnego rozwoju oprogramowania przyrostowo rozszerzyć kodu i przetestować go na małych prostych przypadkach testowych. Następnie wycinanie i wklejanie funkcji do okna Skrypt R [wykonanie skryptu języka R] [ execute-r-script] modułu. Takie podejście umożliwia przewodów zarówno programu RStudio zintegrowane środowisko programistyczne (IDE) i możliwości usługi Azure Machine Learning.  

#### <a name="execute-r-code"></a>Wykonanie kodu języka R
Kod języka R w [wykonanie skryptu języka R] [ execute-r-script] modułu zostanie wykonany, gdy uruchomić eksperyment, klikając **Uruchom** przycisku. Po zakończeniu wykonywania znacznik wyboru pojawią się na [wykonanie skryptu języka R] [ execute-r-script] ikony.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Kodowanie obrony R dla usługi Azure Machine Learning
Jeśli tworzysz kodu R, na przykład usługi sieci web przy użyciu usługi Azure Machine Learning, należy ostatecznie zaplanować, jak kod zajmuje się nieoczekiwane dane wejściowe i wyjątki. Do zachowania przejrzystości, I nie zostały uwzględnione znacznie kategoriach sprawdzanie lub obsługi wyjątków w większości pokazano przykładowy kod. Jednak jak możemy kontynuować I zapewni kilka przykładów funkcji przy użyciu możliwości obsługi wyjątków, R.  

Jeśli potrzebujesz bardziej szczegółowy traktowanie Obsługa wyjątków języka R, najlepiej odczytać odpowiednie części podręcznika przez Wickham na liście [dodatek B — dalsze informacje](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Debugowania i testowania R w usłudze Machine Learning Studio
Aby przywołują, I zaleca się testowanie i debugowanie kodu języka R na małą skalę w programu RStudio. Istnieją jednak przypadki, w którym konieczne będzie wykrywać problemy kodu języka R w [wykonanie skryptu języka R] [ execute-r-script] samej siebie. Ponadto jest dobrym rozwiązaniem Sprawdź wyniki w usłudze Machine Learning Studio.

Dane wyjściowe wykonania kodu języka R i na platformie Azure Machine Learning znajduje się głównie w dane_wyjściowe.log. Dodatkowe informacje będą widoczne w error.log.  

W usłudze Machine Learning Studio występuje błąd podczas uruchamiania kodu języka R, powinno być przyjrzeć się error.log pierwszy przebiegu działań. Ten plik może zawierać przydatne komunikaty, aby ułatwić zrozumienie i poprawić ten błąd. Aby wyświetlić error.log, kliknij **dziennik błędów w widoku** na **w okienku właściwości** dla [wykonanie skryptu języka R] [ execute-r-script] zawierające błąd.

Na przykład został uruchomiony następujący kod R Niezdefiniowany y zmiennej w [wykonanie skryptu języka R] [ execute-r-script] modułu:

    x <- 1.0
    z <- x + y

Ten kod wykonanie nie powiedzie się, co powoduje wystąpienie stanu błędu. Kliknięcie **dziennik błędów w widoku** na **w okienku właściwości** powoduje wyświetlanie pokazany na rysunku 2.

  ![Komunikat o błędzie wyskakującego okienka][2]

*Rysunek 2. Komunikat o błędzie wyskakujących.*

Wygląda na to, musimy Szukaj w dane_wyjściowe.log, aby wyświetlić komunikat o błędzie R. Polecenie [wykonanie skryptu języka R] [ execute-r-script] , a następnie kliknij polecenie **wyświetlić dane_wyjściowe.log** elementu na **w okienku właściwości** z prawej strony. Otwiera nowe okno przeglądarki i są widoczne poniżej.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ten komunikat o błędzie zawiera nie niespodzianki i jasno identyfikuje problem.

Umożliwia sprawdzenie wartości wszystkich obiektów w R, możesz wydrukować te wartości w pliku dane_wyjściowe.log. Reguły badania wartości obiektów są zasadniczo takie same jak sesji interaktywnej R. Na przykład jeśli wpiszesz nazwę zmiennej w wierszu do pliku dane_wyjściowe.log będą wypisywane wartość obiektu.  

#### <a name="packages-in-machine-learning-studio"></a>Pakiety w usłudze Machine Learning Studio
Usługa Azure Machine Learning jest dostarczany z ponad 350 wstępnie zainstalowane pakiety języka R. Można użyć poniższego kodu w [wykonanie skryptu języka R] [ execute-r-script] modułu, aby pobrać listę wstępnie zainstalowane pakiety.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Jeśli nie znasz ostatniego wiersza kodu w tej chwili, poniżej. W pozostałej części tego dokumentu często omówimy użycia języka R w środowisku Azure Machine Learning.

### <a name="introduction-to-rstudio"></a>Wprowadzenie do programu RStudio
Programu RStudio jest powszechnie używaną IDE dla R. Użyję programu RStudio edycji, testowanie i debugowanie części kodu języka R używanych w tym przewodniku Szybki Start. Po kodzie R przetestowane i gotowe, można po prostu wyciąć i wkleić w edytorze programu RStudio Machine Learning Studio [wykonanie skryptu języka R] [ execute-r-script] modułu.  

Jeśli nie masz język programowania R zainstalowana na tym komputerze pulpitu, zaleca się, że możesz to zrobić teraz. Bezpłatne materiały do pobrania open source języka R są dostępne w kompleksowe R archiwum sieci (sieci CRAN) na [ http://www.r-project.org/ ](http://www.r-project.org/). Brak dostępnych pliki do pobrania dla systemu Windows, Mac OS i Linux/UNIX. Wybierz pobliskich dublowania i postępuj zgodnie z instrukcjami pobierania. Ponadto sieci CRAN zawiera wiele pakietów manipulowania przydatne analizy i danych.

Jeśli jesteś nowym użytkownikiem programu RStudio, należy pobrać i zainstalować wersję pulpitu. Można znaleźć programu RStudio pliki do pobrania dla systemu Windows, Mac OS i Linux/UNIX w http://www.rstudio.com/products/RStudio/. Wykonaj instrukcje podane do zainstalowania programu RStudio na komputera stacjonarnego.  

Samouczek Wprowadzenie do programu RStudio znajduje się w temacie https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

I Podaj dodatkowe informacje na temat używania programu RStudio w [dodatek a][appendixa].  

## <a id="scriptmodule"></a>Pobieranie danych i modułu wykonania skryptu języka R
W tej sekcji omówimy, jak pobrać dane do i z [wykonanie skryptu języka R] [ execute-r-script] modułu. Zapoznamy się sposób obsługi różnych typów danych do odczytu do i z [wykonanie skryptu języka R] [ execute-r-script] modułu.

Kompletny kod dla tej sekcji znajduje się w pliku zip pobranego wcześniej.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Załadowanie i sprawdzenie, czy dane w usłudze Machine Learning Studio
#### <a id="loading"></a>Załadowanie zestawu danych
Rozpoczniemy ładując **csdairydata.csv** pliku do usługi Azure Machine Learning Studio.

* Uruchom środowiska Azure Machine Learning Studio.
* Polecenie **+ nowy** w lewym dolnym rogu ekranu, a wybierz **zestawu danych**.
* Wybierz **z pliku lokalnego**, a następnie **Przeglądaj** i wybierz plik.
* Upewnij się, że wybrano **pliku CSV ogólnego z nagłówkiem (.csv)** jako typ zestawu danych.
* Kliknij znacznik wyboru.
* Po przekazaniu zestawu danych, powinien zostać wyświetlony nowy zestaw danych, klikając **zestawów danych** kartę.  

#### <a name="create-an-experiment"></a>Tworzenie eksperymentu
Teraz, gdy mamy niektóre dane w usłudze Machine Learning Studio, należy utworzyć eksperymentu w celu analizy.  

* Polecenie **+ nowy** w lewej dolnej i wybierz **eksperymentu**, następnie **pusty eksperyment**.
* Można określić nazwę eksperymentu wybierając i modyfikowanie **eksperymentu utworzony na...** tytuł w górnej części strony. Na przykład, zmieniając go na **urzędu certyfikacji Nabiał analizy**.
* Po lewej stronie eksperymentu, rozwiń węzeł **zapisane zestawów danych**, a następnie **Moje zestawów danych**. Powinny pojawić się **cadairydata.csv** który został wcześniej przekazany.
* Przeciągnij i upuść **csdairydata.csv dataset** na eksperymentu.
* W **wyszukiwania eksperymentu elementów** pole w górnej części okienka po lewej stronie, a typ [wykonanie skryptu języka R][execute-r-script]. Zostanie wyświetlone modułu są wyświetlane na liście wyszukiwania.
* Przeciągnij i upuść [wykonanie skryptu języka R] [ execute-r-script] modułu na Twoje palety.  
* Połącz dane wyjściowe **csdairydata.csv dataset** do lewej strony danych wejściowych (**Dataset1**) z [wykonanie skryptu języka R][execute-r-script].
* **Nie zapomnij kliknij opcję "Zapisz"!**  

W tym momencie eksperymentu powinien wyglądać jak rysunek 3.

![Eksperymentu analizy Nabiał urzędu certyfikacji z zestawu danych i modułu wykonania skryptu języka R][3]

*Rysunek 3. Analiza Nabiał urzędu certyfikacji Eksperymentuj z zestawu danych i modułu wykonania skryptu języka R.*

#### <a name="check-on-the-data"></a>Sprawdzanie w danych
Załóżmy ma przyjrzeć się dane, które możemy zostały załadowane do naszej eksperymentu. W eksperyment, kliknij na dane wyjściowe **cadairydata.csv dataset** i wybierz **wizualizacji**. Powinny zostać wyświetlone informacje takie jak rysunek 4.  

![Podsumowanie cadairydata.csv zestawu danych][4]

*Rysunek 4. Podsumowanie cadairydata.csv zestawu danych.*

W tym widoku widzimy wiele przydatnych informacji. Możemy stwierdzić, pierwsze kilka wierszy tego zestawu danych. Zaznaczenie kolumny, w sekcji Statystyki zawiera więcej informacji o kolumnie. Na przykład wiersz typu funkcji pokazuje nam, jakie typy danych Azure Machine Learning Studio przypisane do kolumny. Krótki przegląd podobny do tego jest wyboru związane z poprawnością dobra przed Rozpoczniemy wszelkich poważny pracy.

### <a name="first-r-script"></a>Pierwszy skrypt języka R
Umożliwia tworzenie prostego pierwszy skrypt języka R do wykonywania eksperymentów w usłudze Azure Machine Learning Studio. Po utworzeniu i przetestowany następujący skrypt w programu RStudio.  

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

Teraz musisz przenieść ten skrypt do usługi Azure Machine Learning Studio. I może po prostu wyciąć i wkleić. Jednak w takim przypadku I przenieś Moje skrypt języka R za pomocą pliku zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Dane wejściowe modułu wykonania skryptu języka R
Załóżmy ma przyjrzeć się dane wejściowe, aby [wykonanie skryptu języka R] [ execute-r-script] modułu. W tym przykładzie firma Microsoft będzie odczytywać dane mleczarskich Kalifornijskiej do [wykonanie skryptu języka R] [ execute-r-script] modułu.  

Istnieją trzy możliwe dane wejściowe [wykonanie skryptu języka R] [ execute-r-script] modułu. W zależności od aplikacji, można użyć dowolnego lub wszystkich tych danych wejściowych. Jest również doskonale zasadnie Użyj skryptu języka R, która nie wymaga danych wejściowych w ogóle.  

Przyjrzyjmy się każdy z tych danych wejściowych, przechodząc od lewej do prawej. Widać nazwy każdego wejścia umieszczając kursor nad dane wejściowe i odczytywania etykietka narzędzia.  

#### <a name="script-bundle"></a>Skrypt pakietu
Wprowadź pakietu skrypt umożliwia przekazywanie zawartości pliku zip do [wykonanie skryptu języka R] [ execute-r-script] modułu. Można użyć jednej z poniższych poleceń, można odczytać zawartości pliku zip do kodu języka R.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Usługa Azure Machine Learning traktuje plików zip, ponieważ, jeśli są one src / directory, więc należy prefiksu nazwy pliku o tej nazwie w katalogu. Na przykład, jeśli zip zawiera pliki `yourfile.R` i `yourData.rdata` w folderze głównym zip, czy adres je jako `src/yourfile.R` i `src/yourData.rdata` przy użyciu `source` i `load`.
> 
> 

Omówiono już zestawy danych ładowania [podczas ładowania zestawu danych](#loading). Po utworzeniu i przetestować skrypt języka R w poprzedniej sekcji, wykonaj następujące czynności:

1. Zapisz skrypt języka R w. Plik R. Można wywołać pliku skryptu "simpleplot. R". W tym miejscu jest zawartość.
   
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
2. Utwórz plik zip i skopiuj skrypt do tego pliku zip. W systemie Windows, kliknij prawym przyciskiem myszy plik i wybierz **przesyłają**, a następnie **skompresowanego folderu**. Spowoduje to utworzenie nowego pliku zip zawierającego "simpleplot. Plik R".
3. Dodaj plik do **zestawów danych** w usłudze Machine Learning Studio, określenie typu jako **zip**. Plik zip powinien zostać wyświetlony w zestawów danych.
4. Przeciągnij i upuść z pliku zip **zestawów danych** na **kanwy ML Studio**.
5. Połącz dane wyjściowe **zip danych** ikonę, aby **pakietu skryptu** wejściowych z [wykonanie skryptu języka R] [ execute-r-script] modułu.
6. Typ `source()` funkcji z nazwą pliku zip do okna kodu dla [wykonanie skryptu języka R] [ execute-r-script] modułu. W przypadku mojej wpisane `source("src/simpleplot.R")`.  
7. Upewnij się, że możesz kliknąć pozycję **zapisać**.

Po zakończeniu tych czynności [wykonanie skryptu języka R] [ execute-r-script] modułu powoduje uruchomienie skryptu języka R w pliku zip, uruchomienie eksperymentu. W tym momencie eksperymentu powinien wyglądać jak rysunek 5.

![Przy użyciu eksperymentów zip skrypt języka R][6]

*Rysunek 5. Przy użyciu eksperymentów zip skrypt języka R.*

#### <a name="dataset1"></a>Dataset1
Prostokątne tabeli danych można przekazać do kodu języka R, przy użyciu Dataset1 danych wejściowych. W naszym prostego skryptu `maml.mapInputPort(1)` funkcja odczytuje dane z portu 1. Te dane jest przypisywana do nazwy zmiennej dataframe w kodzie. W naszym prostego skryptu pierwsza linia kodu wykonuje przypisania.

    cadairydata <- maml.mapInputPort(1)

Wykonanie eksperymentu, klikając **Uruchom** przycisku. Po zakończeniu wykonywania, kliknij [wykonanie skryptu języka R] [ execute-r-script] modułu, a następnie kliknij przycisk **widoku pliku dziennika** w okienku właściwości. Nowa strona powinna zostać wyświetlona w przeglądarce z zawartością pliku dane_wyjściowe.log. Gdy przewiń w dół powinny zostać wyświetlone informacje podobne do następujących.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Dostępne na stronie jest bardziej szczegółowe informacje dotyczące kolumn, które będą wyglądać jak poniżej.

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

Wyniki są przeważnie zgodnie z oczekiwaniami, 228 uwag i 9 kolumn w dataframe. Widzimy nazwy kolumn, typ danych R i przykładowe każdej kolumny.

> [!NOTE]
> Ten sam wydruku jest łatwo dostępne z danych wyjściowych urządzenia R [wykonanie skryptu języka R] [ execute-r-script] modułu. Omówimy dane wyjściowe [wykonanie skryptu języka R] [ execute-r-script] modułu w następnej sekcji.  
> 
> 

#### <a name="dataset2"></a>Dataset2
Zachowanie Dataset2 danych wejściowych jest identyczna jak Dataset1. Przy użyciu tych danych wejściowych można przekazać drugiej tabeli prostokątne danych do kodu języka R. Funkcja `maml.mapInputPort(2)`, z argumentem 2 jest używany do przekazywania tych danych.  

### <a name="execute-r-script-outputs"></a>Wykonanie skryptu języka R w danych wyjściowych
#### <a name="output-a-dataframe"></a>Dane wyjściowe dataframe
Można output zawartość dataframe R jako tabelę prostokątne za pośrednictwem portu Dataset1 wyników przy użyciu `maml.mapOutputPort()` funkcji. W naszym prostego skryptu języka R jest to wykonywane przez następujący wiersz.

    maml.mapOutputPort('cadairydata')

Po uruchomieniu eksperyment, kliknij port wyjściowy Dataset1 wyników, a następnie kliknij polecenie **Visualize**. Powinny zostać wyświetlone informacje takie jak rysunek 6.

![Wizualizacja danych wyjściowych danych mleczarskich Kalifornijskiej][7]

*Rysunek 6. Wizualizacja danych mleczarskich Kalifornijskiej dane wyjściowe.*

Te dane wyjściowe wygląda takie same jak dane wejściowe, dokładnie tak, jak oczekiwaliśmy.  

### <a name="r-device-output"></a>Dane wyjściowe R urządzenia
Dane wyjściowe urządzenia [wykonanie skryptu języka R] [ execute-r-script] moduł zawiera dane wyjściowe wiadomości i grafiki. Standardowe Wyjście i błąd standardowy komunikaty o błędach z języka R są wysyłane do urządzenia R port wyjściowy.  

Aby wyświetlić dane wyjściowe R urządzenia, kliknij port, a następnie na **Visualize**. Widzimy wyjście standardowe i błąd standardowy ze skryptu języka R na rysunku 7.

![Wyjście standardowe i błąd standardowy z portu urządzenia R][8]

*Rysunek 7. Wyjście standardowe i błąd standardowy z portu urządzenia R.*

Przewijanie w dół możemy wyświetlić dane wyjściowe grafiki z naszych skrypt języka R na rysunku 8.  

![Dane wyjściowe grafiki portu urządzenia R][9]

*Rysunek 8. Graficzne dane wyjściowe z portu urządzenia R.*  

## <a id="filtering"></a>Filtrowanie danych i przekształcania
W tej sekcji zostaną wykonane niektóre podstawowe dane filtrowania i przekształcenie operacje na danych mleczarskich Kalifornijskiej. Do końca w tej sekcji zostanie zostały dane w formacie odpowiedni w przypadku tworzenia modelu danych analitycznych.  

W szczególności, w tej sekcji zostaną wykonane kilka typowych danych czyszczenia i przekształcenie zadań: wpisz przekształcania filtrowanie dataframes Dodawanie nowej kolumny obliczane, a wartość przekształcenia. Ta tła powinna ułatwić obsługi wielu odmian w rzeczywistych problemów.

Kompletny kod R w tej sekcji znajduje się w pliku zip pobranego wcześniej.

### <a name="type-transformations"></a>Typ przekształcenia
Teraz, gdy firma Microsoft może odczytywać danych mleczarskich Kalifornijskiej do kodu języka R [wykonanie skryptu języka R] [ execute-r-script] modułu, musimy upewnij się, że dane w kolumnach ma danego typu i formatu.  

R jest językiem typach określanych dynamicznie, co oznacza, że typy danych są przekształcone z jednego do drugiego zgodnie z potrzebami. Typy danych atomic w R to liczbowe, logicznych i znak. Typ współczynnik służy do compactly przechowywać dane podzielone na kategorie. Znacznie więcej informacji o typach danych można znaleźć w odwołaniach w [dodatek B — dalsze informacje](#appendixb).

Podczas odczytywania danych tabelarycznych w R ze źródła zewnętrznego, zawsze jest dobrym pomysłem jest Sprawdź typy wynikowe w kolumnach. Może być kolumną typu znaków, ale w wielu przypadkach to będzie wyświetlany jako współczynnik lub na odwrót. W innych przypadkach kolumny, które zdaniem użytkownika powinna być liczbowe jest reprezentowana przez dane znakowe, np. numer punktu "1,23" zamiast 1,23 jako liczbą zmiennoprzecinkową.  

Na szczęście może łatwo dojść do przekonwertowania typu na inny, tak długo, jak mapowanie jest możliwe. Na przykład "Nevada" nie można przekonwertować na wartość numeryczną, ale można go przekonwertować na współczynnik (podzielone na kategorie zmienna). Inny przykład liczbowe 1 można przekonwertować na znak "1" lub współczynnik.  

Składnia dla każdej z tych konwersji jest prosty: `as.datatype()`. Oto te funkcje konwersji typu.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Spojrzenie na typy danych kolumn, możemy danych wejściowych w poprzedniej sekcji: wszystkie kolumny są typu liczbowego, z wyjątkiem kolumnę z etykietą "Month", który jest typem znaku. Załóżmy Skonwertuj je na czynnik i wyniki testu.  

Usunięto wiersza, który macierzy scatterplot i dodawane linii współczynnik konwersji kolumny "Month". W moim eksperymencie I będzie tylko wycinanie i wklejanie kodu języka R w oknie kodu [wykonanie skryptu języka R] [ execute-r-script] modułu. Można również zaktualizować plik zip i przekazać go do usługi Azure Machine Learning Studio, ale trwa to kilka kroków.  

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

Umożliwia wykonanie tego kodu i sprawdź w dzienniku danych wyjściowych skryptu języka R. Odpowiednie dane do dziennika jest pokazano na rysunku 9.

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

*Rysunek 9. Podsumowanie dataframe ze zmienną Multi-Factor.*

Teraz zostanie wyświetlony komunikat typu dla miesiąca "**współczynnik z 14 poziomy**". Jest to problem, ponieważ tylko 12 miesięcy w roku. Możesz również sprawdzić, czy typ w **Visualize** wynik zestawu danych jest port "**Categorical**".

Problem polega na tym, że kolumny "Month" ma nie został na stałe systematycznie. W niektórych przypadkach miesiąca nosi kwietnia i w innych jest skracana jako kwietnia. Problem można rozwiązać przez przycinanie ciąg do 3 znaki. Wiersz kodu teraz wygląda następująco:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Ponownie uruchomić eksperyment i wyświetlić dziennik danych wyjściowych. Oczekiwanych rezultatów przedstawiono na rysunku nr 10.  

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

*Rysunek 10. Podsumowanie dataframe z poprawną liczbę poziomów współczynnik.*

Nasze zmienna współczynnik ma teraz żądaną poziomy 12.

### <a name="basic-data-frame-filtering"></a>Filtrowanie ramki danych podstawowych
R dataframes obsługuje zaawansowane funkcje filtrowania. Zestawy danych mogą być podzbiory przy użyciu filtrów logiczne według wierszy lub kolumn. W wielu przypadkach będzie wymagane kryteria filtrowania złożonych. Odwołania w [dodatek B — dalsze informacje](#appendixb) zawierają przykłady szeroką gamę dataframes filtrowania.  

Istnieje jeden bit filtrowania należy wykonać w naszym zestawie danych. Jeśli przyjrzymy się do kolumn w cadairydata dataframe, zobaczysz dwóch zbędne kolumny. Pierwsza kolumna zawiera tylko numer wiersza, który nie jest bardzo przydatne. Druga kolumna Year.Month, zawiera nadmiarowe informacje. Firma Microsoft łatwo wykluczyć te kolumny za pomocą następującego kodu języka R.

> [!NOTE]
> Od teraz w tej sekcji I po prostu opisano dodatkowy kod dodaję w [wykonanie skryptu języka R] [ execute-r-script] modułu. Dodam każdego nowego wiersza **przed** `str()` funkcji. Aby zweryfikować wyniki w usłudze Azure Machine Learning Studio można użyć tej funkcji.
> 
> 

I Dodaj następujący wiersz do mojego kodu języka R w [wykonanie skryptu języka R] [ execute-r-script] modułu.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Uruchom ten kod w eksperymencie i sprawdź wynik z dziennikiem wyjścia. Wyniki te są wyświetlane w rysunek 11.

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

*Rysunek 11. Podsumowanie dataframe z dwóch kolumn będących jej usunąć.*

Dobra wiadomość! Uzyskujemy oczekiwanych rezultatów.

### <a name="add-a-new-column"></a>Dodaj nową kolumnę
Do tworzenia modeli szeregów czasowych należy przygotować ma kolumnę zawierającą miesięcy od rozpoczęcia szeregów czasowych. Utworzymy nową kolumnę "Month.Count".

Aby ułatwić organizowanie kodu utworzymy naszych pierwszej funkcji proste `num.month()`. Tę funkcję, aby utworzyć nową kolumnę w dataframe następnie zostaną zastosowane. Nowy kod ma następującą składnię.

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

Teraz uruchom eksperyment zaktualizowane i korzystania z danych wyjściowych dziennika, aby wyświetlić wyniki. W rysunku 12 przedstawiono te wyniki.

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

*Rysunek 12. Podsumowanie dataframe dodatkowe kolumny.*

Wygląda na to, wszystko działa. W naszym dataframe zostały nową kolumnę z oczekiwanych wartości.

### <a name="value-transformations"></a>Przekształcenia wartości
W tej sekcji zostaną wykonane na wartości w kolumnach naszych dataframe niektórych prostych transformacji. Język R obsługuje przekształcenia niemal dowolną wartość. Odwołania w [dodatek B — dalsze informacje](#appendixb) zawiera szeroką gamę przykłady.

Jeśli przyjrzymy się wartości podsumowań naszych dataframe powinny zostać wyświetlone informacje nieparzysta tutaj. Więcej lodów niż mleko jest generowany w Kalifornijskiej? Nie, oczywiście nie, jak to ma sensu sad jako ten fakt może być niektórzy lovers lodów. Jednostki są różne. Cena jest w jednostkach nam kg, mleko jest w jednostkach 1 mln kg USA, lodów znajduje się w jednostkach 1000 nam galonach, i ser chałupniczej jest w jednostce 1000 jednostkach funt Stanów Zjednoczonych. Zakładając, że lodów waga na galon paliwa około jednostkach 6.5 funt, firma Microsoft można w prosty sposób mnożenia, aby przekonwertować te wartości, dzięki czemu są one w taki sam jednostek 1000 jednostkach funt.

Dla modelu prognozowania używamy mnożenia modelu trendu i okresach dostosowania tych danych. Transformacja dziennika pozwala używać model liniowy, co upraszcza ten proces. Transformacja dziennika w tej samej funkcji, w których jest stosowane Mnożnik można zastosować.

W poniższym kodzie można zdefiniować nową funkcję, `log.transform()`i zastosować je do wierszy zawierających wartości liczbowe. R `Map()` funkcja służy do stosowania `log.transform()` funkcji do wybranych kolumn dataframe. `Map()` przypomina `apply()` , ale zezwala na więcej niż jeden lista argumentów do funkcji. Należy pamiętać, że lista mnożników dostarcza drugi argument `log.transform()` funkcji. `na.omit()` Funkcja jest używana jako nieco oczyszczania zapewnienie w dataframe nie ma wartości Brak lub niezdefiniowany.

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

Występuje dość bitowe w toku w `log.transform()` funkcji. Większość tego kodu jest sprawdzanie potencjalnych problemów z argumentami lub obsłudze wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy kodu faktycznie do obliczenia.

Celem obrony programowania jest aby uniknąć awarii jednej funkcji, który uniemożliwia przetwarzania przed kontynuowaniem. Niespodziewane błąd analizy długotrwałe może być dość irytujące dla użytkowników. Aby tego uniknąć, należy wybrać zwracanych wartości domyślne, który ograniczy szkody w celu przetwarzania podrzędnego. Komunikat jest również tworzony powiadamiania użytkowników, że coś niepowodzenia.

Jeśli nie są używane do programowania obrony w R, ten kod może wydawać się nieco utrudnione. I przeprowadzi Cię przez najważniejsze czynności:

1. Wektor cztery komunikaty jest zdefiniowany. Komunikaty te są używane do przekazywania informacji o pewnych możliwych błędów i wyjątków, które mogą wystąpić z tego kodu.
2. I zwróć wartość NA dla każdego przypadku. Istnieje wiele możliwości, które mogą mieć mniej efekty uboczne. Można wrócić wektor wartości zerowe lub oryginalnego wektora wejściowych, na przykład.
3. Testy są uruchomione na argumenty funkcji. W każdym przypadku, jeśli zostanie wykryty błąd, zwracana jest wartość domyślna i wiadomość jest generowany przez `warning()` funkcji. Używam `warning()` zamiast `stop()` zgodnie z jego spowoduje przerwanie wykonywania, dokładnie co próbuję się uniknąć. Należy pamiętać, że pismo ten kod w stylu procedurach, tak jak w przypadku tego podejścia funkcjonalności sprawiał złożone i zasłoniętej.
4. Obliczenia dziennika są ujęte w `tryCatch()` tak, aby wyjątki nie spowoduje zatrzymania niespodziewane do przetwarzania. Bez `tryCatch()` większość błędów zgłaszane przez wynik funkcji R w sygnał zatrzymania, która obsługuje tylko dla niej.

Wykonanie tego kodu języka R w eksperymencie i plik dane_wyjściowe.log zawiera przyjrzeć się wydruku. Zostanie wyświetlona po przekształceniu wartości cztery kolumny w dzienniku, jak pokazano na rysunku 13.

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

*Rysunek 13. Podsumowanie wartości przekształcone w dataframe.*

Widzimy, że zostały przekształcone wartości. Teraz mleczności znacznie przekracza wszystkich innych produktu mleczarskiego środowiska produkcyjnego, odwołując się, że teraz czekamy na skali logarytmicznej.

W tym momencie nasze dane jest wyczyszczone, a firma Microsoft są gotowe do niektórych modelowania. Spojrzenie na wizualizację podsumowanie wyników Dataset dane wyjściowe naszych [wykonanie skryptu języka R] [ execute-r-script] modułu, zobaczysz kolumny "Month" jest "Categorical" 12 unikatowych wartości, ponownie, podobnie jak możemy.

## <a id="timeseries"></a>Obiekty serie czasu i analiza korelacji
W tej sekcji możemy eksplorowania kilka podstawowych obiektów serii czas R i analizować korelacji między niektóre zmienne. Naszym celem jest dataframe, zawierający informacje o powiązaniu parowania w kilku odchyłki output.

Kompletny kod R w tej sekcji znajduje się w pliku zip pobranego wcześniej.

### <a name="time-series-objects-in-r"></a>Czas obiektów serii R
Jak już wspomniano, czas serii są serii danych wartości indeksowane według czasu. R czasu serii obiektów są używane do tworzenia i zarządzania nimi indeksu czasu. Ma kilka zalet przy użyciu czasu serii obiektów. Obiekty serii czas zwolnienia z wielu szczegółów związanych z zarządzaniem wartości indeks serii czasu, które znajdują się w obiekcie. Ponadto obiekty serii czasu umożliwiają używanie wiele metod serii czas do kreślenia, drukowanie, modelowania itp.

Klasa serii czasu POSIXct to powszechnie używane i jest stosunkowo proste. Ta szeregów czasowych klasy środki czas od początku epoka 1 stycznia 1970. W tym przykładzie używamy POSIXct czasu serii obiektów. Inne powszechnie używane klasy obiektu serii czas R obejmują zoo i xts, szeregów czasowych rozszerzonego.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Przykład obiektu serii czasu
Rozpocznijmy naszym przykładzie. Przeciągnij i upuść **nowe** [wykonanie skryptu języka R] [ execute-r-script] modułu w eksperymencie. Połącz port wyjściowy wyniku Dataset1 istniejącej [wykonanie skryptu języka R] [ execute-r-script] nowy port wejściowy moduł Dataset1 [wykonanie skryptu języka R] [ execute-r-script] modułu.

Tak jak pierwszy przykłady, jak możemy przejść przez przykładzie, w niektórych punktach I wyświetli tylko przyrostowe dodatkowe wiersze kodu języka R w każdym kroku.  

#### <a name="reading-the-dataframe"></a>Odczytywanie dataframe
Pierwszym krokiem umożliwia odczytu w dataframe i upewnij się, że uzyskujemy oczekiwanych rezultatów. Następujący kod należy wykonać zadanie.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Teraz uruchom eksperyment. Dziennik nowy kształt wykonanie skryptu języka R powinien wyglądać rysunku 14.

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

*Rysunek 14. Podsumowanie dataframe w module wykonanie skryptu języka R.*

Te dane są oczekiwane typy i format. Należy zauważyć, że kolumna "Month" współczynnika typu i ma oczekiwanej liczby poziomów.

#### <a name="creating-a-time-series-object"></a>Tworzenie obiektów serii czasu
Musimy dodać obiekt serii czas do naszej dataframe. Zastąp bieżący kod następujący komunikat, który dodaje kolumnę nowej klasy POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Teraz zajrzyj do dziennika. Powinien on wyglądać rys. 15.

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

*Rysunek 15. Podsumowanie dataframe z obiektem serii czasu.*

Możemy stwierdzić Podsumowanie nowej kolumny, która jest w rzeczywistości klasy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Poznawanie i przekształcenia danych
Przyjrzyjmy się niektóre zmienne w tym zestawie danych. Macierz scatterplot jest dobrym sposobem uzyskania szybko wyświetlić. Jestem I zastępowanie `str()` funkcji w poprzednim kodzie R z następującego wiersza.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Uruchom ten kod i zobacz, co się stanie. Kreślenia w portu urządzenia R powinien wyglądać rysunek 16.

![Macierz Scatterplot wybranych zmiennych][17]

*Rysunek 16. Macierz Scatterplot wybranych zmiennych.*

Relacje między tymi zmiennymi jest niektórych odd-looking struktury. Prawdopodobnie wynika to z trendów w danych i z faktu, że firma Microsoft nie ustalić zmienne.

### <a name="correlation-analysis"></a>Analiza korelacji
Aby przeprowadzić analizę korelacji musimy zarówno cofnąć trendów i standaryzowanie zmienne. Firma Microsoft może po prostu użyć R `scale()` funkcji, która koncentruje się jak również skaluje zmiennych. Ta funkcja może być również szybsze. Jednak warto przedstawiają przykłady obrony programing w języku R.

`ts.detrend()` Funkcja pokazano poniżej wykonuje obie te operacje. Następujące dwa wiersze kodu do tendencji danych i zapewnij standaryzację wartości.

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

Występuje dość bitowe w toku w `ts.detrend()` funkcji. Większość tego kodu jest sprawdzanie potencjalnych problemów z argumentami lub obsłudze wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy kodu faktycznie do obliczenia.

Już Omówiliśmy przykład obrony Programowanie w [wartości przekształcenia](#valuetransformations). Zarówno bloki obliczenia są ujęte w `tryCatch()`. Niektóre błędy warto zwrócić oryginalnego wektora wejściowych, a w pozostałych przypadkach wrócić wektor zer.  

Należy pamiętać, że regresji liniowej używane do usuwania analizy trendów regresji serii czasu. Zmienna predykcyjne jest obiekt serii czasu.  

Raz `ts.detrend()` zdefiniowano Trwa stosowanie zmienne zainteresowanie naszych dataframe. Firma Microsoft musi wymuszone wynikowej listy utworzone przez `lapply()` do dataframe danych przy użyciu `as.data.frame()`. Z powodu obrony aspektów `ts.detrend()`, nie można przetworzyć jednego z zmienne nie uniemożliwia poprawne przetwarzanie pozostałych.  

Końcowe wiersz kodu tworzy scatterplot parowania. Po uruchomieniu kodu języka R, w 17 rysunku przedstawiono wyniki scatterplot.

![Scatterplot parowania w szeregu czasowym cofnąć trend i standardowych][18]

*Rysunek 17. Pairwise — scatterplot szeregów czasowych cofnąć trend i standardowych.*

Możesz porównać te wyniki tych przedstawionych na rysunku 16. Trend usunięte i zmienne standaryzowane widzimy znacznie mniej struktury w relacji między tymi zmiennymi.

Kod do obliczenia korelacji jako obiekty KKS R ma następującą składnię.

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

Uruchomienie tego kodu tworzy dziennika pokazano na rysunku 18.

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

*Rysunek 18. Lista KKS obiektów z analizy parowania korelacji.*

Brak wartości korelacji dla każdego opóźnienie. Żaden z tych wartości korelacji nie jest wystarczająco duży, aby mieć znaczenie. Firma Microsoft w związku z tym stwierdzić, czy możemy modelu każdej zmiennej niezależnie.

### <a name="output-a-dataframe"></a>Dane wyjściowe dataframe
Firma Microsoft ma obliczana parowania korelacji jako listę obiektów KKS R. Przedstawia nieco problem, jako port wyjściowy zestaw danych wyników wymaga naprawdę dataframe. Ponadto obiektu KKS jest listą i chcemy tylko wartości w pierwszym elemencie tej listy korelacji w różnych odchyłki.

Poniższy kod wyodrębnia wartości opóźnienia na liście KKS obiektów, które są także list.

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
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

Pierwszy wiersz kodu jest nieco trudnych, a niektóre informacje mogą pomóc w go zrozumieć. Praca z środka na zewnątrz mamy się poniżej:

1. "**[[**"Operator z argumentem"**1**" wybiera wektor korelacji w odchyłki z pierwszym elementem KKS listy obiektów.
2. `do.call()` Ma zastosowanie funkcja `rbind()` przez elementy listy funkcja przez `lapply()`.
3. `data.frame()` Funkcja wymusza traktowanie wyniku zwracany przez `do.call()` do dataframe.

Należy pamiętać, że są nazwy wierszy w kolumnie dataframe. Ten wiersz tak zachowuje nazwy, gdy są one dane wyjściowe z [wykonanie skryptu języka R][execute-r-script].

Wykonywanie kodu generuje dane wyjściowe, pokazano na rysunku 19 podczas I **Visualize** dane wyjściowe w porcie wynik zestawu danych. Wiersz nazwy znajdują się w pierwszej kolumnie zgodnie z założeniami.

![Dane wyjściowe wyniki analizy korelacji][20]

*Rysunek 19. Dane wyjściowe z analizy korelacji wyniki.*

## <a id="seasonalforecasting"></a>Przykład serii czasu: okresach prognozowania
Naszych danych jest teraz w postaci nadające się do analizy, a Ustaliliśmy, że nie ma żadnych znaczących korelacji między zmienne. Umożliwia przenoszenie na i Utwórz szeregów czasowych, funkcja prognozowania modelu. Użycie tego modelu firma Microsoft będzie prognozy Kalifornijskiej mleczności przez 12 miesięcy 2013.

Nasze modelu prognozowania można używać ma dwa składniki, trend składnika i składnik okresach. Zakończenie prognozy jest produktem te dwa składniki. Ten typ modelu nosi nazwę modelu mnożenia. Alternatywą jest model dodatku. Firma Microsoft już stosowane transformację dziennika do zmiennych zainteresowań, co sprawia, że analiza tractable.

Kompletny kod R w tej sekcji znajduje się w pliku zip pobranego wcześniej.

### <a name="creating-the-dataframe-for-analysis"></a>Tworzenie dataframe do analizy
Rozpocznij od dodania **nowe** [wykonanie skryptu języka R] [ execute-r-script] modułu do eksperymentu. Połącz **zestaw wyników danych** istniejące dane wyjściowe [wykonanie skryptu języka R] [ execute-r-script] moduł **Dataset1** wejściowych nowego modułu. Wynik powinien wyglądać jak rysunek 20.

![Doświadczenia z modułem wykonanie skryptu języka R dodane][21]

*Rysunek 20. Doświadczenia z modułem wykonanie skryptu języka R dodane.*

Jako analizy korelacji, który mamy właśnie został ukończony, należy dodać kolumnę z obiektem POSIXct czasu serii. Poniższy kod zostanie w tym właśnie.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Uruchom ten kod i poszukaj w dzienniku. Wynik powinien wyglądać rysunek 21.

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

*Rysunek 21. Podsumowanie dataframe.*

Z tym wynikiem możemy gotowe do rozpoczęcia nasze analizy.

### <a name="create-a-training-dataset"></a>Tworzenie zestawu danych szkolenia
Z dataframe skonstruowany należy utworzyć zestawu danych szkoleniowych. Te dane będzie zawierać wszystkie obserwacji z wyjątkiem ostatnie 12 2013 roku, czyli naszego zestawu danych testowych. Następujące kodu podzestawy dataframe i tworzy powierzchni mleczarskich zmiennych produkcyjnego i ceny. I Utwórz powierzchni cztery produkcyjnych i ceny zmiennych. Funkcja anonimowa służy do zdefiniować niektóre wspomaga dla powierzchni, a następnie przejść przez listę dwa argumenty z `Map()`. Jeśli myślisz, że dla pętli będą działały prawidłowo w tym miejscu, są prawidłowe. Jednak ponieważ funkcjonalności języka R I am zastosowania podejścia funkcjonalności.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Wykonywanie kodu tworzy serię czasu serii geograficzne z danych wyjściowych urządzenia R pokazano na rysunku 22. Należy pamiętać, że na osi czasu w jednostkach dat, nieuprzywilejowany korzyści czasu metody kreślenia serii.

![Pierwszy z serii wykresy czasu Kalifornijskiej mleczarskich danych produkcyjnych i cen](./media/r-quickstart/unnamed-chunk-161.png)

![Sekundy czasu serii wykresów Kalifornijskiej mleczarskich danych produkcyjnych i cen](./media/r-quickstart/unnamed-chunk-162.png)

![Innych czasu serii wykresów Kalifornijskiej mleczarskich danych produkcyjnych i cen](./media/r-quickstart/unnamed-chunk-163.png)

![Czwarta czasu serii wykresów Kalifornijskiej mleczarskich danych produkcyjnych i cen](./media/r-quickstart/unnamed-chunk-164.png)

*Rysunek 22. Czas serii wykresów mleczarskie Kalifornijskiej i cen danych.*

### <a name="a-trend-model"></a>Wzór trendu
Wytworzeniu obiektów serie czasu i o przyjrzeliśmy danych, Zacznijmy do skonstruowania wzór trendu danych produkcyjnych mleka Polski. Można to zrobić dzięki regresji serii czasu. Jednak jest wyczyszczone z powierzchni, firma Microsoft będzie konieczne więcej niż wartość zerowa i przechwycić dokładnie modelowanie obserwowanych trendów w danych szkoleniowych.

Biorąc pod uwagę małą skalę danych, I będzie kompilacji wzór trendu w programu RStudio i następnie wycinanie i wklejanie modelu wynikowego do usługi Azure Machine Learning. Programu RStudio zapewnia interaktywne środowisko dla tego typu analizy interakcyjnej.

Jako pierwsza próba I ponowi wielomianu regresji uprawnienia do 3. Brak rzeczywistych niebezpieczeństwo nadmiernie dopasowanie tych rodzajów modeli. W związku z tym najlepiej uniknąć warunków najwyższego rzędu. `I()` Funkcja zapobiega interpretacji zawartości (interpretuje zawartość "jak jest") i umożliwia pisanie funkcji interpretowany dosłownie w równanie regresji.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

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

Z wartości P (Pr (> | t |)) w tym danych wyjściowych widać, kwadratów termin jest znaczący. Będę używać `update()` funkcji do modyfikowania tego modelu upuszczając kwadratów terminu.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

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

To wygląda lepiej. Wszystkie warunki są istotne. Jednak wartości 2e 16 wartości domyślnej i nie powinny być pobierane za poważnie.  

Jako test związane z poprawnością upewnijmy czasu kreślenia serii danych mleczarskie Kalifornijskiej z krzywej trend wyświetlane. Po dodaniu następujący kod w usłudze Azure Machine Learning [wykonanie skryptu języka R] [ execute-r-script] modelu (nie programu RStudio), aby utworzyć model i spowodować wykresu. Wynik jest wyświetlany na rysunku 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Danych produkcyjnych mleka Kalifornijskiej z modelem trend pokazano](./media/r-quickstart/unnamed-chunk-18.png)

*Rysunek 23. Polski mleka danych produkcyjnych z modelem trend wyświetlane.*

Wygląda na to, dość dobrze dopasowania modelu trendu do danych. Ponadto wydaje się być dowód uwierzytelniając dopasowywania, takie jak nietypowych wierci się krzywej modelu.  

### <a name="seasonal-model"></a>Okresach modelu
Z modelem trendu w ręcznie musimy push i zawiera efekty okresach. Używamy miesiąc w roku jako zmienną zastępczego w liniowej modelu do przechwytywania efekt kolejnych miesięcy. Należy pamiętać, że podczas wprowadzania współczynnik zmiennych do modelu, punkt przecięcia musi nie można obliczyć. Jeśli nie zrobisz, nadmiernie określono formuła i R będzie Porzuć jedną z żądaną czynników, ale zachować termin przechwycenia.

Ponieważ mamy modelu zadowalające trend możemy użyć `update()` funkcji, aby dodać nowe warunki do istniejącego modelu. -1 w formule aktualizacji porzuca termin przechwycenia. Kontynuowanie w programu RStudio w tej chwili:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

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

Widzimy, czy model już ma termin przechwycenia i ma 12 miesiąca istotne czynniki. Jest to dokładnie, możemy wyświetlić.

Upewnijmy innego kreślenia serii czasu danych mleczarskie Kalifornijskiej jak działa okresach modelu. Po dodaniu następujący kod w usłudze Azure Machine Learning [wykonanie skryptu języka R] [ execute-r-script] utworzenia modelu i określony wykres.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Uruchomienie tego kodu w usłudze Azure Machine Learning tworzy wykres pokazano na rysunku 24.

![Polski mleczności z modelu w tym okresach efekty](./media/r-quickstart/unnamed-chunk-20.png)

*Rysunek 24. Polski mleczności z modelu w tym okresach efekty.*

Dopasuj do danych wyświetlanych w 24 rysunku jest raczej wspieranie. Wygląd uzasadnione zarówno trend, jak i okresach efekt (odmiany miesięczne).

Kolejny test na nasz model umożliwia również przyjrzeć się reszty. Poniższy kod oblicza przewidywane wartości z naszych dwa modele, oblicza reszty okresach modelu i następnie geograficzne pozostałości te danych szkoleniowych.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Pozostałe kreślenia jest wyświetlany w 25 rysunku.

![Reszty okresach modelu dla danych szkoleniowych](./media/r-quickstart/unnamed-chunk-21.png)

*Rysunek 25. Reszty okresach modelu dla danych szkoleniowych.*

Te składniki resztkowe Szukaj uzasadnione. Brak określonej struktury, jednak uwzględnione recesja 2008 2009, które bez uwzględnienia nasz model szczególnie dobrze.

Kreślenia pokazano na rysunku 25 jest przydatna do wykrywania wszelkich wzorce zależnych od czasu w reszty. Jawne podejście przetwarzania i kreślenia reszty, I używać miejsc reszty w kolejności czasu na powierzchni. Jeśli z drugiej strony I ma wykreślić `milk.lm$residuals`, powierzchni nie była w kolejności czasu.

Można również użyć `plot.lm()` wygenerowało serii wykresów diagnostycznych.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Ten kod tworzy serii wykresów diagnostycznych pokazano na rysunku 26.

![Pierwszy diagnostycznych powierzchni okresach modelu](./media/r-quickstart/unnamed-chunk-221.png)

![Drugie diagnostycznych powierzchni okresach modelu](./media/r-quickstart/unnamed-chunk-222.png)

![Trzecia diagnostycznych powierzchni okresach modelu](./media/r-quickstart/unnamed-chunk-223.png)

![Czwarta diagnostycznych powierzchni okresach modelu](./media/r-quickstart/unnamed-chunk-224.png)

*Rysunek 26. Diagnostyka geograficzne okresach modelu.*

Istnieje kilka punktów wysokiej znaczenie zidentyfikowany w tych powierzchni, ale nie powoduje nas bardzo ważne. Ponadto możemy stwierdzić, z powierzchni normalny Q-Q blisko zazwyczaj dystrybuowane, ważne założeń modeli liniowych czy reszty.

### <a name="forecasting-and-model-evaluation"></a>Ocena prognozowania i modelu
Istnieje tylko jeden element więcej zrobić, aby ukończyć naszym przykładzie. Musimy obliczania prognozy i pomiar błąd względem danych rzeczywistych. Nasze prognozy będzie przez 12 miesięcy 2013. Firma Microsoft może obliczeniowe błąd miary to prognozy, do rzeczywistych danych, które nie jest częścią naszego zestawu danych szkoleniowych. Ponadto firma Microsoft porównanie wydajności na 18 lat danych szkoleniowych do 12 miesięcy od danych testowych.  

Liczba metryki służą do mierzenia wydajności różnych modeli szeregów czasowych. W tym przypadku użyjemy błędu głównego średniej kwadratowej (RMS). Następująca funkcja oblicza błędu RMS między dwóch serii.  

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

Jak `log.transform()` funkcja omówiono w sekcji "Wartości przekształcenia" jest bardzo dużo kod błędu sprawdzania i wyjątków odzyskiwania w tej funkcji. Zasady stosowane są takie same. Praca odbywa się w dwóch miejscach w `tryCatch()`. Najpierw szeregów czasowych są exponentiated, ponieważ będziemy odbywała się wcześniej Praca z dziennikami wartości. Po drugie usługi RMS błędu jest obliczana.  

Załóżmy wyposażony w funkcję do mierzenia błąd usługi RMS, kompilowania i output dataframe, zawierające błędy usług RMS. Firma Microsoft będzie zawierać warunki dla samego modelu trendu i pełny model o okresach czynników. Poniższy kod wykonuje zadania przy użyciu dwóch modeli liniowych, które możemy zbudowania.

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

Uruchomienie tego kodu generuje dane wyjściowe, pokazano na rysunku 27 na port wyjściowy zestaw danych z wyników.

![Porównanie usług RMS błędów dla modeli][26]

*Rysunek 27. Porównanie błędy usług RMS dla modeli.*

Z tymi wynikami widzimy, że dodawanie okresach czynniki do modelu zmniejsza błędu RMS znacznie. Nie za zaskakująco błąd usługi RMS, aby uzyskać dane szkoleniowe jest nieco mniejsze niż dla prognozy.

## <a id="appendixa"></a>Dodatek A: Przewodnik programu RStudio
Programu RStudio jest bardzo dobrze udokumentowane, dlatego w tym dodatku I podać niektóre łącza kluczy w sekcjach dotyczących dokumentacji programu RStudio ułatwiających rozpoczęcie pracy.

1. Tworzenie projektów
   
   Można porządkować i zarządzać kodem R do projektów przy użyciu programu RStudio. Dokumentacji, który używa projekty można znaleźć w folderze https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Najlepiej wykonać te kroki, a następnie utwórz projekt R przykłady kodu w tym dokumencie.  
2. Edytowanie i wykonywanie kodu języka R
   
   Programu RStudio zapewnia zintegrowane środowisko umożliwiające edytowanie i wykonywanie kodu języka R. Dokumentację można znaleźć w folderze https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Debugowanie
   
   Programu RStudio obejmuje zaawansowane możliwości debugowania. Dokumentacja dla tych funkcji jest na https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   Punkt przerwania funkcji rozwiązywania problemów są udokumentowane w https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>Dodatek B: Dalsze informacje.
Ten samouczek programowania R obejmuje podstawy muszą używać języka R w usłudze Azure Machine Learning Studio. Jeśli nie masz doświadczenia w obsłudze R, dwie instrukcje są dostępne w sieci CRAN:

* R dla początkujących przez Emmanuel Paradis jest dobrym miejscem do rozpoczęli http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* Wprowadzenie do języka R przez W. N. Venables et. al. Przechodzi do nieco bardziej szczegółowo w http://cran.r-project.org/doc/manuals/R-intro.html.

Istnieje wiele — książki na R, które mogą pomóc Ci rozpocząć. Poniżej przedstawiono kilka, które mogę być przydatne:

* Kompozycji programowania w języku R: samouczek z oprogramowania statystycznie przez Matloff Normanowi jest doskonałym wprowadzenie do programowania w języku R.  
* R Cookbook przez Teetor Pawła dostarcza rozwiązanie problemu i rozwiązanie przy użyciu R.  
* R w akcji Robert Kabacoff jest innym przydatne książki wstępne. Szybkie R pomocnika witryny sieci Web jest zasobem przydatne w http://www.statmethods.net/.
* R Inferno przez oparzenia Patrick jest zaskakująco żartobliwy książki, która zajmuje się liczbę trudnych i trudne tematy, które mogą wystąpić podczas programowania w języku R. Książce jest dostępny bezpłatnie na http://www.burns-stat.com/documents/books/the-r-inferno/.
* Jeśli chcesz nowości w zaawansowanych tematów w R mają wygląd książki zaawansowane R przez Hadley Wickham. Wersję online tego podręcznika jest dostępny bezpłatnie na http://adv-r.had.co.nz/.

Wykaz R czasu serii pakietów można znaleźć w widoku zadań sieci CRAN do analizy serii czasowych: http://cran.r-project.org/web/views/TimeSeries.html. Informacje w określonym czasie serii obiekt pakietów należy się z dokumentacją tego pakietu.

Podręcznik wprowadzające szeregu czasowego z R Cowpertwait Pawła i Andrew Metcalfe zawiera wprowadzenie do użycia języka R do analizy serii czasowych. Wiele więcej teoretycznego teksty zawierają przykłady R.

Niektóre dużą zasobów internetowych:

* DataCamp: DataCamp szkoleniowcem R wygodna przeglądarki z lekcje wideo i kodowania ćwiczeń. Brak interaktywnych samouczków najnowsze techniki R i pakietów. Podejmij wolnego interaktywnego samouczka R w https://www.datacamp.com/courses/introduction-to-r
* Przewodnik po uzyskiwanie wprowadzenie do języka R z Programiz https://www.programiz.com/r-programming
* Zawiera krótki samouczek R przez czarne Kelly uniwersytet Clarkson http://www.cyclismo.org/tutorial/R/
* R zasobów 60 + wymienionych w http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/r-quickstart/fig1.png
[2]: ./media/r-quickstart/fig2.png
[3]: ./media/r-quickstart/fig3.png
[4]: ./media/r-quickstart/fig4.png
[5]: ./media/r-quickstart/fig5.png
[6]: ./media/r-quickstart/fig6.png
[7]: ./media/r-quickstart/fig7.png
[8]: ./media/r-quickstart/fig8.png
[9]: ./media/r-quickstart/fig9.png
[10]: ./media/r-quickstart/fig10.png
[11]: ./media/r-quickstart/fig11.png
[12]: ./media/r-quickstart/fig12.png
[13]: ./media/r-quickstart/fig13.png
[14]: ./media/r-quickstart/fig14.png
[15]: ./media/r-quickstart/fig15.png
[16]: ./media/r-quickstart/fig16.png
[17]: ./media/r-quickstart/fig17.png
[18]: ./media/r-quickstart/fig18.png
[19]: ./media/r-quickstart/fig19.png
[20]: ./media/r-quickstart/fig20.png
[21]: ./media/r-quickstart/fig21.png
[22]: ./media/r-quickstart/fig22.png

[26]: ./media/r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
