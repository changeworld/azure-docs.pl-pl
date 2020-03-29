---
title: Korzystanie z zadań moderowania z konsolą INTERFEJSU API REST — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Operacje zadania interfejsu API recenzji umożliwia inicjowanie zadań moderowania zawartości end-to-end dla zawartości obrazu lub tekstu w moderatorze zawartości platformy Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935932"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definiowanie i używanie zadań moderowania (REST)

Zadanie moderowania służy jako rodzaj otoki dla funkcjonalności moderowania zawartości, przepływów pracy i recenzji. W tym przewodniku pokazano, jak za pomocą zadań REST API do inicjowania i sprawdzania zadań moderowania zawartości. Po zrozumieniu struktury interfejsów API można łatwo przenieść te wywołania do dowolnej platformy zgodnej z funkcją REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub utwórz konto w witrynie narzędzia Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)
- (Opcjonalnie) [Zdefiniuj niestandardowy przepływ pracy,](./Review-Tool-User-Guide/Workflows.md) który będzie używany z zadaniem; można również użyć domyślnego przepływu pracy.

## <a name="create-a-job"></a>Tworzenie zadania

Aby utworzyć zadanie moderowania, przejdź do strony Odwołanie [do zadania — Tworzenie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) interfejsu API i wybierz przycisk dla regionu subskrypcji (można go znaleźć w adresie URL punktu końcowego na stronie **Poświadczenia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, gdzie można łatwo konstruować i uruchamiać wywołania interfejsu API REST.

![Zadanie — tworzenie zaznaczenia regionu strony](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź następujące wartości, aby utworzyć wywołanie REST:

- **nazwa zespołu:** identyfikator zespołu utworzony podczas konfigurowania konta [narzędzia recenzja](https://contentmoderator.cognitive.microsoft.com/) (znajdujący się w polu **Identyfikator** na ekranie Poświadczenia narzędzia recenzji).
- **ContentType**: Może to być "Obraz", "Tekst" lub "Wideo".
- **ContentId:** Niestandardowy ciąg identyfikacyjny. Ten ciąg jest przekazywany do interfejsu API i zwracany za pośrednictwem wywołania zwrotnego. Jest to przydatne do kojarzenia identyfikatorów wewnętrznych lub metadanych z wynikami zadania moderowania.
- **Nazwa przepływu pracy:** Nazwa wcześniej utworzonego przepływu pracy (lub "domyślna" dla domyślnego przepływu pracy).
- **CallbackEndpoint**: (Opcjonalnie) Adres URL do odbierania informacji o wywołaniu zwrotnym po zakończeniu przeglądu.
- **Ocp-Apim-Subscription-Key**: Twój klucz moderatora treści. Można to znaleźć na karcie **Ustawienia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Wypełnij treść żądania

Treść wywołania REST zawiera jedno pole **ContentValue**. Wklej nieprzetworzoną zawartość tekstową, jeśli moderujesz tekst, lub wprowadź adres URL obrazu lub filmu, jeśli moderujesz obraz/film. Możesz użyć następującego przykładowego adresu URL obrazu:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Zadanie — tworzenie parametrów kwerendy konsoli, nagłówków i pola treści Żądania](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Prześlij swoją prośbę

Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się pomyślnie, `200 OK`stan **Odpowiedzi** jest , a w polu Zawartość **odpowiedzi** zostanie wyświetlony identyfikator zadania. Skopiuj ten identyfikator, aby użyć go w poniższych krokach.

![Recenzja — w polu Tworzenie zawartości odpowiedzi konsoli jest wyświetlany identyfikator recenzji](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Uzyskaj status zadania

Aby uzyskać stan i szczegóły uruchomionego lub ukończonego zadania, przejdź do strony Odwołanie do [zadania — Pobierz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) interfejs API i wybierz przycisk dla swojego regionu (region, w którym jest administrowany klucz).

![Zadanie — pobierz wybór regionu](images/test-drive-region.png)

Wprowadź parametry wywołania REST, jak w powyższej sekcji. W tym kroku **JobId** jest unikatowym ciągiem identyfikatora otrzymanym podczas tworzenia zadania. Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się pomyślnie, `200 OK`stan **Odpowiedź** jest , a w polu Zawartość **odpowiedzi** jest wyświetlane zadanie w formacie JSON, podobnie jak następujące:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Zadanie — uzyskaj odpowiedź na połączenie REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Sprawdź nowe przeglądy

Jeśli zadanie dotyczące zawartości spowodowało utworzenie recenzji, można ją wyświetlić w [narzędziu Recenzja](https://contentmoderator.cognitive.microsoft.com). Wybierz **opcję Przejrzyj** > **film tekstowy**/**Video** **obrazu**/(w zależności od użytej zawartości). Treść powinna być wyświetlana, gotowa do przeglądu przez człowieka. Po moderator człowieka przegląda automatycznie przypisane tagi i dane przewidywania i przesyła ostateczną decyzję moderacji, zadania interfejsu API przesyła wszystkie te informacje do wyznaczonego punktu końcowego wywołania zwrotnego.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak tworzyć i wysyłać zapytania do zadań moderowania zawartości przy użyciu interfejsu API REST. Następnie należy zintegrować zadania ze scenariuszem moderowania end-to-end, takim jak samouczek [moderowania w handlu elektronicznym.](./ecommerce-retail-catalog-moderation.md)