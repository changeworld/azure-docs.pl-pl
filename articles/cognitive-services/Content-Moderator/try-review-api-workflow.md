---
title: Azure moderatora zawartości - zawartości przepływy pracy łagodzenia z poziomu konsoli interfejsu API | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z przepływów pracy łagodzenia zawartości z poziomu konsoli interfejsu API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 700b2bea5e902141659266a94d61ceb810c1b802
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347133"
---
# <a name="workflows-from-the-api-console"></a>Przepływy pracy z poziomu konsoli interfejsu API

Użyj [operacje przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) w Azure moderatora zawartości do tworzenia lub aktualizacji przepływu pracy lub Pobierz szczegóły przepływu pracy przy użyciu interfejsu API przeglądu. Proste, złożone i nawet zagnieżdżonych wyrażeń dla przepływów pracy można zdefiniować przy użyciu tego interfejsu API. Przepływy pracy są wyświetlane w narzędziu przeglądu dla zespołu do użycia. Przepływy pracy są używane przez interfejs API Przejrzyj zadania operacje.

## <a name="prerequisites"></a>Wymagania wstępne

1. Przejdź do [narzędzie przeglądu](https://contentmoderator.cognitive.microsoft.com/). Zarejestruj się, jeśli nie zostało to jeszcze zrobione jeszcze. 
2. W narzędziu przeglądu w obszarze **ustawienia**, wybierz pozycję **przepływy pracy** karcie, jak pokazano w narzędziu przeglądu [samouczek przepływu pracy](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Przejdź do ekranu przepływów pracy

Na pulpicie nawigacyjnym moderatora zawartości wybierz **przeglądu** > **ustawienia** > **przepływy pracy**. Domyślny przepływ pracy zostanie wyświetlony.

  ![Domyślny przepływ pracy](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Uzyskaj definicję JSON domyślnego przepływu pracy

Wybierz **Edytuj** opcję przepływu pracy, a następnie wybierz **JSON** kartę. Zostaną wyświetlone następujące wyrażenie JSON:

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

Użyj **przepływ pracy - Get** operację, aby uzyskać szczegółowe informacje o istniejących domyślny przepływ pracy.

W narzędziu przeglądu, przejdź do [poświadczenia](Review-Tool-User-Guide/credentials.md#the-review-tool) sekcji.

### <a name="browse-to-the-api-reference"></a>Przejdź do dokumentacji interfejsu API

1. W **poświadczenia** widok, wybierz opcję [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Gdy **przepływu pracy — Tworzenie lub aktualizacja** zostanie otwarta strona, przejdź do [przepływ pracy - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) odwołania.

### <a name="select-your-region"></a>Wybierz region

Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Przepływ pracy - wybór regionu Get](images/test-drive-region.png)

  **Przepływ pracy - Get** zostanie otwarta konsola interfejsu API.

### <a name="enter-parameters"></a>Wprowadzanie parametrów

Wprowadź wartości w polach **zespołu**, **workflowname**, i **Ocp-Apim-subskrypcji — klucz** (klucz subskrypcji):

- **zespołu**: identyfikator zespołu utworzonego podczas konfigurowania sieci [Przejrzyj konta narzędzia](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa przepływu pracy. Użyj `default`.
- **OCP-Apim-subskrypcji — klucz**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

  ![Get parametry zapytania i nagłówków](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się powodzeniem, **stanu odpowiedzi** jest `200 OK`i **zawartości odpowiedzi** w polu są wyświetlane w poniższym przepływie pracy JSON:

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


## <a name="create-a-workflow"></a>Tworzenie przepływów pracy

W narzędziu przeglądu, przejdź do [poświadczenia](Review-Tool-User-Guide/credentials.md#the-review-tool) sekcji.

### <a name="browse-to-the-api-reference"></a>Przejdź do dokumentacji interfejsu API

W **poświadczenia** widok, wybierz opcję [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). **Przepływu pracy — Tworzenie lub aktualizacja** zostanie otwarta strona.

### <a name="select-your-region"></a>Wybierz region

Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Przepływ pracy - tworzenia lub aktualizacji strony region wybór](images/test-drive-region.png)

  **Przepływu pracy — Tworzenie lub aktualizacja** zostanie otwarta konsola interfejsu API.

### <a name="enter-parameters"></a>Wprowadzanie parametrów

Wprowadź wartości w polach **zespołu**, **workflowname**, i **Ocp-Apim-subskrypcji — klucz** (klucz subskrypcji):

- **zespołu**: identyfikator zespołu utworzonego podczas konfigurowania sieci [Przejrzyj konta narzędzia](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa nowego przepływu pracy.
- **OCP-Apim-subskrypcji — klucz**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

  ![Przepływ pracy — Tworzenie parametrów zapytania lub aktualizacji konsoli i nagłówki](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Wprowadź definicji przepływu pracy

1. Edytuj **treść żądania** pole służące do wprowadzania żądania JSON w szczegółowych informacjach dotyczących **opis** i **typu** (Image lub Text). 
2. Aby uzyskać **wyrażenie**, skopiuj domyślne wyrażenie przepływu pracy z poprzedniej sekcji, jak pokazano poniżej:

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
  
Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się powodzeniem, **stanu odpowiedzi** jest `200 OK`i **zawartości odpowiedzi** wyświetlana `true`.

### <a name="check-out-the-new-workflow"></a>Zapoznaj się z nowego przepływu pracy

W narzędziu przeglądu, wybierz **przeglądu** > **ustawienia** > **przepływy pracy**. Nowy przepływ pracy zostanie wyświetlony i jest gotowe do użycia.

  ![Przejrzyj listę narzędzia przepływów pracy](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Przejrzyj szczegóły nowego przepływu pracy

1. Wybierz **Edytuj** opcję przepływu pracy, a następnie wybierz **projektanta** i **JSON** karty.

   ![Karta konstruktora dla wybranego przepływu pracy](images/workflow-console-new-workflow-designer.PNG)

2. Aby wyświetlić widok JSON przepływu pracy, wybierz **JSON** kartę.

## <a name="next-steps"></a>Kolejne kroki

* Bardziej złożone przykłady przepływu pracy można znaleźć [omówienie przepływy pracy](workflow-api.md).
* Informacje o sposobie korzystania z przepływów pracy z [zawartości zadania łagodzenia](try-review-api-job.md).
