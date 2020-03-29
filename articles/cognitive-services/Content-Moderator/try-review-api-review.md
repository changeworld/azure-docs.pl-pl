---
title: Tworzenie recenzji moderowania za pomocą konsoli INTERFEJSU API REST — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Użyj interfejsów API przeglądu moderatora zawartości platformy Azure, aby utworzyć recenzje obrazów lub tekstu dla umiaru ludzkiego.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757136"
---
# <a name="create-human-reviews-rest"></a>Tworzenie ludzkich recenzji (REST)

[Recenzje przechowują](./review-api.md#reviews) i wyświetlają treści dla moderatorów ludzkich do oceny. Gdy użytkownik zakończy przegląd, wyniki są wysyłane do określonego punktu końcowego wywołania zwrotnego. W tym przewodniku dowiesz się, jak skonfigurować recenzje przy użyciu interfejsów API recydywki za pośrednictwem konsoli interfejsu API. Po zrozumieniu struktury interfejsów API można łatwo przenieść te wywołania do dowolnej platformy zgodnej z funkcją REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub utwórz konto w witrynie narzędzia Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-review"></a>Tworzenie recenzji

Aby utworzyć recenzję, przejdź do strony **[Recenzja — Tworzenie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** interfejsu API i wybierz przycisk dla regionu klucza (można go znaleźć w adresie URL punktu końcowego na stronie **Poświadczenia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, gdzie można łatwo konstruować i uruchamiać wywołania interfejsu API REST.

![Recenzja — wybór regionu](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź wartości **dla teamName**i **Ocp-Apim-Subscription-Key**:

- **nazwa zespołu:** identyfikator zespołu utworzony podczas konfigurowania konta [narzędzia recenzja](https://contentmoderator.cognitive.microsoft.com/) (znajdujący się w polu **Identyfikator** na ekranie Poświadczenia narzędzia recenzji).
- **Ocp-Apim-Subscription-Key**: Twój klucz moderatora treści. Można to znaleźć na karcie **Ustawienia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Wprowadzanie definicji recenzji

Edytuj pole **treści Żądanie,** aby wprowadzić żądanie JSON z następującymi polami:

- **Metadane:** Niestandardowe pary klucz-wartość mają być zwracane do punktu końcowego wywołania zwrotnego. Jeśli klucz jest krótkim kodem zdefiniowanym w [narzędziu Recenzja,](https://contentmoderator.cognitive.microsoft.com)jest on wyświetlany jako znacznik.
- **Zawartość:** W przypadku zawartości obrazu i wideo jest to ciąg adresu URL wskazujący na zawartość. W przypadku zawartości tekstowej jest to rzeczywisty ciąg tekstowy.
- **ContentId:** Niestandardowy ciąg identyfikacyjny. Ten ciąg jest przekazywany do interfejsu API i zwracany za pośrednictwem wywołania zwrotnego. Jest to przydatne do kojarzenia identyfikatorów wewnętrznych lub metadanych z wynikami zadania moderowania.
- **CallbackEndpoint**: (Opcjonalnie) Adres URL do odbierania informacji o wywołaniu zwrotnym po zakończeniu przeglądu.

Domyślna treść żądania zawiera przykłady różnych typów recenzji, które można utworzyć:

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

### <a name="submit-your-request"></a>Prześlij swoją prośbę
  
Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się pomyślnie, `200 OK`stan **Odpowiedzi** jest , a w polu Zawartość **odpowiedzi** zostanie wyświetlony identyfikator przeglądu. Skopiuj ten identyfikator, aby użyć go w poniższych krokach.

![Recenzja — w polu Tworzenie zawartości odpowiedzi konsoli jest wyświetlany identyfikator recenzji](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Zapoznaj się z nowym przeglądem

W [narzędziu Recenzja](https://contentmoderator.cognitive.microsoft.com)wybierz **pozycję Przejrzyj** > **film**/**tekstowy**/**Video** obrazu (w zależności od użytej zawartości). Przesłana zawartość powinna być wyświetlana, gotowa do przeglądania przez człowieka.

![Przejrzyj obraz narzędzia piłki nożnej](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Uzyskaj szczegółowe informacje o recenzji

Aby pobrać szczegółowe informacje o istniejącej recenzji, przejdź do [strony Recenzja — Pobierz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) dostęp do interfejsu API i wybierz przycisk dla swojego regionu (region, w którym jest administrowany klucz).

![Przepływ pracy — pobierz wybór regionu](images/test-drive-region.png)

Wprowadź parametry wywołania REST, jak w powyższej sekcji. W tym kroku **reviewId** jest unikatowy ciąg identyfikatora otrzymane podczas tworzenia przeglądu.

![Recenzja — tworzenie konsoli Uzyskaj wyniki](images/test-drive-review-3.PNG)
  
Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się pomyślnie, `200 OK`stan **Odpowiedź** jest , a w polu Zawartość **odpowiedzi** są wyświetlane szczegóły przeglądu w formacie JSON, takie jak następujące:

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

Zanotuj następujące pola w odpowiedzi:

- **Stan**
- **reviewerResultTags**: Pojawia się, jeśli wszystkie tagi zostały ręcznie dodane przez zespół przeglądu człowieka (pokazano **createdBy** pola).
- **metadane**: Pokazuje tagi, które zostały początkowo dodane w recenzji, zanim zespół przeglądu człowieka wprowadzone zmiany.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak tworzyć przeglądy moderowania zawartości przy użyciu interfejsu API REST. Następnie zintegruj opinie ze scenariuszem moderowania end-to-end, takim jak samouczek [moderowania handlu elektronicznego.](./ecommerce-retail-catalog-moderation.md)