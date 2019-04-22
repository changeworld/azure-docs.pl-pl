---
title: Tworzenie przeglądy Moderowanie przy użyciu konsoli interfejsu API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Za pomocą Content Moderator przeglądu interfejsów API usługi Azure, utworzyć obraz lub tekst przeglądy dotyczące moderowania ludzi.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882022"
---
# <a name="create-human-reviews-rest"></a>Utwórz ludziom dokonywanie (REST)

[Przeglądy](./review-api.md#reviews) przechowywania i wyświetlania zawartości dla moderatorów ludzi do oceny. Po ukończeniu weryfikacji użytkownika wyniki są wysyłane do punktu końcowego określonego wywołania zwrotnego. W tym przewodniku dowiesz się, jak skonfigurować przeglądy przy użyciu przeglądu interfejsów API REST za pośrednictwem konsoli interfejsu API. Po zrozumieniu struktury interfejsów API można łatwo portu tych wywołań na dowolną platformę zgodną REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji.

## <a name="create-a-review"></a>Utwórz Przegląd

O utworzenie recenzji, przejdź do **[Przejrzyj — tworzenie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API odwoływać się do strony i wybierz przycisk w Twoim regionie klucza (można znaleźć ten adres URL punktu końcowego na **poświadczenia** strony z [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, w którym można łatwo utworzyć i uruchomienia wywołań interfejsu API REST.

![Przegląd — pole region Get](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź wartości w polach **teamName**, i **Ocp-Apim-Subscription-Key**:

- **teamName**: Identyfikator zespołu, który został utworzony podczas konfigurowania usługi [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) konta (w **identyfikator** pola na ekranie poświadczenia tego narzędzia przeglądu).
- **Ocp-Apim-Subscription-Key**: Klucz usługi Content Moderator. Można je znaleźć na **ustawienia** karcie [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Wprowadź definicję przeglądu

Edytuj **treść żądania** pole, aby wprowadzić żądania JSON przy użyciu następujących pól:

- **metadane**: Niestandardowe pary klucz wartość do zwrócenia do punktu końcowego usługi wywołania zwrotnego. Jeśli klucz jest krótki kod, który jest zdefiniowany w [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com), będzie ono wyświetlane jako tag.
- **Zawartość**: W przypadku obrazów i zawartości wideo jest to ciąg adres URL wskazuje zawartość. Zawartość tekstowa jest to ciąg tekstowy rzeczywistych.
- **ContentId**: Niestandardowego ciągu identyfikatora. Ten ciąg jest przekazywany do interfejsu API i zwrócone za pośrednictwem wywołania zwrotnego. Jest to przydatne kojarzenia identyfikatory wewnętrznego lub metadanych z wyników zadania moderowania.
- **CallbackEndpoint**: (Opcjonalnie) Adres URL do odbierania informacji o wywołaniu zwrotnym, po zakończeniu przeglądu.

Domyślną treść żądania zawiera przykłady różnych rodzajów recenzje, które można utworzyć:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** okno wyświetla identyfikator przeglądu. Skopiuj ten identyfikator do użycia w kolejnych krokach.

![Przejrzyj — Tworzenie konsoli zawartości, że pole zawiera identyfikator przeglądu odpowiedzi](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Sprawdź nowy przegląd

W [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com), wybierz opcję **przeglądu** > **obraz**/**tekstu** / **Wideo** (w zależności od zawartości użyto). Powinna zostać wyświetlona zawartość, który został przekazany, gotowa do przeglądu przez ludzi.

![Obraz narzędzie do przeglądu piłka nożna](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Pobierz szczegóły przeglądu

Aby uzyskać szczegółowe informacje o istniejący Przegląd, przejdź do [Przejrzyj — Pobierz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API odwoływać się do strony i wybierz przycisk w Twoim regionie (region, w którym jest podawana klucz).

![Przepływ pracy - wybór regionu Get](images/test-drive-region.png)

Wprowadź parametry wywołania REST, tak jak w powyższej sekcji. W tym kroku **reviewId** jest unikatowy ciąg Identyfikatora otrzymany po utworzeniu przeglądu.

![Przejrzyj — Tworzenie konsoli wyniki Get](images/test-drive-review-3.PNG)
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** wyświetlone szczegóły przeglądu w formacie JSON, takich jak następujące:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Zwróć uwagę na następujące pola w odpowiedzi:

- **status**
- **reviewerResultTags**: To jest wyświetlana, jeśli ręcznie dodano żadnych tagów przez zespół przeglądu przez ludzi (pokazano **createdBy** pola).
- **metadane**: Pokazuje to tagi, które początkowo zostały dodane w przeglądzie, przed wprowadzeniem zmian zespół przeglądu przez ludzi.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób tworzenia przeglądów moderowanie zawartości przy użyciu interfejsu API REST. Następnie zintegrować przeglądy scenariusz end-to-end Moderowanie takich jak [Moderowanie handlu elektronicznego](./ecommerce-retail-catalog-moderation.md) samouczka.