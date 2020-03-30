---
title: 'MLOps: Zarządzanie modelami ML'
titleSuffix: Azure Machine Learning
description: 'Dowiedz się więcej o zarządzaniu modelami za pomocą usługi Azure Machine Learning (MLOps). Wdrażaj, zarządzaj i monitoruj modele, aby stale je ulepszać. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 40e335ee0377c560b15a588269cbdb39cdebca82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477361"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MlOps: Zarządzanie modelami, wdrażanie i monitorowanie za pomocą usługi Azure Machine Learning

W tym artykule dowiesz się, jak używać usługi Azure Machine Learning do zarządzania cyklem życia modeli. Usługa Azure Machine Learning używa podejścia operacji uczenia maszynowego (MLOps). MlOps poprawia jakość i spójność rozwiązań uczenia maszynowego. 

## <a name="what-is-mlops"></a>Co to jest MLOps?

Operacje uczenia maszynowego (MLOps) opiera się na zasadach i [praktykach DevOps,](https://azure.microsoft.com/overview/what-is-devops/) które zwiększają wydajność przepływów pracy. Na przykład ciągła integracja, dostarczanie i wdrażanie. MlOps stosuje te podmioty do procesu uczenia maszynowego, w celu:

* Szybsze eksperymentowanie i rozwój modeli
* Szybsze wdrażanie modeli w produkcji
* Kontrola jakości

Usługa Azure Machine Learning udostępnia następujące funkcje uczenia maszynowego MLOps:

- **Tworzenie powtarzalnych potoków ml**. Potoki uczenia maszynowego umożliwiają definiowanie powtarzalnych i wielokrotnego użytku kroków do przygotowywania danych, szkolenia i oceniania procesów.
- **Twórz środowiska oprogramowania wielokrotnego użytku** do szkolenia i wdrażania modeli.
- **Rejestrowanie, pakowanie i wdrażanie modeli z dowolnego miejsca.** Można również śledzić skojarzone metadane wymagane do korzystania z modelu.
- **Przechwytywanie danych nadzoru dla cyklu życia kompleksowej ml**. Zarejestrowane informacje mogą obejmować, kto publikuje modele, dlaczego wprowadzono zmiany i kiedy modele zostały wdrożone lub używane w produkcji.
- **Powiadamianie i ostrzeganie o zdarzeniach w cyklu życia ml**. Na przykład ukończenie eksperymentu, rejestracja modelu, wdrożenie modelu i wykrywanie dryfu danych.
- **Monitorowanie aplikacji ML pod kątem problemów operacyjnych i związanych z ml**. Porównaj dane wejściowe modelu między szkolenia i wnioskowania, eksploruj metryki specyficzne dla modelu i zapewnić monitorowanie i alerty w infrastrukturze ml.
- **Zautomatyzuj kompleksowy cykl życia uczenia maszynowego za pomocą usługi Azure Machine Learning i potoków platformy Azure.** Za pomocą potoków umożliwia często aktualizować modele, testować nowe modele i stale wdrażać nowe modele uczenia maszynowego wraz z innymi aplikacjami i usługami.

## <a name="create-reproducible-ml-pipelines"></a>Tworzenie powtarzalnych potoków ml

Użyj potoków uczenia maszynowego platformy Azure, aby połączyć ze sobą wszystkie kroki związane z procesem szkolenia modelu.

Potok ml może zawierać kroki od przygotowania danych do wyodrębniania funkcji do dostrajania hiperparametryczne do oceny modelu. Aby uzyskać więcej informacji, zobacz [potoki ml](concept-ml-pipelines.md).

Jeśli używasz [projektanta](concept-designer.md) do tworzenia potoków ml, możesz w dowolnym momencie kliknąć **"..."** w prawym górnym rogu strony Projektanta, a następnie wybrać opcję **Klonuj**. Klonowanie potoku umożliwia iterację projektu potoku bez utraty starych wersji.  

## <a name="create-reusable-software-environments"></a>Tworzenie środowisk oprogramowania wielokrotnego użytkowania

Środowiska usługi Azure Machine Learning umożliwiają śledzenie i odtwarzanie zależności oprogramowania projektów w miarę ich rozwoju. Środowiska umożliwiają zapewnienie, że kompilacje są powtarzalne bez ręcznych konfiguracji oprogramowania.

Środowiska opisują zależności pip i Conda dla projektów i mogą być używane zarówno do szkolenia, jak i wdrażania modeli. Aby uzyskać więcej informacji, zobacz [Co to są środowiska usługi Azure Machine Learning](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Rejestrowanie, pakowanie i wdrażanie modeli z dowolnego miejsca

### <a name="register-and-track-ml-models"></a>Rejestrowanie i śledzenie modeli ml

Rejestracja modelu umożliwia przechowywanie i przechowywanie wersji modeli w chmurze platformy Azure w obszarze roboczym. Rejestr modeli ułatwia organizowanie i śledzenie wyszkolonych modeli.

> [!TIP]
> Zarejestrowany model jest kontenerem logicznym dla jednego lub więcej plików, które tworzą model. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, można zarejestrować je jako pojedynczy model w obszarze roboczym usługi Azure Machine Learning. Po rejestracji można pobrać lub wdrożyć zarejestrowany model i otrzymać wszystkie zarejestrowane pliki.

Zarejestrowane modele są identyfikowane za pomocą nazwy i wersji. Za każdym razem, gdy rejestrujesz model o takiej samej nazwie, jaką ma już istniejący model, rejestr zwiększa wersję. Podczas rejestracji można podać dodatkowe tagi metadanych. Tagi te są następnie używane podczas wyszukiwania modelu. Usługa Azure Machine Learning obsługuje dowolny model, który można załadować przy użyciu języka Python 3.5.2 lub nowszego.

> [!TIP]
> Można również zarejestrować modele przeszkolone poza usługą Azure Machine Learning.

Nie można usunąć zarejestrowanego modelu, który jest używany w aktywnym wdrożeniu.
Aby uzyskać więcej informacji, zobacz sekcję modelu rejestru [w deploy models](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Modele profili

Usługa Azure Machine Learning może pomóc w zrozumieniu wymagań procesora CPU i pamięci usługi, które zostaną utworzone podczas wdrażania modelu. Profilowanie testuje usługę, która uruchamia model i zwraca informacje, takie jak użycie procesora CPU, użycie pamięci i opóźnienie odpowiedzi. Zapewnia również zalecenie procesora CPU i pamięci na podstawie użycia zasobów.
Aby uzyskać więcej informacji, zobacz sekcję profilowania [modeli wdrażania](how-to-deploy-and-where.md#profilemodel).

### <a name="package-and-debug-models"></a>Modele pakietów i debugowania

Przed wdrożeniem modelu w produkcji, jest on pakowany do obrazu platformy Docker. W większości przypadków tworzenie obrazu odbywa się automatycznie w tle podczas wdrażania. Obraz można określić ręcznie.

Jeśli napotkasz problemy z wdrożeniem, można wdrożyć w lokalnym środowisku programistycznym do rozwiązywania problemów i debugowania.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel) i [rozwiązywanie problemów z wdrożeniami](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Konwertowanie i optymalizacja modeli

Konwersja modelu na [open neural network exchange](https://onnx.ai) (ONNX) może poprawić wydajność. Średnio konwersja na ONNX może przynieść 2-krotny wzrost wydajności.

Aby uzyskać więcej informacji na temat ONNX z usługi Azure Machine Learning, zobacz [Tworzenie i przyspieszanie modeli uczenia maszynowego.](concept-onnx.md)

### <a name="use-models"></a>Używanie modeli

Wyszkolone modele uczenia maszynowego są wdrażane jako usługi sieci web w chmurze lub lokalnie. Można również wdrożyć modele na urządzeniach usługi Azure IoT Edge. Wdrożenia używają procesora CPU, GPU lub programowalnych w terenie macierzy bramek (FPGA) do wnioskowania. Można również używać modeli z usługi Power BI.

Korzystając z modelu jako usługi sieci web lub urządzenia usługi IoT Edge, należy podać następujące elementy:

* Model(y), które są używane do oceniania danych przesłanych do usługi/urządzenia.
* Skrypt wejściowy. Ten skrypt akceptuje żądania, używa modeli do oceny danych i zwraca odpowiedź.
* Środowisko usługi Azure Machine Learning, które opisuje zależności pip i Conda wymagane przez model(y) i skrypt wejścia.
* Wszelkie dodatkowe zasoby, takie jak tekst, dane itp., które są wymagane przez model(y) i skrypt wejścia.

Należy również podać konfigurację platformy wdrażania docelowego. Na przykład typ rodziny maszyn wirtualnych, dostępna pamięć i liczba rdzeni podczas wdrażania w usłudze Azure Kubernetes.

Podczas tworzenia obrazu dodawane są również składniki wymagane przez usługę Azure Machine Learning. Na przykład zasoby potrzebne do uruchomienia usługi sieci web i interakcji z usługą IoT Edge.

#### <a name="batch-scoring"></a>Ocenianie usługi Batch
Ocenianie partii jest obsługiwane za pośrednictwem potoków ml. Aby uzyskać więcej informacji, zobacz [prognozy partii dotyczące dużych zbiorów danych](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Usługi internetowe w czasie rzeczywistym

Modeli można używać w **usługach sieci web** z następującymi celami obliczeniowymi:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service
* Lokalne środowisko programistyczne

Aby wdrożyć model jako usługę sieci web, należy podać następujące elementy:

* Model lub zespół modeli.
* Zależności wymagane do korzystania z modelu. Na przykład skrypt, który akceptuje żądania i wywołuje model, zależności conda, itp.
* Konfiguracja wdrożenia, która opisuje sposób i miejsce wdrażania modelu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Urządzenia IoT Edge

Modele z urządzeniami IoT można używać za pośrednictwem **modułów usługi Azure IoT Edge.** Moduły IoT Edge są wdrażane na urządzeniu sprzętowym, co umożliwia wnioskowanie lub ocenianie modelu na urządzeniu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analiza

Usługa Microsoft Power BI obsługuje używanie modeli uczenia maszynowego do analizy danych. Aby uzyskać więcej informacji, zobacz [Integracja usługi Azure Machine Learning w usłudze Power BI (wersja zapoznawcza)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Przechwytywanie danych nadzoru wymaganych do przechwytywania kompleksowego cyklu życia ml

Usługa Azure ML zapewnia możliwość śledzenia end-to-end ścieżki inspekcji wszystkich zasobów ml. Są to:

- Usługa Azure ML [integruje się z git,](how-to-set-up-training-targets.md#gitintegration) aby śledzić informacje o tym, z którego repozytorium / oddział / zatwierdzenie / zatwierdzenie kodu pochodzi.
- [Zestawy danych usługi Azure ML](how-to-create-register-datasets.md) ułatwią śledzenie danych, profilowania i wersji. 
- Historia uruchamiania usługi Azure ML przechowuje migawkę kodu, danych i obliczeń używanych do uczenia modelu.
- Usługa Azure ML Model Registry przechwytuje wszystkie metadane skojarzone z modelem (który eksperyment uszkolił go, gdzie jest wdrażany, jeśli jego wdrożenia są w dobrej kondycji).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Powiadamianie, automatyzację i ostrzeganie o zdarzeniach w cyklu życia mt
Usługa Azure ML publikuje kluczowe zdarzenia na platformie Azure EventGrid, która może służyć do powiadamiania i automatyzacji zdarzeń w cyklu życia uczenia się. Aby uzyskać więcej informacji, zobacz [ten dokument](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitor pod kątem problemów operacyjnych & ml

Monitorowanie umożliwia zrozumienie, jakie dane są wysyłane do modelu i prognoz, które zwraca.

Te informacje pomagają zrozumieć, jak model jest używany. Zebrane dane wejściowe mogą być również przydatne w szkoleniu przyszłych wersji modelu.

Aby uzyskać więcej informacji, zobacz [Jak włączyć zbieranie danych modelu](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Przekwalifikowanie modelu na nowe dane

Często warto zaktualizować model, a nawet przeszkolić go od podstaw, ponieważ otrzymujesz nowe informacje. Czasami odbieranie nowych danych jest oczekiwaną częścią domeny. Innym razem, jak wspomniano w [detect dryf danych (podgląd) na zestawach danych,](how-to-monitor-datasets.md)wydajność modelu może ulec pogorszeniu w obliczu takich rzeczy, jak zmiany w danym czujniku, naturalne zmiany danych, takie jak efekty sezonowe lub funkcje zmieniające się w stosunku do innych funkcji. 

Nie ma uniwersalnej odpowiedzi na pytanie "Skąd mam wiedzieć, czy powinienem przekwalifikować się?" ale narzędzia do monitorowania zdarzeń i monitorowania usługi Azure ML zostały omówione wcześniej, są dobrymi punktami wyjścia dla automatyzacji. Po podjęciu decyzji o przekwalifikowaniu należy: 

- Wstępne przetwarzanie danych przy użyciu powtarzalnej, zautomatyzowanej
- Trenuj swój nowy model
- Porównaj wyniki nowego modelu z wydajnością starego modelu
- Użyj wstępnie zdefiniowanych kryteriów, aby zdecydować, czy chcesz zastąpić stary model 

Tematem powyższych kroków jest to, że przekwalifikowanie powinno być zautomatyzowane, a nie ad hoc. [Potoki usługi Azure Machine Learning](concept-ml-pipelines.md) są dobrą odpowiedzią na tworzenie przepływów pracy związanych z przygotowaniem danych, szkoleniem, sprawdzaniem poprawności i wdrażaniem. Przeczytaj [modele ponownego uczenia za pomocą projektanta usługi Azure Machine Learning (wersja zapoznawcza),](how-to-retrain-designer.md) aby zobaczyć, jak potoki i projektant usługi Azure Machine Learning pasują do scenariusza przekwalifikowania. 

## <a name="automate-the-ml-lifecycle"></a>Automatyzacja cyklu życia ml 

Za pomocą usługi GitHub i usługi Azure Pipelines można utworzyć proces ciągłej integracji, który trenuje model. W typowym scenariuszu, gdy data scientist sprawdza zmiany w repozytorium Git dla projektu, potoku platformy Azure rozpocznie się przebieg szkolenia. Wyniki przebiegu można następnie sprawdzić, aby zobaczyć charakterystykę wydajności przeszkolonego modelu. Można również utworzyć potok, który wdraża model jako usługę sieci web.

[Rozszerzenie usługi Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) ułatwia pracę z usługi Azure Pipelines. Zawiera następujące ulepszenia usługi Azure Pipelines:

* Umożliwia wybór obszaru roboczego podczas definiowania połączenia usługi.
* Umożliwia wyzwalanie potoków zwalniania przez wyszkolone modele utworzone w potoku szkoleniowym.

Aby uzyskać więcej informacji na temat korzystania z usługi Azure Pipelines z usługą Azure Machine Learning, zobacz następujące łącza:

* [Ciągła integracja i wdrażanie modeli układów ml za pomocą usługi Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Repozytorium mlops usługi Azure Machine Learning.](https://aka.ms/mlops)
* [Repozytorium MLOpsPython usługi Azure Machine Learning.](https://github.com/Microsoft/MLOpspython)

Można również użyć usługi Azure Data Factory, aby utworzyć potok pozyskiwania danych, który przygotowuje dane do użycia ze szkoleniem. Aby uzyskać więcej informacji, zobacz [Potok pozyskiwania danych](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej, czytając i eksplorując następujące zasoby:

+ [Jak & miejsca wdrażania modeli za](how-to-deploy-and-where.md) pomocą usługi Azure Machine Learning

+ [Samouczek: Wdrażanie modelu klasyfikacji obrazów w ACI](tutorial-deploy-models-with-aml.md).

+ [Kompleksowe przykłady mlops repo](https://github.com/microsoft/MLOps)

+ [Ciągła integracja/dysk CD modeli ml za pomocą potoków platformy Azure](/azure/devops/pipelines/targets/azure-machine-learning)

+ Tworzenie klientów [korzystających z wdrożonego modelu](how-to-consume-web-service.md)

+ [Uczenie maszynowe na dużą skalę](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Architektury referencyjne dotyczące sztucznej inteligencji platformy Azure & najlepszych praktyk](https://github.com/microsoft/AI)
