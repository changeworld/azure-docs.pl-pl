---
title: Wykonywanie zadań zawartości łagodzenia moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie uruchamiania zadań łagodzenia zawartości w konsoli interfejsu API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347012"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Uruchom zadanie łagodzenia z poziomu konsoli interfejsu API

Za pomocą interfejsu API przeglądu [zadania operacje](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) do inicjowania zadań łagodzenia zawartości na trasie zawartości image lub text w moderatora zawartości platformy Azure. 

Zadanie łagodzenia skanowanie zawartości za pomocą API łagodzenia obrazu moderatora zawartości lub interfejsu API łagodzenia tekstu. Następnie zadanie łagodzenia używa przepływów pracy (zdefiniowany w narzędziu przeglądu) do generowania przeglądy w narzędziu przeglądu. 

Po człowieka moderatora sprawdza automatycznie przypisane tagów i dane prognozowania i przesyła decyzji końcowego łagodzenia, interfejsu API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Przejdź do [Przegląd narzędzia](https://contentmoderator.cognitive.microsoft.com/). Zarejestruj się, jeśli nie ma to jeszcze gotowe. W narzędziu przeglądu [zdefiniowanie niestandardowego przepływu pracy](Review-Tool-User-Guide/Workflows.md) do użycia w tym `Job` operacji.

## <a name="use-the-api-console"></a>Za pomocą konsoli interfejsu API
Aby test-drive interfejsu API za pomocą konsoli usługi online, należy kilka wartości o wprowadzenie do konsoli:
    
- `teamName`: Użyj `Id` pole z własnych narzędzi przeglądu poświadczenia ekranu. 
- `ContentId`: Ten ciąg jest przekazany do interfejsu API i zwrócony przez metodę wywołania zwrotnego. **ContentId** przydaje się do kojarzenia identyfikatory wewnętrzny lub metadane z wyników zadania łagodzenia.- `Workflowname`: nazwa [przepływu pracy, który został utworzony](Review-Tool-User-Guide/Workflows.md) w poprzedniej sekcji.
- `Ocp-Apim-Subscription-Key`: Znajduje się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

Dostęp do interfejsu API konsoli jest z **poświadczenia** okna.

### <a name="navigate-to-the-api-reference"></a>Przejdź do dokumentacji interfejsu API
W **poświadczenia** wybierz [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  `Job.Create` Zostanie otwarta strona.

### <a name="select-your-region"></a>Wybierz region
Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.
  ![Zadania — Utwórz pole region strony](images/test-drive-job-1.png)

  `Job.Create` Zostanie otwarta konsola interfejsu API. 

### <a name="enter-parameters"></a>Wprowadzanie parametrów

Wprowadź wartości parametrów zapytania wymagane, a klucz subskrypcji. W **treść żądania** Określ lokalizację informacje, które chcesz skanować. Na przykład można użyć tego [obraz przykładowy](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Zadania — Tworzenie konsoli parametry zapytania, nagłówki i okno treści żądania](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Prześlij żądanie
Wybierz pozycję **Wyślij**. Identyfikator zadania jest tworzony. Skopiuj to do użycia w następnych krokach.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Otwórz stronę szczegółów pobrania zadania
Wybierz **uzyskać**, a następnie otwórz interfejs API, wybierając przycisk, który odpowiada Twoim regionie.

  ![Zadanie — Tworzenie konsoli wyniki Get](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Przejrzyj odpowiedzi

Wprowadź wartości w polach **teamName** i **JobID**. Wprowadź klucz subskrypcji, a następnie wybierz **wysyłania**. Następującą odpowiedź zawiera przykładowe stan zadania i szczegółowe informacje.

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
Na pulpicie nawigacyjnym moderatora zawartości, wybierz **przeglądu** > **obrazu**. Pojawia się obraz, który skanowany, gotowe do przeglądu człowieka.

  ![Obraz narzędzia Przegląd trzech rowerzystów](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać [szybkiego startu .NET zadania](moderation-jobs-quickstart-dotnet.md) do integracji z aplikacji.
