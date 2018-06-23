---
title: Microsoft Translator tekst interfejsu API w wersji 3.0 odwołania | Dokumentacja firmy Microsoft
description: Dokumentacji interfejsu API w wersji 3.0 Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347752"
---
#<a name="translator-text-api-v30"></a>Translator tekst interfejsu API w wersji 3.0

## <a name="whats-new"></a>Co nowego?

Wersja 3 interfejsu API Microsoft Translator tekst zawiera nowoczesnych API sieci Web opartych na formacie JSON. Zwiększa użyteczność i wydajność dzięki konsolidacji istniejących funkcji w mniejszą liczbę operacji i udostępnia nowe funkcje.

 * Transliterację przekonwertować tekst w jednym języku z jednego skryptu na inny skrypt.
 * Tłumaczenie do wielu języków w jednym żądaniu.
 * Wykrywanie języka, tłumaczenia i transliterację w jedno żądanie.
 * Słownik do wyszukiwania alternatywne tłumaczenia terminu, aby znaleźć tłumaczeń Wstecz i przykładami przedstawiający terminów używanych w kontekście.
 * Więcej szczegółowych wyników wykrywania języka.

## <a name="base-urls"></a>Podstawowych adresów URL

Tekst interfejsu API w wersji 3.0 jest dostępna w chmurze następujące:

| Opis | Region | Podstawowy adres URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globalny | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentication

Zasubskrybuj Translator API tekstu w kognitywnych usług firmy Microsoft i korzystania z klucza subskrypcji (dostępne w portalu Azure) do uwierzytelniania. 

Najprostszym sposobem jest przekazanie klucz tajny Azure z usługą Translator przy użyciu nagłówek żądania `Ocp-Apim-Subscription-Key`.

Alternatywą jest uzyskać token autoryzacji z usługi tokenu przy użyciu klucza tajnego. Następnie przekaż token autoryzacji przy użyciu usługi Translator `Authorization` nagłówek żądania. Aby uzyskać token autoryzacji, należy `POST` żądania do następującego adresu URL:

| Środowisko     | Adres URL usługi uwierzytelniania                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Poniżej przedstawiono przykład żądań do uzyskania tokenu podany klucz tajny:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Żądanie powiodło się zwraca token dostępu zakodowane jako zwykły tekst w treści odpowiedzi. Nieprawidłowy token jest przekazywany do usługi translatora jako tokenu elementu nośnego w autoryzacji.

```
Authorization: Bearer <Base64-access_token>
```

Token uwierzytelniania jest ważny przez 10 minut. Token powinna być ponownie używane, gdy wielu wywołań interfejsów API translatora. Jednak program sprawia, że żądania interfejsu API translatora przez dłuższy czas, następnie programu musisz poprosić nowy token dostępu w regularnych odstępach czasu (np. co 8 minut).

Podsumowując, żądanie klienta interfejsu API Translator obejmuje jeden nagłówek autoryzacji z poniższej tabeli:

<table width="100%">
  <th width="30%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>OCP-Apim subskrypcji — klawisz</td>
    <td>*Używane z subskrypcją usługi kognitywnych, jeśli klucz tajny*.<br/>Wartość jest Azure klucz tajny dla Twojej subskrypcji do interfejsu API tekst translatora.</td>
  </tr>
  <tr>
    <td>Autoryzacja</td>
    <td>*Jeśli token uwierzytelniania za pomocą subskrypcji usługi kognitywnych.*<br/>Wartość jest token elementu nośnego: "Bearer <token>".</td>
  </tr>
</table> 

## <a name="errors"></a>Błędy

Błąd standardowy odpowiedzi jest obiekt JSON z pary nazwa/wartość o nazwie `error`. Wartość jest także obiekt JSON z właściwościami:

  * `code`Kod błędu zdefiniowany przez serwer.

  * `message`: Ciąg, podając czytelny dla człowieka reprezentację błędu.

Na przykład klient z bezpłatnej subskrypcji próbnej będzie wyświetlony następujący błąd po wyczerpaniu wolnego przydziału:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
