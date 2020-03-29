---
title: Wyszukiwanie widoku niestandardowego — wyszukiwanie niestandardowe bing
titleSuffix: Azure Cognitive Services
description: Po skonfigurowaniu niestandardowego środowiska wyszukiwania można je przetestować w portalu wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983116"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Wywoływanie wystąpienia wyszukiwania niestandardowego Bing z portalu

Po skonfigurowaniu niestandardowego środowiska wyszukiwania można je przetestować w [portalu](https://customsearch.ai)wyszukiwania niestandardowego Bing . 

![zrzut ekranu portalu wyszukiwania niestandardowego usługi Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Tworzenie kwerendy wyszukiwania 

Po zalogowaniu się do [portalu](https://customsearch.ai)wyszukiwania niestandardowego Bing wybierz wystąpienie wyszukiwania i kliknij kartę **Produkcja.** W **obszarze Punkty końcowe**wybierz punkt końcowy interfejsu API (na przykład interfejs API sieci Web). Subskrypcja określa, jakie punkty końcowe są wyświetlane.

Aby utworzyć kwerendę wyszukiwania, wprowadź wartości parametrów dla punktu końcowego. Należy zauważyć, że parametry wyświetlane w portalu mogą ulec zmianie w zależności od wybranego punktu końcowego. Aby uzyskać więcej informacji, zobacz [odwołanie do interfejsu API wyszukiwania niestandardowego.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) Aby zmienić subskrypcję używa wystąpienia wyszukiwania, dodaj odpowiedni klucz subskrypcji i zaktualizuj odpowiednie parametry rynku i/lub języka.

Poniżej znajdują się kilka ważnych parametrów:


|Parametr  |Opis  |
|---------|---------|
|Zapytanie     | Wyszukiwany termin do wyszukania. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i autosuggest |
|Niestandardowy identyfikator konfiguracji | Identyfikator konfiguracji wybranego wystąpienia wyszukiwania niestandardowego. To pole jest tylko do odczytu. |
|Rynku     | Rynek, z którego wynikają, będzie pochodził. Dostępne tylko dla punktów końcowych interfejsu www, obrazu, wideo i hostowanego interfejsu użytkownika.        |
|Klucz subskrypcji | Klucz subskrypcji do przetestowania. Klucz można wybrać z listy rozwijanej lub wprowadzić go ręcznie.          |

Kliknięcie **przycisku Dodatkowe parametry** ujawnia następujące parametry:  

|Parametr  |Opis  |
|---------|---------|
|Bezpieczne wyszukiwanie     | Filtr używany do filtrowania stron sieci Web pod kątem zawartości dla dorosłych. Dostępne tylko dla punktów końcowych interfejsu www, obrazu, wideo i hostowanego interfejsu użytkownika. Należy pamiętać, że niestandardowe wyszukiwanie `moderate` wideo `strict`bing obsługuje tylko dwie wartości: i .        |
|Język interfejsu użytkownika    | Język używany dla ciągów interfejsu użytkownika. Jeśli na przykład włączysz obrazy i filmy w interfejsie użytkownika hostowanego, karty **Obrazy** i **wideo** będą używać określonego języka.        |
|Liczba     | Liczba wyników wyszukiwania do zwrócenia w odpowiedzi. Dostępne tylko dla punktów końcowych sieci Web, obrazów i wideo.         |
|Przesunięcie    | Liczba wyników wyszukiwania do pominięcia przed zwróceniem wyników. Dostępne tylko dla punktów końcowych sieci Web, obrazów i wideo.        |
    
Po określeniu wszystkich wymaganych opcji kliknij przycisk **Zadzwoń,** aby wyświetlić odpowiedź JSON w prawym okienku. Jeśli wybierzesz punkt końcowy hostowanego interfejsu użytkownika, możesz przetestować środowisko wyszukiwania w dolnym okienku.

## <a name="change-your-bing-custom-search-subscription"></a>Zmienianie subskrypcji wyszukiwania niestandardowego Bing

Subskrypcję skojarzoną z wystąpieniem wyszukiwania niestandardowego Bing można zmienić bez tworzenia nowego wystąpienia. Aby wywołania interfejsu API były wysyłane i naliczane do nowej subskrypcji, utwórz nowy zasób wyszukiwania niestandardowego Bing w witrynie Azure portal. Użyj nowego klucza subskrypcji w żądaniach interfejsu API wraz z identyfikatorem konfiguracji niestandardowej wystąpienia.

## <a name="next-steps"></a>Następne kroki

- [Wywoływanie widoku niestandardowego za pomocą języka C #](./call-endpoint-csharp.md)
- [Wywoływanie widoku niestandardowego za pomocą języka Java](./call-endpoint-java.md)
- [Wywoływanie widoku niestandardowego za pomocą nodejs](./call-endpoint-nodejs.md)
- [Wywoływanie widoku niestandardowego za pomocą języka Python](./call-endpoint-python.md)

- [Wywoływanie widoku niestandardowego za pomocą SDK języka C#](./sdk-csharp-quick-start.md)
