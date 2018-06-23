---
title: Źródła danych jest obsługiwana — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348685"
---
# <a name="data-sources"></a>Źródła danych 
Maker — strona główna automatycznie można wyodrębnić pary pytań i odpowiedzi z typowych częściowo ustrukturyzowanych formatów zawartości, takich jak często zadawane pytania i podręczniki. Zawartość można również dodać do bazy wiedzy z strukturze plików.

## <a name="plain-faq-pages"></a>Zwykły stron — często zadawane pytania
Jest to najczęściej spotykanym typem strony — często zadawane pytania, w którym odpowiedzi bezpośrednio po na pytania w tej samej stronie. 

![Zwykły strony — często zadawane pytania](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Często zadawane pytania dotyczące stron z sekcji łącza 
W tym typie z często Zadawanymi pytaniami pytania są agregowane ze sobą i są połączone z odpowiedzi, które znajdują się w różne sekcje na tej samej stronie.

 ![Strona sekcji łącze — często zadawane pytania](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Często zadawane pytania dotyczące stron z łączami do różnych stron. 
Ten typ strony — często zadawane pytania jest podobna do strony — często zadawane pytania związane z sekcji, z wyjątkiem tego łącza przekierowania do innej strony. Maker — strona główna przeszukuje wszystkie połączone strony, aby wyodrębnić odpowiednich odpowiedzi.

 ![Strona link bezpośredni — często zadawane pytania](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Instrukcje obsługi

Ręczny jest zwykle materiały wskazówki towarzyszące produktu. Pomaga użytkownikowi na konfigurowanie, użyj Obsługa i rozwiązywanie problemów z produktu. Kiedy Maker — strona główna przetwarza ręcznego, wyodrębnia nagłówki i nagłówki podrzędne jako pytania i kolejne zawartości jako odpowiedzi. Zobacz przykład [tutaj](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Wyodrębnianie działa najlepiej w instrukcji, które tabeli zawartości i/lub strona indeksu i strukturę hierarchiczną nagłówki.


## <a name="structured-data-format-through-file-upload"></a>Format danych strukturalnych za pomocą przekazywania pliku

Strukturalne plików, na przykład .tsv, xlsx z kolumnami sformatowany mogą być przekazywane do Maker — strona główna podczas tworzenia bazy wiedzy knowledge base. Możesz również przekazać pliki z **ustawienia** kartę bazy wiedzy

| Pytanie  | Odpowiedź  | Metadane                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | `Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 |      `Key:Value`           |
Wszelkie dodatkowe kolumny w pliku źródłowym są ignorowane.

## <a name="structured-data-format-through-import"></a>Format danych strukturalnych za pomocą importu
Importowanie bazy wiedzy zastępuje zawartość istniejącej bazy wiedzy knowledge base. Import wymaga pliku .tsv strukturalnego, który zawiera informacje o źródle danych. Informacje te pomagają Maker — strona główna grupy pary odpowiedź na pytanie i ich atrybutu z określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|`Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 | Redakcyjna|    `Key:Value`       |

## <a name="editorial"></a>Redakcyjna
Jeśli nie masz już istniejącą zawartość do wypełniania bazy wiedzy knowledge base, można również dodać je redakcyjna podstawowej wiedzy Maker — strona główna. Dowiedz się, jak zaktualizować z bazy wiedzy [tutaj](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Maker — strona główna](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie Maker — strona główna](../Overview/overview.md)