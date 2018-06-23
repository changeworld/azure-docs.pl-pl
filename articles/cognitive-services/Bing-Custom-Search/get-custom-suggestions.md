---
title: 'Wyszukiwanie niestandardowe Bing: Niestandardowy automatycznego sugerowania sugestie | Dokumentacja firmy Microsoft'
description: Zawiera opis sposobu pobierania sugestii Autosugerowanie niestandardowych
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347857"
---
# <a name="get-custom-suggestions"></a>Pobierz sugestie dotyczące niestandardowych
Przed wysłaniem kwerendy do wyszukiwania usługi Bing niestandardowe, wywołania API automatycznego sugerowania niestandardowe sugestie terminu wyszukiwania i ulepszaniu środowiska wyszukiwania. Interfejs API automatycznego sugerowania niestandardowe zwraca listę sugerowane zapytań opartych na ciąg zapytania częściowy, który zawiera użytkownika. Wszystkie warunki odpowiednich niestandardowe zapytanie, przez użytkownika są wyświetlane przed sugestii, które generuje Autosugerowanie. Aby uzyskać więcej informacji, zobacz [zdefiniować sugestie dotyczące wyszukiwania niestandardowego](define-custom-suggestions.md).

## <a name="endpoint"></a>Endpoint
Aby uzyskać sugerowane zapytań przy użyciu interfejsu API wyszukiwania usługi Bing niestandardowe, Wyślij `GET` żądanie następujący punkt końcowy.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Odpowiedź JSON
Odpowiedź zawiera listę obiektów SearchAction, które zawierają postanowienia sugerowane zapytania.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Obejmuje każdego sugestię `displayText` i `query` pola. `displayText` Pole zawiera sugerowane zapytania, którego używasz do wypełnienia listy rozwijanej pole wyszukiwania.

Jeśli użytkownik wybierze sugerowane zapytania z listy rozwijanej, użyj wyszukiwanego terminu w `query` podczas wywoływania [interfejsu API Bing niestandardowe wyszukiwania](overview.md).

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Wywołanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)