---
title: Przeszukiwanie widoku niestandardowego — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Opisuje sposób przeszukiwania niestandardowego widoku sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 814f57d4011823da80e53cce41ffcb523fc0bf1b
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405005"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Wywoływanie wystąpienia wyszukiwanie niestandardowe Bing z portalu

Po skonfigurowaniu niestandardowego środowiska wyszukiwania można je przetestować z poziomu [portalu](https://customsearch.ai)wyszukiwanie niestandardowe Bing. 

![zrzut ekranu portalu wyszukiwania niestandardowego Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Utwórz zapytanie wyszukiwania 

Po zalogowaniu się do [portalu](https://customsearch.ai)wyszukiwanie niestandardowe Bing wybierz wystąpienie wyszukiwania i kliknij kartę **produkcja** . W obszarze **punkty końcowe**wybierz punkt końcowy interfejsu API (na przykład internetowy interfejs API). Twoja subskrypcja określa, jakie punkty końcowe są wyświetlane.

Aby utworzyć zapytanie wyszukiwania, wprowadź wartości parametrów dla punktu końcowego. Należy pamiętać, że parametry wyświetlane w portalu mogą ulec zmianie w zależności od wybranego punktu końcowego. Zobacz [odwołanie interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) , aby uzyskać więcej informacji. Aby zmienić subskrypcję używaną przez wystąpienie wyszukiwania, Dodaj odpowiedni klucz subskrypcji i zaktualizuj odpowiednie parametry rynku i/lub języka.

Poniżej przedstawiono kilka ważnych parametrów:


|Parametr  |Opis  |
|---------|---------|
|Zapytanie     | Termin wyszukiwania, który ma zostać wyszukany. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i automatycznego sugerowania |
|Identyfikator konfiguracji niestandardowej | Identyfikator konfiguracji wybranego wystąpienia wyszukiwania niestandardowego. To pole jest tylko do odczytu. |
|Market     | Rynek, z którego pochodzą wyniki. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i hostowanego interfejsu użytkownika.        |
|Klucz subskrypcji | Klucz subskrypcji do przetestowania. Możesz wybrać klucz z listy rozwijanej lub wprowadzić go ręcznie.          |

Kliknięcie **dodatkowych parametrów** ujawnia następujące parametry:  

|Parametr  |Opis  |
|---------|---------|
|Safe Search     | Filtr używany do filtrowania stron sieci Web na potrzeby zawartości dla dorosłych. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i hostowanego interfejsu użytkownika.        |
|Język interfejsu użytkownika    | Język używany dla ciągów interfejsu użytkownika. Na przykład jeśli włączysz obrazy i wideo w hostowanym interfejsie użytkownika, karty **obrazu** i **wideo** używają określonego języka.        |
|Count     | Liczba wyników wyszukiwania do zwrócenia w odpowiedzi. Dostępne tylko dla punktów końcowych sieci Web, obrazów i wideo.         |
|Offset    | Liczba wyników wyszukiwania do pominięcia przed zwróceniem wyników. Dostępne tylko dla punktów końcowych sieci Web, obrazów i wideo.        |
    
Po określeniu wszystkich wymaganych opcji kliknij pozycję **Połącz** , aby wyświetlić odpowiedź JSON w okienku po prawej stronie. W przypadku wybrania punktu końcowego hostowanego interfejsu użytkownika można testować środowisko wyszukiwania w dolnym okienku.

## <a name="change-your-bing-custom-search-subscription"></a>Zmień subskrypcję usługi wyszukiwanie niestandardowe Bing

Subskrypcję skojarzoną z wystąpieniem wyszukiwanie niestandardowe Bing można zmienić bez tworzenia nowego wystąpienia. Aby wywołania interfejsu API były wysyłane i obciążane do nowej subskrypcji, Utwórz nowy zasób wyszukiwanie niestandardowe Bing w Azure Portal. Użyj nowego klucza subskrypcji w żądaniach interfejsu API wraz z niestandardowym IDENTYFIKATORem konfiguracji wystąpienia.

## <a name="next-steps"></a>Kolejne kroki

- [Wywoływanie widoku niestandardowego zC#](./call-endpoint-csharp.md)
- [Wywoływanie niestandardowego widoku przy użyciu języka Java](./call-endpoint-java.md)
- [Wywoływanie niestandardowego widoku przy użyciu NodeJs](./call-endpoint-nodejs.md)
- [Wywoływanie niestandardowego widoku przy użyciu języka Python](./call-endpoint-python.md)

- [Wywoływanie niestandardowego widoku przy użyciu C# zestawu SDK](./sdk-csharp-quick-start.md)
