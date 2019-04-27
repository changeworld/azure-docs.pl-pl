---
title: Definiowanie Moderowanie przepływów pracy, za pomocą konsoli interfejsu API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Interfejsów API usługi Azure zawartości Moderator przeglądu służy do definiowania niestandardowych przepływów pracy i wartości progowe, oparte na zawartości zasad.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605880"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiowanie i korzystanie z przepływów pracy moderation (REST)

Przepływy pracy są oparte na chmurze niestandardowe filtry, które można użyć, aby bardziej efektywnie obsługiwać zawartość. Przepływy pracy mogą łączyć się z różnych usług do filtrowania zawartości na różne sposoby, a następnie podjąć odpowiednie działania. Ten przewodnik pokazuje, jak używać interfejsów API REST, przepływu pracy przy użyciu konsoli interfejsu API, tworzenia i używania przepływów pracy. Po zrozumieniu struktury interfejsów API można łatwo portu tych wywołań na dowolną platformę zgodną REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji.

## <a name="create-a-workflow"></a>Tworzenie przepływu pracy

Aby utworzyć lub zaktualizować przepływu pracy, przejdź do **[przepływu pracy — Utwórz lub zaktualizuj](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API odwoływać się do strony i wybierz przycisk w Twoim regionie klucza (można znaleźć ten adres URL punktu końcowego na **poświadczeń**  strony [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, w którym można łatwo utworzyć i uruchomienia wywołań interfejsu API REST.

![Przepływ pracy — Utwórz pole region strony lub aktualizacji](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź wartości w polach **zespołu**, **workflowname**, i **Ocp-Apim-Subscription-Key**:

- **Zespół**: Identyfikator zespołu, który został utworzony podczas konfigurowania usługi [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) konta (w **identyfikator** pola na ekranie poświadczenia tego narzędzia przeglądu).
- **workflowname**: Nazwa nowego przepływu pracy, aby dodać (lub istniejącej nazwy, jeśli chcesz zaktualizować istniejący przepływ pracy).
- **Ocp-Apim-Subscription-Key**: Klucz usługi Content Moderator. Można je znaleźć na **ustawienia** karcie [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com).

![Przepływ pracy — Tworzenie nagłówki i parametry zapytania konsoli lub aktualizacji](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Wprowadź definicji przepływu pracy

1. Edytuj **treść żądania** pole, aby wprowadzić żądania JSON ze szczegółowymi informacjami dla **opis** i **typu** (albo `Image` lub `Text`).
2. Aby uzyskać **wyrażenie**, skopiuj domyślny przepływ pracy wyrażenie JSON. Usługi ostatni ciąg JSON powinien wyglądać następująco:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Można zdefiniować prostą, złożone i nawet zagnieżdżonych wyrażeń dla przepływów pracy za pomocą tego interfejsu API. [Przepływu pracy — Utwórz lub zaktualizuj](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentacji zawiera przykłady bardziej złożonej logiki.

### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** wyświetlana `true`.

### <a name="examine-the-new-workflow"></a>Sprawdź nowy przepływ pracy

W [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/), wybierz opcję **ustawienia** > **przepływy pracy**. Nowy przepływ pracy powinien pojawić się na liście.

![Narzędzie do przeglądu listy przepływów pracy](images/workflow-console-new-workflow.PNG)

Wybierz **Edytuj** opcji dla przepływu pracy, a następnie przejdź do **projektanta** kartę. Tutaj widać intuicyjne reprezentacja logiki JSON.

![Karta konstruktora dla wybranego przepływu pracy](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Pobierz szczegóły przepływu pracy

Aby uzyskać szczegółowe informacje o istniejącym przepływie pracy, przejdź do **[przepływu pracy — Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API odwoływać się do strony i wybierz przycisk w Twoim regionie (region, w którym jest podawana klucz).

![Przepływ pracy - wybór regionu Get](images/test-drive-region.png)

Wprowadź parametry wywołania REST, tak jak w powyższej sekcji. Upewnij się, że ten czas **workflowname** jest nazwą istniejącego przepływu pracy.

![Pobierz nagłówki i parametry zapytania](images/workflow-get-default.PNG)

Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK`i **zawartość odpowiedzi** wyświetlone przepływu pracy w formacie JSON, takich jak następujące:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać przepływów pracy za pomocą [zadań moderowanie zawartości](try-review-api-job.md).