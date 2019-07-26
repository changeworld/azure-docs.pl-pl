---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/19/2019
ms.openlocfilehash: 763d424d9d462c4a9531df84f3e5e26bfc1b0a14
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68502314"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Przedpłata za Virtual Machines z Azure Reserved VM Instances (RI)

Przedpłata za maszyny wirtualne i oszczędność pieniędzy za pomocą wystąpień zarezerwowanych maszyn wirtualnych platformy Azure. Rabat rezerwacji jest automatycznie stosowany do liczby uruchomionych maszyn wirtualnych, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do maszyny wirtualnej, aby uzyskać rabaty. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia maszyny wirtualnej. W przypadku maszyn wirtualnych z systemem Windows licznik użycia jest podzielony na dwa oddzielne liczniki. Istnieje licznik obliczeniowy, który jest taki sam jak licznik systemu Linux i miernik adresów IP w systemie Windows. Opłaty, które zobaczysz po dokonaniu zakupu, dotyczą tylko kosztów obliczeń. Opłaty nie obejmują kosztów oprogramowania systemu Windows. Aby uzyskać więcej informacji o kosztach oprogramowania, zobacz [koszty oprogramowania nieuwzględnione w Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Ustalanie odpowiedniego rozmiaru maszyny wirtualnej przed zakupem

Przed zakupieniem rezerwacji należy określić wymagany rozmiar maszyny wirtualnej. Poniższe sekcje ułatwią określenie odpowiedniego rozmiaru maszyny wirtualnej.

### <a name="use-reservation-recommendations"></a>Korzystanie z rekomendacji dotyczących rezerwacji

Możesz użyć zaleceń dotyczących rezerwacji, aby pomóc w ustaleniu zastrzeżeń, które należy zakupić.

- Zalecenia dotyczące zakupu i zalecana ilość są wyświetlane podczas zakupu wystąpienia zarezerwowanego maszyny wirtualnej w Azure Portal.
- Azure Advisor zapewnia rekomendacje zakupu dla poszczególnych subskrypcji.  
- Możesz użyć interfejsów API, aby uzyskać zalecenia dotyczące zakupu zarówno dla zakresu udostępnionego, jak i dla jednej subskrypcji. Aby uzyskać więcej informacji, zobacz [interfejsy API rekomendacji wystąpień zarezerwowanych dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- W przypadku klientów z Umowa Enterprise (EA) zalecenia dotyczące zakupu dla udostępnionych i pojedynczych zakresów subskrypcji są dostępne za pomocą [pakietu zawartości Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Usługi pobierające rabaty rezerwacji maszyn wirtualnych

Rezerwacje maszyn wirtualnych mogą dotyczyć użycia maszyn wirtualnych emitowanych z wielu usług — nie tylko dla wdrożeń maszyn wirtualnych. Zasoby pobierające rabaty rezerwacji zmieniają się w zależności od ustawienia elastyczności rozmiaru wystąpienia.

#### <a name="instance-size-flexibility-setting"></a>Ustawienie elastyczności rozmiaru wystąpienia

Ustawienie elastyczność rozmiaru wystąpienia określa, które usługi pobierają rabaty wystąpień zarezerwowanych.

Niezależnie od tego, czy to ustawienie jest włączone, czy wyłączone, rabaty rezerwacji są `Microsoft.Compute`automatycznie stosowane do wszystkich pasujących użycia maszyn wirtualnych, gdy *ConsumedService* . Sprawdź dane użycia dla wartości *ConsumedService* . Oto niektóre przykłady:

- Maszyny wirtualne
- Zestawy skalowania maszyn wirtualnych
- Usługa kontenera
- Wdrożenia Azure Batch (w trybie subskrypcji użytkownika)
- Azure Kubernetes Service (AKS)
- Service Fabric

Gdy to ustawienie jest włączone, rabaty rezerwacji automatycznie stosują się do dopasowywania użycia maszyn wirtualnych, gdy *ConsumedService* jest dowolnym z następujących elementów:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Sprawdź wartość *ConsumedService* w danych użycia, aby określić, czy użycie kwalifikuje się do rabatu rezerwacji.

Aby uzyskać więcej informacji o elastyczności rozmiaru wystąpienia, zobacz [elastyczność rozmiaru maszyny wirtualnej z wystąpieniami zarezerwowanymi maszyn wirtualnych](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analizowanie informacji o użyciu
Analizuj informacje o użyciu, aby pomóc w ustaleniu, które rezerwacje należy zakupić.

Dane użycia są dostępne w pliku użycia i interfejsach API. Należy używać ich razem w celu określenia rezerwacji do zakupu. Sprawdź wystąpienia maszyn wirtualnych o wysokim poziomie użycia codziennie, aby określić liczbę rezerwacji do zakupu.

Należy unikać podkategorii i `Product` pól w danych użycia. `Meter` Nie rozróżniają one rozmiarów maszyn wirtualnych korzystających z magazynu Premium Storage. Jeśli te pola są używane do określenia rozmiaru maszyny wirtualnej dla zakupu rezerwacji, można zakupić niewłaściwy rozmiar. Nie zostanie wyświetlony oczekiwany rabat rezerwacji. Zamiast tego zapoznaj się `AdditionalInfo` z polem w pliku użycia lub interfejsie API użycia, aby określić prawidłowy rozmiar maszyny wirtualnej.

### <a name="purchase-restriction-considerations"></a>Zagadnienia związane z ograniczeniami zakupu

Wystąpienia zarezerwowane maszyn wirtualnych są dostępne w przypadku większości niektórych wyjątków maszyn wirtualnych. Rabaty zarezerwowane nie dotyczą następujących maszyn wirtualnych:

- Seria **maszyn wirtualnych** — seria A, Av2 lub seria G.

- **Wersja zapoznawcza lub promocja maszyn wirtualnych** — wszystkie maszyny wirtualne lub rozmiary, które są w wersji zapoznawczej lub wykorzystują miernik promocyjny.

- **Chmury** — rezerwacje nie są dostępne do zakupu w regionach Niemcy i Chiny.

- **Niewystarczające limity przydziału** — rezerwacja w zakresie jednej subskrypcji musi mieć przydziały vCPU dostępne w subskrypcji dla nowego wystąpienia RI. Jeśli na przykład subskrypcja docelowa ma limit przydziału równy 10 procesorów wirtualnych vCPU dla serii D, nie można zakupić rezerwacji dla 11 wystąpień Standard_D1. Sprawdzanie przydziału dla rezerwacji obejmuje maszyny wirtualne już wdrożone w subskrypcji. Jeśli na przykład subskrypcja ma przydział 10 procesorów wirtualnych vCPU dla serii D i ma wdrożone dwa wystąpienia usługi standard_D1, można kupić rezerwację dla 10 wystąpień standard_D1 w tej subskrypcji. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia oferty](../articles/azure-supportability/resource-manager-core-quotas-request.md) .

- **Ograniczenia pojemności** — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla podzbioru rozmiarów maszyn wirtualnych z powodu niskiej wydajności w regionie.

## <a name="buy-a-reserved-vm-instance"></a>Kupowanie zarezerwowanych wystąpień maszyn wirtualnych

Możesz kupić wystąpienie zarezerwowane maszyny wirtualnej w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Te wymagania dotyczą kupowania zarezerwowanych wystąpień maszyn wirtualnych:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane** w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, musisz być administratorem EA dla subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować rezerwacje.

Aby kupić wystąpienie:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie** > **rezerwacje**usług.
1. Wybierz pozycję **Dodaj** , aby zakupić nową rezerwację, a następnie kliknij pozycję **maszyna wirtualna**.
1. Wprowadź wymagane pola. Uruchomione wystąpienia maszyn wirtualnych, które pasują do wybranych atrybutów, kwalifikują się do uzyskania rabatu rezerwacji. Rzeczywista liczba wystąpień maszyn wirtualnych, które pobiera rabat, zależy od wybranego zakresu i ilości.

| Pole      | Opis|
|------------|--------------|
|Subscription|Subskrypcja używana do płacenia za rezerwację. W przypadku metody płatności w ramach subskrypcji jest naliczana opłata za pozostały koszt. Typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.|    
|Scope       |Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: <ul><li>**Zakres pojedynczej grupy zasobów** — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.</li><li>**Zakres pojedynczej subskrypcji** — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.</li><li>**Zakres udostępniony** — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji z płatnością zgodnie z rzeczywistym użyciem zakres rozliczeń to wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>|
|Region    |Region świadczenia usługi Azure objęty rezerwacją.|    
|Rozmiar maszyny wirtualnej     |Rozmiar wystąpień maszyn wirtualnych.|
|Optymalizuj dla     |Domyślnie wybrana jest elastyczność rozmiaru wystąpienia maszyny wirtualnej. Kliknij pozycję **Ustawienia zaawansowane** , aby zmienić elastyczność rozmiaru wystąpienia, aby zastosować rabat rezerwacji do innych maszyn wirtualnych w tej samej [grupie rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Priorytet pojemności umożliwia określenie priorytetu wydajności centrum danych dla wdrożeń. Oferuje ona dodatkowe zaufanie do uruchamiania wystąpień maszyn wirtualnych, gdy ich potrzebujesz. Priorytet pojemności jest dostępny tylko wtedy, gdy zakresem rezerwacji jest pojedyncza subskrypcja. |
|Termin        |Jeden rok lub trzy lata.|
|Ilość    |Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 Standard_D2 maszyn wirtualnych w regionie Wschodnie stany USA, możesz określić liczbę jako 10, aby zmaksymalizować korzyść dla wszystkich uruchomionych maszyn wirtualnych. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dane użycia i użycie rezerwacji

Dane użycia mają rzeczywistą cenę równą zero za użycie, które pobiera rabat rezerwacji. Można sprawdzić, które wystąpienie maszyny wirtualnej otrzymało rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu wyświetlania rabatów rezerwacji w danych użycia, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji](../articles/billing/billing-understand-reserved-instance-usage-ea.md) w przedsiębiorstwie, jeśli jesteś klientem z umową EA. Jeśli masz pojedynczą subskrypcję, zobacz [Opis użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Zmiana rezerwacji po zakupie

Następujące typy zmian w rezerwacji można wprowadzać po zakupie:

- Aktualizuj zakres rezerwacji
- Elastyczność rozmiaru wystąpienia (jeśli dotyczy)
- Wyniku

Można również podzielić rezerwację na mniejsze fragmenty i scalić już rezerwacje. Żadna ze zmian nie powoduje nowej transakcji komercyjnej lub zmiana daty zakończenia rezerwacji.

Po zakupie nie można wprowadzać następujących typów zmian bezpośrednio:

- Istniejący region rezerwacji
- SKU
- Ilość
- Duration

Istnieje jednak możliwość *wymiany* rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancellations-and-exchanges"></a>Anulowania i wymiany

Jeśli chcesz anulować rezerwację, może zostać naliczona opłata w wysokości 12% za wcześniejsze zakończenie. Zwroty zależą od niższej kwoty z dwóch: cena zakupu i bieżąca cena rezerwacji. Zwroty są ograniczone do 50 000 USD rocznie. Otrzymany zwrot to pozostałe, obliczone proporcjonalnie saldo pomniejszone o 12% opłatę za wcześniejsze zakończenie. Aby anulować, przejdź do rezerwacji w Azure Portal i wybierz pozycję **zwrot**.

Jeśli zachodzi potrzeba zmiany rezerwacji wystąpień zarezerwowanych maszyn wirtualnych na inny region, grupę rozmiarów maszyny wirtualnej lub termin, można wymienić ją na inne. Program Exchange musi być dla innej rezerwacji o wartości równej lub wyższej. Data rozpoczęcia okresu nowej rezerwacji nie jest przenoszona z wymienionej rezerwacji. Okres jednego lub trzech lat zaczyna się od momentu utworzenia nowej rezerwacji. W programie Exchange przejdź do rezerwacji w Azure Portal i wybierz pozycję **Exchange**.

Aby uzyskać więcej informacji na temat sposobu wymiany lub zastrzeżeń, zobacz temat [rezerwacje rezerwacji i](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)zwroty.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [zarządzanie Azure Reservations](../articles/billing/billing-manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:
    - [Co to są Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Zarządzanie rezerwacjami na platformie Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Zapoznaj się z zastosowaniem rabatu rezerwacji](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Poznaj użycie rezerwacji dla subskrypcji z użyciem stawek płatność zgodnie z rzeczywistym zużyciem](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations w programie Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
