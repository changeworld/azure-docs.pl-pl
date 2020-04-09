---
title: Uruchom kontener detektora anomalii w wystąpieniach kontenera platformy Azure
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera detektora anomalii w wystąpieniu kontenera platformy Azure i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4973254c8a53917d99daf38b0d11e5dbecbb731
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875135"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Wdrażanie kontenera detektora anomalii w wystąpieniach kontenerów platformy Azure

Dowiedz się, jak wdrożyć kontener [detektora anomalii](../anomaly-detector-container-howto.md) usług Cognitive Services w [wystąpieniach kontenerów](https://docs.microsoft.com/azure/container-instances/)platformy Azure. Ta procedura pokazuje tworzenie zasobu detektora anomalii. Następnie omówimy ciągnięcie skojarzonego obrazu kontenera. Na koniec podkreślamy możliwość wykonywania aranżacji dwóch z przeglądarki. Korzystanie z kontenerów można przenieść uwagę deweloperów od zarządzania infrastrukturą zamiast skupiać się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Żądanie dostępu do rejestru kontenerów prywatnych

Najpierw należy wypełnić i przesłać [formularz żądania kontenera detektora anomalii,](https://aka.ms/adcontainer) aby zażądać dostępu do kontenera.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [Instalowanie i uruchamianie kontenerów](../anomaly-detector-container-configuration.md) do wyciągania obrazu kontenera i uruchamianie kontenera
* Przeglądanie [Konfigurowanie kontenerów](../anomaly-detector-container-configuration.md) pod kątem ustawień konfiguracji
* [Dowiedz się więcej o usłudze INTERFEJSU API detektora anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
