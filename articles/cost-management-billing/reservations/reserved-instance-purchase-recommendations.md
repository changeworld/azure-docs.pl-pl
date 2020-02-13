---
title: Jak są tworzone zalecenia dotyczące rezerwacji platformy Azure
description: Dowiedz się, jak są tworzone zalecenia dotyczące rezerwacji platformy Azure dla maszyn wirtualnych.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851355"
---
# <a name="how-reservation-recommendations-are-created"></a>Jak są tworzone zalecenia dotyczące rezerwacji

Zalecenia dotyczące zakupu wystąpień zarezerwowanych platformy Azure są generowane przez [interfejs API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations) użycia platformy Azure. Zalecenia z interfejsu API są również używane przez usługę [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). Usługa Advisor wyświetla zalecenia w witrynie Azure Portal.

Jeśli masz maszyny wirtualne działające na platformie Azure, możesz skorzystać z obniżonych cen za wystąpienia zarezerwowane i opłacić z góry maszyny wirtualne. Interfejs API zaleceń dotyczących użycia firmy Microsoft szacuje użycie przez 7, 30 i 60 dni oraz zaleca optymalne konfiguracje dla wystąpień zarezerwowanych. Oblicza on koszt, który zostałby poniesiony w przypadku braku wystąpień zarezerwowanych w porównaniu z kosztami, które zostałyby poniesione za wystąpienia zarezerwowane w celu zoptymalizowania oszczędności.

Usługa Azure Advisor przedstawia zalecenia na podstawie okresu 30-dniowego.

Dla uproszczenia w poniższym przykładzie przedstawiono obliczenia wykonywane w przypadku zalecenia na podstawie siedmiu dni. Ta sama metoda jest stosowana przy obliczaniu zaleceń 30- lub 60-dniowych.

## <a name="calculation-method-example"></a>Przykład metody obliczania

Załóżmy, że godzinne użycie maszyny wirtualnej z systemem Windows dla określonej jednostki SKU i regionu zmienia się w ciągu siedmiu dni (168 godzin). Minimalne użycie to 65 jednostek, a maksymalne użycie to 127 jednostek w ciągu siedmiu dni. W tym przykładzie w godzinie 79 wykorzystano 80 maszyn wirtualnych, a zakupiono 75 wystąpień zarezerwowanych.

Jeśli zakupiono 75 wystąpień zarezerwowanych, płacisz następujące koszty za godzinę 79:

- 75 wystąpień zarezerwowanych. Koszt jest opłacany z góry przy zakupie wystąpień zarezerwowanych.
- Wystąpienia zarezerwowane obejmują koszt sprzętu uruchomionych maszyn wirtualnych, więc za 75 godzin płacisz cenę samego oprogramowania.
- Użycie za godzinę 79 wynosi 80, więc zapłacisz cenę mierzoną za pięć godzin korzystania z kombinacji systemu Windows oraz sprzętu. Cena za kombinację jest oparta na stawkach umowy Enterprise Agreement (EA) lub płatności zgodnie z rzeczywistym użyciem.

W przypadku zakupu 75 wystąpień zarezerwowanych można obliczyć łączny koszt, dodając poprzednie koszty godzinowe. Można też obliczyć bieżący koszt przy użyciu obowiązującej stawki. Różnica między tymi dwoma kwotami to oszczędności za siedem dni w tym przykładzie.

Interfejs API wykonuje obliczenia dla każdego konkretnego punktu użycia. Następnie zwraca zalecaną ilość, przy której oszczędności są zmaksymalizowane. W poniższym przykładzie wykres pokazuje szczyt oszczędności na poziomie 68. Później oszczędności zmniejszają się, dlatego interfejs API zaleca używanie ilości 68.

![Diagram przedstawiający najwyższe oszczędności](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Inne oczekiwane zachowanie interfejsu API

- Interfejs API pokazuje możliwe oszczędności, uwzględniając [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) dla systemu Windows, gdy ta korzyść jest stosowana. Jeśli korzyść nie jest używana, zalecenia interfejsu API są oparte na podstawowym koszcie systemu Windows. Jeśli jest ona dostępna, rozważ zastosowanie korzyści użycia hybrydowego platformy Azure w celu zwiększenia oszczędności.
- W przypadku korzystania z okresu ostatnich siedmiu dni zalecenia mogą nie zostać przedstawione, gdy maszyny wirtualne są zamknięte przez więcej niż jeden dzień.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, [jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](../manage/understand-vm-reservation-charges.md).
