---
title: Interfejsy API usługi Azure Reservation Automation | Microsoft Docs
description: Dowiedz się więcej na temat interfejsów API platformy Azure, których można użyć do programistycznego pobrania informacji o rezerwacji.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2019
ms.author: banders
ms.openlocfilehash: 20ed2bcf793ab5c3913ccf66d338e71c1a99a003
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478933"
---
# <a name="apis-for-azure-reservation-automation"></a>Interfejsy API usługi Azure Reservation Automation

Użyj interfejsów API platformy Azure, aby programowo uzyskać informacje dla organizacji dotyczące usługi lub rezerwacji oprogramowania platformy Azure.

## <a name="find-reservation-plans-to-buy"></a>Znajdź plany rezerwacji do zakupu

Użyj interfejsu API rekomendacji rezerwacji, aby uzyskać zalecenia dotyczące zakupu rezerwacji na podstawie użycia w organizacji. Aby uzyskać więcej informacji, zobacz [pobieranie zaleceń dotyczących rezerwacji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Możesz również analizować użycie zasobów przy użyciu szczegółów użycia interfejsu API zużycia. Aby uzyskać więcej informacji, zobacz [szczegóły użycia — lista dla okresu rozliczeniowego według konta](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod)rozliczeń. Zasoby platformy Azure, z których korzystasz, są zwykle najlepszym kandydatem do rezerwacji.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Rezerwacje platformy Azure i plany oprogramowania można kupić programowo przy użyciu interfejsów API REST. Aby dowiedzieć się więcej, zobacz temat [rezerwacja Order-Purchase API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Oto przykładowe żądanie zakupu przy użyciu interfejsu API REST:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Treść żądania:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Możesz również kupić rezerwację w Azure Portal. Aby uzyskać więcej informacji zobacz następujące artykuły:

Plany usługi:
- [Maszyna wirtualna](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Plany oprogramowania:
- [Oprogramowanie SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Pobieranie rezerwacji

Jeśli jesteś klientem platformy Azure z Umowa Enterprise (klient z umową EA), możesz uzyskać rezerwacje zakupione w organizacji za pomocą [interfejsu API pobierania opłat za zastrzeżonego wystąpienia transakcji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). W przypadku innych subskrypcji Uzyskaj listę zakupionych rezerwacji i masz uprawnienia do wyświetlania, korzystając z [listy Order zastrzeżeń](/rest/api/reserved-vm-instances/reservationorder/list)interfejsu API. Domyślnie właściciel konta lub osoba, która zakupiła rezerwację, ma uprawnienia do wyświetlania rezerwacji.

## <a name="see-reservation-usage"></a>Zobacz Użycie zastrzeżenia

Jeśli jesteś klientem z umową EA, możesz programowo zobaczyć, w jaki sposób rezerwacje w organizacji są używane. Aby uzyskać więcej informacji, zobacz [pobieranie zarezerwowanych wystąpień użycia dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). W przypadku innych subskrypcji Użyj [listy podsumowania rezerwacji interfejsu API — lista według kolejności rezerwacji i rezerwacji](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Jeśli okaże się, że rezerwacje w organizacji są używane:

- Upewnij się, że maszyny wirtualne tworzone przez organizację są zgodne z rozmiarem maszyny wirtualnej, która znajduje się w rezerwacji.
- Upewnij się, że rozmiar wystąpienia jest elastyczny. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — Zmień ustawienie optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Zmień zakres rezerwacji na współużytkowany, aby był bardziej szeroki. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — Zmień zakres dla rezerwacji](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Wymienia nieużywaną ilość. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — anulowanie i wymiana](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Udzielanie dostępu do rezerwacji

Pobierz listę wszystkich rezerwacji, do których użytkownik ma dostęp przy użyciu [interfejsu API listy rezerwacji-operacji](/rest/api/reserved-vm-instances/reservationorder/list). Aby umożliwić programowe uzyskiwanie dostępu do rezerwacji, zobacz jeden z następujących artykułów:

- [Zarządzanie dostępem przy użyciu RBAC i interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
- [Zarządzanie dostępem przy użyciu RBAC i Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Zarządzanie dostępem przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dzielenie lub scalanie rezerwacji

Po zakupieniu więcej niż jednego wystąpienia zasobu w ramach rezerwacji możesz chcieć przypisać wystąpienia w ramach tej rezerwacji do różnych subskrypcji. Zakres rezerwacji można zmienić tak, aby dotyczył wszystkich subskrypcji w ramach tego samego kontekstu rozliczania. Jednak w celu zarządzania kosztami lub budżetowania można zachować zakres jako "pojedyncza subskrypcja" i przypisać wystąpienia rezerwacji do określonej subskrypcji.

Aby podzielić rezerwację, użyj funkcji [rezerwacji API-Split](/rest/api/reserved-vm-instances/reservation/split). Rezerwacja można także podzielić przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — dzielenie rezerwacji na dwie rezerwacje](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Aby scalić dwie rezerwacje w jedną rezerwację, użyj funkcji [rezerwacji](/rest/api/reserved-vm-instances/reservation/merge)interfejsu API — scalanie.

## <a name="change-scope-for-a-reservation"></a>Zmień zakres dla rezerwacji

Zakresem rezerwacji może być jedna subskrypcja, jedna grupa zasobów lub wszystkie subskrypcje w kontekście rozliczeń. Jeśli ustawisz zakres na pojedynczą subskrypcję lub pojedynczą grupę zasobów, rezerwacja zostanie dopasowana do uruchomionych zasobów w wybranej subskrypcji. Jeśli usuniesz lub przeniesiesz subskrypcję lub grupę zasobów, rezerwacja nie zostanie wykorzystana.  Jeśli ustawisz zakres jako udostępniony, platforma Azure dopasowuje rezerwację do zasobów, które działają we wszystkich subskrypcjach w kontekście rozliczania. Kontekst rozliczania zależy od subskrypcji użytej do zakupu rezerwacji. Możesz wybrać zakres przy zakupie lub zmienić go w dowolnym momencie po zakupie. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — Zmień zakres](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Aby programowo zmienić zakres, Użyj usługi API [Reservation-Update](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Dowiedz się więcej

- [Co to są rezerwacje dotyczące platformy Azure](billing-save-compute-costs-reservations.md)
- [Zapoznaj się z zastosowaniem rabatu rezerwacji maszyny wirtualnej](billing-understand-vm-reservation-charges.md)
- [Zapoznaj się z zastosowaniem rabatu planu oprogramowania w systemie SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md)
- [Zapoznaj się z zastosowaniami innych rabatów rezerwacji](billing-understand-reservation-charges.md)
- [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](billing-reserved-instance-windows-software-costs.md)
- [Azure Reservations w programie Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
