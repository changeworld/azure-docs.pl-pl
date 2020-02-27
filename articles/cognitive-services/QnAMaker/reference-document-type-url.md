---
title: Typy adresów URL obsługiwane przez QnA Maker importowania
description: Dowiedz się, w jaki sposób typy adresów URL są używane do importowania i tworzenia zestawów QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651829"
---
# <a name="urls-supported-for-importing-documents"></a>Adresy URL obsługiwane do importowania dokumentów

Dowiedz się, w jaki sposób typy adresów URL są używane do importowania i tworzenia zestawów QnA.

## <a name="faq-urls"></a>Adresy URL — często zadawane pytania

QnA Maker mogą obsługiwać strony sieci Web często zadawane pytania w 3 różnych formach:

* Zwykły stron — często zadawane pytania
* Często zadawane pytania dotyczące strony wraz z łączami
* Strony z często zadawanymi pytaniami z stroną główną tematów

### <a name="plain-faq-pages"></a>Zwykły stron — często zadawane pytania

Jest to najczęściej spotykanym typem z często Zadawanymi pytaniami, w którym odpowiedzi natychmiast wykonaj na pytania w tej samej stronie.

Poniżej przedstawiono przykład zwykłego strony — często zadawane pytania:

![Zwykły przykładowa strona — często zadawane pytania dla bazy wiedzy](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Często zadawane pytania dotyczące strony wraz z łączami

W tym typie — często zadawane pytania pytania są zagregowane ze sobą i są połączone z odpowiedzi, które znajdują się w różne sekcje w tej samej stronie lub na różnych stronach.

Poniżej przedstawiono przykład strony — często zadawane pytania wraz z łączami w sekcji, które znajdują się w tej samej stronie:

 ![Przykładowa strona łącze — często zadawane pytania sekcji dla bazy wiedzy](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Strony tematów nadrzędnych łącza do stron odpowiedzi podrzędnych

Ten typ często zadawanych pytań zawiera strony tematów, w których każdy temat jest połączony z odpowiednim zestawem pytań i odpowiedzi na innej stronie. QnA Maker przeszukuje wszystkie połączone strony w celu wyodrębnienia odpowiednich pytań & odpowiedzi.

Poniżej znajduje się przykład strony tematów z linkami do sekcji często zadawanych pytań na różnych stronach.

 ![Przykładowa strona link bezpośredni — często zadawane pytania dla bazy wiedzy](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Adresy URL pomocy technicznej

QnA Maker mogą przetwarzać strony sieci Web pomocy technicznej z częściową strukturą, takie jak artykuły w sieci Web, które opisują sposób wykonywania danego zadania, diagnozowania i rozwiązywania danego problemu oraz najważniejszych rozwiązań dla danego procesu. Wyodrębnianie działa najlepiej w przypadku zawartości, która ma jasno strukturę z nagłówkami hierarchicznymi.

> [!NOTE]
> Wyodrębnianie artykułów pomocy technicznej jest nową funkcją i znajduje się na wczesnych etapach. Najlepiej sprawdza się w przypadku prostych stron, które są dobrze strukturalne i nie zawierają złożonych nagłówków/stopek.

![QnA Maker obsługuje wyodrębnianie ze stron sieci Web z częściową strukturą, w przypadku których przejrzysta struktura jest prezentowana z nagłówkami hierarchicznymi](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)