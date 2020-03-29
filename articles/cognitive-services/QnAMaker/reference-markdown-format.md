---
title: Markdown format - QnA Maker
description: Poniżej znajduje się lista formatów znaczników, których można użyć w tekście odpowiedzi programu QnA Maker.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045395"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Format markdown obsługiwany w tekście odpowiedzi programu QnA Maker

QnA Maker przechowuje tekst odpowiedzi jako znacznik. Istnieje wiele smaków markdown. Aby upewnić się, że tekst odpowiedzi jest zwracany i wyświetlany poprawnie, użyj tego odwołania.

Użyj samouczka **[CommonMark,](https://commonmark.org/help/tutorial/index.html)** aby zweryfikować markdown. Samouczek ma funkcję **Wypróbuj do** szybkiego sprawdzania poprawności kopiowania/wklejać.

## <a name="supported-markdown-format"></a>Obsługiwany format znaczników

Poniżej znajduje się lista formatów znaczników, których można użyć w tekście odpowiedzi programu QnA Maker.

|Przeznaczenie|Format|Przykładowy kod języka Markdown|Renderowanie<br>jak wyświetli się w chat bot|
|--|--|--|--|
Nowa linia między 2 zdaniami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formatowanie nowej linii między dwoma zdaniami](./media/qnamaker-concepts-datasources/format-newline.png)|
|Nagłówki od h1 do h6, `#` liczba oznaczeń nagłówka. 1 `#` jest h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![format z nagłówkami znaczników](./media/qnamaker-concepts-datasources/format-headers.png)<br>![format z nagłówkami znaczników od H1 do H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursywa |`*text*`|`How do I create a bot with *QnA Maker*?`|![format z kursywą](./media/qnamaker-concepts-datasources/format-italics.png)|
|Mocny (pogrubiony)|`**text**`|`How do I create a bot with **QnA Maker**?`|![z mocnym oznaczeniem dla pogrubienia](./media/qnamaker-concepts-datasources/format-strong.png)|
|Adres URL linku|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![format adresu URL (hiperłącze)](./media/qnamaker-concepts-datasources/format-url.png)|
|*Adres URL obrazu publicznego|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![format adresu URL obrazu publicznego ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Przekreślenie|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![format przekreślenia](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Pogrubienie i kursywa|`***text***`|`How can I create a ***QnA Maker*** bot?`|![format pogrubienia i kursywy](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Pogrubiony adres URL łącza|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![format pogrubionego adresu URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Kursywa URL linku|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![format kursywy URL](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Symbole znaczników ucieczki|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![format kursywy URL](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista uporządkowana|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>W poprzednim przykładzie użyto automatycznego numerowania wbudowanego w markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>W poprzednim przykładzie użyto numerowania jawnego.|![format uporządkowanej listy](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Listy nieuporządkowane|`\n * item1 \n * item2`<br>lub<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![format listy nieurządzonych](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listy zagnieżdżone|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Listy uporządkowane i nieurządzone można zagnieżdżać razem. Karta `\t`, wskazuje poziom wcięci elementu podrzędnego.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![format zagnieżdżonej listy niezaudłonej](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![format zagnieżdżonej uporządkowanej listy](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker nie przetwarza obrazu w żaden sposób. Jest rolą aplikacji klienckiej do renderowania obrazu.

Jeśli chcesz dodać zawartość przy użyciu interfejsów API bazy wiedzy update/replace, a zawartość/plik zawiera znaczniki html, możesz zachować kod HTML w pliku, zapewniając, że otwieranie i zamykanie znaczników jest konwertowane w zakodowanym formacie.

| Zachowywanie kodu HTML  | Reprezentacja w żądaniu interfejsu API  | Reprezentacja w KB |
|-----------|---------|-------------------------|
| Tak | \&lt;br\&gt; | &lt;Br&gt; |
| Tak | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;&lt;nagłówek /h3&gt; |

Ponadto CR LF(\r\n) są konwertowane na \n w KB. LF(\n) jest zachowana w stanie, w jakim jest. Jeśli chcesz uciec przed dowolną sekwencją ucieczki, taką jak \t lub\\\\\n, możesz użyć ukośnika odwrotnego, na przykład:\\\\' r n' i '\\\\t'

## <a name="next-steps"></a>Następne kroki

Przejrzyj [formaty plików testowania wsadowego](reference-tsv-format-batch-testing.md).
