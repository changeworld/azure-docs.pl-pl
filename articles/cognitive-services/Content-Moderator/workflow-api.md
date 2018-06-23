---
title: Azure moderatora zawartości - przepływy pracy łagodzenia | Dokumentacja firmy Microsoft
description: Przepływy pracy za pomocą łagodzenia zawartości.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 079fcd119f1536f9e76ca57fccc76538b3c3ed78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347193"
---
# <a name="moderation-workflows"></a>Łagodzenie przepływów pracy

Moderatora zawartości zawiera narzędzia i interfejsy API do zarządzania przepływami pracy. Korzystania z przepływów pracy z [operacji zadań Przejrzyj API](review-api.md) można zautomatyzować tworzenie przeglądu ludzkich w pętli są oparte na zasadach zawartości i progów.

Przegląd interfejsu API oferuje następujące sposoby obejmują nadzoru ludzkich w procesie łagodzenia zawartości:

1. **Zadania** operacji uruchamiania łagodzenia wspierana maszyny i ludzkich Przejrzyj tworzenie w jednym kroku.
1. **Przejrzyj** tworzenia poza krok łagodzenia Przejrzyj operacji dla człowieka.
1. **Przepływu pracy** operacji związanych z zarządzaniem przepływy pracy, które automatyzują skanowanie z progami dla Przejrzyj tworzenia.

W tym artykule omówiono **przepływu pracy** operacji. Odczyt [zadania i monitoruje](review-api.md) Przegląd, aby dowiedzieć się więcej o zawartości łagodzenia zadania i monitoruje.

Wyewidencjonowywanie **domyślne** przepływu pracy to najlepszy sposób, aby rozpocząć pracę w przepływach pracy opis w polu Moderator zawartości.

## <a name="your-first-workflow"></a>Pierwszy przepływu pracy

Pierwszy przepływu pracy jest powiązane z Twojej [Przejrzyj narzędzia team](https://contentmoderator.cognitive.microsoft.com/). Zarejestruj się, jeśli nie zostało to jeszcze zrobione.

Przejdź do [Przejrzyj przepływy pracy narzędzia](Review-Tool-User-Guide/Workflows.md) ekranu na karcie Ustawienia. Zostanie wyświetlony **domyślne** przepływu pracy, jak pokazano na poniższej ilustracji:

![Zawartości moderatora przepływów pracy](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Otwórz domyślnego przepływu pracy

Użyj **Edytuj** opcję, aby otworzyć przepływu pracy edycji strony, jak pokazano na poniższej ilustracji: ![moderatora zawartości domyślny przepływ pracy](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Widok projektanta

Zostanie wyświetlony **projektanta** kartę przepływu pracy. Widok projektanta zawiera następujące czynności:

1. **Warunku** przepływu pracy, który ma zostać obliczone. W takim przypadku przepływu pracy wywołań moderatora zawartości przez obraz interfejsu API i sprawdza, czy `isAdult` output equals `true`.
1. **Akcji** do wykonania, jeśli jest spełniony warunek. W takim przypadku przepływ pracy tworzy przeglądu w narzędziu przeglądu Jeśli `isAdult` dane wyjściowe są `true`.

![Zawartości moderatora domyślny przepływ pracy - projektanta](images/default-workflow-designer.png)

### <a name="the-json-view"></a>Widok JSON

Wybierz **JSON** kartę, aby wyświetlić JSON definicji przepływu pracy.

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

Przepływy pracy w zawartości moderatora są łatwa do skonfigurowania i elastyczne. Jeśli wbudowane projektanta nie spełnia wymagań, należy zapisać definicji przepływu pracy w **JSON** format. Następnie użyj definicji JSON z [API przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) tworzenie i zarządzanie nimi przepływu pracy z aplikacji.

## <a name="define-a-custom-workflow"></a>Definiowanie niestandardowego przepływu pracy

Zawartości moderatora przepływu pracy umożliwiają definiowanie i przy użyciu niestandardowych przepływów pracy. Użyj [Przejrzyj przepływy pracy narzędzia porad](Review-Tool-User-Guide/Workflows.md) artykuł, aby zdefiniować niestandardowy przepływ pracy. Ten przepływ pracy używa funkcji Rozpoznawania zawartości moderatora można wyodrębnić tekst z obrazu próbki. Następnie tworzy przeglądu w narzędziu przeglądu.

### <a name="the-sample-image"></a>Obraz przykładowej

Zapisz [obraz przykładowy](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na dysk lokalny. Ten obraz potrzebę korzystania.

### <a name="the-designer-view"></a>Widok projektanta

Wybierz **projektanta** kartę i [samouczek tworzenia przepływu pracy](Review-Tool-User-Guide/Workflows.md) do definiowania niestandardowego przepływu pracy. Na poniższej ilustracji przedstawiono projektanta **warunku** widoku. Zapoznaj się z samouczkiem, aby wyświetlić pozostałe kroki.

![Zawartości moderatora - warunek przepływu pracy](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>Widok JSON

Wybierz **JSON** kartę, aby wyświetlić poniższe definicji JSON niestandardowego przepływu pracy. Powiadomienie jak **Jeżeli to** instrukcje w definicji JSON odpowiadają kroki zdefiniowany widok projektanta.

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

Po przetestowaniu przepływu pracy na ekranie przepływy pracy, jest tworzony po dokonaniu przeglądu. Przejdź do **obrazu** w obszarze **Przejrzyj** wyświetlić zapoznania się z nimi.
Przepływ pracy utworzony przeglądu ponieważ przetestowane warunku głównego na obecność tekstu. Przejrzyj wyróżnione **`a`** tag w przeglądzie obrazu.

![Moderatora zawartości — prosty przepływu pracy w danych wyjściowych](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Zaawansowanych przepływów pracy z kombinacją

### <a name="the-sample-image"></a>Obraz przykładowej

Używać tego samego [obraz przykładowy](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) który został użyty w poprzedniej sekcji.

Jednak go, zmienić podstawowy warunek do kombinację dwóch kontroli. Oprócz sprawdzania, czy tekst, należy sprawdzić, czy tekst ma niestosownych żadnych wyrażeń. Przepływ pracy tworzy przeglądu, w przypadku odnalezienia tekst **i** wykryje niestosownych wyrażeń.

### <a name="the-designer-view"></a>Widok projektanta

Aby zmienić **warunku** do **kombinacja**, zmodyfikuj przepływ pracy. Na poniższej ilustracji przedstawiono nowy widok, który zostanie wyświetlony w projektancie.

![Zawartości moderatora - warunek zmodyfikowane przepływu pracy](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>Widok JSON

Wybierz **JSON** kartę, aby wyświetlić poniższe definicji JSON zmodyfikowany niestandardowy przepływ pracy. Powiadomienie jak **Jeżeli to** instrukcje w definicji JSON odpowiadają nowe kroki dodawane do przepływu pracy.

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

Po ponownie przetestuj przepływu pracy można znaleźć czy przeglądu nie został utworzony. Aby upewnić się, brak każdy Przegląd, przejdź do **obrazu** w obszarze **Przejrzyj**.
Przepływ pracy nie może utworzyć przeglądu, ponieważ nie można wykryć niestosownych wyrażeń w wyodrębnionego tekstu.

![Moderatora zawartości — dane wyjściowe zmodyfikowane przepływu pracy](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Przepływ pracy interfejsu API

[Operacje przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) udostępniają interfejs programowania, do funkcji przepływu pracy. Tworzenie przepływów pracy, Pobierz szczegóły przepływu pracy i aktualizacji definicji przepływu pracy przy użyciu interfejsu API przepływu pracy.

### <a name="get-all-workflow-details"></a>Szczegóły przepływu pracy GET [All]

**Przepływ pracy Get** operacji akceptuje następujące dane wejściowe:

- **zespołu**: identyfikator zespołu utworzonego podczas konfigurowania sieci [Przejrzyj konta narzędzia](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa przepływu pracy. Użyj `default` rozpoczynać się od znaku.
- **OCP-Apim-subskrypcji — klucz**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

Jeśli operacja zakończy się powodzeniem, **stanu odpowiedzi** jest `200 OK` i **zawartości odpowiedzi** definicji przepływu pracy w polu są wyświetlane w formacie JSON.
Aby dowiedzieć się więcej, przeczytaj [szybkiego startu konsoli przepływu pracy API](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Utwórz lub zaktualizuj przepływu pracy

Operacja tworzenia i aktualizacji umożliwia tworzenie przepływu pracy z interfejsu API.

**Przepływu pracy — Tworzenie lub aktualizacja** operacji akceptuje następujące dane wejściowe:

- **zespołu**: identyfikator zespołu utworzonego podczas konfigurowania sieci [Przejrzyj konta narzędzia](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Nazwa przepływu pracy. Użyj `default` rozpoczynać się od znaku.
- **OCP-Apim-subskrypcji — klucz**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

Jeśli operacja zakończy się powodzeniem, **stanu odpowiedzi** jest `200 OK` i **zawartości odpowiedzi** wyświetlana `true`. Aby dowiedzieć się więcej, [przetestuj `Create` operacji](try-review-api-job.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak utworzyć niestandardowe przepływy pracy, zapoznaj się [Przejrzyj samouczek przepływu pracy narzędzia](Review-Tool-User-Guide/Workflows.md). 

Przetestuj [konsoli przepływu pracy API](try-review-api-job.md) i użyj przykłady kodu interfejsu API REST. 

Na koniec Użyj niestandardowych przepływów pracy z **zadania** operacje jako shon w [API zadania konsoli](try-review-api-job.md) i [szybkiego startu .NET zadania](moderation-jobs-quickstart-dotnet.md).
