---
title: Samouczek dotyczący przygotowywania danych do klasyfikowania irysów w usłudze Azure Machine Learning (wersja zapoznawcza) | Microsoft Docs
description: W całej serii tego samouczka kompleksowo przedstawiono sposób korzystania z usługi Azure Machine Learning (wersja zapoznawcza). W części pierwszej omówiono przygotowywanie danych.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 03/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: dd10581888da64114debec40cba8564023033864
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278512"
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>Samouczek 1. Klasyfikowanie irysów — przygotowanie danych

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Usługa Azure Machine Learning (wersja zapoznawcza) stanowi zintegrowane, kompleksowe rozwiązanie do nauki o danych i do analiz zaawansowanych przeznaczone dla profesjonalnych analityków, którzy będą z niego korzystać w celu przygotowywania danych, opracowywania eksperymentów i wdrażania modeli na skalę chmury.

Niniejszy samouczek jest **pierwszą częścią trzyczęściowej serii**. W tym samouczku poznasz podstawowe zagadnienia z zakresu usług Azure Machine Learning (wersja zapoznawcza) i sposoby wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu w aplikacji Azure Machine Learning Workbench
> * Tworzenie pakietu przygotowywania danych
> * Generowanie kodu Python/PySpark w celu wywołania pakietu przygotowywania danych

W tym samouczku wykorzystywany jest ponadczasowy [zbiór danych na temat irysów](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Do ukończenia tego samouczka niezbędne są następujące elementy:
- Konto Eksperymentowanie w usłudze Azure Machine Learning
- Zainstalowana aplikacja Azure Machine Learning Workbench

Jeśli te wymagania wstępne nie zostały jeszcze spełnione, wykonaj kroki opisane w artykule [Szybki start: instalacja i uruchamianie](quickstart-installation.md), aby skonfigurować konta i zainstalować aplikację Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Tworzenie nowego projektu w aplikacji Workbench

Jeśli wykonano kroki opisane w artykule [Szybki start: instalacja i uruchamianie](quickstart-installation.md), ten projekt powinien już istnieć i możesz przejść do następnej sekcji.

1. Uruchom aplikację Azure Machine Learning Workbench i w razie potrzeby zaloguj się. 
   
   + W systemie Windows aplikację uruchomisz, używając skrótu **Machine Learning Workbench** na pulpicie. 
   + W systemie macOS wybierz pozycję **Azure ML Workbench** w programie Launchpad.

1. W okienku **PROJEKTY** wybierz znak plus (+), a następnie wybierz pozycję **Nowy projekt**.  

   ![Nowy obszar roboczy](./media/tutorial-classifying-iris/new_ws.png)

1. Wypełnij pola formularza i wybierz przycisk **Utwórz**, aby utworzyć nowy projekt w aplikacji Workbench.

   Pole|Sugerowane wartości dla samouczka|Opis
   ---|---|---
   Project name (Nazwa projektu) | myIris |Wprowadź unikatową nazwę, która identyfikuje Twoje konto. Możesz użyć własnej nazwy albo nazwy działu lub projektu, która najlepiej identyfikuje eksperyment. Nazwa powinna mieć od 2 do 32 znaków. Powinna ona zawierać tylko znaki alfanumeryczne i znak łącznika (-). 
   Katalog projektu | c:\Temp\ | Określ katalog, w którym zostanie utworzony projekt.
   Opis projektu | _Pozostaw puste_ | Pole opcjonalne przydatne do opisywania projektów.
   Adres URL repozytorium usługi GIT VisualStudio.com |_Pozostaw puste_ | Pole opcjonalne. Możesz skojarzyć projekt z repozytorium Git w pakiecie Azure DevOps na potrzeby kontroli źródła i współpracy. [Dowiedz się, jak to skonfigurować](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Wybrany obszar roboczy | IrisGarden (jeśli istnieje) | Wybierz obszar roboczy utworzony dla konta Eksperymentowania w witrynie Azure Portal. <br/>Jeśli samouczek Szybki start był wykonywany, powinien już istnieć obszar roboczy o nazwie IrisGarden. W przeciwnym razie wybierz obszar roboczy utworzony podczas tworzenia konta Eksperymentowania lub dowolny inny, którego chcesz użyć.
   Szablon projektu | Classifying Iris | Szablony zawierają skrypty i dane, których można użyć, aby zapoznać się z produktem. Ten szablon zawiera skrypty i dane potrzebne w tym samouczku Szybki start oraz innych samouczkach w tej witrynie dokumentacji. 

   ![Nowy projekt](media/tutorial-classifying-iris/new_project.png)
 
 Utworzony zostanie nowy projekt i otworzy się pulpit nawigacyjny projektu z tym projektem. Teraz możesz eksplorować stronę główną projektu, źródła danych, notesy i pliki kodu źródłowego. 

   ![Otwieranie projektu](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Tworzenie pakietu przygotowywania danych

W dalszej kolejności możesz przeglądać dane i rozpocząć ich przygotowywanie w aplikacji Azure Machine Learning Workbench. Każde przekształcenie wykonywane w aplikacji Workbench jest przechowywane w formacie JSON w lokalnym pakiecie przygotowywania danych (plik *.dprep). Ten pakiet przygotowywania danych jest podstawowym kontenerem na potrzeby operacji związanych z przygotowywaniem danych w aplikacji Workbench.

Można go przekazać później do środowiska uruchomieniowego, takiego jak lokalne środowisko C#/CoreCLR, Scala/Spark lub Scala/HDI. 

1. Wybierz ikonę folderu, aby otworzyć widok Pliki, a następnie wybierz plik **iris.csv** w celu jego otwarcia.

   Ten plik zawiera tabelę składającą się z 5 kolumn i 50 wierszy. Cztery kolumny są kolumnami funkcji liczbowych. Piąta kolumna jest kolumną docelową ciągu. Żadna z tych kolumn nie ma nazwy nagłówka.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Nie dołączaj plików danych do folderu projektu, szczególnie w przypadku, gdy rozmiar pliku jest duży. Ponieważ plik danych **iris.csv** ma niewielki rozmiar, dla celów demonstracyjnych został on uwzględniony w tym szablonie. Aby uzyskać więcej informacji, zobacz [How to read and write large data files (Jak odczytywać i zapisywać duże pliki danych)](how-to-read-write-files.md).

2. W obszarze **Widok danych** wybierz znak plus (**+**), aby dodać nowe źródło danych. Zostanie otwarta strona **Dodawanie źródła danych**. 

   ![Widok danych w aplikacji Azure Machine Learning Workbench](media/tutorial-classifying-iris/data_view.png)

3. Wybierz pozycję **Pliki tekstowe(\*.csv, \*.json, \*.txt., ...)** i kliknij przycisk **Dalej**.
   ![Źródło danych w aplikacji Azure Machine Learning Workbench](media/tutorial-classifying-iris/data-source.png)

4. Przejdź do pliku **iris.csv** i kliknij pozycję **Zakończ**. Spowoduje to użycie wartości domyślnych parametrów, takich jak separator i typy danych.

   >[!IMPORTANT]
   >Na potrzeby tego ćwiczenia wybierz plik **iris.csv** z bieżącego katalogu projektu. W przeciwnym razie dalsze kroki mogą zakończyć się niepowodzeniem.
 
   ![Wybierz iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Zostanie utworzony nowy plik o nazwie **iris-1.dsource**. Plik ma unikatową nazwę z sufiksem „-1”, ponieważ przykładowy projekt zawiera już nienumerowany plik **iris.dsource**.  

   Plik zostanie otwarty i widoczne będą dane. Do zestawu danych zostaną automatycznie dodane nagłówki kolumn od **Column1** do **Column5**. Przewiń w dół i zwróć uwagę, że ostatni wiersz z zestawu danych jest pusty. Wiersz jest pusty, ponieważ plik CSV zawiera dodatkowy podział wiersza.

   ![Widok danych irysów](media/tutorial-classifying-iris/iris_data_view.png)

1. Wybierz przycisk **Metryki**. Spowoduje to wygenerowanie i wyświetlenie histogramów.

   Możesz wrócić do widoku danych, wybierając przycisk **Dane**.
   
   ![Widok danych irysów](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Przyjrzyj się histogramom. Dla każdej kolumny został obliczony kompletny zestaw statystyk. 

   ![Widok danych irysów](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Rozpocznij tworzenie pakietu przygotowywania danych, naciskając przycisk **Przygotowywanie**. Zostanie otwarte okno dialogowe **Przygotowywanie**. 

   Przykładowy projekt zawiera domyślnie wybrany plik przygotowywania danych **iris.dprep**. 

   ![Widok danych irysów](media/tutorial-classifying-iris/prepare.png)

1. Utwórz nowy pakiet przygotowywania danych, wybierając pozycję **+ Nowy pakiet przygotowywania danych** z menu rozwijanego.

   ![Widok danych irysów](media/tutorial-classifying-iris/prepare_new.png)

1. Wprowadź nową wartość nazwy pakietu (użyj nazwy **iris-1**), a następnie wybierz pozycję **OK**.

   Nowy pakiet przygotowania danych o nazwie **iris-1.dprep** zostanie utworzony i otwarty w edytorze przygotowywania danych.

   ![Widok danych irysów](media/tutorial-classifying-iris/prepare_iris-1.png)

   Teraz wykonajmy kilka podstawowych operacji przygotowania danych. 

1. Zaznacz poszczególne nagłówki kolumn, aby przystąpić do ich edycji. Następnie zmień nazwę każdej kolumny w następujący sposób: 

   W podanej kolejności wprowadź wartości **Długość słupka**, **Szerokość słupka**, **Długość płatka**, **Szerokość płatka** i **Gatunek** odpowiednio w pięciu kolumnach.

   ![Zmienianie nazw kolumn](media/tutorial-classifying-iris/rename_column.png)

1. Zlicz unikatowe wartości:
   1. Wybierz kolumnę **Gatunki**.
   1. Kliknij prawym przyciskiem myszy, aby ją zaznaczyć. 
   1. Z menu rozwijanego wybierz pozycję **Liczba wartości**. 

   Poniżej danych zostanie otwarte okienko **Inspektorzy**. Zostanie wyświetlony histogram z czterema słupkami. Kolumna docelowa ma cztery różne wartości: **Iris-virginica**, **Iris-versicolor**, **Iris-setosa** i wartość **(null)**.

   ![Wybieranie pozycji Liczba wartości](media/tutorial-classifying-iris/value_count.png)

   ![Histogram liczby wartości](media/tutorial-classifying-iris/filter_out.png)

1. Aby odfiltrować wartości null, wybierz słupek „(null)”, a następnie wybierz znak minus (**-**). 

   Po chwili wiersz o wartości (null) zmieni kolor na szary w celu wskazania, że został on odfiltrowany. 

   ![Filtrowanie wartości null](media/tutorial-classifying-iris/filter_out2.png)

1. Zwróć uwagę na poszczególne etapy przygotowywania danych, które zostały szczegółowo opisane w okienku **KROKI**. Każda akcja dotycząca zmiany nazw kolumn i filtrowania wierszy z wartością null została zarejestrowana jako krok przygotowywania danych. Poszczególne kroki można edytować, aby dostosować ich ustawienia, zmienić kolejność i je usunąć.

   ![Kroki](media/tutorial-classifying-iris/steps.png)

1. Zamknij Kreatora przygotowywania danych. Wybierz ikonę **x** na karcie **iris-1** z ikoną wykresu, aby zamknąć kartę. Twoja praca zostanie automatycznie zapisana w pliku **iris-1.dprep** wyświetlanym w obszarze oznaczonym nagłówkiem **Przygotowywanie danych**.

   ![Zamykanie](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generowanie kodu Python/PySpark w celu wywołania pakietu przygotowywania danych

 Dane wyjściowe pakietu przygotowywania danych można eksplorować bezpośrednio w języku Python lub w notesie Jupyter. Pakiety mogą być wykonywane w różnych środowiskach uruchomieniowych, między innymi w lokalnych środowiskach języka Python, aparatu Spark (w tym Docker) i usługi HDInsight. 

1. Znajdź plik **iris-1.dprep** na karcie Przygotowywanie danych.

1. Kliknij prawym przyciskiem myszy plik **iris-1.dprep**, a następnie z menu kontekstowego wybierz opcję **Generuj plik kodu dostępu do danych**. 

   ![Generowanie kodu](media/tutorial-classifying-iris/generate_code.png)

   Zostanie otwarty nowy plik o nazwie **iris-1.py** z następującymi wierszami kodu służącymi do wywołania logiki utworzonej jako pakiet przygotowywania danych:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   W zależności od kontekstu uruchamiania kodu element `df` reprezentuje inny rodzaj ramki DataFrame:
   + Podczas wykonywania w środowisku uruchomieniowym języka Python jest używana ramka danych [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).
   + Podczas wykonywania w kontekście aparatu Spark jest używana ramka danych [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html). 
   
   Aby dowiedzieć się więcej na temat przygotowywania danych w aplikacji Azure Machine Learning Workbench, zapoznaj się z przewodnikiem [Get started with data preparation (Wprowadzenie do przygotowywania danych)](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja Azure Machine Learning Workbench została użyta w celu wykonania następujących czynności:
> [!div class="checklist"]
> * Tworzenie nowego projektu
> * Tworzenie pakietu przygotowywania danych
> * Generowanie kodu Python/PySpark w celu wywołania pakietu przygotowywania danych

Możesz teraz przejść do następnej części serii samouczków, w której dowiesz się, w jaki sposób utworzyć model usługi Azure Machine Learning:
> [!div class="nextstepaction"]
> [Samouczek 2: tworzenie modeli](tutorial-classifying-iris-part-2.md)
