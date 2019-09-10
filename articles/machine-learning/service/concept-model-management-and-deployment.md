---
title: 'MLOps: Zarządzaj, wdrażaj i & Monitoruj modele ML'
titleSuffix: Azure Machine Learning service
description: 'Dowiedz się, jak używać usługi Azure Machine Learning Service for MLOps: wdrażanie i monitorowanie modeli oraz zarządzanie nimi w celu ciągłego ulepszania ich. Można wdrażać te modele, które uczony przy użyciu usługi Azure Machine Learning, na komputerze lokalnym lub z innych źródeł.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 2f3c3532637bef041ad1983b7573837dd0f29211
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860615"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning

W tym artykule dowiesz się, jak za pomocą usługi Azure Machine Learning zarządzać cyklem życia modeli. Azure Machine Learning używa podejścia Machine Learning Operations (MLOps), które poprawia jakość i spójność rozwiązań uczenia maszynowego. 

Usługa Azure Machine Learning zapewnia następujące możliwości MLOps:

- **Wdróż projekty ML z dowolnego miejsca**
- **Monitoruj aplikacje ml pod kątem problemów związanych z działaniem i ml** — Porównaj dane wejściowe modelu między szkoleniem i wnioskami, zbadaj metryki specyficzne dla modelu i zapewnij monitorowanie i alerty w infrastrukturze usługi ml.
- **Przechwyć dane wymagane do ustanowienia kompleksowego dziennika inspekcji cyklu życia**, w tym kto publikuje modele, dlaczego są wprowadzane zmiany i kiedy modele zostały wdrożone lub użyte w środowisku produkcyjnym.
- **Automatyzuj kompleksowe cykle życia ml z użyciem Azure Machine Learning i platformy Azure DevOps** , aby często aktualizować modele, testować nowe modele i ciągle przebiegać nowe modele ml wraz z innymi aplikacjami i usługami.

Aby dowiedzieć się więcej na temat koncepcji związanych z MLOps i sposobami ich zastosowania do usługi Azure Machine Learning, Obejrzyj poniższe wideo.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Wdróż projekty ML z dowolnego miejsca

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Przekształcanie procesu szkolenia w potok powtarzalny
Użyj potoków ML z Azure Machine Learning, aby połączyć wszystkie kroki związane z procesem szkolenia modelu, od przygotowania danych do ekstrakcji funkcji do dostrajania parametrów do oceny modelu.

Aby uzyskać więcej informacji, zobacz temat [potoki ml](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Rejestrowanie i śledzenie modeli ML

Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w chmurze platformy Azure w obszarze roboczym. Rejestr modelu ułatwia organizowanie i śledzenie przeszkolonych modeli.

> [!TIP]
> Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym Azure Machine Learning. Po zarejestrowaniu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.
 
Zarejestrowane modele są identyfikowane przez nazwę i wersję. Zawsze należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru zwiększa numer wersji. Możesz także podać dodatkowe metadane tagów podczas rejestracji, który może służyć podczas wyszukiwania dla modeli. Usługa Azure Machine Learning obsługuje dowolny model, który można załadować przy użyciu języka Python 3.5.2 lub nowszego.

> [!TIP]
> Możesz również rejestrować modele przeszkolone poza usługą Azure Machine Learning.

Nie można usunąć zarejestrowanego modelu, który jest używany w aktywnym wdrożeniu.
Aby uzyskać więcej informacji, zobacz sekcję Register model w artykule [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modele pakietów i debugowania

Przed wdrożeniem modelu w środowisku produkcyjnym jest on spakowany w obrazie platformy Docker. W większości przypadków tworzenie obrazów odbywa się automatycznie w tle podczas wdrażania. W przypadku zaawansowanych scenariuszy można ręcznie określić obraz.

Jeśli wystąpią problemy z wdrożeniem, można wdrożyć program w lokalnym środowisku programistycznym w celu rozwiązywania problemów i debugowania.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel) i [Rozwiązywanie problemów z wdrożeniami](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Weryfikowanie i profilowanie modeli

Usługa Azure Machine Learning może użyć profilowania, aby określić idealne ustawienia procesora CPU i pamięci, które mają być używane podczas wdrażania modelu. Sprawdzanie poprawności modelu odbywa się w ramach tego procesu przy użyciu danych dostarczanych przez proces profilowania.

### <a name="convert-and-optimize-models"></a>Konwertowanie i optymalizowanie modeli

Przekonwertowanie modelu na [otwarty program neuronowych Network Exchange](https://onnx.ai) (ONNX) może poprawić wydajność. Na przykład przekonwertowanie na ONNX może spowodować zwiększenie wydajności.

Aby uzyskać więcej informacji na temat ONNX z usługą Azure Machine Learning, zobacz artykuł [Tworzenie i przyspieszenie modeli ml](concept-onnx.md) .

### <a name="use-models"></a>Używaj modeli

Przeszkolone modele uczenia maszynowego można wdrożyć jako usługi sieci Web w chmurze lub lokalnie w środowisku deweloperskim. Możesz również wdrażać modele na Azure IoT Edge urządzeniach. W przypadku wdrożeń można używać macierzy opartych na procesorach CPU, procesorach GPU lub programowalnych polach (FPGA) dla inferencing. Można również używać modeli z Power BI.

W przypadku korzystania z modelu jako usługi sieci Web lub IoT Edge urządzenia należy udostępnić następujące elementy:

* Modele, które są używane do oceny danych przesyłanych do usługi/urządzenia.
* Skrypt wejściowy. Ten skrypt akceptuje żądania, używa modeli do oceny danych i zwracają odpowiedź.
* Plik środowiska Conda, który opisuje zależności wymagane przez model (y) i skrypt wejścia.
* Wszelkie dodatkowe zasoby, takie jak tekst, dane itp., które są wymagane przez model (y) i skrypt wejścia.

Te zasoby są spakowane w obrazie platformy Docker i wdrażane jako usługa sieci Web lub moduł IoT Edge.

Opcjonalnie można użyć następujących parametrów, aby dodatkowo dostroić wdrożenie:

* Włącz procesor GPU: Służy do włączania obsługi procesora GPU w obrazie platformy Docker. Obraz musi być używany w usługach Microsoft Azure, takich jak Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning COMPUTE lub Azure Virtual Machines.
* Dodatkowe kroki pliku platformy Docker: Plik zawierający dodatkowe kroki platformy Docker do uruchomienia podczas tworzenia obrazu platformy Docker.
* Obraz podstawowy: Obraz niestandardowy, który będzie używany jako obraz podstawowy. Jeśli nie używasz obrazu niestandardowego, podstawowy obraz jest dostarczany przez usługę Azure Machine Learning.

Należy również podać konfigurację docelowej platformy wdrożenia. Na przykład typ rodziny maszyn wirtualnych, dostępna pamięć i liczba rdzeni podczas wdrażania w usłudze Azure Kubernetes Service.

Po utworzeniu obrazu są również dodawane składniki wymagane przez usługę Azure Machine Learning. Na przykład zasoby, które są konieczne do uruchomienia usługi sieci Web i współdziałają z IoT Edge.

> [!NOTE]
> Nie można modyfikować ani zmieniać serwera sieci Web ani składników IoT Edge używanych w obrazie platformy Docker. Usługa Azure Machine Learning używa konfiguracji serwera sieci Web i składników IoT Edge, które są testowane i obsługiwane przez firmę Microsoft.

#### <a name="web-service"></a>Usługa sieci Web

Można używać modeli w **usługach sieci Web** z następującymi obiektami docelowymi obliczeń:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service
* Lokalne środowisko programistyczne

Aby wdrożyć model jako usługę sieci Web, należy podać następujące elementy:

* Model lub kompletność modeli.
* Zależności wymagane do korzystania z modelu. Na przykład skrypt, który akceptuje żądania i wywołuje model, Conda zależności itp.
* Konfiguracja wdrożenia opisująca sposób i miejsce wdrożenia modelu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Urządzenia usługi IoT Edge

Można używać modeli z urządzeniami IoT za pomocą **modułów Azure IoT Edge**. Moduły IoT Edge są wdrażane na urządzeniu sprzętowym, co umożliwia wnioskowanie lub ocenianie modelu na urządzeniu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analiza

Firma Microsoft Power BI obsługuje używanie modeli uczenia maszynowego na potrzeby analizy danych. Aby uzyskać więcej informacji, zobacz [Azure Machine Learning Integration w Power BI (wersja zapoznawcza)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Monitoruj aplikacje ML pod kątem problemów związanych z działaniem i ML

Monitorowanie pozwala zrozumieć, jakie dane są wysyłane do modelu, oraz przewidywane, które zwraca.

Te informacje ułatwiają zrozumienie sposobu korzystania z modelu. Zebrane dane wejściowe mogą być również przydatne w przypadku szkoleń w przyszłych wersjach modelu.

Aby uzyskać więcej informacji, zobacz [jak włączyć zbieranie danych modelu](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Przechwyć kompleksowy Dziennik audytu cyklu życia w usłudze ML

Usługa Azure ML oferuje możliwość śledzenia kompleksowego dziennika inspekcji wszystkich zasobów w sieci. W szczególności:

- Platforma Azure ML [integruje się z usługą git](how-to-set-up-training-targets.md#gitintegration) , aby śledzić informacje o repozytorium/rozgałęzieniu/Zatwierdź swój kod.
- [Zestawy danych usługi Azure ml](how-to-create-register-datasets.md) ułatwiają śledzenie i przechowywanie wersji.
- Historia przebiegów Azure ML przechowuje migawkę kodu, danych i obliczeń używanych do uczenia modelu.
- Rejestr modelu usługi Azure ML przechwytuje wszystkie metadane skojarzone z Twoim modelem (który eksperyment został przeszkolony, gdzie jest wdrażany, jeśli jego wdrożenia są w dobrej kondycji).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatyzowanie cyklu życia do końca ML 

Korzystając z usługi GitHub i Azure Pipelines, można utworzyć proces ciągłej integracji, który pociągnie za model. W typowym scenariuszu, gdy analityk danych sprawdzi zmianę w repozytorium git dla projektu, potok platformy Azure rozpocznie działanie szkoleniowe. Wyniki przebiegu można następnie sprawdzić, aby zobaczyć charakterystykę wydajności nauczonego modelu. Możesz również utworzyć potok, który wdraża model jako usługę sieci Web.

[Rozszerzenie Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) ułatwia pracę z Azure Pipelines. Zapewnia następujące udoskonalenia Azure Pipelines:

* Włącza wybór obszaru roboczego podczas definiowania połączenia z usługą.
* Umożliwia wyzwalanie potoków wydania przez nauczone modele utworzone w potoku szkoleniowym.

Aby uzyskać więcej informacji na temat korzystania z Azure Pipelines z Azure Machine Learning, zobacz temat [ciągła integracja i wdrażanie modeli ml z Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) artykułem oraz repozytorium [MLOps usługi Azure Machine Learning Service](https://aka.ms/mlops) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md) za pomocą usługi Azure Machine Learning. Aby zapoznać się z przykładem wdrożenia [, zobacz Samouczek: Wdróż model klasyfikacji obrazów w Azure Container Instances](tutorial-deploy-models-with-aml.md).

Dowiedz się, jak utworzyć [ciągłą integrację i wdrażanie modeli ml przy użyciu Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Dowiedz się, jak tworzyć aplikacje klienckie i usługi korzystające [z modelu wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).
