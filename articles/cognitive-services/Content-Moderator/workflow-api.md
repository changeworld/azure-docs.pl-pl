---
title: Przepływy pracy Moderowanie - Content Moderator
titlesuffix: Azure Cognitive Services
description: Przepływy pracy za pomocą moderowanie zawartości.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 3de45c62eb208671cc2d1d4de5309d2f9d75adc9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226878"
---
# <a name="moderation-workflows"></a>Moderowanie przepływów pracy

Pakiet Content Moderator obejmuje narzędzia i interfejsy API do zarządzania przepływami pracy. Korzystanie z przepływów pracy za pomocą [Przejrzyj operacje interfejsu API zadań](review-api.md) Aby zautomatyzować tworzenie przeglądu człowieka w pętli, na podstawie zasad dotyczących zawartości i progów.

Interfejs API przeglądu oferuje następujące sposoby uwzględnić ludzkiego nadzoru w procesie moderowanie zawartości:

1. **Zadania** operacji uruchamiania wspomagane maszynowo Moderowanie i ludzi Przejrzyj tworzenie w jednym kroku.
1. **Przejrzyj** operacji dla człowieka Przejrzyj tworzenia poza kroku moderowania.
1. **Przepływu pracy** operacje do zarządzania przepływy pracy umożliwiające automatyzację skanowanie za pomocą progów Przejrzyj tworzenia.

W tym artykule opisano **przepływu pracy** operacji. Odczyt [zadania i przeglądy](review-api.md) Przegląd, aby dowiedzieć się więcej na temat moderowanie zawartości zadań i ocen.

Trwa wyewidencjonowywanie **domyślne** przepływ pracy jest najlepszym sposobem na rozpoczęcie pracy w przepływach pracy zrozumieć, w pakiecie Content Moderator.

## <a name="your-first-workflow"></a>Pierwszego przepływu pracy

Oferujemy pierwszego przepływu pracy, przy użyciu usługi [przeglądu zespołu narzędzia](https://contentmoderator.cognitive.microsoft.com/). Utwórz konto, jeśli nie zostało to jeszcze zrobione.

Przejdź do [Przejrzyj przepływy pracy narzędzia](Review-Tool-User-Guide/Workflows.md) ekranu na karcie Ustawienia. Zostanie wyświetlony **domyślne** przepływu pracy, jak pokazano na poniższej ilustracji:

![Content Moderator przepływów pracy](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Otwórz domyślnego przepływu pracy

Użyj **Edytuj** opcję, aby otworzyć przepływu pracy, edytowania strony, jak pokazano na poniższej ilustracji: ![przepływu pracy domyślnego pakietu Content Moderator](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Widok projektanta

Zostanie wyświetlony **projektanta** kartę dla przepływu pracy. Widok projektanta przedstawia następujące czynności:

1. **Warunek** przepływu pracy, który ma zostać obliczone. W tym przypadku przepływu pracy wywołania usługi Content Moderator firmy obrazów interfejsu API i sprawdza czy `isAdult` danych wyjściowych jest równa `true`.
1. **Akcji** do wykonania, jeśli warunek jest spełniony. W takim przypadku przepływ pracy utworzy Przegląd w narzędzie do przeglądu Jeśli `isAdult` dane wyjściowe są `true`.

![Content Moderator domyślny przepływ pracy - projektanta](images/default-workflow-designer.png)

### <a name="the-json-view"></a>Widok kodu JSON

Wybierz **JSON** kartę, aby zobaczyć definicję formatu JSON przepływu pracy.

    {
        "Type": "Logic",
        If": {
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

### <a name="key-learning"></a>Learning klucza

Przepływy pracy w pakiecie Content Moderator to łatwa do skonfigurowania i elastyczne. Jeśli wbudowane projektanta nie spełnia wymagań, należy zapisać definicji przepływu pracy w **JSON** formatu. Następnie użyj definicję formatu JSON przy użyciu [API przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) tworzenie i zarządzanie nimi przepływu pracy z poziomu aplikacji.

## <a name="define-a-custom-workflow"></a>Definiowanie niestandardowego przepływu pracy

Pakiet Content Moderator przepływu pracy umożliwiają definiowanie i korzystanie z niestandardowych przepływów pracy. Użyj [Przejrzyj przepływy pracy narzędzia porad](Review-Tool-User-Guide/Workflows.md) artykuł, aby zdefiniować niestandardowy przepływ pracy. Ten przepływ pracy używa funkcji optycznego rozpoznawania znaków Content Moderator do wyodrębniania tekstu z obrazem próbki. Następnie tworzy przeglądu w narzędziu do przeglądu.

### <a name="the-sample-image"></a>Przykładowy obraz

Zapisz [przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na dysk lokalny. Ten obraz jest wymagany dla Twojego wykonywania.

### <a name="the-designer-view"></a>Widok projektanta

Wybierz **projektanta** kartę i [samouczek tworzenia przepływu pracy](Review-Tool-User-Guide/Workflows.md) Aby zdefiniować niestandardowy przepływ pracy. Na poniższej ilustracji przedstawiono projektanta **warunek** widoku. Zapoznaj się z samouczkiem, aby wyświetlić pozostałe kroki.

![Pakiet Content Moderator — warunek przepływu pracy](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>Widok kodu JSON

Wybierz **JSON** kartę, aby zobaczyć poniższą definicję formatu JSON niestandardowego przepływu pracy. Zwróć uwagę sposób, w jaki **If-Then** instrukcji w definicji JSON odnoszą się do kroków zdefiniowane przy użyciu projektanta widoku.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Wynik przepływu pracy

Po przetestowaniu przepływu pracy z przepływami pracy ekranu po dokonaniu przeglądu jest tworzony. Przejdź do **obraz** karcie **Przejrzyj** się zapoznania się z nimi.
Przepływ pracy utworzony Przegląd ponieważ podstawowy warunek testowany na obecność tekstu. Przejrzyj również wyróżnione **`a`** tagu w przeglądzie obrazu.

![Pakiet Content Moderator — prosty przepływ pracy w danych wyjściowych](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Zaawansowanych przepływów pracy przy użyciu połączenia

### <a name="the-sample-image"></a>Przykładowy obraz

Użyto tych samych [przykładowy obraz](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) użyty w poprzedniej sekcji.

Jednak go, zmienić podstawowy warunek do połączenia dwóch kontroli. Oprócz sprawdzania, czy tekst, należy sprawdzić, czy tekst ma jakiekolwiek przekleństwa. Przepływ pracy tworzy przeglądu, jeśli znajdzie się tekst **i** wykrywa wulgaryzmów.

### <a name="the-designer-view"></a>Widok projektanta

Aby zmienić **warunek** do **kombinacji**, zmodyfikuj przepływ pracy. Na poniższej ilustracji przedstawiono nowy widok, który zostanie wyświetlony w projektancie.

![Pakiet Content Moderator — warunek modyfikacji przepływu pracy](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>Widok kodu JSON

Wybierz **JSON** kartę, aby zobaczyć poniższą definicję formatu JSON zmodyfikowany niestandardowy przepływ pracy. Zwróć uwagę sposób, w jaki **If-Then** instrukcji w definicji JSON odpowiadają nowe kroki dodane do przepływu pracy.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Wynik przepływu pracy

Po ponownie przetestuj przepływ pracy można znaleźć, czy został utworzony nie przeglądu. Aby upewnić się, brak każdy Przegląd, przejdź do **obraz** karcie **Przejrzyj**.
Ponieważ nie mogła wykrywania wulgaryzmów w wyodrębniony tekst przepływu pracy nie utworzył przeglądu.

![Pakiet Content Moderator — dane wyjściowe modyfikacji przepływu pracy](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Przepływ pracy interfejsu API

[Operacje przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dostarczające interfejs programowania do możliwości przepływu pracy. Tworzenie przepływów pracy, Pobierz szczegóły przepływu pracy i aktualizowanie definicji przepływu pracy przy użyciu interfejsu API przepływu pracy.

### <a name="get-all-workflow-details"></a>Szczegóły przepływu pracy GET [All]

**Przepływ pracy Get** operacji akceptuje następujące dane wejściowe:

- **zespół**: identyfikator zespołu, który został utworzony podczas konfigurowania usługi [zapoznaj się uwagę narzędzie](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa przepływu pracy. Użyj `default` rozpoczynać się.
- **OCP-Apim-Subscription-Key**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [Przegląd](overview.md).

Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK` i **zawartość odpowiedzi** wyświetlone definicji przepływu pracy w formacie JSON.
Aby dowiedzieć się więcej, przeczytaj [Szybki Start konsoli przepływu pracy interfejsu API](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Utwórz lub zaktualizuj przepływ pracy

Operacja tworzenia i aktualizowania umożliwia tworzenie przepływów pracy z interfejsu API.

**Tworzenie przepływu pracy lub aktualizacji** operacji akceptuje następujące dane wejściowe:

- **zespół**: identyfikator zespołu, który został utworzony podczas konfigurowania usługi [zapoznaj się uwagę narzędzie](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa przepływu pracy. Użyj `default` rozpoczynać się.
- **OCP-Apim-Subscription-Key**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [Przegląd](overview.md).

Jeśli operacja się powiedzie, **stan odpowiedzi** jest `200 OK` i **zawartość odpowiedzi** wyświetlana `true`. Aby dowiedzieć się więcej, [przetestuj `Create` operacji](try-review-api-job.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak tworzyć niestandardowe przepływy pracy, zapoznaj się z [Przejrzyj samouczek przepływu pracy narzędzia](Review-Tool-User-Guide/Workflows.md). 

Wersja testowa [konsoli przepływu pracy interfejsu API](try-review-api-job.md) i przykłady kodu interfejsu API REST. 

Na koniec Użyj niestandardowych przepływów pracy za pomocą **zadania** operacji co shon w [Konsola interfejsu API zadań](try-review-api-job.md) i [.NET zadania szybkiego startu](moderation-jobs-quickstart-dotnet.md).
