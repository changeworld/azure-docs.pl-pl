---
title: Źródła danych obsługiwane — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker wyodrębnia automatycznie pary pytań i odpowiedzi z częściową strukturą zawartości, takiej jak — często zadawane pytania, podręczników, wskazówek, Obsługa dokumentów i zasady przechowywane jako strony sieci web, pliki PDF lub pliki programu MS Word doc. Zawartości mogą być również dodawane do bazy wiedzy ze strukturą pytań i odpowiedzi, pliki zawartości.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: tulasim
ms.openlocfilehash: 8fcc3ea8340a8645a1983eebb4a619904f884a19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61379672"
---
# <a name="data-sources-for-qna-maker-content"></a>Źródła danych dla zawartości usługi QnA Maker

Usługa QnA Maker wyodrębnia automatycznie pary pytań i odpowiedzi z częściową strukturą zawartości, takiej jak — często zadawane pytania, podręczników, wskazówek, Obsługa dokumentów i zasady przechowywane jako strony sieci web, pliki PDF lub pliki programu MS Word doc. Zawartości mogą być również dodawane do bazy wiedzy ze strukturą pytań i odpowiedzi, pliki zawartości. 

Poniższa tabela zawiera podsumowanie typów formaty zawartości i plików, które są obsługiwane przez narzędzie QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania<br> (Stosowana jest stała, sekcji lub z określonej strony głównej tematy)<br>Strony pomocy technicznej <br> (Jednostronicowej artykuły, rozwiązywanie problemów z artykułów itp.)|[Zwykły — często zadawane pytania](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Często zadawane pytania wraz z łączami](https://www.microsoft.com/software-download/faq),<br> [Często zadawane pytania dotyczące ze strony głównej — tematy](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artykuł pomocy technicznej](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PLIK PDF / DOC|Często zadawane pytania,<br> Obsługi produktu<br> Broszury,<br> Dokument,<br> Ulotka zasad<br> Przewodnik obsługi<br> QnA ze strukturą<br> etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykładowy Manual.pdf produktu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Przykładowy structured.doc o częściowej lub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykładowy paper.pdf biały](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Ze strukturą pliku pytań i odpowiedzi<br> (w tym formacie RTF, HTML pomocy technicznej)|[Przykładowy FAQ.xls pytań i odpowiedzi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Ze strukturą pliku pytań i odpowiedzi|[Przykładowe chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Lokalizacje źródeł danych

Większość lokalizacje źródeł danych muszą zapewnić publiczne adresy URL lub pliki, które nie wymagają uwierzytelniania. 

[Lokalizacje źródeł danych SharePoint](../How-to/add-sharepoint-datasources.md) są dozwolone w celu zapewnienia uwierzytelnionego plików. Zasoby programu SharePoint muszą być plikami, nie stron sieci web. 

Jeśli masz adres URL lub plik uwierzytelnionego alternatywnych opcji jest aby pobrać plik z witryny uwierzytelniony na komputerze lokalnym, Dodaj plik z lokalnym komputera do bazy wiedzy knowledge base. 

## <a name="faq-urls"></a>Adresy URL — często zadawane pytania

Usługa QnA Maker może obsługiwać często zadawane pytania dotyczące stron sieci web w 3 różne formy: Zwykły stron — często zadawane pytania, stron — często zadawane pytania wraz z łączami, stron — często zadawane pytania przy użyciu określonej strony głównej tematów.

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

Usługa QnA Maker może przetworzyć strony sieci web pomocy technicznej lub częściową strukturą, takich jak artykułów w sieci web, które będzie opisują sposób wykonywania danego zadania, jak zdiagnozować i rozwiązać dany problem i jakie są najlepsze rozwiązania dla danego procesu. Wyodrębnianie działa najlepiej na zawartości, który ma strukturę hierarchiczną nagłówki.

> [!NOTE]
> Wyodrębnianie dla artykułów pomocy technicznej jest nową funkcją i we wczesnych etapach. Jego jest najlepsza dla prostych stron, które również mają strukturę i nie zawierają nagłówki złożonych/stopki.

![Usługa QnA Maker obsługuje wyodrębniania z częściową strukturą stron sieci web, gdzie zostanie wyświetlony strukturę hierarchiczną nagłówki](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Plik PDF / pliki dokumentacji

Narzędzie QnA Maker można przetwarzają zawartość częściową strukturą w pliku PDF lub dokumentu i przekonwertować go na znacznie. Dobre pliku, który można wyodrębnić dobrze jest jednym gdzie zawartość jest zorganizowana w pewnej postaci ze strukturą i jest reprezentowana w sekcjach dobrze zdefiniowane. Sekcje mogą dodatkowo podzielony na podsekcje lub podtematy. Wyodrębnianie sprawdza się najlepiej w dokumentach, które mają strukturę hierarchiczną nagłówki.

Usługa QnA Maker identyfikuje sekcje i podsekcje i relacje w pliku, w oparciu o wskazówki visual, takich jak rozmiar czcionki, styl czcionki numerowanie, kolory itp. Pliki z częściową strukturą pliku PDF lub dokument może być podręczniki, — często zadawane pytania, wskazówek, zasad, broszury, ulotki i wiele innych typów plików. Poniżej przedstawiono niektóre przykładowe typy tych plików.

### <a name="product-manuals"></a>Podręczniki

Podręcznik jest zazwyczaj materiały wskazówki towarzyszące produktu. Umożliwia użytkownikowi konfigurowanie, użycia, obsługa i rozwiązywanie problemów z produktu. Gdy narzędzie QnA Maker przetwarza ręcznego, wyodrębnia nagłówków i podpozycji jako pytania i kolejne zawartość jako odpowiedzi. Zobacz przykład [tutaj](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Poniżej znajduje się przykład ręcznie za pomocą strony indeksu i zawartości hierarchicznej

 ![Ręczne przykład wiedzy produktu](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Wyodrębnianie sprawdza się najlepiej w instrukcji, które mają spisu treści i/lub strony indeksu oraz z nagłówkami hierarchiczną strukturę.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broszury, wskazówek, dokumenty i inne pliki

Wiele typów dokumentów, mogą być przetwarzane do generowania pary pytań i odpowiedzi, pod warunkiem mają Wyczyść strukturę i układ. Należą do nich: Broszury wytycznych, raporty, oficjalne dokumenty, naukowych opracowań, zasady, książki, itp. Zobacz przykład [tutaj](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

| Pytanie  | Odpowiedź  | Metadane (klucz 1: wartość 1) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Wszelkie dodatkowe kolumny w pliku źródłowym są ignorowane.

Poniżej znajduje się przykład ze strukturą QnA *xls* plik z zawartością HTML:

 ![Przykład wiedzy pytań i odpowiedzi ze strukturą w programie excel](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Format danych ze strukturą za pośrednictwem importu

Importowanie bazy wiedzy zastępuje zawartości istniejącej bazie wiedzy knowledge base. Importuj wymaga pliku tsv ze strukturą, który zawiera informacje o źródle danych. Informacje te pomagają usługi QnA Maker grupę pary pytań i odpowiedzi, a ich atrybutu z określonego źródła danych.

| Pytanie  | Odpowiedź  | source| Metadane (klucz 1: wartość 1) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Przez jej tradycyjne zredagowanie dodać do bazy wiedzy

Jeśli nie masz już istniejącą zawartość do wypełniania bazy wiedzy knowledge base, możesz dodać znacznie przez jej tradycyjne zredagowanie w QnA Maker wiedzy. Dowiedz się, jak można zaktualizować bazy wiedzy [tutaj](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Zagadnienia dotyczące formatowania

Po zaimportowaniu pliku lub adres URL jest konwertowane do języka Markdown i przechowywane w tym formacie. Jeśli proces konwersji nie jest poprawnie konwersji łącza w Twoich plików i adresy URL, na należy edytować pytań i odpowiedzi **Edytuj** strony. 

|Format|Przeznaczenie|
|--|--|
|`\n\n`| Nowy wiersz|
|`\n*`|Punkcie do uporządkowanej listy|

## <a name="editing-your-knowledge-base-locally"></a>Edytowanie wiedzy lokalnie

Po utworzeniu bazy wiedzy, zaleca się wprowadzić zmiany w bazie wiedzy knowledge base tekście [portalu narzędzia QnA Maker](https://qnamaker.ai), zamiast eksportowania i ponownie zaimportować za pomocą plików lokalnych. Może jednak wystąpić sytuacje, które trzeba edytować wiedzy lokalnie. 

Eksportowanie bazy wiedzy knowledge base z **ustawienia** stronie, a następnie edytować wiedzy z programem Microsoft Excel. Jeśli zdecydujesz się użyć innej aplikacji do edycji wyeksportowany plik TSV aplikacji może powodować błędy składniowe, ponieważ nie jest w pełni zgodne TSV. Ogólnie pliki TSV programu Microsoft Excel nie spowodują błędy formatowania. 

Po zakończeniu edycji, należy ponownie zaimportować plik TSV z **ustawienia** strony. Spowoduje to zamianę całkowicie bieżącej bazy wiedzy przy importowany bazy wiedzy knowledge base. 

## <a name="testing-your-markdown"></a>Testowanie kod Markdown

Użyj **[CommonMark](https://commonmark.org/help/tutorial/index.html)** samouczka, aby sprawdzić kod Markdown. Samouczek ma **wypróbuj** funkcja szybkiego kopiowania/wklejania weryfikacji. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie usługi QnA Maker](../Overview/overview.md)
