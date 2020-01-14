---
title: 'MLOps: Zarządzanie modelami ML'
titleSuffix: Azure Machine Learning
description: 'Informacje na temat zarządzania modelami za pomocą Azure Machine Learning (MLOps). Wdrażaj i monitoruj modele oraz zarządzaj nimi, aby stale ulepszać je. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9cde171f1c031b4b02a71fe0dac1a689e3ec0722
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772588"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Zarządzanie modelami, wdrażanie i monitorowanie za pomocą Azure Machine Learning

W tym artykule dowiesz się, jak za pomocą Azure Machine Learning zarządzać cyklem życia modeli. Azure Machine Learning używa podejścia Machine Learning Operations (MLOps). MLOps poprawia jakość i spójność rozwiązań do uczenia maszynowego. 

Azure Machine Learning zapewnia następujące możliwości MLOps:

- **Tworzenie powtarzalnych potoków ml**. Potoki umożliwiają zdefiniowanie kroków powtarzalnych i wielokrotnego użytku dla procesów przygotowywania, uczenia i oceniania danych.
- **Rejestrowanie, pakowanie i wdrażanie modeli z dowolnego miejsca** i śledzenie skojarzonych metadanych wymaganych do korzystania z modelu.
- **Przechwyć dane ładu wymagane do przechwycenia kompleksowego cyklu życia ml**, w tym kto publikuje modele, dlaczego są wprowadzane zmiany i kiedy modele zostały wdrożone lub użyte w środowisku produkcyjnym.
- **Powiadamiaj i Ostrzegaj o zdarzeniach w cyklu życia w ml** , takich jak uzupełnianie eksperymentów, rejestracja modelu, Wdrażanie modelu i wykrywanie dryfowania danych.
- **Monitoruj aplikacje ml pod kątem problemów operacyjnych i związanych z ml**. Porównaj dane wejściowe modelu między szkoleniem i wnioskami, Eksploruj metryki specyficzne dla modelu i udostępniaj funkcje monitorowania i alertów infrastruktury dotyczącej sieci.
- **Automatyzuj kompleksowe cykle życia ml z użyciem Azure Machine Learning i platformy Azure DevOps** , aby często aktualizować modele, testować nowe modele i ciągle przebiegać nowe modele ml wraz z innymi aplikacjami i usługami.

## <a name="create-reproducible-ml-pipelines"></a>Tworzenie powtarzalnych potoków ML

Użyj potoków ML z Azure Machine Learning, aby połączyć wszystkie kroki związane z procesem szkolenia modeli.

Potok ML może zawierać kroki od przygotowania danych do wyodrębniania funkcji do dostrajania parametrów do oceny modelu. Aby uzyskać więcej informacji, zobacz temat [potoki ml](concept-ml-pipelines.md).

Jeśli używasz [projektanta](concept-designer.md) do tworzenia potoków ml, możesz w dowolnym momencie kliknąć **"..."** w prawym górnym rogu strony projektanta, a następnie wybrać **klon**. Klonowanie potoku umożliwia iteracyjne projektowanie potoku bez utraty starych wersji.  

## <a name="register-package-and-deploy-models-from-anywhere"></a>Rejestrowanie, pakowanie i wdrażanie modeli z dowolnego miejsca

### <a name="register-and-track-ml-models"></a>Rejestrowanie i śledzenie modeli ML

Rejestracja modelu umożliwia przechowywanie i przechowywanie modeli w chmurze platformy Azure w obszarze roboczym. Rejestr modelu ułatwia organizowanie i śledzenie przeszkolonych modeli.

> [!TIP]
> Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym Azure Machine Learning. Po zarejestrowaniu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

Zarejestrowane modele są identyfikowane przez nazwę i wersję. Zawsze należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru zwiększa numer wersji. Dodatkowe tagi metadanych można podać podczas rejestracji. Te Tagi są następnie używane podczas wyszukiwania modelu. Azure Machine Learning obsługuje dowolny model, który można załadować przy użyciu języka Python w wersji 3.5.2 lub nowszej.

> [!TIP]
> Możesz również rejestrować modele przeszkolone poza Azure Machine Learning.

Nie można usunąć zarejestrowanego modelu, który jest używany w aktywnym wdrożeniu.
Aby uzyskać więcej informacji, zobacz sekcję Register model w artykule [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modele pakietów i debugowania

Przed wdrożeniem modelu w środowisku produkcyjnym jest on spakowany w obrazie platformy Docker. W większości przypadków tworzenie obrazów odbywa się automatycznie w tle podczas wdrażania. Można ręcznie określić obraz.

Jeśli wystąpią problemy z wdrożeniem, można wdrożyć program w lokalnym środowisku programistycznym w celu rozwiązywania problemów i debugowania.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel) i [Rozwiązywanie problemów z wdrożeniami](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Weryfikowanie i profilowanie modeli

Azure Machine Learning może użyć profilowania, aby określić idealne ustawienia procesora CPU i pamięci, które mają być używane podczas wdrażania modelu. Sprawdzanie poprawności modelu odbywa się w ramach tego procesu przy użyciu danych dostarczanych przez proces profilowania.

### <a name="convert-and-optimize-models"></a>Konwertowanie i optymalizowanie modeli

Przekonwertowanie modelu na [otwarty program neuronowych Network Exchange](https://onnx.ai) (ONNX) może poprawić wydajność. Na przykład przekonwertowanie na ONNX może spowodować zwiększenie wydajności.

Aby uzyskać więcej informacji na temat ONNX z Azure Machine Learning, zobacz artykuł [Tworzenie i przyspieszenie modeli ml](concept-onnx.md) .

### <a name="use-models"></a>Używaj modeli

Przeszkolone modele uczenia maszynowego są wdrażane jako usługi sieci Web w chmurze lub lokalnie. Możesz również wdrażać modele na Azure IoT Edge urządzeniach. Wdrożenia używają macierzy procesora CPU, procesora GPU lub programowalnych pól (FPGA) dla inferencing. Można również używać modeli z Power BI.

W przypadku korzystania z modelu jako usługi sieci Web lub IoT Edge urządzenia należy udostępnić następujące elementy:

* Modele, które są używane do oceny danych przesyłanych do usługi/urządzenia.
* Skrypt wejściowy. Ten skrypt akceptuje żądania, używa modeli do oceny danych i zwracają odpowiedź.
* Plik środowiska Conda, który opisuje zależności wymagane przez model (y) i skrypt wejścia.
* Wszelkie dodatkowe zasoby, takie jak tekst, dane itp., które są wymagane przez model (y) i skrypt wejścia.

Należy również podać konfigurację docelowej platformy wdrożenia. Na przykład typ rodziny maszyn wirtualnych, dostępna pamięć i liczba rdzeni podczas wdrażania w usłudze Azure Kubernetes Service.

Po utworzeniu obrazu są również dodawane składniki wymagane przez Azure Machine Learning. Na przykład zasoby, które są konieczne do uruchomienia usługi sieci Web i współdziałają z IoT Edge.

#### <a name="batch-scoring"></a>Ocenianie usługi Batch
Ocenianie wsadowe jest obsługiwane przy użyciu potoków ML. Aby uzyskać więcej informacji, zobacz [przewidywania wsadowe dotyczące danych Big Data](how-to-run-batch-predictions.md).

#### <a name="real-time-web-services"></a>Usługi sieci Web w czasie rzeczywistym

Można używać modeli w **usługach sieci Web** z następującymi obiektami docelowymi obliczeń:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service
* Lokalne środowisko programistyczne

Aby wdrożyć model jako usługę sieci Web, należy podać następujące elementy:

* Model lub kompletność modeli.
* Zależności wymagane do korzystania z modelu. Na przykład skrypt, który akceptuje żądania i wywołuje model, Conda zależności itp.
* Konfiguracja wdrożenia opisująca sposób i miejsce wdrożenia modelu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Urządzenia IoT Edge

Można używać modeli z urządzeniami IoT za pomocą **modułów Azure IoT Edge**. Moduły IoT Edge są wdrażane na urządzeniu sprzętowym, co umożliwia wnioskowanie lub ocenianie modelu na urządzeniu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analiza

Firma Microsoft Power BI obsługuje używanie modeli uczenia maszynowego na potrzeby analizy danych. Aby uzyskać więcej informacji, zobacz [Azure Machine Learning Integration w Power BI (wersja zapoznawcza)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Przechwyć dane ładu wymagane do przechwycenia kompleksowego cyklu życia ML

Usługa Azure ML oferuje możliwość śledzenia kompleksowego dziennika inspekcji wszystkich zasobów w sieci. Są to:

- Platforma Azure ML [integruje się z usługą git](how-to-set-up-training-targets.md#gitintegration) , aby śledzić informacje o repozytorium/rozgałęzieniu/Zatwierdź swój kod.
- [Zestawy danych usługi Azure ml](how-to-create-register-datasets.md) ułatwiają śledzenie, profilowanie i przechowywanie wersji. 
- Historia przebiegów Azure ML przechowuje migawkę kodu, danych i obliczeń używanych do uczenia modelu.
- Rejestr modelu usługi Azure ML przechwytuje wszystkie metadane skojarzone z Twoim modelem (który eksperyment został przeszkolony, gdzie jest wdrażany, jeśli jego wdrożenia są w dobrej kondycji).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Powiadamianie, Automatyzowanie i wyzwalanie alertów dotyczących zdarzeń w cyklu życia w ML
Usługa Azure ML publikuje kluczowe zdarzenia w usłudze Azure EventGrid, które mogą służyć do powiadamiania i automatyzowania zdarzeń w cyklu życia w ML. Aby uzyskać więcej informacji, zobacz [ten dokument](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitoruj problemy dotyczące operacyjnego & ML

Monitorowanie pozwala zrozumieć, jakie dane są wysyłane do modelu, oraz przewidywane, które zwraca.

Te informacje ułatwiają zrozumienie sposobu korzystania z modelu. Zebrane dane wejściowe mogą być również przydatne w przypadku szkoleń w przyszłych wersjach modelu.

Aby uzyskać więcej informacji, zobacz [jak włączyć zbieranie danych modelu](how-to-enable-data-collection.md).

## <a name="automate-the-ml-lifecycle"></a>Automatyzowanie cyklu życia ML 

Korzystając z usługi GitHub i Azure Pipelines, można utworzyć proces ciągłej integracji, który pociągnie za model. W typowym scenariuszu, gdy analityk danych sprawdzi zmianę w repozytorium git dla projektu, potok platformy Azure rozpocznie działanie szkoleniowe. Wyniki przebiegu można następnie sprawdzić, aby zobaczyć charakterystykę wydajności nauczonego modelu. Możesz również utworzyć potok, który wdraża model jako usługę sieci Web.

[Rozszerzenie Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) ułatwia pracę z Azure Pipelines. Zapewnia następujące udoskonalenia Azure Pipelines:

* Włącza wybór obszaru roboczego podczas definiowania połączenia z usługą.
* Umożliwia wyzwalanie potoków wydania przez nauczone modele utworzone w potoku szkoleniowym.

Aby uzyskać więcej informacji na temat korzystania z Azure Pipelines z Azure Machine Learning, zobacz [ciągła integracja i wdrażanie modeli ml z Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) artykułem i [Azure Machine Learning repozytorium MLOps](https://aka.ms/mlops) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, przeczytaj i zbadaj następujące zasoby:

+ [Jak & miejsce wdrożenia modeli](how-to-deploy-and-where.md) z Azure Machine Learning

+ [Samouczek: Wdrażanie modelu klasyfikacji obrazów w ACI](tutorial-deploy-models-with-aml.md).

+ [Kompleksowe repozytorium przykładów MLOps](https://github.com/microsoft/MLOps)

+ [Ciągłej integracji/ciągłego dostarczania modeli ML Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Tworzenie klientów korzystających [ze wdrożonego modelu](how-to-consume-web-service.md)

+ [Uczenie maszynowe na dużą skalę](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Architektury referencyjne platformy Azure AI & Best Practices Rep](https://github.com/microsoft/AI)
