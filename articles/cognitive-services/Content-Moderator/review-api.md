---
title: Moderowanie zadania i przeglądy człowieka w pętli - Content Moderator
titlesuffix: Azure Cognitive Services
description: Łączenie wspomaganego maszynowo moderowania oraz możliwości człowieka w pętli za pomocą Content Moderator Przegląd interfejsu API usługi Azure, aby uzyskać najlepsze wyniki dla Twojej firmy.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: dd7a78f1033a00935346216ae1f80e5400deea07
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206361"
---
# <a name="content-moderation-jobs-and-reviews"></a>Moderowanie zawartości zadania i przeglądy

Łączenie wspomaganego maszynowo moderowania oraz możliwości człowieka w pętli za pomocą usługi Azure Content Moderator [API przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) Aby uzyskać najlepsze wyniki dla Twojej firmy.

Interfejs API przeglądu oferuje następujące sposoby uwzględnić ludzkiego nadzoru w procesie moderowanie zawartości:

* `Job` operacje są używane do uruchamiania wspomagane maszynowo Moderowanie i tworzenie przeglądu przez ludzi w jednym kroku.
* `Review` operacje są używane do tworzenia przeglądu przez ludzi, poza kroku moderowania.
* `Workflow` operacje są używane do zarządzania przepływy pracy umożliwiające automatyzację skanowanie z progami dla tworzenia przeglądu.

`Job` i `Review` operacje zaakceptować punktów końcowych wywołania zwrotnego do odbierania, stanu i wyników.

W tym artykule opisano `Job` i `Review` operacji. Odczyt [omówienie przepływów pracy](workflow-api.md) instrukcje dotyczące sposobu tworzenia, edytowania i pobrać definicji przepływu pracy.

## <a name="job-operations"></a>Operacje zadania

### <a name="start-a-job"></a>Uruchamianie zadania
Użyj `Job.Create` operacji uruchamiania, Moderowanie i zadania tworzenia przeglądu przez ludzi. Pakiet Content Moderator skanuje zawartości i ocenia wyznaczonym przepływu pracy. Oparte na wynikach przepływu pracy, jego tworzy przeglądy albo pominie ten krok. Przesyła znaczniki po Moderowanie i przeglądu po do punktu końcowego usługi wywołania zwrotnego.

Dane wejściowe zawierają następujące informacje:

- Identyfikator przeglądu zespołu.
- Zawartość, do którego należy.
- Nazwa przepływu pracy. (Wartość domyślna to "domyślny" przepływ pracy).
- Wywołanie zwrotne interfejsu API punktu dla powiadomień.
 
Następującą odpowiedź zawiera identyfikator zadania, które zostało uruchomione. Identyfikator zadania umożliwia pobieranie stanu zadania i Azure otrzymują szczegółowe informacje.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Pobierz stan zadania

Użyj `Job.Get` operacji i identyfikator zadania, aby uzyskać szczegółowe informacje o zadaniu uruchomionej lub ukończonej. Operacja zwraca natychmiast, gdy zadanie Moderowanie jest uruchamiane asynchronicznie. Wyniki są zwracane za pośrednictwem punktu końcowego wywołania zwrotnego.

Dane wejściowe zawierają następujące informacje:

- Identyfikator zespołu przeglądu: Identyfikator zadania zwracany przez poprzednią operację

Odpowiedź zawiera następujące informacje:

- Identyfikator utworzono Przegląd. (Aby uzyskać wyniki ostateczny Przegląd, użyj tego Identyfikatora).
- Stan zadania (zakończone lub w toku): Moderowanie przypisane tagi (pary klucz wartość).
- Raport wykonania zadania.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
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
 
![Przeglądanie obrazu przez moderatorów-ludzi](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Przegląd operacji

### <a name="create-reviews"></a>Utwórz przeglądów

Użyj `Review.Create` operację, aby utworzyć ludziom dokonywanie. Możesz średni je w innym miejscu albo użyj niestandardowej logiki, aby przypisać znaczniki moderowania.

Dane wejściowe dla tej operacji obejmują:

- Zawartość do przejrzenia.
- Przypisanych tagów (pary klucz-wartość) do przeglądu przez ludzi moderatorów.

Następującą odpowiedź zawiera identyfikator przeglądu:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Pobierz stan przeglądu
Użyj `Review.Get` operację, aby uzyskać wyniki, po zakończeniu przeglądu przez ludzi moderowanych obrazu. Użytkownik jest informowany za pośrednictwem punktu końcowego podanego wywołania zwrotnego. 

Operacja zwraca dwa zestawy tagi: 

* Tagi przypisane przez usługę Moderowanie
* Znaczniki po przeglądu przez ludzi zostało ukończone.

Dane wejściowe zawierają co najmniej:

- Nazwa zespołu przeglądu
- Identyfikator przeglądu zwrócony przez poprzednią operację

Odpowiedź zawiera następujące informacje:

- Stan przeglądu
- Tagi (pary klucz wartość) potwierdzona przez ludzi recenzenta
- Tagi (pary klucz wartość) przypisany przez usługę Moderowanie

Zobacz tagi przypisane do osoby dokonującej przeglądu (**reviewerResultTags**) i tagi początkowe (**metadanych**) w odpowiedzi na następujące przykładowe:

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>Kolejne kroki

* Wersja testowa [Konsola interfejsu API zadań](try-review-api-job.md)i przykłady kodu interfejsu API REST. Jeśli znasz program Visual Studio w języku C#, Sprawdź również [.NET zadania szybkiego startu](moderation-jobs-quickstart-dotnet.md). 
* Aby rozpocząć pracę z [Konsola interfejsu API przeglądu](try-review-api-review.md)i przykłady kodu interfejsu API REST. Następnie zobacz [Szybki Start .NET przeglądy](moderation-reviews-quickstart-dotnet.md).
* Przeglądy wideo, można użyć [wideo Przejrzyj Przewodnik Szybki Start](video-reviews-quickstart-dotnet.md)i Dowiedz się, jak [Dodaj transkrypcji do wideo przeglądu](video-transcript-reviews-quickstart-dotnet.md).
