---
title: Terminy w arkuszu cen dla umowy klienta firmy Microsoft — Azure
description: Dowiedz się, jak interpretować dane dotyczące użycia i opłaty z tytułu umowy klienta firmy Microsoft.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 1e776e351fe723f0fbc5b1d6a8f5593a9fc975b7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199623"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Terminy w arkuszu cen dla umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego platformy Azure związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Jeśli jesteś właścicielem, współautorem, czytelnikiem lub menedżerem faktur w profilu rozliczeniowym, możesz pobierać arkusz cen organizacji z witryny Azure Portal. Zobacz [Wyświetlanie i pobieranie cen organizacji](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Terminy i opisy w arkuszu cen

W poniższej sekcji opisano ważne terminy widoczne w arkuszu cen umowy klienta firmy Microsoft.

| **Nazwa pola**   | **Opis**   |
| --- | --- |
| basePrice  | Cena rynkowa w momencie zalogowania klienta lub cena rynkowa w momencie uruchomienia miernika usługi, jeśli ma to miejsce po zalogowaniu.   |
| billingAccountId  | Unikatowy identyfikator konta rozliczeniowego.   |
| billingAccountName  | Nazwa konta rozliczeniowego.  |
| billingCurrency | Waluta, w której są określane opłaty. |
| billingProfileId  | Unikatowy identyfikator profilu rozliczeniowego.   |
| billingProfileName  | Nazwa profilu rozliczeniowego skonfigurowanego do otrzymywania faktur. Ceny w arkuszu cen są skojarzone z tym profilem rozliczeniowym. |
| currency | Waluta, w której są określane wszystkie ceny. |
| discount | Rabat cenowy oferowany w przypadku warstwy Graduation, warstwy Bezpłatna, uwzględnionej ilości lub rabatów negocjowanych (jeśli mają zastosowanie). Przedstawiany jako wartość procentowa. |
| effectiveEndDate  | Data końcowa dla obowiązującej ceny. |
| effectiveStartDate  | Data rozpoczęcia obowiązywania ceny. |
| includedQuantity | Ilość określonej usługi, którą może wykorzystać klient bez naliczania opłat przyrostowych. |
| marketPrice | Bieżąca powszechna cena rynkowa dla danej usługi. |
| meterId  | Unikatowy identyfikator miernika. |
| meterCategory  | Nazwa kategorii klasyfikacji dla miernika. Na przykład _Usługi w chmurze_, _Sieć_ itp. |
| meterName  | Nazwa miernika. Miernik reprezentuje możliwy do wdrożenia zasób usługi platformy Azure. |
| meterSubCategory  | Nazwa kategorii klasyfikacji podrzędnej miernika.  |
| meterType  |  Nazwa typu miernika. |
| meterRegion  | Nazwa regionu, w którym jest dostępny miernik usługi. Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych.    |
| Product (Produkt)  | Nazwa produktu, za który naliczane są opłaty. Na przykład: podstawowa baza danych SQL lub standardowa baza danych SQL  |
| productId  | Unikatowy identyfikator produktu, którego miernik jest wykorzystywany. |
| productOrderName  | Nazwa zakupionego planu produktu. |
| serviceFamily  | Typ usługi platformy Azure. Na przykład: obliczenia, analiza, zabezpieczenia |
| tierMinimumUnits  | Definiuje dolną granicę zakresu warstwy, dla którego są zdefiniowane ceny. Na przykład jeśli zakres wynosi od 0 do 100, pole tierMinimumUnits będzie mieć wartość 0.  |
| unitOfMeasure  | Identyfikuje jednostki miary dla rozliczeń usługi. Na przykład opłaty za usługi obliczeniowe są naliczane godzinowo. |
| unitPrice  | Cena za jednostkę w momencie rozliczenia (nie jest to obowiązująca cena uśredniona) określona dla miernika i nazwy zamówienia produktu.  Uwaga: Cena jednostkowa nie jest taka sama jak cena obowiązująca w pobieranych szczegółach użycia w przypadku usług, które mają zróżnicowane ceny w różnych warstwach.  W przypadku usług z cennikiem wielowarstwowym obowiązująca cena jest stawką uśrednioną dla tych warstw i nie odpowiada cenie jednostkowej dla konkretnej warstwy. Cena uśredniona lub cena obowiązująca to cena netto wykorzystanej ilości dla wielu warstw (gdzie każda warstwa ma konkretną cenę jednostkową). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie i pobieranie cennika organizacji](ea-pricing.md)
