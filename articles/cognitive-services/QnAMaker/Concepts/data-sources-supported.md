---
title: Obsługiwane — źródeł danych usług Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Obsługiwane źródła danych
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 698f96b15a9387cd30d26e684ed03ff4cc3346a7
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697904"
---
# <a name="data-sources"></a>Źródła danych 
Usługa QnA Maker automatycznie wyodrębniać pary pytań i odpowiedzi z typowych lub częściową strukturą zawartości formatów, takich jak często zadawane pytania i podręczniki. Zawartości mogą być również dodawane do bazy wiedzy z plików ze strukturą.

## <a name="plain-faq-pages"></a>Zwykły stron — często zadawane pytania
Jest to najczęściej spotykanym typem z często Zadawanymi pytaniami, w którym odpowiedzi natychmiast wykonaj na pytania w tej samej stronie. 

![Zwykła strona często zadawane pytania](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Często zadawane pytania dotyczące strony wraz z łączami sekcji 
W tym typie — często zadawane pytania pytania są zagregowane ze sobą i są połączone z odpowiedzi, które znajdują się w różnych sekcji na tej samej stronie.

 ![Sekcja Łącze często zadawane pytania dotyczące strony](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Często zadawane pytania dotyczące strony wraz z łączami do różnych stron. 
Ten typ strony — często zadawane pytania jest podobna do strony połączone w sekcji często zadawane pytania dotyczące, z tą różnicą, że łącza przekierowania do innej strony. Usługa QnA Maker przeszukuje połączonych stron w celu wyodrębnienia odpowiednich odpowiedzi.

 ![Strona link bezpośredni — często zadawane pytania](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Podręczniki

Podręcznik jest zazwyczaj materiały wskazówki towarzyszące produktu. Umożliwia użytkownikowi konfigurowanie, użycia, obsługa i rozwiązywanie problemów z produktu. Gdy narzędzie QnA Maker przetwarza ręcznego, wyodrębnia nagłówków i podpozycji jako pytania i kolejne zawartość jako odpowiedzi. Zobacz przykład [tutaj](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Wyodrębnianie sprawdza się najlepiej w instrukcji, które mają spisu treści i/lub strony indeksu oraz z nagłówkami hierarchiczną strukturę.


## <a name="structured-data-format-through-file-upload"></a>Format danych ze strukturą za pośrednictwem przekazywanie pliku

Pliki strukturalnych, takie jak TSV, xlsx, przy użyciu kolumn sformatowanych można przekazać również usługi QnA Maker podczas tworzenia bazy wiedzy knowledge base. Możesz również przekazać pliki z **ustawienia** na karcie wiedzy

| Pytanie  | Odpowiedź  | Metadane                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | "Key1:Value1|Key2:Value2 " |
| Question2 | Answer2 |      `Key:Value`           |
Wszelkie dodatkowe kolumny w pliku źródłowym są ignorowane.

## <a name="structured-data-format-through-import"></a>Format danych ze strukturą za pośrednictwem importu
Importowanie bazy wiedzy zastępuje zawartości istniejącej bazie wiedzy knowledge base. Importuj wymaga pliku tsv ze strukturą, który zawiera informacje o źródle danych. Informacje te pomagają usługi QnA Maker grupę pary pytań i odpowiedzi, a ich atrybutu z określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|"Key1:Value1|Key2:Value2 " |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

## <a name="editorial"></a>Redakcyjna
Jeśli nie masz już istniejącą zawartość do wypełniania bazy wiedzy knowledge base, należy można również dodać je przez jej tradycyjne zredagowanie w QnA Maker wiedzy. Dowiedz się, jak można zaktualizować bazy wiedzy [tutaj](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie usługi QnA Maker](../Overview/overview.md)
