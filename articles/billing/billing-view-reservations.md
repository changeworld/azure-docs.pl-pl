---
title: Wyświetlanie rezerwacji zasobów platformy Azure | Microsoft Docs
description: Dowiedz się, jak wyświetlać rezerwacje platformy Azure w witrynie Azure Portal.
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
ms.openlocfilehash: 21bf96866c14615009a17279ff2fdd04bf4116ad
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490267"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Wyświetlanie rezerwacji platformy Azure w witrynie Azure Portal

W zależności od typu subskrypcji i uprawnień istnieje kilka sposobów wyświetlania rezerwacji na platformie Azure.

## <a name="view-purchased-reservations"></a>Wyświetlanie zakupionych rezerwacji

Domyślnie rezerwację może wyświetlać użytkownik, który ją zakupił, i administrator konta. Ten użytkownik oraz administrator konta automatycznie otrzymują rolę właściciela rezerwacji oraz zamówienia rezerwacji. Aby umożliwić innej osobie wyświetlanie rezerwacji, należy dodać tę osobę jako **Właściciela** lub **Czytelnika** w rezerwacji lub w zamówieniu rezerwacji.

Aby uzyskać więcej informacji, zobacz [Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacją](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Aby wyświetlić rezerwację jako właściciel lub czytelnik:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj **Rezerwacje**.
    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-view-reservation/portal-reservation-search.png)  
3. Lista zawiera wszystkie rezerwacje, w których masz rolę właściciela lub czytelnika. Dla każdej rezerwacji pokazany jest ostatni znany procent użycia.  
    ![Przykład przedstawiający listę rezerwacji](./media/billing-view-reservation/view-reservations.png)
4. Wybierz rezerwację, aby wyświetlić trend jej użycia w ciągu ostatnich pięciu dni.  
    ![Przykład pokazujący trend użycia rezerwacji](./media/billing-view-reservation/reservation-utilization.png)
5. [Użycie rezerwacji](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) możesz też pobrać za pomocą interfejsu API użycia wystąpienia zarezerwowanego oraz [pakietu zawartości Microsoft Azure Consumption Insights dla usługi Power BI](/power-bi/service-connect-to-azure-consumption-insights).

Jeśli chcesz zmienić zakres rezerwacji, podzielić rezerwację lub zmienić osoby uprawnione do zarządzania rezerwacją, zobacz [Zarządzanie rezerwacjami na platformie Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Wyświetlanie transakcji rezerwacji w przypadku rejestracji Enterprise

 Jeśli korzystasz z rejestracji Enterprise obsługiwanej przez partnera, możesz wyświetlić rezerwacje w obszarze **Raporty** w witrynie EA Portal. W przypadku pozostałych rejestracji Enterprise możesz wyświetlać rezerwacje w witrynie EA Portal i w witrynie Azure Portal. Aby wyświetlać transakcje rezerwacji, musisz być administratorem umowy EA.

Aby wyświetlić transakcje rezerwacji w witrynie Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Wybierz pozycję **Transakcje rezerwacji**.
1. Aby przefiltrować wyniki, wybierz pozycję **Przedział czasu**, **Typ** lub **Opis**.
1. Wybierz przycisk **Zastosuj**.

    ![Zrzut ekranu pokazujący wyniki wyszukiwania transakcji rezerwacji](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Jeśli chcesz pobrać dane za pomocą interfejsu API, zobacz [Pobieranie opłat za transakcje wystąpień zarezerwowanych dla klientów Enterprise](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](billing-save-compute-costs-reservations.md)
- [Zarządzanie rezerwacjami platformy Azure](billing-manage-reserved-vm-instance.md)

Kup plan usługi:

- [Opłacanie z góry pojemności zarezerwowanej usługi Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Kup plan oprogramowania:

- [Opłacanie planów oprogramowania Red Hat z góry z poziomu usługi Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prepay for SUSE software plans from Azure Reservations (Opłacanie planów oprogramowania SUSE z góry z poziomu usługi Azure Reservations)](../virtual-machines/linux/prepay-suse-software-charges.md)

Informacje na temat użycia:

- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](billing-understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](billing-understand-reserved-instance-usage-ea.md)
- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
