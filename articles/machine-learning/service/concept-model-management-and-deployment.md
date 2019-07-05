---
title: 'MLOps: Zarządzanie, wdrażanie i monitorowanie modeli uczenia Maszynowego'
titleSuffix: Azure Machine Learning service
description: 'Dowiedz się, jak używać usługi Azure Machine Learning dla MLOps: wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je. Można wdrażać te modele, które uczony przy użyciu usługi Azure Machine Learning, na komputerze lokalnym lub z innych źródeł.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 11a4a17d7816d2302b6549cffb9517e10ad1258d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442343"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning

Ten artykuł zawiera informacje na temat jak zarządzanie cyklem życia Twoich modeli za pomocą usługi Azure Machine Learning. Usługę Azure Machine Learning korzysta z metody Machine Learning operacji (MLOps), co zwiększa jakość i spójność Twojego rozwiązania do uczenia maszynowego. 

Usługa Machine Learning zapewnia następujące możliwości MLOps:

- **Wdrażanie projektów uczenia Maszynowego z dowolnego miejsca**
- **Monitorowanie aplikacji uczenia Maszynowego dla operacyjnej i problemów związanych z uczenia Maszynowego** — porównywania wejść modelu między szkoleń i wnioskowania, eksplorowanie metryk konkretnego modelu i zapewniają monitorowanie i alerty w ramach swojej infrastruktury uczenia Maszynowego.
- **Przechwytywanie danych wymagane do ustanowienia dziennik inspekcji typu end to end w cyklu życia uczenia Maszynowego**, w tym kto publikuje modele, dlaczego jest zmieniana i kiedy modele zostały wdrożone lub używane w środowisku produkcyjnym.
- **Automatyzuj cykl życia uczenia Maszynowego typu end to end za pomocą usługi Azure Machine Learning i Azure DevOps** często aktualizacji modeli, testowanie nowych modeli i ciągłe wdrażanie nowych modeli uczenia Maszynowego, wraz z innymi aplikacjami i usług.

Aby Dowiedz się więcej na pojęć dotyczących MLOps i jak odnoszą się do usługi Azure Machine Learning, obejrzyj poniższy film wideo.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Wdrażanie projektów uczenia Maszynowego z dowolnego miejsca

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Włącz proces szkolenia potokiem do odtworzenia
Użyj potokach uczenia Maszynowego z usługi Azure Machine Learning, aby łączyć wszystkie kroki związane z modelu procesu uczenia, od przygotowania danych do wyodrębniania funkcji do hiperparametrycznego dostrajania do modelu w wersji ewaluacyjnej.

Aby uzyskać więcej informacji, zobacz [potokach uczenia Maszynowego](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Rejestrowanie i śledzenie modeli uczenia Maszynowego

Rejestracja modelu pozwala do przechowywania wersji modeli w chmurze platformy Azure, w obszarze roboczym. Rejestru model ułatwia organizowanie i śledzenie wytrenowane modele.

> [!TIP]
> Zarejestrowanego modelu to logiczny kontener dla jednego lub więcej plików, które tworzą model. Na przykład jeśli model, który jest przechowywany w wielu plikach, można zarejestrować je jako pojedynczego modelu w Twoim obszarze roboczym usługi Azure Machine Learning. Po rejestracji można, a następnie pobrać lub zarejestrowanego modelu wdrażania i otrzymywać wszystkie pliki, które zostały zarejestrowane.
 
Zarejestrowane modele są identyfikowane przez nazwę i wersję. Zawsze należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru zwiększa numer wersji. Możesz także podać dodatkowe metadane tagów podczas rejestracji, który może służyć podczas wyszukiwania dla modeli. Usługa Azure Machine Learning obsługuje każdy model, który może być załadowany, przy użyciu języka Python 3.5.2 lub nowszej.

> [!TIP]
> Można również zarejestrować modeli skonfigurowanych pod kątem spoza usługi Azure Machine Learning.

Nie można usunąć zarejestrowanego modelu, który jest używany w aktywnym wdrożeniu.
Aby uzyskać więcej informacji, zobacz sekcję modelu rejestru [wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modele pakietu i debugowania

Przed wdrożeniem modelu w środowisku produkcyjnym należy go znajduje się w pakiecie obrazu platformy Docker. W większości przypadków tworzenia obrazów odbywa się automatycznie w tle podczas wdrażania. W przypadku zaawansowanych scenariuszy można ręcznie określić obrazu.

Jeśli napotkasz problemy z wdrożeniem, można wdrożyć na lokalne Środowisko deweloperskie do debugowania i rozwiązywania problemów.

Aby uzyskać więcej informacji, zobacz [wdrażanie modeli](how-to-deploy-and-where.md#registermodel) i [Rozwiązywanie problemów z wdrożeniami](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Sprawdzanie poprawności i profilowania modeli

Usługa Azure Machine Learning umożliwia profilowanie Określ ustawienia Procesora i pamięci idealne do użycia podczas wdrażania modelu. Sprawdzanie poprawności modelu odbywa się w ramach tego procesu, korzystanie z danych, które podasz w procesie profilowania.

### <a name="convert-and-optimize-models"></a>Konwertowanie i optymalizowanie modeli

Konwertowanie przez model [Otwórz Exchange sieci neuronowych](https://onnx.ai) (ONNX) może zwiększyć wydajność. Średnio konwertowanie ONNX może przynieść 2 x wzrost wydajności.

Aby uzyskać więcej informacji na temat ONNX za pomocą usługi Azure Machine Learning, zobacz [Utwórz i przyspieszyć modeli uczenia Maszynowego](concept-onnx.md) artykułu.

### <a name="use-models"></a>Używanie modeli

Wytrenowane modele uczenia maszynowego można wdrożyć jako usługi sieci web w chmurze lub lokalnie w środowisku deweloperskim. Można także wdrożyć modele na urządzeniach z usługą Azure IoT Edge. Wdrożeń można użyć procesor CPU, procesor GPU lub tablic programowalny bramy (FPGA) do wnioskowania. Można również użyć modeli z usługi Power BI.

Korzystając z modelu jako usługi sieci web lub urządzenie usługi IoT Edge, zapewnia się następujące elementy:

* Modele, które są używane do oceniać dane przesyłane do usługi i/lub urządzeń.
* Skrypt wejścia. Ten skrypt akceptuje żądania, używa modele, aby oceniać dane i zwracają odpowiedzi.
* Plik środowiska conda, który zawiera opis zależności wymagane przez skrypt modele i zapis.
* Wszelkie dodatkowe zasoby takich jak tekst, data itp., które są wymagane przez skrypt modele i zapis.

Te zasoby są zapakowane do obrazu platformy Docker i wdrażane jako usługi sieci web lub moduł usługi IoT Edge.

Opcjonalnie służy następujące parametry do dalsze dostosowywanie wdrożenia:

* Włącz procesora GPU: Używane, aby włączyć obsługę procesora GPU na obrazie platformy Docker. Obraz musi być używana w Microsoft Azure Services, takich jak usługi Azure Container Instances, Azure Kubernetes Service, obliczeniowego usługi Azure Machine Learning lub maszynach wirtualnych platformy Azure.
* Docker dodatkowe kroki pliku: Plik, który zawiera dodatkowe kroki platformy Docker, aby uruchomić podczas tworzenia obrazu platformy Docker.
* Obraz podstawowy: Obraz niestandardowy do użycia jako obraz podstawowy. Jeśli używasz niestandardowego obrazu, obraz podstawowy jest udostępniane przez usługę Azure Machine Learning.

Możesz również podać konfiguracji platformę docelową wdrożenia. Na przykład maszyna wirtualna typu rodziny, dostępnej pamięci i liczby rdzeni w przypadku wdrażania usługi Azure Kubernetes Service.

Po utworzeniu obrazu, dodawane są również składniki wymagane dla usługi Azure Machine Learning. Na przykład zasoby wymagane do uruchamiania usługi sieci web i wchodzić w interakcje z usługą IoT Edge.

> [!NOTE]
> Nie można modyfikować ani zmieniać serwera sieci web lub składniki usługi IoT Edge, które są używane w obrazie platformy Docker. Usługa Azure Machine Learning korzysta z konfiguracji serwera sieci web i składniki usługi IoT Edge, które są przetestowane i obsługiwane przez firmę Microsoft.

#### <a name="web-service"></a>Usługa sieci Web

Można użyć modeli w **usług sieci web** celów obliczeń następującym kodem:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service
* Lokalne Środowisko deweloperskie

Aby wdrożyć model jako usługę sieci web, należy podać następujące elementy:

* Model lub zespole modeli.
* Zależności wymagane do używania tego modelu. Na przykład skrypt, który akceptuje żądania i wywołuje model, zależności conda, itd.
* Konfiguracja wdrożenia, który opisuje, jak i gdzie do wdrażania modelu.

Aby uzyskać więcej informacji, zobacz [wdrażanie modeli](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Urządzenia usługi IoT Edge

Za pomocą modeli urządzeń IoT za pomocą **moduły usługi Azure IoT Edge**. Moduły usługi IoT Edge, które są wdrażane urządzenia sprzętowego, co umożliwia wnioskowania lub modelu oceniania na urządzeniu.

Aby uzyskać więcej informacji, zobacz [wdrażanie modeli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analiza

Microsoft Power BI obsługuje przy użyciu modeli uczenia maszynowego, analizy danych. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure Machine Learning w usłudze Power BI (wersja zapoznawcza)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Monitorowanie aplikacji uczenia Maszynowego dla operacyjnej i problemów związanych z uczenia Maszynowego

Monitorowanie umożliwia Ci zrozumieć, jakie dane są wysyłane do modelu i prognoz, które zwraca.

Te informacje pomagają zrozumieć, jak jest używany model. Zebrane dane wejściowe mogą być też przydatne w przyszłych wersjach szkolenie modelu.

Aby uzyskać więcej informacji, zobacz [jak włączyć zbieranie danych modelu](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Przechwytywanie dziennika inspekcji typu end to end cyklu życia uczenia Maszynowego

Uczenie Maszynowe systemu Azure zapewnia możliwość śledzenia dziennika inspekcji typu end to end wszystkie Twoje zasoby uczenia Maszynowego. W szczególności:

- Uczenie Maszynowe systemu Azure integruje się z usługi Git w celu śledzenia informacji, które repozytorium / gałąź / zatwierdzanie pochodzą z kodu ze.
- Usługa Azure ML zestawów danych pomagają śledzić i wersji danych.
- Historia przebiegów uczenie Maszynowe Azure zarządza kod, dane i moc obliczeniowa, używane do trenowania modelu.
- Rejestr modelu uczenia Maszynowego Azure przechwytuje wszystkie metadane skojarzone z modelu (które eksperymentu skonfigurowanych pod kątem go, w którym jest wdrażany, jeśli jej wdrożenia są w dobrej kondycji).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatyzuj cykl życia uczenia Maszynowego typu end to end 

GitHub i potoków usługi Azure można użyć do utworzenia procesu ciągłej integracji, który przygotowuje modelu. W typowym scenariuszu po analitykiem danych sprawdza zmiany do repozytorium Git dla projektu, potok usługi Azure zostanie uruchomiony przebieg szkolenia. Wyniki przebiegu można następnie można przeprowadzić inspekcji, aby wyświetlić właściwości działania uczonego modelu. Można również utworzyć potok, który służy do wdrażania modelu w postaci usługi sieci web.

[Rozszerzenia usługi Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) sprawia, że łatwiej jest pracować z potokiem, platformy Azure. Potoki usługi Azure zapewnia następujące ulepszenia:

* Umożliwia wybór obszaru roboczego podczas definiowania połączenia z usługą.
* Włącza wersji uruchamianie potoków przez wytrenowane modele utworzone w potoku szkolenia.

Aby uzyskać więcej informacji na temat potoków Azure przy użyciu usługi Azure Machine Learning, zobacz [ciągłej integracji i wdrażania modeli uczenia Maszynowego przy użyciu potoków Azure](/azure/devops/pipelines/targets/azure-machine-learning) artykułu i [Azure Machine Learning Service MLOps](https://aka.ms/mlops) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md) za pomocą usługi Azure Machine Learning. Na przykład wdrażania zobacz [samouczka: Wdrażanie modeli klasyfikacji obrazów w usłudze Azure Container Instances](tutorial-deploy-models-with-aml.md).

Dowiedz się, jak utworzyć [ciągłej integracji i wdrażania modeli uczenia Maszynowego przy użyciu potoków Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Dowiedz się, jak tworzyć klienckie aplikacje i usługi, których [korzystanie z modelu wdrożyć jako usługę sieci web](how-to-consume-web-service.md).
