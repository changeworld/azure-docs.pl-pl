---
title: Uczeniem głębokim na potrzeby scenariuszy w rzeczywistych warunkach konserwacji predykcyjnej — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak replikować samouczek dotyczący uczenia głębokiego, pod kątem konserwacji predykcyjnej przy użyciu usługi Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 5d52433a32d8dc764c7535dacf5872e55f0082ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995520"
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Uczenie głębokie dla konserwacji predykcyjnej scenariuszy w rzeczywistych warunkach

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Uczenie głębokie jest jednym z najbardziej popularnych trendy w usłudze machine learning i aplikacjami do wielu obszarów, takich jak:
- Samochody korzystania i związane z robotyką.
- Rozpoznawanie mowy i obrazu.
- Prognozowanie finansowego.

Znane również jako głębokich sieciach neuronowych (DNN), te metody są zainspirować poszczególne grupy neuronów, które znajdują się w mózg (biologicznych sieci neuronowych).

Niezaplanowane przestoje sprzętu mogą mieć niekorzystny wpływ na każdą firmę. Warto zachować sprzętu pola do zmaksymalizowania użycia i wydajności i minimalizowanie kosztownych i nieplanowanych przestojów. Wczesnej identyfikacji problemów może pomóc przydzielić zasoby ograniczone konserwacji w ekonomiczny sposób i poprawić jakość i procesy łańcucha dostaw. 

Strategia konserwacji zapobiegawczej (PM) używa machine learning metody, aby określić warunek sprzętu prewencyjnego wykonywanie konserwacji, aby uniknąć wydajność niekorzystny maszyny. W PM dane są zbierane wraz z upływem czasu, aby monitorować stan maszyny, a następnie analizowane w celu znalezienia wzorców przewiduje awarie. [Długie krótki okres pamięci (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) sieci są atrakcyjną w przypadku tego ustawienia, ponieważ służą one do ucz się od sekwencji danych.

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub repozytorium

Cortana Intelligence Gallery samouczek PM jest publicznego repozytorium GitHub ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) gdzie zgłosić problemy i swojego wkładu.


## <a name="use-case-overview"></a>Omówienie przypadków użycia

W tym samouczku używa tego przykładu zdarzenia niepowodzeniem przebiegu silnika samolotu symulowane, aby zademonstrować konserwacji predykcyjnej procesu modelowania. Scenariusz jest opisany w [konserwacji predykcyjnej](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Główne założeń, w tym ustawieniu jest aparat stopniowo negatywny wpływ na dostępność przez cały okres ich istnienia. Obniżenia wydajności może zostać wykryte w pomiarów czujników silnika. PM próbuje modelowania relacji między zmiany w tych wartości z czujników i historycznych błędów. Model następnie potrafimy przewidzieć, że aparatu i kiedy może zakończyć się niepowodzeniem w przyszłości na podstawie bieżącego stanu pomiarów czujnika.

Ten scenariusz tworzy sieci LSTM w celu przewidywania pozostałego okresu eksploatacji (RUL) silników samolotów przy użyciu wartości z czujników historycznych. W scenariuszu [Keras](https://keras.io/) biblioteki z [Tensorflow](https://www.tensorflow.org/) platforma do uczenia głębokiego jako aparatu obliczeniowego. Scenariusz szkolenie modeli LSTM z jednego zestawu aparatów i testów w zestawie niewidzianych aparatu sieci.

## <a name="prerequisites"></a>Wymagania wstępne
- [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
- Usługa Azure Machine Learning Workbench, za pomocą obszaru roboczego tworzone.
- Model operacjonalizacji: platformy Azure Machine Learning Operacjonalizacji ze środowiska lokalnego wdrażania, konfigurowania i [konto Zarządzanie modelami w usłudze Azure Machine Learning](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:

1. Otwórz aplikację Machine Learning Workbench.
2. Na **projektów** wybierz opcję **+**, a następnie wybierz pozycję **nowy projekt**.
3. W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu.
4. W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Konserwacja zapobiegawcza" i wybierz **uczeniem głębokim na potrzeby scenariuszu konserwacji predykcyjnej** szablonu.
5. Wybierz pozycję **Utwórz**.

## <a name="prepare-the-notebook-server-computation-target"></a>Przygotowywanie celu obliczeń serwer notesu

Aby uruchomić na komputerze lokalnym z Machine Learning Workbench **pliku** menu, wybierz opcję **Otwórz wiersz polecenia** lub **Otwórz interfejs wiersza polecenia PowerShell**. Interfejs wiersza polecenia umożliwia dostęp do usług platformy Azure przy użyciu `az` poleceń. Najpierw zaloguj się do konta platformy Azure przy użyciu polecenia:

```
az login
``` 

To polecenie dostarcza klucz uwierzytelniania, który będzie używany przy użyciu protokołu https:\\aka.ms\devicelogin adresu URL. Interfejs wiersza polecenia czeka, aż urządzenie operacji logowania zwraca i udostępnia pewne informacje o połączeniu. Dalej, jeśli masz lokalnego [Docker](https://www.docker.com/get-docker) instalacji przygotować środowisko obliczeniowe lokalnego przy użyciu polecenia:

```
az ml experiment prepare --target docker --run-configuration docker
```

Zaleca się uruchamiania na [Data Science Virtual Machine (dsvm dystrybucji) dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) dla wymagania dotyczące pamięci i dysku. Po skonfigurowaniu maszyny DSVM przygotować zdalnym środowisku platformy Docker przy użyciu dwóch następujących poleceń:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po nawiązaniu zdalnym kontenerze platformy Docker, należy przygotować środowisko obliczeniowe DSVM platformy Docker przy użyciu polecenia: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Przygotowanie środowiska obliczeniowego platformy Docker, Otwórz serwer notesu Jupyter z Machine Learning Workbench **notesów** tab lub uruchomić serwer oparty na przeglądarce, za pomocą polecenia: 

```
az ml notebook start
```

Notesy przykład są przechowywane w katalogu kodu. Notesów są konfigurowane do uruchamiania po kolei, począwszy od pierwszego notesu (Code\1_data_ingestion.ipynb). Po otwarciu poszczególnych Notes, zostanie wyświetlony monit wybierz jądrem obliczeniowym. Wybierz jądra szablonu _maszyny [Connection_Name] [nazwa_projektu] do wykonania na wcześniej skonfigurowane maszyny DSVM.

## <a name="data-description"></a>Opis danych

Szablon używa trzech zestawów danych jako dane wejściowe w plikach PM_train.txt PM_test.txt i PM_truth.txt. 

- **Szkolenie danych**: samolotu aparatu danych niepowodzeniem przebiegu. Dane train (PM_train.txt) składa się z wieloma szeregów czasowych wielu zmiennych z *cyklu* jako jednostki czasu. Obejmuje ona 21 odczyty czujników, dla każdego cyklu. 

    - Każdy szereg czasowy jest generowany na podstawie inny aparat tego samego typu. Każdy silnik rozpoczyna się od różne stopnie początkowej zużycia i różnice w produkcji unikatowy. Ta informacja jest nieznany dla użytkownika. 

    - W tym symulowane dane aparat jest zakłada się, że normalnego działania na początku każdego szeregów czasowych. Uruchamia się zmniejszyć w pewnym momencie podczas serii cykli operacyjnych. Obniżenia wydajności w miarę i rozwoju w wielkości. 

    - Po osiągnięciu wstępnie zdefiniowany próg aparat są uważane za niebezpieczne dalszych operacji. Każdy szereg czasowy ostatniego cyklu jest punktem awarii silnika.

- **Dane testowe**: samolotu aparatu dane operacyjne bez zdarzenia błędów rejestrowane. Dane testu (PM_test.txt) mają ten sam schemat danych jako dane szkoleniowe. Jedyną różnicą jest to, że dane nie wskazuje, kiedy wystąpi błąd (jest w ostatnim okresie *nie* reprezentuje punkt awarii). Nie wiadomo, jak wiele więcej cykli może trwać ten aparat, zanim zakończy się niepowodzeniem.

- **Dane w rzeczywistości**: informacje o pozostałych true cykli każdy silnik w danych testowych. Podstaw danych prawdziwych danych zawiera liczbę pozostałych cykle pracy silników danych testowych.

## <a name="scenario-structure"></a>Struktura scenariusza

Przepływ pracy scenariusza jest podzielone na trzy kroki, a każdy krok jest wykonywany w notesie Jupyter. Każdy notesu tworzy artefaktów danych, które są zachowywane lokalnie do użycia w notesach.

### <a name="task-1-data-ingestion-and-preparation"></a>Zadanie 1: Wprowadzanie i przygotowywanie danych

Notes Jupyter pozyskiwania danych w Code/1_data_ingestion_and_preparation.ipnyb ładuje trzech danych wejściowych zestawów danych do formatu Pandas DataFrame. Notes następnie przygotowuje dane do modelowania i jest kilka wizualizacji danych wstępnego. Zestawy danych są przechowywane lokalnie do kontekstu obliczeniowego do użytku w notesie Jupyter tworzenia modelu.

### <a name="task-2-model-building-and-evaluation"></a>Zadanie 2: Tworzenie modelu i ocena

Notes Jupyter Tworzenie modelu w Code/2_model_building_and_evaluation.ipnyb odczytuje szkolenie i testowanie zestawów danych z dysku i kompilacje sieci LSTM dla zestawu danych szkoleniowych. Wydajności modelu jest mierzony w zestawie danych testowych. Modelu wynikowego jest serializowany i przechowywane w lokalnym kontekście obliczeniowym do użycia w ramach zadania operacjonalizacji.

### <a name="task-3-operationalization"></a>Zadanie 3: Operacjonalizacja

Notes Jupyter Operacjonalizacji w Code/3_operationalization.ipnyb używa modelu przechowywanych do tworzenia funkcji i schematu do wywoływania modelu w usłudze internetowej hostowanej na platformie Azure. Notes testuje funkcji i do pliku LSTM_o16n.zip kompresuje zasoby. Plik jest ładowany do kontenera usługi Azure storage dla wdrożenia.

Plik wdrożenia LSTM_o16n.zip zawiera następujących artefaktów:

- **webservices_conda.yaml**: definiuje pakiety języka Python, które są wymagane do uruchomienia modelu LSTM w celu wdrożenia.  
- **service_schema.JSON**: definiuje schemat danych, który jest oczekiwany przez LSTM model.   
- **lstmscore.PY**: Określa funkcje, których cel wdrożenia jest uruchomiona ocena nowych danych.    
- **modellstm.JSON**: definiuje architekturę LSTM. Funkcje lstmscore.py odczytu architektury i wagi zainicjować modelu.
- **modellstm.H5**: definiuje wagi modelu.
- **test_service.PY**: skrypt testu, który wywołuje punkt końcowy wdrożenia z testem rekordów danych. 
- **PM_test_files.pkl**: skrypt test_service.py odczytuje dane historyczne z pliku PM_test_files.pkl i wysyła wystarczającej liczby cykli LSTM do zwrócenia prawdopodobieństwo awarii aparatu usługi sieci web.

Notes testów funkcji przy użyciu definicji modelu przed jego pakietów zasobów operacjonalizacji oprogramowania dla wdrożenia. Instrukcje dotyczące konfigurowania i testowania usługi sieci web są umieszczane na końcu notesu Code/3_operationalization.ipnyb.

## <a name="conclusion"></a>Podsumowanie

Ten samouczek zawiera Prosty scenariusz, który korzysta z wartości z czujników w celu prognozowania. Bardziej zaawansowanych scenariuszy konserwacji predykcyjnej, takich jak [predykcyjne obsługi modelowania przewodnik R notesu](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) można używać wielu źródeł danych, takich jak rekordy historyczne konserwacji, dzienniki błędów i funkcje maszyny. Dodatkowe źródła danych może wymagać różnych procesów za pomocą uczenia głębokiego.


## <a name="references"></a>Dokumentacja

Następujące odwołania zawierają przykłady innych konserwacji predykcyjnej przypadki użycia dla różnych platform:

* [Szablon rozwiązania konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Podręcznik modelowania konserwacji predykcyjnej](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Predykcyjne obsługi modelowania przewodnik przy użyciu usługi języka R programu SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Konserwacja zapobiegawcza modelowania Notes Python przewodnik](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Konserwacja zapobiegawcza za pomocą PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Konserwacja zapobiegawcza scenariuszy w rzeczywistych warunkach](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>Kolejne kroki

Inne przykładowe scenariusze są dostępne w aplikacji Machine Learning Workbench zademonstrować dodatkowe funkcje produktu. 
