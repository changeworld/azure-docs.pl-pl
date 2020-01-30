---
title: Typy zawartości — QnA Maker
description: Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOC i TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843439"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy zawartości dokumentów, które można dodać do bazy wiedzy
Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOC i TXT.

## <a name="file-and-url-data-types"></a>Typy danych plików i adresów URL

Poniższa tabela zawiera podsumowanie typów formaty zawartości i plików, które są obsługiwane przez narzędzie QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania<br> (Płaski, z sekcjami lub z stroną główną tematów)<br>Strony pomocy technicznej <br> (Artykuły z artykułami z artykułu z jedną stroną, rozwiązywanie problemów itp.)|[Zwykłe często zadawane pytania](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Często zadawane pytania dotyczące linków](https://www.microsoft.com/en-us/software-download/faq),<br> [Często zadawane pytania dotyczące strony głównej tematów](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artykuł pomocy technicznej](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PLIK PDF / DOC|Najczęściej<br> Podręcznik produktu,<br> Broszury,<br> Drukowane<br> Zasady ulotek,<br> Przewodnik pomocy technicznej,<br> QnA strukturalne,<br> itd.|[Strukturalny QNA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Przykładowy produkt. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Przykład semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykładowy oficjalny dokument. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Przykład Multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Ze strukturą pliku pytań i odpowiedzi<br> (w tym RTF, obsługa HTML)|[Przykładowy FAQ.xls pytań i odpowiedzi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Ze strukturą pliku pytań i odpowiedzi|[Przykładowe chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Jeśli potrzebujesz uwierzytelniania dla źródła danych, weź pod uwagę następujące metody, aby uzyskać tę zawartość do QnA Maker:

* Pobierz plik ręcznie i zaimportuj do QnA Maker
* Dodaj plik ze uwierzytelnionej [lokalizacji programu SharePoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Zawartość adresu URL

Dwa typy dokumentów można zaimportować za pośrednictwem **adresu URL** w QNA Maker:

* Adresy URL — często zadawane pytania
* Adresy URL pomocy technicznej

Każdy typ wskazuje oczekiwany format.

## <a name="file-based-content"></a>Zawartość oparta na plikach

Pliki można dodawać do bazy wiedzy ze źródła publicznego lub lokalnego systemu plików w [portalu QNA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Wskazówki dotyczące formatu zawartości

QnA Maker dodaje zawartość pliku i adresu URL, konwertując zawartość do zestawów QnA przechowywanych w postaci promocji (`.md`). Zawartość jest zgodna z bazą wiedzy, w której zawartość jest zorganizowana w formie strukturalnej i jest reprezentowana w dobrze zdefiniowanych sekcjach. Sekcje można dodatkowo podzielić na podsekcje lub podtematy. Wyodrębnianie działa najlepiej w przypadku zawartości, która ma jasno strukturę z nagłówkami hierarchicznymi.

QnA Maker identyfikuje sekcje i podsekcje oraz relacje w zawartości na podstawie wizualizacji wizualnych, takich jak rozmiar czcionki, styl czcionki, numeracja, kolory itp. Zawartość z częściową strukturą zawiera instrukcje, często zadawane pytania, wytyczne, zasady, broszury, ulotki i wiele innych typów plików.

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

### <a name="structured-qna-document"></a>Strukturalny dokument QnA

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

## <a name="formatting-considerations"></a>Zagadnienia dotyczące formatowania

Po zaimportowaniu pliku lub adresu URL QnA Maker konwertuje i zapisuje zawartość w [formacie promocji](https://en.wikipedia.org/wiki/Markdown). Proces konwersji dodaje do tekstu nowe wiersze, takie jak `\n\n`. Wiedza o formacie promocji pozwala zrozumieć przekonwertowaną zawartość i zarządzać zawartością bazy wiedzy.

Jeśli dodasz lub edytujesz zawartość bezpośrednio w bazie wiedzy, użyj **formatowania promocji** w celu utworzenia zawartości tekstu sformatowanego lub zmiany zawartości formatu promocji, która jest już w odpowiedzi. QnA Maker obsługuje duży format promocji, aby zapewnić możliwość wprowadzenia tekstu sformatowanego do zawartości. Jednak aplikacja kliencka, taka jak rozmowa bot, może nie obsługiwać tego samego zestawu formatów promocji. Ważne jest, aby przetestować wyświetlanie odpowiedzi aplikacji klienta.

Dowiedz się więcej z [dokumentacji dotyczącej promocji QNA Maker](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>Testowanie promocji

Skorzystaj z samouczka **[CommonMark](https://commonmark.org/help/tutorial/index.html)** , aby sprawdzić poprawność. Samouczek zawiera funkcję **Wypróbuj ją** na potrzeby szybkiej weryfikacji kopiowania/wklejania.

## <a name="next-steps"></a>Następne kroki

* Informacje na temat projektowania [zestawów pytań i odpowiedzi (QNA)](question-answer-set.md) oraz zarządzania nimi