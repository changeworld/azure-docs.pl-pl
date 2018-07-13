---
title: Przykłady wglądu w szczegółowe dane usługi Bing | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Zawiera przykłady szczegółowe informacje o obrazach wyświetlane w witrynie Bing.com.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 927a02b400c7a7b250b315ad5668feb198fd590a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005693"
---
# <a name="examples-of-bing-insights-usage"></a>Przykłady użycia szczegółowych informacji w usłudze Bing

Ta sekcja zawiera przykłady jak Bing może wyświetlić szczegółowe informacje w witrynie Bing.com.

## <a name="pagesincluding-insight-example"></a>Przykład insight PagesIncluding

Poniżej przedstawiono, jak Bing może wyświetlić strony sieci Web, który zawiera obraz. Przykład zawiera łącze do pierwszej strony sieci Web i umożliwia użytkownikowi rozwijać i zwijać listę innych stron sieci Web, który zawiera obraz.

![Rozwinięty stron łącznie](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>Przykład insight ShoppingSources

Poniżej przedstawiono, jak Bing może wyświetlić koszyk źródeł dla produktów widoczne na obrazie.

![Zakupy źródeł](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>Przykład insight VisualSearch

Poniżej pokazano, jak Bing może być wyświetlany podobnych obrazach (zobacz **obrazy powiązane z** w przykładzie).

![Wizualnie podobnych obrazów](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Przepisy szczegółowe informacje o przykładzie

Poniżej przedstawiono, jak Bing może być wyświetlany przepisy DS na obrazie. Przykład informuje użytkownika o tym, że istnieją przepisy.

![Przepisy i stron, w tym](./media/recipes-pages-including.PNG)

 Zawiera link do przepisami w przypadku, gdy użytkownik rozwija listy.

![Strony rozwiniętego przepisu łącznie](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>Przykład insight RelatedSearches

Poniżej przedstawiono, jak Bing może wyświetlać powiązane wyszukiwania obrazów wykonywanymi przez innych użytkowników. Jeśli użytkownik kliknie obrazu, użytkownik jest przejście do Bing.com/images stronę wyników wyszukiwania dla tego zapytania powiązane.

![Powiązane wyszukiwania obrazów](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Przykład wglądu jednostkę

Poniżej przedstawiono, jak Bing może wyświetlać informacje na temat jednostek (osoby, miejsca lub rzeczy) na obrazie. Jeśli użytkownik kliknie łącze do obiektu, użytkownik jest przejście do Bing.com stronę wyników wyszukiwania dla jednostki.

![Jednostki w obrazie](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Wyświetlanie inne szczegółowe informacje, które użytkownik może zapoznaj się z

Poniżej przedstawiono, jak Bing może wyświetlić inne informacje dotyczące obrazu, który można eksplorować użytkownika.

![Zapoznaj się z innymi szczegółowe informacje o obrazie](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Ograniczenia pola i aktywne

Inne niż domyślne znaczniki zawierają obwiedni identyfikujący obszar zainteresowania obraz, który dotyczy tagu. Jeśli pole nie wskazuje całego obrazu, należy użyć obwiedni utworzyć punkt aktywny na obrazku. Użytkownik może kliknąć aktywny, aby uzyskać informacje dotyczące zawartości znajdują się w aktywnym (lub prostokąta). Na przykład, jeśli obraz jest obrazem sposób wysoki, wyniki mogą zawierać tagi (i obwiedni) dla Akcesoria na obrazie, takich jak portfelu biżuteria, scarfs itp. Poniższy przykład pokazuje prostokąt aktywny dla okularów przeciwsłonecznych w nakierowaniu na obrazie.

![Obwiedni i aktywny](./media/click-to-search.PNG)



## <a name="next-steps"></a>Kolejne kroki

Do zapoznania się z kodu JSON za te przykłady, zobacz [insights domyślne](default-insights-tag.md) i [odpowiedź JSON](overview.md#the-response).

Aby szybko rozpocząć pracę z pierwszego żądania, zobacz artykuł Szybki Start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Języka Python](quickstarts/python.md)





