---
title: Uzyskiwanie kluczy subskrypcji dla interfejsu API przetwarzania obrazów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można pobrać kluczy subskrypcji dla wywołań interfejsu API przetwarzania obrazów w usługach Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.openlocfilehash: 682266df9e1a1f8209904697358d10bbaa633cf1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35760248"
---
# <a name="how-to-obtain-subscription-keys"></a>Jak uzyskiwanie kluczy subskrypcji

Komputer wizję usługi wymagają klawisze specjalne subskrypcji. Każde wywołanie interfejsu API przetwarzania obrazów wymaga klucza subskrypcji. Ten klucz musi zostać przekazany przez parametr ciągu zapytania albo określony w nagłówku żądania.

Aby utworzyć klucze subskrypcji, zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). Jest to bezpłatne się zarejestrować. Cennik dla tych usług jest mogą ulec zmianie.

>[!NOTE]
Klucze subskrypcji są prawidłowe dla tylko jednego z tych [regionów platformy Azure w programie Microsoft](https://azure.microsoft.com/regions/). 

| Region | Adres |
|---|---|
| Zachodnie stany USA | westus.API.cognitive.microsoft.com |
| Wschodnie stany USA 2 | eastus2.API.cognitive.microsoft.com |
| Środkowo-zachodnie stany USA | westcentralus.API.cognitive.microsoft.com |
| Europa Zachodnia | westeurope.API.cognitive.microsoft.com |
| Azja Południowo-Wschodnia | southeastasia.API.cognitive.microsoft.com |

Jeśli rejestrujesz się przy użyciu bezpłatnej wersji próbnej przetwarzania obrazów, klucze subskrypcji są prawidłowe dla **westcentral** region (`https://westcentralus.api.cognitive.microsoft.com/`). Najbardziej typowe tak jest. Jednak Jeśli zarejestrujesz się w wizualizacji komputerowej przy użyciu usługi Microsoft Azure konta poprzez [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) witryny sieci Web, określić region, aby pobrać wersję próbną wymienionego na liście regionów.

Na przykład, jeśli rejestrujesz się przetwarzanie obrazów za pomocą konta usługi Microsoft Azure i możesz określić `westus` w Twoim regionie, należy użyć `westus` region dla wywołania interfejsu API REST (`https://westus.api.cognitive.microsoft.com/`).

Jeśli nie pamiętasz region związane z kluczem subskrypcji po uzyskaniu klucza wersji próbnej można znaleźć regionu w [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Linki pokrewne:

* [Opcje cenowe interfejsów API usługi Microsoft Cognitive](https://azure.microsoft.com/pricing/details/cognitive-services/)
