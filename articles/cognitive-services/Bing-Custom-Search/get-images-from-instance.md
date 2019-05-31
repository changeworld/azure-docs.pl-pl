---
title: Pobieranie obrazów z widoku niestandardowego — Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Ogólne omówienie o przy użyciu wyszukiwania niestandardowego Bing, aby pobrać obrazy z sieci Web w widoku niestandardowym.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390328"
---
# <a name="get-images-from-your-custom-view"></a>Uzyskiwanie obrazów w widoku niestandardowym

Niestandardowe wyszukiwanie obrazów Bing pozwala Wzbogacaj środowisko wyszukiwania niestandardowego przy użyciu obrazów. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie obrazów w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. Można uzyskać obrazów przy użyciu interfejs API wyszukiwania obrazów Bing niestandardowego lub za pośrednictwem interfejsu użytkownika obsługiwanych funkcji. Za pomocą funkcji hostowanych interfejsu użytkownika jest łatwa w użyciu i zalecany dla środowiska wyszukiwania i przeprowadzanie w skrócie zamówienia.  Aby uzyskać informacji o konfigurowaniu hostowanych interfejs użytkownika w celu dołączenia obrazów, zobacz [konfigurowania środowiska interfejsu użytkownika hostowanej](hosted-ui.md).

Jeśli chcesz większej kontroli nad wynikami wyszukiwania, można użyć interfejs API wyszukiwania obrazów Bing niestandardowego. Ponieważ wywołanie interfejsu API jest podobny do wywoływania interfejsu API wyszukiwania obrazów Bing wyewidencjonowania [wyszukiwania obrazów Bing](../Bing-Image-Search/overview.md) przykłady, wywołanie interfejsu API. Jednak zanim to zrobisz, zapoznaj się z [dokumentacja interfejsu API wyszukiwania obrazów niestandardowych](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) zawartości. Główne różnice to obsługiwanych parametrów zapytania (musi zawierać parametr zapytania customConfig) i wysyłać żądania do punktu końcowego.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
