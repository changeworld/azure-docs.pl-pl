---
title: Pakiety języka Python dla usługi Azure Machine Learning
description: Więcej informacji na temat pakietów języka python, dostępne dla użytkowników usługi Azure Machine Learning.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 36322c5fde7c992faa830fe272971b53fbf10211
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872486"
---
# <a name="python-packages-for-azure-machine-learning"></a>Pakiety języka Python dla usługi Azure Machine Learning

Dowiedz się więcej o zastrzeżonych pakietów języka Python z firmy Microsoft dla usługi Azure Machine Learning. Te biblioteki i funkcji można użyć w połączeniu z innymi pakietami typu open source lub innych firm, ale można używać zastrzeżonych pakietów, kod musi działać względem usługi lub na komputerze, który zapewnia interpreterów języka Python.

Pakiety usługi Azure Machine Learning to **rozszerzenia do zainstalowania narzędzia pip języka Python dla usługi Azure Machine Learning** umożliwiające analitykom danych i deweloperom sztucznej Inteligencji, które umożliwiają szybkie tworzenie i wdrażanie bardzo dokładnych machine learning i uczenia głębokiego modele dla różnych domen.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Pakiet Azure ML do przetwarzania obrazów

Przy użyciu usługi Azure ML pakietu dla przetwarzania obrazów, można tworzyć, dostosowywać i wdrażanie modeli uczenia głębokiego dla Klasyfikacja obrazów obiektu wykrywania i obraz podobieństwa.

Wypróbuj podjęcie następujących kroków dla tego pakietu:
1. [Pobierz](https://aka.ms/aml-packages/vision/download) pakietu.
1. Odczyt [zainstalować docs](https://aka.ms/aml-packages/vision).
1. [Tworzenie i wdrażanie modelu przetwarzania obrazów](how-to-build-deploy-image-classification-models.md) z notesem Jupyter.
1. Zapoznaj się z pakietu [dokumentację referencyjną](https://aka.ms/aml-packages/vision).

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Pakiet Azure ML do prognozowania

Przy użyciu usługi Azure ML pakietu dla prognozowanie, można tworzyć i wdrażać modele prognozowania szeregu czasowego finansowych i scenariuszy prognozowania popytu.

Wypróbuj podjęcie następujących kroków dla tego pakietu:
1. [Pobierz](https://aka.ms/aml-packages/forecasting/download) pakietu.
1. Odczyt [zainstalować docs](https://aka.ms/aml-packages/forecasting).
1. [Tworzenie i wdrażanie modeli prognozy](how-to-build-deploy-forecast-models.md) z notesem Jupyter.
1. Zapoznaj się z pakietu [dokumentację referencyjną](https://aka.ms/aml-packages/forecasting).

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Pakiet Azure ML analizy tekstu

Przy użyciu usługi Azure ML pakietu analizy tekstu tworzyć modele uczenia głębokiego dla klasyfikacji tekstu, wyodrębnianie jednostek niestandardowych i osadzanie word tekstu.

Wypróbuj podjęcie następujących kroków dla tego pakietu:
1. [Pobierz](https://aka.ms/aml-packages/text/download) pakietu.
1. Odczyt [zainstalować docs](https://aka.ms/aml-packages/text).
1. [Tworzenie i wdrażanie modeli klasyfikacji tekstu](how-to-build-deploy-text-classification-models.md) z notesem Jupyter.
1. Zapoznaj się z pakietu [dokumentację referencyjną](https://aka.ms/aml-packages/text).

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (Akceleracja FPGA)

Za pomocą pakietu Azure Machine Learning sprzętowego przyspieszania analitykom danych i sztucznej Inteligencji deweloperzy mogą cechowanie obrazów przy użyciu wersji wykonywanie kwantyzowanych siecią ResNet-50, uczyć klasyfikatory na podstawie tych funkcji, a następnie Wdróż modele [użytkownika tablice (FPGA) bramy](concept-accelerate-with-fpgas.md) na platformie Azure wnioskowania bardzo niskimi opóźnieniami.

Wypróbuj podjęcie następujących kroków dla tego pakietu:
1. [Pobierz](https://aka.ms/aml-real-time-ai-package) pakietu.
1. Odczyt [zainstalować docs](reference-fpga-package-overview.md).
1. [Wdrażanie modelu do FPGA](how-to-deploy-fpga-web-service.md).

