---
title: Definiowanie niestandardowych sugestii autosuefokaszowych — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Niestandardowy autosugeruje listę sugerowanych ciągów zapytań wyszukiwania, które są istotne dla środowiska wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072800"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Konfigurowanie niestandardowego środowiska autosugestii

Niestandardowy autosugeruje listę sugerowanych ciągów zapytań wyszukiwania, które są istotne dla środowiska wyszukiwania. Sugerowane ciągi zapytań są oparte na częściowym ciągu zapytania, który użytkownik udostępnia w polu wyszukiwania. Lista będzie zawierać maksymalnie 10 sugestii. 

Należy określić, czy zwracać tylko sugestie niestandardowe, czy też dołączać sugestie Bing. Jeśli uwzględnisz sugestie Bing, sugestie niestandardowe pojawią się przed sugestiami Bing. Jeśli podasz wystarczająco dużo odpowiednich sugestii, możliwe, że zwrócona lista sugestii nie będzie zawierać sugestii Bing. Sugestie Bing są zawsze w kontekście wystąpienia wyszukiwania niestandardowego. 

Aby skonfigurować sugestie dotyczące zapytań wyszukiwania dla wystąpienia, kliknij kartę **Autosuggest.**  

> [!NOTE]
> Aby korzystać z tej funkcji, należy zasubskrybować wyszukiwanie niestandardowe na odpowiednim poziomie (patrz [cennik).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Może upłynąć do 24 godzin dla sugestii, które mają być odzwierciedlone w punkt końcowy obsługujących (INTERFEJSU API lub hostowanego interfejsu użytkownika).

## <a name="enable-bing-suggestions"></a>Włączanie sugestii Bing

Aby włączyć sugestie Bing, przełącz suwak **Sugestie automatycznego bingu** do pozycji włączonej. Suwak staje się niebieski.

## <a name="add-your-own-suggestions"></a>Dodawanie własnych sugestii

Aby dodać własne sugestie ciągów zapytań, dodaj je do listy w obszarze **Sugestie zdefiniowane przez użytkownika**. Po dodaniu sugestii na liście naciśnij klawisz **+** Enter lub kliknij ikonę. Sugestię można określić w dowolnym języku. Można dodać maksymalnie 5000 sugestii ciągów zapytań.

## <a name="upload-suggestions"></a>Przekazywanie sugestii

Opcjonalnie możesz przesłać listę sugestii z pliku. Plik musi zawierać jeden ciąg zapytania wyszukiwania w wierszu. Aby przekazać plik, kliknij ikonę przekazywania i wybierz plik do przekazania. Usługa wyodrębnia sugestie z pliku i dodaje je do listy.

## <a name="remove-suggestions"></a>Usuwanie sugestii

Aby usunąć sugestię ciągu zapytania, kliknij ikonę usuwania obok sugestii, którą chcesz usunąć.

## <a name="block-suggestions"></a>Zablokuj sugestie

Jeśli uwzględnisz sugestie Bing, możesz dodać listę ciągów zapytań wyszukiwania, których nie chcesz, aby bing zwracał. Aby dodać zablokowane ciągi zapytań, kliknij pozycję **Pokaż zablokowane sugestie**. Dodaj ciąg zapytania do listy i naciśnij klawisz **+** Enter lub kliknij ikonę. Można dodać maksymalnie 50 zablokowanych ciągów zapytań.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Wprowadzenie zmian konfiguracji niestandardowej konfiguracji autosugesty może potrwać do 24 godzin.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Włączanie autosuefosty w interfejsie hostowanym

Aby włączyć sugestie dotyczące ciągów zapytań dla hostowanego interfejsu użytkownika, kliknij pozycję **Hostowany interfejs użytkownika**. Przewiń w dół do sekcji **Konfiguracja dodatkowa.** W **obszarze Wyszukiwanie w sieci Web**wybierz pozycję **Włącz** dla **włącz autosuggest**. Aby włączyć autosugestię, należy wybrać układ zawierający pole wyszukiwania.


## <a name="calling-the-autosuggest-api"></a>Wywoływanie interfejsu API autosuchłonu

Aby uzyskać sugerowane ciągi zapytań przy użyciu `GET` interfejsu API wyszukiwania niestandardowego Bing, wyślij żądanie do następującego punktu końcowego.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Odpowiedź zawiera listę `SearchAction` obiektów, które zawierają sugerowane ciągi zapytania.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Każda sugestia `displayText` `query` zawiera i pole. To `displayText` pole zawiera sugerowany ciąg zapytania używany do wypełniania listy rozwijanej pola wyszukiwania.

Jeśli użytkownik wybierze sugerowany ciąg zapytania z listy rozwijanej, `query` użyj ciągu zapytania w tym polu podczas [wywoływania interfejsu API wyszukiwania niestandardowego Bing](overview.md).


## <a name="next-steps"></a>Następne kroki

- [Pobieranie niestandardowych sugestii](./get-custom-suggestions.md)
- [Wyszukiwanie wystąpienia niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie i używanie niestandardowego hostowanego interfejsu użytkownika](./hosted-ui.md)
