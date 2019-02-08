---
title: Uruchamianie zadań moderowanie zawartości za pomocą konsoli interfejsu API — Content Moderator
titlesuffix: Azure Cognitive Services
description: Użyj operacji zadań Przegląd interfejsu API do inicjowania zadań end-to-end moderowanie zawartości dla zawartości image lub text w usłudze Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9fac80ff152b0f7b9c36c182759d41245364fff9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862432"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Uruchom zadanie Moderowanie z poziomu konsoli interfejsu API

Za pomocą interfejsu API przeglądu [zadania operacje](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) do inicjowania zadań end-to-end moderowanie zawartości dla zawartości image lub text w usłudze Azure Content Moderator. 

Zadanie Moderowanie skanowanie zawartości za pomocą API moderowania obrazów Moderator zawartości lub interfejs API moderowania tekstu. Następnie zadania Moderowanie używa przepływy pracy (zdefiniowanymi w narzędzie do przeglądu) do generowania przeglądy w narzędzie do przeglądu. 

Po ludzi moderator przeglądy automatycznie przypisane tagi i dane prognozowania i przesyła decyzji końcowego Moderowanie, interfejs API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Przejdź do [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/). Utwórz konto, jeśli nie mają to jeszcze zrobione. W ramach narzędzie do przeglądu [zdefiniować niestandardowe przepływ pracy](Review-Tool-User-Guide/Workflows.md) do użycia w tym `Job` operacji.

## <a name="use-the-api-console"></a>Użyj konsoli interfejsu API
Będą mogli ją testować interfejs API za pomocą konsoli usługi online, konieczne jest kilku wartości w celu wprowadzenia w życie konsoli programu:
    
- `teamName`: Użyj `Id` pola z ekranu poświadczenia tego narzędzia przeglądu. 
- `ContentId`: Ten ciąg jest przekazywany do interfejsu API i zwrócone za pośrednictwem wywołania zwrotnego. **ContentId** przydaje się do kojarzenia identyfikatory wewnętrznego lub metadanych z wyników zadania moderowania.- `Workflowname`: Nazwa [przepływu pracy, który został utworzony](Review-Tool-User-Guide/Workflows.md) w poprzedniej sekcji.
- `Ocp-Apim-Subscription-Key`: Znajduje się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

Interfejs API dostępu do konsoli pochodzi z **poświadczenia** okna.

### <a name="navigate-to-the-api-reference"></a>Przejdź do dokumentacja interfejsu API
W **poświadczenia** wybierz [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  `Job.Create` Zostanie otwarta strona.

### <a name="select-your-region"></a>Wybierz region
Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.
  ![Zadania — Tworzenie strony pole region](images/test-drive-job-1.png)

  `Job.Create` Zostanie otwarta konsola interfejsu API. 

### <a name="enter-parameters"></a>Wprowadzanie parametrów

Wprowadź wartości parametrów zapytania wymagane i klucz subskrypcji. W **treść żądania** Określ lokalizację informacje, które chcesz przeprowadzić skanowanie. W tym przykładzie użyjemy to [przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Zadania — Tworzenie parametry zapytania konsoli, nagłówki i okno treści żądania](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Prześlij żądanie
Wybierz pozycję **Wyślij**. Identyfikator zadania jest tworzony. Skopiuj to do użycia w następnych krokach.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Otwórz stronę szczegółów pobrania zadania
Wybierz **uzyskać**, a następnie otwórz interfejs API, wybierając przycisk, który odpowiada Twoim regionie.

  ![Zadania — Tworzenie konsoli wyniki Get](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Odpowiedź na Przegląd kodu

Wprowadź wartości w polach **teamName** i **JobID**. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **wysyłania**. Następującą odpowiedź zawiera przykładowe zadania stan i szczegółowe informacje.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>Przejdź do narzędzia do przeglądu
Na pulpicie nawigacyjnym Content Moderator wybierz **przeglądu** > **obraz**. Pojawia się obraz, który skanowany, gotowe do przeglądu przez ludzi.

  ![Obraz narzędzie do przeglądu rowerzystów trzy](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [.NET zadania szybkiego startu](moderation-jobs-quickstart-dotnet.md) do integracji z aplikacją.
