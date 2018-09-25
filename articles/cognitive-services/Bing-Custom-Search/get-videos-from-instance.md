---
title: Obejrzyj filmy wideo z widoku niestandardowego | Dokumentacja firmy Microsoft
description: Ogólne omówienie dotyczące korzystania z wyszukiwania niestandardowego Bing można pobrać pliki wideo z sieci Web w widoku niestandardowym.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 980081ae2f576aadd4ac31e29f0f77ddcb4e7f64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978809"
---
# <a name="get-videos-from-your-custom-view"></a>Obejrzyj filmy wideo z widoku niestandardowego

Niestandardowe wyszukiwania wideo Bing pozwala Wzbogacaj środowisko wyszukiwania niestandardowego filmów wideo. Podobnie jak wyniki z Internetu, obsługuje wyszukiwanie niestandardowe wyszukiwanie filmów wideo na liście wystąpienia usługi witryny sieci Web. Można uzyskać wideo, korzystając z interfejs API wyszukiwania wideo Bing niestandardowego lub za pośrednictwem interfejsu użytkownika obsługiwanych funkcji. Za pomocą funkcji hostowanych interfejsu użytkownika jest łatwa w użyciu i zalecany dla środowiska wyszukiwania i przeprowadzanie w skrócie zamówienia. Aby uzyskać informacji o konfigurowaniu hostowanych interfejs użytkownika do uwzględnienia filmów wideo, zobacz [konfigurowania środowiska interfejsu użytkownika hostowanej](hosted-ui.md).

Jeśli chcesz większej kontroli nad wynikami wyszukiwania, można użyć interfejs API wyszukiwania wideo Bing niestandardowego. Ponieważ wywołanie interfejsu API jest podobny do wywoływania interfejsu API wyszukiwania wideo Bing wyewidencjonowania [wyszukiwania wideo Bing](../Bing-Video-Search/search-the-web.md) przykłady, wywołanie interfejsu API. Jednak zanim to zrobisz, zapoznaj się z [dokumentacja interfejsu API wyszukiwania wideo w niestandardowe](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference) zawartości. Główne różnice to obsługiwanych parametrów zapytania (musi zawierać parametr zapytania customConfig) i wysyłać żądania do punktu końcowego.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->