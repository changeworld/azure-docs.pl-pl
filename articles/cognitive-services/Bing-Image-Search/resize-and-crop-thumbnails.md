---
title: Zmienianie rozmiaru i przycinanie obrazów miniatur — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Zmień rozmiar i przycinanie obrazów miniatur dołączony do odpowiedzi z interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c53b84f31100ab72458ab6fb79b2009450eda15c
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917451"
---
# <a name="resize-and-crop-thumbnail-images"></a>Zmienianie rozmiaru i przycinanie obrazów miniatur

Podczas przetwarzania zapytania wyszukiwania Bing wygeneruje miniatury informacje o wszystkich obrazów w jego [odpowiedzi](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Te informacje mogą służyć do wyświetlania wszystkich lub być podzbiorem wartości zwracane miniatury. Jeśli wyświetlane są podzbiorem, podaj opcję, aby wyświetlić pozostałe obrazy.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Jeśli użytkownik kliknie miniaturę, możesz użyć parametru [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) do wyświetlenia użytkownikowi obrazu w pełnym rozmiarze. Pamiętaj o podaniu źródła obrazu.

Jeśli wartość `shoppingSourcesCount` lub `recipeSourcesCount` jest większa od zera, dodaj do miniatury odpowiedni znaczek, taki jak wózek sklepowy, w celu wskazania, że dla elementu na obrazie istnieje możliwość zrobienia zakupów lub uzyskania przepisów.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Aby uzyskać szczegółowe informacje dotyczące obrazu, takie jak strony internetowe zawierające ten obraz lub osoby, które zostały rozpoznane na obrazie, użyj parametru [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Aby uzyskać więcej informacji, zobacz [Szczegółowe informacje o obrazach](image-insights.md).

## <a name="resizing-and-cropping-thumbnails"></a>Zmiana rozmiaru i przycinanie miniatury

Można również zmienić rozmiar i rozwijać miniatury, np. gdy kursor użytkownika znajduje się nad nim.
> [!NOTE]
> Pamiętaj, aby po powiększeniu obrazu podać informacje o jego źródle. Możesz to zrobić na przykład przez wyodrębnienie hosta z parametru [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) i wyświetlenie go pod obrazem.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
