---
title: Konserwacja zapobiegawcza na potrzeby scenariuszy w rzeczywistych warunkach | Dokumentacja firmy Microsoft
description: Konserwacja zapobiegawcza na potrzeby scenariuszy w rzeczywistych warunkach przy użyciu PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9c638ed9132612db7b82168d3a57057aba9b2d60
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870338"
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Konserwacja zapobiegawcza na potrzeby scenariuszy w rzeczywistych warunkach

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Niezaplanowane przestoje sprzętu mogą mieć niekorzystny wpływ na każdą firmę. Warto zachować pola urządzenia uruchomione, aby zmaksymalizować wykorzystanie oraz wydajność oraz zminimalizować kosztownych i nieplanowanych przestojów. Wczesnej identyfikacji problemów może pomóc przydzielić zasoby ograniczone konserwacji w ekonomiczny sposób i poprawić jakość i procesy łańcucha dostaw. 

W tym scenariuszu przedstawiono stosunkowo [na dużą skalę symulowane zestawu danych](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) celu zaprezentowania konserwacji predykcyjnej projektu nauki o danych z pozyskiwanie danych, są wyposażone w inżynierii, konstruowania modelu i operacjonalizacji modeli i wdrożenie. Kod dla całego procesu są zapisywane w notesie Jupyter przy użyciu PySpark w aplikacji Azure Machine Learning Workbench. Końcowego modelu jest wdrażane za pomocą Zarządzanie modelami w usłudze Azure Machine Learning w celu prognozowania awarii urządzeń w czasie rzeczywistym.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub repozytorium

Cortana Intelligence Gallery samouczek PM jest publicznego repozytorium GitHub ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) gdzie zgłosić problemy i swojego wkładu.


## <a name="use-case-overview"></a>Omówienie przypadków użycia

Poważny problem, który stoi firmom w branżach Obciążenie zasobów to znaczne koszty, które są skojarzone z opóźnienia spowodowane przez problemy, mechanicznych. Większość firm są zainteresowani przewidywania, gdy te problemy mogą wystąpić, aby aktywnie im Zapobiegaj przed ich wystąpieniem. Celem jest, aby zmniejszyć koszty przez zmniejszenie przestojów i możliwego zwiększania bezpieczeństwa. 

W tym scenariuszu przyjmuje pomysły z [Podręcznik konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) aby zademonstrować sposób kompilowania modelu predykcyjnego dla symulowanego zestawu danych. Przykładowe dane, jest pochodną wspólne elementy, które pojawiają się w wielu przypadkach użycia konserwacji predykcyjnej.

Problem biznesowy symulowanych danych pozostaje możliwość przewidzenia problemów, które są spowodowane przez awarii składników sprzętowych. To pytanie biznesowe "*jakie jest prawdopodobieństwo, że maszyna ulegnie awarii z powodu błędu składnika?*" Ten problem jest formatowana jako problem klasyfikacji wieloklasowej (wiele składników dla poszczególnych komputerów). Algorytmu uczenia maszynowego służy do tworzenia modeli predykcyjnych. Model jest uczony w danych historycznych, które są zbierane z maszyn. W tym scenariuszu użytkownik przechodzi przez różnych kroków dotyczących implementacji modelu w środowisku Machine Learning Workbench.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Postępuj zgodnie z [Przewodnik instalacji szybkiego startu](quickstart-installation.md) zainstalować ten program i utworzyć obszar roboczy.
* Azure Machine Learning Operacjonalizacji wymaga środowiska lokalnego wdrażania oraz [konto Zarządzanie modelami w usłudze Azure Machine Learning](model-management-overview.md).

W tym przykładzie jest uruchamiany w dowolnym kontekście obliczeniowym Machine Learning Workbench. Jednak zaleca się uruchamiania przykładu z co najmniej 16 GB pamięci. Ten scenariusz był tworzone i testowane na komputerze systemu Windows 10 z systemem zdalnym standard DS4_V2 [Data Science Virtual Machine (dsvm dystrybucji) dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Operacjonalizacja modelu było to przy użyciu wersji 0.1.0a22 z interfejsu wiersza polecenia usługi Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwórz aplikację Machine Learning Workbench.
2.  Na **projektów** wybierz opcję **+**, a następnie wybierz pozycję **nowy projekt**.
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu.
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Konserwacja zapobiegawcza" i wybierz **konserwacji predykcyjnej** szablonu.
5.  Wybierz pozycję **Utwórz**.

## <a name="prepare-the-notebook-server-computation-target"></a>Przygotowywanie celu obliczeń serwer notesu

Aby uruchomić na komputerze lokalnym z Machine Learning Workbench **pliku** menu, wybierz opcję **Otwórz wiersz polecenia** lub **Otwórz interfejs wiersza polecenia PowerShell**. Interfejs wiersza polecenia umożliwia dostęp do usług platformy Azure przy użyciu `az` poleceń. Najpierw zaloguj się do konta platformy Azure przy użyciu polecenia:

```
az login
``` 

To polecenie dostarcza klucza uwierzytelniania za pomocą protokołu https:\\aka.ms\devicelogin adresu URL. Interfejs wiersza polecenia czeka, aż urządzenie operacji logowania zwraca i udostępnia pewne informacje o połączeniu. Dalej, jeśli masz lokalnego [Docker](https://www.docker.com/get-docker) instalacji przygotować środowisko obliczeniowe lokalnego przy użyciu polecenia:

```
az ml experiment prepare --target docker --run-configuration docker
```

Zaleca się uruchamiania na [nauki dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) dla wymagania dotyczące pamięci i dysku. Po skonfigurowaniu maszyny DSVM przygotować zdalnym środowisku platformy Docker przy użyciu dwóch następujących poleceń:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po nawiązaniu zdalnym kontenerze platformy Docker, należy przygotować środowisko obliczeniowe DSVM platformy Docker przy użyciu polecenia: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Przygotowanie środowiska obliczeniowego platformy Docker, Otwórz serwer notesu Jupyter z aplikacji Azure Machine Learning Workbench **notesów** tab lub uruchomić serwer oparty na przeglądarce, za pomocą polecenia: 

```
az ml notebook start
```

Notesy przykład są przechowywane w katalogu kodu. Notesów są konfigurowane do uruchamiania po kolei, począwszy od pierwszego notesu (Code\1_data_ingestion.ipynb). Po otwarciu poszczególnych Notes, zostanie wyświetlony monit wybierz jądrem obliczeniowym. Wybierz jądra szablonu _maszyny [Connection_Name] [nazwa_projektu] do wykonania na wcześniej skonfigurowane maszyny DSVM.

## <a name="data-description"></a>Opis danych

[Symulowane dane](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide) składa się z następujących [wartości pięciu rozdzielanych przecinkami (.csv) plików](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data):

* [Maszyny](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/machines.csv): funkcje wyróżniającego się na każdym komputerze, takie jak wiek i modelu.
* [Błędy](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/errors.csv): dziennik błędów zawiera błędy niepowodujące niezgodności, które są zgłaszane, gdy komputer jest nadal działa. Te błędy nie są uważane za błędy, chociaż może być predykcyjne zdarzenia błędu w przyszłości. Wartości daty i godziny dla błędów są zaokrąglane do najbliższego godzinę, ponieważ dane telemetrii są zbierane na podstawie stawki godzinowej.
* [Konserwacja](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/maint.csv): dziennik konserwacji zawiera rekordy zarówno planowanych i nieplanowanych konserwacji. Zaplanowana konserwacja odpowiada regularnej inspekcji składników. Niezaplanowaną konserwację mogą wynikać z awarii mechanicznych lub innych spadek wydajności. Wartości daty i godziny konserwacji są zaokrąglane do najbliższego godzinę, ponieważ dane telemetrii są zbierane na podstawie stawki godzinowej.
* [Dane telemetryczne](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/telemetry.csv): dane telemetryczne składa się z pomiarów serii czasu z wielu czujników w ramach poszczególnych maszyn. Dane są rejestrowane przez średniej wartości z czujników w poszczególnych przedziałach jedną godzinę.
* [Błędy](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/failures.csv): błędy odpowiadają zamiany składnika w dzienniku konserwacji. Każdy rekord zawiera identyfikator maszyny, typ składnika, a zastąpienie datę i godzinę. Te rekordy są używane do tworzenia etykiety, które podejmuje próbę przewidywania modelu uczenia maszynowego.

Aby pobrać zestawy danych pierwotnych z repozytorium GitHub i Utwórz zestawy danych PySpark na potrzeby tej analizy, zobacz [pozyskiwanie danych](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) scenariusza notesu Jupyter w katalogu z kodem.

## <a name="scenario-structure"></a>Struktura scenariusza
Zawartość dla tego scenariusza znajduje się w temacie [repozytorium GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

[Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) plik zawiera opis przepływu pracy od przygotowywanie danych, kompilowania modelu i następnie wdrażania rozwiązania w środowisku produkcyjnym. Każdy krok przepływu pracy są hermetyzowane w notesie Jupyter w [kodu](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) folder w repozytorium.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): ten notes pobiera pięć plików wejściowych CSV i niektóre wstępne oczyszczania i wizualizacji. Notes konwertuje każdy zestaw danych do formatu PySpark i zapisuje go w kontenerze obiektów blob platformy Azure do użytku w notesie Inżynieria funkcji.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): funkcje modelu są konstruowane na podstawie zestawu danych pierwotnych z magazynu obiektów Blob platformy Azure przy użyciu podejścia serii (czas standardowy) dla danych telemetrii, błędy i konserwacji. Wymiana składników powiązane awarii są używane do konstruowania etykiety modelu, które opisują, który składnik nie powiodło się. Dane funkcji etykietą jest zapisywany w obiektu blob platformy Azure w modelu tworzenie notesu.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): Model tworzenie notesu korzysta z zestawu danych i funkcji oznaczonych i dzieli je na szkolenie i deweloperów zestawów danych, wzdłuż sygnaturę daty i godziny. Notes jest konfigurowany jako eksperymentów z modelami pyspark.ml.classification. Dane szkoleniowe jest zwektoryzowana. Użytkownika można eksperymentować z oboma **DecisionTreeClassifier** lub **RandomForestClassifier** do manipulowania hiperparametrów można znaleźć najlepiej działający model. Wydajność jest określany przez obliczenia statystyk miar dev zestawów danych. Te statystyki są rejestrowane w Machine Learning Workbench ekranu środowiska uruchomieniowego w celu śledzenia. Przy każdym uruchomieniu Notes zapisuje modelu wynikowego na dysk lokalny, który jest uruchomiony jądra notesu programu Jupyter. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): ten notes używa ostatniego modelu, który jest zapisywany na dysku lokalnym (jądra programu Jupyter notebook) do tworzenia składników do wdrażania modelu w usłudze sieci web platformy Azure. Pełne operacyjnej zasoby są skompaktować do pliku o16n.zip, który jest przechowywany w innym kontenerze obiektów blob platformy Azure. Plik zip zawiera:

* **service_schema.JSON**: plik definicji schematu dla wdrożenia. 
* **pdmscore.PY**: **init()** i **run()** funkcje, które są wymagane przez platformę Azure, usługi sieci web.
* **pdmrfull.model**: katalog definicję modelu.
    
Notes testów funkcji z definicji modelu przed spakowaniem zasoby operacjonalizacji oprogramowania dla wdrożenia. Instrukcje dotyczące wdrażania są umieszczane na końcu notesu.

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu przedstawiono omówienie tworzenia rozwiązania do konserwacji zapobiegawczej end-to-end przy użyciu PySpark w środowisku notesu Jupyter w aplikacji Machine Learning Workbench. Ten przykładowy scenariusz przedstawiono również modelu wdrażania za pośrednictwem środowiska zarządzania modelu usługi Machine Learning w celu prognozowania awarii urządzeń w czasie rzeczywistym.

## <a name="references"></a>Dokumentacja

Następujące odwołania zawierają przykłady innych konserwacji predykcyjnej przypadki użycia dla różnych platform:

* [Szablon rozwiązania konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Podręcznik modelowania konserwacji predykcyjnej](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Predykcyjne obsługi modelowania przewodnik przy użyciu usługi języka R programu SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Konserwacja zapobiegawcza modelowania Notes Python przewodnik](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Konserwacja zapobiegawcza za pomocą PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Uczenie głębokie dla konserwacji predykcyjnej](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Kolejne kroki

Inne przykładowe scenariusze są dostępne w aplikacji Machine Learning Workbench zademonstrować dodatkowe funkcje produktu. 
