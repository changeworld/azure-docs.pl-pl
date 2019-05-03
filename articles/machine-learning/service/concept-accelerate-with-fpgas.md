---
title: Co to jest brama programowalny tablic (FPGA)
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak przyspieszyć tworzenie modeli i głębokich sieciach neuronowych za pomocą układów FPGA na platformie Azure. Ten artykuł zawiera wprowadzenie do macierzy programowalny bramy (FPGA) i jak usługa Azure Machine Learning udostępnia w czasie rzeczywistym sztucznej inteligencji (AI), po wdrożeniu modelu FPGA platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 648dc462477570a692eff588c558a18a3121e4e7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025247"
---
# <a name="what-are-field-programmable-gate-arrays-fpga"></a>Co to jest brama programowalny tablic (FPGA)

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

Układów FPGA na platformie Azure są oparte na urządzeniach FPGA firmy Intel, programistów i analityków danych, która umożliwia przyspieszanie w czasie rzeczywistym obliczeń sztucznej Inteligencji. Ta architektura włączone FPGA oferuje wydajność, elastyczność i skalowania i jest dostępna na platformie Azure.

Układów FPGA umożliwiają uzyskanie małych opóźnień dla żądań wnioskowania w czasie rzeczywistym. Żądań asynchronicznych (przetwarzanie wsadowe) nie są wymagane. Przetwarzanie wsadowe może spowodować opóźnienia, ponieważ wymaga przetworzenia większej ilości danych. Implementacje jednostkach przetwarzania neuronowych nie wymagają dzielenia na partie; w związku z tym opóźnienie może być niższa wiele razy w porównaniu do procesorów GPU i CPU.

### <a name="reconfigurable-power"></a>Możliwych do ponownego skonfigurowania zasilania
Można ponownie skonfigurować układów FPGA dla różnych typów modeli uczenia maszynowego. Ta elastyczność ułatwia przyspieszenie aplikacji na podstawie najbardziej optymalną precyzja wartości liczbowych i model pamięci używane. Ponieważ układów FPGA możliwych do ponownego skonfigurowania, możesz Pozostań na bieżąco z wymaganiami gwałtownie zmieniające się algorytmów sztucznej Inteligencji.

### <a name="whats-supported-on-azure"></a>Co to jest obsługiwana na platformie Azure
Platforma Microsoft Azure to na świecie największych chmury zwrot z inwestycji w układów FPGA. Układów FPGA, platforma Azure obsługuje:

+ Scenariusze dotyczące klasyfikacji i rozpoznawania obrazu
+ TensorFlow wdrożenia
+ Sieci: Siecią ResNet 50, siecią ResNet 152, VGG-16, dysk SSD VGG i DenseNet 121
+ Sprzęt firmy Intel FPGA 

Przy użyciu tej architektury włączone FPGA sprzętu, przeszkolonych sieci neuronowych uruchomić szybkie i mniejsze opóźnienie. Azure zrównoleglić wstępnie przeszkolonych głębokich sieciach neuronowych (DNN) różnych układów FPGA skalowania w poziomie usługi. Sieci mogą być wstępnie przeszkolonych, jako głębokiego featurized transferu uczenia lub dostosowaniu za pomocą wag zaktualizowane.

### <a name="scenarios-and-applications"></a>Scenariusze i aplikacje

Azure układów FPGA są zintegrowane z usługą Azure Machine Learning. Firma Microsoft używa układów FPGA oceny DNN, klasyfikacji wyszukiwania Bing i oprogramowania zdefiniowane sieci (komputerowej SDN) składniki Akceleracja — w celu zmniejszenia opóźnień, podczas zwalniania procesorów CPU w przypadku innych zadań.

Układów FPGA są używane w następujących scenariuszach:
+ [Zautomatyzowany system inspekcji optycznych](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapowanie cover ziemi](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Wdrażanie do układów FPGA na platformie Azure

Tworzenie usługi rozpoznawania obrazów na platformie Azure, umożliwia sieci obsługiwane jako featurized do wdrożenia na platformie Azure układów FPGA:

1. Użyj [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) do tworzenia definicji usługi. Definicja usługi jest plik opisujący potoku wykresów (dane wejściowe, featurized i klasyfikatora) w oparciu o TensorFlow. Polecenie wdrożenia automatycznie kompresuje definicji oraz wykresy do postaci pliku ZIP i przekazuje pliku ZIP do usługi Azure Blob storage. System DNN jest już wdrożony do uruchamiania na FPGA.

1. Korzystając z zestawu SDK przy użyciu pliku ZIP w usłudze Azure Blob storage, należy zarejestrować model.

1. Wdrażanie usługi przy użyciu zarejestrowanego modelu przy użyciu zestawu SDK.

Aby rozpocząć wdrażanie przeszkolone modele DNN do układów FPGA w chmurze platformy Azure, zobacz [wdrożyć model jako usługę sieci web na FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z tych filmów wideo i blogach:

+ [Sprzęt w Hiperskali: Uczenie Maszynowe na dużą skalę na platformie Azure i FPGA: Build 2018 (wideo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [W firmie Microsoft na układach FPGA konfigurowalnej chmury (wideo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave dla sztucznej Inteligencji w czasie rzeczywistym: Strona główna projektu](https://www.microsoft.com/research/project/project-brainwave/)
