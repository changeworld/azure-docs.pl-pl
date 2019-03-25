---
title: Zarządzanie zastrzeżeniami platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić zakres subskrypcji i zarządzanie dostępem dla platformy Azure rezerwacji.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: b37f198052ee927ea986d2a6046bfd93b6ee5d5e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399977"
---
# <a name="manage-reservations-for-azure-resources"></a>Zarządzanie rezerwacji dla zasobów platformy Azure

Po możesz kupić rezerwację Azure, może być konieczne zastosowanie rezerwacji do innej subskrypcji, zmienić, kto może zarządzać rezerwacji lub zmienić zakres rezerwacji. Możesz również podzielić rezerwacji do dwóch rezerwacji do stosowania niektórych wystąpień, których używasz do innej subskrypcji.

Jeśli zakupiono Azure Reserved Virtual Machine Instances, możesz zmienić ustawienie optymalizacji dla rezerwacji. Rabat związany z rezerwacją można zastosować do maszyn wirtualnych w tej samej serii, lub możesz zarezerwować pojemnością centrum danych dla określonego rozmiaru maszyny Wirtualnej.

## <a name="change-the-scope-for-a-reservation"></a>Zmień zakres dla rezerwacji

 Rabat związany z rezerwacją ma zastosowanie do maszyn wirtualnych, baz danych SQL, Azure Cosmos DB i inne zasoby, które odpowiada rezerwacji i uruchamiać w zakresie rezerwacji. Kontekstu rozliczeń zależy od subskrypcji zakupu rezerwacji.

Aby zaktualizować zakresu rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz rezerwacji.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień zakres.

W przypadku zmiany z udostępnionego przez pojedynczy zakres, można wybrać tylko te subskrypcje, których jesteś właścicielem. Wybrać można tylko subskrypcje w ramach tego samego kontekstu rozliczeń, co rezerwacja.

Zakres ma zastosowanie tylko do oferty płatności zgodnie z rzeczywistym użyciem MS-AZR-0003P lub MS-AZR-0023P, oferty Enterprise MS-AZR-0017P lub MS-AZR-0148P albo typów subskrypcji dostawcy rozwiązań w chmurze.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacją

Zarządzanie rezerwacją możesz delegować, dodając osoby do ról w rezerwacji. Domyślnie osoba, która kupiła rezerwację, i administrator konta mają rolę właściciela rezerwacji.

Subskrypcje, które zawierają rabat związany z rezerwacją, można zarządzać dostępu niezależnie do rezerwacji. Jeśli nadasz innej osobie uprawnienia do zarządzania rezerwacji, który nie nadaj im praw do zarządzania subskrypcją. A Jeśli nadasz innej osobie uprawnienia do zarządzania subskrypcją w zakresie rezerwacji, który nie zapewnia im praw do zarządzania rezerwacji.

Aby delegować zarządzanie dostępem dla rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacji** do listy rezerwacji, które mają dostęp do.
3. Wybierz zastrzeżenie, którego chcesz delegować dostęp do innych użytkowników.
4. Wybierz pozycję **Kontrola dostępu (IAM)**.
5. Wybierz **Dodaj przypisanie roli** > **roli** > **właściciela**. Ewentualnie, jeśli chcesz nadać ograniczony dostęp, wybierz inną rolę.
6. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela.
7. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Podziel pojedynczy rezerwacji do dwóch rezerwacji

 Po możesz kupić więcej niż jedno wystąpienie zasobów w ramach rezerwacji, możesz przypisać wystąpienia w ramach tej rezerwacji do różnych subskrypcji. Domyślnie wszystkie wystąpienia mają jeden zakres - albo pojedynczej subskrypcji lub udostępniony. Na przykład zakupiono 10 wystąpień rezerwacji i określony zakres do subskrypcji A. Teraz można zmienić zakresu dla rezerwacji 7 do subskrypcji A i pozostałych 3 do subskrypcji B. podział rezerwacji umożliwia dystrybucji wystąpienia dla szczegółowego zakres zarządzania. Wybierając zakres udostępniony, można uprościć alokacji do subskrypcji. Jednak do celów zarządzania lub budżetowania koszt, można przydzielić ilości do określonej subskrypcji.

 Możesz podzielić rezerwacji do dwóch rezerwacji do programu PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API.

### <a name="split-a-reservation-by-using-powershell"></a>Podziel rezerwacji przy użyciu programu PowerShell

1. Pobierz identyfikator zamówienia rezerwacji, uruchamiając następujące polecenie:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Pobierz szczegóły rezerwacji:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Podziel rezerwacji na dwa i dystrybucja wystąpień:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Można zaktualizować zakresu, uruchamiając następujące polecenie:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

W zależności od typu rezerwację można anulować lub wymiany rezerwacji. Aby uzyskać więcej informacji zobacz anulowania i wymiany sekcje w następujących tematach:

- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Prepay for SUSE software plans from Azure Reservations (Opłacanie planów oprogramowania SUSE z góry z poziomu usługi Azure Reservations)](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Zmiana zoptymalizować ustawienie zarezerwowanych wystąpień maszyn wirtualnych

 W przypadku dokonywania zakupu wystąpienia zarezerwowanego maszyny Wirtualnej, możesz wybrać elastyczność rozmiar wystąpienia lub priorytet pojemności. Elastyczność rozmiaru wystąpienia ma zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tym samym [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Priorytet pojemności priorytet pojemności centrum danych wdrożeń. Ta opcja zapewnia dodatkową pewność co do możliwości uruchomienia wystąpienia maszyny Wirtualnej, gdy ich potrzebujesz.

Domyślnie gdy zakres rezerwacji jest udostępniony, elastyczność rozmiaru wystąpienia są. Możliwości Centrum danych nie są uszeregowane według priorytetów dla wdrożeń maszyn wirtualnych.

Dla zastrzeżenia, których zakres jest pojedynczym można zoptymalizować Rezerwacja priorytet pojemności zamiast elastyczność rozmiaru wystąpienia maszyny Wirtualnej.

Aby zaktualizować ustawienia optymalizacji dla rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz rezerwacji.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmiana **Optymalizuj pod kątem** ustawienie.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zapłać z góry za zasoby usługi Azure Cosmos DB z pojemnością zarezerwowane usługi Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Prepay for SUSE software plans from Azure Reservations (Opłacanie planów oprogramowania SUSE z góry z poziomu usługi Azure Reservations)](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Zrozumienie, jak jest stosowany rabat związany z rezerwacją maszyny Wirtualnej](billing-understand-vm-reservation-charges.md)
- [Zrozumienie, jak jest stosowany rabat plan oprogramowania SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Zrozumienie sposobu stosowania rabatów rezerwacji](billing-understand-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)
