---
title: Wskazówki dotyczące formatu dokumentu importu — QnA Maker
description: Dowiedz się, jak typy adresów URL są używane do importowania i tworzenia zestawów QnA.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 1e006520ce5c61f2da3f425af2332b0d41cc3881
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754907"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Wskazówki dotyczące formatowania importowanych dokumentów i adresów URL

Zapoznaj się z tymi wskazówkami dotyczącymi formatowania, aby uzyskać najlepsze wyniki dla zawartości.

## <a name="formatting-considerations"></a>Zagadnienia dotyczące formatowania

Po zaimportowaniu pliku lub adresu URL program QnA Maker konwertuje i przechowuje zawartość w [formacie znaczników.](https://en.wikipedia.org/wiki/Markdown) Proces konwersji dodaje nowe wiersze `\n\n`w tekście, takie jak . Znajomość formatu znaczników pomaga zrozumieć przekonwertowane treści i zarządzać zawartością bazy wiedzy.

Jeśli zawartość zostanie dodana lub edytowana bezpośrednio w bazie wiedzy, użyj **formatowania znaczników,** aby utworzyć zawartość tekstu sformatowania lub zmienić zawartość formatu znaczników, która jest już w odpowiedzi. Program QnA Maker obsługuje większość formatu znaczników, aby zapewnić zawartość funkcji tekstu sformatowego. Jednak aplikacja kliencka, taka jak czat bot może nie obsługiwać tego samego zestawu formatów znaczników. Ważne jest, aby przetestować wyświetlanie odpowiedzi aplikacji klienckiej.

Zobacz pełną listę [typów zawartości i przykładów](./Concepts/content-types.md#file-and-url-data-types).

## <a name="basic-document-formatting"></a>Podstawowe formatowanie dokumentu

QnA Maker identyfikuje sekcje i podsekcje oraz relacje w pliku na podstawie wizualnych wskazówek, takich jak:

* rozmiar czcionki
* styl czcionki
* Numeracja
* kolory

## <a name="product-manuals"></a>Instrukcje obsługi produktu

Instrukcja jest zazwyczaj materiałem prowadzącym, który towarzyszy produktowi. Pomaga użytkownikowi skonfigurować, używać, konserwować i rozwiązywać problemy z produktem. Gdy program QnA Maker przetwarza podręcznik, wyodrębnia nagłówki i podpozycje jako pytania i późniejszą zawartość jako odpowiedzi. Zobacz przykład [tutaj](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Poniżej znajduje się przykład podręcznika ze stroną indeksu i zawartości hierarchicznej

 ![Przykład podręcznika produktu dla bazy wiedzy](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Wyodrębnianie działa najlepiej w podręcznikach, które mają spis treści i/lub stronę indeksu i przejrzystą strukturę z nagłówkami hierarchicznymi.

## <a name="brochures-guidelines-papers-and-other-files"></a>Broszury, wytyczne, dokumenty i inne pliki

Wiele innych typów dokumentów może być również przetwarzanych w celu generowania par kontroli jakości, pod warunkiem, że mają one przejrzystą strukturę i układ. Należą do nich: broszury, wytyczne, raporty, białe księgi, prace naukowe, polityki, książki itp. Zobacz przykład [tutaj](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Poniżej znajduje się przykład doc półstrukturalną, bez indeksu:

 ![Usługa Azure Blob storage — dok.](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Ustrukturyzowany dokument QnA

Format ustrukturyzowanych odpowiedzi na pytania w plikach DOC ma formę naprzemiennych pytań i odpowiedzi na wiersz, po jednym pytaniu w wierszu, po którym następuje odpowiedź w następującym wierszu, jak pokazano poniżej:

```text
Question1

Answer1

Question2

Answer2
```

Poniżej znajduje się przykład ustrukturyzowania dokumentu słownego QnA:

 ![Przykład ustrukturyzowania dokumentu QnA dla bazy wiedzy](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Pliki *TXT,* *TSV* i *XLS* strukturalne

QnAs w postaci ustrukturyzowanych plików *.txt*, *.tsv* lub *xls* można również przesłać do programu QnA Maker w celu utworzenia lub rozszerzenia bazy wiedzy.  Mogą to być zwykły tekst lub zawartość w RTF lub HTML.

| Pytanie  | Odpowiedź  | Metadane (1 klucz: 1 wartość) |
|-----------|---------|-------------------------|
| Pytanie 1 | Odpowiedź1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pytanie 2 | Odpowiedź2 |      `Key:Value`           |

Wszystkie dodatkowe kolumny w pliku źródłowym są ignorowane.

### <a name="example-of-structured-excel-file"></a>Przykład ustrukturyzowania pliku programu Excel

Poniżej znajduje się przykład ustrukturyzowania pliku QnA *.xls* z zawartością HTML:

 ![Ustrukturyzowany przykład QnA excel dla bazy wiedzy](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel

Poniżej znajduje się przykład ustrukturyzowania pliku QnA *.xls,* z kilkoma alternatywnymi pytaniami na jedną odpowiedź:

 ![Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po zaimportowaniu pliku para pytań i odpowiedzi znajduje się w bazie wiedzy, jak pokazano poniżej:

 ![Zrzut ekranu przedstawiający alternatywne pytania dotyczące pojedynczej odpowiedzi importowanej do bazy wiedzy](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Format danych strukturalnych poprzez importowanie

Importowanie bazy wiedzy zastępuje zawartość istniejącej bazy wiedzy. Import wymaga ustrukturyzowania pliku tsv zawierającego informacje o źródle danych. Te informacje pomagają qna maker grupy par pytań odpowiedzi i przypisać je do określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Pytanie 1 | Odpowiedź1 | Adres URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pytanie 2 | Odpowiedź2 | Redakcji|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatowanie dokumentów z wieloma turami

* Nagłówki i nagłówki używają do oznaczania hierarchii. Na przykład można h1 oznaczyć nadrzędne QnA i h2, aby oznaczyć QnA, które powinny być traktowane jako monit. Użyj małego rozmiaru nagłówka, aby oznaczyć kolejną hierarchię. Nie używaj stylu, koloru ani innego mechanizmu, aby sugerować strukturę w dokumencie, program QnA Maker nie wyodrębni monitów z wieloma turami.
* Pierwszy znak nagłówka musi być kapitalizowany.
* Nie końkwuj nagłówka `?`znakiem zapytania, .

**Przykładowe dokumenty:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Korzyści contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Korzyści z contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Następne kroki

Zobacz pełną listę [typów zawartości i przykładów](./Concepts/content-types.md#file-and-url-data-types)