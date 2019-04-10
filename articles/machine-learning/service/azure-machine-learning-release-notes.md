---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o najnowszych aktualizacjach usługi Azure Machine Learning i uczenia maszynowego i zestawy SDK Python przeznaczonego do przygotowania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 5bc67551c70b185ad0b5ed993a510ded2229481f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279622"
---
# <a name="azure-machine-learning-service-release-notes"></a>Informacje o wersji usługi Azure Machine Learning

Ten artykuł zawiera informacje o wersji usługi Azure Machine Learning.  Aby uzyskać pełny opis każdego zestawu SDK odwiedź dokumenty referencyjne dotyczące:
+ Usługi Azure Machine Learning [ **głównego zestawu SDK dla języka Python**](https://aka.ms/aml-sdk)
+ Usługi Azure Machine Learning [ **przygotowania danych zestawu SDK**](https://aka.ms/data-prep-sdk)

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.23

+ **Nowe funkcje**
  + Zestaw SDK usługi Azure Machine Learning obsługuje teraz 3.7 języka Python.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Zestaw SDK v1.1.1 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  + Może odczytywać wielu źródeł magazynu danych/ścieżki danych/element DataReference, przy użyciu read_ * przekształcenia.
  + Można wykonywać następujące operacje na kolumnach, aby utworzyć nową kolumnę: dzielenie, piętro modulo mocy, długość.
  + Przygotowywanie danych jest teraz częścią pakietu diagnostyki usługi uczenie Maszynowe Azure i będzie rejestrować informacje diagnostyczne domyślnie.
    + Aby wyłączyć tę funkcję, należy ustawić tej zmiennej środowiskowej na wartość true: DISABLE_DPREP_LOGGER

+ **Poprawki błędów i ulepszenia**
  + Dokumentacja ulepszone kod często używane klasy i funkcje.
  + Usunięto usterkę w auto_read_file, którego nie można odczytać plików programu Excel.
  + Dodano opcję, aby zastąpić istniejący folder w read_pandas_dataframe.
  + Lepsza wydajność dotnetcore2 zależności instalacji i dodanie obsługi Fedora 27/28 i Ubuntu 1804.
  + Zwiększona wydajność czytanie z obiektów blob platformy Azure.
  + Wykrywanie typu kolumny teraz obsługuje kolumny typu Long.
  + Usunięto usterkę, w której niektóre wartości daty były wyświetlane jako sygnatury czasowe zamiast obiektów daty/godziny w języku Python.
  + Usunięto usterkę, w której niektóre liczniki typu były wyświetlane jako wartości podwójnej precyzji, zamiast liczb całkowitych.
  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.21

+ **Nowe funkcje**
  + *Azureml.core.Run.create_children* metoda umożliwia tworzenie małych opóźnieniach wiele podrzędnych uruchamiany przy użyciu jednego wywołania.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Zestaw SDK v1.1.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Zmiany powodujące niezgodność**
  + Pojęcie pakiet przygotowania bazy danych jest przestarzała i nie jest już obsługiwana. Zamiast utrwalanie wiele przepływów danych w jednym pakiecie, jednak można utrwalić przepływów danych indywidualnie.
    + Przewodnik: [Otwieranie i zapisywanie przepływów danych notesu](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nowe funkcje**
  + Przygotowywanie danych teraz może rozpoznawać kolumn, które dopasować określony typu semantycznego i dzielenie się odpowiednio. STypes aktualnie obsługiwane obejmują: adres e-mail, współrzędne geograficzne (szerokość i długość geograficzna), adresy IPv4 i IPv6, numer telefonu w Stanach Zjednoczonych oraz kodem pocztowym.
    + Przewodnik: [Semantyczne notesu typów](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Przygotowywanie danych obsługuje teraz następujące operacje do generowania wynikowe kolumny z dwóch kolumnach liczbowych: odejmowanie, mnożenie, dzielenie i modulo.
  + Możesz wywołać `verify_has_data()` na przepływ danych w celu sprawdzenia, czy przepływ danych dałby w efekcie rekordy jeśli wykonywane.

+ **Poprawki błędów i ulepszenia**
  + Można teraz określić liczbę pojemniki do użycia w histogram kolumny liczbowej profilów.
  + `read_pandas_dataframe` Przekształcenie teraz wymaga DataFrame mieć parametry - lub bajt typowanie nazw kolumn.
  + Usunięto usterkę w `fill_nulls` brakuje transformacji, gdzie wartości zostały poprawnie wypełniony. Jeśli kolumna.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.18

 + **Zmiany**
   + Pakiet Azure ml tensorboard zastępuje usługę Azure ml — contrib-tensorboard.
   + W tej wersji można skonfigurować konto użytkownika na klastrze zarządzanych zasobów obliczeniowych (amlcompute), podczas jego tworzenia. Można to zrobić, po prostu przekazując tych właściwości w konfiguracji Inicjowanie obsługi administracyjnej elementu. Można znaleźć więcej szczegółów w [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Zestaw SDK v1.0.17 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  + Obsługuje teraz dodawanie dwóch kolumnach liczbowych do generowania wynikowe kolumny przy użyciu języka wyrażeń.

+ **Poprawki błędów i ulepszenia**
  + Ulepszone dokumentacji i Sprawdzanie parametrów dla random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Zestaw SDK v1.0.16 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Naprawienie usterki**
  + Rozwiązano nazwy głównej usługi problem z uwierzytelnianiem, które zostało spowodowane przez zmiany interfejsu API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.17

+ **Nowe funkcje**

  + Usługa Azure Machine Learning teraz obsługuje najwyższej klasy popularnego środowiska DNN Chainer. Za pomocą [ `Chainer` ](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) klasy użytkowników można łatwo uczenie i wdrażanie modeli Chainer.
    + Dowiedz się, jak [Uruchom rozproszonego szkolenia z ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Dowiedz się, jak [Uruchom strojenia hiperparametrycznego przy użyciu HyperDrive Chainer](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure potoków uczenia maszynowego dodano możliwość wyzwalacza, uruchomienie potoku, zależnie od zmian magazynu danych. Potok [notesu harmonogram](https://aka.ms/pl-schedule) zostanie zaktualizowany w celu uwzględnienia tej funkcji.

+ **Poprawki błędów i ulepszenia**
  + Dodaliśmy obsługę potoki usługi Azure Machine Learning do ustawiania właściwości source_directory_data_store żądanego magazynu danych (takich jak magazyn obiektów blob) na [RunConfigurations](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , są dostarczane do [ PythonScriptStep](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Domyślnie kroki używają magazyn plików Azure jako zapasowy magazyn danych, które mogą być uruchamiane do ograniczania problemy, gdy dużą liczbę czynności są wykonywane jednocześnie.

### <a name="azure-portal"></a>Azure Portal

+ **Nowe funkcje**
  + Nowe przeciągania i upuszczania tabeli środowisko edytora raportów. Użytkownikom można przeciągnąć kolumnę z obszaru do obszaru tabeli, w której będzie można wyświetlić podglądu tabeli. Można zmieniać kolejności kolumn.
  + Nowy Podgląd plików dzienników
  + Linki do eksperymentów przebiegów, obliczeniowych, modele, obrazy i wdrożeń, na karcie działania

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Zestaw SDK v1.0.15 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  + Zapisywanie plików strumieni z przepływu danych obsługuje teraz przygotowania bazy danych. Także zapewnia możliwość modyfikowania nazwy strumienia plików do tworzenia nowych nazw plików.
    + Przewodnik: [Praca z strumieni plików notesu](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Poprawki błędów i ulepszenia**
  + Zwiększono wydajność t-Digest, w dużych zestawach danych.
  + Przygotowywanie danych obsługuje teraz odczytu danych ze ścieżki danych.
  + Gorąca kodowań działa teraz w kolumnach boolean i numeryczne.
  + Inne różne poprawki błędów.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.15

+ **Nowe funkcje**
  + Azure potoków uczenia maszynowego dodano AzureBatchStep ([notesu](https://aka.ms/pl-azbatch)), HyperDriveStep ([notesu](https://aka.ms/pl-hyperdrive)) i na podstawie czasu planowania funkcji ([notesu](https://aka.ms/pl-schedule)).
  +  DataTranferStep zaktualizowane do pracy z serwera SQL Azure i usługa Azure database for PostgreSQL ([notesu](https://aka.ms/pl-data-trans)).

+ **Zmiany**
  + Przestarzałe `PublishedPipeline.get_published_pipeline` uzyskać `PublishedPipeline.get`.
  + Przestarzałe `Schedule.get_schedule` uzyskać `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Zestaw SDK v1.0.12 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  + Odczytywanie z usługi Azure SQL database przy użyciu magazynu danych obsługuje teraz przygotowania bazy danych.
 
+ **Zmiany**
  + Znaczne zwiększenie wydajności pamięci niektórych działań na dużej ilości danych.
  + `read_pandas_dataframe()` wymagane jest teraz `temp_folder` należy określić.
  + `name` Właściwość `ColumnProfile` został przestarzałe — użyj `column_name` zamiast tego.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.10

+ **Zmiany**: 
  + Zestaw SDK usługi Azure ML nie ma już pakiety wiersza polecenia platformy azure jako zależność. W szczególności zależności azure-cli-core i profilu w przypadku interfejsu wiersza polecenia platformy azure zostały usunięte z usługi Azure ml core. Poniżej przedstawiono użytkownika, wpływ na zmiany:
    + Jeśli wykonanie "az login", a następnie za pomocą zestawu sdk usługi Azure ml, zestaw SDK będzie wykonywać przeglądarki lub logowanie do urządzenia kod jeszcze raz. Nie będzie ona używać każdy stan poświadczeń utworzonych przez "az login".
    + Uwierzytelnianie wiersza polecenia platformy Azure, takiej jak "az login", można użyć _azureml.core.authentication.AzureCliAuthentication_ klasy. Uwierzytelnianie wiersza polecenia platformy Azure, wykonaj _polecenia pip install azure cli_ w środowisku Python, w którym jest zainstalowany zestaw sdk usługi Azure ml.
    + Jeśli macie "az login", przy użyciu nazwy głównej usługi do automatyzacji, zalecamy użycie _azureml.core.authentication.ServicePrincipalAuthentication_ klasy, jak zestaw sdk usługi Azure ml nie będzie używać stan poświadczeń utworzonych przez wiersza polecenia platformy azure. 

+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Zestaw SDK v1.0.8 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Poprawki błędów**
  + Znaczne zwiększenie wydajności pobieranie danych profilów.
  + Naprawiono drobnych usterek związane z raportowaniem błędów.
  
### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Nowe przeciągnij i upuść środowisko tworzenia wykresów dla raportów. Użytkownikom można przeciągnąć kolumny lub atrybutu z obszaru do obszaru wykresu, do której system automatycznie wybiera typ odpowiedniego wykresu dla użytkownika, na podstawie typu danych. Użytkownikom można zmienić typ wykresu do innych odpowiednich typów lub dodawać dodatkowe atrybuty.

    Obsługiwane typy wykresów:
    - Wykres liniowy
    - Histogram
    - Skumulowany wykres słupkowy
    - Wykres skrzynkowy
    - Wykres punktowy
    - Wykres bąbelkowy
+ Portal dynamicznie generuje raporty dotyczące eksperymentów. Po użytkownik prześle przebieg, aby eksperymentu, zostanie automatycznie wygenerowany raport za pomocą zarejestrowanych metryki i wykresy, aby zezwolić na porównania we wszystkich przebiegach aktualizacji innej. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.8

+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Zestaw SDK v1.0.7 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  + Ulepszenia magazynu danych (udokumentowane w artykule [magazyn danych jak-to-przewodnik](https://aka.ms/aml-data-prep-datastore-nb))
    + Dodano możliwość odczytu i zapisu do udziału plików platformy Azure i magazynów danych usługi ADLS w skalowanie w górę.
    + Korzystając z magazynów danych, przygotowywanie danych obsługuje teraz przy użyciu uwierzytelniania jednostki usługi zamiast przeprowadzić uwierzytelnianie interakcyjne.
    + Dodano obsługę wasb i wasbs adresów URL.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Zestaw SDK v1.0.6 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Poprawki błędów**
  + Usunięto usterkę podczas odczytywania z publiczne kontenery obiektów Blob platformy Azure do odczytu, na platformie Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.6
+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Zestaw SDK v1.0.4 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  + `to_bool` Teraz dzięki funkcji niezgodne wartości są konwertowane na wartości błędów. Jest to nowe zachowanie niezgodność dla `to_bool` i `set_column_types`, podczas gdy poprzednie zachowanie domyślne można przekonwertować niezgodne wartości FALSE.
  + Podczas wywoływania `to_pandas_dataframe`, istnieje nowa opcja do interpretacji null/Brak wartości w kolumnach liczbowych jako NaN.
  + Dodano możliwość sprawdzenia, zwracany typ niektóre wyrażenia w celu zapewnienia spójności typu i wczesna Porażka.
  + Teraz można wywołać `parse_json` przeanalizować wartości w kolumnie jako obiekty JSON i rozszerzać je na wiele kolumn.

+ **Poprawki błędów**
  + Naprawiono usterkę, która uległa awarii `set_column_types` w języku Python 3.5.2.
  + Naprawiono usterkę, która uległa awarii podczas nawiązywania połączenia z magazynem danych przy użyciu obrazu AML.

+ **Aktualizacje**
  * [Przykład notesów](https://aka.ms/aml-data-prep-notebooks) w celu uzyskania pracy — samouczki, analiz przypadków i przewodniki z instrukcjami.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Ogólna dostępność

Usługa Azure Machine Learning jest teraz ogólnie dostępna.

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń
W tej wersji, Pragniemy zaprezentować nowe środowisko obliczeniowe zarządzane za pośrednictwem [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Ten cel obliczenia zastępuje usługi Azure Batch AI zasoby obliczeniowe na potrzeby usługi Azure Machine Learning. 

Ten element docelowy obliczeniowych:
+ Służy do modelu wnioskowania szkolenie i usługi batch
+ Jest jednym — do wielu - node obliczeń
+ Obsługuje zarządzanie klastrowania i planowania zadań dla użytkownika
+ Skalowania domyślnie
+ Obsługa zasobów procesora CPU i procesora GPU 
+ Umożliwia korzystanie z maszyn wirtualnych o niskim priorytecie w obniżonej cenie

Usługa Azure obliczeniowego usługi Machine Learning można tworzyć w języku Python, za pomocą witryny Azure portal lub interfejsu wiersza polecenia. Należy utworzyć w regionie Twojego obszaru roboczego i nie można dołączyć z innym obszarem roboczym. Ten cel obliczenia używa kontenera platformy Docker na potrzeby przebieg, a pakiety zależności replikowanie tym samym środowisku na wszystkie węzły.

> [!Warning]
> Zalecamy utworzenie nowego obszaru roboczego, aby użyć obliczeniowego usługi Azure Machine Learning. Brak zdalnego prawdopodobieństwo, że w trakcie tworzenia obliczeniowego usługi Azure Machine Learning z istniejącym obszarem roboczym użytkownicy będą widzieć błąd. Istniejących zasobów obliczeniowych, w obszarze roboczym będą nadal działać bez zmian.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.2
+ **Zmiany powodujące niezgodność**
  + W tej wersji zostaną usunięte obsługę tworzenia maszyny Wirtualnej z usługi Azure Machine Learning. Nadal możesz dołączyć istniejącej chmury maszyny Wirtualnej lub zdalnej na serwerze lokalnym. 
  + Obsługa BatchAI, które powinny być wspierane za pośrednictwem obliczeniowego usługi Azure Machine Learning teraz również zostaną usunięte.

+ **Nowa**
  + Dla potoków uczenia maszynowego:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Zaktualizowana**
  + Dla potoków uczenia maszynowego:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) teraz akceptuje runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) teraz kopiuje do i z źródła danych SQL
    + Planowanie funkcji zestawu SDK do tworzenia i aktualizowania harmonogramy uruchamiania potoków opublikowane

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Zestaw SDK v0.5.2 przeznaczonego do przygotowania danych usługi Azure Machine Learning
+ **Zmiany powodujące niezgodność** 
  * `SummaryFunction.N` Nazwa została zmieniona na `SummaryFunction.Count`.
  
+ **Poprawki błędów**
  * Użyj najnowszej AML Uruchom tokenu podczas odczytywanie z oraz zapisywanie do magazynów danych na zdalnych przebiegów. Wcześniej Jeśli tokenu Uruchom AML zostanie zaktualizowany w języku Python, przygotowywanie danych środowiska uruchomieniowego nie zostanie zaktualizowany przy użyciu zaktualizowanych AML Uruchom tokenu.
  * Dodatkowe przejrzyste komunikaty o błędach
  * to_spark_dataframe() już ulegnie awarii, jeśli korzysta z platformy Spark `Kryo` serializacji
  * Liczba wartości Inspector umożliwia teraz wyświetlanie więcej niż 1000 unikatowe wartości
  * Losowe podziału nie jest już nie powiedzie się jeśli oryginalny przepływu danych nie ma nazwy  

+ **Więcej informacji**
  * [Usługi Azure Machine Learning przygotowywanie danych zestawu SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty i notesów
+ Potokach uczenia Maszynowego
  + Nowe i zaktualizowane notesów dla klientów zaczynających się od potoki, określania zakresu usługi batch i styl transferu przykłady: https://aka.ms/aml-pipeline-notebooks
  + Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md)
  + Dowiedz się, jak [Uruchom prognozy usługi batch przy użyciu potoków](how-to-run-batch-predictions.md)
+ Cel obliczenia w usłudze Azure Machine Learning
  + [Przykładowy notesów](https://aka.ms/aml-notebooks) są teraz aktualizowane do użycia nowe obliczenie zarządzanych.
  + [Dowiedz się więcej o tym obliczeń](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Tworzenie i zarządzanie nimi [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) typów w portalu.
+ Monitoruj użycie przydziału i [zażądać przydziału](how-to-manage-quotas.md) Azure obliczeniowego usługi Machine Learning.
+ Wyświetl stan klastra obliczeniowego usługi Azure Machine Learning w czasie rzeczywistym.
+ Tworzenie obliczeniowego usługi Azure Machine Learning i usługi Azure Kubernetes Service została dodana obsługa sieci wirtualnej.
+ Należy ponownie uruchomić potoków opublikowanych przy użyciu istniejących parametrów.
+ Nowe [zautomatyzowane machine learning wykresy](how-to-track-experiments.md#auto) modeli klasyfikacji (przyrostu zyski, odwzorowania, funkcja znaczenie na wykresie słupkowym: explainability modelu) i modele regresji (reszty i funkcja znaczenie wykresu za pomocą modelu explainability). 
+ Potoki mogą być wyświetlane w witrynie Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.80

+ **Zmiany powodujące niezgodność** 
  * *azureml.Train.widgets* przestrzeń nazw została przeniesiona do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* następujące klasy — na podstawie której zaniechane *azureml.core.compute.BatchAICompute* i *azureml.core.compute.DSVMCompute*. Ostatnie klasy zostaną usunięte w następnych wersjach. Klasa AmlCompute ma teraz łatwiej definicję po prostu potrzebuje vm_size i max_nodes i automatycznie skaluje klaster z zakresu od 0 do max_nodes po przesłaniu zadania. Nasze [przykładowy notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zostały zaktualizowane przy użyciu tych informacji i powinny zawierać przykłady użycia. Mamy nadzieję, że możesz np. to uproszczenie i wiele więcej fascynujących funkcji są dostępne w nowszej wersji!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Zestaw SDK v0.5.1 przeznaczonego do przygotowania danych usługi Azure Machine Learning 

Więcej informacji na temat zestawu SDK usługi Data Prep, czytając [odwołania docs](https://aka.ms/data-prep-sdk).
+ **Nowe funkcje**
   * Utworzony nowy interfejs wiersza polecenia przygotowania danych do wykonywania pakietów przygotowania danych i wyświetlić profil danych dla zestawu danych i przepływu danych
   * Przeprojektowana SetColumnType interfejsu API w celu zwiększenia użyteczności
   * Zmieniono nazwę smart_read_file do auto_read_file
   * Udostępnia teraz przesunięcia czasowego i kurtoza profilu danych
   * Próbkowanie z włączonym próbkowaniem stratyfikowana
   * Może odczytywać dane z plików zip, które zawierają pliki CSV
   * Można podzielić zestawów danych row-wise za pomocą losowych podziału (na przykład zestawy testów train)
   * Można uzyskać wszystkie kolumny typów danych z przepływu danych lub profil danych przez wywołanie metody `.dtypes`
   * Można pobrać liczbę wierszy z przepływu danych lub profil danych przez wywołanie metody `.row_count`

+ **Poprawki błędów**
   * Stałą Długa podwójna konwersja 
   * Naprawiono assert po dowolnej Dodaj kolumnę 
   * Rozwiązano problem z FuzzyGrouping, gdzie nie może nie wykryć grup w niektórych przypadkach
   * Funkcja stałej sortowania przestrzegają kolejność sortowania wielokolumnowych
   * Naprawiono i/lub wyrażenia są podobne do jak `pandas` je obsługuje
   * Naprawiono odczytu ze ścieżki dbfs
   * Wprowadzone bardziej zrozumiałe komunikaty o błędach 
   * Teraz już nie kończy się niepowodzeniem podczas odczytywania na zdalnym obliczeniowych elementów docelowych przy użyciu tokenu AML
   * Teraz już nie kończy się niepowodzeniem na DSVM systemu Linux
   * Teraz już ulega awarii podczas wartości innych niż ciąg znajdują się w ciągu predykatów
   * Teraz obsługuje błędy potwierdzenia, gdy przepływ danych powinna zakończyć się niepowodzeniem poprawnie
   * Obsługuje teraz lokalizacje przechowywania dbutils zainstalowanego w usłudze Azure Databricks

## <a name="2018-11-05"></a>05-11-2018 r.

### <a name="azure-portal"></a>Azure Portal 
Witryny Azure portal dla usługi Azure Machine Learning ma następujące aktualizacje:
  * Nowy **potoki** kartę opublikowanych potoków.
  * Dodano obsługę dołączania do istniejącego klastra HDInsight, jako cel obliczenia.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.74

+ **Zmiany powodujące niezgodność** 
  * * Workspace.compute_targets, magazynów danych, eksperymentów, obrazy, modele i *usług sieci Web* właściwości zamiast metod. Na przykład Zastąp ciąg *Workspace.compute_targets()* z *Workspace.compute_targets*.
  * *Run.get_context* traktuje jako przestarzałą *Run.get_submitted_run*. Druga metoda zostaną usunięte w następnych wersjach.
  * *PipelineData* klasy teraz oczekuje obiektu magazynu danych jako parametr zamiast datastore_name. Podobnie *potoku* akceptuje default_datastore zamiast default_datastore_name.

+ **Nowe funkcje**
  * Potoków uczenia maszynowego Azure [przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) używa teraz kroki MPI.
  * Element widget RunDetails dla notesów programu Jupyter jest aktualizowana w celu wyświetlenia wizualizacja potoku.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Zestaw SDK v0.4.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning 
 
+ **Nowe funkcje**
  * Liczba typów dodawane do profilu danych 
  * Liczba wartości i histogramu jest teraz dostępna
  * Więcej percentyle w profilu danych
  * Mediana jest dostępna w podsumowanie
  * Python 3.7 jest teraz obsługiwane.
  * Po zapisaniu przepływu danych, który zawiera magazynów danych na pakiet przygotowania danych jako część pakietu przygotowania danych zostaną utrwalone dane magazynu danych
  * Zapisywanie do magazynu danych jest teraz obsługiwane. 
        
+ **Naprawiono usterkę**
  * 64-bitowej nieoznaczonej liczby całkowitej przepełnienia są teraz obsługiwane poprawnie w systemie Linux
  * Stały tekst Nieprawidłowa etykieta plikami ze zwykłym tekstem w smart_read
  * Typ kolumny ciągu teraz zostaną wyświetlone w widoku metryk
  * Liczba typów jest rozwiązano problem, aby pokazać ValueKinds mapowany do jednego typu pola, a nie pojedyncze pliki
  * Write_to_csv już nie kończy się niepowodzeniem podczas ścieżka jest podana jako ciąg
  * Korzystając z Zastąp, pozostawiając "wyszukiwanie" puste nie są już nie powiedzie się 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.68

+ **Nowe funkcje**
  * Obsługa wielu dzierżaw, podczas tworzenia nowego obszaru roboczego.

+ **Usterki usunięte**
  * Nie trzeba przypiąć wersji biblioteki pynacl podczas wdrażania usługi sieci web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Zestaw SDK v0.3.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  * Dodano transform_partition_with_file(script_path) metody, która umożliwia użytkownikom na przekazywanie, ścieżkę pliku Python do wykonywania

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.65
[Wersja 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) zawiera nowe funkcje, więcej dokumentacji, poprawki i [przykładowy notesów](https://aka.ms/aml-notebooks).

Zobacz [listę znanych problemów](resource-known-issues.md) informacje na temat znanych błędów i rozwiązania problemu.

+ **Zmiany powodujące niezgodność**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets Workspace.images Workspace.web_services słownika zwrotu, poprzednio zwróconej liście. Zobacz [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentacji interfejsu API.

  * Automatyczne usługi Machine Learning usunąć błąd znormalizowane średniej kwadratowej z podstawowe metryki.

+ **HyperDrive**
  * Poprawki błędów różnych HyperDrive Bayesowskie, ulepszenia wydajności dotyczące pobrania wywołania metryki. 
  * Uaktualnienie Tensorflow 1.10 1.9 
  * Optymalizacja obrazów platformy docker do zimnego. 
  * Zadania teraz raport prawidłowy stan, nawet wtedy, gdy ich zakończyć pracę z kodem błędu niż 0. 
  * Sprawdzanie poprawności atrybutów RunConfig w zestawie SDK. 
  * Obiekt HyperDrive Uruchom obsługuje anulowanie podobne do regularnego wykonywania: nie trzeba przekazywać żadnych parametrów. 
  * Widżet ulepszenia dotyczące utrzymania stan z listy rozwijanej wartości dla rozproszonej przebiegów oraz uruchomień HyperDrive. 
  * Narzędzia TensorBoard i innych dziennik, który obsługuje pliki Naprawiono parametr serwera. 
  * Obsługa MPI Intel(R) po stronie usługi. 
  * Poprawka do szczegółowego dostosowywania parametrów dla rozproszonego wykonywania awarii podczas sprawdzania poprawności w BatchAI. 
  * Kontekst Manager identyfikuje teraz podstawowe wystąpienie. 

+ **Środowisku witryny Azure portal**
  * log_table() i log_row() są obsługiwane w szczegóły przebiegu. 
  * Automatycznie tworzyć wykresy, tabele i wiersze, 1, 2 lub 3 kolumny liczbowe i opcjonalna kolumna podzielonych na kategorie.

+ **Zautomatyzowane uczenie maszynowe**
  * Ulepszona obsługa błędów i dokumentacji 
  * Naprawiono pobieranie właściwości uruchamiania problemy z wydajnością. 
  * Naprawiono kontynuować wykonywania problem. 
  * Rozwiązano ensembling problemy iteracji.
  * Naprawiono szkolenia zwisa błędów w systemie MAC OS.
  * Próbkowanie — makro średni żądania Ściągnięcia/WIELOKLASOWA krzywa w scenariuszu niestandardowego sprawdzania poprawności.
  * Usunięty indeks dodatkowej logiki.
  * Usunąć filtr z get_output interfejsu API.

+ **Potoki**
  * Dodaje metody Pipeline.publish() publikowanie potoku bezpośrednio, bez konieczności wykonywania są uruchamiane jako pierwsze.   
  * Dodaje metody PipelineRun.get_pipeline_runs() można pobrać uruchomień potoków, które zostały wygenerowane z opublikowanych potoku.

+ **Project Brainwave**
  * Zaktualizowano obsługę na układów FPGA nowe modele sztucznej Inteligencji.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Zestaw SDK v0.2.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning
[Wersja 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) obejmuje następujące funkcje i poprawki:

+ **Nowe funkcje**
  * Obsługa hot jeden kodowania
  * Obsługa przekształcania kwantyl
   
+ **Usterka naprawiona:**
  * Działa z dowolną wersją Tornado nie ma potrzeby obniżyć wersji Tornado
  * Liczba wartości dla wszystkich wartości nie tylko trzy pierwsze

## <a name="2018-09-public-preview-refresh"></a>2018-09 (w wyniku odświeżenia publiczna wersja zapoznawcza)

Nowy, odświeżana wersji usługi Azure Machine Learning: Dowiedz się więcej na temat tej wersji: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z omówieniem dla [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md).
