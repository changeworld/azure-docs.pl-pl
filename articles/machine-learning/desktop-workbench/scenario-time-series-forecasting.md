---
title: Prognozowanie szeregów czasowych zapotrzebowania energii | Dokumentacja firmy Microsoft
description: Jak zastosować uczenia maszynowego w celu prognozowania szeregów czasowych zapotrzebowania na energię w aplikacji Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 3f32c9048c4b2002e7672b46a4f39e86ffa7b98e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647616"
---
# <a name="energy-demand-time-series-forecasting"></a>Prognozowanie szeregów czasowych zapotrzebowania energii


Prognozowanie serii czasu jest zadaniem prognozowania przyszłych wartości w uporządkowanej w czasie sekwencji uwag. Jest to powszechny problem i ma aplikacji w wielu branżach. Na przykład sprzedaży detalicznej, firmy muszą prognozować sprzedaż przyszłych produktów, dzięki czemu mogą efektywnie organizować ich łańcuchami dostaw w celu spełnienia określonych wymagań. Podobnie pakiet dostarczania firmy muszą oszacować popyt na swoje usługi, dzięki czemu można zaplanować wymagania pracowników i tras dostaw wcześniej. W wielu przypadkach ryzyka finansowego niedokładne prognoz mogą być znaczące. W związku z tym funkcja prognozowania jest często krytyczne działanie firmy.

Niniejszy przykład pokazuje, jak prognozowanie serii czasu mogą być wykonywane przez zastosowanie techniki uczenia maszyny. Zapoznasz się na każdym kroku modelowania procesów w tym:
- Przygotowywanie danych, aby wyczyścić i formatowania danych;
- Tworzenie funkcji dla modeli na podstawie danych szeregów czasowych pierwotne; uczenia maszynowego
- Szkolenie modeli uczenia maszynowego różnych;
- Ocenianie modeli przez porównanie ich działania w zestawie danych przechowywanych poza testu; Ponadto
- Operacjonalizacji najlepszy model, udostępniając je za pośrednictwem usługi internetowej, aby generować prognozy na żądanie.

Usługa Azure Machine Learning Workbench ułatwia proces modelowania, na każdym etapie: 
- Przykład pokazuje, jak środowisko notesu programu Jupyter — pochodzących bezpośrednio za pośrednictwem aplikacji Workbench — ułatwia tworzenie kodu w języku Python łatwiejsze. Proces programowania modelu można wyjaśnić innym osobom korzystającym z wyraźniej adnotacji języka znaczników markdown i wykresy. Te notesy można wyświetlać, edytować i wykonywane bezpośrednio z aplikacji Workbench.
- Uczone modele można trwały i przekazane do magazynu obiektów blob. Pomaga to analityk danych, aby śledzić uczonego modelu obiektów i upewnij się, że są one zachowane i pobieranie w razie potrzeby.
- Metryki mogą być rejestrowane podczas wykonywania skryptu w języku Python, umożliwiając badacz danych rejestrować wyniki wydajności modelu.
- Usługa workbench tworzy tabel można dostosowywać zarejestrowanych metryk, umożliwiając badacz danych łatwo porównać metryki wydajności modelu. Wykresy są automatycznie wyświetlane, dzięki czemu można łatwo zidentyfikować najlepiej działający model.
- Na koniec przykład pokazuje, jak uczonego modelu może być przygotowany do działania przez wdrożenie jej w usłudze sieci web w czasie rzeczywistym.

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii
Publicznego repozytorium GitHub, w tym scenariuszu rzeczywistych zawiera wszystkie materiały, w tym przykłady kodu, potrzebne w tym przykładzie:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Omówienie przypadków użycia

Ten scenariusz koncentruje się na prognozowanie, której celem jest przewidzieć przyszłe obciążenie energetycznej zapotrzebowania na energię. Ponieważ operatory muszą poprawnie równowagę między zużycie na siatce energii i energii dostarczonych jej obsługa jest operacji biznesowych, dla firm w sektorze energetycznym. Za dużo energii dostarczone do siatki może spowodować marnotrawstwa energii lub błędów technicznych. Jednak jeśli nie dostarczono zbyt małej mocy może prowadzić do blackouts, pozostawiając klienci, którzy nie zasilania. Zazwyczaj operatory siatki może potrwać krótkoterminowej podjęte decyzje w kwestii zarządzania dostaw energii do siatki i pamiętać równoważenia obciążenia. Dokładne krótkoterminowej prognozy zapotrzebowania na energię w związku z tym ma zasadnicze znaczenie dla operatora, który podejmowanie tych decyzji bez obaw.

W tym scenariuszu przedstawiono konstrukcji rozwiązanie do prognozowania zapotrzebowania na energię uczenia maszynowego. Rozwiązania zostały przeszkolone na publicznego zestawu danych z [operatora New York niezależnie od systemu (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), który działa siatki zasilania dla nowego Jorku stanu. Zestaw danych zawiera co godzinę power żądanie danych dla nowego Jorku przez okres pięciu lat. Zestaw dodatkowych danych zawierające co godzinę warunków pogodowych w Nowym Jorku, w tym samym okresie czasu została pobrana z [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Wymagania wstępne

- [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
- Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [przewodnika Szybki start dotyczącego instalacji](../service/quickstart-installation.md) zainstalować ten program i utworzyć obszar roboczy.
- W tym przykładzie przyjęto założenie, że są uruchomione usługi Azure ML Workbench w systemie Windows 10 za pomocą [aparat platformy Docker](https://www.docker.com/) zainstalowane lokalnie. Jeśli używasz systemu macOS instrukcje są w dużym stopniu takie same.
- Azure maszyny Learning operacjonalizacji oprogramowania zainstalowane przy użyciu środowiska lokalnego wdrażania, konfigurowania oraz konta zarządzania modelami utworzona zgodnie z opisem w tym [przewodnik](./model-management-configuration.md).
- Ten przykładowy skrypt wymaga zaktualizowania instalacji Pandas do wersji 0.20.3 lub nowszej i zainstalować pakiet matplotlib. Kliknij przycisk *Otwórz wiersz polecenia* z *pliku* menu w aplikacji Workbench i uruchom następujące polecenia, aby zainstalować te zależności:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwieranie usługi Azure Machine Learning Workbench
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz ciąg "Energii czas serii prognozowaniu popytu", wybierz szablon
5.  Kliknij przycisk **Utwórz**


## <a name="data-description"></a>Opis danych

Dwa zestawy danych są dostarczane z tego przykładu i są pobierane przy użyciu `1-data-preparation.ipynb` Notes: `nyc_demand.csv` i `nyc_weather.csv`.

**nyc_demand.csv** co godzinę zawiera wartości żądanie energii dla nowego Jorku w latach 2012 – 2017. Dane mają następującą strukturę proste:

| Znacznik czasu: | żądanie |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Żądanie wartości należą do godziny megawatt (usługi MWh). Poniżej przedstawiono wykres zapotrzebowania na energię w okresie 7 dni w lipca 2017 r.:

![Zapotrzebowania na energię](./media/scenario-time-series-forecasting/energy_demand.png  "zapotrzebowania na energię")

**nyc_weather.csv** co godzinę zawiera wartości pogody dla nowego Jorku, w latach 2012 – 2017:

| Znacznik czasu: | precip | Temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* jest miarą procent poziomu opady. *TEMP* wartości (temperatury) ma zostać przeskalowywany w ten sposób taki sposób, że wszystkie wartości mieszczą się w zakresie [0, 100].

## <a name="scenario-structure"></a>Struktura scenariusza

Gdy otworzysz ten projekt po raz pierwszy w aplikacji Azure Machine Learning Workbench, przejdź do *pliki* w okienku po lewej stronie. Następujące pliki kodu są dostarczane z tego przykładu:
- `1-data-preparation.ipynb` — Ten notes Jupyter pobiera i przetwarza dane, aby przygotować go do modelowania. Jest to pierwsze notebooki, które będzie uruchamiane.
- `2-linear-regression.ipynb` — Ten notes szkolenie modeli uczenia modelu regresji liniowej na danych szkoleniowych.
- `3-ridge.ipynb` — szkolenie modeli ridge modelu regresji.
- `4-ridge-poly2.ipynb` — szkolenie modeli modelu regresji ridge funkcji wielomianowej stopnia 2.
- `5-mlp.ipynb` — szkolenie modeli sieci neuronowych perceptron wiele warstw.
- `6-dtree.ipynb` — szkolenie modeli modelu drzewa decyzyjnego.
- `7-gbm.ipynb` — szkolenie modeli modelu gradientu wzmocnione maszyny.
- `8-evaluate-model.py` — Ten skrypt ładuje uczonego modelu i używa ich do prognozowania w zestawie danych przechowywanych poza testu. Tworzy metryk oceny modelu, dzięki czemu można porównać wydajność różnych modeli.
- `9-forecast-output-exploration.ipynb` — Ten notes tworzy wizualizacje prognoz wygenerowanych przez modeli uczenia maszynowego.
- `10-deploy-model.ipynb` — Ten notes pokazuje, jak uczonego modelu prognozowania można być przygotowany do działania w czasie rzeczywistym usługi sieci web.
- `evaluate-all-models.py` — Ten skrypt oblicza wszystkie przeszkolone modele. Jego stanowi alternatywę dla uruchamiania `8-evaluate-model.py` dla każdego uczony model indywidualnie.

### <a name="1-data-preparation-and-cleaning"></a>1. Przygotowywanie danych i czyszczenia

Aby rozpocząć, działa aplikacja przykładowa, najpierw kliknij `1-data-preparation.ipynb` aby otworzyć notes w wersji zapoznawczej. Kliknij pozycję ***uruchom serwer*** można uruchomić serwer notesu Jupyter i jądra języka Python na swojej maszynie. Aplikację możesz uruchomić notesów z w obrębie aplikacji Workbench, lub można użyć przeglądarki, przechodząc do [ http://localhost:8888 ](http://localhost:8888). Zmiana jądro *lokalnego PROJECT_NAME*. Można to zrobić z *jądra* menu w notesie w obszarze *jądra zmiany*. Naciśnij klawisz ***shift + Enter*** do uruchomienia kodu w notesie poszczególnych komórkach lub kliknij przycisk *Uruchom wszystkie* w *komórki* menu, aby uruchomić cały notes.

Notes najpierw pobiera dane z kontenera magazynu obiektów blob, hostowanej na platformie Azure. Dane są następnie przechowywane na swojej maszynie w `AZUREML_NATIVE_SHARE_DIRECTORY`. Ta lokalizacja jest dostępna z dowolnej notesów lub skrypty, które można uruchomić z poziomu aplikacji Workbench jest tak dobre miejsce do przechowywania danych i przeszkolone modele.

Po pobraniu danych Notes czyści dane przez wypełnianie luki w szeregach czasowych i wypełnienie brakujących wartości przez interpolacji. Czyszczenie danych szeregów czasowych w ten sposób pozwala zwiększyć ilość danych dostępnych do szkolenia modeli.

Kilka funkcji modelu są tworzone na podstawie danych pierwotnych operacji czyszczenia. Te funkcje można podzielić na dwie grupy:

- **Czas oparte na funkcji** są uzyskiwane z *sygnatura czasowa* zmiennej np. *miesiąca*, *dayofweek* i *godzinę*.
- **Opóźnioną kopię funkcji** są wartości godziny, przesunięte na żądanie lub temperatury wartości rzeczywistych. Funkcje te mają na celu przechwytywania warunkowego zależności między kolejne okresy w modelu.

Następnie można Wynikowy zestaw funkcji danych, podczas tworzenia modeli prognozowania.

### <a name="2-model-development"></a>2. Model programowania

Pierwszy metoda modelowania mogą być model regresji liniowej proste. `2-linear-regression.ipynb` Notesu pokazuje sposób trenowania modelu regresji liniowej prognozy dla zapotrzebowania na energię w przyszłości. W szczególności odbywać modelu do prognozowania zapotrzebowania na energię godzinę (*t + 1*) przed bieżącym przedziale czasu (*t*). Jednak można zastosować ten rekursywnie modelu "jednoetapowy" w czasie testu, aby generować prognozy dla przyszłych okresów, *t + n*.

Do nauczenia modelu, tworzona jest predykcyjnej potoku, który obejmuje trzy etapy:

- **Przekształcanie danych**: wymaganych formatach dla danych wejściowych może się różnić w zależności od algorytmu uczenia maszynowego. W tym przypadku uczenia modelu regresji liniowej wymaga podzielonych na kategorie zmienne do zakodowania hot jeden.
- **A krzyżowe procedurze weryfikacji**: model uczenia maszynowego będzie często mają co najmniej jeden hiperparametrów, które muszą być dostosowane za pośrednictwem eksperymentów. Krzyżowego sprawdzania poprawności, można znaleźć optymalny zestaw zasobów wystąpień wartości parametrów. Model jest wielokrotnie przeszkolony i oceniać je na różnych złożeń zestawu danych. Najważniejsze parametry są pozwalających na osiągnięcie optymalnej wydajności modelu po WE złożeń krzyżowego sprawdzania poprawności. Ten proces opisano szczegółowo w `2-linear-regression.ipynb`.
- **Szkolenie końcowego modelu**: model jest uczony dla całego zestawu danych za pomocą najlepszy zestaw hiperparametrów.

Wprowadzono szereg 6 różnych modeli w notesy numerowane 2 – 7. Każdy notesu szkolenie modeli innego modelu i zapisuje go w `AZUREML_NATIVE_SHARE_DIRECTORY` lokalizacji. Gdy masz skonfigurowanych pod kątem wszystkich modeli opracowanych dla tego scenariusza, firma Microsoft Oceń i porównywanie ich, jak opisano w następnej sekcji.

### <a name="3-model-evaluation-and-comparison"></a>3. Porównanie i oceny modelu

Możemy użyć uczonego modelu, aby prognoz dla przyszłych okresach. Najlepiej oceniać te modele w zestawie danych przechowywanych poza testu. Poprzez ocenę różne modele na tym samym zestawem danych z niewidocznym, firma Microsoft dość porównaj ich wydajność i Identyfikuj najlepszy model. W tym scenariuszu są stosowane cyklicznie uczonego modelu do prognozowania wielu kroków czas w przyszłości. W szczególności firma Microsoft Generowanie prognoz dla maksymalnie sześć godzin, *t + 6* przód od bieżącej godziny *t*. Te przewidywania są sprawdzane pod kątem rzeczywistego zapotrzebowania stwierdzamy w przypadku każdego przedziału czasu.

Aby ocenić modelu, należy otworzyć `8-evaluate-model.py` skryptu z *pliki* okienko w aplikacji Workbench. Sprawdź, czy *konfiguracji uruchomieniowej* jest ustawiona na **lokalnego** a następnie wpisz nazwę modelu do *argumenty* pola. Nazwa modelu musi być zgodna dokładnie *nazwa_modelu* ustawioną na początku Notes, w którym model jest uczony. Na przykład wprowadź "linear_regression" do oceny modelu uczonego regresji liniowej. Alternatywnie, po przeszkoleni wszystkie modele, będziesz w stanie ocenić je wszystkie za pomocą jednego polecenia, uruchamiając `evaluate-all-models.py`. Aby uruchomić ten skrypt, otwórz wiersz polecenia z aplikacji Workbench i wykonaj następujące polecenie:

```
python evaluate-all-models.py
```
`8-evaluate-model.py` Skrypt wykonuje następujące operacje:

- Ładuje potoku uczonego modelu z dysku
- Wykonuje prognozy w zestawie danych testowych
- Oblicza metryki wydajności modelu i rejestruje je
- Zapisuje prognozy zestawu danych do testu `AZUREML_NATIVE_SHARE_DIRECTORY` nowsze inspekcji i analizy
- Zapisuje potoku uczonego modelu w celu *generuje* folderu.

> [!Note]
> Zapisywanie modelu, który ma *generuje* folderu automatycznie kopiuje obiekt modelu do konta usługi Azure Blob Storage skojarzonych z kontem eksperymentowania. Oznacza to, że zawsze będzie można pobrać obiektu modelu zapisane z magazynu obiektów blob, nawet jeśli zmiana maszyny lub kontekstu obliczeniowego.

Przejdź do *historii uruchamiania* okienku i kliknij przycisk `8-evaluate-model.py`. Zostaną wyświetlone wykresy zawierające kilka metryki wydajności modelu:

- **MNIE**: oznacza błąd prognozy. Może to być interpretowana jako średnia różnica prognozy.
- **MPE**: [oznacza błąd procent](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME wyrażone jako procent rzeczywistego zapotrzebowania)
- **MSE**: [oznacza pierwiastek błędu](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: średnia głównego kwadrat błędu (pierwiastek kwadratowy MSE)
- **MAPE**: [oznaczają procent bezwzględny błąd](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [symetrycznego średni procent bezwzględny błąd](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE linii bazowej prognozy, w którym prognozowania jest równa wartości ostatniego znanego żądanie.
- **MdRAE**: mediana względny błąd absolutny. Mediana stosunek prognozy błąd do linii bazowej prognozy błędu. Wartość mniejszą niż 1 oznacza, że prognozy działa lepiej niż linii bazowej.

Wszystkie powyższe metryki dotyczą *t + 1* prognozy. Oprócz powyższych metryki widoczne będzie także zestaw odpowiednich metryk z *_horizon* sufiks. Jest to metryka uśrednione z samych kropek w zakresie prognozy z okresu *t + 1* okresowi *t + 6*.

Jeśli metryki nie są wyświetlane w obszarze wykresu, kliknij symbol koła zębatego w prawym górnym rogu. Upewnij się, że są zaznaczone metryki wydajności modelu, który Cię interesuje. Metryki pojawi się także w tabeli poniżej wykresów. Ponownie, ta tabela jest możliwe do dostosowania kliknij symbol koła zębatego. Posortuj tabelę według metryki wydajności, aby zidentyfikować najlepszy model. Jeśli interesuje Cię wyświetlenie, jak prognozy wydajności różni się od okresu *t + 1* do *t + 6*, kliknij wpis dla modelu w tabeli. Wyświetlanie MAPE wykresów metryki MPE i MdRAE w okresie prognozy zostaną wyświetlone w obszarze *wizualizacje*.

Podczas oceniania modeli prognozowania, może być bardzo przydatne do wizualizacji prognozy danych wyjściowych. Pomaga to analityk danych w celu ustalenia, czy prognozy, generowany jest wyświetlany realistyczne. Może również pomóc zidentyfikować problemy, które w prognozie, jeśli na przykład prognozy wykonuje się nieprawidłowo w określonych okresach czasu. `9-forecast-output-exploration.ipynb` Notesu będzie tworzyć wizualizacje prognoz wygenerowanych dla zestawu danych testowych.

### <a name="4-deployment"></a>4. Wdrożenie

Najlepszy model można przygotowany do działania, wdrażając go jako usługę internetową czasu rzeczywistego. Ta usługa sieci web można następnie wywołać można wygenerować prognoz na żądanie, ponieważ nowych danych staje się dostępna. W tym scenariuszu nowej prognozy musi być generowane co godzinę do prognozowania zapotrzebowania na energię w kolejnej godziny. Aby wykonać to zadanie, usługa sieci web może być wdrożona, który przyjmuje tablicę funkcji dla danej godziny okres czasu jako dane wejściowe i zwraca przewidywany popyt jako dane wyjściowe.

W tym przykładzie Usługa sieci web jest wdrażana na maszynę z systemem Windows 10. Upewnij się, zostały wykonane kroki wymagań wstępnych dla wdrożenia lokalnego, w tym [przewodnik wprowadzenie](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) Operacjonalizacji interfejsu wiersza polecenia. Po skonfigurowaniu lokalnego środowiska i konta zarządzania modelami, uruchom `10-deploy-model.ipynb` Notes, aby wdrożyć usługę sieci web.

## <a name="conclusion"></a>Podsumowanie

W tym przykładzie przedstawiono sposób tworzenia end-to-end prognozowania szeregu czasowego rozwiązania na potrzeby prognozowania zapotrzebowania na energię. Wiele zasad, przedstawione w tym przykładzie można rozszerzyć do innych prognozowania branżach i scenariuszach.

W tym scenariuszu pokazano, jak Azure Machine Learning Workbench może pomóc wydobywania danych w rzeczywistych tworzenia rozwiązań korzystających z przydatnych funkcji, takich jak środowisko notesu Jupyter i możliwości rejestrowania metryki. Przykład przeprowadzi również czytnik dotyczące modelu może być przygotowany do działania i wdrażane za pomocą interfejsu wiersza polecenia Azure Machine Learning Operacjonalizacji. Po wdrożeniu interfejsu API usługi sieci web umożliwia deweloperom lub inżynierom danych, do integracji modelu prognozowania można używać w szerszym potoku danych.
