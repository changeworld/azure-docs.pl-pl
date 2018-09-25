---
title: Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing — Microsoft Cognitive Services
description: Aby korzystać z usługi Bing Custom Search, musisz utworzyć wystąpienie wyszukiwania niestandardowego, definiujący widoku lub wycinek sieci web. Wystąpienie zawiera ustawienia, które określają domeny publicznej, Lokacje podrzędne i stron sieci Web, które Bing, wyszukiwanie i wszelkie dostosowania klasyfikacji.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 25d622772fe47ffad001834d476e612f8c606904
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981662"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing
Aby korzystać z usługi Bing Custom Search, musisz utworzyć wystąpienie wyszukiwania niestandardowego, definiujący widoku lub wycinek sieci web. Wystąpienie zawiera ustawienia, które określają domeny publicznej, witryn sieci Web i stron sieci Web, które Bing, wyszukiwanie i wszelkie dostosowania klasyfikacji. Aby utworzyć wystąpienie, użyj wyszukiwania niestandardowego Bing [portal](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Tworzenie wystąpienia wyszukiwania niestandardowego

Aby utworzyć wystąpienie wyszukiwania niestandardowego Bing:

1.  Uzyskiwanie klucza interfejsu API wyszukiwania niestandardowego. Zobacz [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Kliknij przycisk **Zaloguj** znajdujący się i zaloguj się do portalu przy użyciu konta Microsoft (MSA). 
    - Jeśli nie masz konta Microsoft, kliknij przycisk **Utwórz konto Microsoft**. Portal poprosi o podanie uprawnień, aby uzyskiwać dostęp do danych. Kliknij przycisk **Yes** (Tak).
    - Zgodę na warunki usługi Cognitive Services. Sprawdź **zgodę** i kliknij przycisk **Zgadzam się**.  
3.  Po zarejestrowaniu się kliknij **nowe wystąpienie** i nazwę wystąpienia. Użyj nazwy, która ma znaczenie i opisuje typ zawartości, którego funkcja wyszukiwania. Nazwę można zmienić w dowolnym momencie. 
4.  Na **Active** karcie **środowisko wyszukiwania**, wprowadź adres URL jednego lub więcej witryn sieci Web, które chcesz uwzględnić w wyszukiwaniu.
5.  Aby upewnić się, że wystąpienie zwraca wyniki, należy wprowadzić kwerendę w okienku podglądu, po prawej stronie. Jeśli nie ma żadnych wyników, należy określić nową witrynę sieci Web. Wyszukiwarka Bing zwróci wyniki tylko dla witryn publicznych, które ma on indeksowane.
6.  Kliknij przycisk **Publikuj** do publikowania zmian w konfiguracji do środowiska produkcyjnego. Po wyświetleniu monitu kliknij **Publikuj** o potwierdzenie.
7.  Kliknij przycisk **produkcji** > **punktów końcowych** i skopiuj **niestandardowy identyfikator konfiguracji**. Ten identyfikator, aby wywołać niestandardowy interfejs API wyszukiwania będzie potrzebny.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do pracy z wystąpienia wyszukiwania niestandardowego, które właśnie utworzoną postępując zgodnie z instrukcjami opisanymi w tych przewodnikach instruktażowych:

- [Konfigurowanie środowiska wyszukiwania niestandardowego](./define-your-custom-view.md)
- [Wywołanie usługi wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Udostępnianie wyników wyszukiwania niestandardowego](./share-your-custom-search.md)
- [Konfigurowanie środowiska obsługiwanego interfejsu użytkownika](./hosted-ui.md)
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
