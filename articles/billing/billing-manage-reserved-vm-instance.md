---
title: Zarządzanie wystąpień zarezerwowanych maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić zakres subskrypcji i zarządzanie dostępem do zarezerwowanych wystąpień maszyn wirtualnych platformy Azure.
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
ms.date: 07/31/2018
ms.author: yashesvi
ms.openlocfilehash: 589afc736faaa210fdcd5cf6c79d10af4af3c0e9
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502529"
---
# <a name="manage-reserved-instances-in-azure"></a>Zarządzanie wystąpień zarezerwowanych na platformie Azure

Po możesz kupić wystąpienia zarezerwowane maszyny Wirtualnej platformy Azure, warto zastosować wystąpienia zarezerwowanego do innej subskrypcji niż określona podczas zakupu. Alternatywnie zgodnych maszyn wirtualnych są uruchomione w ramach wielu subskrypcji, możesz zmienić zakres wystąpienia zarezerwowanego udostępniony. Aby zmaksymalizować rabat na wystąpienie zarezerwowane, upewnij się, że liczba wystąpień zakupiono odpowiada atrybuty i liczbę maszyn wirtualnych, które należy uruchomiona. Aby dowiedzieć się więcej na temat wystąpień zarezerwowanych platformy Azure, zobacz [Oszczędzaj pieniądze, wstępnie płacić za maszyny wirtualne platformy Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Zmień zakres dla wystąpień zarezerwowanych
 Rabat wystąpienia zarezerwowanego ma zastosowanie do maszyn wirtualnych, która pasuje do wystąpienia zarezerwowanego i uruchamiane w ramach zakres wystąpienia zarezerwowanego. Zakres wystąpień zarezerwowanych może być pojedynczej subskrypcji, czy wszystkie subskrypcje w kontekstu rozliczeń. Jeśli zakres jest ustawiona na jedną subskrypcję, zarezerwowane wystąpienia jest dopasowany do uruchomionych maszyn wirtualnych w wybranej subskrypcji. Jeśli zakres do udostępnionego, Azure dopasowań wystąpień zarezerwowanych maszyn wirtualnych, które są uruchamiane w przypadku wszystkich subskrypcji w ramach kontekstu rozliczeń. Kontekstu rozliczeń zależy od subskrypcji zakupu wystąpienia zarezerwowanego. Aby dowiedzieć się więcej, zobacz [Zapłać z góry za maszyny wirtualne z wystąpieniami zarezerwowanymi](https://go.microsoft.com/fwlink/?linkid=861721).

Aby zaktualizować zakres wystąpienia zarezerwowanego: 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz wystąpienia zarezerwowanego.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień zakres. W przypadku zmiany z udostępnionego przez pojedynczy zakres, można wybrać tylko te subskrypcje, których jesteś właścicielem. Można wybrać tylko subskrypcje w ramach tego samego kontekstu rozliczeń jako wystąpienia zarezerwowanego. Kontekstu rozliczeń jest określany przez subskrypcję, która wybranych podczas wystąpienia zarezerwowanego została zakupiona. Zakres dotyczy tylko subskrypcji MS-AZR - 0003P oferty zgodnie z rzeczywistym użyciem i subskrypcji oferty MS-AZR - 0017P przedsiębiorstwa. Dla umów enterprise Agreement subskrypcji i testowania nie kwalifikują się uzyskać rabat w wysokości wystąpienia zarezerwowanego.

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Dodawanie lub modyfikowanie użytkowników, którzy mogą zarządzać wystąpień zarezerwowanych
Przez dodanie osoby do ról na wystąpienie zarezerwowane, możesz delegować Zarządzanie wystąpienia zarezerwowanego. Domyślnie osoby, które zakupione wystąpienia zarezerwowanego, a administrator konta ma rolę właściciela na wystąpienia zarezerwowanego. 

Możesz zarządzać dostępu do wystąpień zarezerwowanych niezależnie z subskrypcji, które zawierają rabat w wysokości wystąpienia zarezerwowanego. Jeśli nadasz innej osobie uprawnienia do zarządzania wystąpienie zarezerwowane, który nie nadaj im praw do zarządzania subskrypcją. A Jeśli nadasz innej osobie uprawnienia do zarządzania subskrypcją w zakresie wystąpień zarezerwowanych, które nie nadaj im praw do zarządzania wystąpienia zarezerwowanego.
 
Aby delegować zarządzanie dostępem do wystąpienia zarezerwowanego: 
1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Wybierz **wszystkich usług** > **rezerwacji** do tworzenia wystąpień zarezerwowanych, które mają dostęp do listy.
3.  Wybierz wystąpienie zarezerwowane, którego chcesz delegować dostęp do innych użytkowników.
4.  Wybierz **kontrola dostępu (IAM)** w menu.
5.  Wybierz **Dodaj** > **roli** > **właściciela** (lub innej roli, jeśli chcesz nadać dostęp ograniczony). 
6. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela. 
7. Wybierz użytkownika, a następnie wybierz **Zapisz**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Optymalizowanie wystąpienia zarezerwowanego maszyny Wirtualnej dla priorytetu elastyczność lub pojemność rozmiar maszyny Wirtualnej
 Elastyczność wystąpienia maszyny Wirtualnej ma zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tym samym [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Domyślnie gdy zakresem rezerwacji jest udostępniony, elastyczność rozmiar wystąpienia jest włączone, i możliwości centrów danych nie są uszeregowane według priorytetów dla wdrożeń maszyn wirtualnych. Dla zastrzeżenia, których zakres jest pojedynczym można zoptymalizować Rezerwacja priorytet pojemności zamiast elastyczność rozmiaru wystąpienia maszyny Wirtualnej. Priorytet pojemności rezerwuje pojemnością centrum danych dla wdrożeń, oferując dodatkową pewność co do możliwości uruchomienia wystąpienia maszyny Wirtualnej, gdy ich potrzebujesz.

Aby zaktualizować zakres wystąpienia zarezerwowanego: 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz wystąpienia zarezerwowanego.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień Optymalizuj pod kątem ustawienie.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Dzielenie pojedynczego wystąpienia zarezerwowanego do dwóch wystąpień zarezerwowanych
 Po możesz kupić więcej niż jedno wystąpienie, możesz przypisać wystąpienia w ramach wystąpienia zarezerwowanego do różnych subskrypcji. Domyślnie wszystkie wystąpienia (określonego podczas zakupu ilość) mają jeden zakres - albo pojedynczej subskrypcji lub udostępniony. Na przykład nabyć 10 standardowych maszyn wirtualnych D2 i określony zakres do subskrypcji A. Można teraz zmienić zakres siedem zarezerwowanych wystąpień do subskrypcji A i pozostałych 3 do subskrypcji B. podział wystąpienia zarezerwowanego umożliwia dystrybucji wystąpienia dla szczegółowego zakres zarządzania. Wybierając zakres udostępniony, można uprościć alokacji do subskrypcji. Jednak do celów zarządzania lub budżetowania koszt, można przydzielić ilości do określonej subskrypcji.

 Możesz podzielić wystąpienia zarezerwowanego do dwóch wystąpień zarezerwowanych do programu PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Podziel wystąpień zarezerwowanych przy użyciu programu PowerShell
1. Pobierz identyfikator zamówienia rezerwacji wystąpienia, uruchamiając następujące polecenie:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Pobierz szczegóły wystąpienia zarezerwowanego:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Podziel wystąpień zarezerwowanych na dwa i dystrybucja wystąpień:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```

1. Można zaktualizować zakresu, uruchamiając następujące polecenie:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat wystąpień zarezerwowanych platformy Azure, zobacz następujące artykuły:

- [Co to jest Azure Reserved VM Instances?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Understand how the reserved instance discount is applied (Sposób udzielania rabatu za wystąpienia zarezerwowane)](billing-understand-vm-reservation-charges.md)
- [Understand reserved instance usage for your Pay-As-You-Go subscription (Sposób rozliczania użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](billing-understand-reserved-instance-usage.md)
- [Understand reserved instance usage for your Enterprise enrollment (Sposób rozliczania użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](billing-understand-reserved-instance-usage-ea.md)
- [Windows software costs not included with reserved instances (Nieuwzględnianie kosztów oprogramowania systemu Windows w przypadku wystąpień zarezerwowanych)](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
