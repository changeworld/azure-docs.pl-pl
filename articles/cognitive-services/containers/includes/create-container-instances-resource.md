---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717158"
---
## <a name="create-an-azure-container-instance-resource"></a>Utwórz zasób usługi wystąpienia kontenera platformy Azure

1. Przejdź do [Utwórz](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) strona Container Instances.

2. Na **podstawy** wprowadź następujące informacje:

    |Ustawienie|Value|
    |--|--|
    |Subscription|Wybierz subskrypcję.|
    |Resource group|Wybierz grupę dostępnych zasobów lub Utwórz nową, takich jak `cognitive-services`.|
    |Nazwa kontenera|Wprowadź nazwę, taką jak `cognitive-container-instance`. Nazwa musi mieć niższe limity.|
    |Location|Wybierz region dla wdrożenia.|
    |Typ obrazu|`Public`|
    |Nazwa obrazu|Wprowadź lokalizację kontenera usług Cognitive Services. Lokalizacja może być taki sam używane w `docker pull` polecenia, można znaleźć [kontenera repozytoriów i obrazów](../../cognitive-services-container-support.md#container-repositories-and-images) nazwy dostępnych obrazów i ich odpowiedniego repozytorium.|
    |Typ systemu operacyjnego|`Linux`|
    |Size|Zmień rozmiar na sugerowane zalecenia dotyczące określonego kontenera usługi cognitive Services:<br>2 rdzenie Procesora<br>4 GB

3. Na **sieć** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Porty|Ustaw TCP port `5000`. Udostępnia kontener na porcie 5000.|

4. Na **zaawansowane** wprowadź wymagane **zmienne środowiskowe** kontenera [rozliczeń ustawienia](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) zasobu usługi ACI:

    | Klucz | Value |
    |--|--|
    |`apikey`|Skopiowane ze **klucze** strony zasobu, analizy tekstu. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Skopiowane ze **Przegląd** strony zasobu, analizy tekstu. Przykład: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Kliknij przycisk **Przejrzyj i Utwórz**
1. Po pomyślnej weryfikacji kliknij przycisk **Utwórz** na zakończenie procesu tworzenia
1. Po pomyślnym wdrożeniu zasobu jest gotowy