---
title: Zarządzanie wystąpieniami Azure zastrzeżonej maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić zakres subskrypcji i zarządzanie dostępem dla zastrzeżonego wystąpień maszyn wirtualnych Azure.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: 003abb3ddf632261dc372549209ba664cb8f8398
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="manage-reserved-virtual-machine-instances"></a>Zarządzanie wystąpieniami zastrzeżonej maszyny wirtualnej

Po kupisz zastrzeżone wystąpienia maszyny Wirtualnej Azure może chcesz zastosować zastrzeżenie do innej subskrypcji niż określona podczas zakupów. Alternatywnie pasującego maszyny wirtualne są uruchomione w ramach wielu subskrypcji, możesz zmienić zakres rezerwacji współużytkowane. Aby zmaksymalizować rabat rezerwacji, upewnij się, czy liczba wystąpień zakupiono zgodny z atrybutów i liczbę maszyn wirtualnych, które masz uruchomiony. Aby dowiedzieć się więcej na temat zastrzeżone wystąpień maszyn wirtualnych, zobacz [zaoszczędzić na wstępnie płatności maszyn wirtualnych platformy Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Zmień zakres dla rezerwacji
 Twoja zniżka zastrzeżenie ma zastosowanie do maszyn wirtualnych, które są zgodne z rezerwacji i uruchamiane w ramach zakresu zastrzeżenia. Zakres zastrzeżenie może być jedną subskrypcją lub wszystkie subskrypcje w kontekst rozliczeń. Jeśli ustawisz zakres jedną subskrypcją rezerwacji jest dopasowywany do maszyn wirtualnych działających w wybranej subskrypcji. Jeśli zakres do platformy Azure, współdzielonych zgodna zastrzeżenia do maszyn wirtualnych uruchomionych w ramach wszystkich subskrypcji w kontekście rozliczeń. Kontekst rozliczeń jest zależna od z subskrypcją użytą do kupić rezerwacji. Aby dowiedzieć się więcej, zobacz [wstępne płatności dla maszyn wirtualnych z zarezerwowanych wystąpień maszyn wirtualnych](https://go.microsoft.com/fwlink/?linkid=861721).

Aby zaktualizować zakresu zastrzeżenia: 
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie usługi** > **zastrzeżenia**.
3. Wybierz.
4. Wybierz **ustawienia** > **konfiguracji**.
5. Zmienić zakres. W przypadku zmiany z udostępnionego na pojedynczy zakres, można wybrać tylko te subskrypcje, w której jesteś właścicielem. Można wybrać tylko subskrypcji w tym samym kontekście rozliczeń jako rezerwacji. Kontekst rozliczeń zależy od wybranej podczas rezerwacji został zakupiony subskrypcji. Zakres ma zastosowanie tylko do oferty płatności obejmujące MS-AZR - 0003P subskrypcji i subskrypcje oferta MS-AZR - 0017P przedsiębiorstwa. Dla umowy enterprise i testowania subskrypcji nie są objęte uzyskać rabat rezerwacji.

## <a name="split-a-single-reservation-into-two-reservations"></a>Podziel zastrzeżenie jednej do dwóch zastrzeżenia
 Po zakupie więcej niż jedno wystąpienie można przypisać wystąpień w ramach zastrzeżenie do różnych subskrypcji. Domyślnie wszystkie wystąpienia (określone podczas zakupu ilość) mają jeden zakres - albo jedną subskrypcją lub udostępnionego. Na przykład zakupione 10 standardowe maszyn wirtualnych D2 i określony zakres być subskrypcji A. Można teraz zmienić zakres 7 wystąpień zastrzeżone maszyny Wirtualnej do subskrypcji, A i pozostałe 3 do subskrypcji B. dzielenia zastrzeżenie umożliwia dystrybucję wystąpień szczegółowego zakresu zarządzania. Można uprościć alokacji do subskrypcji, wybierając zakres udostępniony. Jednak do celów zarządzania lub budżetu kosztów, możesz przydzielić ilości do określonej subskrypcji.

 Można podzielić zastrzeżenia do dwóch zastrzeżenia jednak programu PowerShell, interfejsu wiersza polecenia, lub za pośrednictwem interfejsu API.

### <a name="split-a-reservation-by-using-powershell"></a>Dzielenie zastrzeżenie przy użyciu programu PowerShell
1. Pobierz identyfikator zamówienia rezerwacji, uruchamiając następujące polecenie:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Pobieranie szczegółów zastrzeżenie:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Podziel zastrzeżenia do dwóch i dystrybucja wystąpień:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Można aktualizować zakresu, uruchamiając następujące polecenie:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać zastrzeżenie
Możesz delegować Zarządzanie zastrzeżenie przez dodanie użytkowników do ról na rezerwację. Domyślnie osoba, która zakupione zastrzeżenia i administrator konta ma rolę właściciela na rezerwację. 

Aby można zarządzać dostępem do zastrzeżenia niezależnie z subskrypcji, które uzyskać rabat rezerwacji. Gdy przyznawanie uprawnień do zarządzania zastrzeżenie innym, które nie powodują ich prawa do zarządzania subskrypcją. I przyznaje uprawnienia do zarządzania subskrypcją, w zakresie rezerwacji osoby, które nie powodują je prawa do zarządzania rezerwacji.
 
Aby delegować zarządzanie dostępem dla zastrzeżenie: 
1.  Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2.  Wybierz **wszystkie usługi** > **rezerwacji** do rezerwacji listy, które mają dostęp do.
3.  Wybierz rezerwacji, który chcesz delegować dostęp do innych użytkowników.
4.  Wybierz **kontroli dostępu (IAM)** w menu.
5.  Wybierz **Dodaj** > **roli** > **właściciela** (lub inną rolę, jeśli chcesz przyznać ograniczony dostęp). 
6. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela. 
7. Wybierz użytkownika, a następnie wybierz **zapisać**.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o zastrzeżonych wystąpień maszyn wirtualnych, zobacz następujące artykuły.

- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyny Wirtualnej](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zaoszczędzić na maszynach wirtualnych z zarezerwowanych wystąpień maszyn wirtualnych](billing-save-compute-costs-reservations.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienie maszyny wirtualnej](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
