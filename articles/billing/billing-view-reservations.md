---
title: Wyświetl rezerwacji dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić rezerwacje platformy Azure w witrynie Azure portal.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369230"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Wyświetl rezerwacje platformy Azure w witrynie Azure portal

W zależności od typu subskrypcji i uprawnienia istnieje kilka sposobów wyświetlania rezerwacji dla platformy Azure.

## <a name="view-purchased-reservations"></a>Wyświetlanie zakupu rezerwacji

Domyślnie w przypadku dokonywania zakupu rezerwacji, użytkownika i administratora konta można wyświetlić rezerwacji. Użytkownika i administratora konta automatycznie uzyskują Rola właściciela na zamówienie rezerwacji i rezerwacji. Aby zezwolić innym osobom wyświetlić rezerwacji, należy dodać je jako **właściciela** lub **czytnika** zamówienia rezerwacji lub rezerwacji.

Aby uzyskać więcej informacji, zobacz [apletu Dodaj lub zmień użytkowników, którzy mogą zarządzać rezerwacji](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Aby wyświetlić rezerwacji jako właściciel lub czytnika,

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukiwanie **rezerwacje**.
    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-view-reservation/portal-reservation-search.png)  
3. Lista zawiera wszystkie rezerwacje, w których masz rolę właściciela lub czytelnika. Poszczególne zastrzeżenia pokazuje ostatnie procent użycia znane.  
    ![Przykład przedstawiający listę rezerwacji](./media/billing-view-reservation/view-reservations.png)
4. Wybierz rezerwacji i zobacz trend wykorzystania przez ostatnie pięć dni.  
    ![Trend wykorzystania rezerwacji przedstawiający przykładowy](./media/billing-view-reservation/reservation-utilization.png)
5. Możesz też pobrać [wykorzystania rezerwacji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) przy użyciu interfejs API użycia wystąpień zarezerwowanych i [pakietu zawartości usługi Power BI Microsoft Azure przez wgląd](/power-bi/service-connect-to-azure-consumption-insights).

Jeśli musisz zmienić zakres rezerwacji podziału rezerwacji lub zmiany, kto może zarządzać rezerwacji, zapoznaj się z [Zarządzanie zastrzeżeniami Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Wyświetl transakcje rezerwacji dla rejestracji Enterprise

 W przypadku rejestracji Enterprise partnera prowadzone wyświetlić rezerwacje, przechodząc do **raporty** w witrynie EA portal. Inne rejestracje Enterprise można wyświetlić rezerwacje w witrynie EA portal i w witrynie Azure portal. Musi być administrator umowy EA, aby wyświetlać transakcje rezerwacji.

Aby wyświetlić transakcje rezerwacji w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukiwanie **Cost Management + rozliczenia**.

    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Wybierz **transakcji rezerwacji**.
1. Aby filtrować wyniki, wybierz **Timespan**, **typu**, lub **opis**.
1. Wybierz przycisk **Zastosuj**.

    ![Zrzut ekranu pokazujący rezerwacji wyniki transakcji](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Aby uzyskać dane przy użyciu interfejsu API, zobacz [opłaty za transakcje Pobieranie wystąpień zarezerwowanych dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacji dla platformy Azure?](billing-save-compute-costs-reservations.md)
- [Zarządzanie rezerwacji dla platformy Azure](billing-manage-reserved-vm-instance.md)

Kup plan usługi:

- [Zapłać z góry za pojemność usługi Cosmos DB zastrzeżone](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Kup plan oprogramowania:

- [Zapłać z góry za plany oprogramowania Red Hat z listy zastrzeżeń platformy Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prepay for SUSE software plans from Azure Reservations (Opłacanie planów oprogramowania SUSE z góry z poziomu usługi Azure Reservations)](../virtual-machines/linux/prepay-suse-software-charges.md)

Poznaj sposób użycia:

- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Opis zastrzeżenia dla subskrypcji programu CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
