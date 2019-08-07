---
title: Zarządzaj Azure Reservations
description: Dowiedz się, jak zarządzać Azure Reservations.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840040"
---
# <a name="manage-reservations-for-azure-resources"></a>Zarządzanie rezerwacjami zasobów platformy Azure

Po zakupieniu rezerwacji na platformę Azure może być konieczne zastosowanie rezerwacji do innej subskrypcji, zmianę, kto może zarządzać rezerwacją, lub zmianę zakresu rezerwacji. Można również podzielić rezerwację na dwie rezerwacje, aby zastosować niektóre wystąpienia zakupione w innej subskrypcji.

Jeśli zakupiono Azure Reserved Virtual Machine Instances, możesz zmienić ustawienie optymalizacji dla rezerwacji. Rabat rezerwacji może być stosowany do maszyn wirtualnych w tej samej serii lub można zarezerwować pojemność centrum danych dla określonego rozmiaru maszyny wirtualnej. I należy próbować zoptymalizować rezerwacje, aby były w pełni używane.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Rezerwacja i rezerwacja

Podczas zakupu rezerwacji tworzone są dwa obiekty: Rezerwacja i **rezerwacja**.

W momencie zakupu zamówienie rezerwacji ma jedną rezerwację. Akcje takie jak Split, Merge, częściowe refund lub Exchange tworzą nowe rezerwacje w ramach **zamówienia rezerwacji**.

Aby wyświetlić kolejność rezerwacji, przejdź do pozycji **rezerwacje** > Wybierz rezerwację, a następnie kliknij **Identyfikator zamówienia rezerwacji**.

![Przykład szczegółów zamówienia rezerwacji z IDENTYFIKATORem zamówienia rezerwacji ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Rezerwacja dziedziczy uprawnienia z ich kolejności rezerwacji.

## <a name="change-the-reservation-scope"></a>Zmień zakres rezerwacji

 Rabat związany z rezerwacją dotyczy maszyn wirtualnych, baz danych SQL, Azure Cosmos DB lub innych zasobów, które pasują do rezerwacji i działają w zakresie rezerwacji. Kontekst rozliczania zależy od subskrypcji użytej do zakupu rezerwacji.

Aby zaktualizować zakres rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie** > **rezerwacje**usług.
3. Wybierz rezerwację.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień zakres.

W przypadku zmiany z zakresu udostępnionego na pojedynczy należy wybrać tylko subskrypcje, w których jesteś właścicielem. Wybrać można tylko subskrypcje w ramach tego samego kontekstu rozliczeń, co rezerwacja.

Zakres dotyczy tylko indywidualnych subskrypcji z stawem płatność zgodnie z rzeczywistym użyciem (oferuje firmę MS-AZR-0003P lub MS-AZR-0023P), oferuje Enterprise oferty MS-AZR-0017P lub MS-AZR-0148P lub dostawcy usług kryptograficznych.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Dodawanie lub zmienianie użytkowników, którzy mogą zarządzać rezerwacją

Zarządzanie rezerwacjami można delegować, dodając osoby do ról w zamówieniu rezerwacji lub rezerwacji. Domyślnie osoba, która umieszcza zamówienie rezerwacji i administrator konta, ma rolę właściciela w zamówieniu rezerwacji i rezerwacji.

Możesz zarządzać dostępem do rezerwacji zamówień i rezerwacji niezależnie od subskrypcji, które pobierają rabat rezerwacji. Przyznanie komuś uprawnień do zarządzania kolejnością rezerwacji lub rezerwacją nie daje im uprawnień do zarządzania subskrypcją. Podobnie, jeśli przyznasz komuś uprawnienia do zarządzania subskrypcją w zakresie rezerwacji, nie nadajesz im praw do zarządzania zamówieniem rezerwacji lub rezerwacją.

Aby dokonać wymiany lub zwrotu pieniędzy, użytkownik musi mieć dostęp do zamówienia rezerwacji. Podczas udzielania uprawnień komuś najlepiej jest przyznać uprawnienia do zamówienia rezerwacji, a nie do rezerwacji.


Aby delegować zarządzanie dostępem do rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi** > **rezerwacja** , aby wyświetlić listę rezerwacji, do których masz dostęp.
3. Wybierz rezerwację, do której chcesz delegować dostęp innym użytkownikom.
4. Wybierz **kontrola dostępu (IAM)** .
5. Wybierz pozycję Dodaj**właściciela** **roli** >  **przypisania** > roli. Ewentualnie, jeśli chcesz nadać ograniczony dostęp, wybierz inną rolę.
6. Wpisz adres e-mail użytkownika, który ma zostać dodany jako właściciel.
7. Wybierz użytkownika, a następnie wybierz polecenie **Zapisz**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dzielenie pojedynczej rezerwacji na dwie rezerwacje

 Po zakupieniu więcej niż jednego wystąpienia zasobu w ramach rezerwacji możesz chcieć przypisać wystąpienia w ramach tej rezerwacji do różnych subskrypcji. Domyślnie wszystkie wystąpienia mają jeden zakres — jedną subskrypcję lub udostępnioną. Na przykład zakupiono 10 wystąpień rezerwacji i określono zakres do subskrypcji A. Możesz teraz zmienić zakres siedmiu rezerwacji na subskrypcję a i pozostałe trzy w subskrypcję B. dzielenie rezerwacji pozwala na dystrybuowanie wystąpień do szczegółowego zarządzania zakresem. Można uprościć alokację do subskrypcji, wybierając zakres udostępniony. Jednak w celu zarządzania kosztami lub budżetowania można przydzielić ilości do określonych subskrypcji.

 Rezerwację można podzielić na dwie rezerwacje, chociaż program PowerShell, interfejs wiersza polecenia lub interfejs API.

### <a name="split-a-reservation-by-using-powershell"></a>Dzielenie rezerwacji przy użyciu programu PowerShell

1. Uzyskaj identyfikator zamówienia rezerwacji, uruchamiając następujące polecenie:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Zapoznaj się ze szczegółami zastrzeżenia:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Podziel rezerwację na dwa i Dystrybuuj wystąpienia:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Aby zaktualizować zakres, należy uruchomić następujące polecenie:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zastrzeżenia zwrotne

Można anulować, wymienić lub refundować rezerwacje z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Zmień ustawienie optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych

 Podczas zakupu wystąpienia zarezerwowanego maszyny wirtualnej wybiera się elastyczność rozmiaru wystąpienia lub priorytet pojemności. Elastyczność rozmiaru wystąpienia stosuje rabat rezerwacji do innych maszyn wirtualnych w tej samej [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Priorytet pojemności umożliwia określenie priorytetu wydajności centrum danych dla wdrożeń. Ta opcja zapewnia dodatkowe zaufanie do uruchamiania wystąpień maszyn wirtualnych, gdy ich potrzebujesz.

Domyślnie, gdy zakres rezerwacji jest współużytkowany, elastyczność rozmiaru wystąpienia jest włączona. W przypadku wdrożeń maszyn wirtualnych nie ma priorytetu wydajności centrum danych.

W przypadku rezerwacji, w których zakres jest pojedynczy, można zoptymalizować rezerwację dla priorytetu pojemności zamiast elastyczności rozmiaru wystąpienia maszyny wirtualnej.

Aby zaktualizować ustawienia optymalizacji dla rezerwacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie** > **rezerwacje**usług.
3. Wybierz rezerwację.
4. Wybierz pozycję **Ustawienia** > **Konfiguracja**.
5. Zmień ustawienie **Optymalizuj dla** .

## <a name="optimize-reservation-use"></a>Optymalizuj użycie rezerwacji

Oszczędności rezerwacji platformy Azure wynikają jedynie z nietrwałego użycia zasobów. Po dokonaniu zakupu rezerwacji opłata jest obciążana kosztami z góry, co jest zasadniczo 100% możliwe użycie zasobów w okresie jednego lub trzech lat. Staraj się zmaksymalizować rezerwację, aby uzyskać jak najwięcej użycia i oszczędności. W poniższych sekcjach wyjaśniono, jak monitorować rezerwację i zoptymalizować jej użycie.

### <a name="view-reservation-use-in-the-azure-portal"></a>Wyświetl użycie rezerwacji w Azure Portal

Jeden ze sposobów wyświetlania użycia rezerwacji znajduje się w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję Wszystkie[**rezerwacje**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) **usług** > i zanotuj **użycie (%)** dla rezerwacji.  
  ![Obraz przedstawiający listę zastrzeżeń](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Wybierz rezerwację.
4. Przejrzyj trend użycia w czasie.  
  ![Obraz przedstawiający użycie rezerwacji ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Wyświetl użycie rezerwacji z interfejsem API

Jeśli jesteś klientem z Umowa Enterprise (EA), możesz programowo zobaczyć, jak są używane rezerwacje w organizacji. Nieużywane rezerwacje są uzyskiwane za poorednictwem danych użycia. Podczas przeglądania opłat za rezerwację należy pamiętać, że dane są dzielone między kosztami rzeczywistymi i amortyzowanymi. Rzeczywisty koszt zapewnia dane umożliwiające uzgadnianie miesięcznego rachunku. Zawiera również informacje o kosztach zakupu rezerwacji i aplikacji rezerwacji. Amortyzowany koszt jest podobny do rzeczywistego kosztu, z tą różnicą, że efektywna cena użycia rezerwacji jest naliczana proporcjonalnie. Niewykorzystane godziny rezerwacji są wyświetlane w danych kosztów z amortyzacją. Aby uzyskać więcej informacji o danych użycia dla klientów z umowami EA, zobacz [pobieranie Umowa Enterprise koszty rezerwacji i użycie](billing-understand-reserved-instance-usage-ea.md).

W przypadku innych typów subskrypcji Użyj [listy podsumowania rezerwacji interfejsu API — lista według kolejności rezerwacji i rezerwacji](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optymalizowanie rezerwacji

Jeśli okaże się, że rezerwacje organizacji są nieużywane:

- Upewnij się, że maszyny wirtualne tworzone przez organizację są zgodne z rozmiarem maszyny wirtualnej dla rezerwacji.
- Upewnij się, że rozmiar wystąpienia jest elastyczny. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami — Zmień ustawienie optymalizacji dla wystąpień zarezerwowanych maszyn wirtualnych](#change-optimize-setting-for-reserved-vm-instances).
- Zmień zakres rezerwacji na współużytkowane , aby zapewnić szersze zastosowanie. Aby uzyskać więcej informacji, zobacz [zmiana zakresu rezerwacji](#change-the-reservation-scope).
- Rozważ wymianę niewykorzystanej ilości. Aby uzyskać więcej informacji, zobacz [anulowania i wymiany](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:

- [Co to są rezerwacje dla systemu Azure?](billing-save-compute-costs-reservations.md)

Kup plan usługi:
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Przedpłata za zasoby Azure Cosmos DB z Azure Cosmos DB zarezerwowaną pojemnością](../cosmos-db/cosmos-db-reserved-capacity.md)

Kup plan oprogramowania:
- [Przedpłata za plany oprogramowania Red Hat z Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prepay for SUSE software plans from Azure Reservations (Opłacanie planów oprogramowania SUSE z góry z poziomu usługi Azure Reservations)](../virtual-machines/linux/prepay-suse-software-charges.md)

Informacje o rabacie i użyciu:
- [Zapoznaj się z zastosowaniem rabatu rezerwacji maszyny wirtualnej](billing-understand-vm-reservation-charges.md)
- [Informacje o sposobie stosowania rabatu planu oprogramowania Red Hat Enterprise Linux](../billing/billing-understand-rhel-reservation-charges.md)
- [Zapoznaj się z zastosowaniem rabatu planu oprogramowania w systemie SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Zapoznaj się z zastosowaniami innych rabatów rezerwacji](billing-understand-reservation-charges.md)
- [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](billing-reserved-instance-windows-software-costs.md)
