---
title: Przykłady usługi Bing insights | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Zawiera przykłady insights obraz wyświetlany na Bing.com.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 102bd0e916491738d74956c209829008d779bcbf
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348704"
---
# <a name="examples-of-bing-insights-usage"></a>Przykłady użycia szczegółowych informacji Bing

Ta sekcja zawiera przykłady sposobu Bing może wyświetlania szczegółowych informacji na Bing.com.

## <a name="pagesincluding-insight-example"></a>Przykład szczegółowe informacje o PagesIncluding

Poniżej przedstawiono, jak Bing może wyświetlania stron sieci Web, który zawiera obraz. W przykładzie zawiera łącze do pierwszej strony sieci Web i umożliwia użytkownikowi rozwijanie i zwijanie listy innych stron sieci Web, który zawiera obraz.

![Rozwinięte stron, tym](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>Przykład szczegółowe informacje o ShoppingSources

Poniżej przedstawiono, jak Bing może wyświetlić źródeł zakupów dla produktów widoczne na obrazie.

![Zakupy źródeł](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>Przykład szczegółowe informacje o VisualSearch

Poniżej pokazano, jak Bing może wyświetlić wizualnie podobne obrazy (zobacz **powiązanych obrazy** w przykładzie).

![Wizualne podobne obrazy](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Przykład wglądu przepisami

Poniżej przedstawiono, jak Bing może wyświetlać przepisami ds wyświetlany w obrazie. Przykład zezwala użytkownikowi na tym, że dostępne są przepisami.

![Przepisami i stron, tym](./media/recipes-pages-including.PNG)

 Zawiera link do przepisami, gdy użytkownik rozwija listę.

![W tym strony przepisu rozszerzonej](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>Przykład szczegółowe informacje o RelatedSearches

Poniżej przedstawiono, jak Bing może wyświetlić wyszukiwań powiązanych obrazy utworzone przez innych użytkowników. Jeśli użytkownik kliknie obrazu, użytkownik jest przejście do strony wyników wyszukiwania Bing.com/images tej kwerendy pokrewne.

![Umożliwia wyszukiwanie powiązanych obrazy](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Przykład wglądu jednostki

Poniżej przedstawiono, jak Bing może wyświetlać informacje na temat jednostek (osoby, miejsca lub operacją) wyświetlany w obrazie. Jeśli użytkownik kliknie łącze do obiektu, użytkownik jest przejście do strony wyników wyszukiwania Bing.com dla jednostki.

![Jednostki wyświetlany w obrazie](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Wyświetlanie inne szczegółowe informacje, które użytkownik może Eksploruj

Poniżej przedstawiono, jak Bing może wyświetlać inne informacje dotyczące obrazu, który można eksplorować użytkownika.

![Eksploruj inne szczegółowe informacje dotyczące obrazu](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Ograniczenia pola i punkty aktywne

Inne niż domyślne znaczniki to obwiedni identyfikujący obszaru zainteresowania obrazu, który dotyczy tagu. Jeśli pole nie będzie rozpoznawał całego obrazu, umożliwiają utworzenie punkt aktywny na obrazie obwiedni. Użytkownik może kliknąć punkt aktywny, aby uzyskać informacje dotyczące zawartości w obszarze aktywnego (lub prostokąt). Na przykład jeśli obraz jest obrazem sposób wysoki, wyniki może zawierać tagi (i obwiedni) dla Akcesoria pokazano na ilustracji, takie jak użyciu, biżuteria, scarfs itp. W poniższym przykładzie przedstawiono aktywnego prostokąt dla okulary słoneczne, wyświetlany w obrazie.

![Obwiedni i aktywnego](./media/click-to-search.PNG)



## <a name="next-steps"></a>Kolejne kroki

Można wyewidencjonować JSON za te przykłady, zobacz [domyślne insights](default-insights-tag.md) i [odpowiedź w formacie JSON](overview.md#the-response).

Aby szybko rozpocząć pracę z pierwszego żądania, zobacz poradniki Szybki Start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Języka Python](quickstarts/python.md)





