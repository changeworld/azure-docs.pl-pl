---
title: Konserwacji predykcyjnej w rzeczywistych scenariuszach | Dokumentacja firmy Microsoft
description: Konserwacji predykcyjnej w rzeczywistych scenariuszach przy użyciu PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: bb6e4f9f147db2fb70d991922cf0bb8d16b29671
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Konserwacji predykcyjnej, do rzeczywistych scenariuszy

Wpływ urządzeń nieplanowanych przestojów mogą być szkodliwe dla dowolnego rodzaju działalności. Warto zachować urządzeń pola uruchomiona w celu zmaksymalizowania wykorzystania i wydajności oraz zminimalizować kosztowne, nieplanowanych przestojów. Wczesne identyfikację problemów mogą pomóc przydzielić zasoby obsługi ograniczony w sposób ekonomiczny i zwiększyć jakość i procesy łańcucha dostaw. 

W tym scenariuszu Eksploruje stosunkowo [na dużą skalę symulowane zestawu danych](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) przechodzenia przez projekt nauki konserwacji predykcyjnej danych z wprowadzanie danych, funkcji zespołu inżynieryjnego, konstruowania modelu i operationalization modelu i wdrożenia. Kod dla całego procesu są zapisywane w notesu Jupyter przy użyciu PySpark w Azure Machine Learning Workbench. Ostatecznego modelu jest wdrażane za pomocą Azure Machine Learning Model zarządzania w celu prognozowania awarii sprzętu w czasie rzeczywistym.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Repozytorium Cortana Intelligence Gallery GitHub

Cortana Intelligence Gallery samouczek PM jest publicznego repozytorium GitHub ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) gdzie zgłaszania problemów i utworzyć udziały.


## <a name="use-case-overview"></a>Omówienie przypadków użycia

Poważny problem, który stoi przez firmy w branży ciężki zasobów jest znacznych kosztów, skojarzonych z opóźnienia z powodu problemów z mechaniczne. Większość firm są zainteresowani przewidywania, gdy te problemy mogą wystąpić do aktywnego zapobiec, przed ich wystąpieniem. Celem jest zmniejszenie kosztów, zmniejszając czas przestoju i możliwego zwiększania bezpieczeństwa. 

W tym scenariuszu przyjmuje pomysłów [podręcznikowym konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) do pokazują, jak utworzyć model predykcyjny dla symulowane zestawu danych. Przykładowe dane pochodzi z typowymi elementami, które są przestrzegane w wielu przypadkach użycia konserwacji predykcyjnej.

Problem biznesowy symulowane danych jest przewidywanie problemów, które są spowodowane przez błędy składnika. To pytanie biznesowe "*jakie jest prawdopodobieństwo, że maszyna przestanie działać z powodu błędu składnika?*" Ten problem jest formatowana jako problem klasyfikacji wielu klas (wiele składników dla poszczególnych komputerów). Algorytmu uczenia maszynowego służy do tworzenia modeli predykcyjnych. Model jest uczony w danych historycznych, które mają być zbierane z maszyn. W tym scenariuszu użytkownik przechodzi przez różne czynności, aby zaimplementować modelu w danym środowisku Machine Learning Workbench.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
* Zainstalowana kopia programu [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Postępuj zgodnie z [Przewodnik Szybki Start instalacji](./quickstart-installation.md) Aby zainstalować ten program i utworzyć obszaru roboczego.
* Azure Machine Learning Operationalization wymaga środowiska lokalnego wdrożenia i [konta Azure Machine Learning Model zarządzania](model-management-overview.md).

W tym przykładzie jest uruchamiane w dowolnym kontekście obliczeń Machine Learning Workbench. Jednak zaleca się uruchomienie w przykładzie z co najmniej 16 GB pamięci. W tym scenariuszu została zbudowana i przetestowany na maszynie z systemem Windows 10 z systemem zdalnym standard DS4_V2 [danych nauki maszyny wirtualnej (DSVM) dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Model operationalization została wykonana za pomocą wersji 0.1.0a22 z interfejsu wiersza polecenia Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt, korzystając z tego przykładu jako szablon:
1.  Otwórz aplikację Machine Learning Workbench.
2.  Na **projekty** wybierz pozycję **+**, a następnie wybierz **nowy projekt**.
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu.
4.  W **szablony projektów wyszukiwania** polu wyszukiwania wpisz "Konserwacji predykcyjnej", wybierz **konserwacji predykcyjnej** szablonu.
5.  Wybierz pozycję **Utwórz**.

## <a name="prepare-the-notebook-server-computation-target"></a>Przygotowanie docelowy obliczeniowym serwera notesu

Uruchamianie na komputerze lokalnym, z Machine Learning Workbench **pliku** menu, wybierz opcję **Otwórz okno wiersza polecenia** lub **Otwórz interfejs wiersza polecenia programu PowerShell**. Interfejs interfejsu wiersza polecenia umożliwia dostęp do usług platformy Azure przy użyciu `az` poleceń. Po pierwsze Zaloguj się do konta platformy Azure przy użyciu polecenia:

```
az login
``` 

To polecenie dostarcza klucz uwierzytelniania do użycia z https:\\aka.ms\devicelogin adresu URL. Interfejsu wiersza polecenia oczekuje, aż operację logowania za pomocą urządzenia zwraca i udostępnia pewne informacje o połączeniu. Dalej, jeśli masz lokalnym [Docker](https://www.docker.com/get-docker) instalacji, przygotować środowisko lokalnym przy użyciu polecenia:

```
az ml experiment prepare --target docker --run-configuration docker
```

Zaleca się uruchamiania na [DSVM dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) wymagania dotyczące pamięci i dysku. Po skonfigurowaniu DSVM przygotować środowisko Docker zdalnego za pomocą następujących dwóch poleceń:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po nawiązaniu połączenia zdalnego kontenera Docker przygotowanie środowiska obliczeniowego DSVM Docker przy użyciu polecenia: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Środowiska obliczeniowe Docker przygotowany, otwórz serwera notesu Jupyter z Azure Machine Learning Workbench **notesów** karcie lub uruchamiania serwera opartego na przeglądarce przy użyciu polecenia: 

```
az ml notebook start
```

Przykład notesów są przechowywane w katalogu kodu. Notebooki są skonfigurowane do uruchamiają się po kolei, uruchomienie na pierwszy notesu (Code\1_data_ingestion.ipynb). Po otwarciu każdego notesu monit wybierz jądro obliczeniowe. Wybierz jądra _Template [Connection_Name] [Project_Name] do wykonania na DSVM wcześniej skonfigurowany.

## <a name="data-description"></a>Opis elementu danych

[Symulowane danych](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) składa się z pięciu plików z wartościami rozdzielanymi przecinkami (CSV). Skorzystaj z poniższych linków, aby uzyskać szczegółowe informacje dotyczące zestawów danych.

* [Maszyny](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): funkcje wyróżniającego każdej maszynie, na przykład wieku i modelu.
* [Błąd](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): dziennik błędów zawiera błędy nierozdzielające, które są generowane, gdy komputer jest nadal działa. Te błędy nie są uznawane za błędy, mimo że może być predykcyjnej zdarzenia błędu w przyszłości. Wartości daty i godziny dla błędy są zaokrąglane do najbliższej godziny, ponieważ dane telemetryczne są zbierane z szybkością co godzinę.
* [Obsługa](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): konserwacji dziennika zawiera rekordy zarówno zaplanowanych, jak i nieplanowanych konserwacji. Zaplanowanej konserwacji odpowiada regularnych kontroli składników. Niezaplanowaną konserwację mogą wynikać z awarii mechaniczne lub innych spadek wydajności. Wartości daty i godziny konserwacji są zaokrąglane do najbliższej godziny, ponieważ dane telemetryczne są zbierane z szybkością co godzinę.
* [Dane telemetryczne](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): dane telemetryczne składa się z pomiarów serii czasu z wielu czujników w każdej maszynie. Dane są rejestrowane przez uśrednianie czujnik każdego interwału jedną godzinę.
* [Błędy](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): błędy odpowiadają zamiany składnika w dzienniku konserwacji. Każdy rekord zawiera identyfikator komputera, typu składnika i zastąpienie datę i godzinę. Te rekordy są używane do tworzenia etykiety, które podejmuje próbę prognozowania modelu uczenia maszynowego.

Aby pobrać zestawów danych pierwotnych z repozytorium GitHub i utworzyć zestawy danych PySpark tej analizy, zobacz [wprowadzanie danych](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) scenariusza notesu Jupyter w folderze kodu.

## <a name="scenario-structure"></a>Scenariusz — struktura
Zawartość dla tego scenariusza znajduje się w temacie [repozytorium GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

[Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) plik zawiera opis przepływu pracy z Przygotowanie danych, tworzenia modelu, a następnie wdrażanie rozwiązania do produkcji. Każdy krok przepływu pracy są umieszczane w notesu Jupyter w [kod](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) folder w repozytorium.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): ten notes pobiera pięć plików wejściowych CSV i niektóre Oczyszczanie wstępne danych i wizualizacji. Notesu konwertuje zestawów danych do formatu PySpark i zapisuje go w kontenerze obiektów blob platformy Azure do użycia w notesie Engineering funkcji.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): funkcje modelu są tworzone na podstawie zestawu danych pierwotnych z magazynu obiektów Blob platformy Azure przy użyciu podejścia serii (czas standardowy) dla danych telemetrii, błędy i konserwacji. Zastępcze dotyczące awarii składników są używane do tworzenia etykiety modelu, które opisują, których nie powiodła się. Dane funkcji etykietą jest zapisywany w obiektów blob platformy Azure notesu Kompilowanie modelu.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Model tworzenie notesu korzysta z zestawu danych oznaczonych funkcji i dzieli dane na pociągu i deweloperów zestawów danych wzdłuż sygnaturę daty i godziny. Notesu jest skonfigurowany jako doświadczenia z modelami pyspark.ml.classification. Przekształcono jest danych szkoleniowych. Użytkownik może wypróbować albo **DecisionTreeClassifier** lub **RandomForestClassifier** do manipulowania hyperparameters można znaleźć najbardziej modelu. Wydajność zależy od obliczenia pomiaru statystyki standardowego zestawu danych. Te statystyki są rejestrowane w Machine Learning Workbench ekranu środowiska uruchomieniowego w celu śledzenia. Przy każdym uruchomieniu notesu zapisuje dysk lokalny, który działa jądra notesu Jupyter modelu wynikowego. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): ten notes używa ostatniego modelu jest zapisany na dysku lokalnym (jądro notesu Jupyter) do tworzenia składników do wdrażania modelu do usługi sieci web platformy Azure. Pełne operacyjne zasoby są kompaktowanie w pliku o16n.zip, który jest przechowywany w innym kontenerze obiektów blob platformy Azure. Plik zip zawiera:

* **service_schema.JSON**: plik definicji schematu dla wdrożenia. 
* **pdmscore.PY**: **init()** i **run()** usługi sieci web funkcji, które są wymagane przez platformy Azure.
* **pdmrfull.model**: katalog definicji modelu.
    
Notesu testy funkcji z definicji modelu przed spakowaniem zasoby operationalization dla wdrożenia. Instrukcje dotyczące wdrażania znajdują się na końcu notesu.

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu przedstawiono omówienie tworzenia przy użyciu PySpark w środowisku notesu Jupyter w Machine Learning Workbench rozwiązanie do konserwacji predykcyjnej end-to-end. Ten przykładowy scenariusz zawiera także szczegóły modelu wdrożenia za pośrednictwem środowiska Machine Learning Model zarządzania w celu prognozowania awarii sprzętu w czasie rzeczywistym.

## <a name="references"></a>Dokumentacja

Przykłady innych konserwacji predykcyjnej przypadki użycia dla różnych platform zapewniają następujące informacje:

* [Szablon rozwiązania konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Podręcznik modelowania konserwacji predykcyjnej](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Predykcyjnej konserwacji modelowania przewodnik przy użyciu usług R SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Konserwacji predykcyjnej notesu Python Podręcznik modelowania](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Konserwacji predykcyjnej przy użyciu PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Bezpośrednie uczenia dla konserwacji predykcyjnej](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Kolejne kroki

Inne przykładowe scenariusze są dostępne w Machine Learning Workbench, który zademonstrować dodatkowe funkcje produktu. 
