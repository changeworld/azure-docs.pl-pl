---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503469"
---
Następujące polecenia z wiersza polecenia platformy Azure, przydzieli zasób wykrywanie anomalii w warstwie cenowej bezpłatna. Kliknij pozycję **wypróbuj** przycisk, Wklej kod i naciśnij klawisz enter, aby go uruchomić na platformie Azure cloud shell. Będzie ona drukowana klucze do uwierzytelniania aplikacji. Po zakończeniu [Utwórz zmienną środowiskową](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) z dowolnego z kluczy, o nazwie `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Opcjonalnie możesz:
>    * Tworzenie zasobów za pomocą [witryny Azure portal](../articles/cognitive-services/cognitive-services-apis-create-account.md), lub [wiersza polecenia platformy Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) na komputerze lokalnym.
>    * Pobierz [klucza wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) prawidłowe przez 7 dni za darmo. Po jego utworzeniem będą dostępne na [witryny sieci Web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * wyświetlenia tego zasobu na [witryny Azure portal](https://portal.azure.com/). 

Usługi cognitive Services są reprezentowane przez zasoby platformy Azure, które należy zarezerwować. Każde konto usług Cognitive Services (i jego skojarzonego zasobu platformy Azure) musi należeć do grupy zasobów platformy Azure.

1. Utwórz grupę zasobów platformy Azure

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Utwórz zasób usługi Wykrywanie anomalii w warstwie bezpłatna

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Utwórz listę kluczy dla zasobu

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```