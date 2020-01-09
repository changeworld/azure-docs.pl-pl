---
title: Źródła danych obsługiwane — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker automatycznie wyodrębnia pary odpowiedzi pytań przechowywanych jako strony sieci Web, pliki PDF lub pliki DOC programu MS Word lub strukturalne pliki zawartości QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: 7f51021df2234438eb81a29887a714b0f09d0998
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563194"
---
# <a name="data-sources-for-qna-maker-content"></a>Źródła danych dla zawartości usługi QnA Maker

Usługa QnA Maker wyodrębnia automatycznie pary pytań i odpowiedzi z częściową strukturą zawartości, takiej jak — często zadawane pytania, podręczników, wskazówek, Obsługa dokumentów i zasady przechowywane jako strony sieci web, pliki PDF lub pliki programu MS Word doc. Zawartości mogą być również dodawane do bazy wiedzy ze strukturą pytań i odpowiedzi, pliki zawartości.

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Typy danych plików i adresów URL

Poniższa tabela zawiera podsumowanie typów formaty zawartości i plików, które są obsługiwane przez narzędzie QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania<br> (Płaski, z sekcjami lub z stroną główną tematów)<br>Strony pomocy technicznej <br> (Artykuły z artykułami z artykułu z jedną stroną, rozwiązywanie problemów itp.)|[Zwykłe często zadawane pytania](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Często zadawane pytania dotyczące linków](https://www.microsoft.com/en-us/software-download/faq),<br> [Często zadawane pytania dotyczące strony głównej tematów](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artykuł pomocy technicznej](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PLIK PDF / DOC|Najczęściej<br> Podręcznik produktu,<br> Broszury,<br> Drukowane<br> Zasady ulotek,<br> Przewodnik pomocy technicznej,<br> QnA strukturalne,<br> I tak dalej.|[Strukturalny QNA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Przykładowy produkt. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Przykład semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykładowy oficjalny dokument. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Przykład Multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Ze strukturą pliku pytań i odpowiedzi<br> (w tym RTF, obsługa HTML)|[Przykładowy FAQ.xls pytań i odpowiedzi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Ze strukturą pliku pytań i odpowiedzi|[Przykładowe chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Importowanie i eksportowanie bazy wiedzy

**Pliki TSV i xls**z wyeksportowanych baz wiedzy mogą być używane tylko przez zaimportowanie plików ze strony **ustawienia** w portalu QNA Maker. Nie można ich używać jako źródeł danych podczas tworzenia bazy wiedzy lub z funkcji **+ Dodaj plik** lub **+ Dodaj adres URL** na stronie **Ustawienia** .

## <a name="data-source-locations"></a>Lokalizacje źródeł danych

Lokalizacje źródeł danych to **publiczne adresy URL lub pliki**, które nie wymagają uwierzytelniania.

Jeśli potrzebujesz uwierzytelniania dla źródła danych, Rozważ następujące metody, aby uzyskać te dane do QnA Maker:

* [Pobierz plik ręcznie](#download-file-from-authenticated-data-source-location) i zaimportuj do QNA Maker
* Importuj plik dla uwierzytelnionej [lokalizacji programu SharePoint](#import-file-from-authenticated-sharepoint)

### <a name="download-file-from-authenticated-data-source-location"></a>Pobierz plik z lokalizacji uwierzytelnionego źródła danych

Jeśli masz uwierzytelniony plik (nie w lokalizacji uwierzytelnionej programu SharePoint) lub adres URL, alternatywną opcją jest pobranie pliku z witryny uwierzytelnionej na komputer lokalny, a następnie dodanie pliku z komputera lokalnego do bazy wiedzy.

### <a name="import-file-from-authenticated-sharepoint"></a>Importuj plik ze uwierzytelnionego programu SharePoint

[Lokalizacje źródeł danych programu SharePoint](../How-to/add-sharepoint-datasources.md) mogą udostępniać uwierzytelnione **pliki**. Zasoby programu SharePoint muszą być plikami, a nie stronami sieci Web. Jeśli adres URL jest zakończony rozszerzeniem sieci Web, na przykład **. ASPX**nie zostanie on zaimportowany do QNA Maker z programu SharePoint.


## <a name="faq-urls"></a>Adresy URL — często zadawane pytania

Narzędzie QnA Maker obsługuje często zadawane pytania dotyczące stron sieci web w 3 różnych formularzach: zwykły często zadawane pytania dotyczące strony, strony — często zadawane pytania wraz z łączami, stron — często zadawane pytania przy użyciu określonej strony głównej tematy.

### <a name="plain-faq-pages"></a>Zwykły stron — często zadawane pytania

Jest to najczęściej spotykanym typem z często Zadawanymi pytaniami, w którym odpowiedzi natychmiast wykonaj na pytania w tej samej stronie.

Poniżej przedstawiono przykład zwykłego strony — często zadawane pytania:

![Zwykły przykładowa strona — często zadawane pytania dla bazy wiedzy](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Często zadawane pytania dotyczące strony wraz z łączami

W tym typie — często zadawane pytania pytania są zagregowane ze sobą i są połączone z odpowiedzi, które znajdują się w różne sekcje w tej samej stronie lub na różnych stronach.

Poniżej przedstawiono przykład strony — często zadawane pytania wraz z łączami w sekcji, które znajdują się w tej samej stronie:

 ![Przykładowa strona łącze — często zadawane pytania sekcji dla bazy wiedzy](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>Często zadawane pytania dotyczące stron przy użyciu określonej strony głównej — tematy

Ten typ — często zadawane pytania ma stronę główną z tematów, gdzie każdy temat jest link do jego odpowiednich znacznie w innej strony. W tym miejscu usługi QnA Maker przeszukuje połączonych stron w celu wyodrębnienia odpowiednie pytania i odpowiedzi.

Poniżej przedstawiono przykład często Zadawanymi pytaniami, gdzie głównej tematów zawiera linki do sekcji często zadawane pytania na różnych stronach.

 ![Przykładowa strona link bezpośredni — często zadawane pytania dla bazy wiedzy](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>Adresy URL pomocy technicznej

QnA Maker mogą przetwarzać strony sieci Web pomocy technicznej z częściową strukturą, takie jak artykuły w sieci Web, które opisują sposób wykonywania danego zadania, diagnozowania i rozwiązywania danego problemu oraz najważniejszych rozwiązań dla danego procesu. Wyodrębnianie działa najlepiej w przypadku zawartości, która ma jasno strukturę z nagłówkami hierarchicznymi.

> [!NOTE]
> Wyodrębnianie artykułów pomocy technicznej jest nową funkcją i znajduje się na wczesnych etapach. Najlepiej sprawdza się w przypadku prostych stron, które są dobrze strukturalne i nie zawierają złożonych nagłówków/stopek.

![QnA Maker obsługuje wyodrębnianie ze stron sieci Web z częściową strukturą, w przypadku których przejrzysta struktura jest prezentowana z nagłówkami hierarchicznymi](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Plik PDF / pliki dokumentacji

Narzędzie QnA Maker można przetwarzają zawartość częściową strukturą w pliku PDF lub dokumentu i przekonwertować go na znacznie. Dobre pliku, który można wyodrębnić dobrze jest jednym gdzie zawartość jest zorganizowana w pewnej postaci ze strukturą i jest reprezentowana w sekcjach dobrze zdefiniowane. Sekcje mogą dodatkowo podzielony na podsekcje lub podtematy. Wyodrębnianie sprawdza się najlepiej w dokumentach, które mają strukturę hierarchiczną nagłówki.

QnA Maker identyfikuje sekcje i podsekcje oraz relacje w pliku na podstawie wizualizacji wizualnych, takich jak rozmiar czcionki, styl czcionki, numeracja, kolory itp. Częściowo strukturalne pliki PDF lub DOC mogą być podręcznikami, często zapytaniami, wskazówkami, zasadami, broszurami, ulotkami i wieloma innymi rodzajami plików. Poniżej przedstawiono niektóre przykładowe typy tych plików.

### <a name="product-manuals"></a>Podręczniki

Podręcznik jest zazwyczaj materiały wskazówki towarzyszące produktu. Umożliwia użytkownikowi konfigurowanie, użycia, obsługa i rozwiązywanie problemów z produktu. Gdy narzędzie QnA Maker przetwarza ręcznego, wyodrębnia nagłówków i podpozycji jako pytania i kolejne zawartość jako odpowiedzi. Zobacz przykład [tutaj](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Poniżej znajduje się przykład ręcznie za pomocą strony indeksu i zawartości hierarchicznej

 ![Ręczne przykład wiedzy produktu](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Wyodrębnianie sprawdza się najlepiej w instrukcji, które mają spisu treści i/lub strony indeksu oraz z nagłówkami hierarchiczną strukturę.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broszury, wskazówek, dokumenty i inne pliki

Wiele typów dokumentów, mogą być przetwarzane do generowania pary pytań i odpowiedzi, pod warunkiem mają Wyczyść strukturę i układ. Obejmują one: broszury, wytyczne, raporty, oficjalne dokumenty, dokumenty naukowe, zasady, książki itp. Zobacz przykład [tutaj](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Poniżej przedstawiono przykład doc lub częściową strukturą, bez indeksu:

 ![Usługa Azure Blob storage lub częściową strukturą dokumentu](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Dokument o określonej strukturze pytań i odpowiedzi

Format ze strukturą — odpowiedzi na pytania w plikach DOC, jest w formie przemienne pytania i odpowiedzi w każdym wierszu, jedno pytanie w każdym wierszu następuje jej odpowiedzi w następującym wierszu, jak pokazano poniżej:

```text
Question1

Answer1

Question2

Answer2
```

Poniżej przedstawiono przykład ze strukturą dokument programu word pytań i odpowiedzi:

 ![Strukturalne pytań i odpowiedzi dokumentu przykład wiedzy](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturalne *TXT*, *TSV* i *XLS* plików

Struktura znacznie w formie *.txt*, *tsv* lub *xls* pliki można przekazać do usługi QnA Maker można utworzyć lub rozszerzyć bazy wiedzy.  To może to być zwykły tekst lub może mieć zawartości w formacie RTF lub HTML.

| Pytanie  | Odpowiedź  | Metadane (1 klucz: 1 wartość) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Wszelkie dodatkowe kolumny w pliku źródłowym są ignorowane.

### <a name="example-of-structured-excel-file"></a>Przykład strukturalnego pliku programu Excel

Poniżej znajduje się przykład ze strukturą QnA *xls* plik z zawartością HTML:

 ![Przykład wiedzy pytań i odpowiedzi ze strukturą w programie excel](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel

Poniżej znajduje się przykład strukturalnego pliku QnA *. xls* z kilkoma dodatkowymi pytaniami dotyczącymi pojedynczej odpowiedzi:

 ![Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po zaimportowaniu pliku para pytań i odpowiedzi znajduje się w bazie wiedzy, jak pokazano poniżej:

 ![Zrzut ekranu z alternatywnymi pytaniami dotyczącymi pojedynczej odpowiedzi zaimportowanej do bazy wiedzy](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Format danych ze strukturą za pośrednictwem importu

Importowanie bazy wiedzy zastępuje zawartości istniejącej bazie wiedzy knowledge base. Importuj wymaga pliku tsv ze strukturą, który zawiera informacje o źródle danych. Te informacje pomagają w QnA Maker zgrupować pary odpowiedzi i atrybutów do określonego źródła danych.

| Pytanie  | Odpowiedź  | Źródło| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Przez jej tradycyjne zredagowanie dodać do bazy wiedzy

Jeśli nie masz już istniejącą zawartość do wypełniania bazy wiedzy knowledge base, możesz dodać znacznie przez jej tradycyjne zredagowanie w QnA Maker wiedzy. Dowiedz się, jak można zaktualizować bazy wiedzy [tutaj](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Zagadnienia dotyczące formatowania

Po zaimportowaniu pliku lub adresu URL QnA Maker konwertuje i zapisuje zawartość w [formacie promocji](https://en.wikipedia.org/wiki/Markdown). Proces konwersji dodaje do tekstu nowe wiersze, takie jak `\n\n`. Wiedza o formacie promocji pozwala zrozumieć przekonwertowaną zawartość i zarządzać zawartością bazy wiedzy.

Jeśli dodasz lub edytujesz zawartość bezpośrednio w bazie wiedzy, użyj **formatowania promocji** w celu utworzenia zawartości tekstu sformatowanego lub zmiany zawartości formatu promocji, która jest już w odpowiedzi. QnA Maker obsługuje duży format promocji, aby zapewnić możliwość wprowadzenia tekstu sformatowanego do zawartości. Jednak aplikacja kliencka, taka jak rozmowa bot, może nie obsługiwać tego samego zestawu formatów promocji. Ważne jest, aby przetestować wyświetlanie odpowiedzi aplikacji klienta.

Poniżej znajduje się lista formatów promocji, których można użyć w QnA Maker:

|Przeznaczenie|Format|Przykładowy kod języka Markdown|Renderowanie<br>jak w programie Chat bot|
|--|--|--|--|
Nowy wiersz między 2 zdaniami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![Formatuj nowy wiersz między dwoma zdaniami](../media/qnamaker-concepts-datasources/format-newline.png)|
|Nagłówki od h1 do H6 —, liczba `#` wskazuje, który nagłówek. 1 `#` to H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Formatuj z nagłówkami promocji](../media/qnamaker-concepts-datasources/format-headers.png)<br>![format z nagłówkami promocji od h1 do h](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursywa |`*text*`|`How do I create a bot with *QnA Maker*?`|![Formatuj z kursywą](../media/qnamaker-concepts-datasources/format-italics.png)|
|Ciąg (pogrubiony)|`**text**`|`How do I create a bot with **QnA Maker**?`|![Formatuj z silnym oznaczeniem dla pogrubienia](../media/qnamaker-concepts-datasources/format-strong.png)|
|Adres URL linku|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![Format adresu URL (hiperłącze)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* Adres URL obrazu publicznego|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![Format adresu URL obrazu publicznego ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Przekreślenie|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![Format przekreślenia](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Pogrubienie i kursywa|`***text***`|`How can I create a ***QnA Maker*** bot?`|![Format dla pogrubienia i kursywy](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Pogrubienie adresu URL dla linku|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![Format pogrubionego adresu URL](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|Adres URL w postaci kursywy dla linku|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![Format adresu URL kursywy](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Symbole promocji z przeznaczeniem|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Format adresu URL kursywy](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista uporządkowana|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>W poprzednim przykładzie funkcja automatycznego numerowania została wbudowana w funkcję promocji.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>W poprzednim przykładzie jest stosowane jawne numerowanie.|![Format listy uporządkowanej](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Listy nieuporządkowane|`\n * item1 \n * item2`<br>lub<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![Format listy nieuporządkowanej](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listy zagnieżdżone|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Uporządkowane i nieuporządkowane listy można zagnieżdżać razem. Karta `\t`, wskazuje poziom wcięcia elementu podrzędnego.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![Format zagnieżdżonej listy nieuporządkowanej](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![Format zagnieżdżonej listy uporządkowanej](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker nie przetwarza obrazu w jakikolwiek sposób. Jest to rola aplikacji klienckiej do renderowania obrazu.

Jeśli chcesz dodać zawartość przy użyciu funkcji aktualizacji/zastępowania interfejsów API bazy danych, a zawartość/plik zawiera Tagi HTML, możesz zachować kod HTML w pliku, upewniając się, że otwierając i zamykając Tagi są konwertowane w zakodowanym formacie.

| Zachowaj kod HTML  | Reprezentacja w żądaniu interfejsu API  | Reprezentacja w KB |
|-----------|---------|-------------------------|
| Tak | \&lt; br\&gt; | &lt;br&gt; |
| Tak | \&lt; H3\&gt; nagłówek\&lt;/H3\&gt; | &lt;H3&gt;nagłówka&lt;/H3&gt; |

Ponadto znak CR LF (\r\n) jest konwertowany na \n w KB. Znaki LF (\n) są utrzymywane w postaci, w jakiej jest. Jeśli chcesz wypróbować dowolną sekwencję ucieczki, taką jak \t lub \n, możesz użyć ukośnika odwrotnego, na przykład: "\\\\r\\\\n" i "\\\\t"

## <a name="editing-your-knowledge-base-locally"></a>Lokalne edytowanie bazy wiedzy

Po utworzeniu bazy wiedzy zaleca się dokonanie edycji tekstu bazy wiedzy w [portalu QNA Maker](https://qnamaker.ai), a nie eksportowanie i ponowne importowanie za pomocą plików lokalnych. Mogą jednak wystąpić sytuacje, w których należy edytować bazę wiedzy lokalnie.

Wyeksportuj bazę wiedzy ze strony **Ustawienia** , a następnie przeprowadź edycję bazy wiedzy przy użyciu programu Microsoft Excel. Jeśli zdecydujesz się użyć innej aplikacji do edycji wyeksportowanego pliku TSV, aplikacja może wprowadzić błędy składniowe, ponieważ nie jest w pełni zgodna z TSV. Pliki TSV programu Microsoft Excel zwykle nie wprowadzają żadnych błędów formatowania.

Po dokonaniu edycji ponownie zaimportuj plik TSV ze strony **ustawień** . Spowoduje to całkowite zastąpienie bieżącej bazy wiedzy zaimportowaną bazą wiedzy.

## <a name="testing-your-markdown"></a>Testowanie promocji

Skorzystaj z samouczka **[CommonMark](https://commonmark.org/help/tutorial/index.html)** , aby sprawdzić poprawność. Samouczek zawiera funkcję **Wypróbuj ją** na potrzeby szybkiej weryfikacji kopiowania/wklejania.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Kontrola wersji danych w bazie wiedzy

Kontrola wersji danych jest dostępna za pomocą [funkcji importowania/eksportowania](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) na stronie **Ustawienia** .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)
