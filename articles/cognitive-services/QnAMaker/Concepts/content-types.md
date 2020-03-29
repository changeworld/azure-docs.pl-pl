---
title: Typy zawartości — QnA Maker
description: Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOC i TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650201"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy treści dokumentów, które można dodać do bazy wiedzy
Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOC i TXT.

## <a name="file-and-url-data-types"></a>Typy danych plików i adresów URL

Poniższa tabela zawiera podsumowanie typów zawartości i formatów plików obsługiwanych przez program QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania<br> (Płaski, z sekcjami lub ze stroną główną tematów)<br>Strony pomocy technicznej <br> (Artykuły inzykingowe na jednej stronie, artykuły dotyczące rozwiązywania problemów itp.)|[Proste często zadawane pytania,](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Często zadawane pytania z linkami](https://www.microsoft.com/en-us/software-download/faq),<br> [Często zadawane pytania ze stroną główną tematów](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artykuł pomocy technicznej](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Najczęściej zadawane pytania<br> Instrukcja obsługi produktu,<br> Broszury<br> Papieru<br> Zasady dotyczące ulotek,<br> Przewodnik wsparcia,<br> Struktura QnA,<br> itp.|**Bez wielu zakrętów**<br>[Struktura QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Przykładowa instrukcja obsługi produktu.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Przykładowy semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykładowy oficjalny plik paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Wielo tura:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Korzyści contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Korzyści z contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Program Excel|Strukturalny plik QnA<br> (w tym RTF, obsługa HTML)|[Przykładowy qna faq.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Strukturalny plik QnA|[Przykładowy chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Jeśli potrzebujesz uwierzytelniania dla źródła danych, rozważ następujące metody, aby uzyskać tę zawartość do programu QnA Maker:

* Pobieranie pliku ręcznie i importowanie do programu QnA Maker
* Dodawanie pliku z [uwierzytelnionej lokalizacji programu Sharepoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Zawartość adresu URL

Dwa typy dokumentów można importować za pomocą **adresu URL** w aplikacji QnA Maker:

* Często zadawane pytania adresy URL
* Obsługa adresów URL

Każdy typ wskazuje oczekiwany format.

## <a name="file-based-content"></a>Zawartość oparta na plikach

Pliki można dodawać do bazy wiedzy ze źródła publicznego lub lokalnego systemu plików w [portalu QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Wskazówki dotyczące formatu zawartości

Dowiedz się więcej o [wskazówkach dotyczących formatu](../reference-document-format-guidelines.md) dla różnych plików.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jakie informacje są przechowywane w [zestawie pytań i odpowiedzi (QnA).](question-answer-set.md)