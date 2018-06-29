---
title: Określić sposób użycia szczegółowe Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeczytane i zrozumiane sekcji szczegółowe użycie woluminów CSV dla subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 723e42d2bb2af09eb2236c3cbefeee33987ea45b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060804"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Zrozumienie warunki na opłaty za programu Microsoft Azure szczegółowe dane użycia 

Szczegółowe dane użycia pliku CSV opłat zawiera codziennie i licznika użycia poziomu opłat dla bieżącego okresu rozliczeniowego. 

Aby uzyskać szczegółowe dane użycia pliku, zobacz [jak uzyskać Azure rozliczeń faktury i dziennego użycia danych](billing-download-azure-invoice-daily-usage-date.md).
Jest ona dostępna w formacie wartości rozdzielanych przecinkami (CSV), który można otworzyć w aplikacji arkusza kalkulacyjnego. Jeśli zobaczysz dostępne dwie wersje, Pobierz w wersji 2. To najbardziej bieżącego formatu pliku.

Opłaty za użycie są łączną liczbę **miesięczne** opłat w ramach subskrypcji. Opłaty za użycie nie uwzględniać żadnych środków ani rabatów.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Szczegółowe warunki i opisy szczegółowe dane użycia pliku

W poniższych sekcjach opisano ważne pojęcia, które przedstawiono w wersji 2 szczegółowe dane użycia pliku.

### <a name="statement"></a>Oświadczenie

W górnej części szczegółowe dane użycia pliku CSV zawiera usługi, które są używane w okresie rozliczeniowym w miesiącu. W poniższej tabeli wymieniono terminy i opisy przedstawionym w tej sekcji.

| Termin | Opis |
| --- | --- |
|Okres rozliczeniowy |Okresie rozliczeniowym stosowania liczników |
|Kategoria miernika |Identyfikuje usługi najwyższego poziomu do użycia |
|Podkategoria miernika |Definiuje typ usługi platformy Azure, które mogą wpływać na szybkość |
|Nazwa miernika |Określa jednostkę miary dla pomiaru są używane |
|Region miernika |Określa lokalizację centrum danych dla niektórych usług, które kosztują na podstawie lokalizacji centrum danych |
|SKU |Identyfikuje systemowy Unikatowy identyfikator dla każdego licznika Azure |
|Jednostka |Identyfikuje jednostkę, w której rozliczana jest usługa. Na przykład, GB, godziny, 10 000 s. |
|Zużyta ilość |Wartość licznika używana w okresie rozliczeniowym |
|Uwzględniona ilość |Ilość dostępnej za darmo w Twojej bieżącego okresu rozliczeniowego licznika |
|Ilość nadwyżkowego użycia |Pokazano różnicę między ilości zużytego i uwzględniona ilość. Opłaty są naliczane na tę kwotę. W przypadku ofert płatność za rzeczywiste użycie nie uwzględniona ilość z oferty to suma jest taka sama jak liczba zużywane. |
|W ramach zobowiązania |Pokazuje opłat miernika, które jest odejmowany od kwota zobowiązania skojarzonych z Twoją ofertę 6 lub 12 miesięcy. Licznik opłaty są odejmować w kolejności chronologicznej. |
|Waluta |To waluta stosowana w sieci bieżącego okresu rozliczeniowego |
|Nadwyżka |Pokazuje opłat miernika, które przekraczają kwota zobowiązania skojarzonych z Twoją ofertę 6 i 12-miesięczny |
|Stawka ze zobowiązania |Pokazuje współczynnik zobowiązań na podstawie całkowitej zobowiązania skojarzonych z Twoją ofertę 6 i 12-miesięczny |
|Stawka |Szybkość, z którego są pobierane na jednostkę rozliczeń |
|Wartość |Przedstawia wynik mnożenia kolumny ilości nadwyżkowe według kolumny szybkości. Jeśli liczba zużywane nie przekracza ilość uwzględnione, Brak bez dodatkowych opłat w tej kolumnie. |

### <a name="daily-usage"></a>Dzienne użycie

Dzienne użycie części pliku CSV przedstawia szczegółowe informacje użycia, wpływających na rozliczeń stawki. W poniższej tabeli wymieniono terminy i opisy przedstawionym w tej sekcji.

| Termin | Opis |
| --- | --- |
|Data wykorzystania |Data stosowania licznika |
|Kategoria miernika |Identyfikuje najwyższego poziomu usługi, dla którego należy to użycie |
|Identyfikator miernika |Identyfikator rachunku licznika, który jest używany do ceny użycia rozliczeń |
|Podkategoria miernika |Definiuje typ usługi Azure, które mogą wpływać na szybkość |
|Nazwa miernika |Określa jednostkę miary dla pomiaru są używane |
|Region miernika |Określa lokalizację centrum danych dla niektórych usług, które kosztują na podstawie lokalizacji centrum danych |
|Jednostka |Określa jednostki, która licznik jest rozliczana w. Na przykład, GB, godziny, 10 000 s. |
|Zużyta ilość |Wartość licznika, który został zużyty za ten dzień |
|Lokalizacja zasobu |Określa centrum danych, w którym jest uruchomiona licznika |
|Użyta usługa |Usługi platformy Azure, który został użyty |
|Grupa zasobów |Grupy zasobów, w którym działa mierniku wdrożonej w. <br/><br/>Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|Identyfikator wystąpienia | Identyfikator licznika. <br/><br/> Identyfikator zawiera nazwę, którą określisz dla licznika podczas tworzenia. Jest nazwy zasobu lub pełny identyfikator zasobu. Aby uzyskać więcej informacji, zobacz [interfejsu API usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Tagi | Tag, które można przypisać do licznika. Używaj tagów do grupowania rekordów rozliczeń.<br/><br/>Na przykład można użyć znaczników, aby dystrybuować koszty według działów, która używa licznika. Usługi obsługujące emisji tagi są maszyny wirtualne, magazynu i usług sieciowych udostępnione przy użyciu [interfejsu API usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Aby uzyskać więcej informacji, zobacz [organizowania zasobów na platformie Azure przy użyciu tagów](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Dodatkowe informacje |Metadane specyficzne dla usługi. Na przykład typ obrazu dla maszyny wirtualnej. |
|Informacje o usłudze 1 |Nazwa projektu, który usługę, należy w ramach subskrypcji |
|Informacje o usłudze 2 |Starszych pola, który przechwytuje opcjonalne metadane specyficzne dla usługi |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Jak utworzyć się, że opłaty w szczegółowe dane użycia pliku są poprawne?
Jeśli w pliku szczegółowe dane użycia, które mają więcej szczegółów na znajduje się opłat, zobacz [zrozumieć rachunku platformy Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>Informacje o zewnętrznych opłaty za usługę?
Usług zewnętrznych (znanej także jako zamówienia witryny Marketplace) są dostarczane przez dostawców usługi niezależne i są rozliczane oddzielnie. Opłaty nie widać na fakturze platformy Azure. Aby dowiedzieć się więcej, zobacz [zrozumieć sieci Azure koszty usługi zewnętrzne](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?) uzyskać szybkie rozwiązanie problemu.
