---
title: Omówienie stosowania rabatu za rezerwację do usługi Azure Disk Storage
description: Dowiedz się, jak rabat na zarezerwowane dyski platformy Azure jest stosowany do dysków zarządzanych SSD w warstwie Premium.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902140"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Omówienie stosowania rabatu za rezerwację do usługi Azure Disk Storage

Po zakupie pojemności zarezerwowanej dysku na platformie usługi Azure rabat za rezerwację jest stosowany automatycznie do zasobów dyskowych spełniających warunki rezerwacji. Rabat za rezerwację dotyczy tylko pojemności dysku, a za migawki dysku są naliczane opłaty zgodnie z rzeczywistym użyciem.

Aby uzyskać więcej informacji na temat rezerwacji dysków platformy Azure, zobacz [Oszczędzanie kosztów dzięki rezerwacji dysków platformy Azure](../../virtual-machines/linux/disks-reserved-capacity.md).
Aby uzyskać informacje na temat cennika rezerwacji dysków platformy Azure, zobacz [Cennik dysków platformy Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację dysków platformy Azure jest dostępny na zasadzie „wykorzystaj lub strać” i jest stosowany do zasobów dysków zarządzanych co godzinę. Jeśli o danej godzinie nie masz żadnych zasobów dysków zarządzanych spełniających warunki rezerwacji, utracisz ilość rezerwacji za tę godzinę. Niewykorzystanych godzin nie można przenieść na później.

Po usunięciu zasobu rabat za rezerwację jest automatycznie stosowany do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak jest stosowany rabat za rezerwację dysków platformy Azure Storage w zależności od wdrożeń:

Załóżmy, że zakupiono 100 dysków P30 (~1 TiB na dysk) o pojemności zarezerwowanej w regionie Zachodnie stany USA 2 na okres 1 roku. Załóżmy, że koszt tej przykładowej rezerwacji to 140 100 USD. Możesz zapłacić całą kwotę z góry lub płacić stałe miesięczne raty w wysokości 11 675 USD przez kolejnych 12 miesięcy.
W tych przykładach założono, że wybrano plan płatności miesięcznych za rezerwację. W poniższych scenariuszach opisano, co się stanie w przypadku niepełnego lub nadmiernego wykorzystania pojemności zarezerwowanej bądź podzielenia jej na warstwy.

### <a name="underusing-your-capacity"></a>Niepełne wykorzystanie pojemności

Załóżmy, że o danej godzinie w okresie rezerwacji wdrożono tylko 99 dysków SSD P30 w warstwie Premium ze 100 rezerwacji dysków P30. Pozostały 1 dysk P30 nie jest stosowany przez tę godzinę i nie jest przenoszony.

### <a name="overusing-your-capacity"></a>Nadmierne wykorzystanie pojemności

Załóżmy, że o danej godzinie w okresie rezerwacji jest używanych 101 dysków SSD P30 w warstwie Premium. Rabat za rezerwację jest stosowany tylko do 100 dysków P30, a opłata za pozostały 1 dysk P30 jest naliczana przez tę godzinę według stawek płatności zgodnie z rzeczywistym użyciem. Jeśli w ciągu następnej godziny użycie zmaleje do 100 dysków P30, rezerwacja obejmie całe użycie.

### <a name="tiering-your-capacity"></a>Dzielenie pojemności na warstwy

Załóżmy, że o danej godzinie w okresie rezerwacji chcesz używać łącznie 200 dysków SSD P30 w warstwie Premium. Przez pierwsze 30 minut używasz tylko 100 dysków. W tym okresie korzystanie jest w pełni pokryte, ponieważ zgłoszono rezerwację dla 100 dysków P30. Jeśli potem przestaniesz używać pierwszych 100 (czyli nie będziesz używać żadnych), a następnie zaczniesz używać innych 100 przez pozostałe 30 minut, to użycie jest również pokryte rezerwacją.

![Przykłady niepełnego i nadmiernego wykorzystania pojemności oraz dzielenia jej na warstwy](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Obniżenie kosztów dzięki rezerwacji dysków platformy Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Obniżenie kosztów dzięki rezerwacji dysków platformy Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)