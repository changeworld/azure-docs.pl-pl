---
title: Moderowanie zadania za pomocą konsoli interfejsu API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Użyj operacji zadań Przegląd interfejsu API do inicjowania zadań end-to-end moderowanie zawartości dla zawartości image lub text w usłudze Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607563"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definiowanie i korzystanie z zadań moderation (REST)

Zadania Moderowanie służy jako typ otoki dla funkcji moderowania zawartości, przepływy pracy i recenzje. Ten przewodnik pokazuje, jak użyć zadania interfejsów API REST do inicjowania i sprawdzić zadania moderowanie zawartości. Po zrozumieniu struktury interfejsów API można łatwo portu tych wywołań na dowolną platformę zgodną REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji.
- (Opcjonalnie) [Zdefiniować niestandardowe przepływ pracy](./Review-Tool-User-Guide/Workflows.md) za pomocą zadania; możesz również użyć domyślnego przepływu pracy.

## <a name="create-a-job"></a>Tworzenie zadania

Aby utworzyć zadanie Moderowanie, przejdź do [zadania — Tworzenie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API odwoływać się do strony i wybierz przycisk w Twoim regionie klucza (można znaleźć ten adres URL punktu końcowego na **poświadczenia** stronie [przeglądu Narzędzie](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, w którym można łatwo utworzyć i uruchomienia wywołań interfejsu API REST.

![Zadania — Tworzenie strony pole region](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź następujące wartości do konstruowania wywołania REST:

- **teamName**: Identyfikator zespołu, który został utworzony podczas konfigurowania usługi [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) konta (w **identyfikator** pola na ekranie poświadczenia tego narzędzia przeglądu).
- **Typ zawartości**: Może to być "Image", "Text" lub "Wideo".
- **ContentId**: Niestandardowego ciągu identyfikatora. Ten ciąg jest przekazywany do interfejsu API i zwrócone za pośrednictwem wywołania zwrotnego. Jest to przydatne kojarzenia identyfikatory wewnętrznego lub metadanych z wyników zadania moderowania.
- **workflowname**: Nazwa przepływu pracy, który został wcześniej utworzony (lub "default" domyślnego przepływu pracy).
- **CallbackEndpoint**: (Opcjonalnie) Adres URL do odbierania informacji o wywołaniu zwrotnym, po zakończeniu przeglądu.
- **Ocp-Apim-Subscription-Key**: Klucz usługi Content Moderator. Można je znaleźć na **ustawienia** karcie [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Wprowadź treść żądania

Treść wywołania REST zawiera jedno pole **ContentValue**. Wklej zawartość nieprzetworzony tekst, jeśli są Moderowanie tekstu, lub wprowadź obrazu lub adres URL filmu wideo, jeśli masz Moderowanie obrazów i wideo. Możesz użyć następującego adresu URL obrazu próbki: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Zadania — Tworzenie parametry zapytania konsoli, nagłówki i okno treści żądania](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Prześlij żądanie

Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** pole zawiera identyfikator zadania. Skopiuj ten identyfikator do użycia w kolejnych krokach.

![Przejrzyj — Tworzenie konsoli zawartości, że pole zawiera identyfikator przeglądu odpowiedzi](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Pobierz stan zadania

Aby uzyskać stan i szczegółów dotyczących uruchomionej lub ukończonej zadania, przejdź do [zadania — Pobierz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API odwoływać się do strony i wybierz przycisk w Twoim regionie (region, w którym jest podawana klucz).

![Zadania — pole region Get](images/test-drive-region.png)

Wprowadź parametry wywołania REST, tak jak w powyższej sekcji. W tym kroku **JobId** jest unikatowy ciąg Identyfikatora otrzymany po utworzeniu zadania. Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** okno wyświetla zadania w formacie JSON, takich jak następujące:

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

![Zadania — Pobierz odpowiedź wywołania REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Sprawdź nowe review(s)

Jeśli zadanie zawartości spowodowało utworzenie recenzji, możesz wyświetlić ją w [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com). Wybierz **przeglądu** > **obraz**/**tekstu**/**wideo** (zależnie od tego, co możesz zawartości używane). Zawartość powinna zostać wyświetlona, gotowe do przeglądu przez ludzi. Po ludzi moderator przeglądy automatycznie przypisane tagi i dane prognozowania i przesyła decyzji końcowego Moderowanie, interfejsu API zadań przesyła wszystkie te informacje do punktu końcowego punktu końcowego wyznaczonym wywołania zwrotnego.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób tworzyć i wykonywać zapytania zadań moderowanie zawartości przy użyciu interfejsu API REST. Następnie zintegrować zadania scenariuszu Moderowanie end-to-end, takich jak [Moderowanie handlu elektronicznego](./ecommerce-retail-catalog-moderation.md) samouczka.