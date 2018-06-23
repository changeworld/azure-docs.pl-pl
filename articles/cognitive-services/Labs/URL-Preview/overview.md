---
title: Co to jest podgląd adres URL projektu? -Microsoft kognitywnych usług | Dokumentacja firmy Microsoft
description: Wprowadzenie do podglądu adres URL projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348609"
---
# <a name="what-is-project-url-preview"></a>Co to jest podgląd adres URL projektu?
Punkt końcowy adres URL podglądu przyjmuje parametr zapytania adresu URL i zwraca odpowiedź w formacie JSON z nazwą zasobu docelowego, krótki opis i łącze do obrazu do wyświetlenia w podglądzie. Odpowiedź zawiera także [isFamilyFriendly](url-preview-reference.md#query-parameters) flagę wskazującą, czy adres URL zawiera niedozwolony zawartość dla dorosłych, pirackie lub innych. 

Aby uzyskać adres URL Podgląd wyników, Prześlij żądanie GET i obejmują *Ocp-Apim-subskrypcji — klucz* nagłówek z prawidłowym tokenem:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Odpowiedź: 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>Scenariusze 

Adres URL interfejsu API w wersji zapoznawczej obsługuje krótkie opisy zasobów sieci Web. Deweloperzy Użyj, aby tworzyć środowiska podglądu.  Użytkownicy mogą udostępniać lub zakładki stron sieci Web, grup dyskusyjnych, blogów, forów itp. Ten interfejs API można również uzyskać łagodzenia zawartości.    

Aplikacje Podgląd adres URL do wysyłania żądań sieci Web do punktu końcowego za pomocą kwerendy przypisana do adresu URL do podglądu.  Odpowiedź JSON zawiera informacje o wersji zapoznawczej: nazwa, opis zasobu, *familyFriendly* flagę i linki, które zapewniają dostęp do reprezentatywnego obrazu oraz pełną zasób w tryb online. 

## <a name="terms-of-use"></a>Warunki użytkowania
Tylko dane z wersji zapoznawczej adres URL projektu umożliwia wyświetlenie wstawki podglądu i miniatur Link do ich lokacji źródłowych w adresie URL inicjowanych przez użytkownika końcowego, udostępniania mediów społecznościowych, rozmów bot lub podobne oferty. D nie kopiuje magazynu lub pamięci podręcznej danych otrzymywanych z podglądu adres URL projektu. Należy uwzględnić wszystkie żądania, aby wyłączyć podglądy, które mogą otrzymywać Właściciele witryny sieci Web lub zawartości.

Możesz lub innej firmy w Twoim imieniu może nie, Zachowaj, przechowywania, pamięci podręcznej, udział, albo dystrybucji żadnych danych z interfejsu API w wersji zapoznawczej adres URL do testowania, tworzenie, uczenie, dystrybucja lub udostępnia usługi firmy Microsoft lub funkcji. 

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](csharp.md)
- [Szybki Start Java](java-quickstart.md)
- [JavaScript — Szybki Start](javascript.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)
