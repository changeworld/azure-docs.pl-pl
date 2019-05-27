---
title: 'MLOps: Zarządzanie, wdrażanie i monitorowanie modeli uczenia Maszynowego'
titleSuffix: Azure Machine Learning service
description: 'Dowiedz się, jak używać usługi Azure Machine Learning dla MLOps: wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je. Można wdrażać te modele, które uczony przy użyciu usługi Azure Machine Learning, na komputerze lokalnym lub z innych źródeł.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 416bebc070cfcad52c6180e65f0066c46c826cbe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849648"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu usługi Azure Machine Learning

W tym artykule możesz Dowiedz się, jak wdrażanie, zarządzanie i monitorowanie swoje modele, aby nieustannie podnosić je za pomocą usługi Azure Machine Learning. Można wdrażać modele, które możesz skonfigurowanych pod kątem korzystania z usługi Azure Machine Learning na komputerze lokalnym lub z innych źródeł. 

Na poniższym diagramie przedstawiono przepływ pracy całego procesu wdrażania: [![Wdrażanie przepływu pracy dla usługi Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

MLOps / przepływ pracy wdrożenia obejmuje następujące kroki:
1. **Zarejestruj model** w rejestrze hostowanych w obszarze roboczym usługi Azure Machine Learning
1. **Użyj** modelu w usłudze sieci web w chmurze na urządzenia IoT lub do analizy w usłudze Power BI.
1. **Monitorowanie i zbieranie danych**
1. **Aktualizacja** wdrożenia za pomocą nowego obrazu.

Poszczególne kroki można wykonać niezależnie lub jako część jednego polecenia. Ponadto można tworzyć **przepływu pracy ciągłej integracji/ciągłego Dostarczania** jak pokazano na poniższej ilustracji.

[!["Usługa azure Machine Learning ciągłej integracji/ciągłego wdrażania (CI/CD) cyklu"](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="step-1-register-model"></a>Krok 1: Rejestrowanie modelu

Rejestracja modelu pozwala do przechowywania wersji modeli w chmurze platformy Azure, w obszarze roboczym. Rejestru model ułatwia organizowanie i śledzenie wytrenowane modele.

> [!TIP]
> Można również zarejestrować modeli skonfigurowanych pod kątem spoza usługi Azure Machine Learning.
 
Zarejestrowane modele są identyfikowane przez nazwę i wersję. Zawsze należy zarejestrować model o takiej samej nazwie jak innego istniejącego rejestru zwiększa numer wersji. Możesz także podać dodatkowe metadane tagów podczas rejestracji, który może służyć podczas wyszukiwania dla modeli. Usługa Azure Machine Learning obsługuje każdy model, który może być załadowany, przy użyciu języka Python 3.5.2 lub nowszej.

Nie można usunąć modeli, które są używane w aktywnym wdrożeniu.

Aby uzyskać więcej informacji, zobacz sekcję modelu rejestru [wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

Na przykład rejestrowania modelu są przechowywane w formacie pakietu pickle, zobacz [samouczka: Szkolenie modeli klasyfikacji obrazów](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Krok 2: Użyj modelu

Modele uczenia maszynowego może służyć jako usługi sieci web, na urządzeniach IoT Edge lub analiza danych z usług, takich jak usługa Power BI.

### <a name="web-service"></a>Usługa sieci Web

Można użyć modeli w **usług sieci web** celów obliczeń następującym kodem:

* Wystąpienie kontenera platformy Azure
* Azure Kubernetes Service

Aby wdrożyć model jako usługę sieci web, należy dostarczyć następujące elementy:

* Model lub zespole modeli.
* Zależności wymagane do używania tego modelu. Na przykład skrypt, który akceptuje żądania i wywołuje model, zależności conda, itd.
* Konfiguracja wdrożenia, który opisuje, jak i gdzie do wdrażania modelu.

Aby uzyskać więcej informacji, zobacz [wdrażanie modeli](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Urządzenia usługi IoT Edge

Za pomocą modeli urządzeń IoT za pomocą **moduły usługi Azure IoT Edge**. Moduły usługi IoT Edge są wdrażane do urządzeń sprzętowych, które umożliwia wnioskowania lub modelu oceniania na urządzeniu.

Aby uzyskać więcej informacji, zobacz [wdrażanie modeli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analiza

Microsoft Power BI obsługuje przy użyciu modeli uczenia maszynowego, analizy danych. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure Machine Learning w usłudze Power BI (wersja zapoznawcza)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Krok 3: Modele monitorują i zbierają dane

Monitorowanie umożliwia Ci zrozumieć, jakie dane są wysyłane do modelu i prognoz, które zwraca.

Te informacje pomagają zrozumieć, jak jest używany model. Zebrane dane wejściowe mogą być też przydatne w przyszłych wersjach szkolenie modelu.

Aby uzyskać więcej informacji, zobacz [jak włączyć zbieranie danych modelu](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Krok 4: Aktualizuj wdrożenie

Wdrożenia muszą zostać jawnie zaktualizowane. Aby uzyskać więcej informacji, zobacz Aktualizowanie sekcji [wdrażanie modeli](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [jak i gdzie można wdrażać modele](how-to-deploy-and-where.md) za pomocą usługi Azure Machine Learning. Na przykład wdrażania zobacz [samouczka: Wdrażanie modeli klasyfikacji obrazów w usłudze Azure Container Instances](tutorial-deploy-models-with-aml.md).

Dowiedz się, jak utworzyć [ciągłej integracji i wdrażania modeli uczenia Maszynowego przy użyciu potoków Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Dowiedz się, jak tworzyć klienckie aplikacje i usługi, których [korzystanie z modelu wdrożyć jako usługę sieci web](how-to-consume-web-service.md).
