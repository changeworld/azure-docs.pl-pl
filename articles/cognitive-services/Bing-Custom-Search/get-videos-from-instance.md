---
title: Pobierz filmy z widoku niestandardowego - Wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Omówienie wysokiego poziomu dotyczące korzystania z niestandardowego wyszukiwania Bing w celu uzyskania klipów wideo z niestandardowego widoku sieci Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 222256036a59c7df302546bbf82648c4d524d43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68405093"
---
# <a name="get-videos-from-your-custom-view"></a>Otrzymuj filmy z widoku niestandardowego

Wyszukiwanie niestandardowych filmów wideo bing pozwala wzbogacić niestandardowe środowisko wyszukiwania o filmy. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie wideo w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. Możesz uzyskać filmy za pomocą niestandardowego interfejsu API wyszukiwania wideo bing lub za pomocą funkcji Hosted UI. Korzystanie z funkcji Hosted UI jest proste w użyciu i zalecane do uruchamiania wyszukiwania w krótkim czasie. Aby uzyskać informacje dotyczące konfigurowania hostowanego interfejsu użytkownika w celu dołączania klipów wideo, zobacz [Konfigurowanie hostowanego interfejsu użytkownika](hosted-ui.md).

Jeśli chcesz mieć większą kontrolę nad wyświetlaniem wyników wyszukiwania, możesz użyć interfejsu API wyszukiwania niestandardowych filmów wideo bing. Ponieważ wywołanie interfejsu API jest podobne do wywoływania interfejsu API wyszukiwania wideo Bing, wyewidencjonuj [wyszukiwanie wideo Bing](../Bing-Video-Search/search-the-web.md) w celu uzyskania przykładów wywoływania interfejsu API. Zanim jednak to zrobisz, zapoznaj się z zawartością [referencyjną interfejsu API wyszukiwania niestandardowych filmów.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) Główne różnice są obsługiwane parametry kwerendy (należy dołączyć parametr kwerendy customConfig) i punkt końcowy, do którego wysyłasz żądania.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
