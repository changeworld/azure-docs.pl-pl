---
title: Czym jest projektu podglądu adresu URL?
titlesuffix: Azure Cognitive Services
description: Wprowadzenie do laboratorium Project URL Preview.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 022fe002057f157d4d9fe6e6cad03fc79810888c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706904"
---
# <a name="what-is-project-url-preview"></a>Czym jest projektu podglądu adresu URL?
Punkt końcowy laboratorium URL Preview pobiera parametr zapytania adresu URL i zwraca odpowiedź JSON z nazwą zasobu docelowego, krótkim opisem i linkiem do obrazu, czyli treściami, które mają zostać wyświetlone w podglądzie. Odpowiedź zawiera także flagę [isFamilyFriendly](url-preview-reference.md#query-parameters), która wskazuje na to, czy adres URL zawiera treści pirackie, dla dorosłych lub inną nielegalną zawartość. 

Aby uzyskać podgląd adresu URL, należy przesłać żądanie GET oraz dołączyć nagłówek *Ocp-Apim-Subscription-Key* z prawidłowym tokenem:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Odpowiedź: 
```
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

```
## <a name="scenarios"></a>Scenariusze 

Interfejs URL Preview API obsługuje krótkie opisy zasobów internetowych. Deweloperzy używają go do tworzenia zaawansowanych podglądów.  Użytkownicy mogą udostępniać lub oznaczać zakładkami strony internetowe, grupy dyskusyjne, blogi, fora itp. Ten interfejs API może również służyć do moderowania zawartości.    

Aplikacje korzystają z laboratorium URL Preview na potrzeby wysyłania żądań internetowych do punktu końcowego za pomocą zapytania przypisanego do adresu URL, dla którego ma zostać wyświetlony podgląd.  Odpowiedź JSON zawiera informacje dotyczące podglądu: nazwę, opis zasobu, flagę *familyFriendly* i linki, które zapewniają dostęp do obrazu odpowiadającego treści podglądu i pełnego zasobu w trybie online. 

## <a name="terms-of-use"></a>Warunki użytkowania
Wyświetlając fragmenty podglądu i miniaturki powiązane linkami z witrynami źródłowymi, należy korzystać wyłącznie z danych udostępnianych w laboratorium Project URL Preview. Dotyczy to udostępniania adresu URL inicjowanego przez użytkownika końcowego w mediach społecznościowych, czatbocie lub podobnych usługach. Nie należy kopiować, przechowywać ani też zapisywać do pamięci podręcznej żadnych danych otrzymywanych z laboratorium Project URL Preview. Należy uwzględniać wszystkie żądania z prośbą o wyłączenie podglądów, które mogą pochodzić od właścicieli treści lub witryn.

Użytkownik ani żaden inny podmiot w jego imieniu nie może używać, zachowywać, przechowywać, zapisywać w pamięci podręcznej, udostępniać ani rozpowszechniać żadnych danych z interfejsu URL Preview API do celów testowania, opracowywania, przeprowadzania szkoleń, rozpowszechniania lub udostępniania jakiejkolwiek usługi lub funkcji firmy innej niż Microsoft. 

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka C#](csharp.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka JavaScript](javascript.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
