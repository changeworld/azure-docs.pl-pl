---
title: Szeregów czasowych żądanie energii prognozowania | Dokumentacja firmy Microsoft
description: Jak stosować prognozy szeregu czasowego żądanie energii w konsoli usługi Azure Machine Learning Workbench uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: b40ebba3c85760d947861b4215dab6e16a86002b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="energy-demand-time-series-forecasting"></a>Szeregów czasowych żądanie energii prognozowania


Prognozowanie serii czasu jest zadanie przewidywania przyszłych wartości w sekwencji uporządkowane czasu uwag. Jest to powszechny problem i współpracuje z aplikacjami, w wielu branżach. Na przykład konieczne przewidywania przyszłych produktów, sprzedaż, ich skutecznie organizowanie ich łańcuchami, aby spełnić wymagania firm handlowych. Podobnie firm dostarczania pakietu należy oszacować żądanie dla swoich usług, aby zaplanować wymagania pracowników i tras dostaw wcześniejsze. W wielu przypadkach może być istotne ryzyka finansowego prognoz niedokładne. W związku z tym prognozowania jest często krytyczne działalności.

W tym przykładzie pokazano, jak prognozowania szeregu czas mogą być wykonywane za pośrednictwem stosowania technik learning maszyny. Zapoznasz się na każdym kroku modelowania procesu w tym:
- Przygotowywanie danych do czyszczenia i formatowania danych;
- Tworzenie funkcji dla modeli z danych serii raw czasu; uczenia maszynowego
- Uczenie różne modele uczenia maszyny;
- Ocena modele przez porównanie ich działania w zestawie danych przechowywane poza testu; a
- Operacyjnych najlepsze modelu, udostępniając za pośrednictwem usługi sieci web do wygenerowania prognoz na żądanie.

Azure Machine Learning Workbench ułatwia ten proces modelowania w każdym kroku: 
- Pokazano, jak wprowadzić środowisko notesu Jupyter — dostępne bezpośrednio z za pomocą narzędzia Workbench — tworzenie łatwiejsze kodu języka Python. Proces tworzenia modelu można wyjaśnić innym dokładniej przy użyciu języka znaczników markdown adnotacje i wykresy. Te komputery przenośne można wyświetlać, edytować i wykonać bezpośrednio z poziomu Workbench.
- Modele przeszkolone można utrwalenia i przekazać do magazynu obiektów blob. Dzięki temu naukowca danych, aby śledzić uczonego modelu obiektów i upewnij się, że są one zachowane i pobieranie, w razie potrzeby.
- Metryki mogą być rejestrowane podczas wykonywania skryptu języka Python, włączanie naukowca danych rejestrować wyniki wydajności modelu.
- Workbench tworzy tabele można dostosowywać zarejestrowane metryk, dzięki czemu naukowca danych można łatwo porównać metryki wydajności modelu. Wykresy są automatycznie wyświetlane, można łatwo zidentyfikować najbardziej modelu.
- Na koniec pokazano, jak uczonego modelu można operationalized przez wdrożenie jej w czasie rzeczywistym usługi sieci web.

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii
W tym scenariuszu rzeczywistych publicznego repozytorium GitHub zawiera wszystkich materiałów, w tym przykłady kodu, potrzebne w tym przykładzie:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Omówienie przypadków użycia

W tym scenariuszu skupiono się na zapotrzebowania na energię prognozowania, której celem jest do przewidywania przyszłych obciążenia na siatce energii. Jest to operacja krytycznym dla firm z sektora energetycznego jak operatory należy obsługiwać poprawnie równowagi między energii zużytej na siatce i energii dostarczonych. Za dużo zasilania siatki może spowodować odpady energii lub błędów technicznych. Jednak jeśli podano za mało zasilania może prowadzić do blackouts, pozostawiając klientów bez zasilania. Zazwyczaj operatory siatki może zająć krótkoterminowej decyzje dotyczące zarządzania zasilania do siatki i należy równoważenia obciążenia. Dokładne krótkoterminowej Prognoza zapotrzebowania na energię w związku z tym jest niezbędne dla operatora podejmowanie tych decyzji bez obaw.

Ten scenariusz zawiera szczegóły dotyczące konstrukcji zapotrzebowania na energię prognozowania rozwiązania do uczenia maszynowego. Rozwiązania jest uczony na publiczny zestawu danych za pomocą [Nowy Jork niezależnie od systemu — Operator (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), który działa siatki zasilania dla nowego Jorku stanu. Zestaw danych zawiera co godzinę zasilania żądanie danych dla nowego Jorku w okresie 5 lat. Dodatkowe zestawu danych zawierającego co godzinę pogodą w Nowym Jorku w tym samym czasie została pobrana z [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Wymagania wstępne

- [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
- Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [Przewodnik Szybki start instalacji](../service/quickstart-installation.md) Aby zainstalować ten program i utworzyć obszaru roboczego.
- W tym przykładzie przyjęto założenie, że uruchamiasz Workbench uczenie Maszynowe Azure w systemie Windows 10 z [aparatem platformy Docker](https://www.docker.com/) zainstalowane lokalnie. Jeśli używasz macOS instrukcje dotyczą przede wszystkim takie same.
- Azure Machine Learning Operationalization instalowany z skonfigurować środowisko wdrożenia lokalnego oraz konta zarządzania modelu utworzona zgodnie z opisem w tym [przewodnik](./model-management-configuration.md).
- W tym przykładzie wymaga aktualizacji instalacji Pandas do wersji 0.20.3 lub nowszej i instalacji matplotlib. Kliknij przycisk *Otwórz okno wiersza polecenia* z *pliku* menu Narzędzia Workbench i uruchom następujące polecenia, aby zainstalować te zależności:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz Azure Machine Learning Workbench
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Energii żądanie czasu serii prognozowania" i wybierz szablon
5.  Kliknij przycisk **Utwórz**


## <a name="data-description"></a>Opis elementu danych

Dwa zestawy danych są dostarczane z tego przykładu i zostaną pobrane przy użyciu `1-data-preparation.ipynb` notesu: `nyc_demand.csv` i `nyc_weather.csv`.

**nyc_demand.csv** co godzinę zawiera wartości żądanie energii dla nowego Jorku lat 2012 2017. Danych ma następującą strukturę prosty:

| Znacznik czasu | żądanie |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Żądanie wartościami są megawatt godzin (MWh). Poniżej znajdują się wykres zapotrzebowania na energię w okresie 7-dniowe w lipcu 2017:

![Zapotrzebowania na energię](./media/scenario-time-series-forecasting/energy_demand.png  "zapotrzebowania na energię")

**nyc_weather.csv** co godzinę zawiera wartości pogody dla nowego Jorku całościowo 2012 2017:

| Znacznik czasu | precip | Temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* jest miarą procent poziomu wytrącanie. *TEMP* wartości (temperatury) ma zostać przeskalowywany w ten sposób taki sposób, że wszystkie wartości mieszczą się w zakresie [0, 100].

## <a name="scenario-structure"></a>Scenariusz — struktura

Gdy po raz pierwszy w konsoli usługi Azure Machine Learning Workbench możesz otworzyć ten projekt, przejdź do *pliki* w okienku po lewej stronie. Dostępne są następujące pliki kodu z tego przykładu:
- `1-data-preparation.ipynb` — Ten notesu Jupyter pobiera i przetwarza dane, aby przygotować go do modelowania. Jest to pierwszy notesu zostanie uruchomiona.
- `2-linear-regression.ipynb` — Ten notes przygotowuje model regresji liniowej na danych szkoleniowych.
- `3-ridge.ipynb` -przygotowuje pierścieniem modelu regresji.
- `4-ridge-poly2.ipynb` -przygotowuje modelu regresji pierścieniem wielomianu funkcji stopnia 2.
- `5-mlp.ipynb` -przygotowuje perceptron wielowarstwową sieci neuronowej.
- `6-dtree.ipynb` -przygotowuje modelu drzewa decyzyjnego.
- `7-gbm.ipynb` -przygotowuje modelu gradientu boosted maszyny.
- `8-evaluate-model.py` — Ten skrypt ładuje uczonego modelu i używa go do tworzenia prognoz na przechowywane poza testowego zestawu danych. Tworzy model metryki oceny, wydajność różne modele można porównać.
- `9-forecast-output-exploration.ipynb` -wizualizacje prognoz generowane przez modeli uczenia maszynowego tworzy tego notesu.
- `10-deploy-model.ipynb` — Ten notes pokazano, jak uczonego modelu prognozowania można operationalized w czasie rzeczywistym usługi sieci web.
- `evaluate-all-models.py` — Ten skrypt ocenia nauczony wszystkich modeli. Zapewnia zamiast uruchamiania `8-evaluate-model.py` dla każdego uczony model pojedynczo.

### <a name="1-data-preparation-and-cleaning"></a>1. Przygotowanie danych i czyszczenia

Uruchomienia przykładu, najpierw kliknij `1-data-preparation.ipynb` aby otworzyć notes w wersji zapoznawczej. Polecenie ***uruchamianie serwera notesu*** można uruchomić serwera notesu Jupyter i Python jądra na tym komputerze. Możesz uruchomić notesów ze środowiska roboczego lub można użyć przeglądarki, przechodząc do [ http://localhost:8888 ](http://localhost:8888). Zmiana jądra *lokalnego PROJECT_NAME*. Można to zrobić z *jądra* menu w notesie w obszarze *jądra zmiany*. Naciśnij klawisz ***shift + Enter*** do uruchomienia kodu w komórkach poszczególnych notesu, lub kliknij przycisk *Uruchom wszystkie* w *komórki* menu, aby uruchomić cały notes.

Notesu najpierw pobiera dane z kontenera magazynu obiektów blob hostowanej na platformie Azure. Dane są następnie przechowywane na tym komputerze w `AZUREML_NATIVE_SHARE_DIRECTORY`. Ta lokalizacja jest dostępna z dowolnego notesów lub skrypty uruchamiane z Workbench jest tak dobre miejsce do przechowywania danych i modele przeszkolone.

Po pobraniu danych notesu czyści dane przez wypełniania luk w szeregu czasowym i wypełnienie brakujących wartości przez interpolacji. Czyszczenie danych w serii. czas w ten sposób pozwala zwiększyć ilość danych, dostępna dla modeli uczenia.

Wprowadzono ulepszenia modelu są tworzone na podstawie oczyszczony danych pierwotnych. Te funkcje można podzielić na dwie grupy:

- **Czas pracy funkcji** pochodne *sygnatury czasowej* zmiennej np. *miesiąca*, *dayofweek* i *godzinę*.
- **Połączenie z otuliną funkcje** wartości czasu przesunięte żądanie lub temperatury wartości rzeczywistych. Funkcje te mają na celu przechwytywania warunkowego zależności między kolejnych okresów w modelu.

Następnie można wynikowego zestawu funkcji danych, podczas tworzenia modeli prognozowania.

### <a name="2-model-development"></a>2. Projektowanie modelu

Pierwszym sposobem modelowania może być model regresji liniowej proste. `2-linear-regression.ipynb` Notesu pokazano, jak do uczenia modelu regresji liniowej prognozy dla zapotrzebowania na energię w przyszłości. W szczególności, model zostanie uczony do prognozowania zapotrzebowania na energię godzinę (*t + 1*) przed bieżącym przedziale czasu (*t*). Jednak ta rekursywnie modelu "jednoetapowy" można zastosować w czasie testu, aby wygenerować prognoz dla przyszłych okresów, *t + n*.

Do nauczenia modelu, predykcyjnej potoku jest tworzony, która obejmuje trzy odrębne czynności:

- **Przekształcenia danych**: wymagane formaty danych wejściowych zależy od algorytmu uczenia maszynowego. W takim przypadku model regresji liniowej wymaga podzielone na kategorie zmienne do zakodowania hot jeden.
- **A krzyżowe sprawdzanie poprawności procedury**: często modelu uczenia maszynowego ma co najmniej jeden hyperparameters, które muszą być dopasowane poprzez eksperymenty. Krzyżowego sprawdzania poprawności można znaleźć optymalne zestaw wartości parametrów. Model jest często uczenia i oceniać je na różne złożeń zestawu danych. Najlepszych parametrów to osiągnąć najwyższą wydajność modelu podczas uśredniona złożeń krzyżowego sprawdzania poprawności. Ten proces jest co omówiono bardziej szczegółowo w `2-linear-regression.ipynb`.
- **Uczenie modelu końcowego**: model jest uczony na cały zestaw danych przy użyciu najlepszych zestawu hyperparameters.

Firma Microsoft zawiera szereg 6 różne modele notesów numerowane 2-7. Każdy notesu przygotowuje innego modelu i zapisuje go w `AZUREML_NATIVE_SHARE_DIRECTORY` lokalizacji. Gdy ma uczony wszystkich modeli utworzonych dla tego scenariusza, możemy ocenić i porównaj je jak opisano w następnej sekcji.

### <a name="3-model-evaluation-and-comparison"></a>3. Porównanie i oceny modelu

Możemy użyć uczonego modelu do zapewnienia prognoz dla przyszłych okresów. Najlepiej ocenić te modele przechowywane poza testowego zestawu danych. Wyniku obliczenia różne modele w tym samym zestawie danych niewidoczne, możemy dość porównania ich wydajności i zidentyfikować najlepsze modelu. W tym scenariuszu stosujemy rekursywnie uczonego modelu do prognozowania wielu kroków czasu w przyszłości. W szczególności firma Microsoft Generowanie prognoz przez 6 godzin, *t + 6* wcześniejsze bieżącej godziny *t*. Te przewidywania są oceniane pod względem rzeczywiste żądanie, dla każdego okresu.

Aby ocenić modelu, należy otworzyć `8-evaluate-model.py` skrypt z *pliki* okienka w Workbench. Sprawdź, czy *Konfiguracja Uruchom* ustawiono **lokalnego** , a następnie wpisz nazwę modelu do *argumenty* pola. Nazwa modelu musi być dokładnie *nazwa_modelu* zmienna jest ustawiona na początku notesu, w którym jest uczenia modelu. Na przykład wprowadź "linear_regression" do oceny modelu regresji liniowej przeszkolone. Alternatywnie, po ma przeprowadzono uczenia wszystkie modele, będziesz w stanie ocenić je wszystkie z jednego polecenia uruchamiając `evaluate-all-models.py`. Aby uruchomić ten skrypt, otwórz wiersz polecenia z Workbench i uruchom następujące polecenie:

```
python evaluate-all-models.py
```
`8-evaluate-model.py` Skrypt wykonuje następujące operacje:

- Ładuje potoku uczonego modelu z dysku
- Operacje przewidywania dla sprawia, że na zestawu danych testowych
- Dzienniki je i oblicza model metryki wydajności
- Operacje przewidywania dla zestawu danych do testu jest zapisywany `AZUREML_NATIVE_SHARE_DIRECTORY` nowsze inspekcji i analiza
- Zapisuje potoku uczonego modelu do *generuje* folderu.

> [!Note]
> Zapisywanie modelu do *generuje* folderu automatycznie kopiuje obiekt modelu do konta magazynu obiektów Blob Azure skojarzonego z kontem eksperymenty. Oznacza to, że zawsze można pobrać obiektu zapisany model z obiektu blob, nawet jeśli zmiana maszyny lub obliczeniowe kontekstu.

Przejdź do *Uruchom historii* okienko i kliknij pozycję `8-evaluate-model.py`. Zostaną wyświetlone wykresy zawierające kilka miar wydajności modelu:

- **ME**: oznacza błąd prognozy. Mogą to być interpretowane jako średnia różnica prognozy.
- **MPE**: [oznacza błąd procent](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME wyrażone jako procent rzeczywiste żądanie)
- **MSE**: [oznacza kwadratów błędów](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: główny średniej kwadrat błędu (pierwiastek kwadratowy MSE)
- **MAPE**: [oznacza błąd procent bezwzględne](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [symetrycznego średni procent bezwzględny błąd](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE linii bazowej prognozy, w którym Prognozowanie wartość ostatnich znanych żądanie.
- **MdRAE**: względny błąd absolutny mediana. Stosunek środkowej prognozy błąd do linii bazowej prognozy błędu. Wartość mniejszą niż 1 oznacza, że prognozy działa lepiej niż linii bazowej.

Wszystkie metryki powyżej odwoływać się do *t + 1* prognozy. Oprócz powyższych metryki, pojawi się także zestaw odpowiednich metryk ze *_horizon* sufiks. To metryka uśrednionej za wszystkich okresów w zakresie prognozy z okresu *t + 1* do okresu *t + 6*.

Jeśli metryki nie są wyświetlane w obszarze wykresu, kliknij koło zębate symbol w prawym górnym rogu. Upewnij się, że jest zaznaczone modelu metryki wydajności, które planuje się. Metryki również będą wyświetlane w tabeli poniżej wykresy. Ponownie, ta tabela jest modyfikowalny kliknij koło zębate symbolu. Posortuj tabelę według metryki wydajności, aby zidentyfikować najlepsze modelu. Jeśli interesuje Cię w niewidoczny sposób prognozy wydajności różni się od okresu *t + 1* do *t + 6*, wybierz polecenie wpisu dla modelu w tabeli. Wyświetlanie MAPE wykresy, metryki MPE i MdRAE w okresie prognozy są wyświetlane w obszarze *wizualizacje*.

Podczas obliczania modeli prognozowania, może być przydatne do wizualizacji danych wyjściowych prognoz. Dzięki temu naukowca danych, aby określić, czy prognozy tworzone jest wyświetlany realistyczne. Ułatwia również identyfikowanie problemów w prognozy, jeśli na przykład prognozy wykonuje nieprawidłowo w określonych okresach czasu. `9-forecast-output-exploration.ipynb` Notesu utworzy wizualizacje prognoz wygenerowany dla zestawu danych testowych.

### <a name="4-deployment"></a>4. Wdrożenie

Najlepsze modelu można operationalized przez wdrożenie jej jako usługę sieci web w czasie rzeczywistym. Następnie można wywołać tej usługi sieci web do wygenerowania prognoz na żądanie, wraz ze wzrostem dostępności nowych danych. W tym scenariuszu nowej prognozy musi przewidywanie zapotrzebowania na energię w kolejnych godziny wygenerowania co godzinę. Aby wykonać to zadanie, usługi sieci web może być wdrożona, która pobiera tablicę funkcje dla godziny okres czasu jako dane wejściowe i zwraca przewidywane żądanie jako dane wyjściowe.

W tym przykładzie usługi sieci web jest wdrażany na komputerze z systemem Windows 10. Upewnij się, zostały wykonane kroki wymagań wstępnych dla wdrożenia lokalnego wymienione w tym [Wprowadzenie — przewodnik](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) dla Operationalization interfejsu wiersza polecenia. Po skonfigurowaniu lokalnego środowiska i konto zarządzania modelu `10-deploy-model.ipynb` notesu wdrożenie usługi sieci web.

## <a name="conclusion"></a>Podsumowanie

Ten przykład demonstruje sposób tworzenia szeregów czasowych na trasie, funkcja prognozowania rozwiązania na potrzeby prognozowania zapotrzebowania na energię. Wiele zasad przedstawione w tym przykładzie można rozszerzyć do innych scenariuszy i branż prognozowania.

W tym scenariuszu pokazano, jak Azure Machine Learning Workbench może pomóc naukowca danych przy tworzeniu rzeczywistych rozwiązań z przydatnych funkcji, takich jak środowisko notesu Jupyter i funkcje rejestrowania metryki. Próbki również prowadzi do czytnika w sposób modelu mogą być operationalized i wdrażane za pomocą interfejsu wiersza polecenia Azure Machine Learning Operationalization. Po wdrożeniu interfejsu API usługi sieci web umożliwia deweloperom lub inżynierów danych integracji modelu prognozowania można używać do szerszego potoku danych.
