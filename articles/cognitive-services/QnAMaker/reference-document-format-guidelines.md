---
title: Wytyczne dotyczące formatu dokumentu importowania — QnA Maker
description: Dowiedz się, w jaki sposób typy adresów URL są używane do importowania i tworzenia zestawów QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651840"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Wskazówki dotyczące formatu dla zaimportowanych dokumentów i adresów URL

Zapoznaj się z tymi wskazówkami dotyczącymi formatowania, aby uzyskać najlepsze wyniki dla zawartości.

## <a name="formatting-considerations"></a>Zagadnienia dotyczące formatowania

Po zaimportowaniu pliku lub adresu URL QnA Maker konwertuje i zapisuje zawartość w [formacie promocji](https://en.wikipedia.org/wiki/Markdown). Proces konwersji dodaje do tekstu nowe wiersze, takie jak `\n\n`. Wiedza o formacie promocji pozwala zrozumieć przekonwertowaną zawartość i zarządzać zawartością bazy wiedzy.

Jeśli dodasz lub edytujesz zawartość bezpośrednio w bazie wiedzy, użyj **formatowania promocji** w celu utworzenia zawartości tekstu sformatowanego lub zmiany zawartości formatu promocji, która jest już w odpowiedzi. QnA Maker obsługuje duży format promocji, aby zapewnić możliwość wprowadzenia tekstu sformatowanego do zawartości. Jednak aplikacja kliencka, taka jak rozmowa bot, może nie obsługiwać tego samego zestawu formatów promocji. Ważne jest, aby przetestować wyświetlanie odpowiedzi aplikacji klienta.

## <a name="basic-document-formatting"></a>Podstawowe formatowanie dokumentu

QnA Maker identyfikuje sekcje i podsekcje oraz relacje w pliku na podstawie wizualizacji wizualnych, takich jak:

* rozmiar czcionki
* styl czcionki
* Numerowanie
* kolory

|Przykłady dotyczące dokumentów|
|--|
||



## <a name="product-manuals"></a>Podręczniki

Podręcznik jest zazwyczaj materiały wskazówki towarzyszące produktu. Umożliwia użytkownikowi konfigurowanie, użycia, obsługa i rozwiązywanie problemów z produktu. Gdy narzędzie QnA Maker przetwarza ręcznego, wyodrębnia nagłówków i podpozycji jako pytania i kolejne zawartość jako odpowiedzi. Zobacz przykład [tutaj](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Poniżej znajduje się przykład ręcznie za pomocą strony indeksu i zawartości hierarchicznej

 ![Ręczne przykład wiedzy produktu](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Wyodrębnianie sprawdza się najlepiej w instrukcji, które mają spisu treści i/lub strony indeksu oraz z nagłówkami hierarchiczną strukturę.

## <a name="brochures-guidelines-papers-and-other-files"></a>Broszury, wskazówek, dokumenty i inne pliki

Wiele typów dokumentów, mogą być przetwarzane do generowania pary pytań i odpowiedzi, pod warunkiem mają Wyczyść strukturę i układ. Obejmują one: broszury, wytyczne, raporty, oficjalne dokumenty, dokumenty naukowe, zasady, książki itp. Zobacz przykład [tutaj](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Poniżej przedstawiono przykład doc lub częściową strukturą, bez indeksu:

 ![Usługa Azure Blob storage lub częściową strukturą dokumentu](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Dokument o określonej strukturze pytań i odpowiedzi

Format ze strukturą — odpowiedzi na pytania w plikach DOC, jest w formie przemienne pytania i odpowiedzi w każdym wierszu, jedno pytanie w każdym wierszu następuje jej odpowiedzi w następującym wierszu, jak pokazano poniżej:

```text
Question1

Answer1

Question2

Answer2
```

Poniżej przedstawiono przykład ze strukturą dokument programu word pytań i odpowiedzi:

 ![Strukturalne pytań i odpowiedzi dokumentu przykład wiedzy](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturalne pliki *txt*, *TSV* i *xls*

Bazami w postaci pliku Structured *. txt*, *. tsv* lub *xls* można także przekazać do QNA Maker, aby utworzyć lub rozszerzyć bazę wiedzy.  To może to być zwykły tekst lub może mieć zawartości w formacie RTF lub HTML.

| Pytanie  | Odpowiedź  | Metadane (1 klucz: 1 wartość) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Wszelkie dodatkowe kolumny w pliku źródłowym są ignorowane.

### <a name="example-of-structured-excel-file"></a>Przykład strukturalnego pliku programu Excel

Poniżej znajduje się przykład strukturalnego pliku QnA *. xls* z zawartością HTML:

 ![Przykład wiedzy pytań i odpowiedzi ze strukturą w programie excel](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel

Poniżej znajduje się przykład strukturalnego pliku QnA *. xls* z kilkoma dodatkowymi pytaniami dotyczącymi pojedynczej odpowiedzi:

 ![Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po zaimportowaniu pliku para pytań i odpowiedzi znajduje się w bazie wiedzy, jak pokazano poniżej:

 ![Zrzut ekranu z alternatywnymi pytaniami dotyczącymi pojedynczej odpowiedzi zaimportowanej do bazy wiedzy](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Format danych ze strukturą za pośrednictwem importu

Importowanie bazy wiedzy zastępuje zawartości istniejącej bazie wiedzy knowledge base. Importuj wymaga pliku tsv ze strukturą, który zawiera informacje o źródle danych. Te informacje pomagają w QnA Maker zgrupować pary odpowiedzi i atrybutów do określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatowanie dokumentu wielowymiarowego

* Użyj nagłówków i nagłówków podrzędnych do określenia hierarchii. Na przykład można H1 do określenia elementu Parent QnA i H2, aby zauważyć QnA, które należy wykonać jako monit. Użyj małego rozmiaru nagłówka, aby zauważyć dalszą hierarchię. Nie używaj stylu, koloru ani innego mechanizmu, aby oznaczać strukturę w dokumencie, QnA Maker nie Wyodrębnij wyświetlonych przez siebie wierszy.
* Pierwszy znak nagłówka musi być wielkimi literami.
* Nie należy kończyć nagłówka ze znakiem zapytania, `?`.


|Przykłady dotyczące dokumentów|
|--|
||