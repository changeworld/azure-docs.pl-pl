---
title: Pobieranie obrazów z widoku niestandardowego — Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Ogólne omówienie o przy użyciu wyszukiwania niestandardowego Bing, aby pobrać obrazy z sieci Web w widoku niestandardowym.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 04a2306853b658ecd719106680affc81a95034eb
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813658"
---
# <a name="get-images-from-your-custom-view"></a>Uzyskiwanie obrazów w widoku niestandardowym

Niestandardowe wyszukiwanie obrazów Bing pozwala Wzbogacaj środowisko wyszukiwania niestandardowego przy użyciu obrazów. Podobnie jak wyniki z Internetu, obsługuje wyszukiwanie niestandardowe wyszukiwanie obrazów na liście wystąpienia usługi witryny sieci Web. Można uzyskać obrazów przy użyciu interfejs API wyszukiwania obrazów Bing niestandardowego lub za pośrednictwem interfejsu użytkownika obsługiwanych funkcji. Za pomocą funkcji hostowanych interfejsu użytkownika jest łatwa w użyciu i zalecany dla środowiska wyszukiwania i przeprowadzanie w skrócie zamówienia.  Aby uzyskać informacji o konfigurowaniu hostowanych interfejs użytkownika w celu dołączenia obrazów, zobacz [konfigurowania środowiska interfejsu użytkownika hostowanej](hosted-ui.md).

Jeśli chcesz większej kontroli nad wynikami wyszukiwania, można użyć interfejs API wyszukiwania obrazów Bing niestandardowego. Ponieważ wywołanie interfejsu API jest podobny do wywoływania interfejsu API wyszukiwania obrazów Bing wyewidencjonowania [wyszukiwania obrazów Bing](../Bing-Image-Search/overview.md) przykłady, wywołanie interfejsu API. Jednak zanim to zrobisz, zapoznaj się z [dokumentacja interfejsu API wyszukiwania obrazów niestandardowych](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) zawartości. Główne różnice to obsługiwanych parametrów zapytania (musi zawierać parametr zapytania customConfig) i wysyłać żądania do punktu końcowego.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->