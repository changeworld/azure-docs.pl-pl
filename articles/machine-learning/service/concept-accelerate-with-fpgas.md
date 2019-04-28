---
title: Jakie są układów FPGA i Project Brainwave?
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak przyspieszyć tworzenie modeli i głębokich sieciach neuronowych za pomocą układów FPGA na platformie Azure. Ten artykuł zawiera wprowadzenie do macierzy programowalny bramy (FPGA) i jak usługa Azure Machine Learning udostępnia w czasie rzeczywistym sztucznej inteligencji (AI), po wdrożeniu modelu FPGA platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 1/10/2019
ms.custom: seodec18
ms.openlocfilehash: 716de6d491be753c5c32c0a2774404140010f72c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821208"
---
# <a name="what-are-fpgas-and-project-brainwave"></a>Jakie są układów FPGA i Project Brainwave?

Ten artykuł zawiera wprowadzenie do macierzy programowalny bramy (FPGA) i jak usługa Azure Machine Learning udostępnia w czasie rzeczywistym sztucznej inteligencji (AI), po wdrożeniu modelu FPGA platformy Azure.

Układów FPGA zawierała tablicę bloków programowalny i typu hierarchii możliwych do ponownego skonfigurowania. Dla połączeń zezwolić na te bloki, należy skonfigurować na różne sposoby po produkcji. W porównaniu do innych mikroukładami, układów FPGA dostarczać kombinację programowania i wydajności.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Vs układów FPGA. Procesor CPU, procesora GPU i ASIC

Poniższy diagram i tabeli pokazano, jak układów FPGA wypadają w porównaniu do innych procesorów.

![Diagram przedstawiający porównanie FPGA usług Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Procesor||Opis|
|---|:-------:|------|
|Scalonych specyficzne dla aplikacji|ASICs|Obwody niestandardowe, takie jak Google TensorFlow procesora jednostki (TPU), zapewnia najwyższą efektywnością. Nie można ich skonfigurować stosownie do potrzeb.|
|Tablice programowalny bramy|Układy FPGA|Układów FPGA, takie jak te, które są dostępne na platformie Azure zapewniają wydajność blisko ASICs. Są one również elastyczne i możliwych do ponownego skonfigurowania wraz z upływem czasu, aby zaimplementować logikę nowe.|
|Procesory graficzne|Procesory GPU|Popularne wybór dla obliczeń sztucznej Inteligencji. Procesory GPU oferują możliwości przetwarzania równoległego, ułatwiając szybsze na renderowanie obrazu niż procesorów CPU.|
|Jednostek centralnych|Procesory CPU|Ogólnego przeznaczenia procesorów, których wykonanie nie jest idealne rozwiązanie dla grafiki i przetwarzania plików wideo.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave na platformie Azure

[Project Brainwave](https://www.microsoft.com/en-us/research/project/project-brainwave/) to architektura sprzętu od firmy Microsoft. Jest ona oparta na urządzeniach FPGA firmy Intel, programistów i analityków danych, która umożliwia przyspieszanie w czasie rzeczywistym obliczeń sztucznej Inteligencji. Ta architektura włączone FPGA oferuje wydajność, elastyczność i skalowania i jest dostępna na platformie Azure.

Układów FPGA umożliwiają uzyskanie małych opóźnień dla żądań wnioskowania w czasie rzeczywistym. Żądań asynchronicznych (przetwarzanie wsadowe) nie są wymagane. Przetwarzanie wsadowe może spowodować opóźnienia, ponieważ wymaga przetworzenia większej ilości danych. Project Brainwave implementacje jednostek przetwarzania neuronowych nie wymagają dzielenia na partie; w związku z tym opóźnienie może być niższa wiele razy w porównaniu do procesorów GPU i CPU.

### <a name="reconfigurable-power"></a>Możliwych do ponownego skonfigurowania zasilania
Można ponownie skonfigurować układów FPGA dla różnych typów modeli uczenia maszynowego. Ta elastyczność ułatwia przyspieszenie aplikacji na podstawie najbardziej optymalną precyzja wartości liczbowych i model pamięci używane. Ponieważ układów FPGA możliwych do ponownego skonfigurowania, możesz Pozostań na bieżąco z wymaganiami gwałtownie zmieniające się algorytmów sztucznej Inteligencji.

### <a name="whats-supported-on-azure"></a>Co to jest obsługiwana na platformie Azure
Platforma Microsoft Azure to na świecie największych chmury zwrot z inwestycji w układów FPGA. Możesz uruchomić Project Brainwave w infrastrukturze skalowania platformy Azure.

Obecnie obsługuje Project Brainwave:
+ Scenariusze dotyczące klasyfikacji i rozpoznawania obrazu
+ TensorFlow wdrożenia
+ Sieci: Siecią ResNet 50, siecią ResNet 152, VGG-16, dysk SSD VGG i DenseNet 121
+ Sprzęt firmy Intel FPGA 

Przy użyciu tej architektury włączone FPGA sprzętu, przeszkolonych sieci neuronowych uruchomić szybkie i mniejsze opóźnienie. Project Brainwave zrównoleglić wstępnie przeszkolonych głębokich sieciach neuronowych (DNN) różnych układów FPGA skalowania w poziomie usługi. Sieci mogą być wstępnie przeszkolonych, jako głębokiego featurized transferu uczenia lub dostosowaniu za pomocą wag zaktualizowane.

### <a name="scenarios-and-applications"></a>Scenariusze i aplikacje

Project Brainwave jest zintegrowana z usługą Azure Machine Learning. Firma Microsoft używa układów FPGA oceny DNN, klasyfikacji wyszukiwania Bing i oprogramowania zdefiniowane sieci (komputerowej SDN) składniki Akceleracja — w celu zmniejszenia opóźnień, podczas zwalniania procesorów CPU w przypadku innych zadań.

Następujące scenariusze użycia FPGA w architekturze Project Brainwave:
+ [Zautomatyzowany system inspekcji optycznych](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapowanie cover ziemi](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Wdrażanie do układów FPGA na platformie Azure

Tworzenie usługi rozpoznawania obrazów na platformie Azure, umożliwia sieci obsługiwane jako featurized do wdrożenia na platformie Azure układów FPGA:

1. Użyj [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) do tworzenia definicji usługi. Definicja usługi jest plik opisujący potoku wykresów (dane wejściowe, featurized i klasyfikatora) w oparciu o TensorFlow. Polecenie wdrożenia automatycznie kompresuje definicji oraz wykresy do postaci pliku ZIP i przekazuje pliku ZIP do usługi Azure Blob storage. System DNN została już wdrożona na Project Brainwave do uruchamiania na FPGA.

1. Korzystając z zestawu SDK przy użyciu pliku ZIP w usłudze Azure Blob storage, należy zarejestrować model.

1. Wdrażanie usługi przy użyciu zarejestrowanego modelu przy użyciu zestawu SDK.

Aby rozpocząć wdrażanie przeszkolone modele DNN do układów FPGA w chmurze platformy Azure, zobacz [wdrożyć model jako usługę sieci web na FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z tych filmów wideo i blogach:

+ [Sprzęt w Hiperskali: Uczenie Maszynowe na dużą skalę na platformie Azure i FPGA: Build 2018 (wideo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [W firmie Microsoft na układach FPGA konfigurowalnej chmury (wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave dla sztucznej Inteligencji w czasie rzeczywistym: Strona główna projektu](https://www.microsoft.com/research/project/project-brainwave/)
