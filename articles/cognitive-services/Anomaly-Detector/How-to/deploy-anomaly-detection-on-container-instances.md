---
title: Uruchom kontener wykrywania anomalii w Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener wykrywania anomalii w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716350"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Wdróż kontener wykrywania anomalii w Azure Container Instances

Dowiedz się, jak wdrożyć kontener [wykrywania anomalii](../anomaly-detector-container-howto.md) Cognitive Services na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu wykrywania anomalii. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

Musisz najpierw zakończyć i przesłać [formularz żądania kontenera wykrywania anomalii](https://aka.ms/adcontainer) , aby zażądać dostępu do kontenera.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [Instalowanie i uruchamianie kontenerów](../anomaly-detector-container-configuration.md) na potrzeby ściągania obrazu kontenera i uruchamiania kontenera
* Przegląd [skonfigurować kontenery](../anomaly-detector-container-configuration.md) ustawień konfiguracji
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
