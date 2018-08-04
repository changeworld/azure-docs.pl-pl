---
ms.topic: include
ms.openlocfilehash: c30dbe25252a18e1edaed5ec81d86cc1dd976250
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513883"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure

Przedpłaty dotyczące maszyn wirtualnych i Oszczędzaj pieniądze dzięki wystąpień zarezerwowanych maszyn wirtualnych (maszyny Wirtualnej platformy Azure). Aby uzyskać więcej informacji, zobacz [oferty Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Możesz kupić wystąpienia zarezerwowane platformy Azure [witryny Azure portal](https://portal.azure.com). Aby kupić wystąpienie zarezerwowane:
-   Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
-   W przypadku subskrypcji Enterprise, zakup wystąpienia zarezerwowanego musi być włączona w [portalu EA portal](https://ea.azure.com).
-   Dla programu Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży mogą kupować wystąpienia zarezerwowane.

## <a name="determine-the-right-vm-size-before-purchase"></a>Określić właściwy rozmiar maszyny Wirtualnej przed dokonaniem zakupu
Podkategoria miernika i produktu pola danych użycia nie rozróżnia rozmiarów maszyn wirtualnych, korzystających z usługi premium storage z rozmiarów maszyn wirtualnych, które nie używają usługi premium storage, przy użyciu tych pól, aby określić maszyna wirtualna rozmiar dla zakupu rezerwacji może spowodować niepoprawne Rezerwacja zakupu i nie zapewnia rabaty rezerwacji. Aby określić właściwy rozmiar maszyny Wirtualnej dla zakupu rezerwacji, użyj jednej z poniższych metod.
- Zapoznaj się AdditionalInfo > ServiceType pola w pliku użycia lub dane dotyczące użycia interfejsu API. W tym polu zapewni odpowiedni rozmiar maszyny Wirtualnej, podczas wdrażania maszyn wirtualnych używających magazynu w warstwie premium.
- Można również uzyskać dokładne informacje rozmiar maszyny Wirtualnej przy użyciu programu Powershell, usługi Azure Resource Manager lub z maszyny Wirtualnej z jego szczegóły w witrynie Azure portal.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Kup wystąpienia zarezerwowane maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz **Dodaj** do zakupu nowego wystąpienia zarezerwowanego.
4. Wypełnij wymagane pola. Uruchomionej maszyny Wirtualnej wystąpienia, które pasują do atrybutów, którą wybierzesz kwalifikują się uzyskać rabat w wysokości wystąpienia zarezerwowanego. Rzeczywista liczba wystąpień maszyn wirtualnych, które Rabat zależy od tego, zakresu i ilości wybranej.

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa wystąpienia zarezerwowanego.| 
    |Subskrypcja|Subskrypcja użytej do zapłacenia za wystąpienie zarezerwowane. Forma płatności subskrypcji jest obciążana z góry za wystąpienie zarezerwowane. Wymagany typ subskrypcji to Enterprise Agreement (numer oferty MS-AZR-0017P) lub Płatność zgodnie z rzeczywistym użyciem (numer oferty MS-AZR-0003P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|    
    |Zakres       |Zakres wystąpienia zarezerwowanego może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <ul><li>Pojedynczej subskrypcji — rabat wystąpienia zarezerwowanego jest stosowane do maszyn wirtualnych w ramach tej subskrypcji. </li><li>W warstwie współdzielona — dotyczy rabatu wystąpień zarezerwowanych maszyn wirtualnych działających w dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem tworzenia i testowania subskrypcje) w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.</li></ul>|
    |Lokalizacja    |Region platformy Azure, który pasuje do żadnego wystąpienia zarezerwowanego.|    
    |Rozmiar maszyny wirtualnej     |Rozmiar wystąpienia maszyny Wirtualnej.|
    |Optymalizuj dla     |Elastyczność rozmiaru wystąpienia maszyny Wirtualnej ma zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tym samym [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Priorytet pojemności rezerwuje możliwości centrów danych wdrożeń. Zapewnia to dodatkową pewność co do możliwości uruchomienia wystąpienia maszyny Wirtualnej, gdy ich potrzebujesz. Priorytet pojemności jest dostępna tylko w przypadku, gdy zakresem rezerwacji jest pojedyncza subskrypcja. |
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach wystąpienia zarezerwowanego. Ilość jest liczby uruchomionych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 maszyna wirtualna Standard_D2 maszyn wirtualnych w regionie wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |
5. Koszt wystąpienia zarezerwowanego można wyświetlić po wybraniu **obliczyć koszt**.

    ![Zrzut ekranu, przed przesłaniem zakupu wystąpień zarezerwowanych](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Wybierz pozycję **Kup**.
7. Wybierz **Wyświetl tę rezerwację** Aby wyświetlić stan zakupu.

    ![Zrzut ekranu po przesłaniu zakupu wystąpień zarezerwowanych](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Kolejne kroki 
Rabat w wysokości wystąpienia zarezerwowanego jest automatycznie stosowany do liczba uruchomionych maszyn wirtualnych, które odpowiadają zakres wystąpienia zarezerwowanego i atrybutów. Można zaktualizować zakresu wystąpień zarezerwowanych za pośrednictwem [witryny Azure portal](https://portal.azure.com), PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API. 

Aby dowiedzieć się, jak zarządzać wystąpienie zarezerwowane, zobacz [Zarządzanie wystąpień zarezerwowanych na platformie Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat wystąpień zarezerwowanych platformy Azure, zobacz następujące artykuły:

- [Co to jest Azure Reserved VM Instances?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie wystąpień zarezerwowanych na platformie Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Understand how the reserved instance discount is applied (Sposób udzielania rabatu za wystąpienia zarezerwowane)](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Understand reserved instance usage for your Pay-As-You-Go subscription (Sposób rozliczania użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Understand reserved instance usage for your Enterprise enrollment (Sposób rozliczania użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Windows software costs not included with reserved instances (Nieuwzględnianie kosztów oprogramowania systemu Windows w przypadku wystąpień zarezerwowanych)](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Reserved instances in Partner Center Cloud Solution Provider (CSP) program (Wystąpienia zarezerwowane w programie Cloud Solution Provider w Centrum partnerskim)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
