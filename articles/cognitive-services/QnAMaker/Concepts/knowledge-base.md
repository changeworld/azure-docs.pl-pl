---
title: Importowanie ze źródeł danych — QnA Maker
description: Baza wiedzy QnA Maker składa się z zestawu zestawów pytań i odpowiedzi (QnA) oraz opcjonalnych metadanych skojarzonych z poszczególnymi parami QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219688"
---
# <a name="importing-from-data-sources"></a>Importowanie ze źródeł danych

Baza wiedzy składa się z zestawów pytań i odpowiedzi, które są udostępniane przez publiczne adresy URL i pliki.

## <a name="data-source-locations"></a>Lokalizacje źródeł danych

Zawartość jest przenoszona do bazy wiedzy ze źródła danych. Lokalizacje źródeł danych to **publiczne adresy URL lub pliki**, które nie wymagają uwierzytelniania.

Wyjątkiem są [pliki programu SharePoint](../how-to/add-sharepoint-datasources.md)zabezpieczone przy użyciu uwierzytelniania. Zasoby programu SharePoint muszą być plikami, a nie stronami sieci Web. Jeśli adres URL jest zakończony rozszerzeniem sieci Web, na przykład. ASPX nie zostanie on zaimportowany do QnA Maker z programu SharePoint.

## <a name="chit-chat-content"></a>Zawartość rozmowy Chit

Zestaw zawartości Chit Chat QnA jest oferowany jako kompletne źródło danych zawartości w kilku językach i stylach konwersacji. Może to być punkt wyjścia do osobowości Twój bot, a jego pozwoli zaoszczędzić czas i pieniądze, zapisywania ich od podstaw. Dowiedz się [, jak dodać](../how-to/chit-chat-knowledge-base.md) ten zestaw zawartości do bazy wiedzy.

## <a name="structured-data-format-through-import"></a>Format danych ze strukturą za pośrednictwem importu

Importowanie bazy wiedzy zastępuje zawartości istniejącej bazie wiedzy knowledge base. Import wymaga strukturalnego pliku `.tsv` zawierającego pytania i odpowiedzi. Te informacje pomagają w QnA Maker zgrupować zestawy odpowiedzi i atrybutów do określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturalny format wielowymiarowy przy użyciu importu

Można utworzyć wiele przełączanych konwersacji w formacie pliku `.tsv`. Format umożliwia tworzenie wielostopniowych konwersacji przez analizowanie poprzednich dzienników rozmowy (z innymi procesami, nie przy użyciu QnA Maker), a następnie Tworzenie pliku `.tsv` za pośrednictwem automatyzacji. Zaimportuj plik w celu zamienienia istniejącej bazy wiedzy.

> [!div class="mx-imgBorder"]
> model koncepcyjny ![3 poziomów pytań z obsługą wieloletnich](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Kolumna dla `.tsv`z obsługą wieloczęściową, charakterystyczną dla wielowierszowego, jest **wyświetlane**. Przykład `.tsv`, pokazany w programie Excel, pokazuje informacje do uwzględnienia w celu zdefiniowania elementów podrzędnych obejmujących wiele wyłączeń:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** jest liczbą, a **DisplayText** jest tekstem, który nie powinien zawierać promocji.

> [!div class="mx-imgBorder"]
> ![przykład pytań z obsługą wieloletnią, jak pokazano w programie Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Eksportuj jako przykład

Jeśli nie wiesz, jak reprezentować zestaw QnA w pliku `.tsv`, Utwórz zestaw w portalu QnA Maker, Zapisz, a następnie wyeksportuj bazę wiedzy, aby zapoznać się z przykładem sposobu reprezentowania zestawu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia rozwoju bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz też

Skorzystaj z QnA Maker informacji o [promocji](../reference-markdown-format.md) , aby ułatwić formatowanie odpowiedzi.

[Omówienie usługi QnA Maker](../Overview/overview.md)

Tworzenie i edytowanie bazy wiedzy przy użyciu:
* [Interfejs API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generuj odpowiedź z:
* [Interfejs API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
