---
title: Architektury i kluczowe pojęcia
titleSuffix: Azure Machine Learning service
description: Więcej informacji na temat architektury, warunki, pojęcia i przepływu pracy wchodzące w skład usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8bb06d04aec8e98308c0f5595b6b39e4b98302ff
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480055"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Jak działa usługa Azure Machine Learning: Architektura i pojęcia

Więcej informacji na temat architektury, pojęcia i przepływ pracy dla usługi Azure Machine Learning. Główne składniki usługi i ogólny przepływ pracy przy użyciu usługi przedstawiono na poniższym diagramie:

[![Architektura usługi Azure Machine Learning service i przepływ pracy](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>Przepływ pracy

Ogólnie maszyny uczenie się przepływu pracy przebiega w następującej kolejności:

1. Tworzenie usługi machine learning szkolenia skryptów w **Python**.
1. Tworzenie i konfigurowanie **obliczeniowego elementu docelowego**.
1. **Przesyłanie skryptów usługi** docelową obliczeń skonfigurowany do uruchamiania w środowisku. Podczas szkolenia, skrypty można odczytać lub zapisać do **datastore**. I rekordy wykonywania są zapisywane jako **uruchamia** w **obszaru roboczego** i zgrupowane w obszarze **eksperymentów**.
1. **Zapytanie eksperymentu** dla zarejestrowanych metryk z bieżącej i wcześniejszych przebiegów. Jeśli metryki nie wskazują żądanego wyniku, pętli, wróć do kroku 1 i powtarzanie czynności w skryptach.
1. Po znalezieniu zadowalające Uruchom rejestrowanie utrwalonej modelu w **rejestru modelu**.
1. Twórz oceniania skrypt, który używa modelu i **wdrożyć model** jako **usługi sieci web** na platformie Azure lub do **urządzenie usługi IoT Edge**.

Należy wykonać te czynności przy użyciu dowolnej z następujących czynności:
+ [Usługi Azure Machine Learning zestawu SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Usługi Azure Machine Learning interfejs wiersza polecenia](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Rozszerzenie usługi Azure Machine Learning programu VS Code](how-to-vscode-tools.md)
+  [Interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning](ui-concept-visual-interface.md)

> [!NOTE]
> Chociaż w tym artykule definiuje terminy i pojęcia używane przez usługę Azure Machine Learning, nie definiuje terminy i pojęcia związane z platformą Azure. Aby uzyskać więcej informacji na temat terminologii platformy Azure, zobacz [słownik Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Obszar roboczy

[Obszar roboczy](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi Azure Machine Learning. Zapewnia scentralizowanym miejscem do pracy z wszystkich artefaktów, które możesz utworzyć, korzystając z usługi Azure Machine Learning.

Taksonomia obszaru roboczego zostało zilustrowane na poniższym diagramie:

[![Taksonomia obszaru roboczego](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Aby uzyskać więcej informacji na temat obszarów roboczych, zobacz [co to jest obszar roboczy usługi Azure Machine Learning?](concept-workspace.md).

## <a name="experiment"></a>Eksperyment

Eksperyment to zbiór wielu uruchomień z określonego skryptu. Zawsze należy do obszaru roboczego. Po przesłaniu przebiegu można podać nazwę eksperymentu. Informacje dotyczące uruchomienia są przechowywane w ramach tego eksperymentu. Jeśli przesłać przebiegu i określ nazwę eksperymentu, który nie istnieje, automatycznie zostanie utworzony nowy eksperyment o tej nazwie nowo określonego.

Na przykład z użyciem eksperymentu zobacz [Szybki Start: Wprowadzenie do usługi Azure Machine Learning](quickstart-run-cloud-notebook.md).

## <a name="model"></a>Modelowanie

W najprostszym modelem jest fragmentem kodu, który przyjmuje dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego obejmuje algorytmu, wybierając dostarczanie danych i dostosowywanie hiperparametrów. Szkolenie jest procesem iteracyjnym, tworzącego trenowanego modelu, który hermetyzuje, jakie zakorzenione podczas procesu uczenia modelu.

Model jest generowany przez uruchomienie w usłudze Azure Machine Learning. Można również użyć modelu, który jest uczony poza usługi Azure Machine Learning. Możesz zarejestrować model, w obszarze roboczym usługi Azure Machine Learning.

Usługa Azure Machine Learning jest niezależny od framework. Podczas tworzenia modelu, używając dowolną platformę nauczania popularnych maszyny, jak Scikit-learn, XGBoost, PyTorch, TensorFlow, Chainer.

Aby uzyskać przykład uczenia modelu, zobacz [samouczka: uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a>Model rejestru

Rejestr modelu przechowuje informacje o wszystkich modeli w obszarze roboczym usługi Azure Machine Learning.

Modele są identyfikowane przez nazwę i wersję. Każdorazowo, należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru przyjęto założenie, że jest nowa wersja. Numer wersji jest zwiększany, a nowy model jest zarejestrowany w ramach tej samej nazwie.

Po zarejestrowaniu modelu, można zapewnić dodatkowe metadane tagów, a następnie użyć tagów, podczas wyszukiwania dla modeli.

Nie można usunąć modeli, które są używane przez aktywne wdrożenie.

Na przykład zarejestrować model, zobacz [Wytrenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run-configuration"></a>Uruchom konfigurację

Konfigurację uruchomieniową jest zestaw instrukcji, który definiuje, jak uruchomić skryptu w określonym obliczeniowego elementu docelowego. Konfiguracja obejmuje szerokiego zestawu definicji zachowanie, takie jak czy użyć istniejącego środowiska Python, czy używać środowiska Conda, który jest zbudowany z specyfikację.

Konfigurację uruchomieniową mogą zostać utrwalone w pliku w katalogu, który zawiera skrypt szkolenia lub może być skonstruowany jako obiekt w pamięci i użytego do przesłania przebiegu.

Na przykład konfiguracji uruchamiania, zobacz [wybierz, a następnie użyć obliczeniowego elementu docelowego na podstawie uczyć modele](how-to-set-up-training-targets.md).

## <a name="dataset"></a>Zestaw danych

Zestawy Azure Machine Learning danych (wersja zapoznawcza) ułatwiają dostęp i pracować z danymi. Zestawy danych zarządzania danymi w różnych scenariuszach, takich jak szkolenia modelu i potoku tworzenia. Przy użyciu zestawu SDK usługi Azure Machine Learning, można dostęp bazowy magazyn, zapoznaj się z i przygotowywania danych, Zarządzanie cyklem życia różnych definicjach zestawów danych i porównanie między zestawów danych używanych w szkolenia i w środowisku produkcyjnym.

Zestawy danych udostępnia metody do pracy z danymi w popularnych formatach, takich jak przy użyciu `from_delimited_files()` lub `to_pandas_dataframe()`.

Aby uzyskać więcej informacji, zobacz [tworzenie i rejestrowanie zestawów danych Learning maszyny Azure](how-to-create-register-datasets.md).

Przykład przy użyciu zestawów danych, zobacz [przykładowy notesów](https://aka.ms/dataset-tutorial).

## <a name="datastore"></a>Magazyn danych

Magazyn danych to Abstrakcja magazynu za pośrednictwem konta usługi Azure storage. Magazyn danych można użyć kontenera obiektów blob platformy Azure lub udziału plików platformy Azure jako magazynu zaplecza. Każdy obszar roboczy ma domyślny magazyn danych, dlatego możesz zarejestrować dodatkowe magazynów danych.

Użyj interfejsu API zestawu SDK języka Python lub interfejsu wiersza polecenia usługi Azure Machine Learning do przechowywania i pobierania plików z magazynu danych.

## <a name="compute-target"></a>Obliczeniowego elementu docelowego

Cel obliczenia jest zasobu obliczeniowego, używanej do uruchomienia skryptu w języku szkolenia lub hosta wdrożenia swojej usługi. Są obsługiwane obliczeniowych elementów docelowych:

| Obliczeniowego elementu docelowego | Szkolenia | Wdrożenie |
| ---- |:----:|:----:|
| Komputer lokalny | ✓ | &nbsp; |
| Obliczeniowe platformy Azure Machine Learning | ✓ | &nbsp; |
| Maszyny Wirtualnej z systemem Linux na platformie Azure</br>(na przykład maszyna wirtualna do nauki o danych) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Platforma Apache Spark dla HDInsight | ✓ | &nbsp; |
| Azure Container Instances | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Tablica programowalny bramy (FPGA) | &nbsp; | ✓ |

Obliczeniowych elementów docelowych są dołączone do obszaru roboczego. Obliczeniowe elementy docelowe, innym niż komputer lokalny są udostępniane przez użytkowników obszaru roboczego.

### <a name="managed-and-unmanaged-compute-targets"></a>Zarządzane i niezarządzane obliczeniowych elementów docelowych

* **Zarządzane**: Obliczeniowych elementów docelowych, które są tworzone i zarządzane przez usługę Azure Machine Learning. Te obliczenia obiekty docelowe są zoptymalizowane dla obciążeń learning maszyn. Obliczeniowy uczenie maszynowe systemu Azure to jedyny zarządzanych obliczeniowego elementu docelowego w dniu 4 grudnia 2018 r. Dodatkowe zasoby obliczeniowe zarządzane obiekty docelowe mogą być dodawane w przyszłości.

    Można utworzyć usługi machine learning obliczeń wystąpienia bezpośrednio za pomocą obszaru roboczego przy użyciu witryny Azure portal, Machine Learning zestawu SDK usługi Azure lub interfejsu wiersza polecenia platformy Azure. Wszystkie inne obliczeniowych elementów docelowych muszą być tworzone spoza obszaru roboczego i następnie jest podłączone do niego.

* **Niezarządzane**: Obliczeniowych elementów docelowych, które są *nie* zarządzane przez usługę Azure Machine Learning. Może być konieczne, utwórz je poza usługi Azure Machine Learning, a następnie dołącz je do swojego obszaru roboczego przed użyciem. Niezarządzane obliczeniowych elementów docelowych może wymagać dodatkowych kroków służących do obsługi lub w celu poprawy wydajności dla obciążeń learning maszyn.

Aby uzyskać informacji o wybieraniu obliczeniowego elementu docelowego dla szkolenia, zobacz [wybierz, a następnie użyć obliczeniowego elementu docelowego na podstawie uczyć modele](how-to-set-up-training-targets.md).

Aby uzyskać informacji o wybieraniu obliczeniowego elementu docelowego dla wdrożenia, zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="training-script"></a>Skrypt szkoleniowy

Do nauczenia modelu, można określić katalog, który zawiera skrypt szkolenia i skojarzone pliki. Należy również określić nazwę eksperymentu, który jest używany do przechowywania informacji, które są zbierane podczas szkolenia. Podczas szkolenia, cały katalog jest kopiowany do treningowe (obiekt docelowy obliczeniowe) i uruchomieniu skryptu, który określono za pomocą konfiguracji uruchamiania. Migawka katalogu znajduje się również w obszarze eksperymentu, w obszarze roboczym.

Przykład — patrz [Tutorial: uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run"></a>Uruchom polecenie

Przebieg jest rekord, który zawiera następujące informacje:

* Metadane dotyczące przebiegu (sygnatura czasowa, czas trwania itd.)
* Metryki, które są rejestrowane przez skrypt
* Pliki wyjściowe, które są autocollected w doświadczeniu lub jawnie przekazane przez użytkownika
* Migawka katalogu, który zawiera skrypty, przed przebiegu

Uruchom zostanie wyświetlony po przesłaniu skryptu w celu nauczenia modelu. Uruchom może mieć zero lub więcej uruchomień podrzędnych. Na przykład uruchom najwyższego poziomu może mieć dwa przebiegi podrzędnych, z których każdy może mieć własne podrzędne uruchamiania.

Na przykład wyświetlanie uruchomień, które są produkowane przez uczenia modelu, zobacz [Szybki Start: Wprowadzenie do usługi Azure Machine Learning](quickstart-run-cloud-notebook.md).

## <a name="github-tracking-and-integration"></a>Śledzenie usługi GitHub i integracja

Po rozpoczęciu szkolenia, gdzie katalog źródłowy jest w nim program lokalnego repozytorium Git, informacje o repozytorium znajduje się w historii uruchamiania. Na przykład identyfikator bieżącego zatwierdzenia w repozytorium jest rejestrowany jako część historii. Działa to z przebiegów przesłane za pomocą narzędzie do szacowania potoku uczenia Maszynowego i uruchomienia skryptu. Działa z zestawu SDK lub interfejsu wiersza polecenia Machine Learning przesłane uruchomień.

## <a name="snapshot"></a>Snapshot

Po przesłaniu uruchomienie usługi Azure Machine Learning kompresuje katalogu, który zawiera skrypt jako plik zip i wysyła je do obliczeniowego elementu docelowego. Następnie został wyodrębniony plik zip, a skrypt jest uruchamiany istnieje. Usługa Azure Machine Learning są także przechowywane w pliku zip jako migawka jako część rekordu uruchomienia. Każda osoba mająca dostęp do obszaru roboczego można przeglądać rekordu uruchomienia i pobrać migawki.

> [!NOTE]
> Aby uniknąć niepotrzebnych plików zostaną uwzględnione w migawce, należy utworzyć plik Ignoruj (.gitignore lub .amlignore). Umieść ten plik w katalogu migawki i Dodaj nazwy plików do ignorowania w nim. Plik .amlignore używa tych samych [składni i wzorce jako plik .gitignore](https://git-scm.com/docs/gitignore). Jeśli oba pliki istnieją, pierwszeństwo ma plik .amlignore.

## <a name="activity"></a>Działanie

Działanie reprezentuje długotrwałej operacji. Następujące operacje są przykłady działań:

* Tworzenie lub usuwanie obliczeniowego elementu docelowego
* Uruchomienie skryptu na obliczeniowego elementu docelowego

Działania można określić powiadomienia za pomocą zestawu SDK lub interfejsu użytkownika sieci web co mogą łatwo monitorować postęp tych operacji.

## <a name="image"></a>Image

Obrazy umożliwiają niezawodne wdrażanie modelu, oraz wszystkie składniki potrzebne do korzystania z modelu. Obraz zawiera następujące elementy:

* Model.
* Do oceniania skryptu lub aplikacji. Skrypt służy do przekazywania danych wejściowych do modelu i zwraca wynik w modelu.
* Zależności, które są wymagane przez model lub oceniania skryptu lub aplikacji. Na przykład mogą obejmować plik środowiska Conda, który zawiera listę zależności pakietu języka Python.

Usługa Azure Machine Learning można tworzyć dwa typy obrazów:

* **Obraz FPGA**: Używany podczas wdrażania na tablicę programowalny bramy na platformie Azure.
* **Obraz platformy docker**: Używany podczas wdrażania do celów innych niż FPGA obliczeń. Przykładami są usługi Azure Container Instances i Azure Kubernetes Service.

Usługa Azure Machine Learning zapewnia obraz podstawowy, który jest używany domyślnie. Możesz również podać własne niestandardowe obrazy.

Aby uzyskać przykład tworzenia obrazu, zobacz [wdrażanie modeli klasyfikacji obrazów w usłudze Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Rejestru obrazów

Obrazy, które są tworzone na podstawie modeli śledzi informacje o rejestru obrazów. Możesz podać dodatkowe metadane tagów po utworzeniu obrazu. Tagi metadanych są przechowywane w rejestrze obrazu i kwerendy można je znaleźć obrazu.

## <a name="deployment"></a>Wdrożenie

Wdrożenie jest egzemplarzem modelu do obu usługę internetową, która może być hostowana w chmurze lub moduł usługi IoT dla wdrożeń IDE.

### <a name="web-service"></a>Usługa sieci Web

Wdrożonej usługi sieci web można użyć usługi Azure Container Instances, Azure Kubernetes Service lub układów FPGA. Możesz utworzyć usługę z modelu, skrypt i skojarzone pliki. Te są hermetyzowane w obraz, który zapewnia środowisko czasu uruchamiania usługi sieci web. Obraz ma ze zrównoważonym obciążeniem końcowego HTTP, który odbiera oceniania żądań, które są wysyłane do usługi sieci web.

Platforma Azure ułatwia monitorowanie wdrożenia swojej usługi sieci web przez zbieranie danych telemetrycznych usługi Application Insights lub model danych telemetrycznych, jeśli wybrano włączyć tę funkcję. Dane telemetryczne są dostępne tylko dla Ciebie i jest przechowywany w usłudze Application Insights i wystąpień konta magazynu.

Po włączeniu automatycznego skalowania platformy Azure jest skalowana na wdrożenie.

Aby uzyskać przykład wdrażania modelu w postaci usługi sieci web, zobacz [wdrażanie modeli klasyfikacji obrazów w usłudze Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Moduł IoT

Wdrożone moduł IoT to kontener platformy Docker, który zawiera model i skojarzone skryptu lub aplikacji i wszelkie dodatkowe zależności. Te moduły można wdrożyć przy użyciu usługi Azure IoT Edge na urządzeniach brzegowych.

Po włączeniu monitorowania platformy Azure zbiera dane telemetryczne z modelu w module usługi Azure IoT Edge. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniu konta magazynu.

Usługa Azure IoT Edge zapewnia, że modułu i monitoruje urządzenia, który jest hostem go.

## <a name="pipeline"></a>Potok

Korzystanie z uczenia maszynowego na błędy potoków można tworzyć i zarządzać nimi przepływy pracy, które łączyć usługi machine learning faz. Na przykład potok może obejmować przygotowywania danych, szkolenie modelu, wdrożenie modelu i fazy wnioskowania/oceniania. Każda faza może obejmować wiele kroków, z których każdy może uruchamiane w różnych celów obliczeń.

Aby uzyskać więcej informacji na temat potoków uczenia maszynowego za pomocą tej usługi, zobacz [potoków i Azure Machine Learning](concept-ml-pipelines.md).

## <a name="logging"></a>Rejestrowanie

Podczas opracowywania rozwiązania do rejestrowania dowolnego metryki należy używać zestawu SDK języka Python usługi Azure Machine Learning w skrypcie języka Python. Po uruchomieniu zapytania metryki, aby określić, czy uruchomienie tworzył model który chcesz wdrożyć.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz:

* [Co to jest usługa Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md)
* [Samouczek: trenowanie modelu](tutorial-train-models-with-aml.md)
* [Tworzenie obszaru roboczego przy użyciu szablonu usługi Resource Manager](how-to-create-workspace-template.md)
