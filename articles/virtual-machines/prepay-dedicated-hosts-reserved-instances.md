---
title: Przedpłata za dedykowane hosty platformy Azure pozwala zaoszczędzić pieniądze
description: Dowiedz się, jak kupić dedykowane hosty platformy Azure wystąpienia zarezerwowane, aby zaoszczędzić na kosztach obliczeniowych.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207747"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Oszczędzaj koszty dzięki zarezerwowanemu wystąpieniu dedykowanych hostów platformy Azure

Po zatwierdzeniu do zarezerwowanego wystąpienia hostów dedykowanych platformy Azure można zaoszczędzić pieniądze. Rabat rezerwacji jest stosowany automatycznie do liczby uruchomionych dedykowanych hostów, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do dedykowanego hosta, aby uzyskać zniżki. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia i obejmuje koszty licencjonowania oprogramowania. Zobacz [omówienie dedykowanych hostów platformy Azure dla maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Przed zakupem określ właściwą dedykowaną jednostkę SKU hosta


Przed zakupem rezerwacji należy określić, którego dedykowanego hosta potrzebujesz. Jednostka SKU jest zdefiniowana dla dedykowanego hosta reprezentującego serię i typ maszyny Wirtualnej. 

Zacznij od przechodzenia przez obsługiwane rozmiary dla [maszyny wirtualnej systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) lub systemu [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aby zidentyfikować serię maszyn wirtualnych.

Następnie sprawdź, czy jest obsługiwany na platformie Azure Dedykowane hosty. [Strona cenowa Azure Dedicated Hosts](https://aka.ms/ADHPricing) zawiera pełną listę dedykowanych jednostek SKU hostów, ich informacje o procesorze i różne opcje cenowe (w tym wystąpienia zarezerwowane).

Może się okazać, że kilka jednostek SKU obsługujących serię maszyn wirtualnych (z różnymi typami). Zidentyfikuj najlepszą jednostkę SKU, porównując pojemność hosta (liczbę procesorów wirtualnych). Należy zauważyć, że można zastosować rezerwację do wielu dedykowanych jednostek SKU hostów obsługujących tę samą serię maszyn wirtualnych (na przykład DSv3_Type1 i DSv3_Type2), ale nie w różnych seriach maszyn wirtualnych (takich jak DSv3 i ESv3).



## <a name="purchase-restriction-considerations"></a>Uwagi dotyczące ograniczeń zakupu

Wystąpienia zarezerwowane są dostępne dla większości dedykowanych rozmiarów hosta, z pewnymi wyjątkami.

Zniżki na rezerwacje nie dotyczą następujących elementów:

- **Chmury** — rezerwacje nie są dostępne w niemczech i chinach.

- **Za mało przydziału** — rezerwacja, która jest ograniczona do pojedynczej subskrypcji musi mieć przydział vCPU dostępne w subskrypcji dla nowego wystąpienia zarezerwowanego. Na przykład jeśli subskrypcja docelowa ma limit przydziału 10 procesorów wirtualnych dla serii DSv3, nie można kupić dedykowanych hostów rezerwacji obsługujących tę serię. Sprawdzanie przydziału dla rezerwacji obejmuje maszyny wirtualne i dedykowane hosty już wdrożone w ramach subskrypcji. Aby rozwiązać ten problem, można utworzyć  [żądanie zwiększenia przydziału.](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)

- **Ograniczenia pojemności** — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla podzbiór dedykowanych jednostek SKU hosta ze względu na małą pojemność w regionie.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

W [witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)można kupić zarezerwowane wystąpienie wystąpienia hosta dedykowanego platformy Azure.

Zapłać za [rezerwację z góry lub z miesięcznymi płatnościami.](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations) Te wymagania dotyczą zakupu zarezerwowanego wystąpienia dedykowanego hosta:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji ze stawką płatności zgodnie z rzeczywistymu po stronie.

- W przypadku subskrypcji **Add Reserved Instances** EA opcja Dodaj wystąpienia zarezerwowane musi być włączona w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.

- W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować rezerwacje.

Aby kupić instancję:

1. Zaloguj się do witryny  [Azure Portal](https://portal.azure.com/).

2. Wybierz **pozycję Rezerwacje wszystkich usług** \> **Reservations**.

3. Wybierz **pozycję Dodaj,** aby kupić nową rezerwację, a następnie kliknij pozycję **Dedykowane hosty**.

4. Podaj wartości w wymaganych polach. Uruchamianie dedykowanych hostów wystąpień, które pasują do wybranych atrybutów, kwalifikuje się do uzyskania rabatu rezerwacji. Rzeczywista liczba wystąpień dedykowanego hosta, które otrzymują rabat, zależy od wybranego zakresu i ilości.

Jeśli masz umowę EA, możesz użyć **opcji** Dodaj więcej, aby szybko dodać dodatkowe wystąpienia. Opcja nie jest dostępna dla innych typów subskrypcji.

| **Pole**           | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subskrypcja        | Subskrypcja używana do opłacenia rezerwacji. W przypadku subskrypcji pobierana jest opłata za rezerwację. Typ subskrypcji musi być umową enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub Umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawkami płatności zgodnie z rzeczywistymi przynastwem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odejmowane od salda zobowiązań pieniężnych, jeśli są dostępne, lub naliczane jako nadawolne. W przypadku subskrypcji ze stawkami płatności zgodnie z rzeczywistym obciążeniem opłaty są naliczane na kartę kredytową lub metodę płatności faktury w ramach subskrypcji. |
| Zakres               | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres współużytkowy). W przypadku wybrania:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | Region platformy Azure, który jest objęty rezerwacją.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedykowany rozmiar hosta | Rozmiar wystąpienia hosta dedykowanego.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termin                | Rok lub trzy lata.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Liczba            | Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych wystąpień hosta dedykowanego, które mogą uzyskać rabat rozliczeniowy.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Zakres pojedynczej grupy zasobów** — stosuje rabat rezerwacji do pasujących zasobów tylko w wybranej grupie zasobów.

- **Zakres pojedynczej subskrypcji** — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.

- **Zakres udostępniony** — stosuje rabat rezerwacji do pasujących zasobów w kwalifikujących się subskrypcji, które znajdują się w kontekście rozliczeń. Dla klientów EA kontekstem rozliczeń jest rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.

## <a name="usage-data-and-reservation-utilization"></a>Dane użytkowania i wykorzystanie rezerwacji

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Można zobaczyć, które wystąpienie maszyny Wirtualnej otrzymał rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu, w jaki rabaty rezerwacji są wyświetlane w danych użycia, zobacz [Opis użycia rezerwacji platformy Azure dla rejestracji](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) enterprise, jeśli jesteś klientem EA. Jeśli masz indywidualną subskrypcję, zobacz [Opis użycia rezerwacji platformy Azure dla subskrypcji płatności zgodnie z rzeczywistym użyciem.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

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

Jednak można *wymienić* rezerwację, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz [Samoobsługowe wymiany i zwroty pieniędzy za rezerwacje platformy Azure.](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz prośbę o pomoc](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [Zarządzanie rezerwacjami platformy Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance).

Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Korzystanie z dedykowanych hostów platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Cennik dedykowanych hostów](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Zarządzanie rezerwacjami na platformie Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Omówienie stosowania rabatu na rezerwacje](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Koszty oprogramowania systemu Windows nie są wliczone w rezerwacje](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)


