---
title: Wywoływanie interfejsu API usługi Text Analytics
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak można wywołać interfejs API REST usługi Azure Cognitive Services i listonosz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219303"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analizy tekstu

Wywołania **interfejsu API analizy tekstu** są wywołaniami HTTP POST/GET, które można sformułować w dowolnym języku. W tym artykule używamy REST i [Postman,](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) aby zademonstrować kluczowe pojęcia.

Każde żądanie musi zawierać klucz dostępu i punkt końcowy HTTP. Punkt końcowy określa region wybrany podczas rejestracji, adres URL usługi i zasób `languages`używany `entities`na żądanie: `sentiment`, `keyphrases`, i . 

Przypomnijmy, że analiza tekstu jest bezstanowa, więc nie ma żadnych zasobów danych do zarządzania. Tekst jest przekazyany, analizowany po otrzymaniu, a wyniki są natychmiast zwracane do aplikacji wywołującej.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definicja schematu JSON

Dane wejściowe muszą być JSON w nieprzetworzonym tekście nieustrukturyzowanym. XML nie jest obsługiwany. Schemat jest prosty, składający się z elementów opisanych na poniższej liście. 

Obecnie można przesłać te same dokumenty dla wszystkich operacji analizy tekstu: tonacji, frazy kluczowej, wykrywania języka i identyfikacji encji. (Schemat może się różnić dla każdej analizy w przyszłości.)

| Element | Prawidłowe wartości | Wymagana? | Sposób użycia |
|---------|--------------|-----------|-------|
|`id` |Typ danych jest ciągiem, ale w praktyce identyfikatory dokumentów wydają się być liczbami całkowitymi. | Wymagany | System używa identyfikatorów, które udostępniasz do struktury danych wyjściowych. Kody języków, frazy kluczowe i wyniki tonacji są generowane dla każdego identyfikatora w żądaniu.|
|`text` | Nieustrukturyzowany tekst nieprzetworzony, do 5120 znaków. | Wymagany | W przypadku wykrywania języka tekst może być wyrażony w dowolnym języku. W przypadku analizy tonacji, wyodrębniania fraz kluczowych i identyfikacji encji tekst musi być w [obsługiwanym języku](../text-analytics-supported-languages.md). |
|`language` | 2-znakowy kod [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../text-analytics-supported-languages.md) | Różna | Wymagane do analizy tonacji, wyodrębniania fraz kluczowych i łączenia jednostek; opcjonalne wykrywanie języka. Nie ma żadnego błędu, jeśli go wykluczyć, ale analiza jest osłabiona bez niego. Kod języka powinien odpowiadać podasz. `text` |

Aby uzyskać więcej informacji o limitach, zobacz [Omówienie analizy tekstu > limity danych](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Konfigurowanie żądania w listonoszu

Usługa akceptuje żądanie o rozmiarze do 1 MB. Jeśli używasz Postman (lub innego narzędzia do testowania interfejsu API sieci Web), skonfiguruj punkt końcowy, aby uwzględnić zasób, którego chcesz użyć, i podaj klucz dostępu w nagłówku żądania. Każda operacja wymaga dołączenia odpowiedniego zasobu do punktu końcowego. 

1. W Listonoszu:

   + Wybierz **pozycję Opublikuj** jako typ żądania.
   + Wklej w punkcie końcowym skopiowanym ze strony portalu.
   + Dołącz zasób.

   Punkty końcowe zasobów są następujące (region może się różnić):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Ustaw trzy nagłówki żądań:

   + `Ocp-Apim-Subscription-Key`: klucz dostępu uzyskany z witryny Azure portal.
   + `Content-Type`: aplikacja/json.
   + `Accept`: aplikacja/json.

   Żądanie powinno wyglądać podobnie do poniższego zrzutu ekranu, przy założeniu, że **zasób /keyPhrases.**

   ![Zrzut ekranu żądania z punktem końcowym i nagłówkami](../media/postman-request-keyphrase-1.png)

4. Kliknij **pozycję Treść** i wybierz **nieprzetworzone** dla formatu.

   ![Zrzut ekranu żądania z ustawieniami treści](../media/postman-request-body-raw.png)

5. Wklej niektóre dokumenty JSON w formacie, który jest prawidłowy dla zamierzonej analizy. Aby uzyskać więcej informacji na temat konkretnej analizy, zobacz tematy poniżej:

  + [Wykrywanie języka](text-analytics-how-to-language-detection.md)  
  + [Wyodrębnianie fraz kluczowych](text-analytics-how-to-keyword-extraction.md)  
  + [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md)  


6. Kliknij **przycisk Wyślij,** aby przesłać żądanie. Zobacz sekcję [limity danych](../overview.md#data-limits) w przeglądzie, aby uzyskać informacje na temat liczby żądań, które można wysyłać na minutę i sekundę.

   W postman odpowiedź jest wyświetlana w następnym oknie w dół, jako pojedynczy dokument JSON, z elementem dla każdego identyfikatora dokumentu podane w żądaniu.

## <a name="see-also"></a>Zobacz też 

 [Przegląd analizy tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wykrywanie języka](text-analytics-how-to-language-detection.md)
