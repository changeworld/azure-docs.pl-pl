---
title: Przykłady szczegółowych informacji usługi Bing — wyszukiwanie wizualne usługi Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera przykłady sposobu używania i wyświetlania szczegółowych informacji o obrazie w usłudze Bing Visual Search w Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111639"
---
# <a name="examples-of-bing-insights-usage"></a>Przykłady użycia usługi Bing insights

Ten artykuł zawiera przykłady sposobu używania i wyświetlania szczegółowych informacji o obrazie w usłudze Bing w Bing.com.

## <a name="pagesincluding-insight-example"></a>PagesWłączenie przykładu wglądu

Poniżej znajduje się łącze do pierwszej strony sieci Web i umożliwia użytkownikowi rozwinięcie i zwinięcie listy innych stron sieci Web, które zawierają obraz:

![Rozszerzone strony, w tym](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Przykład wglądu w shoppingsources

Poniżej przedstawiono, jak usługa Bing może wyświetlać źródła zakupów produktów widocznych na obrazie:

![Źródła zakupów](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Przykład wglądu w wygląd programu VisualSearch

Poniżej przedstawiono, jak bing może wyświetlać obrazy podobne wizualnie (zobacz **Powiązane obrazy** w przykładzie):

![Obrazy podobne wizualnie](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Przykład wglądu w przepisy

Poniżej przedstawiono, jak bing może wyświetlać przepisy na jedzenie pokazane na obrazie. W przykładzie użytkownik wie, że dostępne są przepisy:

![Przepisy i strony, w tym](./media/recipes-pages-including.PNG)

 I zawiera link do receptur, gdy użytkownik rozszerza listę:

![Rozszerzone strony z przepisami, w tym](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Przykład informacji RelatedSearches

Poniżej przedstawiono, jak bing może wyświetlać powiązane wyszukiwania obrazów wykonanych przez inne osoby. Jeśli użytkownik kliknie obraz, użytkownik zostanie przesuń do Bing.com/images strony wyników wyszukiwania dla tej powiązanej kwerendy.

![Powiązane wyszukiwania obrazów](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Przykład wglądu w encję

Poniżej przedstawiono, jak bing może wyświetlać informacje o encji (osoby, miejsca lub rzeczy) wyświetlane na obrazie. Jeśli użytkownik kliknie łącze encji, zostanie on przesunął się na stronę wyników wyszukiwania Bing.com encji:

![Jednostka pokazana na obrazie](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Wyświetlanie innych szczegółowych informacji, które użytkownik może eksplorować

Poniżej przedstawiono, jak Bing może wyświetlać inne informacje o obrazie, który użytkownik może eksplorować.

![Poznaj inne szczegółowe informacje o obrazie](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Obwiednie i hot spoty

Tagi inne niż domyślne obejmują obwiednię identyfikujące obszar zainteresowania obrazem, do których ma zastosowanie znacznik. Jeśli obwiednia nie identyfikuje całego obrazu, użyj obwiedni, aby utworzyć punkt gorąca na obrazie. Użytkownik może kliknąć punkt kontaktowy, aby uzyskać informacje związane z zawartością znajdującą się w punkcie kontaktowym (lub prostokącie). Na przykład, jeśli obraz jest obrazem o wysokiej jakości, wyniki mogą zawierać znaczniki (i obwiedni) dla akcesoriów wyświetlanych na obrazie, takich jak torebka, biżuteria, szaliki i tak dalej. W poniższym przykładzie pokazano prostokąt hot-spot dla okularów przeciwsłonecznych pokazanych na obrazie:

![Obwiednia i hot spot](./media/click-to-search.PNG)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z pierwszym żądaniem, zobacz przewodniki Szybki start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





