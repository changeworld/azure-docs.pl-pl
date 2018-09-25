---
title: Gdzie można wdrażać modele przy użyciu usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Poznaj różne sposoby, które można wdrożyć swoje modele do produkcji przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961012"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Wdrażaj modele za pomocą usługi Azure Machine Learning

Usługa Azure Machine Learning udostępnia uczony model jest wdrażany na kilka sposobów. W tym dokumencie informacje o sposobie wdrażania swojego modelu jako usługi sieci web w chmurze platformy Azure lub na urządzeniach brzegowych IoT.

Można wdrażać modele do następujących celów obliczeń:

- Usługa Azure Container Instances (ACI)
- Azure Kubernetes Service (AKS)
- Azure IoT Edge
- Tablica programowalny bramy (FPGA)

Pozostałej części tego dokumentu zawiera informacje o każdej z tych opcji szczegółowo.

## <a name="azure-container-instances"></a>Azure Container Instances

Użyj usługi Azure Container Instances, wdrażając swoje modele jako punkt końcowy interfejsu API REST, jeśli jeden lub więcej z następujących warunków jest spełniony:
- Szukasz szybkiego wdrażania, aby oceniać i weryfikacja modelu. Wdrożenie usługi ACI zwykle zostało zakończone w ciągu mniej niż 5 minut.
- Szukasz do wdrażania modelu w trakcie opracowywania lub środowiska testowego. ACI umożliwia wdrażanie 20 grup kontenerów na subskrypcję. Aby uzyskać więcej informacji, zobacz [limity przydziałów i dostępność regionów dla usługi Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentu.

Aby uzyskać więcej informacji, zobacz [wdrażanie modelu w usłudze Azure Container Instances](how-to-deploy-to-aci.md) dokumentu.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Na potrzeby scenariuszy produkcyjnych w dużej skali możesz wdrożyć model do usługi Azure Kubernetes Service (AKS). Można użyć istniejącego klastra AKS lub utworzyć nowe konto, przy użyciu zestawu SDK usługi Azure Machine Learning, interfejsu wiersza polecenia lub witryny Azure portal.

Tworzenie usługi AKS klastra jest czas procesu dla obszaru roboczego. Po utworzeniu można ponownie użyć klastra dla wielu wdrożeń. Jeśli możesz usunąć klaster lub grupę zasobów, która ją zawiera, następnie musi utworzysz nowy klaster przy następnym zajdzie potrzeba wdrożenia.

Wdrażanie w usłudze AKS zapewnia automatyczne skalowanie, rejestrowanie, zbierania danych modelu i krótszych czasów reakcji dla usług sieci web. 

Proces tworzenia klastra usługi AKS trwa około 20 minut.

Aby uzyskać więcej informacji, zobacz [wdrażanie modelu w usłudze Azure Kubernetes Service](how-to-deploy-to-aks.md) dokumentu.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Z urządzeń IoT jest szybsze przeprowadzać ocenianie na urządzeniu, zamiast wysyłania danych do chmury i Oczekiwanie na model hostowanymi w chmurze, aby zwrócić dane. Za pomocą usługi Azure IoT Edge może obsługiwać modelu na urządzeniach brzegowych. Wdrażanie modelu do usługi IoT Edge, jeśli potrzebujesz co najmniej jedną z następujących możliwości:
- Obsługa zadań o priorytecie lokalnie, nawet bez połączenia z chmury
- Praca z wygenerowane dane, który jest zbyt duży, aby szybko ściąganie z chmury
- Włącz przetwarzanie w czasie rzeczywistym za pomocą inteligentnych funkcji w lub w pobliżu lokalne urządzenia
- Uwzględnić wymagania dotyczące ochrony prywatności danych 

Aby uzyskać więcej informacji, zobacz [Wdróż na platformie Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) dokumentu.

Aby uzyskać więcej informacji na temat usługi IoT Edge, zobacz [dokumentacji usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a name="field-programmable-gate-arrays-fpga"></a>Tablice programowalny bramy (FPGA)

Obsługiwane przez Project Brainwave modeli Accelerated sprzętowych należy możliwe uzyskanie bardzo niskimi opóźnieniami dla żądań wnioskowania w czasie rzeczywistym. Project Brainwave przyspiesza głębokich sieciach neuronowych (DNN) wdrożone w tablicach programowalny bramy w chmurze platformy Azure. Najczęściej używane sieci są dostępne jako featurizers transferu uczenia lub można dostosować za pomocą wag uczony z własnych danych.

Aby uzyskać więcej informacji, zobacz [Wdróż na FPGA](how-to-deploy-fpga-web-service.md) dokumentu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o tym, jak wdrożyć model do określonych obliczeniowego elementu docelowego, zobacz następujące dokumenty:

* [Wdrażanie modelu w usłudze Azure Container Instances](how-to-deploy-to-aci.md)
* [Wdrażanie modelu w usłudze Azure Kubernetes Service](how-to-deploy-to-aks.md)
* [Wdrażanie modelu do usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Wdrażanie modelu do FPGA](how-to-deploy-fpga-web-service.md)
