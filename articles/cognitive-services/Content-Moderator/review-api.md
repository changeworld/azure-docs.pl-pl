---
title: Azure moderatora zawartości - łagodzenia zadania i ludzkich w pętli recenzje | Dokumentacja firmy Microsoft
description: Zastosowanie człowieka nadzoru do łagodzenia wspierana maszyny, aby uzyskać najlepsze wyniki.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347129"
---
# <a name="moderation-jobs-and-reviews"></a>Łagodzenie zadań i przeglądów

Łączenie łagodzenia wspierana maszyny z możliwościami ludzkich w pętli za pomocą moderatora zawartości Azure [API przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) Aby uzyskać najlepsze wyniki dla Twojej firmy.

Przegląd interfejsu API oferuje następujące sposoby obejmują nadzoru ludzkich w procesie łagodzenia zawartości:

* `Job` operacje są używane do uruchamiania łagodzenia wspierana maszyny i tworzenia człowieka przeglądu w jednym kroku.
* `Review` operacji są używane do tworzenia przeglądu ludzi, poza krok łagodzenia.
* `Workflow` operacje są używane do zarządzania przepływy pracy automatyzujących skanowanie z progami tworzenia przeglądu.

`Job` i `Review` operacji akceptować wywołania zwrotnego punktów końcowych dla odbieranie stanu i wyników.

W tym artykule omówiono `Job` i `Review` operacji. Odczyt [omówienie przepływy pracy](workflow-api.md) informacji na temat sposobu tworzenia, edytowania i pobrać definicji przepływu pracy.

## <a name="job-operations"></a>Operacje zadania

### <a name="start-a-job"></a>Uruchom zadanie
Użyj `Job.Create` operacji uruchamiania łagodzenia i zadania tworzenia człowieka przeglądu. Zawartości moderatora skanuje zawartość i ocenia wyznaczonych przepływu pracy. Oparte na wynikach przepływu pracy, jego tworzy przeglądami albo pomija kroku. Przesyła także po łagodzenia i przejrzyj po tagi do punktu końcowego wywołania zwrotnego.

Dane wejściowe zawierają następujące informacje:

- Identyfikator przeglądu zespołu.
- Zawartość do się łagodzenie.
- Nazwa przepływu pracy. (Wartość domyślna to "domyślny" przepływ pracy).
- Wywołanie zwrotne interfejsu API w punkcie powiadomienia.
 
Następujące odpowiedzi zawiera identyfikator zadania, które zostało uruchomione. Identyfikator zadania umożliwia stan zadań i uzyskać szczegółowe informacje.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Pobierz stan zadania

Użyj `Job.Get` operacji i identyfikatora zadania, aby uzyskać szczegóły zadania uruchomione lub zostało zakończone. Operacja zwraca, podczas gdy zadanie łagodzenia jest uruchamiane asynchronicznie. Wyniki są zwracane za pośrednictwem punktu końcowego wywołania zwrotnego.

Dane wejściowe zawierają następujące informacje:

- Sprawdź identyfikator zespołu: identyfikator zadania zwracany przez poprzednią operację

Odpowiedź zawiera następujące informacje:

- Identyfikator recenzowania utworzone. (Aby uzyskać wyników przeglądu ostatecznego, użyj tego Identyfikatora).
- Stan zadania (wykonane lub w toku): tagi przypisanej łagodzenia (pary klucz wartość).
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
 
![Przejrzyj obrazu dla człowieka moderatorów](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Czynności przeglądu

### <a name="create-reviews"></a>Utwórz przeglądów

Użyj `Review.Create` operacji tworzenia przeglądy człowieka. Można je w innym miejscu średnie albo użyj niestandardowej logiki Aby przypisać znaczniki łagodzenia.

Dane wejściowe dla tej operacji obejmują:

- Zawartość do przeglądu.
- Tagi przypisanej (pary wartości klucza) do przeglądu przez człowieka moderatorów.

Następujące odpowiedzi zawiera identyfikator przeglądu:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Pobierz stan przeglądu
Użyj `Review.Get` operację, aby uzyskać wyniki, po zakończeniu człowieka Przegląd moderowane obrazu. Użytkownik jest informowany za pośrednictwem punktu końcowego podanego wywołania zwrotnego. 

Operacja zwraca dwa zestawy znaczników: 

* Tagi przypisany przez usługę łagodzenia
* Tagi zakończenia człowieka przeglądu

Dane wejściowe zawierają co najmniej:

- Nazwa zespołu przeglądu
- Identyfikator przeglądu zwrócony przez poprzednią operację

Odpowiedź zawiera następujące informacje:

- Stan przeglądu
- Tagi (pary klucz wartość) potwierdzona przez człowieka osoby dokonującej przeglądu
- Przypisany przez usługę łagodzenia tagi (pary klucz wartość)

Zobacz tagi przypisane osoba dokonująca przeglądu (**reviewerResultTags**) i początkowe tagi (**metadanych**) w odpowiedzi na następujące przykładowe:

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

* Przetestuj [API zadania konsoli](try-review-api-job.md)i użyj przykłady kodu interfejsu API REST. Jeśli znasz i Visual Studio C#, Sprawdź również [szybkiego startu .NET zadania](moderation-jobs-quickstart-dotnet.md). 
* Recenzji, wprowadzenie [API przeglądu konsoli](try-review-api-review.md)i użyj przykłady kodu interfejsu API REST. Następnie zobacz [szybkiego startu .NET przeglądami](moderation-reviews-quickstart-dotnet.md).
* Wideo recenzji, użyj [szybkiego startu przeglądu wideo](video-reviews-quickstart-dotnet.md)i Dowiedz się, jak [Dodaj zapisy rozmów wideo Przegląd](video-transcript-reviews-quickstart-dotnet.md).
