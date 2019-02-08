---
title: Moderowanie zawartości przepływów pracy, z poziomu konsoli interfejsu API — Content Moderator
titlesuffix: Azure Cognitive Services
description: Używać operacji przepływu pracy w usłudze Azure Content Moderator, aby utworzyć lub zaktualizować przepływu pracy lub Pobierz szczegóły przepływu pracy przy użyciu interfejsu API przeglądu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1c18544a0fd135eb546660c442b865bf1249dfe5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883088"
---
# <a name="workflows-from-the-api-console"></a>Przepływy pracy za pomocą konsoli interfejsu API

Użyj [operacje przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) w usługi Azure Content Moderator, aby utworzyć lub zaktualizować przepływu pracy lub Pobierz szczegóły przepływu pracy przy użyciu interfejsu API przeglądu. Proste, złożone i nawet zagnieżdżonych wyrażeń dla przepływów pracy można zdefiniować za pomocą tego interfejsu API. Przepływy pracy są wyświetlane w narzędzie do przeglądu dla zespołu do użycia. Przepływy pracy są używane przez interfejs API przeglądu zadania operacje.

## <a name="prerequisites"></a>Wymagania wstępne

1. Przejdź do [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/). Utwórz konto, jeśli nie zostało to zrobione jeszcze. 
2. W narzędziu przeglądu w obszarze **ustawienia**, wybierz opcję **przepływy pracy** karty, jak pokazano na narzędzie do przeglądu [samouczek przepływu pracy](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Przejdź do ekranu przepływów pracy

Na pulpicie nawigacyjnym pakietu Content Moderator wybierz **przeglądu** > **ustawienia** > **przepływy pracy**. Zobaczysz domyślny przepływ pracy.

  ![Domyślny przepływ pracy](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Pobierz definicję formatu JSON domyślnego przepływu pracy

Wybierz **Edytuj** opcji dla przepływu pracy, a następnie wybierz **JSON** kartę. Zostaną wyświetlone następujące wyrażenie JSON:

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>Pobierz szczegóły przepływu pracy

Użyj **przepływu pracy — Get** operację, aby uzyskać szczegółowe informacje z istniejącym przepływem pracy domyślnego.

Narzędzie do przeglądu, przejdź do [poświadczenia](Review-Tool-User-Guide/credentials.md#the-review-tool) sekcji.

### <a name="browse-to-the-api-reference"></a>Przeglądaj, aby dokumentacja interfejsu API

1. W **poświadczenia** widoku, wybierz opcję [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Gdy **przepływu pracy — Utwórz lub zaktualizuj** zostanie otwarta strona, przejdź do [przepływu pracy — Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) odwołania.

### <a name="select-your-region"></a>Wybierz region

Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Przepływ pracy - wybór regionu Get](images/test-drive-region.png)

  **Przepływu pracy — Get** zostanie otwarta konsola interfejsu API.

### <a name="enter-parameters"></a>Wprowadzanie parametrów

Wprowadź wartości w polach **zespołu**, **workflowname**, i **Ocp-Apim-Subscription-Key** (klucz subskrypcji):

- **Zespół**: Identyfikator zespołu, który został utworzony podczas konfigurowania usługi [zapoznaj się uwagę narzędzie](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa przepływu pracy. Użyj `default`.
- **Ocp-Apim-Subscription-Key**: Znajduje się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

  ![Pobierz nagłówki i parametry zapytania](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** wyświetlone poniższy przepływ pracy w formacie JSON:

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Tworzenie przepływu pracy

Narzędzie do przeglądu, przejdź do [poświadczenia](Review-Tool-User-Guide/credentials.md#the-review-tool) sekcji.

### <a name="browse-to-the-api-reference"></a>Przeglądaj, aby dokumentacja interfejsu API

W **poświadczenia** widoku, wybierz opcję [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). **Przepływu pracy — Utwórz lub zaktualizuj** zostanie otwarta strona.

### <a name="select-your-region"></a>Wybierz region

Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Przepływ pracy — Utwórz pole region strony lub aktualizacji](images/test-drive-region.png)

  **Przepływu pracy — Utwórz lub zaktualizuj** zostanie otwarta konsola interfejsu API.

### <a name="enter-parameters"></a>Wprowadzanie parametrów

Wprowadź wartości w polach **zespołu**, **workflowname**, i **Ocp-Apim-Subscription-Key** (klucz subskrypcji):

- **Zespół**: Identyfikator zespołu, który został utworzony podczas konfigurowania usługi [zapoznaj się uwagę narzędzie](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa nowego przepływu pracy.
- **Ocp-Apim-Subscription-Key**: Znajduje się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

  ![Przepływ pracy — Tworzenie nagłówki i parametry zapytania konsoli lub aktualizacji](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Wprowadź definicji przepływu pracy

1. Edytuj **treść żądania** pole, aby wprowadzić żądania JSON ze szczegółowymi informacjami dla **opis** i **typu** (Image lub Text). 
2. Aby uzyskać **wyrażenie**, skopiuj wyrażenia przepływu pracy domyślnego z poprzedniej sekcji, jak pokazano poniżej:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Treść żądania wygląda następujące żądania JSON:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** wyświetlana `true`.

### <a name="check-out-the-new-workflow"></a>Zapoznaj się z nowego przepływu pracy

Narzędzie do przeglądu, wybierz **przeglądu** > **ustawienia** > **przepływy pracy**. Nowy przepływ pracy pojawi się i jest gotowa do użycia.

  ![Narzędzie do przeglądu listy przepływów pracy](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Zapoznaj się z nowymi szczegółami przepływu pracy

1. Wybierz **Edytuj** opcji dla przepływu pracy, a następnie wybierz **projektanta** i **JSON** karty.

   ![Karta konstruktora dla wybranego przepływu pracy](images/workflow-console-new-workflow-designer.PNG)

2. Aby wyświetlić widok kodu JSON przepływu pracy, wybierz **JSON** kartę.

## <a name="next-steps"></a>Kolejne kroki

* Bardziej złożone przykłady przepływu pracy, zobacz [omówienie przepływów pracy](workflow-api.md).
* Dowiedz się, jak używać przepływów pracy za pomocą [zadań moderowanie zawartości](try-review-api-job.md).
