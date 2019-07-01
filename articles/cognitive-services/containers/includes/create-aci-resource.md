---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi kontenera platformy azure wystąpienia (ACI).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455072"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Utwórz zasób usługi Azure Container wystąpienia (ACI)

1. Przejdź do [Utwórz](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) strona Container Instances.

2. Na **podstawy** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz subskrypcję.|
    |Grupa zasobów|Wybierz grupę dostępnych zasobów lub Utwórz nową, takich jak `cognitive-services`.|
    |Nazwa kontenera|Wprowadź nazwę, taką jak `cognitive-container-instance`. Ta nazwa musi mieć niższe limity.|
    |Location|Wybierz region dla wdrożenia.|
    |Typ obrazu|`Public`|
    |Nazwa obrazu|Wprowadź lokalizację kontenera usługi Cognitive Services. Może to być tej samej lokalizacji, które są używane w `docker pull` polecenia _na przykład_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Typ systemu operacyjnego|`Linux`|
    |Size|Zmień rozmiar na sugerowane zalecenia dotyczące określonego kontenera usługi cognitive Services.:<br>2 rdzenie<br>4 GB

3. Na **sieć** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Porty|Ustaw TCP port `5000`. Udostępnia kontener na porcie 5000.|

4. Na **zaawansowane** wprowadź następujące szczegóły, aby przechodzić przez kontener [wymagane ustawienia rozliczeń](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) do zasobu usługi ACI:

    |Strona Zaawansowane klucza|Advanced page value|
    |--|--|
    |`apikey`|Skopiowane ze **klucze** strony zasobu, analizy tekstu. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Skopiowane ze **Przegląd** strony zasobu, analizy tekstu. Przykład: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Kliknij przycisk **Przejrzyj i Utwórz**
1. Po pomyślnej weryfikacji kliknij przycisk **Utwórz** na zakończenie procesu tworzenia
1. Po pomyślnym wdrożeniu zasobu jest gotowy do użycia