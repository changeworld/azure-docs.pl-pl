---
title: Samouczek budowania modelu na potrzeby usługi Azure Machine Learning (wersja zapoznawcza) | Microsoft Docs
description: W całej serii tego samouczka kompleksowo przedstawiono sposób korzystania z usługi Azure Machine Learning (wersja zapoznawcza). To jest część druga, w której omówiono eksperymenty.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 4f9b14762332bd11fd69a855d8fabdb206e34919
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919201"
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Samouczek 2: klasyfikowanie irysów — kompilowanie modelu
Usługi Azure Machine Learning (wersja zapoznawcza) stanowią zintegrowane rozwiązanie do przetwarzania danych i do przeprowadzania zaawansowanych analiz. Jest ono przeznaczone dla profesjonalnych analityków, którzy będą z niego korzystać w celu przygotowywania danych, opracowywania eksperymentów i wdrażania modeli na skalę chmury.

Niniejszy samouczek jest **drugą częścią trzyczęściowej serii**. W tej części samouczka użyjesz usługi Azure Machine Learning, aby:

> [!div class="checklist"]
> * Otwierać skrypty i przeglądać kod
> * Wykonywać skrypty w środowisku lokalnym
> * Przeglądać historie uruchamiania
> * Wykonywać skrypty w oknie lokalnego interfejsu wiersza polecenia platformy Azure
> * Wykonywać skrypty w lokalnym środowisku platformy Docker
> * Wykonywać skrypty w zdalnym środowisku platformy Docker
> * Wykonywać skrypty w środowisku usługi Azure HDInsight w chmurze

W tym samouczku wykorzystywany jest ponadczasowy [zbiór danych na temat irysów](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Konto eksperymentowania i aplikacja Azure Machine Learning Workbench zainstalowana zgodnie z opisem w tym [przewodniku Szybki start](../service/quickstart-installation.md)
- Projekt i przygotowane dane irysów z [części 1. samouczka](tutorial-classifying-iris-part-1.md)
- Aparat platformy Docker zainstalowany i uruchomiony lokalnie. Wersja Community Edition platformy Docker jest wystarczająca. Dowiedz się, jak zainstalować platformę Docker tutaj: https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Przeglądanie pliku iris_sklearn.py i plików konfiguracji

1. Uruchom aplikację Azure Machine Learning Workbench.

1. Otwórz projekt **myIris** utworzony w [części 1. serii samouczków](tutorial-classifying-iris-part-1.md).

2. W otwartym projekcie wybierz przycisk **Pliki** (ikona folderu) w okienku po lewej stronie, aby otworzyć listę plików w folderze projektu.

   ![Otwieranie projektu w aplikacji Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Wybierz plik skryptu języka Python **iris_sklearn.py**. 

   ![Wybieranie skryptu](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   Kod zostanie otwarty w nowej karcie edytora tekstów wewnątrz aplikacji Workbench. Jest to skrypt używany w tej części samouczka. 

   >[!NOTE]
   >Kod, który zostanie wyświetlony, nie musi być dokładnie taki sam jak kod powyżej, ponieważ ten przykładowy projekt jest często aktualizowany.
   
   ![Otwieranie pliku](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Zbadaj kod skryptu języka Python, aby zapoznać się ze stylem kodowania. 

   Skrypt **iris_sklearn.py** wykonuje następujące zadania:

   * Ładuje domyślny pakiet przeznaczony do przygotowania danych o nazwie **iris.dprep** w celu utworzenia struktury danych [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Dodaje losowe funkcje utrudniające rozwiązanie problemu. Losowość jest konieczna, ponieważ Iris jest małym zestawem danych, który można łatwo sklasyfikować z niemal 100-procentową dokładnością.

   * Używa biblioteki [scikit-learn](http://scikit-learn.org/stable/index.html) uczenia maszynowego w celu zbudowania modelu regresji logistycznej.  Ta biblioteka jest domyślnie dostarczana razem z aplikacją Azure Machine Learning Workbench.

   * Serializuje model przez użycie biblioteki [pickle](https://docs.python.org/3/library/pickle.html) w pliku w folderze `outputs`. 
   
   * Ładuje zserializowany model i deserializuje go z powrotem do pamięci.

   * Za pomocą modelu po deserializacji przewiduje nowy rekord. 

   * Rysuje dwa wykresy, macierz pomyłek oraz wieloklasową krzywą ROC (Receiver Operating Characteristic) przy użyciu biblioteki [matplotlib](https://matplotlib.org/), a następnie zapisuje te elementy w folderze `outputs`. Tę bibliotekę możesz zainstalować we własnym środowisku, jeśli jeszcze w nim nie istnieje.

   * Automatycznie tworzy wykres współczynnika uregulowania i dokładności modelu w historii uruchamiania. Obiekt `run_logger` jest używany przez cały czas w celu rejestrowania współczynnika uregulowania i modelowania dokładności w dziennikach. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Uruchamianie skryptu iris_sklearn.py w środowisku lokalnym

1. Uruchom interfejs wiersza polecenia usługi Azure Machine Learning:
   1. Uruchom aplikację Azure Machine Learning Workbench.

   1. Z menu aplikacji Workbench wybierz kolejno pozycje **Plik** > **Otwórz wiersz polecenia**. 
   
   Okno interfejsu wiersza polecenia usługi Azure Machine Learning zostanie otwarte w folderze projektu `C:\Temp\myIris\>` w systemie Windows. Ten projekt jest taki sam jak projekt utworzony w 1. części samouczka.

   >[!IMPORTANT]
   >W celu wykonania kolejnych kroków musisz użyć tego okna interfejsu wiersza polecenia.

1. W oknie interfejsu wiersza polecenia zainstaluj bibliotekę wykreślania języka Python**matplotlib**, jeśli jeszcze nie masz biblioteki.

   Skrypt **iris_sklearn.py** ma zależności z dwoma pakietami języka Python: **scikit-learn** i **matplotlib**.  Pakiet **scikit-learn** jest instalowany przez aplikację Azure Machine Learning Workbench, aby ułatwić Ci pracę. Bibliotekę **matplotlib** musisz jednak zainstalować, jeśli ta czynność nie została wykonana wcześniej.

   Jeśli będziesz kontynuować bez instalowania biblioteki **matplotlib**, kod w tym samouczku może nadal działać prawidłowo. Nie wygeneruje on jednak wykresu wyjściowej macierzy pomyłek ani wykresu wieloklasowej krzywej ROC, które przedstawiono w wizualizacjach historycznych.

   ```azurecli
   pip install matplotlib
   ```

   Ta instalacja trwa około minuty.

1. Wróć do aplikacji Workbench. 

1. Znajdź kartę o nazwie **iris_sklearn.py**. 

   ![Wyszukiwanie karty ze skryptem](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. Na pasku narzędzi tej karty wybierz pozycję **lokalne** jako środowisko wykonywania i `iris_sklearn.py` jako skrypt do uruchomienia. Te elementy mogły już zostać wybrane.

   ![Wybieranie pozycji „lokalne” i skryptu](media/tutorial-classifying-iris/2-local-script.png)

1. Przejdź na prawą stronę paska narzędzi i wprowadź ciąg `0.01` w polu **Argumenty**. 

   Ta wartość odpowiada współczynnikowi uregulowania modelu regresji logistycznej.

   ![Wybieranie pozycji „lokalne” i skryptu](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Wybierz przycisk **Uruchom**. Nastąpi natychmiastowe zaplanowanie zadania. To zadanie jest widoczne w panelu **Zadania** po prawej stronie okna aplikacji Workbench. 

   ![Wybieranie pozycji „lokalne” i skryptu](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Po kilku chwilach stan zadania zostanie zmieniony z **Przesyłanie** na **Uruchomiono**, a na koniec na wartość **Zakończono**.

1. Wybierz wyraz **Ukończono** w tekście stanu zadania w panelu **Zadania**. 

   ![Uruchamianie skryptu sklearn](media/tutorial-classifying-iris/2-completed.png)

   Zostanie otwarte okno podręczne, w którym widoczny będzie tekst wyjścia standardowego (stdout) dla uruchomienia. Aby zamknąć tekst stdout, wybierz przycisk **Zamknij** (**x**) w prawym górnym rogu okna podręcznego.

   ![Wyjście standardowe](media/tutorial-classifying-iris/2-standard-output.png)

9. W tym samym stanie zadania w okienku **Zadania** wybierz niebieski tekst **iris_sklearn.py [n]** (_n_ to numer uruchomienia) tuż nad stanem **Zakończono** i czasem uruchomienia. Zostanie otwarte okno **Właściwości uruchamiania**, które zawiera następujące informacje dotyczące konkretnego uruchomienia:
   - Informacje w oknie **Właściwości uruchomienia**
   - **Dane wyjściowe**
   - **Metryki**
   - **Wizualizacje**, jeśli są dostępne
   - **Dzienniki** 

   Gdy uruchomienie zostanie zakończone, pojawi się okno podręczne z następującymi wynikami:

   >[!NOTE]
   >Do samouczka wprowadziliśmy generowanie losowe, dlatego uzyskane wyniki mogą się nieznacznie różnić od widocznych tutaj.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. Zamknij kartę **Właściwości uruchomienia**, a następnie wróć do karty **iris_sklearn.py**. 

11. Powtórz te czynności dla dodatkowych uruchomień. 

    W polu **Argumenty** wprowadź serię wartości z zakresu od `0.001` do `10`. Wybierz przycisk **Uruchom**, aby wykonać ten kod jeszcze kilka razy. Zmieniana za każdym razem wartość argumentu jest wprowadzana do modelu regresji logistycznej w kodzie, przez co wyszukane dane są za każdym razem różne.

## <a name="review-the-run-history-in-detail"></a>Przeglądanie szczegółów historii uruchamiania
W aplikacji Azure Machine Learning Workbench każde wykonanie skryptu jest przechwytywane jako rekord w historii uruchamiania. Jeśli otworzysz widok **Uruchomienia**, możesz wyświetlić historię uruchamiania konkretnego skryptu.

1. Aby otworzyć listę **Uruchomienia**, wybierz przycisk **Uruchomienia** (ikona zegara) na pasku narzędzi po lewej stronie. Następnie wybierz skrypt **iris_sklearn.py**, aby wyświetlić **Pulpit nawigacyjny uruchomień** skryptu `iris_sklearn.py`.

   ![Widok uruchomienia](media/tutorial-classifying-iris/run_view.png)

1. Zostanie otwarta karta **Pulpit nawigacyjny uruchomień**. 

   Przejrzyj statystyki przechwycone z wielu uruchomień. Wykresy są renderowane w górnej części karty. Każde uruchomienie ma kolejny numer, a szczegóły dotyczące uruchomienia są wyświetlane na liście w tabeli u dołu ekranu.

   ![Pulpit nawigacyjny uruchomień](media/tutorial-classifying-iris/run_dashboard.png)

1. Filtruj tabelę i wybieraj dowolne wykresy, aby wyświetlić stan, czas trwania, dokładność i współczynnik uregulowania każdego uruchomienia. 

1. Zaznacz pola wyboru obok co najmniej dwóch uruchomień w tabeli **Uruchomienia**. Wybierz przycisk **Porównaj**, aby otworzyć okienko ze szczegółowym porównaniem. Porównaj pozycje zestawione obok siebie. 

1. Aby wrócić do **pulpitu nawigacyjnego uruchomień**, wybierz przycisk Wstecz **listy Uruchomienia** w lewym górnym rogu okienka **Porównanie**.

   ![Powrót do listy uruchomień](media/tutorial-classifying-iris/2-compare-back.png)

1. Wybierz pojedyncze uruchomienie, aby wyświetlić widok szczegółów uruchomienia. Zwróć uwagę na to, że statystyki dotyczące wybranego uruchomienia będą widoczne w sekcji **Właściwości uruchomienia**. Pliki zapisane w folderze danych wyjściowych będą widoczne na liście w sekcji **Dane wyjściowe** — można je stamtąd pobrać.

   ![Szczegóły uruchomienia](media/tutorial-classifying-iris/run_details.png)

   Dwa wykresy — macierz pomyłek i wieloklasowa krzywa ROC — zostaną zrenderowane w sekcji **Wizualizacje**. Wszystkie pliki dziennika można znaleźć w sekcji **Dzienniki**.


## <a name="run-scripts-in-local-docker-environments"></a>Uruchamianie skryptów w lokalnych środowiskach platformy Docker

Opcjonalnie możesz eksperymentować, uruchamiając skrypty dla lokalnego kontenera platformy Docker. Możesz konfigurować dodatkowe środowiska wykonawcze, takich jak platforma Docker, i uruchamiać skrypty w tych środowiskach. 

>[!NOTE]
>Aby poeksperymentować z wysyłką skryptu uruchamianego w kontenerze platformy Docker na zdalnej maszynie wirtualnej platformy Azure lub w klastrze usługi Azure HDInsight Spark, możesz wykonać [instrukcje, aby utworzyć klaster usługi HDInsight lub maszynę wirtualną analizy danych platformy Azure w oparciu o system Ubuntu](how-to-create-dsvm-hdi.md).

1. Jeśli te czynności nie zostały jeszcze wykonane, zainstaluj i uruchom platformę Docker lokalnie na maszynie z systemem Windows lub MacOS. Aby uzyskać więcej informacji, zapoznaj się z instrukcjami dotyczącymi instalacji platformy Docker pod adresem https://docs.docker.com/install/. Wersja Community jest wystarczająca.

1. W okienku po lewej stronie wybierz ikonę **folderu**, aby otworzyć listę **Pliki** dla projektu. Rozwiń folder `aml_config`. 

2. Istnieje kilka wstępnie skonfigurowanych środowisk: **docker-python**, **docker-spark** oraz **lokalne**. 

   Każde środowisko ma dwa pliki, takie jak `docker.compute` (środowiska **docker python** i **docker spark**) i `docker-python.runconfig`. Otwórz każdy plik, aby zobaczyć, że niektóre opcje można skonfigurować w edytorze tekstów.  

   Aby wyczyścić, wybierz przycisk **Zamknij** (**x**) na kartach wszystkich otwartych edytorów tekstu.

3. Uruchom skrypt **iris_sklearn.py**, używając środowiska **docker-python**: 

   - Na pasku narzędzi po lewej stronie wybierz ikonę **zegara**, aby otworzyć okienko **Uruchomienia**. Wybierz pozycję **Wszystkie uruchomienia**. 

   - U góry karty **Wszystkie uruchomienia** wybierz **docker-python** jako środowisko docelowe zamiast domyślnego środowiska **lokalnego**. 

   - Następnie po prawej stronie wybierz **iris_sklearn.py** jako skrypt do uruchomienia. 

   - Pozostaw pole **Argumenty** puste, ponieważ skrypt określa wartość domyślną. 

   - Wybierz przycisk **Uruchom**.

4. Sprawdź, czy nowe zadanie uruchamia się. To zadanie jest widoczne w okienku **Zadania** po prawej stronie okna aplikacji Workbench.

   Jeśli uruchamiasz skrypt na platformie Docker po raz pierwszy, zadanie trwa kilka minut dłużej. 

   W tle aplikacja Azure Machine Learning Workbench tworzy nowy plik platformy Docker. 
   Nowy plik odwołuje się do obrazu podstawowego platformy Docker określonego w pliku `docker.compute` oraz pakietów zależności języka Python określonych w pliku `conda_dependencies.yml`. 
   
   Aparat platformy Docker wykonuje następujące zadania:

    - Pobiera obraz podstawowy z platformy Azure.
    - Instaluje pakiety języka Python określone w pliku `conda_dependencies.yml`.
    - Uruchamia kontener platformy Docker.
    - Kopiuje (albo — w zależności od konfiguracji uruchamiania — tworzy do niej odniesienie) lokalną kopię folderu projektu.      
    - Wykonuje skrypt `iris_sklearn.py`.

   Wyniki końcowe powinny być dokładnie takie same jak wtedy, gdy miejscem docelowym jest środowisko **lokalne**.

5. Teraz wypróbujmy platformę Spark. Obraz podstawowy platformy Docker zawiera wstępnie zainstalowane i skonfigurowane wystąpienie platformy Spark, którego można używać do wykonywania skryptu PySpark. Użycie tego obrazu podstawowego to prosty sposób tworzenia i testowania programu na platformę Spark bez konieczności samodzielnego instalowania i konfigurowania platformy Spark. 

   Otwórz plik `iris_spark.py`. Ten skrypt ładuje plik danych `iris.csv` i używa algorytmu regresji logistycznej z biblioteki Spark Machine Learning do klasyfikowania zestawu danych Iris. Teraz zmień środowisko uruchomieniowe na **docker-spark**, a skrypt na **iris_spark.py**, a następnie uruchom go ponownie. Proces trwa nieco dłużej, ponieważ sesja platformy Spark musi zostać utworzona i uruchomiona w kontenerze platformy Docker. Możesz także zobaczyć, że wyjście standardowe różni się od wyjścia standardowego dla pliku `iris_spark.py`.

6. Rozpocznij kilka dodatkowych uruchomień i poeksperymentuj z różnymi argumentami. 

7. Otwórz plik `iris_spark.py`, aby zobaczyć model regresji logistycznej utworzony za pomocą biblioteki Spark Machine Learning. 

8. Korzystaj z okienka **Zadania**, widoku listy historii uruchamiania i widoku szczegółów uruchomień w różnych środowiskach wykonawczych.

## <a name="run-scripts-in-the-cli-window"></a>Uruchamianie skryptów w oknie interfejsu wiersza polecenia

1. Uruchom interfejs wiersza polecenia usługi Azure Machine Learning:
   1. Uruchom aplikację Azure Machine Learning Workbench.

   1. Z menu aplikacji Workbench wybierz kolejno pozycje **Plik** > **Otwórz wiersz polecenia**. 
   
   W folderze projektu `C:\Temp\myIris\>` w systemie Windows zostanie otwarte okno interfejsu wiersza polecenia. Jest to projekt utworzony w części 1. samouczka.

   >[!IMPORTANT]
   >W celu wykonania kolejnych kroków musisz użyć tego okna interfejsu wiersza polecenia.

1. W oknie interfejsu wiersza polecenia zaloguj się do platformy Azure. [Dowiedz się więcej o poleceniu az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   Użytkownik może już być zalogowany. W takim przypadku można pominąć ten krok.

   1. W wierszu polecenia wprowadź:
      ```azurecli
      az login
      ```

      To polecenie zwraca kod do użycia w przeglądarce pod adresem https://aka.ms/devicelogin.

   1. Przejdź do adresu https://aka.ms/devicelogin w przeglądarce.

   1. Po wyświetleniu monitu wprowadź kod odebrany w interfejsie wiersza polecenia w przeglądarce.

   Podczas uwierzytelniania w zasobach platformy Azure aplikacja Workbench i interfejs wiersza polecenia używają osobnych pamięci podręcznych poświadczeń. Po zalogowaniu ponowne uwierzytelnianie nie będzie konieczne do momentu wygaśnięcia ważności tokenu z pamięci podręcznej. 

1. Jeśli Twoja organizacja ma wiele subskrypcji platformy Azure (środowisko przedsiębiorstwa), musisz ustawić subskrypcję do użycia. Znajdź swoją subskrypcję, ustaw ją za pomocą identyfikatora subskrypcji i przetestuj.

   1. Utwórz listę wszystkich subskrypcji platformy Azure, do których masz dostęp, za pomocą tego polecenia:
   
      ```azurecli
      az account list -o table
      ```

      Polecenie **az account list** zwraca listę subskrypcji dostępnych na potrzeby logowania. 
      Jeśli istnieje więcej niż jedna, określ wartość identyfikatora subskrypcji, której chcesz użyć.

   1. Ustaw subskrypcję platformy Azure, która ma być używana jako konto domyślne:
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      gdzie \<your-subscription-id\> to wartość identyfikatora subskrypcji do użycia. Nie uwzględniaj nawiasów.

   1. Potwierdź nowe ustawienie subskrypcji, żądając szczegółów bieżącej subskrypcji. 

      ```azurecli
      az account show
      ```    

1. W oknie interfejsu wiersza polecenia zainstaluj bibliotekę wykreślania języka Python**matplotlib**, jeśli jeszcze nie masz biblioteki.

   ```azurecli
   pip install matplotlib
   ```

1. W oknie interfejsu wiersza polecenia prześlij skrypt **iris_sklearn.py** jako eksperyment.

   Wykonywanie skryptu iris_sklearn.py jest uruchamiane w lokalnym kontekście przetwarzania.

   + W systemie Windows:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + W systemie MacOS:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   Dane wyjściowe powinny być podobne do:
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Przejrzyj dane wyjściowe. Dane wyjściowe i wyniki są takie same jak w przypadku wcześniejszego użycia aplikacji Workbench do uruchomienia skryptu. 

1. W oknie interfejsu wiersza polecenia uruchom ponownie skrypt języka Python **iris_sklearn.py** przy użyciu środowiska wykonawczego Docker (jeśli platforma Docker została zainstalowana na maszynie).

   + Jeśli Twój kontener działa w systemie Windows: 
     |Wykonanie<br/>environment|Polecenie w systemie Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |platforma Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Jeśli Twój kontener działa w systemie MacOS: 
     |Wykonanie<br/>environment|Polecenie w systemie Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |platforma Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Wróć do aplikacji Workbench, a następnie:
   1. Wybierz ikonę folderu w okienku po lewej stronie, aby wyświetlić listę plików projektu.
   
   1. Otwórz skrypt języka Python o nazwie **run.py**. Ten skrypt jest przydatny do zapętlania różnych współczynników uregulowania. 

   ![Powrót do listy uruchomień](media/tutorial-classifying-iris/2-runpy.png)

1. Uruchom eksperyment wiele razy z tymi współczynnikami. 

   Ten skrypt uruchamia zadanie `iris_sklearn.py` ze współczynnikiem uregulowania `10.0` (bardzo duża liczba). Następnie skrypt zmniejsza współczynnik o połowę w kolejnym uruchomieniu. Robi tak, aż współczynnik nie będzie mniejszy niż `0.005`. 

   Skrypt zawiera następujący kod:

   ![Powrót do listy uruchomień](media/tutorial-classifying-iris/2-runpy-code.png)

1. Uruchom skrypt **run.py** z poziomu wiersza polecenia w następujący sposób:

   ```cmd
   python run.py
   ```

   To polecenie przesyła skrypt iris_sklearn.py wiele razy przy użyciu różnych współczynników uregulowania

   Gdy działanie skryptu `run.py` zostanie zakończone, w widoku listy historii uruchamiania w aplikacji Workbench będzie można zobaczyć wykres różnych metryk.

## <a name="run-scripts-in-a-remote-docker-container"></a>Uruchamianie skryptów w zdalnym kontenerze platformy Docker
W celu wykonania skryptu w kontenerze platformy Docker na komputerze zdalnym z systemem Linux wymagany jest dostęp za pośrednictwem powłoki SSH (nazwa użytkownika i hasło) do tego komputera zdalnego. Ponadto na maszynie należy zainstalować i uruchomić aparat platformy Docker. Najprostszym sposobem uzyskania takiego komputera z systemem Linux jest utworzenie maszyny wirtualnej Data Science Virtual Machine (DSVM) opartej na dystrybucji Ubuntu na platformie Azure. Dowiedz się, [jak utworzyć maszynę wirtualną DSVM opartą na dystrybucji Ubuntu do użycia w aplikacji Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>Maszyna wirtualna DSVM oparta na dystrybucji CentOS *nie* jest obsługiwana.

1. Gdy maszyna wirtualna zostanie utworzona, można dołączyć ją jako środowisko wykonawcze, jeśli wygenerowano parę plików `.runconfig` i `.compute`. Użyj następującego polecenia, aby wygenerować pliki. 

 Nadajmy nowemu celowi obliczenia nazwę `myvm`.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >Adres IP może być również adresowaną publicznie w pełni kwalifikowaną nazwą domeny (FQDN), taką jak `vm-name.southcentralus.cloudapp.azure.com`. Dobrym rozwiązaniem jest dodanie nazwy FQDN do maszyny wirtualnej DSVM i użycie jej zamiast adresu IP. Takie rozwiązanie jest dobrym pomysłem, ponieważ w pewnym momencie maszynę wirtualną można będzie wyłączyć w związku z oszczędnościami. Ponadto przy następnym uruchomieniu maszyny wirtualnej adres IP może już być zmieniony.

   >[!NOTE]
   >Oprócz uwierzytelniania nazwy użytkownika i hasła możesz określić klucz prywatny i odpowiednie hasło (jeśli istnieją) przy użyciu opcji `--private-key-file` i (opcjonalnie) `--private-key-passphrase`. Jeśli chcesz użyć klucza prywatnego, który był używany podczas tworzenia maszyny wirtualnej DSVM, określ opcję `--use-azureml-ssh-key`.

   Następnie przygotuj cel obliczenia **myvm**, uruchamiając poniższe polecenie.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   Poprzednie polecenie konstruuje obraz platformy Docker na maszynie wirtualnej i przygotowuj ją na uruchamianie skryptów.
   
   >[!NOTE]
   >W pliku `myvm.runconfig` możesz również zmienić wartość `PrepareEnvironment` z domyślnej wartości `false` na `true`. Ta zmiana spowoduje automatyczne przygotowanie kontenera platformy Docker jako części pierwszego uruchomienia.

2. Edytuj wygenerowany plik `myvm.runconfig` w folderze `aml_config` i zmień ustawienie Framework z wartości domyślnej `PySpark` na wartość `Python`:

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >Rozwiązanie PySpark powinno działać, ale użycie języka Python jest bardziej wydajne, jeśli do uruchomienia skryptu w języku Python faktycznie nie potrzeba sesji platformy Spark.

3. Wydaj to samo polecenie jak poprzednio w oknie interfejsu wiersza polecenia, używając tym razem środowiska docelowego _myvm_ w celu uruchomienia skryptu iris_sklearn.py w zdalnym kontenerze platformy Docker:
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   Polecenie zostanie wykonane tak, jakby używane było środowisko `docker-python`, ale wykonanie ma miejsce na zdalnej maszynie wirtualnej z systemem Linux. W oknie interfejsu wiersza polecenia pojawią się takie same informacje wyjściowe.

4. Wypróbujmy użycie platformy Spark w kontenerze. Otwórz Eksploratora plików. Utwórz kopię pliku `myvm.runconfig` i nadaj jej nazwę `myvm-spark.runconfig`. Edytuj nowy plik, aby zmienić wartość ustawienia `Framework` z `Python` na `PySpark`:
   ```yaml
   Framework: PySpark
   ```
   Nie wprowadzaj żadnych zmian w pliku `myvm.compute`. W celu przeprowadzenia wykonania na platformie Spark zostanie wykorzystany ten sam obraz platformy Docker na tej samej maszynie wirtualnej. W nowym pliku `myvm-spark.runconfig` pole `Target` wskazuje ten sam plik `myvm.compute` za pośrednictwem jego nazwy `myvm`.

5. Wpisz następujące polecenie, aby uruchomić skrypt **iris_spark.py** w wystąpieniu platformy Spark uruchomionej w zdalnym kontenerze Docker:
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Uruchamianie skryptów w klastrach usługi HDInsight
Ten skrypt można również uruchomić w klastrze usługi HDInsight Spark. Dowiedz się, [jak utworzyć klaster usługi HDInsight Spark do użycia w aplikacji Azure ML Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Klaster usługi HDInsight musi używać usługi Azure Blob Storage jako magazynu głównego. Korzystanie z magazynu usługi Azure Data Lake nie jest jeszcze obsługiwane.

1. Jeśli masz dostęp do platformy Spark dla klastra usługi Azure HDInsight, wygeneruj polecenie konfiguracji uruchomieniowej HDInsight podobne do pokazanego tutaj. Jako parametry podaj nazwę klastra usługi HDInsight, swoją nazwę użytkownika usługi HDInsight i hasło. 

   Użyj następującego polecenia, aby utworzyć cel obliczenia wskazujący na klaster usługi HDInsight:

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Aby przygotować klaster usługi HDInsight, uruchom następujące polecenie:

   ```
   az ml experiment prepare -c myhdi
   ```

   Nazwa FQDN węzła głównego klastra to zazwyczaj `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` to nazwa użytkownika protokołu SSH dla klastra zdefiniowanego podczas instalacji usługi HDInsight. Domyślna wartość to `sshuser`. Wartością nie jest `admin`, ponieważ jest to drugi użytkownik utworzony podczas konfiguracji w celu zapewnienia dostępu do administracyjnej witryny internetowej klastra. 

2. Uruchom skrypt **iris_spark.py** w klastrze usługi HDInsight za pomocą tego polecenia:

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >W przypadku wykonywania względem zdalnego klastra usługi HDInsight możesz również wyświetlić szczegóły wykonania zadania YARN (Yet Another Resource Negotiator) pod adresem `https://<your_cluster_name>.azurehdinsight.net/yarnui`, korzystając z konta użytkownika `admin`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki
W drugiej części z serii trzech części samouczka pokazano, jak:
> [!div class="checklist"]
> * Otwierać skrypty i przeglądać kod w aplikacji Workbench
> * Wykonywać skrypty w środowisku lokalnym
> * Przeglądać historię uruchamiania
> * Wykonywać skrypty w lokalnym środowisku platformy Docker

Teraz możesz wypróbować trzecią część z tej serii samouczków, w którym można wdrożyć utworzony model regresji logistycznej jako usługę internetową w czasie rzeczywistym.

> [!div class="nextstepaction"]
> [Samouczek 3 — wdrażanie modeli](tutorial-classifying-iris-part-3.md)
