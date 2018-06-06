---
title: Co to jest układu FPGA? Uczenie maszynowe Azure — projektu Brainwave —
description: Dowiedz się, jak przyspieszanie modeli i sieci neuronowej głębokość z FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: ace39e415bc06013008091a43d37a992a984e46b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736715"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Co to jest układu FPGA i Brainwave projektu?

Ten artykuł zawiera wprowadzenie do tablic programowalny bramy (układu FPGA) oraz jak układu FPGA jest zintegrowany z Azure machine learning zapewnienie AI w czasie rzeczywistym.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. Procesora GPU i ASIC

FPGAs zawiera tablicę programowalny bloków i hierarchię reconfigurable połączeń umożliwiająca bloków do skonfigurowania na różne sposoby po produkcji.

FPGAs zawierają kombinację programowania i porównanie wydajności na inne moduły:

![Azure Machine Learning z układu FPGA porównania](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Jednostek centralnych (CPU)** procesorów ogólnego przeznaczenia. Wydajność procesora CPU nie jest idealnym rozwiązaniem dla grafiki i wideo przetwarzania.
- **Procesorów (GPU)** oferować przetwarzanie równoległe i popularnych nadają się do obliczenia AI. Równoległe przetwarzanie z wynikiem szybsze renderowanie obrazów niż procesorach GPU.
- **Scalonych specyficzne dla aplikacji (ASIC)**, takich jak jednostki procesora TensorFlow firmy Google, są dostosowane obwodów. Te moduły zapewnić najwyższą wydajność, układy ASIC są sztywne.
- **FPGAs**, takich jak zasoby dostępne w systemie Azure, zapewniają wydajność, możliwości wykorzystania ASIC, ale oferują elastyczność zostać skonfigurowane ponownie później.

FPGAs są teraz każdy nowy serwer Azure. Microsoft już używa FPGAs dla klasyfikacji, głębokie neuronowej oceny sieci (DNN) wyszukiwania usługi Bing i acceleration (SDN) sieci definiowanej przez oprogramowanie. Tych implementacji układu FPGA zmniejszenia opóźnienia podczas zwalniania procesorów CPU w przypadku innych zadań.

## <a name="project-brainwave-on-azure"></a>Brainwave projektu na platformie Azure

Brainwave projektu jest architektury, której celem jest oparta na urządzeniach układu FPGA firmy Intel i używać w celu przyspieszenia obliczeń AI w czasie rzeczywistym. Taka architektura ekonomiczny włączone układu FPGA przeszkolone sieci neuronowej może działać w możliwie jak najszybciej i mniejsze opóźnienia. Brainwave projektu można parallelize wstępnie przeszkolone DNNs między FPGAs do skalowania w poziomie usługi.

- Wydajność

    FPGAs umożliwiają uzyskanie małych opóźnieniach żądań inferencing w czasie rzeczywistym. Przetwarzanie wsadowe oznacza rozdzielanie żądanie na mniejsze części i stosowania ich do procesora, aby poprawić wykorzystanie sprzętu. Przetwarzanie wsadowe nie jest wydajne i może powodować opóźnienia. Brainwave nie wymaga przetwarzania wsadowego; w związku z tym opóźnienie jest 10 razy niższe w porównaniu do procesora CPU i procesora GPU.

- Elastyczność

    FPGAs można skonfigurować dla różnych typów modeli uczenia maszyny. Tego rodzaju elastyczności ułatwia przyspiesza aplikacje na podstawie najbardziej optymalne dokładności wartości liczbowych i używanego modelu pamięci.

    Nowe techniki learning maszyny rozwijane są regularnie, a projekt sprzętu Brainwave projektu również zmieniających się szybko. Ponieważ FPGAs reconfigurable, istnieje możliwość Pozostań na bieżąco z wymaganiami zmieniających się szybko algorytmów AI.

- Skalowanie

    Microsoft Azure to na świecie największy chmury inwestycji w FPGAs. Możesz uruchomić Brainwave w infrastrukturze skali platformy Azure.

Podgląd Brainwave projektu zintegrowany z usługi Azure Machine Learning jest obecnie dostępna. I ograniczona wersja zapoznawcza jest również dostępny do Przenieś Brainwave projektu do krawędzi, dzięki czemu możesz korzystać z prędkości obliczeniowych w własnych firmy i urządzeń.

W bieżącej wersji zapoznawczej Brainwave jest ograniczona do wdrożenia TensorFlow i ResNet50 neuronowej sieci oparte na sprzęcie układu FPGA Intel dla obrazu klasyfikacji i rozpoznawania. Brak planów obsługi więcej Galeria modeli i innych platform.

Następujące scenariusze użycia układu FPGA w oparciu o architekturę Brainwave projektu:

- Automatyczne system optyczny inspekcji. Zobacz [AI w czasie rzeczywistym: Microsoft ogłasza podgląd projektu Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Mapowanie okładce ziemi. Zobacz [wykonać mapowania na terabajtów satelitarnej obrazy obejmują ziemi przy użyciu FPGAs dla bezpośrednich Learning wnioskowania](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-create-an-fpga-service"></a>Jak utworzyć usługę układu FPGA?

Ogólny przepływ dla tworzenia usługi rozpoznawania obrazu na platformie Azure przy użyciu ResNet50 jako featurizer wygląda następująco:

1. ResNet50 została już wdrożona w Brainwave. Kompilacji innych wykresy (dane wejściowe daty, klasyfikacji i tak dalej) z TensorFlow i zdefiniuj potoku (dane wejściowe -> featurize -> klasyfikowania) przy użyciu pliku json definicji usługi. Kompresuje definicji i wykresy do pliku zip i przekazać plik zip do magazynu obiektów Blob Azure.
2. Zarejestruj modelu przy użyciu interfejsu API usługi Azure ML modelu administracyjnego z pliku zip w magazynie obiektów Blob.
3. Wdrażanie usługi z zarejestrowanego modelu przy użyciu interfejsu API zarządzania modelu uczenia Maszynowego Azure.

Samouczek, zobacz [ML sprzętu przyspieszony modeli Szybki Start Azure](https://github.com/Azure/aml-real-time-ai/blob/master/notebooks/resnet50/00_QuickStart.ipynb).

## <a name="start-using-fpga"></a>Rozpoczynanie korzystania z układu FPGA

Azure Machine Learning sprzętu przyspieszony modeli umożliwiają wdrażanie przeszkolone modeli DNN do FPGAs w chmurze Azure. Aby rozpocząć, zobacz:

- [Wdróż model jako usługę sieci web na układu FPGA](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning sprzętu przyspieszony modeli obsługiwane przez projekt Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Kolejne kroki

[Wdróż model jako usługę sieci web na układu FPGA](how-to-deploy-fpga-web-service.md)

[Informacje o sposobie instalowania układu FPGA zestawu SDK](reference-fpga-package-overview.md)

[Sprzęt informatycznych: ML na dużą skalę na górze Azure + układu FPGA: 2018 kompilacji (klip wideo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[W firmie Microsoft w chmurze można skonfigurować na podstawie układu FPGA (klip wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft przedstawia Brainwave projektu dla AI w czasie rzeczywistym](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)