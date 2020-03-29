---
title: Definiowanie przepływów pracy moderowania za pomocą konsoli interfejsu API REST — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Za pomocą interfejsów API przeglądu moderatora zawartości platformy Azure można zdefiniować niestandardowe przepływy pracy i progi na podstawie zasad zawartości.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754179"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiowanie i używanie przepływów pracy moderowania (REST)

Przepływy pracy to filtry dostosowane do chmury, których można używać do bardziej efektywnego obchodzenia się z zawartością. Przepływy pracy mogą łączyć się z różnymi usługami, aby filtrować zawartość na różne sposoby, a następnie podjąć odpowiednie działania. W tym przewodniku pokazano, jak używać interfejsów API REST przepływu przepływu pracy za pośrednictwem konsoli interfejsu API do tworzenia i używania przepływów pracy. Po zrozumieniu struktury interfejsów API można łatwo przenieść te wywołania do dowolnej platformy zgodnej z funkcją REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub utwórz konto w witrynie narzędzia Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="create-a-workflow"></a>Tworzenie przepływu pracy

Aby utworzyć lub zaktualizować przepływ pracy, przejdź do strony odwołania do **[przepływu pracy — tworzenie lub aktualizowanie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** interfejsu API i wybierz przycisk dla regionu klucza (można go znaleźć w adresie URL punktu końcowego na stronie **Poświadczenia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, gdzie można łatwo konstruować i uruchamiać wywołania interfejsu API REST.

![Przepływ pracy — tworzenie lub aktualizowanie zaznaczenia regionu strony](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź wartości **dla zespołu**, nazwa przepływu **pracy**i **Ocp-Apim-Subscription-Key**:

- **zespół:** Identyfikator zespołu utworzony podczas konfigurowania konta [narzędzia recenzja](https://contentmoderator.cognitive.microsoft.com/) (znajdujący się w polu **Identyfikator** na ekranie Poświadczenia narzędzia recenzja).
- **nazwa przepływu pracy**: Nazwa nowego przepływu pracy do dodania (lub istniejącej nazwy, jeśli chcesz zaktualizować istniejący przepływ pracy).
- **Ocp-Apim-Subscription-Key**: Twój klucz moderatora treści. Można to znaleźć na karcie **Ustawienia** [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com).

![Przepływ pracy — tworzenie lub aktualizowanie parametrów i nagłówków kwerend konsoli](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Wprowadzanie definicji przepływu pracy

1. Edytuj pole **treści Żądanie,** aby wprowadzić żądanie JSON ze `Image` szczegółami **opisu** i **typu** (albo). `Text`
2. W polu **Wyrażenie**skopiuj domyślne wyrażenie JSON przepływu pracy. Twój ostatni ciąg JSON powinien wyglądać następująco:

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
> Można zdefiniować proste, złożone, a nawet zagnieżdżone wyrażenia dla przepływów pracy przy użyciu tego interfejsu API. [Dokumentacja przepływu pracy — tworzenie lub aktualizowanie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) ma przykłady bardziej złożonej logiki.

### <a name="submit-your-request"></a>Prześlij swoją prośbę
  
Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się pomyślnie, `200 OK`stan **Odpowiedź** jest `true`, a zawartość **odpowiedzi** zostanie wyświetlona.

### <a name="examine-the-new-workflow"></a>Sprawdzanie nowego przepływu pracy

W [narzędziu Recenzja](https://contentmoderator.cognitive.microsoft.com/)wybierz pozycję **Ustawienia** > **przepływów pracy**. Nowy przepływ pracy powinien pojawić się na liście.

![Przeglądanie listy narzędzi przepływów pracy](images/workflow-console-new-workflow.PNG)

Wybierz opcję **Edytuj** dla przepływu pracy i przejdź do karty **Projektant.** W tym miejscu można zobaczyć intuicyjną reprezentację logiki JSON.

![Karta Projektanta dla wybranego przepływu pracy](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Uzyskaj szczegółowe informacje o przepływie pracy

Aby pobrać szczegółowe informacje o istniejącym przepływie pracy, przejdź do strony odwołania do **[przepływu pracy — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** interfejs API i wybierz przycisk dla swojego regionu (regionu, w którym jest administrowany klucz).

![Przepływ pracy — pobierz wybór regionu](images/test-drive-region.png)

Wprowadź parametry wywołania REST, jak w powyższej sekcji. Upewnij się, że tym razem nazwa **przepływu pracy** jest nazwą istniejącego przepływu pracy.

![Pobierz parametry kwerendy i nagłówki](images/workflow-get-default.PNG)

Wybierz pozycję **Wyślij**. Jeśli operacja zakończy się pomyślnie, `200 OK`stan **Odpowiedź** jest , a w polu Zawartość **odpowiedzi** jest wyświetlany przepływ pracy w formacie JSON, podobnie jak następujące:

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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak korzystać z przepływów pracy z [zadaniami moderowania zawartości](try-review-api-job.md).