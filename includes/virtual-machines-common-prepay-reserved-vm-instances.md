---
ms.topic: include
ms.openlocfilehash: b41b93db63038645257bc348d5a027fbc4f7fdc9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyny Wirtualnej

Przedpłaty dla maszyn wirtualnych i tańszą z zarezerwowanych wystąpień maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [oferty zarezerwowane wystąpień maszyn wirtualnych](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Zarezerwowane wystąpień maszyn wirtualnych można kupić [portalu Azure](https://portal.azure.com). Aby kupić zastrzeżone wystąpienie maszyny wirtualnej:
-   Musisz być w roli właściciela co najmniej jednej organizacji lub z subskrypcji.
-   Dla subskrypcji Enterprise zakupy rezerwacji musi być włączony w [EA portal](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Kup wystąpienia zastrzeżonej maszyny wirtualnej
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie usługi** > **zastrzeżenia**.
3. Wybierz **Dodaj** kupić nowe zastrzeżenie.
4. Wypełnij wymagane pola. Maszyn wirtualnych uruchomionych wystąpień zgodnych wybrane atrybuty zakwalifikować uzyskać rabat rezerwacji. Rzeczywista liczba wystąpień maszyn wirtualnych, które zawierają rabat są zależne od zakresu i ilość wybrana.

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego zastrzeżenia.| 
    |Subskrypcja|Z subskrypcją użytą do opłacać rezerwacji. Formy płatności subskrypcji jest pobierana góry koszty rezerwacji. Typ subskrypcji musi być umowy enterprise agreement (numer oferty: MS-AZR - 0017P) lub płatność za rzeczywiste użycie (numer oferty: MS-AZR - 0003P). Dla subskrypcji enterprise opłaty są odjąć od salda zobowiązania pieniężnego rejestracji lub rozliczany jako nadwyżkowe. Płatność za rzeczywiste użycie subskrypcji opłaty są rozliczane do metody płatności kartą kredytową lub faktury dla tej subskrypcji.|    
    |Zakres       |Zakresu zastrzeżenia mogą obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania: <ul><li>Jedną subskrypcją - rabat rezerwacji jest stosowany do maszyn wirtualnych w tej subskrypcji. </li><li>Shared - rabat rezerwacji jest stosowany do maszyn wirtualnych uruchomionych w żadnych subskrypcji w ramach kontekstu rozliczeń. Dla przedsiębiorstw zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem subskrypcje tworzenie/testowanie oprogramowania) w ramach rejestracji. W przypadku klientów płatność za rzeczywiste użycie zakres udostępniony jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzone przez administratora konta.</li></ul>|
    |Lokalizacja    |Region platformy Azure jest objęta rezerwacji.|    
    |Rozmiar maszyny wirtualnej     |Liczba wystąpień maszyn wirtualnych.|
    |Termin        |Rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach rezerwacji. Ilość jest liczba uruchomionych wystąpień maszyn wirtualnych, które można uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 Standard_D2 maszyn wirtualnych w wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |
5. Po wybraniu można wyświetlić koszt zastrzeżenia **obliczenia kosztu**.

    ![Zrzut ekranu przed przesłaniem rezerwacji zakupu](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Wybierz **zakupu**.
7. Wybierz **wyświetlić tego zastrzeżenia** aby zobaczyć stan zakupu.

    ![Zrzut ekranu przed przesłaniem rezerwacji zakupu](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Kolejne kroki 
Zniżki rezerwacji jest stosowana automatycznie do liczba uruchomionych maszyn wirtualnych, które pasują do zakresu zastrzeżenia i atrybutów. Można zaktualizować zakresu zastrzeżenia za pośrednictwem [portalu Azure](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia lub za pośrednictwem interfejsu API. 

Aby dowiedzieć się, jak zarządzać zastrzeżenie, zobacz [zarządzania Azure zastrzeżone wystąpień maszyn wirtualnych](../articles/billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej o zastrzeżonych wystąpień maszyn wirtualnych, zobacz następujące artykuły.

- [Zaoszczędzić na maszynach wirtualnych z zarezerwowanych wystąpień maszyn wirtualnych](../articles/billing/billing-save-compute-costs-reservations.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienie maszyny wirtualnej](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](../articles/billing/billing-reserved-instance-windows-software-costs.md)