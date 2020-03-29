---
title: Pobierz obrazy z widoku niestandardowego — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Omówienie wysokiego poziomu dotyczące korzystania z niestandardowego wyszukiwania Bing w celu uzyskania obrazów z niestandardowego widoku sieci Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390328"
---
# <a name="get-images-from-your-custom-view"></a>Pobierz obrazy z widoku niestandardowego

Wyszukiwanie obrazów niestandardowych bing umożliwia wzbogacenie niestandardowego środowiska wyszukiwania o obrazy. Podobnie jak w przypadku wyników z Internetu interfejs API wyszukiwania niestandardowego obsługuje wyszukiwanie obrazów w witrynach internetowych uwzględnionych na liście w Twoim wystąpieniu. Obrazy można uzyskać za pomocą interfejsu API wyszukiwania obrazów niestandardowych bing lub za pośrednictwem funkcji hostowanego interfejsu użytkownika. Korzystanie z funkcji Hosted UI jest proste w użyciu i zalecane do uruchamiania wyszukiwania w krótkim czasie.  Aby uzyskać informacje dotyczące konfigurowania hostowanego interfejsu użytkownika w celu dołączania obrazów, zobacz [Konfigurowanie hostowanego interfejsu użytkownika](hosted-ui.md).

Jeśli chcesz mieć większą kontrolę nad wyświetlaniem wyników wyszukiwania, możesz użyć interfejsu API wyszukiwania obrazów niestandardowych bing. Ponieważ wywołanie interfejsu API jest podobne do wywoływania interfejsu API wyszukiwania obrazów Bing, wyewidencjonuj [wyszukiwanie obrazów Bing](../Bing-Image-Search/overview.md) w przykładach wywoływania interfejsu API. Zanim jednak to zrobisz, zapoznaj się z zawartością [referencyjną interfejsu API wyszukiwania obrazów niestandardowych.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) Główne różnice są obsługiwane parametry kwerendy (należy dołączyć parametr kwerendy customConfig) i punkt końcowy, do którego wysyłasz żądania.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
