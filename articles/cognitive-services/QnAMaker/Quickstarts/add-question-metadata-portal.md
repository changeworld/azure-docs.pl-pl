---
title: 'Szybki Start: dodawanie pytań i odpowiedzi w portalu QnA Maker'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 92735d8982fb1364d5ebfe0494f5ee51f4302469
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794009"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Szybki Start: dodawanie pytań i odpowiedzi za pomocą portalu QnA Maker

Po utworzeniu bazy wiedzy Dodaj do niej zestawy pytań i odpowiedzi, aby użytkownicy mogli znaleźć odpowiednią odpowiedź na pytanie.

Właściwą odpowiedzią jest pojedyncza odpowiedź, ale może być wiele sposobów, aby klient mógł zadać pytanie, które prowadzi do tej pojedynczej odpowiedzi.

Na przykład pytania w poniższej tabeli dotyczą limitów usługi platformy Azure, ale każdy z nich musi wykonać inną usługę platformy Azure. 

<a name="qna-table"></a>


|Zestaw|Pytania|Odpowiedź|Metadane|
|--|--|--|--|
|1\.|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2\.|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Po dodaniu metadanych do zestawu pytań i odpowiedzi aplikacja kliencka może:

* Zażądaj odpowiedzi, które pasują tylko do niektórych metadanych.
* Odbieraj wszystkie odpowiedzi, ale nie Przetwarzaj odpowiedzi w zależności od metadanych dla każdej odpowiedzi.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa QnA Maker
* Baza wiedzy utworzona w ramach tej usługi QnA Maker

Oba zostały utworzone w [pierwszym przewodniku szybki start](../how-to/create-knowledge-base.md).

## <a name="sign-in-to-the-qna-maker-portal"></a>Logowanie się do portalu QnA Maker

1. Zaloguj się do [portalu QNA Maker](https://www.qnamaker.ai).

1. Wybierz istniejącą bazę wiedzy. Jeśli nie masz bazy wiedzy, Wróć do [poprzedniego przewodnika Szybki Start](../how-to/create-knowledge-base.md) i Zakończ procedurę tworzenia bazy wiedzy.

## <a name="add-additional-alternatively-phrased-questions"></a>Dodawanie dodatkowych pytań z możliwością frazy 

Bieżąca baza wiedzy z [poprzedniego przewodnika Szybki Start](../how-to/create-knowledge-base.md)zawiera QNA Maker pytania i odpowiedzi dotyczące rozwiązywania problemów. Te zestawy zostały utworzone, gdy adres URL został dodany do bazy wiedzy podczas procesu tworzenia. 

Po zaimportowaniu tego adresu URL zostanie utworzona tylko jedno pytanie z jedną odpowiedzią. 

W tej procedurze należy dodać dodatkowe pytania.

1. Na stronie **Edytuj** Użyj pola tekstowego Wyszukaj powyżej zestawów pytań i odpowiedzi, aby znaleźć pytania `How large a knowledge base can I create?`

1. W kolumnie **pytania** wybierz pozycję **+ Dodaj alternatywne sformułowanie** , a następnie Dodaj każde nowe sformułowanie, które podano w poniższej tabeli.

    |Alternatywne sformułowanie|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Wybierz pozycję **Zapisz i poszkol** , aby ponownie przeprowadzić uczenie bazy wiedzy. 

1. Wybierz pozycję **test**, a następnie wprowadź pytanie blisko jednego z nowych alternatywnych frazy, ale nie dokładnie ten sam wyraz:

    `What GB size can a knowledge base be?`

    Prawidłowa odpowiedź jest zwracana w formacie promocji: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    W przypadku wybrania opcji **Sprawdź** w odpowiedzi na odpowiedź można zobaczyć więcej odpowiedzi, ale nie ma tego samego wysokiego poziomu zaufania. 

    Nie dodawaj każdej możliwej kombinacji alternatywnych wyrazów. Włącz [aktywną naukę](../how-to/improve-knowledge-base.md)w ramach usługi QNA Maker, aby znaleźć alternatywną frazy, która najlepiej odpowiada potrzebom użytkowników w bazie wiedzy.

1. Ponownie wybierz **test** , aby zamknąć okno testowe.

## <a name="add-metadata-to-filter-the-answers"></a>Dodawanie metadanych w celu filtrowania odpowiedzi

Dodanie metadanych do zestawu pytań i odpowiedzi umożliwia aplikacji klienckiej żądanie filtrowanych odpowiedzi. Ten filtr jest stosowany przed zastosowaniem [pierwszej i drugiej rangi](../concepts/knowledge-base.md#ranker-process) .

1. Dodaj drugie pytanie i zestaw odpowiedzi, bez metadanych, z [pierwszej tabeli w tym przewodniku szybki start](#qna-table), a następnie wykonaj poniższe kroki. 

1. Wybierz pozycję **Wyświetl opcje**, a następnie wybierz pozycję **Pokaż metadane**. 

1. Dla dodawanego pytania i odpowiedzi wybierz pozycję **Dodaj Tagi metadanych**, a następnie dodaj nazwę `service` i wartość `search`, `service:search`.

1. Dodaj kolejne Tagi metadanych o nazwie `link_in_answer` i wartości `false`, `link_in_answer:false`.

1. Wyszukaj pierwszą odpowiedź w tabeli, `How large a knowledge base can I create?`. 
1. Dodaj pary metadanych dla tych samych dwóch tagów metadanych:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Masz teraz dwa pytania z tymi samymi tagami metadanych o różnych wartościach. 

1. Wybierz pozycję **Zapisz i poszkol** , aby ponownie przeprowadzić uczenie bazy wiedzy. 

1. Wybierz pozycję **Publikuj** w górnym menu, aby przejść do strony publikowanie. 
1. Wybierz przycisk **Publikuj** , aby opublikować bieżącą bazę wiedzy w punkcie końcowym queryable. 
1. Po opublikowaniu bazy wiedzy wybierz kartę **zwinięcie** , aby zobaczyć przykładowe polecenie zwinięcie użyte do wygenerowania odpowiedzi z bazy wiedzy.
1. Skopiuj polecenie do konsoli notatek lub innego środowiska do edycji, aby można było edytować polecenie. Edytuj pod kątem własnej nazwy zasobu, identyfikatora bazy wiedzy i klucza punktu końcowego:

    |Replace|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Zwróć uwagę, że pytanie jest tylko pojedynczym słowem, `size`, które może zwracać zestaw pytań i odpowiedzi. Tablica `strictFilters` informuje odpowiedź, aby zmniejszyć do zaledwie `qna_maker` odpowiedzi. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. Odpowiedź zawiera tylko odpowiedź, która spełnia kryteria filtru. 

    Następująca odpowiedź programu zwinięcie została sformatowana w celu zapewnienia czytelności:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Jeśli istnieje pytanie i odpowiedź, które nie spełniły wyszukiwanego terminu, ale spełniały filtr, nie zostanie on zwrócony. Zamiast tego zostanie zwrócona ogólna `No good match found in KB.` odpowiedzi.

    Upewnij się, że nazwy metadanych i wartości są przechowywane w wymaganym limicie. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj odpowiedź przy użyciu programu Poster](get-answer-from-kb-using-postman.md)