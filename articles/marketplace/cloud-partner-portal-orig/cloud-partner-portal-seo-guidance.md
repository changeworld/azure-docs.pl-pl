---
title: Wskazówki dotyczące seo w portalu Azure Marketplace
description: Zawiera wskazówki dotyczące maksymalizacji optymalizacji pod kątem wyszukiwarek (SEO).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280154"
---
# <a name="azure-marketplace-seo-guidance"></a>Wskazówki dotyczące seo w portalu Azure Marketplace

W tym artykule wyjaśniono, jak zmaksymalizować wykrywalność oferty za pomocą funkcji wyszukiwania w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) i [AppSource.](https://appsource.microsoft.com) 


## <a name="general-explanation-of-algorithm"></a>Ogólne wyjaśnienie algorytmu

Witryny firmy Microsoft korzystają z usługi Azure Cognitive Search w celu zasilania funkcji wyszukiwania witryny. Algorytm jest oparty na częstotliwości terminowej częstotliwość odwrotna częstotliwość dokumentu[(TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Używany jest [standardowy analizator Lucene.](https://lucene.apache.org/core/)

Ogólnie rzecz biorąc, wszystkie pola tekstowe, kategorie i branże i uwzględnione w wadze istotności. Wyspecjalizowane terminy, które są rzadko używane przez aplikacje, ale często w aplikacji wygeneruje wyższy wynik dopasowania z wyszukiwania. Tak więc uwzględnienie terminów takich jak "Maszyna wirtualna" przyniesie niewielkie korzyści, podczas gdy "wyszukiwanie platformy Azure" byłoby znacznie bardziej wyspecjalizowane.
Poniżej znajdują się najbardziej odpowiednie pola do rozważenia.

 
|  Pole                   | Ważność | Wskazówki                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Offer Name               |  Wysoka      | Dokładne lub zbliżone do pełnego dopasowania z zapytaniem wyszukiwania przyniesie wysoką pozycję w rankingu.                       |
| Publisher Name           |  Wysoka      | Dokładne lub zbliżone do pełnego dopasowania z zapytaniem wyszukiwania przyniesie wysoką pozycję w rankingu.                       |
| Krótki opis        |  Medium    | Biorąc pod uwagę nazewnictwo aplikacji i nazw wydawców prawie zagwarantuje wysoką pozycję w rankingu, może nie być najbardziej istotne. W tym przypadku krótki opis jest krytyczny. Zachowaj tekst zwięzły i do punktu. Aby uzyskać najlepszy wynik, należy uwzględnić słowa kluczowe i oczekiwane wyszukiwane hasła.  Na przykład "Jest to najlepszy punkt sprzedaży detalicznej zbudowany w pełni na dynamics 365" jest mniej skuteczny niż "Retail POS (punkt sprzedaży) dla Dynamics 365".  | 
| Długi opis         |  Małe       | Opis oferuje sposób, aby przejść do bardziej szczegółowe. Najskuteczniejsze opisy mają rozsądną długość i używane są słowa kluczowe.  Opisy do punktu za pomocą słów kluczowych będą korzystne więcej niż długi, długi tekst. Upewnij się, że kluczowe terminy, takie jak "IoT", są obecne w opisie.  |
| Kategorie produktów       | Medium     |  Kategorie produktów są określane przez kombinację wyborów wydawców i firmy Microsoft. Wybierz te kategorie odpowiednio, aby użytkownicy mogli łatwo znaleźć aplikacje w odpowiedniej kategorii. |
|  |  |  |


## <a name="other-tips"></a>Inne wskazówki

-   Wyszukiwanie sugeruje, że uzyskuje dużą aktywność użytkownika. Nadaje priorytet dopasowaniom względem nazwy/wydawcy aplikacji. Krótki opis staje się polem klucza, gdy wyszukiwany termin nie jest dokładnie zgodny z nazwą wydawcy/aplikacji.
-   Dokumenty do pobrania nie są uwzględniane w wadze wyszukiwania.
-   Rzeczywiste pozyskiwanie i użytkowanie aplikacji wpłynie również na ranking wyszukiwań. Na przykład dwie równoważne aplikacje, w których jeden ma znacznie więcej użytkowników, otrzymają wyższą pozycję.
