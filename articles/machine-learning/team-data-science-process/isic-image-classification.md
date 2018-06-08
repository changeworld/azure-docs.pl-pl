---
title: Klasyfikacja obrazu z pakietem uczenie maszynowe Azure (AML) dla komputera wizji (AMLPCV) i proces nauki danych zespołu (TDSP) | Dokumentacja firmy Microsoft
description: W tym artykule opisano stosowania TDSP (proces nauki zespołu danych) i AMLPCV klasyfikacji obrazu
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837222"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Klasyfikacja obrazu raka skórki z pakietem Azure Machine Learning (AML) dla komputera wizji (AMLPCV) i zespołu danych nauki procesu (TDSP)

## <a name="introduction"></a>Wprowadzenie

W tym artykule przedstawiono sposób użycia [Azure Machine Learning pakietu wizji komputera (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) do uczenia, testowania i wdrażania **obrazu klasyfikacji** modelu. W przykładzie użyto TDSP struktury i szablonów w [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Kompletnego przykładu znajduje się w tym przewodniku. Używa [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) jako dokładnego learning framework i szkolenia odbywa się na [danych nauki wirtualna](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) maszyny procesora GPU. Instalacja używa interfejsu wiersza polecenia Azure ML Operationalization.

Wiele aplikacji w domenie wizji komputera może mieć ramkę jako obraz klasyfikacji problemów. Obejmują one budowania modeli, aby odpowiedzieć na pytania takie jak "jest obiektem obecnych w obrazie?" (obiekt może być *dog*, *samochodu*, lub *wysłać*) i bardziej złożone pytania, takie jak "klasę ważności choroby oczu jest evinced przez ten pacjenta retinal skanowania?" AMLPCV usprawnia przetwarzanie danych klasyfikacji obrazu i modelowania potoku. 

## <a name="link-to-github-repository"></a>Połącz z repozytorium GitHub
Udostępniamy podsumowania dokumentacji tutaj przykładowej. Bardziej szczegółową dokumentację można znaleźć w [witryny GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Zespół danych nauki procesu (TDSP) wskazówki z AMLPCV

W tym przewodniku zastosowano [zespołu danych nauki procesu (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) cyklu życia.

Instruktaż obejmuje następujące kroki cyklu życia:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Acquision danych](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
ISIC zestawu danych jest używany do zadań klasyfikacji obrazu. ISIC (międzynarodowe skórki obrazowania współpracy) jest partership ośrodkami akademickimi i branży, w celu ułatwienia stosowania imaging skórki cyfrowe do badania i pomóc w zmniejszeniu śmiertelności innych typów. [ISIC archiwum](https://isic-archive.com/#images) zawiera ponad 13 000 skórki uszkodzenie obrazów z etykietami niegroźne lub złośliwe. Możemy pobrać przykładowy obrazów z ISIC archiwum.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelowanie](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
W kroku modelowania są wykonywane następujące podetapów. 

<b>2.1 Tworzenie zestawu danych</b><br>

Aby można było wygenerować obiektu Dataset w AMLPCV, podaj katalog główny obrazów na dysku lokalnym. 

<b>2.2 obrazu adnotacji i wizualizacji</b><br>

Wizualizuj obrazów w obiekcie dataset i popraw niektóre z tych etykiet, jeśli to konieczne.

<b>2.3 rozszerzeniu obrazu</b><br>

Rozszerzyć obiektu dataset za pomocą przekształcenia opisanego w [imgaug](https://github.com/aleju/imgaug) biblioteki.

<b>2.4 DNN definicji modelu</b><br>

Zdefiniuj architektura modelu w kroku szkolenia. Sześć różne modele sieci neuronowej głębokość na uczony są obsługiwane w AMLPCV: AlexNet, Resnet 18 34 Resnet i Resnet – 50, Resnet 101 i Resnet 152.

<b>2.5 szkolenie klasyfikatora</b><br>

Uczenie sieci neuronowej z domyślnych lub niestandardowych parametrów.

<b>2.6 oceny i wizualizacji</b><br>

Punkcie zapewnia funkcje do oceny wydajności uczonego modelu w zestawie danych niezależnych testu. Metryki oceny obejmują dokładność, dokładności i recall oraz krzywą ROC.

Te podetapów są objaśnione szczegółowo w odpowiednich notesu Jupyter. Możemy również podane wskazówki służący do włączania parametry, takie jak uczenia szybkości, rozmiar partii mini i szybkość przerwany do dalszego zwiększenia wydajności modelu.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. wdrożenia](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Ten krok operationalizes modelu utworzonego w kroku modelowania. Podaj operationalization wymagań wstępnych i Instalator. Na koniec korzystania z usługi sieci web jest także objaśniony. Za pomocą tego samouczka nauczysz do tworzenia modeli uczenia głębokie z AMLPCV i operacjonalizacji model w programie Azure.

## <a name="next-steps"></a>Następne kroki
Przeczytaj więcej dokumentacji na [Azure Machine Learning pakietu wizji komputera (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) i [zespołu danych nauki procesu (TDSP)](https://aka.ms/tdsp) rozpocząć pracę.


## <a name="references"></a>Dokumentacja

* [Azure Machine Learning pakietu dla komputera wizji (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Środowisko robocze usługi Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Połączenie analiz danych maszyny Wirtualnej](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

