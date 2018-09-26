---
title: Co to jest FPGA i Project Brainwave? — Usługi azure Machine Learning
description: Dowiedz się, jak przyspieszyć tworzenie modeli i głębokich sieciach neuronowych za pomocą układów FPGA na platformie Azure. Ten artykuł zawiera wprowadzenie do macierzy programowalny bramy (FPGA) i jak Azure Machine Learning udostępnia w czasie rzeczywistym sztucznej inteligencji (AI), po wdrożeniu modelu FPGA platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: adcd812bc63f98e4cbde949946bf32a037c6d704
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158691"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Co to jest FPGA i Project Brainwave?

Ten artykuł zawiera wprowadzenie do macierzy programowalny bramy (FPGA) i jak Azure Machine Learning udostępnia w czasie rzeczywistym sztucznej inteligencji (AI), po wdrożeniu modelu FPGA platformy Azure.

Układów FPGA zawierała tablicę bloków programowalny i typu hierarchii możliwych do ponownego skonfigurowania. Dla połączeń zezwolić na te bloki, aby można skonfigurować na różne sposoby, po produkcji. Układów FPGA zawierają kombinację programowania i wydajność w porównaniu do innych mikroukłady.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Vs układów FPGA. Procesor CPU, procesora GPU i ASIC

![Porównanie usług Azure Machine Learning FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Procesor||Opis|
|---|:-------:|------|
|Scalonych specyficzne dla aplikacji|ASICs|Obwody niestandardowe, takie jak Google TensorFlow procesora jednostki (TPU), zapewnia najwyższą efektywnością. Nie można ich skonfigurować stosownie do potrzeb.|
|Tablice programowalny bramy|Układów FPGA|Układów FPGA, takie jak te, które są dostępne na platformie Azure zapewniają wydajność blisko ASICs, ale są elastyczne i możliwych do ponownego skonfigurowania wraz z upływem czasu, aby zaimplementować logikę nowe.|
|Procesory graficzne|Procesory GPU|Popularne wybór dla obliczeń sztucznej Inteligencji, oferując możliwości przetwarzania równoległego, ułatwiając szybsze na renderowanie obrazu niż procesorów CPU.|
|Jednostek centralnych|Procesory CPU|Ogólnego przeznaczenia procesorów, których działanie nie jest idealnym rozwiązaniem dla grafiki i przetwarzania plików wideo.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave na platformie Azure

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) to architektura ekonomiczne sprzętu firmy Microsoft, oparte na urządzeniach FPGA firmy Intel, że programistów i analityków danych umożliwia przyspieszenie w czasie rzeczywistym obliczeń sztucznej Inteligencji.  Ta architektura włączone FPGA oferuje **wydajności**, **elastyczność**, i **skalowania** i jest dostępna na platformie Azure.

**Układów FPGA umożliwiają uzyskanie małych opóźnień dla żądań wnioskowania w czasie rzeczywistym.** Przetwarzanie wsadowe oznacza zbieranie dużej ilości danych i wprowadzając do niej z procesorem, aby poprawić wykorzystanie sprzętu. Przetwarzanie wsadowe może spowodować opóźnienia, ponieważ wymaga przetworzenia większej ilości danych, ale może to zwiększyć przepływność. Project Brainwave implementacje jednostek przetwarzania neuronowych nie wymagają dzielenia na partie; w związku z tym opóźnienie może być wiele razy niższa w porównaniu do GPU i CPU.

### <a name="reconfigurable-power"></a>Możliwych do ponownego skonfigurowania zasilania
**Układów FPGA można skonfigurować dla różnych typów modeli uczenia maszynowego.** Ta elastyczność ułatwia przyspieszenie aplikacji na podstawie najbardziej optymalną precyzja wartości liczbowych i model pamięci używane.

Nowe techniki uczenia maszyny są opracowywane w regularnych odstępach czasu, a projekt sprzętu Project Brainwave również zmieniających się szybko. Ponieważ układów FPGA możliwych do ponownego skonfigurowania, jest to możliwe, aby być na bieżąco z wymaganiami gwałtownie zmieniającym algorytmów sztucznej Inteligencji.

### <a name="whats-supported-on-azure"></a>Co to jest obsługiwana na platformie Azure
**Platforma Microsoft Azure to na świecie największych chmury zwrot z inwestycji w układów FPGA.** Możesz uruchomić Project Brainwave w infrastrukturze skalowania platformy Azure.

Obecnie obsługuje Project Brainwave:
+ Scenariusze dotyczące klasyfikacji i rozpoznawania obrazu
+ TensorFlow wdrożenia
+ Sieci: Siecią ResNet 50, siecią ResNet 152, VGG-16, VGG dysków SSD i DenseNet 121
+ Sprzęt firmy Intel FPGA 

Przy użyciu tej architektury włączone FPGA sprzętu, przeszkolonych sieci neuronowych uruchomić szybkie i mniejsze opóźnienie. Project Brainwave zrównoleglić wstępnie przeszkolonych głębokich sieciach neuronowych (DNN) różnych układów FPGA skalowania w poziomie usługi. Sieci mogą być wstępnie przeszkolonych, jako głębokiego featurized transferu uczenia lub dostosowaniu za pomocą wag zaktualizowane.

### <a name="scenarios-and-applications"></a>Scenariusze i aplikacje

Project Brainwave jest zintegrowana z usługą Azure Machine Learning. Firma Microsoft używa układów FPGA oceny DNN, klasyfikacji wyszukiwania Bing i oprogramowania zdefiniowane sieci (komputerowej SDN) składniki Akceleracja — Aby zmniejszyć opóźnienie podczas zwalniania procesorów CPU w przypadku innych zadań.

Następujące scenariusze użycia FPGA w architekturze Project Brainwave:
+ [Zautomatyzowany system inspekcji optycznych](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapowanie cover ziemi](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Wdrażanie do układów FPGA na platformie Azure

Poniżej przedstawiono przepływ pracy do tworzenia usługi rozpoznawania obrazów na platformie Azure przy użyciu obsługiwanych sieci jako featurized do wdrożenia na platformie Azure układów FPGA:

1. Użyj usługi Azure Machine Learning zestaw SDK for Python do tworzenia definicji usługi, który jest plikiem zawierająca opis potoku wykresów (dane wejściowe, featurized i klasyfikatora) oparte na TensorFlow. Polecenie wdrożenia zostanie automatycznie skompresuj wykresów i definicji do pliku ZIP i przekazywanie pliku ZIP do usługi Azure Blob storage.  System DNN została już wdrożona na Project Brainwave do uruchamiania na FPGA.

1. Zarejestruj model przy użyciu zestawu SDK przy użyciu pliku ZIP w usłudze Azure Blob storage.

1. Wdrażanie usługi przy użyciu zarejestrowanego modelu przy użyciu zestawu SDK.

Możesz rozpocząć wdrażanie uczone modele DNN do układów FPGA na platformie Azure w chmurze z tym artykułem **"[wdrożyć model jako usługę sieci web na FPGA](how-to-deploy-fpga-web-service.md)"**.


## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z tych filmów wideo i blogach:

+ [Sprzętu w Hiperskali: uczenie Maszynowe na dużą skalę na platformie Azure i FPGA: Build 2018 (wideo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [W firmie Microsoft na układach FPGA konfigurowalnej chmury (wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave dla sztucznej Inteligencji w czasie rzeczywistym: Strona główna projektu](https://www.microsoft.com/research/project/project-brainwave/)
