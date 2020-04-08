---
title: Typy adresów URL obsługiwane do importowania — QnA Maker
description: Dowiedz się, jak typy adresów URL są używane do importowania i tworzenia par QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804320"
---
# <a name="urls-supported-for-importing-documents"></a>Adresy URL obsługiwane do importowania dokumentów

Dowiedz się, jak typy adresów URL są używane do importowania i tworzenia par QnA.

## <a name="faq-urls"></a>Często zadawane pytania adresy URL

QnA Maker może obsługiwać strony internetowe FAQ w 3 różnych formach:

* Zwykłe strony często zadawanych pytań
* Strony często zadawanych pytań z linkami
* Strony często zadawanych pytań ze stroną główną tematów

### <a name="plain-faq-pages"></a>Zwykłe strony często zadawanych pytań

Jest to najczęstszy typ strony FAQ, na której odpowiedzi natychmiast podążają za pytaniami na tej samej stronie.

Poniżej znajduje się przykład zwykłej strony z często zadawanymi pytaniami:

![Zwykły przykład strony często zadawanych pytań dla bazy wiedzy](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Strony często zadawanych pytań z linkami

Na tej stronie często zadawanych pytań pytania są agregowane razem i są połączone z odpowiedziami, które znajdują się w różnych sekcjach tej samej strony lub na różnych stronach.

Poniżej znajduje się przykład strony z często zadawanymi pytaniami z łączami w sekcjach, które znajdują się na tej samej stronie:

 ![Przykład często zadawanych pytań dotyczących łącza sekcji dla bazy wiedzy](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Strony Tematy nadrzędne zawiera łącza do stron odpowiedzi podrzędnych

Ten typ często zadawanych pytań zawiera stronę Tematy, na której każdy temat jest połączony z odpowiednim zestawem pytań i odpowiedzi na innej stronie. QnA Maker indeksuje wszystkie połączone strony, aby wyodrębnić odpowiednie pytania & odpowiedzi.

Poniżej znajduje się przykład strony Tematy z łączami do sekcji często zadawanych pytań na różnych stronach.

 ![Przykład strony faq z głębokim linkiem dla bazy wiedzy](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Obsługa adresów URL

Program QnA Maker może przetwarzać częściowo ustrukturyzowane strony sieci web, takie jak artykuły sieci Web opisujące sposób wykonywania danego zadania, diagnozowanie i rozwiązywanie danego problemu oraz najlepsze rozwiązania dla danego procesu. Wyodrębnianie działa najlepiej na zawartości, która ma przejrzystą strukturę z nagłówkami hierarchicznymi.

> [!NOTE]
> Ekstrakcja artykułów pomocy technicznej jest nową funkcją i jest we wczesnych stadiach. Najlepiej sprawdza się w przypadku prostych stron, które są dobrze zorganizowane i nie zawierają złożonych nagłówków/stopek.

![QnA Maker obsługuje ekstrakcję ze stron półstrukturacyjnych, gdzie przedstawiono jasną strukturę z nagłówkami hierarchicznymi](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)