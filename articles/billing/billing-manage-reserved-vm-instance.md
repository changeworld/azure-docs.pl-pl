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
ms.date: 08/08/2018
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628576"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Zarządzanie rezerwacji dla zasobów platformy Azure

Po możesz kupić rezerwację platformy Azure, warto zastosować rezerwacji do innej subskrypcji niż ta, które zostały określone podczas zakupu. Alternatywnie zgodnych maszyn wirtualnych, baz danych SQL lub inne zasoby są uruchomione w ramach wielu subskrypcji, możesz zmienić zakres rezerwacji udostępniony. Aby zmaksymalizować rabat związany z rezerwacją, upewnij się, że liczba wystąpień zakupiono odpowiada atrybuty i liczby zasobów, do których masz uruchomiony. Aby dowiedzieć się więcej, zobacz [Azure rezerwacje](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Zmień zakres dla rezerwacji

 Rabat związany z rezerwacją ma zastosowanie do maszyn wirtualnych, baz danych SQL lub innych zasobów, która pasuje do rezerwacji i uruchamiane w ramach zakresu rezerwacji. Zakres rezerwacji może być pojedynczej subskrypcji, czy wszystkie subskrypcje w kontekstu rozliczeń. Jeśli zakres jest ustawiona na jedną subskrypcję, rezerwacja jest dopasowany do uruchomionego zasobów w wybranej subskrypcji. Jeśli ustawiono zakres udostępniony, Azure pasuje rezerwacji do zasobów, które są uruchamiane w przypadku wszystkich subskrypcji w ramach kontekstu rozliczeń. Kontekstu rozliczeń zależy od subskrypcji zakupu rezerwacji.

Aby zaktualizować zakresu rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz rezerwacji.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień zakres. W przypadku zmiany z udostępnionego przez pojedynczy zakres, można wybrać tylko te subskrypcje, których jesteś właścicielem. Można wybrać tylko subskrypcje w ramach tego samego kontekstu rozliczeń, co Rezerwacja. Kontekstu rozliczeń jest określany przez subskrypcję, dla której wybrano podczas rezerwacji został kupiony. Zakres dotyczy tylko subskrypcji MS-AZR - 0003P oferty zgodnie z rzeczywistym użyciem i subskrypcji oferty MS-AZR - 0017P przedsiębiorstwa. Dla umów enterprise Agreement subskrypcji i testowania nie kwalifikują się uzyskać rabat związany z rezerwacją.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Dodawanie lub modyfikowanie użytkowników, którzy mogą zarządzać rezerwacji

Możesz delegować Zarządzanie zastrzeżenia przez dodanie osoby do ról w rezerwacji. Domyślnie osoby, które zakupione rezerwacji, a administrator konta ma rolę właściciela na rezerwacji.

Subskrypcje, które zawierają rabat związany z rezerwacją, można zarządzać dostępu niezależnie do rezerwacji. Jeśli nadasz innej osobie uprawnienia do zarządzania rezerwacji, który nie nadaj im praw do zarządzania subskrypcją. A Jeśli nadasz innej osobie uprawnienia do zarządzania subskrypcją w zakresie rezerwacji, który nie zapewnia im praw do zarządzania rezerwacji.

Aby delegować zarządzanie dostępem dla rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacji** do listy rezerwacji, które mają dostęp do.
3. Wybierz zastrzeżenie, którego chcesz delegować dostęp do innych użytkowników.
4. Wybierz **kontrola dostępu (IAM)** w menu.
5. Wybierz **Dodaj** > **roli** > **właściciela** (lub innej roli, jeśli chcesz nadać dostęp ograniczony).
6. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela. 
7. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Optymalizowanie wystąpienia zarezerwowanego maszyny Wirtualnej dla priorytetu elastyczność lub pojemność rozmiar maszyny Wirtualnej

 Elastyczność wystąpienia maszyny Wirtualnej ma zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tym samym [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Domyślnie gdy zakresem rezerwacji jest udostępniony, elastyczność rozmiar wystąpienia jest włączone, i możliwości centrów danych nie są uszeregowane według priorytetów dla wdrożeń maszyn wirtualnych. Dla zastrzeżenia, których zakres jest pojedynczym można zoptymalizować Rezerwacja priorytet pojemności zamiast elastyczność rozmiaru wystąpienia maszyny Wirtualnej. Priorytet pojemności rezerwuje pojemnością centrum danych dla wdrożeń, oferując dodatkową pewność co do możliwości uruchomienia wystąpienia maszyny Wirtualnej, gdy ich potrzebujesz.

Aby zaktualizować zakresu rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz rezerwacji.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień Optymalizuj pod kątem ustawienie.

## <a name="split-a-single-reservation-into-two-reservations"></a>Podziel pojedynczy rezerwacji do dwóch rezerwacji

 Po możesz kupić więcej niż jedno wystąpienie, możesz przypisać wystąpienia w ramach rezerwacji do różnych subskrypcji. Domyślnie wszystkie wystąpienia (określonego podczas zakupu ilość) mają jeden zakres - albo pojedynczej subskrypcji lub udostępniony. Na przykład nabyć 10 standardowych maszyn wirtualnych D2 i określony zakres do subskrypcji A. Teraz można zmienić zakresu dla rezerwacji siedem subskrypcji A i pozostałych 3 do subskrypcji B. podział zastrzeżenie umożliwia dystrybucji wystąpienia dla szczegółowego zakres zarządzania. Wybierając zakres udostępniony, można uprościć alokacji do subskrypcji. Jednak do celów zarządzania lub budżetowania koszt, można przydzielić ilości do określonej subskrypcji.

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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zapłać z góry za zasoby obliczeniowe bazy danych SQL Database o pojemności usługi Azure SQL Database, zarezerwowane](../sql-database/sql-database-reserved-capacity.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
