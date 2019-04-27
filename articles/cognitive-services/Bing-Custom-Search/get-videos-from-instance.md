---
title: Obejrzyj filmy wideo z widoku niestandardowego — Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Ogólne omówienie dotyczące korzystania z wyszukiwania niestandardowego Bing można pobrać pliki wideo z sieci Web w widoku niestandardowym.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 202691bc03a7447cce28932406b00cc945a889db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589189"
---
# <a name="get-videos-from-your-custom-view"></a>Obejrzyj filmy wideo z widoku niestandardowego

Niestandardowe wyszukiwania wideo Bing pozwala Wzbogacaj środowisko wyszukiwania niestandardowego filmów wideo. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie wideo w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. Można uzyskać wideo, korzystając z interfejs API wyszukiwania wideo Bing niestandardowego lub za pośrednictwem interfejsu użytkownika obsługiwanych funkcji. Za pomocą funkcji hostowanych interfejsu użytkownika jest łatwa w użyciu i zalecany dla środowiska wyszukiwania i przeprowadzanie w skrócie zamówienia. Aby uzyskać informacji o konfigurowaniu hostowanych interfejs użytkownika do uwzględnienia filmów wideo, zobacz [konfigurowania środowiska interfejsu użytkownika hostowanej](hosted-ui.md).

Jeśli chcesz większej kontroli nad wynikami wyszukiwania, można użyć interfejs API wyszukiwania wideo Bing niestandardowego. Ponieważ wywołanie interfejsu API jest podobny do wywoływania interfejsu API wyszukiwania wideo Bing wyewidencjonowania [wyszukiwania wideo Bing](../Bing-Video-Search/search-the-web.md) przykłady, wywołanie interfejsu API. Jednak zanim to zrobisz, zapoznaj się z [dokumentacja interfejsu API wyszukiwania wideo w niestandardowe](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference) zawartości. Główne różnice to obsługiwanych parametrów zapytania (musi zawierać parametr zapytania customConfig) i wysyłać żądania do punktu końcowego.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
