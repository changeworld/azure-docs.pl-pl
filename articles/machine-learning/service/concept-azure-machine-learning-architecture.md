---
title: Jak działa usługa Azure Machine Learning?
description: Więcej informacji na temat architektury, terminologii i pojęcia związane z usługi Azure Machine Learning. Poznasz również ogólny przepływ pracy zalety korzystania z usługi i usług platformy Azure, które są używane przez usługę Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 3011fa85dbac2135f4d9113c6b76a8b667ee4013
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952137"
---
# <a name="architecture-and-concepts-how-does-azure-machine-learning-service-work"></a>Architektury i koncepcji: jak działa usługa Azure Machine Learning? 

W tym dokumencie opisano architekturę i pojęcia dotyczące usługi Azure Machine Learning. Poniższy diagram przedstawia główne składniki usługi i przedstawiono ogólny przepływ pracy, podczas korzystania z usługi: 

[![Usługa Azure Machine Learning architektury i przepływu pracy](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Przepływ pracy jest zwykle obejmuje następujące kroki:

1. Tworzenie usługi machine learning szkolenia skryptów w __Python__.
1. Tworzenie i konfigurowanie __obliczeniowego elementu docelowego__.
1. __Przesyłanie skryptów usługi__ docelową obliczeń skonfigurowany do uruchamiania w środowisku. Podczas szkolenia, obliczeniowego elementu docelowego przechowuje wykonywania rekordów __datastore__. Brak rekordów są zapisywane w __eksperymentować__.
1. __Zapytanie eksperymentu__ dla zarejestrowanych metryk z bieżącej i wcześniejszych przebiegów. Jeśli metryki nie wskazują żądanego wyniku, pętli, wróć do kroku 1 i powtarzanie czynności w skryptach.
1. Po znalezieniu zadowalające Uruchom rejestrowanie utrwalonej modelu w __rejestru modelu__.
1. Twórz skrypt oceniania.
1. __Tworzenie obrazu__ i zarejestrować ją w __rejestru obrazów__. 
1. __Wdrażanie obrazu__ jako __usługi sieci web__ na platformie Azure.


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> Chociaż w tym dokumencie definiuje terminy i pojęcia używane przez usługi Azure Machine Learning, nie definiuje terminy i pojęcia związane z platformą Azure. Aby uzyskać więcej informacji na temat terminologii platformy Azure, zobacz [słownik Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Obszar roboczy

Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning. Zapewnia scentralizowanym miejscem do pracy z wszystkich artefaktów, utworzonej w przypadku korzystania z usługi Azure Machine Learning.

Obszar roboczy przechowuje listę obliczeniowych elementów docelowych, które mogą służyć do uczenia modelu. Zapewnia również historię przebiegów szkoleniowych, w tym dzienniki, metryki, dane wyjściowe i migawkę skryptów. Te informacje jest używany do określenia, które Uruchom szkolenia tworzy najlepszy model.

Modele zostały zarejestrowane przy użyciu obszaru roboczego. Zarejestrowanego modelu i oceniania skrypty są używane do tworzenia obrazu. Następnie można wdrożyć obraz do usługi Azure Container Instances, Azure Kubernetes Service lub do tablicy programowalny bramy (FPGA) jako punkt końcowy oparty na protokole REST protokołu HTTP. On również można wdrożyć na urządzeniu z systemem Azure IoT Edge jako moduł.

Możesz utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowany przez wiele osób. Udostępniając obszar roboczy, kontrolować dostęp do obszaru roboczego, przypisując następujące role użytkowników:

* Właściciel
* Współautor
* Czytelnik

Gdy tworzysz nowy obszar roboczy, automatycznie tworzy kilka zasobów platformy Azure, które są używane przez obszar roboczy:

* [Usługa Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -rejestry kontenerów platformy docker, które są używane podczas uczenia i podczas wdrażania modelu.
* [Usługa Azure Storage](https://azure.microsoft.com/services/storage/) — używany jako domyślny magazyn danych dla obszaru roboczego.
* [Usługa Azure Application Insights](https://azure.microsoft.com/services/application-insights/) — monitorowanie informacji o modelach magazynów.
* [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) — magazyny wpisów tajnych używanych przez celów obliczeń i inne poufne informacje wymagane przez obszar roboczy.

> [!NOTE]
> Zamiast tworzyć nowe wersje, umożliwia również istniejące usługi platformy Azure. 

Poniższy diagram jest taksonomii obszaru roboczego:

[![Taksonomia obszaru roboczego](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>Modelowanie

W najprostszym modelem jest fragmentem kodu, który przyjmuje dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego obejmuje algorytmu, wybierając dostarczanie danych i dostosowywanie hiperparametrów. Szkolenie jest procesem iteracyjnym, tworzącego trenowanego modelu, który hermetyzuje, jakie zakorzenione podczas procesu uczenia modelu.

Model jest generowany przez uruchomienie w usłudze Azure Machine Learning. Można również użyć modelu skonfigurowanych pod kątem poza usługi Azure Machine Learning. W obszarze roboczym usługi Azure Machine Learning można zarejestrować model.

Usługa Azure Machine Learning jest niezależny od framework. Podczas tworzenia modelu, na przykład scikit, można użyć dowolnej architektury uczenia maszynowego popularnych — Dowiedz się, xgboost, PyTorch, TensorFlow, Chainer i CNTK.

Aby uzyskać przykład uczenia modelu, zobacz [Szybki Start: tworzenie obszaru roboczego uczenia maszynowego](quickstart-get-started.md) dokumentu.

### <a name="model-registry"></a>Model rejestru

Rejestr modelu przechowuje informacje o wszystkich modeli w obszarze roboczym usługi Azure Machine Learning. 

Modele są identyfikowane przez nazwę i wersję. Każdorazowo, należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru przyjęto założenie, że jest nowa wersja. Numer wersji jest zwiększany i nowy model jest zarejestrowany pod nazwą.

Możesz podać dodatkowe metadane znaczniki po zarejestrować model, a następnie użyć tych znaczników podczas wyszukiwania dla modeli.

Nie można usunąć modeli, które są używane przez obraz.

Na przykład zarejestrować model, zobacz [Wytrenuj model klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu.

## <a name="image"></a>Image (Obraz)

Obrazy umożliwiają niezawodne wdrażanie modelu, oraz wszystkie składniki wymagane do używania tego modelu. Obraz zawiera następujące elementy:

* Model.
* Do oceniania skryptu lub aplikacji. Ten skrypt służy do przekazywania danych wejściowych do modelu i zwraca wynik w modelu.
* Zależności wymagane przez model lub skrypt oceniania/aplikacji.  Na przykład mogą obejmować plik środowiska Conda, który zawiera listę zależności pakietu języka Python.

Istnieją dwa typy obrazów, które mogą być tworzone przez usługi Azure Machine Learning:

* Obraz FPGA: używana podczas wdrażania na tablicę programowalny bramy w chmurze platformy Azure.
* Obraz platformy docker: używana do celów innych niż FPGA obliczeń podczas wdrażania. Na przykład w usłudze Azure Container Instances i Azure Kubernetes Service.

Aby uzyskać przykład tworzenia obrazu, zobacz [wdrożyć model klasyfikacji obrazów w wystąpieniu kontenera platformy Azure](tutorial-deploy-models-with-aml.md) dokumentu.

### <a name="image-registry"></a>Rejestru obrazów

Rejestru obrazów przechowuje informacje o obrazów utworzonych przy użyciu modeli. Możesz podać dodatkowe metadane tagów podczas tworzenia obrazu. Tagi metadanych są przechowywane w rejestrze obrazu i może być odpytywana można znaleźć obrazu.

## <a name="deployment"></a>Wdrożenie

Wdrożenie jest egzemplarzem obraz na obu usługę internetową, która może być hostowana w chmurze lub moduł usługi IoT dla wdrożeń IDE. 

### <a name="web-service"></a>Usługa sieci Web

Wdrożonej usługi sieci web można użyć usługi Azure Container Instances, Azure Kubernetes Service lub tablic programowalny bramy (FPGA).
Usługa jest tworzona z obrazu, który hermetyzuje z modelu, skrypt i skojarzone pliki. Obraz ma ze zrównoważonym obciążeniem końcowego HTTP, który odbiera oceniania żądań wysyłanych do usługi sieci web.

Platforma Azure ułatwia monitorowanie wdrożenia swojej usługi sieci Web przez zbieranie danych telemetrycznych Application Insights i/lub model danych telemetrycznych, jeśli wybrano włączyć tę funkcję. Dane telemetryczne tylko jest dostępny dla Ciebie i przechowywane w usłudze Application Insights i wystąpień konta magazynu.

Po włączeniu automatycznego skalowania platformy Azure zostanie automatycznie skalować wdrożenie.

Aby uzyskać przykład wdrażania modelu w postaci usługi sieci web, zobacz [wdrożyć model klasyfikacji obrazów w wystąpieniu kontenera platformy Azure](tutorial-deploy-models-with-aml.md) dokumentu.

### <a name="iot-module"></a>Moduł IoT

Wdrożone moduł IoT to kontener platformy Docker, który zawiera model i skojarzone skryptu lub aplikacji i wszelkie dodatkowe zależności. Te moduły są wdrażane na urządzeniach brzegowych za pomocą usługi Azure IoT Edge. 

Po włączeniu monitorowania platformy Azure zbiera dane telemetryczne z modelu w module usługi Azure IoT Edge. Dane telemetryczne tylko jest dostępny dla Ciebie i przechowywane w wystąpieniu konta usługi storage.

Usługa Azure IoT Edge będzie upewnij się, że modułu działa i monitorowania urządzeń, który jest hostem go.

## <a name="datastore"></a>Magazyn danych

Magazyn danych jest abstrakcyjną magazynu przez konto usługi Azure Storage. Magazyn danych można użyć kontenera obiektów blob platformy Azure lub udziału plików platformy Azure jako magazynu zaplecza. Każdy obszar roboczy ma domyślny magazyn danych, dlatego możesz zarejestrować dodatkowe magazynów danych. 

Użyj interfejsu API zestawu SDK języka Python lub interfejsu wiersza polecenia usługi Azure Machine Learning do przechowywania i pobierania plików z magazynu danych. 

## <a name="run"></a>Uruchom polecenie

Przebieg jest rekord, który zawiera następujące informacje:

* Metadane dotyczące przebiegu (sygnatura czasowa, czas trwania itp.)
* Zarejestrowane przez skrypt metryki
* Pliki wyjściowe automatycznie zebranych w doświadczeniu lub jawnie przekazane przez użytkownika.
* Migawka katalogu, który zawiera skrypty, przed przebiegu

Uruchom jest generowany, gdy prześlesz skryptu w celu nauczenia modelu. Uruchom może mieć zero lub więcej uruchomień podrzędnych. Więc wykonywania najwyższego poziomu może mieć dwa przebiegi podrzędnych, z których każdy może mieć własne podrzędne działa.

Aby uzyskać przykład wyświetlanie działa produkowane uczenia modelu, zobacz [Szybki Start: wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a name="experiment"></a>Eksperyment

Eksperyment to zbiór wielu uruchomień z danego skryptu. Zawsze należy do obszaru roboczego. Po przesłaniu przebiegu można podać nazwę eksperymentu. Informacje dotyczące uruchomienia są przechowywane w ramach tego eksperymentu. Jeśli przesłać przebiegu i określ nazwę eksperymentu, który nie istnieje, automatycznie zostanie utworzony nowy eksperyment o tej nazwie.

Na przykład z użyciem eksperymentu zobacz [Szybki Start: wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a name="compute-target"></a>Obliczeniowego elementu docelowego

Cel obliczenia jest używane do uruchamiania skryptu szkolenia lub wdrożeniu usługi sieci web hosta zasobów obliczeniowych. Są obsługiwane obliczeniowych elementów docelowych: 

* Komputer lokalny
* Maszyny Wirtualnej z systemem Linux na platformie Azure (np. maszyna wirtualna do nauki o danych)
* Usługa Azure Batch AI klastra
* Platforma Apache Spark dla HDInsight
* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service

Obliczeniowych elementów docelowych są dołączone do obszaru roboczego. Obliczeniowe elementy docelowe, innym niż komputer lokalny są udostępniane przez użytkowników obszaru roboczego.

Większość obliczeń obiekty docelowe mogą być tworzone za pomocą witryny Azure portal, zestawu SDK usługi Azure Machine Learning lub wiersza polecenia platformy Azure bezpośrednio za pomocą obszaru roboczego. Jeśli masz obliczeniowych elementów docelowych, które zostały utworzone przez inny proces (na przykład, witryny Azure portal lub interfejsu wiersza polecenia platformy Azure), możesz dodać (dołączanie) je do swojego obszaru roboczego. Niektóre obliczeniowych elementów docelowych muszą być tworzone spoza obszaru roboczego i następnie dołączyć.

Więcej informacji o dobieraniu obliczeniowego elementu docelowego dla szkolenia, zobacz [wybierz, a następnie użyć obliczeniowego elementu docelowego na podstawie uczyć modele](how-to-set-up-training-targets.md) dokumentu.

Więcej informacji o dobieraniu obliczeniowego elementu docelowego dla wdrożenia, zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md) dokumentu.

## <a name="run-configuration"></a>Uruchom konfigurację

Konfigurację uruchomieniową jest zestaw instrukcji, która definiuje, jak uruchomić skryptu w danym obliczeniowego elementu docelowego. Obejmuje wielu różnych definicjach zachowanie, np. Czy chcesz użyć istniejącego środowiska Python, czy użyć środowiska Conda, utworzony na podstawie specyfikacji.

Konfigurację uruchomieniową mogą zostać utrwalone w pliku w katalogu, który zawiera skrypt szkolenia lub konstruowany jako obiekt w pamięci i użytego do przesłania przebiegu.

Na przykład konfiguracji uruchamiania, zobacz [wybierz, a następnie użyć obliczeniowego elementu docelowego na podstawie uczyć modele](how-to-set-up-training-targets.md) dokumentu.

## <a name="training-script"></a>Skrypt szkoleniowy

Do nauczenia modelu, można określić katalog, który zawiera skrypt szkolenia i skojarzone pliki. Należy również określić nazwę eksperymentu, który jest używany do przechowywania informacji zebranych podczas szkolenia. Podczas szkolenia, cały katalog jest kopiowany do treningowe (obiekt docelowy obliczeniowe) i uruchomieniu skryptu określony w konfiguracji przebiegu. Migawka katalogu znajduje się również w obszarze eksperymentu, w obszarze roboczym.

Przykład za pomocą skryptów do nauczenia modelu, zobacz [Utwórz obszar roboczy za pomocą języka Python](quickstart-get-started.md)

## <a name="logging"></a>Rejestrowanie

Podczas tworzenia rozwiązania, należy użyć zestawu SDK języka Python usługi Azure Machine Learning w skrypcie języka Python do rejestrowania dowolnego metryki. Po uruchomieniu zapytania metryki, aby określić, jeśli działanie utworzony model który chcesz wdrożyć. 

## <a name="snapshot"></a>Snapshot

Podczas przesyłania przebiegu, Azure Machine Learning kompresuje katalogu, który zawiera skrypt jako plik zip i wysyła je do obliczeniowego elementu docelowego. Pliku zip jest rozszerzany, a skrypt jest uruchamiany istnieje. Usługa Azure Machine Learning są także przechowywane w pliku zip jako migawka jako część rekordu uruchomienia. Każda osoba mająca dostęp do obszaru roboczego można przeglądać rekordu uruchomienia i pobrać migawki.

## <a name="activity"></a>Działanie

Działanie reprezentuje długotrwałej operacji. Następujące operacje są przykłady działań:

* Tworzenie lub usuwanie obliczeniowego elementu docelowego
* Uruchomienie skryptu na obliczeniowego elementu docelowego

Działania może zapewnić powiadomienia za pośrednictwem zestawu SDK lub interfejsu użytkownika sieci Web, dzięki czemu możesz łatwo monitorować postęp tych operacji.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć korzystanie z usługi Azure Machine Learning, należy użyć następujących łączy:

* [Co to jest usługa Azure Machine Learning](overview-what-is-azure-ml.md)
* [Szybki Start: Tworzenie obszaru roboczego za pomocą języka Python](quickstart-get-started.md)
* [Samouczek: Uczenie modelu](tutorial-train-models-with-aml.md)
