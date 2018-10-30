---
title: Źródła danych obsługiwane — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker wyodrębnia automatycznie pary pytań i odpowiedzi z częściową strukturą zawartości, takiej jak — często zadawane pytania, podręczników, wskazówek, Obsługa dokumentów i zasady przechowywane jako strony sieci web, pliki PDF lub pliki programu MS Word doc. Zawartości mogą być również dodawane do bazy wiedzy ze strukturą pytań i odpowiedzi, pliki zawartości.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/25/2018
ms.author: tulasim
ms.openlocfilehash: 4c96eb5b49c72fe9bb5ab977c773407889c56805
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213308"
---
# <a name="data-sources-for-qna-maker-content"></a>Źródła danych dla zawartości usługi QnA Maker

Usługa QnA Maker wyodrębnia automatycznie pary pytań i odpowiedzi z częściową strukturą zawartości, takiej jak — często zadawane pytania, podręczników, wskazówek, Obsługa dokumentów i zasady przechowywane jako strony sieci web, pliki PDF lub pliki programu MS Word doc. Zawartości mogą być również dodawane do bazy wiedzy ze strukturą pytań i odpowiedzi, pliki zawartości. 

Poniższa tabela zawiera podsumowanie typów formaty zawartości i plików, które są obsługiwane przez narzędzie QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania (płaskie, sekcji lub z określonej strony głównej tematy)|[Zwykły — często zadawane pytania](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), [— często zadawane pytania wraz z łączami](https://www.microsoft.com/software-download/faq), [— często zadawane pytania za pomocą strony głównej — tematy](https://support.microsoft.com/products/windows?os=windows-10)|
|PLIK PDF / DOC|Przewodnik dotyczący — często zadawane pytania, obsługi produktu, broszury, dokument, ulotka zasad, pomoc techniczną, ze strukturą pytań i odpowiedzi, np.|[Strukturę QnA.doc](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Bot%20Service%20Sample%20FAQ.docx), [Manual.pdf produktu przykładowe](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf), [przykładowy o częściowej lub structured.doc](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx), [przykładowy paper.pdf biały](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-stack-wortmann-bring-the-power-of-the-public-cloud-into-your-data-center/Azure_Stack_Wortmann_Bring_the_Power_of_the_Public_Cloud_into_Your_Data_Center.pdf)|
|Excel|Ze strukturą pliku pytań i odpowiedzi (łącznie z RTF, HTML pomocy technicznej)|[Przykładowy FAQ.xls pytań i odpowiedzi](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Ze strukturą pliku pytań i odpowiedzi|[Przykładowe chit-chat.tsv](https://github.com/Microsoft/BotBuilder-PersonalityChat/blob/master/CSharp/Datasets/scenario_responses_the_friend.tsv)|

## <a name="faq-urls"></a>Adresy URL — często zadawane pytania

Narzędzie QnA Maker obsługuje często zadawane pytania dotyczące stron sieci web w 3 różnych formularzach: zwykły często zadawane pytania dotyczące strony, strony — często zadawane pytania wraz z łączami, stron — często zadawane pytania przy użyciu określonej strony głównej tematy.

### <a name="plain-faq-pages"></a>Zwykły stron — często zadawane pytania

Jest to najczęściej spotykanym typem z często Zadawanymi pytaniami, w którym odpowiedzi natychmiast wykonaj na pytania w tej samej stronie. 

Poniżej przedstawiono przykład zwykłego strony — często zadawane pytania:

![Zwykła strona często zadawane pytania](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Często zadawane pytania dotyczące strony wraz z łączami 

W tym typie — często zadawane pytania pytania są zagregowane ze sobą i są połączone z odpowiedzi, które znajdują się w różne sekcje w tej samej stronie lub na różnych stronach.

Poniżej przedstawiono przykład strony — często zadawane pytania wraz z łączami w sekcji, które znajdują się w tej samej stronie:

 ![Sekcja Łącze często zadawane pytania dotyczące strony](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Często zadawane pytania dotyczące stron przy użyciu określonej strony głównej — tematy

Ten typ — często zadawane pytania ma stronę główną z tematów, gdzie każdy temat jest link do jego odpowiednich znacznie w innej strony. W tym miejscu usługi QnA Maker przeszukuje połączonych stron w celu wyodrębnienia odpowiednie pytania i odpowiedzi.

Poniżej przedstawiono przykład często Zadawanymi pytaniami, gdzie głównej tematów zawiera linki do sekcji często zadawane pytania na różnych stronach. 

 ![Strona link bezpośredni — często zadawane pytania](../media/qnamaker-concepts-datasources/topics-faq.png) 


## <a name="pdf-doc-files"></a>Plik PDF / pliki dokumentacji

Narzędzie QnA Maker można przetwarzają zawartość częściową strukturą w pliku PDF lub dokumentu i przekonwertować go na znacznie. Dobre pliku, który można wyodrębnić dobrze jest jednym gdzie zawartość jest zorganizowana w pewnej postaci ze strukturą i jest reprezentowana w sekcjach dobrze zdefiniowane. Sekcje mogą dodatkowo podzielony na podsekcje lub podtematy. Wyodrębnianie sprawdza się najlepiej w dokumentach, które mają strukturę hierarchiczną nagłówki.

Usługa QnA Maker identyfikuje sekcje i podsekcje i relacje w pliku, w oparciu o wskazówki visual, takich jak rozmiar czcionki, styl czcionki numerowanie, kolory itp. Pliki z częściową strukturą pliku PDF lub dokument może być podręczniki, — często zadawane pytania, wskazówek, zasad, broszury, ulotki i wiele innych typów plików. Poniżej przedstawiono niektóre przykładowe typy tych plików.

### <a name="product-manuals"></a>Podręczniki

Podręcznik jest zazwyczaj materiały wskazówki towarzyszące produktu. Umożliwia użytkownikowi konfigurowanie, użycia, obsługa i rozwiązywanie problemów z produktu. Gdy narzędzie QnA Maker przetwarza ręcznego, wyodrębnia nagłówków i podpozycji jako pytania i kolejne zawartość jako odpowiedzi. Zobacz przykład [tutaj](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Poniżej znajduje się przykład ręcznie za pomocą strony indeksu i zawartości hierarchicznej

 ![Przykład ręczne produktu](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Wyodrębnianie sprawdza się najlepiej w instrukcji, które mają spisu treści i/lub strony indeksu oraz z nagłówkami hierarchiczną strukturę.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broszury, wskazówek, dokumenty i inne pliki

Wiele typów dokumentów, mogą być przetwarzane do generowania pary pytań i odpowiedzi, pod warunkiem mają Wyczyść strukturę i układ. Obejmują one: broszury, wytyczne, raporty, biały papier, naukowych opracowań, zasady, książki, itp. Zobacz przykład [tutaj](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

 ![Dokument o określonej strukturze pytań i odpowiedzi](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturalne *TXT*, *TSV* i *XLS* plików

Struktura znacznie w formie *.txt*, *tsv* lub *xls* pliki można przekazać do usługi QnA Maker można utworzyć lub rozszerzyć bazy wiedzy.  To może to być zwykły tekst lub może mieć zawartości w formacie RTF lub HTML. 

| Pytanie  | Odpowiedź  | Metadane                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Wszelkie dodatkowe kolumny w pliku źródłowym są ignorowane.

Poniżej znajduje się przykład ze strukturą QnA *xls* plik z zawartością HTML:

 ![W programie excel ze strukturą pytań i odpowiedzi](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Format danych ze strukturą za pośrednictwem importu

Importowanie bazy wiedzy zastępuje zawartości istniejącej bazie wiedzy knowledge base. Importuj wymaga pliku tsv ze strukturą, który zawiera informacje o źródle danych. Informacje te pomagają usługi QnA Maker grupę pary pytań i odpowiedzi, a ich atrybutu z określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Przez jej tradycyjne zredagowanie dodać do bazy wiedzy

Jeśli nie masz już istniejącą zawartość do wypełniania bazy wiedzy knowledge base, możesz dodać znacznie przez jej tradycyjne zredagowanie w QnA Maker wiedzy. Dowiedz się, jak można zaktualizować bazy wiedzy [tutaj](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie usługi QnA Maker](../Overview/overview.md)
