---
title: Klasyfikacja obrazów przy użyciu pakietu usługi Azure Machine Learning do przetwarzania obrazów i Team Data Science naukowych | Dokumentacja firmy Microsoft
description: W tym artykule opisano korzystanie z zespołu Data Science naukowych i pakiet Azure Machine Learning dla wizualizacji komputerowej dla Klasyfikacja obrazów.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 427ea1f3f22855b2c54beacbfb89a8f7fd37cce0
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42055979"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Skórki raka Klasyfikacja obrazów przy użyciu pakietu usługi Azure Machine Learning do przetwarzania obrazów i danych zespołu dla celów naukowych

W tym artykule dowiesz się, jak używać [pakiet usługi Azure Machine Learning dla przetwarzania obrazów](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) do szkolenia, Testuj i wdrażaj *Klasyfikacja obrazów* modelu. W przykładzie użyto Team Data Science naukowych struktury i szablony w [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). Ten przewodnik zawiera pełny przykład. Używa ona [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) jako głębokiego uczenia i szkolenia odbywa się na [maszyny wirtualnej do nauki o danych](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) procesora GPU maszyny. Wdrożenie używa operacjonalizacji usługi Azure Machine Learning interfejs wiersza polecenia.

Wiele aplikacji w domenie komputera przetwarzania może mieć ramkę jako obraz klasyfikacji problemów. Należą do budowania modeli, aby uzyskać odpowiedzi na proste pytanie, takie jak "jest obiektem obecnych w obrazie?" gdy obiekt jest pies, samochód lub statku. Obejmuje również odpowiedzi na bardziej złożone pytania, takie jak "jakie klasy oka choroby ważności jest evinced przez skanowanie retinal to pacjenta?" Pakiet usługi Azure Machine Learning do przetwarzania obrazów usprawnia przetwarzanie danych klasyfikacji obrazów i potoku modelowania. 

## <a name="link-to-the-github-repository"></a>Połącz z repozytorium GitHub
Ten artykuł jest dokumentem podsumowania dotyczących przykładu. Bardziej szczegółową dokumentację można znaleźć na [witryny GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Przewodnik po procesie nauki o danych zespołu

W tym instruktażu wykorzystano [zespołu danych dla celów naukowych](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) cyklu życia. Instruktaż obejmuje następujące kroki cyklu życia.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Pozyskiwanie danych](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Zestaw danych International skórki Imaging współpracy (ISIC) jest używany do zadań klasyfikacji obrazów. ISIC to zaawansowane, skalowalne rozwiązanie i branży, które ułatwiają stosowanie cyfrowego skórki imaging warsztatami i pomóc w zmniejszeniu śmiertelności innych typów. [ISIC archiwum](https://isic-archive.com/#images) zawiera ponad 13 000 obrazów uszkodzenie skórki, które są oznaczone jako nieszkodliwy lub złośliwe. Pobierz przykładowe obrazy z archiwum ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelowanie](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
W kroku modelowania są wykonywane następujące podrzędne.

#### <a name="dataset-creation"></a>Tworzenie zestawu danych

Generuj obiekt dataset w pakiecie usługi Azure Machine Learning do przetwarzania obrazów, zapewniając katalog główny obrazów na dysku lokalnym. 

#### <a name="image-visualization-and-annotation"></a>Obraz wizualizacji i adnotacji

Wizualizuj obrazy w obiekcie dataset i popraw etykiety zgodnie z potrzebami.

#### <a name="image-augmentation"></a>Rozszerzenie obrazu

Rozszerzaj obiektu dataset za pomocą przekształcenia, które są opisane w [imgaug](https://github.com/aleju/imgaug) biblioteki.

#### <a name="dnn-model-definition"></a>Definicja modelu DNN

Zdefiniuj architektura modelu, który jest używany w kroku szkolenia. Sześć wstępnie szkolone modele sieci neuronowej są obsługiwane w pakiecie usługi Azure Machine Learning do przetwarzania obrazów: AlexNet, siecią Resnet-18, siecią Resnet-34, siecią Resnet-50, siecią Resnet-101 i siecią Resnet-152.

#### <a name="classifier-training"></a>Klasyfikator szkolenia

Uczenie sieci neuronowych przy użyciu domyślnych lub niestandardowych parametrów.

#### <a name="evaluation-and-visualization"></a>Ocena i wizualizacji

Udostępniają funkcje do oceny wydajności uczonego modelu dla zestawu danych niezależnie od testów. Metryki oceny obejmują dokładności, dokładności, odwołania i WIELOKLASOWA krzywa.

Podkroki jest omówiona szczegółowo w odpowiedniej notesu programu Jupyter. Notes również wskazówki Włączanie parametrów, takich jak tempo uczenia, rozmiar partii mini i szybkość przerwany w celu dalszego zwiększenia wydajności modelu.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Wdrożenia](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

W tym kroku operationalizes model, który jest generowany z kroku modelowania. Przedstawia on wymagań wstępnych i Instalacja wymagana. Korzystanie z usługi sieci web jest poświęcony aktualizacjom. W tym samouczku dowiesz się tworzyć modele uczenia głębokiego z pakietu usługi Azure Machine Learning do przetwarzania obrazów i modelu na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj dokumentację [pakiet usługi Azure Machine Learning dla przetwarzania obrazów](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Odczyt [zespołu danych dla celów naukowych](https://aka.ms/tdsp) dokumentację ułatwiającą rozpoczęcie pracy.


## <a name="references"></a>Dokumentacja

* [Pakiet Azure Machine Learning do przetwarzania obrazów](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Środowisko robocze usługi Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Maszyna wirtualna do nauki o danych](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

