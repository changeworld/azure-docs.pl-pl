---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371664"
---
Po zatwierdzeniu do wystąpienia zarezerwowanej maszyny Wirtualnej platformy Azure można zaoszczędzić pieniądze. Rabat rezerwacji jest stosowany automatycznie do liczby uruchomionych maszyn wirtualnych, które pasują do zakresu rezerwacji i atrybutów. Nie trzeba przypisywać rezerwacji do maszyny wirtualnej, aby uzyskać rabaty. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia maszyny Wirtualnej. W przypadku maszyn wirtualnych z systemem Windows miernik użycia jest podzielony na dwa oddzielne liczniki. Jest miernik obliczeniowy, który jest taki sam jak miernik Linuksa, i miernik IP systemu Windows. Opłaty widoczne podczas dokonywania zakupu są tylko dla kosztów obliczeń. Opłaty nie obejmują kosztów oprogramowania systemu Windows. Aby uzyskać więcej informacji na temat kosztów oprogramowania, zobacz [Koszty oprogramowania nieuwzględniane z wystąpieniami zarezerwowanych maszyn wirtualnych platformy Azure](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Ustalanie odpowiedniego rozmiaru maszyny wirtualnej przed zakupem

Przed zakupem rezerwacji należy określić rozmiar maszyny Wirtualnej, która jest potrzebna. Poniższe sekcje pomogą Ci określić odpowiedni rozmiar maszyny Wirtualnej.

### <a name="use-reservation-recommendations"></a>Korzystanie z zaleceń dotyczących rezerwacji

Możesz użyć zaleceń dotyczących rezerwacji, aby ustalić rezerwacje, które należy zakupić.

- Zalecenia dotyczące zakupu i zalecana ilość są wyświetlane podczas zakupu wystąpienia zarezerwowanego maszyny Wirtualnej w witrynie Azure portal.
- Usługa Azure Advisor udostępnia zalecenia dotyczące zakupu dla poszczególnych subskrypcji.  
- Za pomocą interfejsów API można uzyskać zalecenia dotyczące zakupu zarówno dla zakresu udostępnionego, jak i zakresu pojedynczej subskrypcji. Aby uzyskać więcej informacji, zobacz [Interfejsy API rekomendacji zakupu wystąpienia zarezerwowanego dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- W przypadku klientów z umową Enterprise Agreement (EA) i microsoft customer agreement (MCA) zalecenia dotyczące zakupu dla zakresów subskrypcji udostępnionych i pojedynczych są dostępne w [pakiecie zawartości usługi Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Usługi, dla których są stosowane rabaty na rezerwacje maszyn wirtualnych

Rezerwacje maszyn wirtualnych mogą dotyczyć użycia maszyn wirtualnych emitowanego przez różne usługi — nie tylko w przypadku wdrożeń maszyn wirtualnych. Zasoby, dla których są stosowane rabaty na rezerwację, zmieniają się w zależności od ustawienia elastyczności rozmiaru wystąpienia.

#### <a name="instance-size-flexibility-setting"></a>Ustawienie elastyczności rozmiaru wystąpienia

Ustawienie elastyczności rozmiaru wystąpienia określa, dla których usług są stosowane rabaty na wystąpienia zarezerwowane.

Niezależnie od tego, czy to ustawienie jest włączone czy wyłączone, rabaty na rezerwację są automatycznie stosowane do każdego pasującego użycia maszyn wirtualnych, dla którego wartość *ConsumedService* to `Microsoft.Compute`. Należy zatem sprawdzić dane użycia dla wartości *ConsumedService*. Oto niektóre przykłady:

- Maszyny wirtualne
- Zestawy skalowania maszyn wirtualnych
- Usługa kontenera
- Wdrożenia usługi Azure Batch (w trybie subskrypcji użytkownika)
- Azure Kubernetes Service (AKS)
- Service Fabric

Jeśli to ustawienie jest włączone, rabaty na rezerwację są automatycznie stosowane do pasującego użycia maszyn wirtualnych, które ma dowolną z następujących wartości *ConsumedService*:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Sprawdź wartość *ConsumedService* w danych użycia, aby ustalić, czy użycie kwalifikuje się do zastosowania rabatów na rezerwację.

Aby uzyskać więcej informacji o elastyczności rozmiaru wystąpienia, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analizowanie informacji o użytkowaniu

Analizuj informacje o użytkowaniu, aby określić, które rezerwacje należy kupić. Dane użycia są dostępne w pliku użycia i interfejsach API. Użyj ich razem, aby określić, która rezerwacja ma być zakupiona. Sprawdź, czy wystąpienia maszyn wirtualnych, które mają wysokie użycie na co dzień, aby określić ilość rezerwacji do zakupu. Unikaj `Meter` podkategorii i `Product` pól w danych użycia. Nie rozróżniają rozmiarów maszyn wirtualnych, które używają magazynu w wersji premium. Jeśli używasz tych pól do określenia rozmiaru maszyny Wirtualnej dla zakupu rezerwacji, możesz kupić niewłaściwy rozmiar. Wtedy nie otrzymasz rabatu na rezerwację, którego oczekujesz. Zamiast tego należy `AdditionalInfo` zapoznać się z polem w pliku użycia lub interfejsie API użycia, aby określić poprawny rozmiar maszyny Wirtualnej.

Plik użycia pokazuje opłaty według okresu rozliczeniowego i codziennego użytkowania. Aby uzyskać informacje dotyczące pobierania pliku użycia, zobacz [Wyświetlanie i pobieranie użycia i opłat na platformie Azure.](../articles/cost-management-billing/understand/download-azure-daily-usage.md) Następnie, korzystając z informacji o pliku użycia, można [określić, jaką rezerwację kupić](../articles/cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Uwagi dotyczące ograniczeń zakupu

Zarezerwowane wystąpienia maszyn wirtualnych są dostępne dla większości rozmiarów maszyn wirtualnych z pewnymi wyjątkami. Rabaty rezerwacyjne nie dotyczą następujących maszyn wirtualnych:

- **Seria VM** - seria A, Seria Av2 lub Seria G.

- **Podgląd lub promo maszyny wirtualne** — dowolna seria maszyn wirtualnych lub rozmiar, który znajduje się w wersji zapoznawczej lub używa miernika promocyjnego.

- **Chmury** — rezerwacje nie są dostępne w niemczech i chinach.

- **Za mało przydziału** — rezerwacja, która jest ograniczona do pojedynczej subskrypcji musi mieć przydziału procesora wirtualnego dostępne w subskrypcji dla nowej ri. Na przykład jeśli subskrypcja docelowa ma limit przydziału 10 procesorów wirtualnych dla serii D, nie można kupić rezerwacji dla 11 Standard_D1 wystąpień. Sprawdzanie przydziału dla rezerwacji obejmuje maszyny wirtualne już wdrożone w ramach subskrypcji. Na przykład jeśli subskrypcja ma przydział 10 procesorów wirtualnych dla serii D i ma dwa standard_D1 wystąpień wdrożonych, można kupić rezerwację dla 10 standard_D1 wystąpień w tej subskrypcji. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia oferty.](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)

- **Ograniczenia pojemności** — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla podzbiorów rozmiarów maszyn wirtualnych ze względu na małą pojemność w regionie.

## <a name="buy-a-reserved-vm-instance"></a>Kupowanie wystąpienia zarezerwowanego maszyny wirtualnej

Zarezerwowane wystąpienie maszyny Wirtualnej można kupić w [witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Te wymagania dotyczą zakupu zarezerwowanego wystąpienia maszyny Wirtualnej:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji ze stawką płatności zgodnie z rzeczywistymu po stronie.
- W przypadku subskrypcji EA opcja **Dodaj wystąpienia zarezerwowane** musi być włączona w portalu [EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.
- W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować rezerwacje.

Aby kupić instancję:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz **pozycję Rezerwacje wszystkich usług** > **Reservations**.
1. Wybierz **pozycję Dodaj,** aby kupić nową rezerwację, a następnie kliknij pozycję **Maszyna wirtualna**.
1. Podaj wartości w wymaganych polach. Uruchomione wystąpienia maszyn wirtualnych pasujące do wybranych atrybutów kwalifikują się do uzyskania rabatu rezerwacji. Rzeczywista liczba wystąpień maszyn wirtualnych objętych rabatem zależy od wybranego zakresu i wybranej ilości.

Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej,** aby szybko dodać dodatkowe wystąpienia. Opcja nie jest dostępna dla innych typów subskrypcji.


| Pole      | Opis|
|------------|--------------|
|Subskrypcja|Subskrypcja używana do opłacenia rezerwacji. W przypadku subskrypcji pobierana jest opłata za rezerwację. Typ subskrypcji musi być umową enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub Umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawkami płatności zgodnie z rzeczywistymi przynastwem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odejmowane od salda zobowiązań pieniężnych, jeśli są dostępne, lub naliczane jako nadawolne. W przypadku subskrypcji ze stawkami płatności zgodnie z rzeczywistym obciążeniem opłaty są naliczane na kartę kredytową lub metodę płatności faktury w ramach subskrypcji.|    
|Zakres       |Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres współużytkowy). W przypadku wybrania: <ul><li>**Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.</li><li>**Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.</li><li>**Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. Dla klientów EA kontekstem rozliczeń jest rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>|
|Region    |Region platformy Azure, który jest objęty rezerwacją.|    
|Rozmiar maszyny wirtualnej     |Rozmiar wystąpień maszyny Wirtualnej.|
|Optymalizacja pod kątem     |Domyślnie wybierana jest elastyczność rozmiaru wystąpienia maszyny Wirtualnej. Kliknij **pozycję Ustawienia zaawansowane,** aby zmienić wartość elastyczności rozmiaru wystąpienia, aby zastosować rabat rezerwacji do innych maszyn wirtualnych z tej samej [grupy rozmiarów maszyn wirtualnych](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Priorytet pojemności powoduje przydzielanie priorytetów pojemności centrum danych dla wdrożeń. Zapewnia dodatkowe zaufanie do możliwości uruchamiania wystąpień maszyny Wirtualnej, gdy ich potrzebujesz. Priorytet pojemności jest dostępny tylko wtedy, gdy zakres rezerwacji jest pojedyncza subskrypcja. |
|Termin        |Rok lub trzy lata.|
|Liczba    |Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeniowy. Na przykład jeśli używasz 10 Standard_D2 maszyn wirtualnych we wschodnich stanach ZJEDNOCZONYCH, należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich uruchomionych maszyn wirtualnych. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dane użytkowania i wykorzystanie rezerwacji

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Można zobaczyć, które wystąpienie maszyny Wirtualnej otrzymał rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu, w jaki rabaty rezerwacji są wyświetlane w danych użycia, zobacz [Opis użycia rezerwacji platformy Azure dla rejestracji enterprise,](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) jeśli jesteś klientem EA. Jeśli masz indywidualną subskrypcję, zobacz [Opis użycia rezerwacji platformy Azure dla subskrypcji płatności zgodnie z rzeczywistym użyciem.](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Zmiana rezerwacji po zakupie

W zakupionej rezerwacji można wprowadzać następujące zmiany:

- Aktualizacja zakresu rezerwacji
- Elastyczność rozmiaru wystąpienia (jeśli dotyczy)
- Własność

Można również podzielić rezerwację na mniejsze fragmenty i scalić już podzielone rezerwacje. Żadna ze zmian nie powoduje nowej transakcji handlowej ani nie zmienia daty zakończenia rezerwacji.

Nie można bezpośrednio wprowadzić następujących typów zmian po zakupie:

- Region istniejącej rezerwacji
- SKU
- Liczba
- Czas trwania

Jednak można *wymienić* rezerwację, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz [Zarządzanie rejestracjami platformy Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:
    - [Co to są rezerwacje platformy Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Zarządzanie rezerwacjami na platformie Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Omówienie stosowania rabatu na rezerwacje](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows nie są wliczone w rezerwacje](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
