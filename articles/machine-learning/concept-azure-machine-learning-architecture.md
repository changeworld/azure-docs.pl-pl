---
title: Architektura & kluczowe pojęcia
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o architekturze, terminach, pojęciach i przepływach pracy, które składają się na usługę Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 9f1d23f11cf73680a8861c9f1ac6cbd40ad497a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257338"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak działa usługa Azure Machine Learning: architektura i koncepcje

Dowiedz się więcej o architekturze, koncepcjach i przepływie pracy dla usługi Azure Machine Learning. Główne składniki usługi i ogólny przepływ pracy dla korzystania z usługi są pokazane na poniższym diagramie:

![Architektura i przepływ pracy usługi Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Przepływ pracy

Przepływ pracy modelu uczenia maszynowego zazwyczaj następuje następująca sekwencja:

1. **Szkolenie**
    + Tworzenie skryptów szkoleniowych uczenia maszynowego w **pythonie**, **R**lub z projektantem wizualnym.
    + Tworzenie i **konfigurowanie celu obliczeniowego**.
    + **Prześlij skrypty** do skonfigurowanego obiektu docelowego obliczeń, aby działały w tym środowisku. Podczas szkolenia skrypty mogą odczytywać z magazynów danych lub zapisywać je w **magazynach danych.** Dzienniki i dane wyjściowe wyprodukowane podczas szkolenia są zapisywane jako **przebiegi** w **obszarze roboczym** i grupowane w ramach **eksperymentów.**

1. **Pakiet** — po znalezieniu zadowalającego uruchomienia zarejestruj utrwalony model w **rejestrze modelu**.

1. **Sprawdź poprawność****kwerendy eksperymentu** dla zarejestrowanych metryk z bieżących i przeszłych przebiegów. -  Jeśli metryki nie wskazują pożądanego rezultatu, wróć do kroku 1 i iteruj w skryptach.

1. **Wdrażanie** — opracowanie skryptu oceniającego, który używa modelu i **wdrażanie modelu** jako **usługi sieci web** na platformie Azure lub na urządzeniu usługi **IoT Edge.**

1. **Monitor** — **monitorowanie dryfu danych** między zestawem danych szkolenia i danymi wnioskowania wdrożonego modelu. W razie potrzeby należy ponownie przejść do kroku 1, aby przeszkolić model z nowymi danymi treningowymi.

## <a name="tools-for-azure-machine-learning"></a>Narzędzia do uczenia maszynowego platformy Azure

Użyj tych narzędzi dla usługi Azure Machine Learning:

+  Interakcja z usługą w dowolnym środowisku języka Python za pomocą [zestawu SDK usługi Azure Machine Learning dla języka Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Interakcja z usługą w dowolnym środowisku języka R za pomocą [sdk usługi Azure Machine Learning dla języka R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Zautomatyzuj swoje działania związane z uczeniem maszynowym za pomocą [interfejsu wiersza polecenia usługi Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ Użyj [projektanta usługi Azure Machine Learning (wersja zapoznawcza),](concept-designer.md) aby wykonać kroki przepływu pracy bez pisania kodu.


> [!NOTE]
> Mimo że w tym artykule zdefiniowano terminy i pojęcia używane przez usługę Azure Machine Learning, nie definiuje terminów i pojęć platformy Azure. Aby uzyskać więcej informacji na temat terminologii platformy Azure, zobacz [słowniczek platformy Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Słownik

* [Działanie](#activities)
* [Workspace](#workspaces)
    * [Eksperymenty](#experiments)
        * [Run](#runs) 
            * [Uruchom konfigurację](#run-configurations)
            * [Migawka](#snapshots)
            * [Śledzenie gitów](#github-tracking-and-integration)
            * [Rejestrowanie](#logging)
    * [Potoki uczenia maszynowego](#ml-pipelines)
    * [Modele](#models)
        * [Środowiska](#environments)
        * [Skrypt szkoleniowy](#training-scripts)
        * [Estymatorów](#estimators)
    * [Punkty końcowe](#endpoints)
        * [Usługa sieci Web](#web-service-endpoint)
        * [Moduły IoT](#iot-module-endpoints)
    * [Zestaw danych & magazyny danych](#datasets-and-datastores)
    * [Docelowe obiekty obliczeniowe](#compute-targets)

### <a name="activities"></a>Działania

Działanie reprezentuje długotrwałą operację. Następujące operacje są przykładami działań:

* Tworzenie lub usuwanie obiektu docelowego obliczeń
* Uruchamianie skryptu w docelowych obliczeniach

Działania mogą dostarczać powiadomienia za pośrednictwem sdk lub interfejsu użytkownika sieci web, dzięki czemu można łatwo monitorować postęp tych operacji.

### <a name="workspaces"></a>Obszary robocze

[Obszar roboczy](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi Azure Machine Learning. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które można utworzyć podczas korzystania z usługi Azure Machine Learning. Obszar roboczy można udostępniać innym osobom. Aby uzyskać szczegółowy opis obszarów roboczych, zobacz [Co to jest obszar roboczy usługi Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Eksperymenty

Eksperyment jest grupowanie wielu przebiegów z określonego skryptu. Zawsze należy do obszaru roboczego. Podczas przesyłania przebiegu podajesz nazwę eksperymentu. Informacje dotyczące przebiegu są przechowywane w ramach tego eksperymentu. Jeśli prześlesz przebieg i określisz nazwę eksperymentu, która nie istnieje, zostanie automatycznie utworzony nowy eksperyment z tą nowo określoną nazwą.

Na przykład użycia eksperymentu zobacz [Samouczek: Trenuj swój pierwszy model](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Uruchamianie

Przebieg jest pojedynczym wykonaniem skryptu szkoleniowego. Eksperyment zazwyczaj będzie zawierać wiele przebiegów.

Usługa Azure Machine Learning rejestruje wszystkie przebiegi i przechowuje następujące informacje w eksperymencie:

* Metadane dotyczące przebiegu (sygnatura czasowa, czas trwania itd.)
* Metryki zarejestrowane przez skrypt
* Pliki wyjściowe, które są automatycznie czesane przez eksperyment lub jawnie przesłane przez Ciebie
* Migawka katalogu zawierającego skrypty przed uruchomieniem

Uruchom podczas przesyłania skryptu do szkolenia modelu. Przebieg może mieć zero lub więcej uruchomień podrzędnych. Na przykład przebieg najwyższego poziomu może mieć dwa podrzędne przebiegi, z których każdy może mieć własny przebieg podrzędny.

### <a name="run-configurations"></a>Uruchamianie konfiguracji

Konfiguracja uruchamiania to zestaw instrukcji, który definiuje sposób uruchamiania skryptu w określonym miejscu docelowym obliczeń. Konfiguracja zawiera szeroki zestaw definicji zachowań, takich jak czy używać istniejącego środowiska Python lub używać środowiska Conda, który jest zbudowany na podstawie specyfikacji.

Konfiguracja uruchamiania może być utrwalona w pliku wewnątrz katalogu, który zawiera skrypt szkolenia lub może być skonstruowany jako obiekt w pamięci i używany do przesyłania uruchomienia.

Na przykład uruchom konfiguracje, zobacz [Wybieranie i używanie celu obliczeniowego do trenowania modelu](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Migawki

Po przesłaniu uruchomienia usługa Azure Machine Learning kompresuje katalog zawierający skrypt jako plik zip i wysyła go do obiektu docelowego obliczeń. Plik zip jest następnie wyodrębniany, a skrypt jest tam uruchamiany. Usługa Azure Machine Learning przechowuje również plik zip jako migawkę jako część rekordu przebiegu. Każdy, kto ma dostęp do obszaru roboczego, może przeglądać rekord biegu i pobierać migawkę.

> [!NOTE]
> Aby zapobiec uwzględnieniu niepotrzebnych plików w migawce, należy wykonać plik ignorowania (.gitignore lub .amlignore). Umieść ten plik w katalogu Migawka i dodaj nazwy plików, które mają być w nim ignorowane. Plik .amlignore używa tej samej [składni i wzorców co plik gitignore](https://git-scm.com/docs/gitignore). Jeśli oba pliki istnieją, ma pierwszeństwo plik .amlignore.

### <a name="github-tracking-and-integration"></a>Śledzenie i integracja GitHub

Po uruchomieniu przebiegu szkolenia, w którym katalog źródłowy jest lokalnym repozytorium Git, informacje o repozytorium są przechowywane w historii uruchamiania. Działa to z uruchomieniami przesłane przy użyciu estymatora, potoku ML lub uruchomienia skryptu. Działa również dla uruchomień przesłanych z SDK lub machine learning CLI.

Aby uzyskać więcej informacji, zobacz [Integracja git dla usługi Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Rejestrowanie

Podczas opracowywania rozwiązania użyj zestawu SDK języka Python usługi Azure Machine Learning w skrypcie języka Python, aby rejestrować dowolne metryki. Po uruchomieniu kwerendy metryki, aby ustalić, czy uruchomienie wyprodukował model, który chcesz wdrożyć.

### <a name="ml-pipelines"></a>Rurociągi ML

Potoki uczenia maszynowego są używane do tworzenia przepływów pracy i zarządzania nimi, które łączą fazy uczenia maszynowego. Na przykład potok może obejmować przygotowanie danych, szkolenie modelu, wdrażanie modelu i fazy wnioskowania/oceniania. Każda faza może obejmować wiele kroków, z których każdy może działać bez nadzoru w różnych obiektów docelowych obliczeń. 

Kroki potoku są wielokrotnego użycia i można uruchomić bez ponownego uruchamiania poprzednich kroków, jeśli dane wyjściowe tych kroków nie uległy zmianie. Na przykład można przeszkolić model bez ponownego uruchamiania kosztownych kroków przygotowania danych, jeśli dane nie uległy zmianie. Potoki umożliwiają również analitykom danych współpracę podczas pracy nad oddzielnymi obszarami przepływu pracy uczenia maszynowego.

Aby uzyskać więcej informacji na temat potoków uczenia maszynowego za pomocą tej usługi, zobacz [Potoki i uczenie maszynowe platformy Azure.](concept-ml-pipelines.md)

### <a name="models"></a>Modele

W najprostszym modelu jest fragment kodu, który pobiera dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego polega na wybraniu algorytmu, udostępnieniu go z danymi i dostrajaniu hiperparametrów. Szkolenie jest iteracyjny proces, który tworzy wyszkolony model, który hermetyzuje, co model dowiedział się podczas procesu szkolenia.

Model jest produkowany przez uruchomienie w usłudze Azure Machine Learning. Można również użyć modelu, który jest przeszkolony poza usługi Azure Machine Learning. Model można zarejestrować w obszarze roboczym usługi Azure Machine Learning.

Usługa Azure Machine Learning jest niezależna od struktury. Podczas tworzenia modelu można użyć dowolnej popularnej struktury uczenia maszynowego, takiej jak Scikit-learn, XGBoost, PyTorch, TensorFlow i Chainer.

Na przykład szkolenia modelu przy użyciu Scikit-learn i estymatora, zobacz [Samouczek: Szkolenie modelu klasyfikacji obrazu z usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

**Rejestr modelu** śledzi wszystkie modele w obszarze roboczym usługi Azure Machine Learning.

Modele są identyfikowane według nazwy i wersji. Za każdym razem, gdy rejestrujesz model o takiej samej nazwie jak istniejący, rejestr zakłada, że jest to nowa wersja. Wersja jest zwiększana, a nowy model jest zarejestrowany pod tą samą nazwą.

Podczas rejestrowania modelu można podać dodatkowe tagi metadanych, a następnie użyć tagów podczas wyszukiwania modeli.

> [!TIP]
> Zarejestrowany model jest kontenerem logicznym dla jednego lub więcej plików, które tworzą model. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, można zarejestrować je jako pojedynczy model w obszarze roboczym usługi Azure Machine Learning. Po rejestracji można pobrać lub wdrożyć zarejestrowany model i otrzymać wszystkie zarejestrowane pliki.

Nie można usunąć zarejestrowanego modelu, który jest używany przez aktywne wdrożenie.

Na przykład rejestrowania modelu zobacz [Szkolenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning.](tutorial-train-models-with-aml.md)

### <a name="environments"></a>Środowiska

Środowiska usługi Azure ML są używane do określania konfiguracji (Docker / Python / Spark / etc.) używane do tworzenia powtarzalnego środowiska do przygotowywania danych, szkolenia modelu i obsługi modelu. Są one zarządzane i wersjonowane jednostki w obszarze roboczym usługi Azure Machine Learning, które umożliwiają odtwarzalne, inspekcji i przenośne przepływy pracy uczenia maszynowego w różnych obiektach docelowych obliczeń.

Obiekt środowiska na komputerze lokalnym można użyć do opracowania skryptu szkoleniowego, ponownego użycia tego samego środowiska w usłudze Azure Machine Learning Compute do szkolenia modelu na dużą skalę, a nawet wdrożenia modelu w tym samym środowisku. 

Dowiedz [się, jak tworzyć środowisko ml wielokrotnego użytku i zarządzać nim](how-to-use-environments.md) do celów szkoleniowych i wnioskowania.

### <a name="training-scripts"></a>Skrypty szkoleniowe

Aby wyszkolić model, należy określić katalog zawierający skrypt szkoleniowy i skojarzone pliki. Należy również określić nazwę eksperymentu, która jest używana do przechowywania informacji, które są zbierane podczas szkolenia. Podczas szkolenia cały katalog jest kopiowany do środowiska szkoleniowego (obiekt docelowy obliczeń) i uruchamiany jest skrypt określony przez konfigurację uruchamiania. Migawka katalogu jest również przechowywana w ramach eksperymentu w obszarze roboczym.

Na przykład zobacz [Samouczek: Trenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Estymatorów

Aby ułatwić szkolenie modelu z popularnych struktur, estymator klasy pozwala łatwo konstruować konfiguracje uruchamiania. Można utworzyć i używać ogólnego [estymatora](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptów szkoleniowych, które używają dowolnej struktury uczenia się wybierzesz (takich jak scikit-learn).

W przypadku zadań PyTorch, TensorFlow i Chainer usługa Azure Machine Learning udostępnia również odpowiednie estymatory [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) aby uprościć korzystanie z tych struktur.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Trenuj modele ML z estymatorami](how-to-train-ml-models.md).
* [Trenuj modele uczenia głębokiego Pytorch na dużą skalę dzięki usłudze Azure Machine Learning.](how-to-train-pytorch.md)
* [Trenuj i rejestruj modele TensorFlow na dużą skalę za pomocą usługi Azure Machine Learning.](how-to-train-tensorflow.md)
* [Trenuj i rejestruj modele Chainer na dużą skalę dzięki usłudze Azure Machine Learning.](how-to-train-ml-models.md)

### <a name="endpoints"></a>Punkty końcowe

Punkt końcowy to wystąpienie modelu do usługi sieci web, która może być hostowana w chmurze lub moduł IoT dla zintegrowanych wdrożeń urządzeń.

#### <a name="web-service-endpoint"></a>Punkt końcowy usługi sieci Web

Podczas wdrażania modelu jako usługi sieci web punkt końcowy można wdrożyć w wystąpieniach kontenera platformy Azure, usłudze Azure Kubernetes lub układach FPGA. Usługę można utworzyć na podstawie modelu, skryptu i skojarzonych plików. Są one umieszczane w obrazie kontenera podstawowego, który zawiera środowisko wykonywania dla modelu. Obraz ma zbilansowany obciążeniem punkt końcowy HTTP, który odbiera żądania oceniania, które są wysyłane do usługi sieci web.

Platforma Azure ułatwia monitorowanie usługi sieci web przez zbieranie danych telemetrycznych usługi Application Insights lub danych telemetrycznych modelu, jeśli wybrano włączenie tej funkcji. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniach konta usługi Application Insights i magazynu.

Jeśli włączono automatyczne skalowanie, platforma Azure automatycznie skaluje wdrożenie.

Na przykład wdrażania modelu jako usługi sieci web, zobacz [Wdrażanie modelu klasyfikacji obrazu w wystąpieniach kontenera platformy Azure.](tutorial-deploy-models-with-aml.md)

#### <a name="iot-module-endpoints"></a>Punkty końcowe modułu IoT

Wdrożony punkt końcowy modułu IoT to kontener platformy Docker, który zawiera model i skojarzony skrypt lub aplikację oraz wszelkie dodatkowe zależności. Wdrożyć te moduły przy użyciu usługi Azure IoT Edge na urządzeniach brzegowych.

Jeśli włączono monitorowanie, platforma Azure zbiera dane telemetryczne z modelu wewnątrz modułu usługi Azure IoT Edge. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniu konta magazynu.

Usługa Azure IoT Edge zapewnia, że moduł jest uruchomiony i monitoruje urządzenie, które go hostuje.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Instancji obliczeń (wersja zapoznawcza)

**Wystąpienie obliczeniowe usługi Azure Machine Learning** (dawniej maszyna wirtualna notesu) to w pełni zarządzana stacja robocza oparta na chmurze, która zawiera wiele narzędzi i środowisk zainstalowanych dla uczenia maszynowego. Wystąpienia obliczeniowe mogą służyć jako cel obliczeniowy dla zadań szkoleniowych i wnioskowania. W przypadku dużych zadań [klastry obliczeniowe usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) z możliwościami skalowania wielu węzłów są lepszym wyborem docelowym obliczeń.

Dowiedz się więcej o [wystąpieniach obliczeniowych](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Zestawy danych i magazyny danych

**Zestawy danych usługi Azure Machine Learning** (wersja zapoznawcza) ułatwiają dostęp do danych i pracę z nim. Zestawy danych zarządzają danymi w różnych scenariuszach, takich jak szkolenie modelu i tworzenie potoku. Za pomocą zestawu SDK usługi Azure Machine Learning można uzyskać dostęp do magazynu bazowego, eksplorować dane i zarządzać cyklem życia różnych definicji zestawu danych.

Zestawy danych zawierają metody pracy z danymi w popularnych `from_delimited_files()` `to_pandas_dataframe()`formatach, takich jak używanie lub .

Aby uzyskać więcej informacji, zobacz [Tworzenie i rejestrowanie zestawów danych usługi Azure Machine Learning](how-to-create-register-datasets.md).  Aby uzyskać więcej przykładów użycia zestawów danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

**Magazyn danych** to abstrakcja magazynu za pomocą konta magazynu platformy Azure. Magazyn danych można użyć kontenera obiektów blob platformy Azure lub udziału plików platformy Azure jako magazynu zaplecza. Każdy obszar roboczy ma domyślny magazyn danych i można zarejestrować dodatkowe magazyny danych. Użyj interfejsu API zestawów SDK języka Python lub interfejsu wiersza polecenia usługi Azure Machine Learning do przechowywania i pobierania plików z magazynu danych.

### <a name="compute-targets"></a>Docelowe obiekty obliczeniowe

Obiekt [docelowy obliczeń](concept-compute-target.md) umożliwia określenie zasobu obliczeniowego, w którym uruchomisz skrypt szkoleniowy lub hostujesz wdrożenie usługi. Ta lokalizacja może być komputer lokalny lub zasób obliczeniowy oparty na chmurze.

Dowiedz się więcej o [dostępnych celach obliczeniowych dla szkoleń i wdrażania.](concept-compute-target.md)

### <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz:

* [Co to jest usługa Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md)
* [Samouczek (część 1): Trenuj model](tutorial-train-models-with-aml.md)
