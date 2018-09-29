---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09-05-2018
ms.openlocfilehash: 065ac0855fc47b23b434287f9f4406bd641f01ae
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454465"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure

Przedpłaty dotyczące maszyn wirtualnych i Oszczędzaj pieniądze dzięki wystąpień zarezerwowanych maszyn wirtualnych (maszyny Wirtualnej platformy Azure). Aby uzyskać więcej informacji, zobacz [oferty Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Rezerwacja wystąpienia maszyny Wirtualnej można kupić [witryny Azure portal](https://portal.azure.com). Aby kupić wystąpienie:

- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise, musi być włączony zakupu rezerwacji [portalu EA portal](https://ea.azure.com).
- Program Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży mogą kupować rezerwacje.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Określić właściwy rozmiar maszyny Wirtualnej przed zakupem

Podkategoria miernika i produktu pola danych użycia nie rozróżnia rozmiarów maszyn wirtualnych, korzystających z usługi premium storage z maszyn wirtualnych, które nie. Jeśli używasz tych pól do ustalenia rozmiaru maszyny Wirtualnej do użycia dla rezerwacji, może kupić niewłaściwy rozmiar i nie będą otrzymywać rabat związany z rezerwacją, których oczekujesz. Aby określić właściwy rozmiar maszyny Wirtualnej, w przypadku dokonywania zakupu rezerwacji, użyj jednej z następujących metod:

- Zapoznaj się z polem AdditionalInfo w pliku użycia lub interfejs API użycia, aby określić odpowiedni rozmiar maszyny Wirtualnej. Nie używaj wartości z pól Podkategoria licznika lub produkt. Te pola nie odróżnić S i Non-S wersje maszyny Wirtualnej.
- Uzyskaj dokładne informacje rozmiar maszyny Wirtualnej przy użyciu programu Powershell, usługi Azure Resource Manager, lub z maszyny Wirtualnej szczegółowych informacji w witrynie Azure portal.

Zarezerwowane wystąpienia maszyn wirtualnych są dostępne dla większości rozmiarów maszyn wirtualnych z pewnymi wyjątkami:

- Rabat związany z rezerwacją nie ma zastosowania dla następujących maszyn wirtualnych:
  - Klasyczne maszyny wirtualne i usługi w chmurze
  - Rozmiary ograniczone procesorów wirtualnych
  - Serię maszyn wirtualnych: A-series serii Av2 i serii G
  - Maszyny wirtualne w wersji zapoznawczej: serię maszyn wirtualnych ani rozmiaru, który jest w wersji zapoznawczej
- Chmury: Rezerwacji nie są dostępne do zakupu w regionach platformy Azure instytucji rządowych USA, Niemcy i Chiny.
- Za mały limit przydziału: rezerwacji, które są ograniczone do pojedynczej subskrypcji musi mieć dostępnego w ramach subskrypcji dla nowych wystąpień Zarezerwowanych limitu przydziału procesorów wirtualnych. Na przykład jeśli docelowa subskrypcja ma limit przydziału procesorów wirtualnych 10 dla serii D, następnie możesz dokonać zakupu rezerwacji 11 wystąpień maszyna wirtualna Standard_D1. Sprawdzenie limitu przydziału dla rezerwacji obejmuje maszyny wirtualne wdrożone w ramach subskrypcji. Na przykład jeśli subskrypcja ma limit przydziału procesorów wirtualnych 10 dla serii D, ma dwa wystąpienia maszyna wirtualna standard_D1 wdrożeniu można kupować Rezerwacja 10 wystąpień maszyna wirtualna standard_D1 w ramach tej subskrypcji. 
- Ograniczenia dotyczące pojemności: W rzadkich przypadkach limitów zakupu nowej rezerwacji dla podzbioru rozmiarów maszyn wirtualnych z powodu niskiej wydajności w regionie platformy Azure.

## <a name="buy-a-reserved-vm-instance"></a>Kup wystąpienia zarezerwowane maszyn wirtualnych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz **Dodaj** kupić nową rezerwację.
4. Wypełnij wymagane pola. Uruchomionej maszyny Wirtualnej wystąpienia, które pasują do atrybutów, którą wybierzesz kwalifikują się uzyskać rabat związany z rezerwacją. Rzeczywista liczba wystąpień maszyn wirtualnych, które Rabat zależy od tego, zakresu i ilości wybranej.

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego zastrzeżenia.| 
    |Subskrypcja|Subskrypcja użytej do zapłacenia za rezerwację. Metodę płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwacji. Wymagany typ subskrypcji to Enterprise Agreement (numer oferty MS-AZR-0017P) lub Płatność zgodnie z rzeczywistym użyciem (numer oferty MS-AZR-0003P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|    
    |Zakres       |Zakres rezerwacji może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <ul><li>Subskrypcja pojedyncza — rabat związany z rezerwacją jest stosowane do maszyn wirtualnych w ramach tej subskrypcji. </li><li>W warstwie współdzielona — rabat związany z rezerwacją jest stosowane do maszyn wirtualnych działających w dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem tworzenia i testowania subskrypcje) w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.</li></ul>|
    |Region    |Region platformy Azure, który pasuje do żadnego rezerwacji.|    
    |Rozmiar maszyny wirtualnej     |Rozmiar wystąpienia maszyny Wirtualnej.|
    |Optymalizuj dla     |Elastyczność rozmiaru wystąpienia maszyny Wirtualnej ma zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tym samym [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Priorytet pojemności priorytet pojemności centrum danych wdrożeń. Zapewnia to dodatkową pewność co do możliwości uruchomienia wystąpienia maszyny Wirtualnej, gdy ich potrzebujesz. Priorytet pojemności jest dostępna tylko w przypadku, gdy zakresem rezerwacji jest pojedyncza subskrypcja. |
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach rezerwacji. Ilość jest liczby uruchomionych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 maszyna wirtualna Standard_D2 maszyn wirtualnych w regionie wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |
5. Można wyświetlić kosztów rezerwacji, po wybraniu **obliczyć koszt**.

    ![Zrzut ekranu, przed przesłaniem zakupu rezerwacji](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Wybierz pozycję **Kup**.
7. Wybierz **Wyświetl tę rezerwację** Aby wyświetlić stan zakupu.

    ![Zrzut ekranu po przesłaniu zakupu rezerwacji](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

Jeśli potrzebujesz anulować swoje zastrzeżenia, może to być 12% zakończenia opłatę za wcześniejsze zakończenie. Zwroty zależą od najniższej ceny zakupu cen lub bieżąca cena zastrzeżenia. Zwroty są ograniczone do 50 000 USD rocznie. Zwrot kosztów, które otrzymujesz jest pozostałe saldo proporcjonalnie pomniejszona o 12% zakończenia opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w witrynie Azure portal i wybierz pozycję **zwrot** do utworzenia żądania obsługi.

Jeśli musisz zmienić rezerwacji wystąpienia zarezerwowane maszyn wirtualnych do innego regionu grupie rozmiarów maszyny Wirtualnej i termin może wymieniać go do innej rezerwacji, która jest większa lub równa wartości. Data rozpoczęcia okresu dla nowej rezerwacji nie jest przenoszone z wymiana rezerwacji. Wartość 1 lub 3 lat. rozpoczyna się podczas tworzenia nowej rezerwacji. Aby zażądać programu exchange, przejdź do rezerwacji w witrynie Azure portal i wybierz **Exchange** do utworzenia żądania obsługi.

## <a name="next-steps"></a>Kolejne kroki

Rabat związany z rezerwacją jest automatycznie stosowany do liczba uruchomionych maszyn wirtualnych, które są zgodne z zakresem rezerwacji i atrybutów. Można zaktualizować zakresu rezerwacji za pośrednictwem [witryny Azure portal](https://portal.azure.com), PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API.

Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [Zarządzanie zastrzeżeniami Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie zastrzeżeniami na platformie Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
