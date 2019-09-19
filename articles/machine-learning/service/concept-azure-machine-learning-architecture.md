---
title: Architektura & kluczowe pojęcia
titleSuffix: Azure Machine Learning
description: Poznaj architekturę, terminy, koncepcje i przepływy pracy, które składają się Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: 91c747b8b4ca58e7714dc101777bad51f9f0286f
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035595"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak działa Azure Machine Learning: Architektura i pojęcia

Poznaj architekturę, koncepcje i przepływ pracy dla Azure Machine Learning. Główne składniki usługi i ogólny przepływ pracy związane z korzystaniem z usługi są pokazane na poniższym diagramie:

![Azure Machine Learning architektura i przepływ pracy](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Przepływ pracy

Przepływ pracy modelu uczenia maszynowego jest zwykle następujący:

1. **Trasy**
    + Opracowuj skrypty szkoleniowe dotyczące uczenia maszynowego w języku **Python** lub za pomocą interfejsu wizualnego.
    + Tworzenie i konfigurowanie **obliczeniowego elementu docelowego**.
    + **Przesyłanie skryptów usługi** docelową obliczeń skonfigurowany do uruchamiania w środowisku. Podczas szkoleń skrypty mogą odczytywać dane z **magazynu**danych lub zapisywać do niego. Rekordy wykonywania są zapisywane jako **uruchomienia** w **obszarze roboczym** i pogrupowane w obszarze **eksperymenty**.

1. **Pakiet** — po znalezieniu zadowalającego przebiegu Zarejestruj trwały model w **rejestrze modelu**.

1. **Sprawdź** - poprawność**zapytania** o zarejestrowane metryki z bieżącego i przeszłego uruchomienia. Jeśli metryki nie wskazują żądanego wyniku, pętla Wróć do kroku 1 i wykonuje iterację w skryptach.

1. **Wdróż** — opracowuje skrypt oceniania, który używa modelu i **wdraża model** jako **usługę sieci Web** na platformie Azure lub na **IoT Edge urządzenie**.

1. **Monitor** -monitoruje **dryf danych** między zestawem danych szkoleń i danymi wnioskowania wdrożonego modelu. W razie potrzeby Wróć do kroku 1, aby ponownie przeprowadzić uczenie modelu przy użyciu nowych danych szkoleniowych.

## <a name="tools-for-azure-machine-learning"></a>Narzędzia dla Azure Machine Learning

Użyj tych narzędzi dla Azure Machine Learning:

+  Korzystanie z usługi w dowolnym środowisku języka Python z [zestawem SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatyzuj działania uczenia maszynowego za pomocą [interfejsu wiersza polecenia Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Napisz kod w Visual Studio Code z [rozszerzeniem Azure Machine Learning vs Code](how-to-vscode-tools.md)
+ Użyj [interfejsu wizualizacji (wersja zapoznawcza), aby uzyskać Azure Machine Learning](ui-concept-visual-interface.md) wykonywania kroków przepływu pracy bez pisania kodu.

> [!NOTE]
> Chociaż w tym artykule opisano warunki i pojęcia używane przez Azure Machine Learning, nie zdefiniowano warunków i koncepcji dla platformy Azure. Aby uzyskać więcej informacji na temat terminologii platformy Azure, zobacz [słownik Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Słownik

+ <a href="#workspaces">Workspace</a>
+ <a href="#experiments">Eksperymenty</a>
+ <a href="#models">Przykładów</a>
+ <a href="#run-configurations">Uruchom konfigurację</a>
+ [Szacowania](#estimators)
+ <a href="#datasets-and-datastores">& Magazynów danych</a>
+ <a href="#compute-targets">Cele obliczeń</a>
+ <a href="#training-scripts">Skrypt szkoleniowy</a>
+ <a href="#runs">Run</a>
+ <a href="#github-tracking-and-integration">Śledzenie git</a>
+ <a href="#snapshots">Zdjęcie</a>
+ <a href="#activities">Aktywność</a>
+ <a href="#images">Obraz</a>
+ <a href="#deployment">Wdrożenie</a>
+ <a href="#web-service-deployments">Usługi sieci Web</a>
+ <a href="#iot-module-deployments">Moduły IoT</a>
+ <a href="#ml-pipelines">Potoki ML</a>
+ <a href="#logging">Logging</a>

### <a name="workspaces"></a>Obszary robocze

[Obszar roboczy](concept-workspace.md) jest zasobem najwyższego poziomu dla Azure Machine Learning. Zapewnia centralne miejsce do pracy ze wszystkimi artefaktami tworzonymi podczas korzystania z Azure Machine Learning. Obszar roboczy można udostępniać innym osobom. Aby uzyskać szczegółowy opis obszarów roboczych, zobacz [co to jest obszar roboczy Azure Machine Learning?](concept-workspace.md)

### <a name="experiments"></a>Eksperymenty

Eksperyment to grupa wielu przebiegów z określonego skryptu. Zawsze należy do obszaru roboczego. Po przesłaniu przebiegu można podać nazwę eksperymentu. Informacje dotyczące uruchomienia są przechowywane w ramach tego eksperymentu. Jeśli przesyłasz przebieg i określisz nieistniejącą nazwę eksperymentu, zostanie automatycznie utworzony nowy eksperyment z nowo określoną nazwą.

Przykład korzystania z eksperymentu można znaleźć [w samouczku: Uczenie swojego pierwszego](tutorial-1st-experiment-sdk-train.md)modelu.

### <a name="models"></a>Modele

W najprostszym modelem jest fragmentem kodu, który przyjmuje dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego obejmuje algorytmu, wybierając dostarczanie danych i dostosowywanie hiperparametrów. Szkolenie jest procesem iteracyjnym, tworzącego trenowanego modelu, który hermetyzuje, jakie zakorzenione podczas procesu uczenia modelu.

Model jest generowany przez uruchomienie w usłudze Azure Machine Learning. Można również użyć modelu, który jest przeszkolony poza Azure Machine Learning. Model można zarejestrować w obszarze roboczym Azure Machine Learning.

Azure Machine Learning to struktura niezależny od. Podczas tworzenia modelu można użyć dowolnej popularnej platformy uczenia maszynowego, takiej jak Scikit-Learning, XGBoost, PyTorch, TensorFlow i łańcucha.

Aby zapoznać się z przykładem szkoleń dotyczących modelu przy użyciu Scikit — uczenie i [szacowania, zobacz Samouczek: Uczenie modelu klasyfikacji obrazów przy użyciu](tutorial-train-models-with-aml.md)Azure Machine Learning.

**Rejestr modelu** śledzi wszystkie modele w obszarze roboczym Azure Machine Learning.

Modele są identyfikowane przez nazwę i wersję. Za każdym razem, gdy rejestrujesz model o takiej samej nazwie jak istniejący, rejestr założono, że jest to nowa wersja. Wersja jest zwiększana, a nowy model jest zarejestrowany pod tą samą nazwą.

Po zarejestrowaniu modelu można podać dodatkowe Tagi metadanych, a następnie użyć tagów podczas wyszukiwania modeli.

> [!TIP]
> Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym Azure Machine Learning. Po zarejestrowaniu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

Nie można usunąć zarejestrowanego modelu, który jest używany przez aktywne wdrożenie.

Aby zapoznać się z przykładem rejestrowania modelu, zobacz [uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Uruchom konfiguracje

Konfiguracja przebiegu to zestaw instrukcji, które definiują sposób uruchamiania skryptu w określonym elemencie docelowym obliczeń. Konfiguracja obejmuje szeroki zestaw definicji zachowań, takich jak użycie istniejącego środowiska Python lub użycie środowiska Conda, które jest kompilowane ze specyfikacji.

Konfigurację przebiegu można utrwalić do pliku znajdującego się w katalogu, który zawiera skrypt szkoleniowy lub który może być skonstruowany jako obiekt w pamięci i użyty do przesłania uruchomienia.

Na przykład Uruchom konfiguracje, zobacz [Wybieranie i używanie elementu docelowego obliczeń do uczenia modelu](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Szacowania

Aby ułatwić uczenie modeli przy użyciu popularnych struktur, Klasa szacowania umożliwia łatwe konstruowanie konfiguracji uruchomieniowych. Można utworzyć i użyć generycznej [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptów szkoleniowych, które korzystają z wybranej platformy szkoleniowej (na przykład scikit-Learning).

W przypadku zadań PyTorch, TensorFlow i łańcucha, Azure Machine Learning również udostępnia odpowiednie [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) szacowania, aby uprościć korzystanie z tych platform.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Uczenie modeli ml z szacowania](how-to-train-ml-models.md).
* [Pytorch modele uczenia głębokiego na dużą skalę w Azure Machine Learning](how-to-train-pytorch.md).
* [Uczenie i rejestrowanie modeli TensorFlow na dużą skalę przy użyciu Azure Machine Learning](how-to-train-tensorflow.md).
* [Uczenie i rejestrowanie modeli łańcucha na dużą skalę przy użyciu Azure Machine Learning](how-to-train-chainer.md).

### <a name="datasets-and-datastores"></a>Zestawy danych i magazyny datastores

**Azure Machine Learning zestawy danych** (wersja zapoznawcza) ułatwia dostęp do danych i pracę z nimi. Zestawy danych zarządzają danymi w różnych scenariuszach, takich jak szkolenia modeli i tworzenie potoku. Za pomocą zestawu SDK Azure Machine Learning można uzyskać dostęp do magazynu bazowego, eksplorować dane i zarządzać cyklem życia różnych definicji zestawu danych.

Zestawy danych udostępniają metody pracy z danymi w popularnych formatach, np. przy `from_delimited_files()` użyciu `to_pandas_dataframe()`lub.

Aby uzyskać więcej informacji, zobacz [Tworzenie i Rejestrowanie zestawów danych Azure Machine Learning](how-to-create-register-datasets.md).  Aby uzyskać więcej przykładów użycia zestawów danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

**Magazyn** danych to Abstrakcja magazynu przez konto usługi Azure Storage. Magazyn danych może używać kontenera obiektów blob platformy Azure lub udziału plików platformy Azure jako magazynu zaplecza. Każdy obszar roboczy ma domyślny magazyn danych i można zarejestrować dodatkowe magazyny danych. Użyj interfejsu API zestawu SDK języka Python lub wiersza polecenia Azure Machine Learning, aby przechowywać i pobierać pliki z magazynu danych.

### <a name="compute-targets"></a>Celów obliczeń

[Obiekt docelowy obliczeń](concept-compute-target.md) pozwala określić zasób obliczeniowy służący do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. Ta lokalizacja może być maszyną lokalną lub zasobem obliczeniowym opartym na chmurze. Elementy docelowe obliczeń ułatwiają zmianę środowiska obliczeniowego bez konieczności zmiany kodu.

Dowiedz się więcej o [dostępnych celach obliczeniowych na potrzeby szkolenia i wdrażania](concept-compute-target.md).

### <a name="training-scripts"></a>Skryptów szkolenia

Do nauczenia modelu, można określić katalog, który zawiera skrypt szkolenia i skojarzone pliki. Należy również określić nazwę eksperymentu, która jest używana do przechowywania informacji zebranych podczas szkolenia. Podczas szkolenia cały katalog jest kopiowany do środowiska szkoleniowego (elementu docelowego obliczeń), a skrypt określony przez konfigurację uruchamiania jest uruchamiany. Migawka katalogu znajduje się również w obszarze eksperymentu, w obszarze roboczym.

Przykład — patrz [Tutorial: Uczenie modelu klasyfikacji obrazów przy użyciu](tutorial-train-models-with-aml.md)Azure Machine Learning.

### <a name="runs"></a>Przebiegi

Uruchomienie to pojedyncze wykonanie skryptu szkoleniowego. Azure Machine Learning rejestruje wszystkie uruchomienia i przechowuje następujące informacje:

* Metadane dotyczące przebiegu (Sygnatura czasowa, czas trwania itd.)
* Metryki rejestrowane przez skrypt
* Pliki wyjściowe, które są zbierane przez eksperyment lub jawnie przekazane przez użytkownika
* Migawka katalogu, który zawiera skrypty, przed przebiegu

Podczas przesyłania skryptu w celu uczenia modelu należy utworzyć przebieg. Uruchom może mieć zero lub więcej uruchomień podrzędnych. Na przykład uruchomienie najwyższego poziomu może mieć dwa uruchomienia podrzędne, z których każdy może mieć własne uruchomienie podrzędne.

### <a name="github-tracking-and-integration"></a>Śledzenie i integracja z usługą GitHub

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Na przykład bieżący identyfikator zatwierdzenia dla repozytorium jest rejestrowany jako część historii. Działa to z przebiegami przesłanymi przy użyciu potoku szacowania, ML lub uruchamiania skryptu. Działa również w przypadku przebiegów przesłanych z zestawu SDK lub interfejsu wiersza polecenia Machine Learning.

### <a name="snapshots"></a>Migawki

W przypadku przesyłania przebiegu Azure Machine Learning kompresuje katalog zawierający skrypt jako plik zip i wysyła go do obiektu docelowego obliczeń. Następnie plik zip zostanie wyodrębniony, a skrypt zostanie uruchomiony w tym miejscu. Usługa Azure Machine Learning są także przechowywane w pliku zip jako migawka jako część rekordu uruchomienia. Każda osoba mająca dostęp do obszaru roboczego można przeglądać rekordu uruchomienia i pobrać migawki.

> [!NOTE]
> Aby zapobiec dołączeniu niepotrzebnych plików do migawki, należy wykonać ignorowanie pliku (. gitignore lub. amlignore). Umieść ten plik w katalogu migawek i Dodaj do niego nazwy plików. Plik. amlignore używa tej samej [składni i wzorców co plik. gitignore](https://git-scm.com/docs/gitignore). Jeśli istnieją oba pliki, plik. amlignore ma pierwszeństwo.

### <a name="activities"></a>Działania

Działanie reprezentuje długotrwałej operacji. Następujące operacje są przykłady działań:

* Tworzenie lub usuwanie obliczeniowego elementu docelowego
* Uruchomienie skryptu na obliczeniowego elementu docelowego

Działania mogą udostępniać powiadomienia za pomocą zestawu SDK lub internetowego interfejsu użytkownika, dzięki czemu można łatwo monitorować postęp tych operacji.

### <a name="images"></a>Obrazy

Obrazy umożliwiają niezawodne Wdrażanie modelu wraz ze wszystkimi składnikami, które są potrzebne do korzystania z modelu. Obraz zawiera następujące elementy:

* Model.
* Do oceniania skryptu lub aplikacji. Za pomocą skryptu można przekazać dane wejściowe do modelu i zwrócić dane wyjściowe modelu.
* Zależności, które są wymagane przez model lub skrypt oceniający lub aplikację. Na przykład mogą obejmować plik środowiska Conda, który zawiera listę zależności pakietu języka Python.

Azure Machine Learning można utworzyć dwa typy obrazów:

* **Obraz FPGA**: Używane podczas wdrażania do tablicy opartej na trybie programowalnym dla pól na platformie Azure.
* **Obraz platformy Docker**: Używane podczas wdrażania do elementów docelowych obliczeń innych niż FPGA. Przykłady to Azure Container Instances i usługa Azure Kubernetes.

Azure Machine Learning udostępnia obraz podstawowy, który jest używany domyślnie. Możesz również udostępnić własne obrazy niestandardowe.

### <a name="image-registry"></a>Rejestru obrazów

Obrazy są umieszczane w katalogu w **rejestrze obrazu** w obszarze roboczym. Możesz podać dodatkowe Tagi metadanych podczas tworzenia obrazu, aby można było wykonać zapytania do nich, aby później znaleźć obraz.

Aby zapoznać się z przykładem tworzenia obrazu, zobacz [Wdrażanie modelu klasyfikacji obrazów w Azure Container Instances](tutorial-deploy-models-with-aml.md).

Aby zapoznać się z przykładem wdrażania modelu przy użyciu obrazu niestandardowego, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Wdrożenie

Wdrożenie to tworzenie wystąpienia modelu w usłudze sieci Web, która może być hostowana w chmurze lub w module IoT na potrzeby wdrożeń zintegrowanych urządzeń.

#### <a name="web-service-deployments"></a>Wdrożenia usługi sieci Web

Wdrożona usługa sieci Web może używać Azure Container Instances, usługi Azure Kubernetes lub FPGA. Usługę można utworzyć z modelu, skryptu i skojarzonych plików. Są one hermetyzowane w obrazie, który zapewnia środowisko uruchomieniowe dla usługi sieci Web. Obraz zawiera punkt końcowy HTTP o zrównoważonym obciążeniu, który odbiera żądania oceniania wysyłane do usługi sieci Web.

Platforma Azure pomaga monitorować wdrożenie usługi sieci Web, zbierając Application Insights dane telemetryczne lub model telemetrii, jeśli wybrano włączenie tej funkcji. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniach Application Insights i konta magazynu.

Jeśli włączono automatyczne skalowanie, platforma Azure automatycznie skaluje wdrożenie.

Aby zapoznać się z przykładem wdrażania modelu jako usługi sieci Web, zobacz [Wdrażanie modelu klasyfikacji obrazów w Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Wdrożenia modułu IoT

Wdrożony moduł IoT to kontener platformy Docker, który obejmuje model i skojarzony skrypt lub aplikację oraz wszelkie dodatkowe zależności. Te moduły są wdrażane przy użyciu Azure IoT Edge na urządzeniach brzegowych.

Jeśli włączono monitorowanie, platforma Azure zbiera dane telemetryczne z modelu w module Azure IoT Edge. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniu konta magazynu.

Azure IoT Edge zapewnia, że moduł jest uruchomiony, i monitoruje urządzenie, które je obsługuje.

### <a name="ml-pipelines"></a>Potokach uczenia Maszynowego

Potoki uczenia maszynowego umożliwiają tworzenie przepływów pracy, które łączą etapy uczenia maszynowego, oraz zarządzanie nimi. Na przykład potok może obejmować przygotowanie danych, szkolenie modeli, wdrożenie modelu i etapy wnioskowania/oceniania. Każda faza może obejmować wiele kroków, z których każdy może uruchamiane w różnych celów obliczeń. 

Kroki potoku są wielokrotnego użytku i można je uruchomić bez ponownego uruchomienia kolejnych kroków, jeśli dane wyjściowe tego kroku nie uległy zmianie. Na przykład możesz ponownie przeprowadzić uczenie modelu bez ponownego uruchamiania kosztownych kroków przygotowywania danych, jeśli dane nie uległy zmianie. Potoki umożliwiają również analitykom danych współpracę podczas pracy nad osobnymi obszarami przepływu pracy uczenia maszynowego.

Aby uzyskać więcej informacji na temat potoków uczenia maszynowego za pomocą tej usługi, zobacz [potoki i Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Rejestrowanie

Podczas opracowywania rozwiązania Użyj zestawu SDK języka Python Azure Machine Learning w skrypcie języka Python, aby rejestrować arbitralne metryki. Po uruchomieniu Zbadaj metryki, aby określić, czy przebieg został utworzony przez model, który chcesz wdrożyć.

### <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Azure Machine Learning, zobacz:

* [Co to jest Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md)
* [Samouczek (część 1): trenowanie modelu](tutorial-train-models-with-aml.md)
