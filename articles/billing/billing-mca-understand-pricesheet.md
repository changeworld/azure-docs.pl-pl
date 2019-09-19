---
title: Omówienie terminów w arkuszu cen dla umowy klienta firmy Microsoft — Azure
description: Dowiedz się, jak interpretować dane dotyczące użycia i opłaty z tytułu umowy klienta firmy Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490664"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Terminy w arkuszu cen dla umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego platformy Azure związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Jeśli jesteś właścicielem, współautorem, czytelnikiem lub menedżerem faktur w profilu rozliczeniowym, możesz pobierać arkusz cen organizacji z witryny Azure Portal. Zobacz [Wyświetlanie i pobieranie cen organizacji](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Terminy i opisy w arkuszu cen

W poniższej sekcji opisano ważne terminy widoczne w arkuszu cen umowy klienta firmy Microsoft.

| **Nazwa pola**   | **Opis**   |
| --- | --- |
| billingAccountId  | Unikatowy identyfikator konta rozliczeniowego.   |
| billingAccountName  | Nazwa konta rozliczeniowego.  |
| billingProfileId  | Unikatowy identyfikator profilu rozliczeniowego.   |
| billingProfileName  | Nazwa profilu rozliczeniowego skonfigurowanego do otrzymywania faktur. Ceny w arkuszu cen są skojarzone z tym profilem rozliczeniowym. |
| productOrderName  | Nazwa zakupionego planu produktu. |
| serviceFamily  | Typ usługi platformy Azure. Na przykład: obliczenia, analiza, zabezpieczenia |
| Product (Produkt)  | Nazwa produktu, za który naliczane są opłaty. Na przykład: podstawowa baza danych SQL lub standardowa baza danych SQL  |
| productId  | Unikatowy identyfikator produktu, którego miernik jest wykorzystywany. |
| unitOfMeasure  | Identyfikuje jednostki miary dla rozliczeń usługi. Na przykład opłaty za usługi obliczeniowe są naliczane godzinowo. |
| meterId  | Unikatowy identyfikator miernika. |
| meterName  | Nazwa miernika. Miernik reprezentuje możliwy do wdrożenia zasób usługi platformy Azure. |
| meterCategory  | Nazwa kategorii klasyfikacji dla miernika. Na przykład _Usługi w chmurze_, _Sieć_ itp. |
| meterType  |  Nazwa typu miernika. |
| meterSubCategory  | Nazwa kategorii klasyfikacji podrzędnej miernika.  |
| meterRegion  | Nazwa regionu, w którym jest dostępny miernik usługi. Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych.    |
| tierId  | W przypadku, gdy ma to zastosowanie, identyfikuje warstwę cenową. To pole działa w połączeniu z polem tierMinimumUnits do ustawiania cen warstwowych, gdy ceny różnią się w zależności od liczby wykorzystanych jednostek.    |
| tierMinimumUnits  | Definiuje dolną granicę zakresu warstwy, dla którego są zdefiniowane ceny. Na przykład jeśli zakres wynosi od 0 do 100, pole tierMinimumUnits będzie mieć wartość 0.  |
| effectiveStartDate  | Data rozpoczęcia obowiązywania ceny. |
| effectiveEndDate  | Data końcowa dla obowiązującej ceny. |
| unitPrice  | Cena za jednostkę w momencie rozliczenia (nie jest to obowiązująca cena uśredniona) określona dla miernika i nazwy zamówienia produktu.  Uwaga: Cena jednostkowa nie jest taka sama jak cena obowiązująca w pobieranych szczegółach użycia w przypadku usług, które mają zróżnicowane ceny w różnych warstwach.  W przypadku usług z cennikiem wielowarstwowym obowiązująca cena jest stawką uśrednioną dla tych warstw i nie odpowiada cenie jednostkowej dla konkretnej warstwy. Cena uśredniona lub cena obowiązująca to cena netto wykorzystanej ilości dla wielu warstw (gdzie każda warstwa ma konkretną cenę jednostkową). |
| basePrice  | Cena rynkowa w momencie zalogowania klienta lub cena rynkowa w momencie uruchomienia miernika usługi, jeśli ma to miejsce po zalogowaniu.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie i pobieranie cennika organizacji](billing-ea-pricing.md)
