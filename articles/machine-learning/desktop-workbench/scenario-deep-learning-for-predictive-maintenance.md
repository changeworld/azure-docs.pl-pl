---
title: Głębokie learning konserwacji predykcyjnej rzeczywistych scenariuszach - Azure | Dokumentacja firmy Microsoft
description: Informacje o replikacji samouczek dotyczący uczenia głębokie dla konserwacji predykcyjnej z usługi Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 005470298a115a822350f345cdab6e9cafb15eda
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Głębokie learning konserwacji predykcyjnej rzeczywistych scenariuszy

Głębokie learning jest jednym z najpopularniejszych trendów w uczeniu maszynowym i nie ma aplikacji do wielu obszarach, takich jak:
- Driverless samochodów i robotics.
- Rozpoznawanie mowy i obrazu.
- prognozy finansowe.

Znane również jako sieci neuronowej głębokość (DNN), te metody są inspirowana przez poszczególne grupy neuronów, znajdujące się w ramach inteligencji (biologicznych sieci neuronowej).

Wpływ urządzeń nieplanowanych przestojów mogą być szkodliwe dla dowolnego rodzaju działalności. Warto zachować urządzeń pola do zmaksymalizowania wykorzystania i wydajności i zminimalizować kosztowne, nieplanowanych przestojów. Wczesne identyfikację problemów mogą pomóc przydzielić zasoby obsługi ograniczony w sposób ekonomiczny i zwiększyć jakość i procesy łańcucha dostaw. 

Strategii konserwacji predykcyjnej (PM) używa machine learning metod, aby określić stan urządzenia preemptively przeprowadzania konserwacji w celu uniknięcia wydajność niekorzystny maszyny. W PM dane są zbierane wraz z upływem czasu, aby monitorować stan maszyny, a następnie analizować je w celu znaleźć wzorców do prognozowania błędów. [Długie krótki okres pamięci (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) sieci są atrakcyjny dla tego ustawienia, ponieważ służą one do poznania sekwencji danych.

### <a name="cortana-intelligence-gallery-github-repository"></a>Repozytorium Cortana Intelligence Gallery GitHub

Cortana Intelligence Gallery samouczek PM jest publicznego repozytorium GitHub ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) gdzie zgłaszania problemów i utworzyć udziały.


## <a name="use-case-overview"></a>Omówienie przypadków użycia

W tym samouczku użyto przykładu zdarzenia Uruchom — błąd aparatu symulowane powietrznego aby zademonstrować konserwacji predykcyjnej modelowania procesu. Scenariusz jest opisany w [konserwacji predykcyjnej](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Głównych założeń, w tym ustawieniu jest aparatem stopniowo ze spadkiem swojego okresu istnienia. Degradacji można wykryć w aparat czujnik pomiarów. PM próbuje modelowania relacji między zmiany w wartościach tych czujników i historycznych błędów. Model można następnie prognozowania, aparatu i kiedy może zakończyć się niepowodzeniem w przyszłości, na podstawie bieżącego stanu czujnik pomiarów.

Ten scenariusz tworzy sieć LSTM do prognozowania pozostałych użytkowania (RUL) aparatów powietrznego przy użyciu wartości czujnik historycznych. Scenariusz używa [Keras](https://keras.io/) biblioteki z [Tensorflow](https://www.tensorflow.org/) głębokie learning framework jako aparat obliczeń. Scenariusz przygotowuje LSTM z jednego zestawu aparaty i analiz sieci na podstawie zestawu niewidocznym aparatu.

## <a name="prerequisites"></a>Wymagania wstępne
- [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
- Azure Machine Learning Workbench z utworzono obszar roboczy.
- Dla modelu operationalization: Azure Machine Learning Operationalization ze środowiska lokalnego wdrożenia, konfigurowanie i [konta Azure Machine Learning Model zarządzania](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt, korzystając z tego przykładu jako szablon:

1. Otwórz aplikację Machine Learning Workbench.
2. Na **projekty** wybierz pozycję **+**, a następnie wybierz **nowy projekt**.
3. W **Utwórz nowy projekt** okienku, wprowadź informacje dotyczące nowego projektu.
4. W **szablony projektów wyszukiwania** polu wyszukiwania wpisz "Konserwacji predykcyjnej", wybierz **głębokie Learning w scenariuszu konserwacji predykcyjnej** szablonu.
5. Wybierz pozycję **Utwórz**.

## <a name="prepare-the-notebook-server-computation-target"></a>Przygotowanie docelowy obliczeniowym serwera notesu

Uruchamianie na komputerze lokalnym, z Machine Learning Workbench **pliku** menu, wybierz opcję **Otwórz okno wiersza polecenia** lub **Otwórz interfejs wiersza polecenia programu PowerShell**. Interfejs interfejsu wiersza polecenia umożliwia dostęp do usług platformy Azure przy użyciu `az` poleceń. Po pierwsze Zaloguj się do konta platformy Azure przy użyciu polecenia:

```
az login
``` 

To polecenie dostarcza klucz uwierzytelniania do użycia z https:\\aka.ms\devicelogin adresu URL. Interfejsu wiersza polecenia oczekuje, aż operację logowania za pomocą urządzenia zwraca i udostępnia pewne informacje o połączeniu. Dalej, jeśli masz lokalnym [Docker](https://www.docker.com/get-docker) instalacji, przygotować środowisko lokalnym przy użyciu polecenia:

```
az ml experiment prepare --target docker --run-configuration docker
```

Zaleca się uruchamiania na [danych nauki maszyny wirtualnej (DSVM) dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) wymagania dotyczące pamięci i dysku. Po skonfigurowaniu DSVM przygotować środowisko Docker zdalnego za pomocą następujących dwóch poleceń:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po nawiązaniu połączenia zdalnego kontenera Docker przygotowanie środowiska obliczeniowego DSVM Docker przy użyciu polecenia: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Środowiska obliczeniowe Docker przygotowany, otwórz serwera notesu Jupyter z Machine Learning Workbench **notesów** karcie lub uruchamiania serwera opartego na przeglądarce przy użyciu polecenia: 

```
az ml notebook start
```

Przykład notesów są przechowywane w katalogu kodu. Notebooki są skonfigurowane do uruchamiają się po kolei, uruchomienie na pierwszy notesu (Code\1_data_ingestion.ipynb). Po otwarciu każdego notesu monit wybierz jądro obliczeniowe. Wybierz jądra _Template [Connection_Name] [Project_Name] do wykonania na DSVM wcześniej skonfigurowany.

## <a name="data-description"></a>Opis elementu danych

Szablon używa trzech zestawów danych jako dane wejściowe w plikach PM_train.txt, PM_test.txt i PM_truth.txt. 

- **Dane pociągu**: powietrznego aparat danych uruchom do awarii. Dane pociągu (PM_train.txt) składa się z wieloma szeregów czasowych wielu zmiennych z *cykl* jako jednostki czasu. Obejmuje on 21 odczyty czujników dla każdego cyklu. 

    - Każdy szeregu czasowego jest generowany na podstawie inny aparat tego samego typu. Każdy aparat rozpoczyna się od różnych stopni początkowej zużycia i różnice unikatowy produkcyjnym. Te informacje są nieznane dla użytkownika. 

    - W tym symulowane danych aparat przyjęto, że działają normalnie na początku każdego szeregów czasowych. Rozpoczyna się zmniejszyć w pewnym momencie podczas serii cykli operacyjnych. Degradacji postępu i rozwoju wielkości. 

    - Po osiągnięciu progu wstępnie zdefiniowanych przez aparat są uważane za niebezpieczne dla dalszego działania. Ostatniego cyklu każdego szeregu czasowego jest punktem awarii tego aparatu.

- **Dane testowe**: powietrznego aparat dane operacyjne bez rejestrowane zdarzenia błędów. Dane testowe (PM_test.txt) ma ten sam schemat danych jako dane szkoleniowe. Jedyną różnicą jest to, że dane nie wskazuje po wystąpieniu błędu (jest ostatnim okresie *nie* reprezentuje punkt awarii). Nie wiadomo, ile więcej cykli ten aparat może trwać przed jej nie powiedzie się.

- **Dane prawdy**: informacje o pozostałych true cykli dla każdego aparatu danych testowych. Dane prawdy podstaw zawiera liczby cykli pracy pozostałej dla aparatów danych testowych.

## <a name="scenario-structure"></a>Scenariusz — struktura

Przepływ pracy scenariusza jest podzielone na trzy kroki, a każdy krok jest wykonywany w notesu Jupyter. Każdy notesu tworzy artefaktów danych, które są trwałe lokalnie do użycia w notesach.

### <a name="task-1-data-ingestion-and-preparation"></a>Zadanie 1: Wprowadzanie danych i przygotowanie

Notesu Jupyter wprowadzanie danych w Code/1_data_ingestion_and_preparation.ipnyb ładuje trzy zestawy danych wejściowych do formatu Pandas DataFrame. Następnie notesu przygotowuje dane do modelowania i jest niektórych wizualizację wstępnych danych. Zestawy danych są przechowywane lokalnie w kontekście obliczeń do użycia w notesu Jupyter tworzenia modelu.

### <a name="task-2-model-building-and-evaluation"></a>Zadanie 2: Konstruowania modelu i ocena

Notesu Jupyter tworzenia modelu w Code/2_model_building_and_evaluation.ipnyb odczytuje pociągu i testowania zestawów danych z dysku i tworzy sieć LSTM szkoleniowy zestaw danych. W przypadku testowego zestawu danych mierzona jest wydajność modelu. Modelu wynikowego są serializowane i przechowywane w kontekście lokalnym do użycia w zadaniu operationalization.

### <a name="task-3-operationalization"></a>Zadanie 3: Operationalization

Operationalization notesu Jupyter w Code/3_operationalization.ipnyb korzysta z przechowywanych modelu Tworzenie funkcji i schematu wywoływania modelu usługi hostowanej platformy Azure w sieci web. Notesu testów funkcje i następnie kompresuje zasoby do pliku LSTM_o16n.zip. Plik jest ładowany do Twojej kontenera magazynu systemu Azure dla wdrożenia.

Plik wdrożenia LSTM_o16n.zip zawiera następujące artefakty:

- **webservices_conda.yaml**: definiuje pakietów języka Python, które są wymagane do uruchomienia LSTM modelu w celu wdrożenia.  
- **service_schema.JSON**: definiuje schemat danych, który jest oczekiwany przez LSTM model.   
- **lstmscore.PY**: definiuje funkcje, których cel wdrożenia jest uruchomiona do oceny nowych danych.    
- **modellstm.JSON**: definiuje architekturę LSTM. Funkcje lstmscore.py odczytać architektury i wagi zainicjować modelu.
- **modellstm.H5**: definiuje wag na poziomie modelu.
- **test_service.PY**: skrypt testu, który wywołuje punkt końcowy wdrożenia z testem rekordów danych. 
- **PM_test_files.pkl**: skrypt test_service.py odczytuje dane historyczne aparatu z pliku PM_test_files.pkl i wysyła usługi sieci web wystarczającej liczby cykli LSTM do zwrócenia prawdopodobieństwo błędu aparatu.

Notesu testy funkcji za pomocą definicji modelu, przed jego pakietów zasobów operationalization dla wdrożenia. Instrukcje dotyczące konfigurowania i testowania usługi sieci web znajdują się na końcu Code/3_operationalization.ipnyb notesu.

## <a name="conclusion"></a>Podsumowanie

Ten samouczek zawiera Prosty scenariusz korzystającą z czujnika wartości w celu tworzenia prognoz. Bardziej zaawansowanych scenariuszy konserwacji predykcyjnej, takich jak [predykcyjnej konserwacji modelowania przewodnik R notesu](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) można używać wielu źródeł danych, takich jak rekordy historyczne konserwacji, dzienniki błędów i funkcje maszyny. Dodatkowych źródeł danych może wymagać różnych procesów do użycia z learning bezpośrednich.


## <a name="references"></a>Dokumentacja

Przykłady innych konserwacji predykcyjnej przypadki użycia dla różnych platform zapewniają następujące informacje:

* [Szablon rozwiązania konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Podręcznik modelowania konserwacji predykcyjnej](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Predykcyjnej konserwacji modelowania przewodnik przy użyciu usług R SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Konserwacji predykcyjnej notesu Python Podręcznik modelowania](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Konserwacji predykcyjnej przy użyciu PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Konserwacji predykcyjnej rzeczywistych scenariuszy](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>Kolejne kroki

Inne przykładowe scenariusze są dostępne w Machine Learning Workbench, który zademonstrować dodatkowe funkcje produktu. 
