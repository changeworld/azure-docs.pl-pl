---
ms.topic: include
ms.openlocfilehash: 99eaa667e4c6a9d63b4cc43ada8c6e36f7365610
ms.sourcegitcommit: 39f4911b5933f7062dcf5d57af94eab8a0740b2b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2018
ms.locfileid: "35683053"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyna wirtualna platformy Azure

Przedpłaty dla maszyn wirtualnych i tańszą wystąpieniami Azure zastrzeżone maszyn wirtualnych (VM). Aby uzyskać więcej informacji, zobacz [oferty Azure zastrzeżone wystąpień](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Zarezerwowane wystąpień Azure można kupić [portalu Azure](https://portal.azure.com). Aby kupić zastrzeżone wystąpienie:
-   Musisz być w roli właściciela co najmniej jednej organizacji lub z subskrypcji.
-   Dla subskrypcji Enterprise zakupy zastrzeżone wystąpienie musi być włączony w [EA portal](https://ea.azure.com).
-   Dla programu Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży można kupić zastrzeżone wystąpień.

[!IMPORTANT]
Należy użyć jednej z metod opisanych poniżej, aby zidentyfikować poprawnie rozmiar maszyny Wirtualnej dla zakupu rezerwacji.

## <a name="determine-the-right-vm-size-before-purchase"></a>Określić właściwego rozmiaru maszyny Wirtualnej przed zakupu
1. Odwołuje się do pola części informacje dodatkowe aby w pliku użycia lub użycia interfejsu API, aby określić odpowiedni rozmiar maszyny Wirtualnej dla zakupu rezerwacji. Nie należy używać wartości pól podkategorii miernika lub produktu, ponieważ te pola nie rozróżnianie między wersjami S i Non-S maszyny wirtualnej.
2. Można także uzyskać dokładne informacje rozmiar maszyny Wirtualnej przy użyciu programu Powershell, usługi Azure Resource Manager lub z maszyny Wirtualnej szczegółów w portalu Azure.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Kup wystąpienia zastrzeżonej maszyny wirtualnej
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie usługi** > **zastrzeżenia**.
3. Wybierz **Dodaj** zakupu nowego wystąpienia zastrzeżone.
4. Wypełnij wymagane pola. Uruchomionej maszyny Wirtualnej wystąpienia zgodnych wybrane atrybuty zakwalifikować uzyskać rabat wystąpienia zastrzeżone. Rzeczywista liczba wystąpień maszyn wirtualnych, które zawierają rabat są zależne od zakresu i ilość wybrana.

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego wystąpienia zastrzeżone.| 
    |Subskrypcja|Z subskrypcją użytą do płatności dla zastrzeżonego wystąpienia. Formy płatności subskrypcji jest pobierana góry koszty dla tego wystąpienia zastrzeżone. Typ subskrypcji musi być umowy enterprise agreement (numer oferty: MS-AZR - 0017P) lub płatność za rzeczywiste użycie (numer oferty: MS-AZR - 0003P). Dla subskrypcji enterprise opłaty są odjąć od salda zobowiązania pieniężnego rejestracji lub rozliczany jako nadwyżkowe. Płatność za rzeczywiste użycie subskrypcji opłaty są rozliczane do metody płatności kartą kredytową lub faktury dla tej subskrypcji.|    
    |Zakres       |Wystąpienia zastrzeżony zakres może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). W przypadku wybrania: <ul><li>Jedną subskrypcją - rabat zastrzeżone wystąpienia jest stosowany do maszyn wirtualnych w tej subskrypcji. </li><li>Shared - rabat zastrzeżone wystąpienia są stosowane do maszyn wirtualnych uruchomionych w żadnych subskrypcji w ramach kontekstu rozliczeń. Dla przedsiębiorstw zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem subskrypcje tworzenie/testowanie oprogramowania) w ramach rejestracji. W przypadku klientów płatność za rzeczywiste użycie zakres udostępniony jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzone przez administratora konta.</li></ul>|
    |Lokalizacja    |Region platformy Azure jest objęta zastrzeżone wystąpienia.|    
    |Rozmiar maszyny wirtualnej     |Liczba wystąpień maszyn wirtualnych.|
    |Termin        |Rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach wystąpienia zastrzeżone. Ilość jest liczba uruchomionych wystąpień maszyn wirtualnych, które można uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 Standard_D2 maszyn wirtualnych w wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |
5. Można wyświetlić koszt zastrzeżone wystąpienia, po wybraniu **obliczenia kosztu**.

    ![Zrzut ekranu przed przesłaniem zakupu wystąpienia zastrzeżone](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Wybierz **zakupu**.
7. Wybierz **wyświetlić tego zastrzeżenia** aby zobaczyć stan zakupu.

    ![Zrzut ekranu po przesłaniu zakupu wystąpienia zastrzeżone](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Kolejne kroki 
Zniżki zastrzeżone wystąpienia stosowany jest automatycznie liczba uruchomionych maszyn wirtualnych, które pasują do wystąpienia zastrzeżony zakres i atrybutów. Można aktualizować zakres zarezerwowanych wystąpienie za pośrednictwem [portalu Azure](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia lub za pośrednictwem interfejsu API. 

Aby dowiedzieć się, jak zarządzać zastrzeżone wystąpienia, zobacz [Zarządzanie wystąpień zastrzeżone Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej o wystąpieniach zastrzeżone Azure, zobacz następujące artykuły:

- [Oszczędzić pieniądze na maszynach wirtualnych z wystąpieniami zastrzeżone](../articles/billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie wystąpieniami zastrzeżone Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Zarezerwowane wystąpień w programie partnera Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.