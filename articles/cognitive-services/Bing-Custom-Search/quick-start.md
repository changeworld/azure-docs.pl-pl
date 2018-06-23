---
title: Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe - kognitywnych usług firmy Microsoft
description: Aby użyć niestandardowej wyszukiwania usługi Bing, musisz utworzyć wystąpienia wyszukiwania niestandardowego, który definiuje widoku lub wycinek sieci web. Wystąpienie zawiera ustawienia, które określają domeny publicznej, Lokacje podrzędne i stron sieci Web, które wyszukiwania usługi Bing i zmiany klasyfikacji.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347873"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe
Aby użyć niestandardowej wyszukiwania usługi Bing, musisz utworzyć wystąpienia wyszukiwania niestandardowego, który definiuje widoku lub wycinek sieci web. Wystąpienie zawiera ustawienia, które określają domeny publicznej, witryn sieci Web i stron sieci Web, które wyszukiwania usługi Bing i zmiany klasyfikacji. Aby utworzyć wystąpienie, użyj wyszukiwania usługi Bing niestandardowe [portal](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Utwórz wystąpienie wyszukiwania niestandardowego

Aby utworzyć wystąpienie wyszukiwania usługi Bing niestandardowe:

1.  Pobierz klucz interfejsu API wyszukiwania niestandardowego. Zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Zaloguj się do portalu przy użyciu konta Microsoft (MSA). Kliknij przycisk **Zaloguj** przycisku. Jeśli jest używany po raz pierwszy przy użyciu portalu wykonaj dodatkowe czynności, w przeciwnym razie przejdź do kroku 3.
    - Jeśli nie masz zarządzanych kont usług, kliknij przycisk **utworzyć konto Microsoft**. Portalu poprosi o podanie uprawnień dostępu do danych. Kliknij przycisk **Yes** (Tak).
    - Zgodę na warunki usługi kognitywnych. Sprawdź **zgadzam się** i kliknij przycisk **Zgadzam się**.  
3.  Po zalogowaniu kliknij **nowe wystąpienie** i nazwę wystąpienia. Użyj nazwy, która ma znaczenie i opisuje typ zawartości, która zwraca wyszukiwania. Nazwę można zmienić w dowolnym momencie. 
4.  Na **Active** karcie **środowiska wyszukiwania**, wprowadź adres URL jednego lub więcej lokacji, którą chcesz uwzględnić w wyszukiwaniu.
5.  Aby upewnić się, że wystąpienie zwraca wyniki, wprowadź kwerendę w okienku podglądu po prawej stronie. Jeśli nie są wyniki, określić nową lokację. Bing zwraca wyniki tylko dla publicznych witryn, które ma on indeksowane.
6.  Kliknij przycisk **publikowania** do publikowania zmian konfiguracji w środowisku produkcyjnym. Po wyświetleniu monitu kliknij przycisk **publikowania** o potwierdzenie.
7.  Kliknij przycisk **produkcji** > **punkty końcowe** i skopiuj **identyfikator konfiguracji niestandardowej**. Należy ten identyfikator wywołania interfejsu API wyszukiwania niestandardowego.

## <a name="next-steps"></a>Kolejne kroki

Kontynuować pracę z wystąpieniem wyszukiwania niestandardowego, który właśnie został utworzony przez następujące instrukcje w tych przewodnikach instrukcje:

- [Konfigurowanie środowiska wyszukiwania niestandardowego](./define-your-custom-view.md)
- [Wywołanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Udostępnianie wyników wyszukiwania niestandardowego](./share-your-custom-search.md)
- [Konfigurowanie środowiska hostowanej interfejsu użytkownika](./hosted-ui.md)
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)
