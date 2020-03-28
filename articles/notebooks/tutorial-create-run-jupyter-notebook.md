---
title: Samouczek — tworzenie i uruchamianie notesu Jupyter — Azure Notebooks Preview
description: Dowiedz się, jak utworzyć i uruchomić notes Jupyter w usłudze Azure Notebooks Preview, który pokazuje proces regresji liniowej w naukach o danych.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 8a1c13f41ef1588b040b3540b852d83764c6ce79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75660821"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Samouczek: tworzenie i uruchamianie notesu Jupytera za pomocą języka Python

W tym samouczku przedstawiono proces tworzenia kompletnego notesu jupytera w procesie tworzenia kompletnego notesu jupirydowskiego, który pokazuje prostą regresję liniową. W trakcie tego samouczka zapoznajesz się z interfejsem użytkownika notesu Jupyter, który obejmuje tworzenie różnych komórek, uruchamianie komórek i prezentowanie notesu jako pokazu slajdów.

Ukończony notes można znaleźć w [usłudze GitHub — przykłady notesów platformy Azure.](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps) Ten samouczek zaczyna się jednak od nowego projektu i pustego notesu, dzięki czemu można doświadczyć tworzenia go krok po kroku.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie notesu projektu z przykładowymi danymi
> * Tworzenie różnych typów komórek za pomocą interfejsu notesu
> * Uruchamianie notesu
> * Zapisywanie notesu
> * Debugowanie notesu w programie Visual Studio Code

## <a name="create-the-project"></a>Tworzenie projektu

1. Przejdź do [usługi Azure Notess](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki start — logowanie się do notesów platformy Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na stronie profilu publicznego wybierz **pozycję Moje projekty** u góry strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję + **Nowy projekt** (skrót klawiaturowy: n); przycisk może pojawić **+** się tak, jakby okno przeglądarki było wąskie:

    ![Polecenie Nowy projekt na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym **wyskakującym** okienku Utwórz nowy projekt wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz:**

   - **Nazwa projektu**: Przykład regresji liniowej - Chirps krykieta
   - **Identyfikator projektu**: przykład regresji liniowej
   - **Projekt publiczny**: (wyczyszczone)
   - **Tworzenie README.md**: (wyczyszczone)

1. Po kilku chwilach notesy platformy Azure przechodzi do nowego projektu.

## <a name="create-the-data-file"></a>Tworzenie pliku danych

Model regresji liniowej utworzony w notesie pobiera dane z pliku w projekcie o nazwie *cricket_chirps.csv*. Można utworzyć ten plik, kopiując go z [usługi GitHub — przykłady notesów platformy Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)lub wprowadzając dane bezpośrednio. W poniższych sekcjach opisano oba podejścia.

### <a name="upload-the-data-file"></a>Przekazywanie pliku danych

1. Na pulpicie nawigacyjnym projektu w notesach platformy Azure wybierz pozycję **Przekaż** > **z adresu URL**
1. W wyskakującym okienku wprowadź następujący adres URL w **adresie URL pliku** i *cricket_chirps.csv* w **polu Nazwa pliku**, a następnie wybierz pozycję **Gotowe**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Plik *cricket_chirps.csv* powinien teraz pojawić się na liście plików projektu:

    ![Nowo utworzony plik CSV wyświetlany na liście plików projektu](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Tworzenie pliku od podstaw

1. Na pulpicie nawigacyjnym projektu w notesach platformy Azure wybierz pozycję **+ Nowy** > **pusty plik**
1. Na liście plików projektu pojawi się pole. Wprowadź *cricket_chirps.csv* i naciśnij klawisz Enter.
1. Kliknij prawym przyciskiem myszy *cricket_chirps.csv* i wybierz polecenie **Edytuj plik**.
1. W wyświetlonym edytorze wprowadź następujące dane:

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

1. Wybierz **pozycję Zapisz plik,** aby zapisać plik i powrócić do pulpitu nawigacyjnego projektu.

## <a name="install-project-level-packages"></a>Instalowanie pakietów na poziomie projektu

W notesie zawsze można użyć `!pip install` poleceń, takich jak w komórce kodu, aby zainstalować wymagane pakiety. Jednak takie polecenia są uruchamiane za każdym razem, gdy uruchamiasz komórki kodu notesu i może zająć dużo czasu. Z tego powodu zamiast tego można zainstalować pakiety `requirements.txt` na poziomie projektu przy użyciu pliku.

1. Użyj procesu opisanego w [polu Utwórz plik od podstaw,](#create-a-file-from-scratch) aby utworzyć plik o nazwie o następującej `requirements.txt` zawartości:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Można również przesłać `requirements.txt` plik z komputera lokalnego, jeśli wolisz, zgodnie z opisem na [Prześlij plik danych](#upload-the-data-file).

1. Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**.
1. W wyświetlonym wyskakującym okienku wybierz kartę **Środowisko,** a następnie wybierz pozycję **+Dodaj**.
1. W pierwszym formancie rozwijanej (operacji) w obszarze **Kroki konfiguracji środowiska**wybierz pozycję **Requirements.txt**.
1. W drugim formancie rozwijanej (nazwa pliku) wybierz *requirements.txt* (utworzony plik).
1. W trzeciej kontroli rozwijanej (wersja Pythona) wybierz **wersję Pythona 3.6**.
1. Wybierz **pozycję Zapisz**.

![Karta Środowisko ustawień projektu określająca plik requirements.txt](media/tutorial/tutorial-requirements-txt.png)

Po tym kroku konfiguracji każdy notes uruchomiony w projekcie będzie uruchamiany w środowisku, w którym są zainstalowane te pakiety.

## <a name="create-and-run-a-notebook"></a>Tworzenie i uruchamianie notesu

Po przygotowaniu pliku danych i ustawieniu środowiska projektu można teraz utworzyć i otworzyć notes.

1. Na pulpicie nawigacyjnym projektu wybierz pozycję **+ Nowy** > **notes**.
1. W wyskakującym okienku wprowadź *przykład regresji liniowej - Cricket Chirps.ipynb* dla **nazwy elementu**, wybierz Python **3.6** dla języka, a następnie **wybierz**nowy .
1. Po wyświetleniu nowego notesu na liście plików wybierz go, aby uruchomić notes. Nowa karta przeglądarki zostanie otwarta automatycznie.
1. Ponieważ masz plik *requirements.txt* w ustawieniach środowiska, zostanie wyświetlony komunikat "Oczekiwanie na zakończenie przygotowywania kontenera". Można wybrać **przycisk OK,** aby zamknąć wiadomość i kontynuować pracę w notesie; nie można jednak uruchomić komórek kodu, dopóki środowisko nie zostanie w pełni skonfigurowane.
1. Notes zostanie otwarty w interfejsie Jupyter z pojedynczą pustą komórką kodu jako domyślną.

    [![Początkowy widok nowego notesu w notesach platformy Azure](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Zwiedzanie interfejsu notesu

Po uruchomieniu notesu można dodawać komórki kodu i markdown, uruchamiać te komórki i zarządzać działaniem notesu. Najpierw jednak warto poświęcić kilka minut na zapoznanie się z interfejsem. Aby uzyskać pełną dokumentację, wybierz polecenie menu**Pomoc notesu** **pomocy.** > 

W górnej części okna widoczne są następujące elementy:

(A) Nazwa notesu, którą można edytować, klikając.
(B) Przyciski, aby przejść do projektu zawierającego i pulpitu nawigacyjnego projektów, które otwierają nowe karty w przeglądarce.
(C) Menu z poleceniami do pracy z notesem.
(D) pasek narzędzi ze skrótami do typowych operacji.
(E) kanwy edycji zawierającej komórki.
(F) wskaźnik, czy notes jest zaufany (domyślnie nie jest **zaufany).**
(G) jądro używane do uruchamiania notesu wraz ze wskaźnikiem aktywności.

[![Podstawowe obszary interfejsu użytkownika interfejsu Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter zapewnia wbudowaną wycieczkę podstawowych elementów interfejsu użytkownika. Rozpocznij wycieczkę, wybierając polecenie **Help** > **User Interface Tour** i klikając wyskakujące okienka.

Grupy poleceń menu są następujące:

| Menu | Opis |
| --- | --- |
| Plik | Polecenia do zarządzania plikiem notesu, w tym polecenia do tworzenia i kopiowania notesów, pokaż podgląd wydruku i pobrać notes w różnych formatach. |
| Edytuj | Typowe polecenia do wycinania, kopiowania i wklejania komórek, znajdowania i zamieniania wartości, zarządzania załącznikami komórek i wstawiania obrazów.  |
| Widok | Polecenia do kontrolowania widoczności różnych części interfejsu użytkownika Jupyter. |
| Insert | Polecenia wstawiania nowej komórki powyżej lub poniżej bieżącej komórki. Te polecenia są często używane podczas tworzenia notesu. |
| Komórka | Różne polecenia **Uruchom** uruchamiają jedną lub więcej komórek w różnych kombinacjach. Polecenia **Typ komórki** zmieniają typ komórki między **kodem**, **Znacznikami**i **Raw NBConvert** (zwykły tekst). Polecenia **Bieżące wyjścia** i **Wszystkie wyjścia** sterują sposóbem wyświetlenia danych wyjściowych z kodu uruchamiania i zawierają polecenie wyczyszczenie wszystkich danych wyjściowych. |
| Jądro | Polecenia do zarządzania, jak kod jest uruchamiany w jądrze, wraz z **Zmień jądro,** aby zmienić język lub wersję Języka Python używane do uruchomienia notesu. |
| Dane | Polecenia do przekazywania i pobierania plików z projektu lub sesji. Zobacz [Praca z plikami danych projektu](work-with-project-data-files.md) |
| Widżety | Polecenia do zarządzania [widżetami Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), które zapewniają dodatkowe możliwości wizualizacji, mapowania i drukowania.|
| Pomoc | Polecenia, które zapewniają pomoc i dokumentację dla interfejsu Jupyter. |

Większość poleceń na pasku narzędzi ma równoważne polecenia menu. Jednym z **wyjątków**jest Enter / Edit RISE Pokaz slajdów , który jest omawiany na Udostępnianie i [prezentować notesy](present-jupyter-notebooks-slideshow.md).

Wiele z tych poleceń należy używać podczas wypełniania notesu w kolejnych sekcjach.

## <a name="create-a-markdown-cell"></a>Tworzenie komórki Znacznikdown

1. Kliknij pierwszą pustą komórkę wyświetlaną na kanwie notesu. Domyślnie komórka jest typem **kodu,** co oznacza, że jest przeznaczona do przechowywania kodu możliwego do uruchomienia dla wybranego jądra (Python, R lub F#). Bieżący typ jest wyświetlany z listy rozwijanej typu na pasku narzędzi:

    ![Listy rozwijanej paska narzędzi typu komórki](media/tutorial/tutorial-cell-type-drop-down.png)

1. Zmień typ komórki na **Markdown** za pomocą listy rozwijanej paska narzędzi; alternatywnie **Cell** > użyj polecenia menu Oznacz**typ** > komórki**komórki:**

    ![Polecenie menu typ komórki](media/tutorial/tutorial-cell-type-menu.png)

1. Kliknij komórkę, aby rozpocząć edycję, a następnie wprowadź następujące markdown:

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

1. Aby renderować markdown do kodu HTML dla przeglądarki, wybierz polecenie **Uruchom** na pasku narzędzi lub użyj polecenia **Komórki** > **uruchamiania komórek.** Kod Markdown do formatowania i łączy jest teraz wyświetlany zgodnie z oczekiwaniami w przeglądarce.

1. Po uruchomieniu ostatniej komórki w notesie, Jupyter automatycznie tworzy nową komórkę poniżej tej, którą uruchomiono. Umieść więcej Markdown w tej komórce, powtarzając kroki opisane w tej sekcji z następującym markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Aby ponownie edytować markdown, kliknij dwukrotnie w renderowanym komórce. Aby ponownie renderować kod HTML po wsznuniu zmian, uruchom komórkę.

## <a name="create-a-code-cell-with-commands"></a>Tworzenie komórki kodu z poleceniami

Jak wyjaśniono w poprzedniej komórce Markdown, można dołączyć polecenia bezpośrednio do notesu. Za pomocą poleceń można instalować pakiety, uruchamiać curl lub wget, aby pobrać dane lub cokolwiek innego. Notebooki Jupyter skutecznie działają w obrębie maszyny wirtualnej Systemu Linux, dzięki czemu masz pełne polecenie Linuksa ustawione do pracy.

1. Wprowadź poniższe polecenia w komórce kodu, która pojawiła się po użyciu **Uruchom** w poprzedniej komórce Markdown. Jeśli nie widzisz nowej komórki, utwórz ją z **poleceniem Wstaw** > **komórkę poniżej** lub użyj przycisku **+** na pasku narzędzi.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Przed uruchomieniem komórki utwórz nową **+** komórkę z przyciskiem na pasku narzędzi, ustaw ją na Markdown i wprowadź następujące wyjaśnienie:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Zaznacz polecenie **Cell** > **Run All,** aby uruchomić wszystkie komórki notesu. Zwróć uwagę, że komórki Markdown są renderowane jako HTML, a polecenie jest uruchamiane w jądrze i obserwuj wskaźnik jądra zgodnie z opisem w samym Znaczniku:

    ![Wskaźnik zajętości jądra notebooka](media/tutorial/tutorial-kernel-busy.png)

1. Jest to również trochę czasu `pip install` dla wszystkich poleceń do uruchomienia, a ponieważ już zainstalowane te pakiety w środowisku projektu (i ponieważ są one również zawarte w notesach platformy Azure domyślnie), można wyświetlić wiele komunikatów z napisem "Wymagania już spełnione." Wszystkie te dane wyjściowe mogą rozpraszać wzrokowo, więc zaznacz tę **Cell** > komórkę (za pomocą jednego kliknięcia), a następnie > **przełącz** **dane wyjściowe komórki,** aby ukryć dane wyjściowe. Można również użyć polecenia **Wyczyść** w tym samym podmenu, aby całkowicie usunąć dane wyjściowe.

    **Polecenie Przełącz** ukrywa tylko najnowsze dane wyjściowe z komórki; Po ponownym uruchomieniu komórki dane wyjściowe pojawią się ponownie.

1. Ponieważ pakiety są zainstalowane w środowisku `! pip install` projektu, `#`skomentuj polecenia za pomocą ; w ten sposób mogą pozostać w notesie jako materiał instruktażowy, ale nie zajmie czasu, aby uruchomić i nie przyniesie niepotrzebnych danych wyjściowych. W takim przypadku zachowanie skomentowanych poleceń w notesie wskazuje również zależności notesu.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Tworzenie pozostałych komórek

Aby wypełnić pozostałą część notesu, należy następnie utworzyć serię komórek Markdown i kod. Dla każdej komórki wymienionej poniżej najpierw utwórz nową komórkę, a następnie ustaw typ, a następnie wklej zawartość.

Chociaż po utworzeniu każdej komórki można poczekać na uruchomienie notesu, warto uruchomić każdą komórkę podczas jej tworzenia. Nie wszystkie komórki pokazują dane wyjściowe; Jeśli nie widzisz żadnych błędów, załóżmy, że komórka działała normalnie.

Każda komórka kodu zależy od kodu, który został uruchomiony w poprzednich komórkach, a jeśli zaniedbujesz uruchomienie jednej z komórek, późniejsze komórki mogą powodować błędy. Jeśli okaże się, że zapomniano uruchomić komórkę, spróbuj użyć **komórki** > **Uruchom wszystkie powyżej** przed uruchomieniem bieżącej komórki.

Jeśli widzisz nieoczekiwane wyniki (które prawdopodobnie będzie!), sprawdź, czy każda komórka jest ustawiona na "Kod" lub "Markdown" w razie potrzeby. Na przykład błąd "Nieprawidłowa składnia" zwykle występuje po wprowadzeniu znacznika do komórki Kod.

1. Komórka znaczników:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Komórka kodu; po uruchomieniu pokazuje zawartość tabeli jako dane wyjściowe. Można pominąć dane wyjściowe, `print` komentując instrukcję.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Mogą pojawić się ostrzeżenia z tego kodu o "numpy.dtype rozmiar zmieniony"; ostrzeżenia mogą być bezpiecznie ignorowane.

1. Komórka znaczników:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Komórka kodu; po uruchomieniu ta komórka nie ma danych wyjściowych.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Komórka znaczników:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Komórka kodu; po uruchomieniu ta komórka pokazuje `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`dane wyjściowe, .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Komórka znaczników:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Komórka kodu; po uruchomieniu ta komórka `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`pokazuje wyniki, takie jak .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Komórka znaczników:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Komórka znaczników:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Komórka kodu; po uruchomieniu ta komórka tworzy wykres graficzny. Jeśli wykres nie jest widoczny za pierwszym razem (a zamiast tego zobacz "Rysunek o rozmiarze 640x480 z 1 osiami"), uruchom ponownie komórkę.

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

    ![Wydrukuj dane wyjściowe z kodu matplotlib](media/tutorial/tutorial-plot-output.png)

1. Komórka znaczników:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Czyszczenie wyjść i ponowne ścieranie wszystkich komórek

Po wykonać kroki opisane w poprzedniej sekcji, aby wypełnić cały notes, utworzono zarówno kawałek uruchomionego kodu w kontekście pełnego samouczka na temat regresji liniowej. To bezpośrednie połączenie kodu i tekstu jest jedną z największych zalet notebooków!

Spróbuj teraz ponownie uruchomić cały notes:

1. Wyczyść wszystkie dane sesji jądra i wszystkie dane wyjściowe komórek, wybierając opcję Uruchom ponownie **jądro** > **& wyczyść wyjście**. To polecenie jest zawsze dobre do uruchomienia po zakończeniu notesu, aby upewnić się, że nie utworzono żadnych dziwnych zależności między komórkami kodu.

1. Uruchom ponownie notes przy użyciu **funkcji Cell** > **Run All**. Zwróć uwagę, że wskaźnik jądra jest wypełniony, gdy kod jest uruchomiony.

    Jeśli masz kod, który działa zbyt długo lub w inny sposób utknie, możesz zatrzymać jądro za pomocą polecenia**Przerwanie** **jądra.** > 

1. Przewiń notes, aby sprawdzić wyniki. (Jeśli wykres ponownie się nie pojawi, uruchom ponownie tę komórkę).

## <a name="save-halt-and-close-the-notebook"></a>Zapisywanie, zatrzymywki i zamykanie notesu

Podczas edytowania notesu można zapisać jego bieżący stan za pomocą polecenia Zapisz **pliki** > **i Punktu kontrolnego** lub przycisku Zapisz na pasku narzędzi. "Punkt kontrolny" tworzy migawkę, do której można powrócić w dowolnym momencie podczas sesji. Punkty kontrolne umożliwiają wprowadzenie serii eksperymentalnych zmian, a jeśli te zmiany nie działają, można po prostu powrócić do punktu kontrolnego za pomocą polecenia **Powrót pliku** > **do punktu kontrolnego.** Alternatywne podejście polega na tworzeniu dodatkowych komórek i komentowaniu kodu, którego nie chcesz uruchamiać; tak czy inaczej działa.

Polecenia **File** > Make a Copy można również w dowolnym**momencie** wykonać kopię bieżącego stanu notesu w nowym pliku w projekcie. Ta kopia zostanie automatycznie otwarta na nowej karcie przeglądarki.

Po zakończeniu pracy z notesem użyj polecenia**Zamknij i zatrzymaj** **plik,** > które zamyka notes i zamyka jądro, które go uruchamia. Usługi Azure Notebooks następnie automatycznie zamyka kartę przeglądarki.

## <a name="debug-notebooks-using-visual-studio-code"></a>Debugowanie notesów przy użyciu kodu programu Visual Studio

Jeśli komórki kodu w notesie nie zachowują się w sposób oczekiwany, mogą mieć błędy kodu lub inne wady. Jednak inne niż `print` przy użyciu instrukcji, aby pokazać wartość zmiennych, typowe środowisko Jupyter nie oferuje żadnych urządzeń debugowania.

Na szczęście można pobrać plik *.ipynb* notesu, a następnie otworzyć go w programie Visual Studio Code przy użyciu rozszerzenia Python. Rozszerzenie bezpośrednio importuje notes jako pojedynczy plik kodu, zachowując komórki Markdown w komentarzach. Po zaimportowaniu notesu można użyć debugera kodu programu Visual Studio, aby przejść przez kod, ustawić punkty przerwania, zbadać stan i tak dalej. Po dokonaniu poprawek do kodu, następnie wyeksportować plik *.ipynb* z programu Visual Studio Code i przekazać go z powrotem do notesów platformy Azure.

Aby uzyskać więcej informacji, zobacz [Debugowanie notesu Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) w dokumentacji kodu programu Visual Studio.

Zobacz też [Visual Studio Code — Obsługa jupyter](https://code.visualstudio.com/docs/python/jupyter-support) dodatkowe funkcje visual studio kod dla notesów Jupyter.

## <a name="next-steps"></a>Następne kroki

- [Explore sample notebooks (Eksplorowanie przykładowych notesów)](azure-notebooks-samples.md)

Artykuły z poradami:

- [Tworzenie i klonowanie projektów](create-clone-jupyter-notebooks.md)
- [Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
- [Praca z plikami danych](work-with-project-data-files.md)
- [Uzyskiwanie dostępu do zasobów danych](access-data-resources-jupyter-notebooks.md)
- [Używanie usługi Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
