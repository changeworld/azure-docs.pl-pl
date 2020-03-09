---
title: Co to jest Azure Reservations?
description: Dowiedz się więcej o rezerwacjach platformy Azure i cenach, które umożliwiają oszczędzanie za kosztach maszyn wirtualnych, baz danych SQL, usługi Azure Cosmos DB i innych zasobów.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: banders
ms.openlocfilehash: 1c5dd5ebd5023b75aad5dabf6a721a1193a6224d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272204"
---
# <a name="what-are-azure-reservations"></a>Co to jest Azure Reservations?

Rezerwacje platformy Azure pomagają zaoszczędzić pieniądze dzięki zobowiązaniu do korzystania z planów dla maszyn wirtualnych, usługi Azure Blob Storage lub Azure Data Lake Storage Gen2, pojemności obliczeniowej bazy danych SQL Database, usługi Azure Disk Storage, przepływności bazy danych Azure Cosmos DB lub innych zasobów platformy Azure przez jeden rok lub trzy lata. Zobowiązanie umożliwia uzyskanie rabatu dotyczącego używanych zasobów. Rezerwacje mogą znacznie obniżyć koszty zasobów, nawet o 72% w porównaniu do cen przy płatności zgodnie z rzeczywistym użyciem. Rezerwacje umożliwiają skorzystanie z rabatu na rozliczenia i nie mają wpływu na stan środowiska uruchomieniowego Twoich zasobów.

Za rezerwację można zapłacić z góry lub w miesięcznych ratach. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat. Opcja płatności miesięcznych jest dostępna w przypadku rezerwacji platformy Azure, a nie produktów innych firm.

Rezerwację można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Dlaczego warto kupić rezerwację?

Jeśli masz maszyny wirtualne, dane w magazynie obiektów blob, bazę danych Azure Cosmos DB lub bazy danych SQL, które używają dużo pojemności lub przepływności bądź są uruchamiane przez długi czas, kupowanie rezerwacji jest najbardziej opłacalną opcją. Jeśli na przykład ciągle uruchamiasz cztery wystąpienia usługi bez rezerwacji, opłaty są naliczone według stawek płatności zgodnie z rzeczywistym użyciem. Przy zakupie rezerwacji dla tych zasobów natychmiast uzyskasz rabat na rezerwację. Zasoby nie będą już obciążane opłatami według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="charges-covered-by-reservation"></a>Opłaty objęte rezerwacją

Plany usługi:

- **Wystąpienie zarezerwowane maszyny wirtualnej** — rezerwacja obejmuje tylko koszty obliczeń na maszynie wirtualnej. Nie dotyczy dodatkowych opłat za oprogramowanie, sieć ani magazyn.
- **Pojemność zarezerwowana usługi Azure Storage** — rezerwacja obejmuje pojemność magazynu dla kont magazynu w warstwie Standardowa dla magazynu obiektów blob lub magazynu usługi Azure Data Lake Gen2. Rezerwacja nie obejmuje stawek za przepustowość lub transakcje.
- **Rezerwacje usługi Azure Disk Storage** — rezerwacja obejmuje tylko dyski SSD w warstwie Premium o rozmiarze P30 lub większym. Nie obejmuje żadnych innych typów dysków ani rozmiarów mniejszych niż P30.
- **Pojemność zarezerwowana usługi Azure Cosmos DB** — rezerwacja obejmuje przepływność przydzieloną dla zasobów. Nie dotyczy opłat za magazyn i sieć.
- **Zarezerwowany rdzeń wirtualny w usłudze SQL Database** — w rezerwacji są uwzględniane tylko koszty obliczeń. Opłata za licencję jest naliczana oddzielnie.
- **SQL Data Warehouse** — rezerwacja obejmuje użycie jednostek cDWU. Nie pokrywa kosztów magazynu i sieci wynikających z użycia usługi SQL Data Warehouse.
- **Podatek od czynności cywilnoprawnych za korzystanie z usługi App Service** — rezerwacja obejmuje użycie sygnatury. Nie dotyczy to procesów roboczych, dlatego opłaty za inne zasoby związane z sygnaturą są naliczane oddzielnie.
- **Azure Databricks** — rezerwacja obejmuje tylko użycie jednostek DBU. Pozostałe opłaty, na przykład za obliczenia, magazyn i sieć, są naliczane oddzielnie.
- **Azure Database for MySQL** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za oprogramowanie, sieć ani magazyn związane z serwerem bazy danych MySQL.
- **Azure Database for PostgreSQL** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za oprogramowanie, sieć ani magazyn związane z serwerami bazy danych PostgreSQL.
- **Azure Database for MariaDB** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za oprogramowanie, sieć ani magazyn związane z serwerem bazy danych MariaDB.
- **Azure Data Explorer** — rezerwacja obejmuje opłaty za znaczniki. Rezerwacja nie obejmuje opłat za obliczenia, sieć ani magazyn związane z klastrami.
- **Azure Cache for Redis** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za sieć ani magazyn związanych z wystąpieniami pamięci podręcznej Redis Cache.
- **Dyski zarządzane SSD w warstwie Premium** — rezerwacja jest wykonywana dla określonej jednostki SKU dysku. 

Plany oprogramowania:

- **SUSE Linux** — rezerwacja obejmuje koszty planu oprogramowania. Rabaty mają zastosowanie tylko do mierników SUSE, a nie do użycia maszyn wirtualnych.
- **Plany oprogramowania Red Hat** — rezerwacja obejmuje koszty planu oprogramowania. Rabaty mają zastosowanie tylko do mierników RedHat, a nie do użycia maszyn wirtualnych.
- **Azure VMware Solution by CloudSimple** — rezerwacja obejmuje węzły rozwiązania VMWare CloudSimple. Nadal obowiązują dodatkowe opłaty za oprogramowanie.
- **Azure Red Hat OpenShift** — rezerwacja ma zastosowanie do kosztów oprogramowania OpenShift, a nie do kosztów infrastruktury platformy Azure.

W przypadku maszyn wirtualnych z systemem Windows i usługi SQL Database koszty licencji można pokryć za pomocą opcji [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kto jest uprawniony do zakupu rezerwacji?

Aby kupić plan, musisz mieć rolę właściciela subskrypcji w subskrypcji Enterprise (MS-AZR-0017P lub MS-AZR-0148P), subskrypcji z płatnością zgodnie z rzeczywistym użyciem (MS-AZR-0003P lub MS-AZR-0023P) albo subskrypcji w ramach umowy klienta firmy Microsoft. Dostawy rozwiązań w chmurze mogą użyć witryny Azure Portal lub [Centrum partnerskiego](/partner-center/azure-reservations), aby zakupić rezerwacje platformy Azure.

Klienci korzystający z umowy Enterprise Agreement (EA) mogą ograniczyć możliwość zakupów tylko do administratorów EA, wyłączając opcję **Dodaj wystąpienia zarezerwowane** w portalu EA. Administrator EA musi być właścicielem co najmniej jednej subskrypcji EA, aby mieć możliwość zakupu rezerwacji. Ta opcja jest przydatna w przypadku przedsiębiorstw, które chcą kupować rezerwacje dla różnych centrów kosztów za pośrednictwem centralnego zespołu. Po zakupie centralne zespoły mogą dodawać do rezerwacji właścicieli centrów kosztów. Właściciele mogą następnie ograniczyć rezerwację do swoich subskrypcji. Centralny zespół nie musi mieć dostępu właściciela subskrypcji do obszaru, w którym zakupiono rezerwację.

Rabat na rezerwację dotyczy tylko zasobów skojarzonych z subskrypcjami zakupionymi w ramach umowy Enterprise, subskrypcji dostawcy usług w chmurze, umowy klienta firmy Microsoft i planów indywidualnych ze stawkami płatności zgodnie z rzeczywistym użyciem.

## <a name="scope-reservations"></a>Określanie zakresu rezerwacji

Zakres rezerwacji można ograniczyć do subskrypcji lub grup zasobów. Ustawienie zakresu rezerwacji określa, gdzie wystąpią oszczędności z rezerwacji. W przypadku określenia zakresu rezerwacji jako grupa zasobów rabaty na rezerwację dotyczą tylko tej grupy zasobów — a nie całej subskrypcji.

### <a name="reservation-scoping-options"></a>Opcje określania zakresu rezerwacji

W przypadku określania zakresu grupy zasobów masz trzy opcje zakresu rezerwacji do wyboru w zależności od potrzeb:

- **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
- **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
- **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. W przypadku klientów z umową klienta firmy Microsoft zakres rozliczeniowy to profil rozliczeniowy. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.

Podczas stosowania rabatów na rezerwację w stosunku do użycia platforma Azure przetwarza rezerwacje w następującej kolejności:

1. Rezerwacje, których zakres jest ograniczony do grupy zasobów
2. Rezerwacje pojedynczego zakresu
3. Rezerwacje zakresu udostępnionego

Jedna grupa zasobów może uzyskać rabaty na rezerwację z wielu rezerwacji, w zależności od sposobu określania zakresu rezerwacji.

### <a name="scope-a-reservation-to-a-resource-group"></a>Określanie zakresu rezerwacji dla grupy zasobów

Zakres rezerwacji można ograniczyć do grupy zasobów podczas zakupu. Można też ustawić zakres po zakupie. Aby ograniczyć rezerwację do grupy zasobów, musisz być właścicielem subskrypcji.

Aby ustawić zakres, przejdź do strony [Zakup rezerwacji](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) w witrynie Azure Portal. Wybierz typ rezerwacji, którą chcesz kupić. Na stronie formularza wyboru **Wybierz produkt, który chcesz kupić** zmień wartość w polu Zakres na Pojedyncza grupa zasobów. Następnie wybierz grupę zasobów.

![Przykład przedstawiający wybór zakupu rezerwacji maszyny wirtualnej](./media/save-compute-costs-reservations/select-product-to-purchase.png)

Wyświetlane są rekomendacje dotyczące zakupu dla grupy zasobów w rezerwacji maszyny wirtualnej. Rekomendacje są obliczane przez analizowanie użycia w ciągu ostatnich 30 dni. Rekomendacja zakupu jest generowana, jeśli koszt uruchamiania zasobów z użyciem wystąpień zarezerwowanych jest niższy niż koszt uruchamiania zasobów z użyciem stawek płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat rekomendacji dotyczących zakupu rezerwacji, zobacz artykuł [Get Reserved Instance purchase recommendations based on usage pattern](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) (Uzyskiwanie rekomendacji dotyczących zakupu wystąpień zarezerwowanych na podstawie wzorca użycia).

Zakres można zaktualizować w dowolnym momencie po zakupie rezerwacji. W tym celu należy przejść do rezerwacji, kliknąć pozycję **Konfiguracja** i ponownie określić zakres. Zmiana zakresu rezerwacji nie jest transakcją handlową. Okres rezerwacji nie jest zmieniany. Aby uzyskać więcej informacji o aktualizowaniu zakresu, [zobacz Aktualizowanie zakresu po zakupie rezerwacji.](manage-reserved-vm-instance.md#change-the-reservation-scope)

![Przykład pokazujący zmianę zakresu rezerwacji](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorowanie i optymalizowanie użycia rezerwacji

Możesz monitorować użycie rezerwacji na wiele sposobów — przez witrynę Azure Portal lub za pośrednictwem interfejsów API albo danych użycia. Aby wyświetlić wszystkie rezerwacje, do których masz dostęp, przejdź do pozycji **Rezerwacje** w witrynie Azure Portal. Siatka rezerwacji pokazuje ostatni zarejestrowany procent wykorzystania dla rezerwacji. Kliknij rezerwację, aby wyświetlić długoterminowe wykorzystanie rezerwacji.

Wykorzystanie rezerwacji możesz również sprawdzić przy użyciu [interfejsów API](reservation-apis.md#see-reservation-usage) i [danych użycia](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks), jeśli jesteś klientem z umową Enterprise Agreement lub umową klienta firmy Microsoft.

Jeśli zauważysz, że wykorzystanie rezerwacji w zakresie grupy zasobów jest niskie, możesz zaktualizować zakres rezerwacji do pojedynczej subskrypcji lub udostępnić go w ramach kontekstu rozliczeń. Można również podzielić rezerwację i zastosować uzyskane w ten sposób rezerwacje do różnych grup zasobów.

### <a name="other-considerations"></a>Inne zagadnienia

Jeśli nie masz pasujących zasobów w grupie zasobów, rezerwacja nie zostanie w pełni wykorzystana. W przypadku niskiego wykorzystania rezerwacja nie zostanie automatycznie zastosowania do innej grupy zasobów lub subskrypcji.

Zakres rezerwacji nie jest automatycznie aktualizowany w przypadku przenoszenia grupy zasobów z jednej subskrypcji do innej. Zakres nie jest aktualizowany w przypadku usunięcia grupy zasobów. Konieczna jest [zmiana zakresu rezerwacji](manage-reserved-vm-instance.md#change-the-reservation-scope). W przeciwnym razie rezerwacja nie zostanie w pełni wykorzystana.

## <a name="discounted-subscription-and-offer-types"></a>Subskrypcje z rabatem i typy ofert

Rabaty na rezerwacje dotyczą następujących kwalifikujących się subskrypcji i typów ofert.

- Umowy Enterprise Agreement (oferty o numerach: MS-AZR-0017P i MS-AZR-0148P)
- Subskrypcje w ramach umów klienta firmy Microsoft
- Indywidualne plany ze stawką płatności zgodnie z rzeczywistym użyciem (oferty o numerach: MS-AZR-0003P i MS-AZR-0023P)
- Subskrypcje CSP

Zasoby działające w ramach subskrypcji z innymi typami ofert nie otrzymują rabatu na rezerwację.

## <a name="how-is-a-reservation-billed"></a>Jak jest rozliczana rezerwacja?

Kosztem rezerwacji jest obciążana forma płatności powiązana z subskrypcją. Koszt rezerwacji jest odejmowany od salda zobowiązania pieniężnego, jeśli jest dostępne. Gdy saldo zobowiązania pieniężnego nie pokrywa kosztu rezerwacji, są naliczane opłaty za użycie nadwyżkowe. Jeśli masz subskrypcję z planu indywidualnego z płatnością zgodnie z rzeczywistym użyciem, karta kredytowa powiązana z kontem jest obciążana natychmiast w przypadku zakupów płatnych z góry. Płatności miesięczne są widoczne na fakturze, a karta kredytowa jest obciążana co miesiąc. W przypadku rozliczania z użyciem faktury opłaty pojawią się na następnej fakturze.

## <a name="how-reservation-discount-is-applied"></a>Jak jest stosowany rabat na rezerwacje

Rabat związany z rezerwacją dotyczy użycia zasobów pasującego do atrybutów wybranych podczas zakupu rezerwacji. Atrybuty obejmują zakres, w którym są uruchamiane zgodne maszyny wirtualne, bazy danych SQL, bazy danych Azure Cosmos DB lub inne zasoby. Jeśli na przykład chcesz uzyskać rabat na rezerwację dla czterech standardowych maszyn wirtualnych D2 w regionie Zachodnie stany USA, wybierz subskrypcję, w której działają te maszyny wirtualne.

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

Na przykład możesz później utworzyć zasób i dopasować go do rezerwacji, która jest niedostatecznie wykorzystywana. Rabat na rezerwację zostanie automatycznie zastosowany do nowego pasującego zasobu.

Jeśli maszyny wirtualne są uruchomione w różnych subskrypcjach w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia stosowanie rabatu na rezerwację w różnych subskrypcjach. Zakres można zmienić po zakupie rezerwacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md).

Rabat na rezerwację dotyczy tylko zasobów skojarzonych z subskrypcją Enterprise, umową klienta firmy Microsoft, dostawcą usług w chmurze lub subskrypcjami ze stawkami płatności zgodnie z rzeczywistym użyciem. Zasoby działające w ramach subskrypcji z innymi typami ofert nie otrzymują rabatu na rezerwację.

## <a name="when-the-reservation-term-expires"></a>Gdy okres rezerwacji wygaśnie

Na koniec okresu rezerwacji rabat rozliczenia wygasa i od tego momentu zasoby są rozliczane według cen przy płatności zgodnie z rzeczywistym użyciem. Domyślnie rezerwacje nie są ustawiane na automatyczne odnawianie. Automatyczne odnawianie rezerwacji możesz włączyć, wybierając opcję w ustawieniach odnawiania. W przypadku automatycznego odnawiania zastępcza rezerwacja będzie kupowana po wygaśnięciu istniejącej rezerwacji. Domyślnie zastępcza rezerwacja ma te same atrybuty co wygasająca rezerwacja. Opcjonalnie można zmienić częstotliwość rozliczeń, okres lub ilość w ustawieniach odnawiania. Każdy użytkownik mający dostęp na poziomie właściciela do rezerwacji i subskrypcji używanej do rozliczeń może skonfigurować odnawianie.  

## <a name="discount-applies-to-different-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów

W przypadku zakupu rezerwacji rabat może być stosowany do innych wystąpień z atrybutami, które znajdują się w tej samej grupie rozmiarów. Ta funkcja jest określana jako elastyczność rozmiaru wystąpienia. Elastyczność zastosowania rabatu zależy od typu rezerwacji i atrybutów wybranych podczas zakupu rezerwacji.

Plany usługi:

- Wystąpienia zarezerwowane maszyn wirtualnych: Po zakupieniu rezerwacji i wybraniu opcji **Zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia** zastosowanie rabatu zależy od wybranego rozmiaru maszyny wirtualnej. Rezerwacja może dotyczyć rozmiarów maszyn wirtualnych w tej samej grupie serii rozmiarów. Aby uzyskać więcej informacji, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Pojemność zarezerwowana usługi Azure Storage: Możesz kupić pojemność zarezerwowaną dla kont usługi Azure Storage w warstwie Standardowa w jednostkach po 100 TiB lub 1 PiB na miesiąc. Pojemność zarezerwowana usługi Azure Storage jest dostępna we wszystkich regionach dla dowolnej warstwy dostępu (Gorąca, Chłodna lub Archiwalna) i dla dowolnej opcji replikacji (magazyn lokalnie nadmiarowy, magazyn geograficznie nadmiarowy lub magazyn strefowo nadmiarowy).
- Pojemność zarezerwowana usługi SQL Database: Zastosowanie rabatu zależy od wybranej warstwy wydajności. Aby uzyskać więcej informacji, zobacz [opis sposobu stosowania rabatu na rezerwację platformy Azure](understand-reservation-charges.md).
- Pojemność zarezerwowana usługi Azure Cosmos DB: Zastosowanie rabatu zależy od alokowanej przepływności. Aby uzyskać więcej informacji, zobacz [opis sposobu stosowania rabatu na rezerwację usługi Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Powiadomienia dotyczące rezerwacji

W zależności od tego, jak płacisz za subskrypcję platformy Azure, powiadomienia o rezerwacji wysyłamy pocztą e-mail do następujących użytkowników w organizacji. Wysyłane są powiadomienia dotyczące różnych zdarzeń, takich jak:

- Zakup
- Zbliżające się wygaśnięcie rezerwacji
- Wygaśnięcie
- Odnowienie
- Anulowanie
- Zmiana zakresu

W przypadku klientów z subskrypcjami EA:
- Powiadomienie o zakupie jest wysyłane do kupującego i do kontaktów dla powiadomień ustawionych w ramach umowy EA.
- Inne powiadomienia dotyczące cyklu życia rezerwacji są wysyłane tylko do kontaktów dla powiadomień ustawionych w ramach umowy EA.
- Użytkownicy dodani do rezerwacji przy użyciu uprawnień RBAC (Zarządzanie dostępem i tożsamościami) nie otrzymują żadnych powiadomień e-mail.

W przypadku klientów z subskrypcjami indywidualnymi:
- Kupujący otrzymuje powiadomienie o zakupie.
- W momencie zakupu właściciel konta rozliczenia subskrypcji otrzymuje powiadomienie o zakupie.
- Właściciel konta otrzymuje wszystkie inne powiadomienia.


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat rezerwacji na platformie Azure, czytając następujące artykuły:
    - [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
    - [Understand reservation usage for your subscription with pay-as-you-go rates](understand-reserved-instance-usage.md) (Informacje na temat użycia rezerwacji w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)
    - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)
    - [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)

- Dowiedz się więcej na temat rezerwacji w przypadku planów usługi:
    - [Maszyny wirtualne w ramach usługi Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Zasoby Azure Cosmos DB w ramach zarezerwowanej pojemności Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Zasoby obliczeniowe usługi SQL Database w ramach zarezerwowanej pojemności usługi Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md) Dowiedz się więcej na temat rezerwacji w przypadku planów oprogramowania:
    - [Plany oprogramowania Red Hat w ramach rezerwacji platformy Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Plany oprogramowania SUSE w ramach rezerwacji platformy Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
