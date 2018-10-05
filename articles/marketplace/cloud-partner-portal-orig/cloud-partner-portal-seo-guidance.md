---
title: Przewodnik dotyczący publikowania w portalu Azure Marketplace optymalizacji dla aparatów wyszukiwania | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące maksymalizując optymalizacji dla aparatów wyszukiwania (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: b8ca5fc5348818a7d0f1075557d4fe5c7e3bef44
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810513"
---
<a name="azure-marketplace-seo-publisher-guide"></a>Przewodnik dotyczący publikowania w portalu Azure Marketplace optymalizacji dla aparatów wyszukiwania
=======================================

### <a name="general-explanation-of-algorithm"></a>Ogólne omówienie algorytmu

Portal marketplace korzysta z usługi Azure Search zapewniająca możliwości wyszukiwania w witrynie. Algorytm opiera się na określenie częstotliwości — odwrotność dokumentu częstotliwość ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Standardowa [analizator Lucene](http://lucene.apache.org/core/) jest używany.

Ogólnie rzecz biorąc cały tekst pola kategorii i branżach każdego typu i należy do weightage zgodność. Terminy specjalistyczne, które są rzadko używane przez aplikacje, ale często w swojej aplikacji, spowoduje wygenerowanie wyższą ocenę dopasowanie za pomocą wyszukiwania. Dlatego w tym terminy, takie jak "Maszyny Wirtualnej" będzie oferują niewielkie korzyści, konieczne będzie znacznie bardziej wyspecjalizowane "Usługa Azure search".
Poniżej przedstawiono najbardziej odpowiednie pola, aby wziąć pod uwagę.

 
|  Pole                   | Ważność | Wskazówki                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nazwa oferty               |  Wysoka      | Dokładnie lub blisko pełne dopasowanie z usługą search zapytania umożliwia uzyskanie wysokiego szczebla.                       |
| Nazwa wydawcy           |  Wysoka      | Dokładnie lub blisko pełne dopasowanie z usługą search zapytania umożliwia uzyskanie wysokiego szczebla.                       |
| Krótki opis        |  Medium    | Podanej nazwy aplikacji i wydawcy nazwy prawie gwarantuje wysokiego szczebla, nie może być najbardziej odpowiednie. W tym przypadku krótki opis jest krytyczny. Zachowaj tekst zwięzły i w punkcie. Słowa kluczowe i oczekiwanego wyszukiwane terminy powinny zostać uwzględnione w najlepszych wyników.  Na przykład "to jest najlepsze punktu sprzedaży detalicznej zainstalowanych w miejscach do w pełni Dynamics 365" jest mniej skuteczna, niż "punktu sprzedaży detalicznej (punktu sprzedaży) for Dynamics 365".  | 
| Długi opis         |  Małe       | Opis elementu pozwala na bardziej szczegółowo. Najbardziej efektywne opisy są uzasadnione długości i słowa kluczowe są używane.  Opisy do punktu, przy użyciu słów kluczowych będą mogli korzystać więcej niż długi długi tekst. Upewnij się, że klucza warunków, takich jak "IoT", znajdują się w opisie.  |
| Kategorie produktów       | Medium     |  Kategorie produktów są określone przez kombinację opcji wydawcy i firmy Microsoft. Te kategorie wybierz odpowiednią opcję, aby użytkownicy mogli łatwo znajdować aplikacje prawidłową kategorię. |
|  |  |  |


### <a name="other-tips"></a>Inne porady

-   Wyszukiwanie sugeruje aktywności użytkownika mocno pobiera. Priorytet odpowiedników wydawcę nazwę aplikacji. Krótki opis staje się pole klucza dla gdy wyszukiwany termin nie jest on identyczny z nazwą wydawcy/aplikacji.
-   Dokumenty do pobrania nie są uwzględnione w weightage wyszukiwania.
-   Twoje nabycia rzeczywiste aplikacje i użycie będzie miało wpływ na wyszukiwanie ranking także. Na przykład dwie aplikacje równoważne, gdy jedna ma znacznie większą liczbę użytkowników, otrzyma wyższej klasyfikacji.
