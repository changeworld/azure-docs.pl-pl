---
title: Definiowanie niestandardowego automatycznego sugerowania sugestie — Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Opisuje sposób konfigurowania niestandardowego automatycznego sugerowania przy użyciu niestandardowych sugestii
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bbad72b41a177bdbafd6cf98bfd2025190d98b16
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128961"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurowanie środowiska funkcję niestandardowego automatycznego sugerowania

Funkcję niestandardowego automatycznego sugerowania zwraca listę ciągów zapytania sugerowane wyszukiwania, które mają zastosowanie do środowiska wyszukiwania. Ciągi zapytań sugerowane są oparte na ciąg zapytania częściowego, który użytkownik udostępnia w polu wyszukiwania. Lista będzie zawierać maksymalnie 10 sugestie. 

Należy określić, czy zwracać tylko niestandardowych sugestii, czy też zawierają sugestie Bing. Jeśli dodasz sugestie Bing, niestandardowych sugestii występować przed sugestie Bing. Jeśli podasz za mało istotne sugestie, istnieje możliwość, zwracana lista sugestii będzie zawiera sugestie Bing. Sugestie Bing są zawsze w kontekście wystąpienia wyszukiwania niestandardowego. 

Aby skonfigurować sugestie dotyczące zapytań wyszukiwania dla swojego wystąpienia, kliknij **automatycznego sugerowania** kartę.  

> [!NOTE]
> Aby użyć tej funkcji, należy zasubskrybować wyszukiwania niestandardowego na odpowiednim poziomie (zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Może upłynąć do 24 godzin sugestie, które zostaną odzwierciedlone w punkcie końcowym obsługująca (interfejs API lub hostowanej interfejsu użytkownika).

## <a name="enable-bing-suggestions"></a>Włącz sugestie Bing

Aby włączyć sugestie Bing, Przełącz **sugestie automatyczne Bing** suwak w pozycji. Suwak staje się niebieski.

## <a name="add-your-own-suggestions"></a>Dodać własne sugestie

Aby dodać własne sugestie ciągu zapytania, należy dodać je do listy w obszarze **zdefiniowanych przez użytkownika sugestie**. Po dodaniu sugestii na liście, naciśnij klawisz enter lub kliknij przycisk **+** ikony. Możesz określić sugestię w dowolnym języku. Możesz dodać maksymalnie 5000 sugestie ciągu zapytania.

## <a name="upload-suggestions"></a>Przekazywanie sugestii

Opcjonalnie możesz przekazać listę sugestii pochodzących z pliku. Plik musi zawierać jeden ciąg zapytania wyszukiwania dla każdego wiersza. Aby przekazać plik, kliknij ikonę przekazywania i wybierz plik do przekazania. Usługa wyodrębnia sugestie z pliku i dodaje je do listy.

## <a name="remove-suggestions"></a>Usuń sugestie

Aby usunąć sugestię ciągu zapytania, kliknij ikonę Usuń obok sugestii, który chcesz usunąć.

## <a name="block-suggestions"></a>Blokuj sugestie

Jeśli dodasz sugestie Bing, można dodać listę ciągów zapytania wyszukiwania nie chcesz, aby Bing do zwrócenia. Aby dodać ciągów zablokowanych zapytania, kliknij **Pokaż zablokowane sugestie**. Dodaj ciąg zapytania do listy i naciśnij klawisz enter lub kliknij przycisk **+** ikony. Możesz dodać maksymalnie 50 ciągi zablokowanych zapytań.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Może upłynąć do 24 godzin niestandardowego automatycznego sugerowania zmiany konfiguracji zostały wprowadzone.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Włączanie automatycznego sugerowania w interfejsie użytkownika usług hostowanych

Aby włączyć podpowiedzi dla ciągu zapytania dla obsługiwanego interfejsu użytkownika, kliknij przycisk **hostowanych interfejsu użytkownika**. Przewiń w dół do **dodatkowych czynności konfiguracyjnych** sekcji. W obszarze **wyszukiwania w Internecie**, wybierz opcję **na** dla **włączenie automatycznego sugerowania**. Aby włączyć automatyczne sugerowanie, musisz wybrać układ, który zawiera pole wyszukiwania.


## <a name="calling-the-autosuggest-api"></a>Wywołanie API automatycznego sugerowania

Aby uzyskać ciągów sugerowane zapytania za pomocą interfejsu API wyszukiwania niestandardowego Bing, Wyślij `GET` żądanie następujący punkt końcowy.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Odpowiedź zawiera listę `SearchAction` obiektów zawierających ciągi zapytań sugerowane.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Obejmuje każdego sugestii `displayText` i `query` pola. `displayText` Pole zawiera ciąg sugerowane zapytania, których używasz do wypełnienia listy rozwijanej pole wyszukiwania.

Jeśli użytkownik wybierze ciąg sugerowane zapytania z listy rozwijanej, użyj ciągu zapytania w `query` pola podczas wywoływania [interfejs API wyszukiwania niestandardowego Bing](overview.md).


## <a name="next-steps"></a>Kolejne kroki

- [Pobierz sugestie dotyczące niestandardowych](./get-custom-suggestions.md)
- [Wyszukaj wystąpienia niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie i używanie niestandardowych hostowanej interfejsu użytkownika](./hosted-ui.md)
