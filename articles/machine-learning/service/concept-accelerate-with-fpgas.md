---
title: Co to jest FPGA? — Project Brainwave — usługi Azure Machine Learning
description: Dowiedz się, jak przyspieszyć tworzenie modeli i głębokich sieciach neuronowych za pomocą układów FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644698"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Co to jest FPGA i Project Brainwave?

Ten artykuł zawiera wprowadzenie do macierzy programowalny bramy (FPGA) i jak FPGA jest zintegrowana z usługą aplikacji Azure machine learning w celu zapewnienia sztucznej Inteligencji w czasie rzeczywistym.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Vs układów FPGA. Procesor CPU, procesora GPU i ASIC

Układów FPGA zawiera tablicę bloki programowalny i hierarchię możliwych do ponownego skonfigurowania typu, dzięki czemu bloki, które należy skonfigurować na różne sposoby po produkcji.

Układów FPGA zawierają kombinację programowania i wydajności porównanie z innymi mikroukładami:

![Porównanie usług Azure Machine Learning FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Jednostek centralnych (CPU)** procesorów ogólnego przeznaczenia. Wydajność procesora CPU nie jest idealny dla grafiki i przetwarzania plików wideo.
- **Graficznych (GPU)** oferują możliwości równoległego przetwarzania i popularnych nadają się do obliczeń sztucznej Inteligencji. Równoległe, przetwarzanie z wynikiem szybsze renderowanie obrazu niż procesorów GPU.
- **Scalonych specyficzne dla aplikacji (ASIC)**, takich jak Google TensorFlow procesora, jednostki są dostosowane obwodów. Te moduły zapewnić najwyższą wydajność, ASICs są sztywnymi.
- **Układów FPGA**, takich jak te dostępne na platformie Azure, zapewniają wydajność blisko ASIC, ale oferują elastyczność ponownego później.

Układów FPGA są teraz każdy nowy serwer platformy Azure. Firmy Microsoft używa już układów FPGA dla arizony neuronowej sieci (DNN) wersji ewaluacyjnej wyszukiwania Bing i oprogramowania zdefiniowane sieci (komputerowej SDN) przyspieszenia. Tych implementacji FPGA zmniejszyć opóźnienie podczas zwalniania procesorów CPU w przypadku innych zadań.

## <a name="project-brainwave-on-azure"></a>Project Brainwave na platformie Azure

Project Brainwave jest architekturą sprzętową, której celem jest oparty na urządzeniach FPGA firmy Intel i używane w celu przyspieszenia w czasie rzeczywistym obliczeń sztucznej Inteligencji. Dzięki tej architekturze ekonomiczne włączone FPGA przeszkolonych sieci neuronowych może działać w możliwie jak najszybciej i mniejsze opóźnienie. Project Brainwave zrównoleglić wstępnie przeszkolonych sieci różnych układów FPGA skalowania w poziomie usługi.

- Wydajność

    Układów FPGA umożliwiają uzyskanie małych opóźnień dla żądań wnioskowania w czasie rzeczywistym. Przetwarzanie wsadowe oznacza podzielenie żądania na mniejsze kawałki i wprowadzając je do procesora, aby poprawić wykorzystanie sprzętu. Przetwarzanie wsadowe jest wydajny i może powodować opóźnienia. Brainwave nie wymaga dzielenia na partie; w związku z tym opóźnienie jest 10 razy niższa w porównaniu do GPU i CPU.

- Elastyczność

    Układów FPGA można skonfigurować dla różnych typów modeli uczenia maszynowego. Ta elastyczność ułatwia przyspieszenie aplikacji na podstawie najbardziej optymalną precyzja wartości liczbowych i model pamięci używane.

    Nowe techniki uczenia maszyny są opracowywane w regularnych odstępach czasu, a projekt sprzętu Project Brainwave również zmieniających się szybko. Ponieważ układów FPGA możliwych do ponownego skonfigurowania, jest to możliwe, aby być na bieżąco z wymaganiami gwałtownie zmieniającym algorytmów sztucznej Inteligencji.

- Skalowanie

    Platforma Microsoft Azure to na świecie największych chmury zwrot z inwestycji w układów FPGA. Możesz uruchomić Brainwave w infrastrukturze skalowania platformy Azure.

Podgląd Project Brainwave zintegrowana z usługą Azure Machine Learning jest obecnie dostępna. Ograniczona wersja zapoznawcza dostępna jest również i Project Brainwave na urządzeniach brzegowych można korzystać z zalet obliczeń prędkości we własnych firm i urządzeń.

W bieżącej wersji zapoznawczej Brainwave jest ograniczona do wdrażania TensorFlow i na podstawie ResNet50 sieci neuronowych na sprzęcie FPGA firmy Intel, Klasyfikacja obrazów i rozpoznawania. Istnieją plany pomocy technicznej więcej modeli galerii i innych platform.

Następujące scenariusze użycia FPGA w architekturze Project Brainwave:

- Zautomatyzowany system optyczne inspekcji. Zobacz [sztucznej Inteligencji w czasie rzeczywistym: Microsoft zapowiada Podgląd Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Mapowanie cover lądzie. Zobacz [jak wykonać Cover ziemi mapowanie pod kątem terabajtów obrazów z powietrza za pomocą układów FPGA do uczenia głębokiego wnioskowania](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Jak wdrożyć usługę sieci web FPGA?

Ogólny przepływ do tworzenia usługi rozpoznawania obrazów na platformie Azure przy użyciu ResNet50 featurized jest następująca:

1. ResNet50 jest już wdrożony w Brainwave. Tworzenie innych wykresów (dane wejściowe daty, klasyfikacji i tak dalej) z TensorFlow i definiuje się potok (wejście -> cechowanie -> klasyfikowania) przy użyciu pliku json definicji usługi. Skompresuj wykresów i definicji do pliku zip i przekaż plik zip do usługi Azure Blob storage.
2. Zarejestruj model przy użyciu interfejsu API zarządzania modelu uczenia Maszynowego Azure przy użyciu pliku zip w usłudze Blob storage.
3. Wdrażanie usługi przy użyciu zarejestrowanego modelu przy użyciu interfejsu API zarządzania modelu uczenia Maszynowego Azure.

Dowiedz się więcej na temat tego procesu w artykule [wdrożyć model jako usługę sieci web na FPGA za pomocą usługi Azure Machine Learning](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Rozpoczynanie korzystania z FPGA

Azure modeli uczenia maszynowego sprzętu Accelerated umożliwiają wdrażanie przeszkolone modele DNN do układów FPGA w chmurze platformy Azure. Aby rozpocząć pracę, zobacz:

- [Wdrażanie modelu jako usługi sieci web na FPGA](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning sprzętu Accelerated modeli działającemu Project Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie modelu jako usługi sieci web na FPGA](how-to-deploy-fpga-web-service.md)

[Dowiedz się, jak zainstalować zestaw SDK FPGA](reference-fpga-package-overview.md)

[Sprzętu w Hiperskali: uczenie Maszynowe na dużą skalę na platformie Azure i FPGA: Build 2018 (wideo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[W firmie Microsoft na układach FPGA konfigurowalnej chmury (wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft przedstawia Project Brainwave dla sztucznej Inteligencji w czasie rzeczywistym](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)