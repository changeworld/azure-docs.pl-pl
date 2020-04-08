---
title: Importowanie ze źródeł danych — QnA Maker
description: Baza wiedzy QnA Maker składa się z zestawu zestawów pytań i odpowiedzi (QnA) i opcjonalnych metadanych skojarzonych z każdą parą QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804286"
---
# <a name="importing-from-data-sources"></a>Importowanie ze źródeł danych

Baza wiedzy składa się z zestawów pytań i odpowiedzi wprowadzanych przez publiczne adresy URL i pliki.

## <a name="data-source-locations"></a>Lokalizacje źródeł danych

Zawartość jest wprowadzana do bazy wiedzy ze źródła danych. Lokalizacje źródeł danych to **publiczne adresy URL lub pliki,** które nie wymagają uwierzytelniania.

Wyjątkiem są [pliki programu SharePoint](../how-to/add-sharepoint-datasources.md)zabezpieczone uwierzytelnianiem. Zasoby programu SharePoint muszą być plikami, a nie stronami sieci Web. Jeśli adres URL kończy się rozszerzeniem sieci Web, takim jak . ASPX, nie będzie importować do programu QnA Maker z programu SharePoint.

## <a name="chit-chat-content"></a>Treści na czacie Chit

Zestaw zawartości Chit chat QnA jest oferowany jako kompletne źródło danych zawartości w kilku językach i stylach konwersacyjnych. Może to być punkt wyjścia dla osobowości twojego bota, a to pozwoli Ci zaoszczędzić czas i koszty pisania ich od podstaw. Dowiedz [się, jak dodać](../how-to/chit-chat-knowledge-base.md) ten zestaw zawartości do bazy wiedzy.

## <a name="structured-data-format-through-import"></a>Format danych strukturalnych poprzez importowanie

Importowanie bazy wiedzy zastępuje zawartość istniejącej bazy wiedzy. Import wymaga ustrukturyzowania `.tsv` pliku zawierającego pytania i odpowiedzi. Te informacje pomagają qna maker grupy zestawów pytań odpowiedzi i przypisać je do określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Pytanie 1 | Odpowiedź1 | Adres URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pytanie 2 | Odpowiedź2 | Redakcji|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Ustrukturyzowany format wielokierunkowy poprzez import

Można tworzyć konwersacje wieloewymiężne w formacie `.tsv` pliku. Format zapewnia możliwość tworzenia rozmów wieloecowych poprzez analizowanie poprzednich dzienników czatu (z innymi procesami, `.tsv` bez korzystania z QnA Maker), a następnie tworzenie pliku za pomocą automatyzacji. Zaimportuj plik, aby zastąpić istniejącą bazę wiedzy.

> [!div class="mx-imgBorder"]
> ![Model koncepcyjny 3 poziomów pytań wielookrętowych](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Kolumna dla wielu `.tsv`zakrętów, specyficznych dla wielu **zakrętów,** to Monity . Przykład `.tsv`, pokazany w programie Excel, pokaż informacje, które mają być uwzględnione w celu zdefiniowania niań podrzędnych z wieloma kolejami:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** jest numericzny, a **displayText** to tekst, który nie powinien zawierać znaczników.

> [!div class="mx-imgBorder"]
> ![Przykład pytań z wieloma kolejami, jak pokazano w programie Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Eksport jako przykład

Jeśli nie masz pewności, jak reprezentować `.tsv` parę QnA w pliku:
* Użyj tego [przykładu do pobrania z gitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Możesz też utworzyć zestaw w portalu QnA Maker, zapisać, a następnie wyeksportować bazę wiedzy, aby uzyskać przykład reprezentowania zestawu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia opracowywania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz też

Użyj odwołania QnA Maker [Markdown,](../reference-markdown-format.md) aby pomóc ci sformatować odpowiedzi.

[Omówienie usługi QnA Maker](../Overview/overview.md)

Tworzenie i edytowanie bazy wiedzy za pomocą:
* [Interfejs API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Zestaw SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Wygeneruj odpowiedź za pomocą:
* [Interfejs API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Zestaw SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
