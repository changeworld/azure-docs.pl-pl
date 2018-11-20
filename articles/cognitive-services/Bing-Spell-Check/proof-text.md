---
title: Czym jest interfejs API sprawdzania pisowni Bing?
titlesuffix: Azure Cognitive Services
description: Interfejs API sprawdzania pisowni Bing wykorzystuje uczenie maszynowe oraz statystyczne tłumaczenie maszynowe do kontekstowego sprawdzania pisowni.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 3a277b10561b2756fab0af6455d17557a8d93a53
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345712"
---
# <a name="what-is-bing-spell-check-api"></a>Czym jest interfejs API sprawdzania pisowni Bing?

Interfejs API sprawdzania pisowni Bing umożliwia kontekstowe sprawdzanie pisowni i gramatyki.

Jaka jest różnica pomiędzy zwykłymi narzędziami do sprawdzania pisowni a rozwiązaniem trzeciej generacji Bing? Dostępne obecnie narzędzia do sprawdzania pisowni porównują pisownię i gramatykę z zestawami reguł utworzonymi na podstawie słowników, które są okresowo aktualizowane i rozszerzane. Innymi słowy takie narzędzie jest ograniczone przez zawartość słownika, na którym się opiera, oraz wiedzę redaktorów tworzących ten słownik. Tego rodzaju narzędzie do sprawdzania pisowni jest wykorzystywane w programie Microsoft Word i innych edytorach tekstu.

Natomiast usługa Bing oferuje narzędzie do sprawdzania pisowni oparte na sieci, które wykorzystuje uczenie maszynowe i statystyczne tłumaczenie maszynowe, aby dynamicznie szkolić stale rozwijający się i wysoce kontekstowy algorytm. Sprawdzanie pisowni opiera się na olbrzymim korpusie wyszukiwań internetowych i dokumentów.

To narzędzie może obsłużyć dowolny scenariusz dotyczący edytora tekstów:

- Rozpoznaje żargon i język potoczny
- Rozpoznaje typowe błędy nazw w kontekście
- Poprawia błędy dzielenia wyrazów za pomocą jednej flagi
- Poprawia błędne użycie homofonów w zależności od kontekstu i inne trudne do wykrycia błędy
- Rozpoznaje nowo pojawiające się marki, produkty cyfrowe i popularne wyrażenia
- Słowa, które brzmią tak samo, ale różnią się znaczeniem i pisownią, na przykład „może” i „morze”.

## <a name="spell-check-modes"></a>Tryby sprawdzania pisowni

Interfejs API obsługuje dwa tryby sprawdzania: `Proof` i `Spell`.  Wypróbuj przykłady [tutaj](https://azure.microsoft.com/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Proof — scenariusze dotyczące dokumentów
`Proof` to tryb domyślny. Tryb sprawdzania pisowni `Proof` zapewnia najbardziej kompleksowe testy, dodaje wielkie litery i podstawowe znaki interpunkcyjne oraz oferuje inne funkcje pomocne w tworzeniu dokumentu. Jest jednak dostępny tylko w wersji en-US (angielski — Stany Zjednoczone), es-ES (hiszpański) i pt-BR (portugalski). Uwaga: w przypadku języka hiszpańskiego i portugalskiego dostępna jest tylko wersja beta. W przypadku wszystkich pozostałych rynków należy ustawić wartość Spell w parametrze mode w zapytaniu. 
<br /><br/>**UWAGA:** jeśli długość tekstu zapytania przekracza 4096 znaków, tekst zostanie obcięty do 4096 znaków przed przetworzeniem zapytania. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell — scenariusze dotyczące wyszukiwania w Internecie lub zapytań
Tryb `Spell` jest bardziej agresywny i umożliwia zwrócenie lepszych wyników wyszukiwania. Tryb `Spell` odnajduje większość błędów pisowni, ale nie odnajduje części błędów gramatycznych, które wyłapuje tryb `Proof`, na przykład błędów użycia wielkiej litery i powtarzających się słów.

> [!NOTE]
> * Maksymalną obsługiwaną długość zapytania podano poniżej. Jeśli zapytanie przekracza maksymalną długość, zapytanie i jego wyniki nie zostaną zmienione.
>    * 130 znaków dla następujących kodów języka: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh i ko. 
>    * 65 znaków dla wszystkich innych.
> * Tryb sprawdzania pisowni nie obsługuje nawiasów kwadratowych (`[` i `]`) w zapytaniach i może spowodować niespójne wyniki. W przypadku używania trybu pisowni zalecamy usunięcie ich z zapytań.

## <a name="market-setting"></a>Ustawienie rynku
W parametrze zapytania w adresie URL żądania należy określić rynek. W przeciwnym wypadku narzędzie określi domyślny rynek na podstawie adresu IP.


## <a name="post-vs-get"></a>POST a GET

Interfejs API obsługuje żądania HTTP POST i HTTP GET. To, którego użyjesz, zależy od długości tekstu, który chcesz sprawdzić. Jeśli ciągi zawsze mają mniej niż 1500 znaków, należy użyć żądania HTTP GET. Jeśli chcesz, aby obsługiwane były ciągi dłuższe niż 10 000 znaków, należy użyć żądania HTTP POST. Ciąg tekstu może zawierać wszelkie prawidłowe znaki UTF-8.

W poniższym przykładzie przedstawiono żądanie POST sprawdzające pisownię i gramatykę w ciągu tekstowym. Przykład zawiera parametr zapytania [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode), aby pokazać pełne zapytanie (mógł zostać pominięty, ponieważ domyślna wartość parametru `mode` to Proof). Parametr zapytania [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) zawiera ciąg, który ma zostać sprawdzony.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

W przypadku użycia żądania HTTP GET należy uwzględnić parametr zapytania `text` w ciągu zapytania adresu URL.
  
Poniżej przedstawiono odpowiedź na poprzednie żądanie. Odpowiedź zawiera obiekt [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck). 
  
```  
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
W polu [FlaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) wyświetlana jest lista błędów pisowni i gramatyki, które interfejs API odnalazł w ciągu [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text). Pole `token` zawiera wyraz, który ma zostać zastąpiony. Należy użyć przesunięcia zaczynającego się od zera w polu `offset`, aby znaleźć token w ciągu `text`. Następnie należy zastąpić słowo w tej lokalizacji słowem podanym w polu `suggestion`. 

Jeśli pole `type` zawiera wartość RepeatedToken, i tak należy zastąpić token słowem z pola `suggestion`, ale prawdopodobnie należy także usunąć spację końcową.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz [Making Your First Request (Tworzenie pierwszego zapytania)](quickstarts/csharp.md).

Zapoznaj się z [dokumentacją interfejsu API sprawdzania pisowni Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference). Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach wyników wyszukiwania oraz definicje obiektów odpowiedzi. 

Nie zapomnij zapoznać się z [wymaganiami dotyczącymi korzystania z usługi Bing i wyświetlania jej danych](./useanddisplayrequirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników.
