---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/13/2019
ms.openlocfilehash: d9b9aae8bea323e5aac74a2e317b82d4cb43568f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406884"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure

Przedpłaty dotyczące maszyn wirtualnych i Oszczędzaj pieniądze dzięki wystąpień zarezerwowanych maszyn wirtualnych (maszyny Wirtualnej platformy Azure). Aby uzyskać więcej informacji, zobacz [oferty Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Rezerwacja wystąpienia maszyny Wirtualnej można kupić [witryny Azure portal](https://portal.azure.com). Aby kupić wystąpienie:

- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** musi być włączona w [portalu EA portal](https://ea.azure.com). Lub, jeśli to ustawienie jest wyłączone, musi być administratorem subskrypcji umowy EA.
- Program Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży mogą kupować rezerwacje.

Rabat związany z rezerwacją jest automatycznie stosowany do liczba uruchomionych maszyn wirtualnych, które są zgodne z zakresem rezerwacji i atrybutów. Można zaktualizować zakresu rezerwacji za pośrednictwem [witryny Azure portal](https://portal.azure.com), PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Określić właściwy rozmiar maszyny Wirtualnej przed zakupem

Przed zakupem rezerwacji należy określić rozmiar maszyny Wirtualnej, które są potrzebne. Poniższe sekcje pomoże określić właściwy rozmiar maszyny Wirtualnej.

### <a name="use-reservation-recommendations"></a>Zalecenia dotyczące używania rezerwacji

Rezerwacja zaleceń można użyć w celu określenia rezerwacji, które należy kupić.

- Pokaż zalecenia zakupu oraz zalecaną ilość podczas zakupu wystąpienia zarezerwowanego maszyny Wirtualnej w witrynie Azure portal.
- Usługa Azure Advisor zawiera zalecenia zakupu dla poszczególnych subskrypcji.  
- Aby uzyskać zalecenia zakupu z zakresem pojedynczej subskrypcji i zakresie udostępnionym, można użyć interfejsów API. Aby uzyskać więcej informacji, zobacz [zastrzeżone zalecenia zakupu wystąpień interfejsów API dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Dla klientów EA zalecenia zakupu współużytkowanych i zakresy pojedynczej subskrypcji są dostępne w usłudze [pakietu zawartości usługi Power BI Insights zużycie Azure](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Klasyczne maszyny wirtualne i usługi w chmurze

Wystąpienia zarezerwowane maszyny wirtualnej automatycznie dotyczą zarówno klasycznych maszyn wirtualnych i usług w chmurze po włączeniu elastyczność rozmiaru wystąpienia. Nie ma żadnych specjalnych jednostki SKU dla klasycznych maszyn wirtualnych ani usług w chmurze. To samo jednostek SKU maszyn wirtualnych się do nich.

Na przykład może przekonwertować klasycznych maszyn wirtualnych ani usług w chmurze, do maszyn wirtualnych opartych na usłudze Azure Resource Manager. W tym przykładzie rabat związany z rezerwacją jest automatycznie stosuje do pasujących maszyn wirtualnych. Nie ma potrzeby do *exchange* istniejące wystąpienie zarezerwowane — automatycznie stosuje.

### <a name="analyze-your-usage-information"></a>Analizowanie danych użycia
Powinieneś przeanalizować informacji o sposobie w celu określenia rezerwacji, które należy kupić.

Dane użycia są dostępne w pliku użycia i interfejsów API. Użyć ich razem do określenia które rezerwacji do zakupu. Należy sprawdzić dla wystąpień maszyn wirtualnych, które mają wysokie użycie codziennie, aby określić ilość rezerwacji do zakupu.

Należy unikać `Meter` podkategorii i `Product` pól danych użycia. One nie rozróżnia rozmiarów maszyn wirtualnych, korzystających z usługi premium storage. Jeśli używasz tych pól do ustalenia rozmiaru maszyny Wirtualnej dla zakupu rezerwacji, może kupić niewłaściwy rozmiar. Następnie nie będą otrzymywać rabat związany z rezerwacją, których oczekujesz. Zamiast tego należy odwoływać się do `AdditionalInfo` pola w pliku użycia lub interfejs API użycia, aby określić odpowiedni rozmiar maszyny Wirtualnej.

### <a name="purchase-restriction-considerations"></a>Zagadnienia dotyczące ograniczeń zakupu

Zarezerwowane wystąpienia maszyn wirtualnych są dostępne dla większości rozmiarów maszyn wirtualnych z pewnymi wyjątkami. Rabaty w ramach rezerwacji nie mają zastosowania w przypadku następujących maszyn wirtualnych:

- **Maszyna wirtualna serii** -A-series serii Av2 i serii G.

- **Maszyny wirtualne w wersji zapoznawczej** -serię maszyn wirtualnych ani rozmiaru, który jest w wersji zapoznawczej.

- **Chmury** -rezerwacji nie są dostępne do zakupu w regionach Niemcy i Chiny.

- **Za mały limit przydziału** -rezerwacji, które są ograniczone do pojedynczej subskrypcji musi mieć dostępnego w ramach subskrypcji dla nowych wystąpień Zarezerwowanych limitu przydziału procesorów wirtualnych. Na przykład jeśli docelowa subskrypcja ma limit przydziału procesorów wirtualnych 10 dla serii D, następnie możesz dokonać zakupu rezerwacji 11 wystąpień maszyna wirtualna Standard_D1. Sprawdzenie limitu przydziału dla rezerwacji obejmuje maszyny wirtualne wdrożone w ramach subskrypcji. Na przykład jeśli subskrypcja ma limit przydziału procesorów wirtualnych 10 dla serii D, ma dwa wystąpienia maszyna wirtualna standard_D1 wdrożeniu można kupować Rezerwacja 10 wystąpień maszyna wirtualna standard_D1 w ramach tej subskrypcji. Możesz [Tworzenie oferty żądań zwiększenia](../articles/azure-supportability/resource-manager-core-quotas-request.md) Aby rozwiązać ten problem.

- **Ograniczenia dotyczące pojemności** — w rzadkich przypadkach, limity platformy Azure po zakupie nowej rezerwacji dla podzbioru maszyn wirtualnych o rozmiarach, z powodu niskiej wydajności w regionie.

## <a name="buy-a-reserved-vm-instance"></a>Kup wystąpienia zarezerwowane maszyn wirtualnych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz **Dodaj** kupić nową rezerwację.
4. Wypełnij wymagane pola. Uruchomionej maszyny Wirtualnej wystąpienia, które pasują do atrybutów, którą wybierzesz kwalifikują się uzyskać rabat związany z rezerwacją. Rzeczywista liczba wystąpień maszyn wirtualnych, które Rabat zależy od tego, zakresu i ilości wybranej.

    | Pole      | Opis|
    |------------|--------------|
    |Name (Nazwa)        |Nazwa tego zastrzeżenia.|
    |Subskrypcja|Subskrypcja użytej do zapłacenia za rezerwację. Metodę płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwacji. Typ subskrypcji musi być umowy enterprise agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P) lub płatność za rzeczywiste użycie (oferują liczb: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|    
    |Zakres       |Zakres rezerwacji może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <ul><li>Subskrypcja pojedyncza — rabat związany z rezerwacją jest stosowane do maszyn wirtualnych w ramach tej subskrypcji. </li><li>W warstwie współdzielona — rabat związany z rezerwacją jest stosowane do maszyn wirtualnych działających w dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.</li></ul>|
    |Region    |Region platformy Azure, który pasuje do żadnego rezerwacji.|    
    |Rozmiar maszyny wirtualnej     |Rozmiar wystąpienia maszyny Wirtualnej.|
    |Optymalizuj dla     |Elastyczność rozmiaru wystąpienia maszyny Wirtualnej ma zastosowanie rabatu związanego z rezerwacją do innych maszyn wirtualnych w tym samym [grupie rozmiarów maszyn wirtualnych](https://aka.ms/RIVMGroups). Priorytet pojemności priorytet pojemności centrum danych wdrożeń. Zapewnia to dodatkową pewność co do możliwości uruchomienia wystąpienia maszyny Wirtualnej, gdy ich potrzebujesz. Priorytet pojemności jest dostępna tylko w przypadku, gdy zakresem rezerwacji jest pojedyncza subskrypcja. |
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach rezerwacji. Ilość jest liczby uruchomionych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 maszyna wirtualna Standard_D2 maszyn wirtualnych w regionie wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Zmieniać po zakupie rezerwacji

Możesz wprowadzić następujące typy zmian po zakupie rezerwacji:

- Zaktualizować zakresu rezerwacji
- Elastyczność rozmiar wystąpienia (jeśli dotyczy)
- Własność

Możesz również podzielić rezerwacji w mniejszych fragmentach i scalania już podziału rezerwacji. Zmiany powodują nowej transakcji handlowych, lub zmienić datę zakończenia rezerwacji.

Po zakupie bezpośrednio, nie można wprowadzić następujące typy zmian:

- Zastrzeżenie istniejącego regionu
- SKU
- Ilość
- Czas trwania

Można jednak *exchange* rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

Jeśli chcesz anulować rezerwację, może zostać naliczona opłata w wysokości 12% za wcześniejsze zakończenie. Zwroty zależą od niższej kwoty z dwóch: cena zakupu i bieżąca cena rezerwacji. Zwroty są ograniczone do 50 000 USD rocznie. Otrzymany zwrot to pozostałe, obliczone proporcjonalnie saldo pomniejszone o 12% opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w witrynie Azure portal i wybierz pozycję **zwrot** do utworzenia żądania obsługi.

Jeśli musisz zmienić rezerwację wystąpień zarezerwowanych maszyn wirtualnych na inny region, grupę rozmiarów maszyny wirtualnej lub okres obowiązywania, możesz wymienić ją na inną rezerwację o takiej samej lub większej wartości. Data rozpoczęcia okresu nowej rezerwacji nie jest przenoszona z wymienionej rezerwacji. Termin 1 i 3-letnie rozpoczyna się podczas tworzenia nowej rezerwacji. Aby zażądać programu exchange, przejdź do rezerwacji w witrynie Azure portal i wybierz **Exchange** do utworzenia żądania obsługi.

Aby uzyskać więcej informacji na temat rezerwacji exchange lub refundacji zobacz [wymiany rezerwacji i zwroty](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zarządzać rezerwacji, zobacz [Zarządzanie zastrzeżeniami Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:
    - [Co to jest Azure rezerwacje?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Zarządzanie zastrzeżeniami na platformie Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
