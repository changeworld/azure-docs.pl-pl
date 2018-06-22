---
title: Obraz klasyfikacji z pakietem usługi Azure Machine Learning wizji komputera i zespołu danych nauki procesu (TDSP) | Dokumentacja firmy Microsoft
description: Opisuje zespołu danych nauki procesu (TDSP) i usługi Azure Machine Learning pakietu dla komputera wizji klasyfikacji obrazu.
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
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300801"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Skórki raka klasyfikacji obrazu z pakietem usługi Azure Machine Learning wizji komputera i proces nauki danych zespołu

W tym artykule przedstawiono sposób użycia [pakiet Azure Machine Learning dla komputera wizji](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) do uczenia, testowania i wdrażania *obrazu klasyfikacji* modelu. W przykładzie użyto Struktura zespołu danych nauki procesu (TDSP) i szablonów w [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Ten przewodnik zawiera kompletnego przykładu. Używa [kognitywnych zestaw narzędzi firmy Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) jako dokładnego learning framework i szkolenia odbywa się na [nauki danych maszyny wirtualnej](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) maszyny procesora GPU. Wdrożenie używa usługi Azure Machine Learning operationalization interfejsu wiersza polecenia.

Wiele aplikacji w domenie wizji komputera może mieć ramkę jako obraz klasyfikacji problemów. Obejmują one budowania modeli, aby uzyskać odpowiedzi na pytania proste, takich jak "jest obiektem obecnych w obrazie?" gdy obiekt jest dog, samochodu lub wysyłki. Obejmuje też odpowiedzi na pytania bardziej złożonych, takie jak "klasę ważności choroby oczu jest evinced przez ten pacjenta retinal skanowania?" Pakiet Azure Machine Learning dla komputera wizji usprawnia przetwarzanie danych klasyfikacji obrazu i potoki modelowania. 

## <a name="link-to-the-github-repository"></a>Połącz z repozytorium GitHub
W tym artykule jest podsumowanie dokumentu o próbki. Bardziej szczegółową dokumentację można znaleźć na [witryny GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Wskazówki procesu nauki danych zespołu

W tym przewodniku zastosowano [proces nauki danych zespołu](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) cyklu życia. Instruktaż obejmuje następujące kroki cyklu życia.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Uzyskiwanie danych](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Dataset międzynarodowej skórki Imaging współpracy ISIC () jest używany do zadań klasyfikacji obrazu. ISIC jest powiązanie między środowiskiem akademickim a branży, które ułatwiają stosowanie cyfrowe skórki imaging do badania i pomóc w zmniejszeniu śmiertelności innych typów. [ISIC archiwum](https://isic-archive.com/#images) zawiera ponad 13 000 skórki uszkodzenie obrazów, które są oznaczone jako niegroźne lub złośliwe. Pobierz przykładowe obrazów z ISIC archiwum.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelowanie](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
W kroku modelowania są wykonywane następujące podetapów.

#### <a name="dataset-creation"></a>Tworzenie zestawu danych

Generuje obiekt dataset w pakiecie usługi Azure Machine Learning dla komputera wizji, zapewniając katalogiem głównym obrazów na dysku lokalnym. 

#### <a name="image-visualization-and-annotation"></a>Obraz wizualizacji i adnotacji

Wizualizuj obrazów w obiekcie dataset i popraw etykiet w razie potrzeby.

#### <a name="image-augmentation"></a>Rozszerzeniu obrazu

Rozszerzyć obiekt dataset za pomocą przekształcenia, które zostały opisane w [imgaug](https://github.com/aleju/imgaug) biblioteki.

#### <a name="dnn-model-definition"></a>Definicja modelu DNN

Zdefiniuj architektura modelu, który jest używany w kroku szkolenia. Sześć wstępnie przeszkolone modele sieci neuronowej głębokość są obsługiwane w pakiecie usługi Azure Machine Learning dla komputera wizji: AlexNet, Resnet 18 Resnet 34, Resnet – 50, Resnet 101 i Resnet 152.

#### <a name="classifier-training"></a>Klasyfikator szkolenia

Uczenie sieci neuronowej z domyślnych lub niestandardowych parametrów.

#### <a name="evaluation-and-visualization"></a>Ocena i wizualizacji

Udostępniają funkcje do oceny wydajności uczonego modelu w zestawie danych niezależnych testu. Metryki oceny obejmują dokładność, dokładność, odwoływania i krzywą ROC.

Podetapów są objaśnione szczegółowo w odpowiednich notesu Jupyter. Notesu zawiera również wskazówki dotyczące włączania parametry, takie jak tempo uczenia, rozmiar partii mini i szybkość przerwany do dalszego zwiększenia wydajności modelu.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. wdrożenia](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Ten krok operationalizes model, który jest tworzony z kroku modelowania. Podaj wymagań wstępnych oraz wymagane ustawienia. Użycie usługi sieci web jest także objaśniony. Z tego samouczka dowiesz się skompilować modele uczenia głębokie z pakietem Azure Machine Learning wizji komputera i operacjonalizacji model w programie Azure.

## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj o dodatkową dokumentację [pakiet Azure Machine Learning dla komputera wizji](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Odczyt [proces nauki danych zespołu](https://aka.ms/tdsp) dokumentacji, aby rozpocząć pracę.


## <a name="references"></a>Dokumentacja

* [Pakiet Azure Machine Learning do widzenia komputera](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Środowisko robocze usługi Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Maszyna wirtualna nauki danych](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

