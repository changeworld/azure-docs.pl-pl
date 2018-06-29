---
title: Zarządzanie wystąpieniami zastrzeżone maszyna wirtualna platformy Azure | Dokumentacja firmy Microsoft
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
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064010"
---
# <a name="manage-reserved-instances-in-azure"></a>Zarządzanie wystąpieniami zarezerwowane na platformie Azure

Po kupić zastrzeżone wystąpienia maszyny Wirtualnej Azure, może chcesz zastosować wystąpienia zastrzeżone do innej subskrypcji niż określona podczas zakupów. Alternatywnie pasującego maszyny wirtualne są uruchomione w ramach wielu subskrypcji, możesz zmienić zakres zarezerwowanych wystąpienie do udostępnionego. Aby zmaksymalizować rabat zastrzeżone wystąpienia, upewnij się, czy liczba wystąpień zakupiono zgodny z atrybutów i liczbę maszyn wirtualnych, które masz uruchomiony. Aby dowiedzieć się więcej o wystąpieniach zastrzeżone platformy Azure, zobacz [zaoszczędzić na wstępnie płatności maszyn wirtualnych platformy Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Zmień zakres dla wystąpienia zastrzeżone
 Twoja zniżka zastrzeżone wystąpienie ma zastosowanie do maszyn wirtualnych, które odpowiada zastrzeżone wystąpienia i uruchomić w zakresie zastrzeżonych wystąpienia. Zakres zarezerwowanych wystąpienie może być jedną subskrypcją lub wszystkie subskrypcje w kontekst rozliczeń. Jeśli ustawisz zakres do jednej subskrypcji zastrzeżonego wystąpienia jest dopasowywany do maszyn wirtualnych działających w wybranej subskrypcji. Jeśli zakres do platformy Azure, współdzielonych zgodna zastrzeżone wystąpienie do maszyn wirtualnych uruchomionych w ramach wszystkich subskrypcji w kontekście rozliczeń. Kontekst rozliczeń jest zależna od z subskrypcją użytą do kupić zastrzeżone wystąpienia. Aby dowiedzieć się więcej, zobacz [wstępne płatności dla maszyn wirtualnych z wystąpieniami zastrzeżone](https://go.microsoft.com/fwlink/?linkid=861721).

Aby zaktualizować zakres zarezerwowanych wystąpienie: 
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie usługi** > **zastrzeżenia**.
3. Wybierz wystąpienie zastrzeżone.
4. Wybierz **ustawienia** > **konfiguracji**.
5. Zmienić zakres. W przypadku zmiany z udostępnionego na pojedynczy zakres, można wybrać tylko te subskrypcje, w której jesteś właścicielem. Można wybrać tylko subskrypcji w tym samym kontekście rozliczeń jak zastrzeżone wystąpienie. Kontekst rozliczeń zależy od wybranej podczas zastrzeżone wystąpienia został zakupiony subskrypcji. Zakres ma zastosowanie tylko do oferty płatności obejmujące MS-AZR - 0003P subskrypcji i subskrypcje oferta MS-AZR - 0017P przedsiębiorstwa. Dla umowy enterprise i testowania subskrypcji nie są objęte uzyskać rabat zastrzeżone wystąpienia.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Podziel pojedynczego wystąpienia zarezerwowane do dwóch wystąpień zastrzeżone
 Po zakupie więcej niż jedno wystąpienie można przypisać wystąpień w wystąpieniu zarezerwowane do różnych subskrypcji. Domyślnie wszystkie wystąpienia (określone podczas zakupu ilość) mają jeden zakres - albo jedną subskrypcją lub udostępnionego. Na przykład zakupione 10 standardowe maszyn wirtualnych D2 i określony zakres być subskrypcji A. Można teraz zmienić zakres siedmiu wystąpień zarezerwowane do subskrypcji A i pozostałych 3 do subskrypcji B. dzielenia zastrzeżone wystąpienia umożliwia dystrybucję wystąpień szczegółowego zakresu zarządzania. Można uprościć alokacji do subskrypcji, wybierając zakres udostępniony. Jednak do celów zarządzania lub budżetu kosztów, możesz przydzielić ilości do określonej subskrypcji.

 Można podzielić zastrzeżone wystąpienie do dwóch wystąpień zastrzeżone jednak programu PowerShell, interfejsu wiersza polecenia, lub za pośrednictwem interfejsu API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Podziel zastrzeżone wystąpienia przy użyciu programu PowerShell
1. Pobierz identyfikator zamówienia zastrzeżone wystąpienia, uruchamiając następujące polecenie:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Pobieranie szczegółów zastrzeżone wystąpienie:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Podziel zastrzeżone wystąpienia na dwie i dystrybucja wystąpień:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Można aktualizować zakresu, uruchamiając następujące polecenie:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać zastrzeżone wystąpienia
Możesz delegować Zarządzanie zastrzeżone wystąpienia przez dodanie użytkowników do ról w wystąpieniu zastrzeżone. Domyślnie osoby, które zakupione zastrzeżone wystąpienia, a administrator konta ma rolę właściciela wystąpieniu zastrzeżone. 

Można zarządzać dostęp do wystąpień zastrzeżone niezależnie z subskrypcji, które uzyskać rabat zastrzeżone wystąpienia. Gdy przyznawanie uprawnień do zarządzania zastrzeżone wystąpienia innym, które nie powodują je prawa do zarządzania subskrypcją. I przyznaje uprawnienia do zarządzania subskrypcją, w zakresie zastrzeżonych wystąpienia osoby, które nie powodują je prawa do zarządzania zastrzeżone wystąpienia.
 
Aby delegować zarządzanie dostępem dla zastrzeżonego wystąpienie: 
1.  Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2.  Wybierz **wszystkie usługi** > **rezerwacji** na listę zastrzeżonych wystąpień, które mają dostęp do.
3.  Wybierz wystąpienie zastrzeżone, który chcesz delegować dostęp do innych użytkowników.
4.  Wybierz **kontroli dostępu (IAM)** w menu.
5.  Wybierz **Dodaj** > **roli** > **właściciela** (lub inną rolę, jeśli chcesz przyznać ograniczony dostęp). 
6. Wpisz adres e-mail użytkownika, którego chcesz dodać jako właściciela. 
7. Wybierz użytkownika, a następnie wybierz **zapisać**.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o wystąpieniach zastrzeżone platformy Azure, zobacz następujące artykuły:

- [Co to są zastrzeżone wystąpień maszyn wirtualnych Azure?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyna wirtualna platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
