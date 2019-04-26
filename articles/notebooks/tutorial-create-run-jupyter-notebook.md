---
title: Samouczek — tworzenie i uruchamianie notesu programu Jupyter na platformie Azure
description: Jak utworzyć uruchomienia notesu programu Jupyter w notesach platformy Azure, który demonstruje proces regresji liniowej w zakresie analizy danych.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: d5ccf3e9f35a8d35387962278577333ff92ff02b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238231"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Samouczek: tworzenie i uruchamianie notesu Jupyter przy użyciu języka Python

Ten samouczek przeprowadzi Cię przez proces przy użyciu notesów usługi Azure do utworzenia pełne notesu programu Jupyter, który pokazuje prosty regresji liniowej. W ramach tego samouczka możesz zapoznać się z notesu programu Jupyter interfejsu użytkownika, w tym tworzenie różnych komórek, uruchamianie komórek i umożliwienie korzystania z notesu jako pokaz slajdów.

Ukończone notesu można znaleźć na [GitHub - Samples notesów usługi Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Ten samouczek rozpoczyna się od nowy projekt i pusty notes jednak, dzięki czemu możesz skorzystać z jej tworzenia, krok po kroku.

## <a name="create-the-project"></a>Tworzenie projektu

1. Przejdź do [notesów usługi Azure](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać więcej informacji, zobacz [Szybki Start — Zaloguj się do platformy Azure, notesy](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie profilu publicznego, wybierz **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na **Moje projekty** wybierz opcję **+ nowy projekt** (skrót klawiaturowy: n); przycisku może występować tylko jako **+** Jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W **Utwórz nowy projekt** okna podręcznego, który pojawia się, wprowadź lub ustaw następujące szczegóły, a następnie wybierz **Utwórz**:

    - **Nazwa projektu**: Przykład regresji liniowej — Cricket Chirps
    - **Identyfikator projektu**: przykład liniowej regresji
    - **Projekt publicznych**: (usunięty)
    - **Utwórz plik README.md**: (usunięty)

1. Po kilku chwilach notesy platformy Azure przechodzi należy do nowego projektu.

## <a name="create-the-data-file"></a>Tworzenie pliku danych

Tworzenie notesu uczenia modelu regresji liniowej rysuje danych z pliku w projekcie o nazwie *cricket_chirps.csv*. Ten plik można utworzyć albo przez skopiowanie go z [GitHub - Samples notesów usługi Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), lub wprowadzając dane bezpośrednio. W poniższych sekcjach opisano oba podejścia.

### <a name="upload-the-data-file"></a>Przekaż plik danych

1. Na pulpicie nawigacyjnym projektu w notesach platformy Azure, wybierz **przekazywanie** > **z adresu URL**
1. W okienku wyskakującym wprowadź następujący adres URL w **adres URL pliku** i *cricket_chirps.csv* w **nazwy pliku**, a następnie wybierz **gotowe**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. *Cricket_chirps.csv* plik teraz powinien być widoczny na liście plików projektu:

    ![Nowo utworzony plik CSV, wyświetlane na liście plików projektu](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Utwórz plik od podstaw

1. Na pulpicie nawigacyjnym projektu w notesach platformy Azure, wybierz **+ nowy** > **pusty plik**
1. Pole jest wyświetlane na liście plików projektu. Wprowadź *cricket_chirps.csv* i naciśnij klawisz Enter.
1. Kliknij prawym przyciskiem myszy *cricket_chirps.csv* i wybierz **Edytuj plik**.
1. W edytorze, który pojawi się wprowadź następujące dane:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Wybierz **Zapisz plik** Zapisz plik i powróć do pulpitu nawigacyjnego projektu.

## <a name="install-project-level-packages"></a>Instalowanie pakietów na poziomie projektu

W notesie, zawsze można użyć poleceń, takich jak `!pip install` w komórce kodu, aby zainstalować wymagane pakiety. Jednak takie polecenia są uruchamiane za każdym razem, gdy uruchomienia komórki kodu w notesie, a może zająć znaczną ilość czasu. Z tego powodu, możesz zamiast tego zainstalować pakiety na poziomie projektu przy użyciu `requirements.txt` pliku.

1. Użyj procesu opisanego w [Utwórz plik od podstaw](#create-a-file-from-scratch) do utworzenia pliku o nazwie `requirements.txt` z następującą zawartością:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Możesz również przekazać `requirements.txt` plików z komputera lokalnego, jeśli wolisz, zgodnie z opisem na [przekazywanie pliku danych](#upload-the-data-file).

1. Na pulpicie nawigacyjnym projektu wybierz **ustawienia projektu**.
1. W wyświetlonym oknie podręcznym, zaznacz **środowiska** kartę, a następnie wybierz **+ Dodaj**.
1. W pierwszej kontrolce listy rozwijanej (operacji) w obszarze **kroków konfiguracji środowiska**, wybierz **Requirements.txt**.
1. W drugiej kontrolce listy rozwijanej (nazwa pliku) wybierz *requirements.txt* (plik utworzono).
1. W trzecim kontrolka listy rozwijanej (wersja języka Python) wybierz **środowisko Python w wersji 3.6**.
1. Wybierz pozycję **Zapisz**.

![Na karcie środowisko ustawień projektu Określanie pliku requirements.txt](media/tutorial/tutorial-requirements-txt.png)

Ten krok konfiguracji w miejscu każdego dostępnego po uruchomieniu w projekcie będą uruchamiane w środowisku zainstalowanym te pakiety.

## <a name="create-and-run-a-notebook"></a>Tworzenie i uruchamianie notesu

Dzięki zestawowi środowiska projektu i pliku danych, które jest gotowy można tworzyć i otworzyć notes.

1. Na pulpicie nawigacyjnym projektu wybierz **+ nowy** > **notesu**.
1. W okienku wyskakującym wprowadź *liniowej przykład regresji — Cricket Chirps.ipynb* dla **nazwa elementu**, wybierz **środowiska Python 3.6** w języku — a następnie wybierz pozycję **New**.
1. Gdy nowy notes zostanie wyświetlony na liście plików, wybierz go do uruchamiania notesu. Automatycznie zostanie otwarta nowa karta przeglądarki.
1. Ponieważ masz *requirements.txt* plików w ustawieniach środowiska zostanie wyświetlony komunikat "Oczekiwanie na zakończenie kontener przygotowywany." Możesz wybrać **OK** Zamknij komunikat i kontynuować pracę w notesie; nie można uruchomić komórki kodu, jednak, dopóki środowisko jest w pełni skonfigurowany.
1. Notes zostanie otwarty w interfejsie Jupyter, z jednym pustej komórce kodu jako domyślny.

    [![Widok początkowy nowy notes w notesach platformy Azure](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Przewodnik po interfejsie notesu

Z notesem uruchomiony możesz dodać kod komórek języka znaczników Markdown, uruchom tych komórek i zarządzać działaniem notesu. Po pierwsze jednak warto trwa kilka minut na zapoznanie się z interfejsem. Pełną dokumentację można wybrać **pomocy** > **pomocy notesu** polecenia menu.

Wzdłuż górnej części okna, zostaną wyświetlone następujące elementy:

(A) Nazwa notesu, który można edytować, klikając przycisk.
(B) przycisków, aby przejść do projektu zawierającego i pulpit nawigacyjny projekty, które otwarcie nowej karty w przeglądarce.
Menu (C) A za pomocą polecenia do pracy z notesem.
(D) pasek narzędzi za skrótów klawiaturowych dla typowych operacji.
(E) edycji kanwy zawierających komórki.
(F) wskazuje, czy jest zaufany Notes (wartość domyślna to **zaufany**).
(G) jądra używane do uruchamiania notesu wraz z wskaźnik aktywności.

[![Główne obszary interfejsu użytkownika interfejsu programu Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter zapewnia wbudowane Przewodnik po przykładzie podstawowe elementy interfejsu użytkownika. Uruchom samouczek, wybierając **pomocy** > **samouczek interfejsu użytkownika** polecenia i klikając okna podręczne.

Grupy poleceń menu są następujące:

| Menu | Opis |
| --- | --- |
| Plik | Polecenia do zarządzania pliku Notes, włącznie z poleceniami, aby utworzyć i kopiowanie notesów, Pokaż podgląd wydruku i Pobierz Notes w różnych formatach. |
| Edytuj | Typowe polecenia w celu wycinania, kopiowania i wklejać komórki, Znajdź i Zamień wartości, Zarządzaj załącznikami komórki i wstawiać obrazy.  |
| Widok | Polecenia służące do sterowania widocznością różnych części interfejsu użytkownika programu Jupyter. |
| Insert | Polecenia, aby wstawić nową komórkę powyżej lub poniżej bieżącej komórki. Możesz użyć tych poleceń, często używany podczas tworzenia notesu. |
| Komórki | Różne **Uruchom** polecenia są uruchamiane w różnych kombinacjach co najmniej jedną komórkę. **Typ komórki** polecenia Zmień typ komórki między **kodu**, **Markdown**, i **pierwotne NBConvert** (zwykły tekst). **Bieżące dane wyjściowe** i **wszystkie dane wyjściowe** polecenia Sterowanie dane wyjściowe wykonywania kodu jest wyświetlany i uwzględnić polecenia, aby wyczyścić wszystkie dane wyjściowe. |
| Jądra | Polecenia do zarządzania, jak kod jest uruchamiany w jądrze, wraz z **jądra zmiany** do zmiany języka lub wersji języka Python, używane do uruchamiania notesu. |
| Dane | Polecenia, aby przekazywać i pobierać pliki z projektu lub sesji. Zobacz [Praca z plikami danych projektu](work-with-project-data-files.md) |
| elementy widget | Polecenia do zarządzania [widżetów Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), które zapewniają dodatkowe możliwości dla wizualizacji, mapowanie i rysowanie.|
| Pomoc | Polecenia, które zapewniają pomoc i dokumentacja interfejsu Jupyter. |

Większość poleceń na pasku narzędzi mają odpowiednie polecenia z menu. Jedynym wyjątkiem jest **Enter/Edytuj ROSNĄĆ w pokaz slajdów**, omówiony w [udziału i notesy obecne](present-jupyter-notebooks-slideshow.md).

Wykorzystanie wielu tych poleceń, jak wypełnić Notes w kolejnych sekcjach.

## <a name="create-a-markdown-cell"></a>Utwórz komórki języka znaczników Markdown

1. Klikaj pierwszą pustą komórkę wyświetlane na kanwie notesu. Domyślnie jest komórki **kodu** typ, który oznacza, że zostało zaprojektowane do zawierają możliwe do uruchomienia kodu dla wybranych jądra (Python, R, lub F#). Bieżącym typem jest wyświetlany w typie listy rozwijanej na pasku narzędzi:

    ![Pasek narzędzi listy rozwijanej typ komórki](media/tutorial/tutorial-cell-type-drop-down.png)

1. Zmień typ komórki na **Markdown** przy użyciu narzędzi listy rozwijanej; możesz też skorzystać **komórki** > **typ komórki**  >   **Markdown** polecenia menu:

    ![Polecenia menu Typ komórki](media/tutorial/tutorial-cell-type-menu.png)

1. Kliknij w komórce w celu rozpoczęcia edycji, a następnie wprowadź następujący kod Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Aby renderować języku znaczników Markdown w kodzie HTML do przeglądarki, zaznacz **Uruchom** polecenia na pasku narzędzi lub użyj **komórki** > **Uruchom komórek** polecenia. Kod języka znaczników Markdown do formatowania i łącza są teraz wyświetlane zgodnie z oczekiwaniami ich w przeglądarce.

1. Po uruchomieniu ostatniej komórki w notesie Jupyter automatycznie tworzy nową komórkę poniżej, które było uruchomione. Umieścić więcej języka znaczników Markdown w tej komórce, powtarzając kroki w tej części następujący kod Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Ponownie edytować języku znaczników Markdown, kliknij dwukrotnie w komórce renderowany. Aby renderować HTML ponownie po wprowadzeniu zmian, należy uruchomić komórkę.

## <a name="create-a-code-cell-with-commands"></a>Tworzenie komórkę kodu za pomocą polecenia

Jak wyjaśniono poprzedniej komórki języka znaczników Markdown, można umieścić bezpośrednio w notesie. Polecenia można użyć do zainstalowania pakietów, uruchom narzędzie curl lub wget, aby pobrać dane lub cokolwiek innego. Notesów programu Jupyter efektywnej pracy w przypadku maszyn wirtualnych systemu Linux pozwala uzyskać pełnej Linux zestaw poleceń chcesz pracować.

1. Wpisz poniższe polecenia w komórce kodu, które pojawiło się po **Uruchom** na poprzedniej komórki języka znaczników Markdown. Jeśli nie widzisz nową komórkę, zrób to za pomocą **Wstaw** > **Wstaw komórkę poniżej** lub użyj **+** przycisk na pasku narzędzi.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Przed uruchomieniem komórki, Utwórz nową komórkę z **+** znajdujący się na pasku narzędzi, ustaw ją na języka znaczników Markdown i wprowadź poniżej informacje:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Wybierz **komórki** > **Uruchom wszystkie** polecenie, aby uruchomić wszystkie komórki w notesie. Zauważ, że komórek Markdown renderowane jako HTML i polecenia uruchamiane w jądrze i sprawdź, czy wskaźnik jądra zgodnie z opisem w języku znaczników Markdown, sama:

    ![Wskaźnik zajętości dla jądra notesu](media/tutorial/tutorial-kernel-busy.png)

1. Zajmuje trochę czasu dla wszystkich `pip install` polecenia do uruchomienia, a ponieważ zainstalowane te pakiety w środowisku projektu (i są one również dołączone w notesach Azure domyślnie), zobacz wiele komunikatów, które odczytują "wymagania jeszcze spełniony." Wszystkie te dane wyjściowe można wzrokowo rozprasza uwagę, dlatego należy wybierać, sprzedawanie, (za pomocą jednego kliknięcia), a następnie użyj **komórki** > **komórkę w danych wyjściowych** > **Przełącz**Aby ukryć dane wyjściowe. Można również użyć **wyczyść** na tej samej podmenu, aby całkowicie usunąć dane wyjściowe polecenia.

    **Przełącz** polecenia ukrywa tylko najbardziej aktualne dane wyjściowe z komórki; ponownie uruchomić komórkę danych wyjściowych zostanie wyświetlony ponownie.

1. Ponieważ te pakiety są zainstalowane w środowisku projektu, komentarz `! pip install` polecenia przy użyciu `#`; w ten sposób ich może pozostać w notesie jako materiałów szkoleniowych, ale nie będzie skorzystać w dowolnym momencie do uruchomienia i nie generuje niepotrzebne danych wyjściowych. W takim przypadku zachowanie polecenia komentarze w notesie wskazuje także zależności notesu.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Tworzenie pozostałych komórek

Aby wypełnić pozostałe Notes, możesz utworzyć szereg języka znaczników Markdown i komórki kodu. Dla każdej komórki wymienione poniżej najpierw utwórz nową komórkę, Ustaw typ, a następnie wklej zawartość.

Chociaż możesz zaczekać, aby uruchomić Notes, po utworzeniu każdej komórki, jest interesujące do uruchomienia każdej komórki podczas jej tworzenia. Nie wszystkie komórki Pokaż dane wyjściowe; Jeśli nie widzisz błędy przyjęto założenie, że komórki został uruchomiony normalnie.

Każda komórka kod zależy od kodu uruchamianego w poprzedniej komórki, a pominięcie Uruchom jedną z komórek, nowsze komórek może powodować błędy. Jeśli okaże się, czy pamiętasz uruchomić komórkę, spróbuj użyć **komórki** > **Uruchom wszystkie powyższe** przed uruchomieniem bieżącej komórki.

Jeśli widzisz nieoczekiwane wyniki, (które prawdopodobnie będą!), sprawdź, czy każda komórka jest ustawione na "Code" lub "Markdown" zgodnie z potrzebami. Na przykład błąd "Nieprawidłowa składnia" zazwyczaj występuje po wprowadzeniu języka znaczników Markdown w komórce kodu.

1. Komórka markdown:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Komórkę kodu; Po uruchomieniu spisu treści jest wyświetlany jako dane wyjściowe. Dane wyjściowe można pominąć, zakomentowując `print` instrukcji.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Może zostać wyświetlony ostrzeżenia dotyczące tego kodu o "zmienić rozmiar numpy.dtype"; można bezpiecznie zignorować ostrzeżenia.

1. Komórka markdown:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Komórkę kodu; Po uruchomieniu tej komórki nie ma wyjściowego.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Komórka markdown:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. Komórkę kodu; Po uruchomieniu ta komórka zawiera dane wyjściowe, `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Komórka markdown:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Komórkę kodu; Po uruchomieniu tej komórki przedstawia wyniki, takie jak `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Komórka markdown:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Komórka markdown:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Komórkę kodu; Po uruchomieniu tej komórki tworzy wykres graficzny. Jeśli nie sprawdzić czas kreślenia pierwszy (i zamiast tego zobacz "Rozmiar rysunek 640 x 480 z osiami 1"), należy ponownie uruchomić komórkę.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Dane wyjściowe kodu matplotlib wykres](media/tutorial/tutorial-plot-output.png)

1. Komórka markdown:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Wyczyść dane wyjściowe i uruchom ponownie wszystkie komórki

Po wykonaniu czynności opisane w poprzedniej sekcji, aby wypełnić całe Notes, utworzono zarówno element uruchamianie kodu w kontekście pełnym samouczku dotyczącym na regresji liniowej. Ta kombinacja bezpośredniego kodu i tekstu jest jedną z zalet notesów!

Wypróbuj teraz ponowne uruchomienie całego Notes:

1. Wyczyść wszystkie jądra dane sesji, a wszystkie komórki danych wyjściowych, wybierając **jądra** > **Wyczyść dane wyjściowe & ponownego uruchomienia**. To polecenie jest zawsze zalecana do uruchomienia po zakończeniu Notes, po prostu, aby upewnić się, że nie utworzono żadnych dziwne zależności między komórki kodu.

1. Ponownie przy użyciu notesu **komórki** > **Uruchom wszystkie**. Zwróć uwagę, że wskaźnik jądra jest wypełnione, gdy kod jest uruchomiony.

    Jeśli masz wszelki kod, który jest uruchamiany zbyt długo lub w przeciwnym razie zablokowania, można zatrzymać jądra za pomocą **jądra** > **przerwań** polecenia.

1. Przewiń Notes, aby przejrzeć wyniki. (Jeśli ponownie wykres nie jest widoczna, należy ponownie uruchomić tę komórkę.)

## <a name="save-halt-and-close-the-notebook"></a>Zapisz, wstrzymać i zamknąć notes

Podczas edytowania notesu można zapisać jego bieżący stan z **pliku** > **zapisywanie i punktu kontrolnego** polecenia lub Zapisz przycisk na pasku narzędzi. "Punkt kontrolny" tworzy migawkę, możesz przywrócić w dowolnym momencie podczas sesji. Punkty kontrolne umożliwiają szereg zmian eksperymentalne i jeśli te zmiany nie działa, możesz po prostu przywrócić punktu kontrolnego przy użyciu **pliku** > **przywrócić punktu kontrolnego** polecenia. Alternatywne podejście dotyczy tworzenie dodatkowych komórek i komentarz dla jakiegokolwiek kodu, który nie ma być uruchomione. w obu przypadkach działa.

Można również użyć **pliku** > **Utwórz kopię** polecenie w dowolnym momencie, aby utworzyć kopię bieżącego stanu Notes do nowego pliku w projekcie. Tej kopii automatycznie otwiera się na nowej karcie przeglądarki.

Po zakończeniu korzystania z notesu, użyj **pliku** > **Zamknij i zatrzymania** polecenia, które powoduje zamknięcie notesu i zamyka jądra, która działa go. Notesy platformy Azure następnie na karcie przeglądarki zostanie automatycznie zamknięte.

## <a name="debug-notebooks-using-visual-studio-code"></a>Debugowanie notesów programu Visual Studio Code

Jeśli komórki kodu w notesie nie działają w taki sposób, w których oczekujesz, możesz mieć kod błędy lub inne usterki. Jednak inne niż przy użyciu `print` instrukcji, aby wyświetlić wartości zmiennych, typowe środowisko Jupyter nie oferuje żadnych urządzeń debugowania.

Na szczęście możesz pobrać notesu *.ipynb* pliku, a następnie otwórz go w programie Visual Studio Code za pomocą rozszerzenia języka Python. Rozszerzenie bezpośrednio importuje notesu jako plik pojedynczego kodu, zachowując swoje komórek języka znaczników Markdown w komentarzach. Po zaimportowaniu Notes, można użyć debugera programu Visual Studio Code, można przejść przez kod, ustawić punkty przerwania, sprawdź stan i tak dalej. Po wprowadzeniu poprawek w kodzie, można następnie wyeksportować *.ipynb* pliku z programu Visual Studio Code, a następnie przekaż go do notesów usługi Azure.

Aby uzyskać więcej informacji, zobacz [debugowania notesu programu Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) w dokumentacji programu Visual Studio Code.

Zobacz też [programu Visual Studio Code — Obsługa Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) do dodatkowych funkcji programu Visual Studio Code dla notesów programu Jupyter.

## <a name="next-steps"></a>Kolejne kroki

- [Eksploruj przykładowe notesów](azure-notebooks-samples.md)

Artykuły z poradami:

- [Tworzenie i klonowanie projektów](create-clone-jupyter-notebooks.md)
- [Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
- [Praca z plikami danych](work-with-project-data-files.md)
- [Uzyskiwanie dostępu do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Korzystanie z usług Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
