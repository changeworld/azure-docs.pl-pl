---
title: Co to jest Azure Reservations?
description: Dowiedz się więcej o Azure Reservations i cenach do zapisania na maszynach wirtualnych, bazach danych SQL, Azure Cosmos DB i innych kosztach zasobów.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: bb90a9dec161746356b8c13df448718c53626684
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806346"
---
# <a name="what-are-azure-reservations"></a>Co to jest Azure Reservations?

Azure Reservations pomóc w zaoszczędzeniu pieniędzy dzięki zatwierdzeniu do jednego roku lub trzech lat maszyn wirtualnych, SQL Database pojemności obliczeniowej, Azure Cosmos DB przepływności lub innych zasobów platformy Azure. Zatwierdzenie umożliwia uzyskanie rabatu za zasoby, których używasz. Rezerwacje mogą znacząco obniżyć ilość zasobów dla maszyny wirtualnej, obliczeń w usłudze SQL Database, Azure Cosmos DB lub innych kosztów zasobu do 72% w przypadku cen z wynagrodzeniem zgodnie z rzeczywistym użyciem. Rezerwacje zapewniają rabat na rozliczenia i nie mają wpływu na stan środowiska uruchomieniowego Twoich zasobów.

Możesz zanieść opłaty za rezerwację na pierwszy miesiąc lub miesięcznie. Łączny koszt z góry i miesięcznych rezerwacji jest taki sam, a opłaty za nie są naliczane, gdy użytkownik zdecyduje się płacić miesięcznie. Miesięczna płatność jest dostępna dla rezerwacji platformy Azure, a nie produktów innych firm.

Możesz kupić rezerwację w [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Dlaczego warto kupić rezerwację?

Jeśli masz maszyny wirtualne, Azure Cosmos DB lub bazy danych SQL, które są uruchamiane przez długi czas, kupowanie rezerwacji zapewnia najbardziej opłacalną opcję. Na przykład gdy ciągle uruchamiasz cztery wystąpienia usługi bez rezerwacji, opłaty są naliczone według stawek płatność zgodnie z rzeczywistym użyciem. Po zakupieniu rezerwacji dla tych zasobów natychmiast uzyskasz rabat na rezerwację. Zasoby nie są już obciążane opłatami według stawek płatność zgodnie z rzeczywistym użyciem.

## <a name="charges-covered-by-reservation"></a>Opłaty objęte rezerwacją

Plany usługi:

- **Zarezerwowane wystąpienie maszyny wirtualnej** — rezerwacja obejmuje tylko koszty obliczeń maszyn wirtualnych. Nie dotyczy to dodatkowych opłat za oprogramowanie, Sieć ani magazyn.
- **Azure Cosmos DB zarezerwowana pojemność** — rezerwacja obejmuje przepływność zainicjowaną dla zasobów. Nie dotyczy to opłat za magazyn i sieć.
- **SQL Database zastrzeżone rdzeń wirtualny** — tylko koszty obliczeń są uwzględniane w rezerwacji. Licencje są rozliczane osobno.
- **SQL Data Warehouse** — rezerwacja obejmuje użycie cDWU. Nie dotyczy to opłat za magazyn lub sieć związanych z SQL Data Warehouse użyciu.
- **Opłata za sygnaturę App Service** — rezerwacja obejmuje użycie sygnatury. Nie dotyczy to procesów roboczych, dlatego opłaty za inne zasoby związane z sygnaturą są naliczane oddzielnie.

W przypadku maszyn wirtualnych z systemem Windows i SQL Database można obsłużyć koszty licencji z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kto jest uprawniony do zakupu rezerwacji?

Aby kupić plan, musisz mieć rolę właściciela subskrypcji w przedsiębiorstwie (MS-AZR-0017P lub MS-AZR-0148P) lub z opcją płatność zgodnie z rzeczywistym użyciem (MS-AZR-0003P lub MS-AZR-0023P). Dostawcy rozwiązań w chmurze mogą kupować Azure Reservations za pomocą Azure Portal lub  [Centrum partnerskiego](/partner-center/azure-reservations).

Klienci korzystający z programu Umowa Enterprise (EA) mogą ograniczyć liczbę zakupów do administratorów EA, wyłączając opcję **Dodaj wystąpienia zarezerwowane** w portalu EA. Administratorzy EA muszą być właścicielami subskrypcji dla co najmniej jednej subskrypcji EA do zakupu rezerwacji. Ta opcja jest przydatna w przypadku przedsiębiorstw, które chcą kupować rezerwacje dla różnych centrów kosztów za pomocą scentralizowanego zespołu. Po zakupie zespoły centralne mogą dodawać do rezerwacji właścicieli centrów kosztów. Właściciele mogą następnie ograniczyć rezerwację do swoich subskrypcji. Centralny zespół nie musi mieć dostępu właściciela subskrypcji w przypadku zakupu rezerwacji.

Rabat rezerwacji dotyczy tylko zasobów skojarzonych z subskrypcjami zakupionymi w ramach przedsiębiorstwa, dostawcy rozwiązań w chmurze (CSP) i poszczególnych planów z uwzględnieniem stawek płatność zgodnie z rzeczywistym użyciem.

## <a name="scope-reservations"></a>Rezerwacje zakresu

Można ograniczyć rezerwację do subskrypcji lub grup zasobów. Ustawienie zakresu rezerwacji umożliwia wybranie miejsca, w którym obowiązują oszczędności rezerwacji. W przypadku określania zakresu rezerwacji do grupy zasobów rabaty rezerwacji dotyczą tylko grupy zasobów — nie całej subskrypcji.

### <a name="reservation-scoping-options"></a>Opcje zakresu rezerwacji

Za pomocą określania zakresu grupy zasobów masz trzy opcje zakresu rezerwacji, w zależności od potrzeb:

- **Zakres pojedynczej grupy zasobów** — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.
- **Zakres pojedynczej subskrypcji** — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.
- **Zakres udostępniony** — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów Umowa Enterprise, kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji z płatnością zgodnie z rzeczywistym użyciem zakres rozliczeń to wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.

Podczas stosowania rabatów rezerwacji w przypadku użycia platforma Azure przetwarza rezerwację w następującej kolejności:

1. Rezerwacje należące do zakresu grupy zasobów
2. Rezerwacje pojedynczego zakresu
3. Rezerwacje zakresu udostępnionego

Jedna grupa zasobów może uzyskać rabaty rezerwacji z wielu rezerwacji, w zależności od sposobu określania zakresu rezerwacji.

### <a name="scope-a-reservation-to-a-resource-group"></a>Określanie zakresu rezerwacji dla grupy zasobów

Można ograniczyć rezerwację do grupy zasobów podczas zakupu rezerwacji lub ustawić zakres po zakupie. Aby ograniczyć rezerwację do grupy zasobów, musisz być właścicielem subskrypcji.

Aby ustawić zakres, przejdź do strony [kupowanie rezerwacji](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) w Azure Portal. Wybierz typ rezerwacji, który chcesz kupić. Na stronie **Wybierz produkt, do którego chcesz kupić** wybór Zmień wartość zakresu na pojedynczą grupę zasobów. Następnie wybierz grupę zasobów.

![Przykład przedstawiający wybór zakupu rezerwacji maszyny wirtualnej](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Pokazywane są zalecenia dotyczące zakupu dla grupy zasobów w rezerwacji maszyny wirtualnej. Zalecenia są obliczane przez analizowanie użycia w ciągu ostatnich 30 dni. Zalecenie zakupu jest podejmowane, jeśli koszt uruchamiania zasobów z wystąpieniami zarezerwowanymi jest tańszy niż koszt używania zasobów z użyciem stawek płatność zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat zaleceń dotyczących zakupu rezerwacji, zobacz [pobieranie zaleceń dotyczących zakupu wystąpień zarezerwowanych na podstawie wzorca użycia](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Po zakupie rezerwacji można zawsze aktualizować zakres. W tym celu przejdź do zastrzeżenia, kliknij pozycję **Konfiguracja**, a następnie Przekrocz zakres rezerwacji. Przeznaczanie zakresu rezerwacji nie jest transakcją komercyjną. Termin rezerwacji nie jest zmieniany. Aby uzyskać więcej informacji o aktualizowaniu zakresu, zobacz [Aktualizowanie zakresu po zakupie rezerwacji](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Przykład pokazujący zmianę zakresu rezerwacji](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorowanie i optymalizowanie użycia rezerwacji

Możesz monitorować użycie rezerwacji na wiele sposobów — przez Azure Portal, za poorednictwem interfejsów API lub danych użycia. Aby wyświetlić wszystkie rezerwacje, do których masz dostęp, przejdź do pozycji **rezerwacje** w Azure Portal. Siatka rezerwacji pokazuje ostatni zarejestrowany procent użycia dla rezerwacji. Kliknij rezerwację, aby wyświetlić długoterminowe użycie rezerwacji.

Możesz również uzyskać użycie rezerwacji przy użyciu [interfejsów API](billing-reservation-apis.md#see-reservation-usage) i [danych użycia](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) , jeśli jesteś klientem z umową Enterprise Agreement.

Jeśli zauważysz, że użycie rezerwacji w zakresie grupy zasobów jest niskie, możesz zaktualizować zakres rezerwacji do pojedynczej subskrypcji lub udostępnić go w kontekście rozliczania. Można również podzielić rezerwację i zastosować wynikłe rezerwacje do różnych grup zasobów.

### <a name="other-considerations"></a>Inne zagadnienia

Jeśli nie masz pasujących zasobów w grupie zasobów, rezerwacja nie zostanie wykorzystana. Rezerwacja nie ma automatycznie zastosowania do innej grupy zasobów lub subskrypcji, w której występuje niskie wykorzystanie.

Zakres rezerwacji nie jest automatycznie aktualizowany w przypadku przenoszenia grupy zasobów z jednej subskrypcji do innej. Zakres nie jest aktualizowany w przypadku usunięcia grupy zasobów. Konieczne będzie zmienienie [zakresu rezerwacji](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). W przeciwnym razie rezerwacja będzie niedostępna.

## <a name="discounted-subscription-and-offer-types"></a>Subskrypcje z rabatem i typy ofert

Rabaty na rezerwacje dotyczą następujących kwalifikujących się subskrypcji i typów ofert.

- Umowy Enterprise Agreement (oferty o numerach: MS-AZR-0017P i MS-AZR-0148P)
- Indywidualne plany ze stawką płatności zgodnie z rzeczywistym użyciem (oferty o numerach: MS-AZR-0003P i MS-AZR-0023P)
- Subskrypcje CSP

Zasoby działające w ramach subskrypcji z innymi typami ofert nie otrzymują rabatu na rezerwację.

## <a name="how-is-a-reservation-billed"></a>Jak jest rozliczana rezerwacja?

Rezerwacja jest naliczana za metodę płatności powiązanej z subskrypcją. W przypadku subskrypcji przedsiębiorstwa koszt rezerwacji jest odejmowany od salda zobowiązania pieniężnego. Gdy saldo zobowiązań pieniężnych nie pokrywa kosztów rezerwacji, opłaty są naliczane za użycie nadwyżkowe. Jeśli masz subskrypcję z danego planu z płatnością zgodnie z rzeczywistym użyciem, karta kredytowa na koncie jest rozliczana natychmiast w przypadku zakupów z góry. Miesięczne płatności są wyświetlane na fakturze, a karta kredytowa jest naliczana miesięcznie. Po rozliczeniu za faktury są wyświetlane opłaty za następną fakturę.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat rezerwacji

Rabat związany z rezerwacją dotyczy użycia zasobów pasującego do atrybutów wybranych podczas zakupu rezerwacji. Atrybuty obejmują zakres, w którym są uruchamiane zgodne maszyny wirtualne, bazy danych SQL, Azure Cosmos DB lub inne zasoby. Jeśli na przykład chcesz uzyskać rabat rezerwacji dla czterech standardowych maszyn wirtualnych D2 w regionie zachodnie stany USA, wybierz subskrypcję, w której działają maszyny wirtualne.

Rabat rezerwacji to "*Użyj go lub*". Jeśli nie masz pasujących zasobów przez żadną godzinę, utracisz ilość rezerwacji dla tej godziny. Nie można przenieść nieużywanych godzin zarezerwowanych do przodu.

Po wyłączeniu zasobu rabat rezerwacji automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie znaleziono pasujących zasobów, zarezerwowanych godzin zostaną *utracone*.

Na przykład możesz później utworzyć zasób i uzyskać zgodną rezerwację, która jest niedostatecznie wykorzystywana. Rabat rezerwacji automatycznie dotyczy nowego pasującego zasobu.

Jeśli maszyny wirtualne są uruchomione w różnych subskrypcjach w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia stosowanie rabatu rezerwacji między subskrypcjami. Zakres można zmienić po zakupie rezerwacji. Aby uzyskać więcej informacji, zobacz [zarządzanie Azure Reservations](billing-manage-reserved-vm-instance.md).

Rabat rezerwacji dotyczy tylko zasobów skojarzonych z przedsiębiorstwami, dostawcami CSP lub subskrypcjami z stawką płatność zgodnie z rzeczywistym użyciem. Zasoby działające w ramach subskrypcji z innymi typami ofert nie otrzymują rabatu na rezerwację.

## <a name="when-the-reservation-term-expires"></a>Gdy termin rezerwacji wygaśnie

Na koniec okresu rezerwacji rabat rozliczenia wygasa. Opłaty za maszyny wirtualne, bazy danych SQL, Azure Cosmos DB lub inne zasoby są naliczane według ceny płatności zgodnie z rzeczywistym użyciem. Azure Reservations nie odnawiać automatycznie. Aby kontynuować pobieranie rabatu rozliczeniowego, musisz kupić nową rezerwację dla kwalifikujących się usług i oprogramowania.

## <a name="discount-applies-to-different-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów

W przypadku zakupu rezerwacji rabat może być stosowany do innych wystąpień z atrybutami, które znajdują się w tej samej grupie rozmiarów. Ta funkcja jest określana jako elastyczność rozmiaru wystąpienia. Elastyczność pokrycia rabatu zależy od typu rezerwacji i atrybutów wybranych podczas zakupu rezerwacji.

Plany usługi:

- Zarezerwowane wystąpienia maszyn wirtualnych: Po zakupieniu rezerwacji i wybraniu opcji **zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia**pokrycie rabatu zależy od wybranego rozmiaru maszyny wirtualnej. Rezerwacja może dotyczyć rozmiarów maszyn wirtualnych w tej samej grupie serii rozmiarów. Aby uzyskać więcej informacji, zobacz [elastyczność rozmiaru maszyny wirtualnej z wystąpieniami zarezerwowanych maszyn wirtualnych](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SQL Database zarezerwowana pojemność: Pokrycie rabatu zależy od wybranej warstwy wydajności. Aby uzyskać więcej informacji, zobacz [Opis sposobu stosowania rabatu rezerwacji platformy Azure](billing-understand-reservation-charges.md).
- Azure Cosmos DB zarezerwowana pojemność: Pokrycie rabatu zależy od alokowanej przepływności. Aby uzyskać więcej informacji, zobacz [opis zastosowania rabatu rezerwacji Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Powiadomienia dotyczące rezerwacji

W zależności od tego, jak płacisz za subskrypcję platformy Azure, wysyłamy pocztą e-mail powiadomienia o zastrzeżeniach do następujących użytkowników w organizacji. Powiadomienia są wysyłane dla różnych zdarzeń, w tym:

- Kup
- Zbliżające się wygaśnięcie rezerwacji
- Expiry
- Odnawiania
- Anulowania
- Zmiana zakresu

W przypadku klientów z subskrypcjami EA:
- Powiadomienie o zakupie jest wysyłane do kupującego i kontaktów z powiadomieniem EA.
- Inne powiadomienia dotyczące cyklu życia zastrzeżeń są wysyłane tylko do kontaktów z powiadomieniem EA.
- Użytkownicy dodani do rezerwacji przy użyciu uprawnień RBAC (IAM) nie odbierają żadnych powiadomień e-mail.

W przypadku klientów z indywidualnymi subskrypcjami:
- Zakup otrzymuje powiadomienie o zakupie.
- W momencie zakupu właściciel konta rozliczenia subskrypcji otrzymuje powiadomienie o zakupie.
- Właściciel konta odbiera wszystkie inne powiadomienia.


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o Azure Reservations z następującymi artykułami:
    - [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
    - [Informacje na temat użycia rezerwacji dla subskrypcji z użyciem stawek płatność zgodnie z rzeczywistym zużyciem](billing-understand-reserved-instance-usage.md)
    - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](billing-understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows, które nie są objęte rezerwacjami](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations w programie Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Dowiedz się więcej na temat rezerwacji planów usługi:
    - [Virtual Machines z Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Zasoby Azure Cosmos DB z Azure Cosmos DB zarezerwowaną pojemnością](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Zasoby obliczeniowe SQL Database z Azure SQL Database zarezerwowaną pojemnością](../sql-database/sql-database-reserved-capacity.md) Dowiedz się więcej o rezerwacjach planów oprogramowania:
    - [Plany oprogramowania Red Hat z Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Plany oprogramowania SUSE z Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)
