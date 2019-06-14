---
title: Co to jest Azure Reservations? | Microsoft Docs
description: Więcej informacji na temat rezerwacji platformy Azure i ceny, aby zaoszczędzić na maszynach wirtualnych, baz danych SQL, Azure Cosmos DB i innych kosztów zasobów.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b20983c45cd62b9812cdb52de32a6e29da459efe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370802"
---
# <a name="what-are-azure-reservations"></a>Co to jest Azure Reservations?

Rezerwacje platformy Azure ułatwiają Oszczędzaj pieniądze, wstępnie płacenia za rok lub trzy lata maszyny wirtualne, bazy danych SQL obliczeniowych, pojemność przepływności usługi Azure Cosmos DB i innych zasobów platformy Azure. Wstępnie płacenia pozwala uzyskać rabat na zasoby, których używasz. Rezerwacje mogą znacznie zmniejszyć maszynę wirtualną, mocy obliczeniowej z bazy danych SQL, Azure Cosmos DB lub innego zasobu koszty do 72% przy cenach zgodnie z rzeczywistym użyciem w. Rezerwacje Podaj rozliczeń rabat, a nie wpływają na stan środowiska uruchomieniowego zasobów.

Możesz kupić rezerwację [witryny Azure portal](https://aka.ms/reservations).

## <a name="why-buy-a-reservation"></a>Dlaczego warto kupić rezerwację?

Jeśli masz maszyny wirtualne, usługi Azure Cosmos DB lub baz danych SQL, działające przez długie okresy, zakup rezerwacji zapewnia najbardziej ekonomiczna opcja. Na przykład po uruchomieniu stale cztery wystąpień usługi bez rezerwacji, opłaty są naliczane według stawek zgodnie z rzeczywistym użyciem. Możesz kupić rezerwację dla tych zasobów, natychmiast otrzymują rabat związany z rezerwacją. Zasoby nie są naliczane według stawek płatności zgodnie z rzeczywistym.

## <a name="charges-covered-by-reservation"></a>Opłaty za objętych rezerwacji

Plany usługi:

- Rezerwacja wystąpienia maszyny wirtualnej: Rezerwacja obejmuje jedynie koszty operacji obliczeniowych maszyn wirtualnych. Nie omówiono dodatkowe opłaty za oprogramowanie, sieci i magazynu.
- Usługa Azure Cosmos DB wydajności rezerwowej: Rezerwacja obejmuje przepływnością aprowizowaną dla zasobów. Nie omówiono magazynu i są opłaty za sieć.
- Bazy danych SQL zastrzeżone rdzeniach wirtualnych: Tylko koszty operacji obliczeniowych są dołączone do rezerwacji. Licencja jest rozliczana osobno.

Dla maszyn wirtualnych Windows i usługi SQL Database, mogą obejmują koszty licencjonowania z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kto może skorzystać kupić rezerwację?

Aby kupić plan, musi mieć rolę właściciela subskrypcji w subskrypcji płatności (MS-AZR - 003P lub MS-AZR - 0023P) lub Enterprise (MS-AZR - 0017P lub MS-AZR - 0148P). Dostawców rozwiązań w chmurze można użyć witryny Azure portal lub [Centrum partnerskiego](/partner-center/azure-reservations) do zakupu rezerwacji platformy Azure.

Klienci z umową EA, można ograniczyć zakupów dla administratorów umowy EA, wyłączając **Dodawanie wystąpień zarezerwowanych** opcji w portalu EA. Administratorzy EA musi być właścicielem subskrypcji dla co najmniej jednej subskrypcji umowy EA, aby kupić rezerwację. Opcja jest przydatna dla przedsiębiorstw, które scentralizowane zespołu do zakupu rezerwacji dla centrów kosztów. Po zakupie zespoły scentralizowanego można dodać właścicieli Centrum kosztów do rezerwacji. Właściciele mogą następnie zakresu rezerwacji do swojej subskrypcji. Centralny zespół nie musi mieć dostęp właściciela subskrypcji, w którym zakupu rezerwacji.

Rabat związany z rezerwacją dotyczy tylko zasobów skojarzonych z typów subskrypcji Enterprise, płatność za rzeczywiste użycie lub dostawcy usług Kryptograficznych.

## <a name="reservation-scope"></a>Zakres rezerwacji

Zakres rezerwacji określa zasoby, do których ma zastosowanie rabatu związanego z rezerwacją. Zakres rezerwacji może mieć następujące wartości:

**Zakres udostępniony** — rabat związany z rezerwacją jest stosowany do pasujących zasobów w uprawnione subskrypcje w ramach kontekstu rozliczeń.

- W przypadku klientów z umową Enterprise Agreement kontekstu rozliczeń jest rejestracja.
 W przypadku klientów płatność za rzeczywiste użycie rozliczeń zakres jest wszystkie uprawnione subskrypcje utworzone przez administratora konta.

**Subskrypcja pojedyncza** — rabat związany z rezerwacją jest stosowany do pasujących zasobów w wybranej subskrypcji.

Możesz [zaktualizować zakresu po zakupie rezerwacji](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

## <a name="discounted-subscription-and-offer-types"></a>Rabatem subskrypcji i typów ofert

Rezerwacja rabaty dotyczą następujących kwalifikujących się subskrypcji i oferują typów.

- Umowy Enterprise agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P)
- Płatność za rzeczywiste użycie (oferują liczb: MS-AZR-0003P lub MS-AZR - 0023 P)
- Subskrypcje dostawcy usług Kryptograficznych

Zasoby, które działają w subskrypcji przy użyciu innych typów ofert nie otrzymują rabat związany z rezerwacją.

## <a name="how-is-a-reservation-billed"></a>Jak jest rozliczana rezerwacji

Rezerwacja jest obciążany opłatą za metodę płatności, powiązane z subskrypcją. W przypadku subskrypcji Enterprise kosztów rezerwacji jest odejmowany od salda zobowiązania pieniężnego. Jeśli saldo zobowiązania pieniężnego nie pokrywa kosztów rezerwacji, opłaty są naliczane nadwyżkowe użycie. Jeśli masz subskrypcję płatność za rzeczywiste użycie, natychmiast rozliczany karty kredytowej, posiadane na Twoim koncie. Jeśli opłaty są naliczane za pomocą faktury, możesz zobaczyć opłaty na następnej fakturze.

## <a name="how-reservation-discount-is-applied"></a>Jaki jest stosowany rabat związany z rezerwacją

Rabat związany z rezerwacją dotyczy użycia zasobów, pasujące atrybuty, które możesz wybrać w przypadku dokonywania zakupu rezerwacji. Atrybuty obejmują zakres, w którym uruchamiane zgodnych maszyn wirtualnych, baz danych SQL, Azure Cosmos DB i inne zasoby. Na przykład chcąc rabat związany z rezerwacją do czterech maszyn wirtualnych standardowa D2 w regionie zachodnie stany USA, wybierz subskrypcję, w którym działają maszyny wirtualne.

Rabat związany z rezerwacją jest "*Użyj it lub utracić — it*". Jeśli nie masz, dopasowując zasoby do godziny, następnie traci ilość rezerwacji dla danej godziny. Nie można wykonać nieużywane zastrzeżone godzin przekazywania.

Podczas zamykania zasobem rabat związany z rezerwacją jest automatycznie stosuje do innego zasobu zgodnego z określonego zakresu. Jeśli żadne pasujące zasoby znajdują się w określonym zakresie, a następnie są zarezerwowane godzin *utracone*.

Na przykład może później utworzyć zasób i mieć pasującego rezerwacji, który jest skutkowało niewystarczającym wykorzystaniem. W tym przykładzie rabat związany z rezerwacją jest automatycznie stosuje do nowego zasobu dopasowania.

Jeśli maszyny wirtualne są uruchomione w różnych subskrypcjach w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia rabatu związanego z rezerwacją do zastosowania w subskrypcjach. Po zakupie rezerwacji, można zmienić zakres. Aby uzyskać więcej informacji, zobacz [Zarządzanie zastrzeżeniami Azure](billing-manage-reserved-vm-instance.md).

Rabat związany z rezerwacją dotyczy tylko zasobów skojarzonych z typów subskrypcji Enterprise, płatność za rzeczywiste użycie lub dostawcy usług Kryptograficznych. Zasoby, które działają w subskrypcji przy użyciu innych typów ofert nie otrzymują rabat związany z rezerwacją.

## <a name="when-the-reservation-term-expires"></a>Kiedy wygasa w okresie rezerwacji

Po zakończeniu okresu rezerwacji rozliczeń rabat wygasa, a maszyny wirtualne, bazy danych SQL, Azure Cosmos DB lub innego zasobu jest rozliczana w cenie płatności — jako — możesz Przejdź. Azure rezerwacji nie automatycznego odnawiania. Aby kontynuować wprowadzenie rozliczeń rabat, musisz kupić nowe zastrzeżenie dla kwalifikujących się usług i oprogramowania.

## <a name="discount-applies-to-different-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów

W przypadku dokonywania zakupu rezerwacji, Rabat można stosować do innych wystąpień z atrybutów, które znajdują się w tej samej grupie rozmiarów. Ta funkcja jest nazywana elastyczność rozmiaru wystąpienia. Elastyczność w zakresie Rabat zależy od typu rezerwacji i atrybuty, które można wybrać w przypadku dokonywania zakupu rezerwacji.

Plany usługi:

- Wystąpienia zarezerwowane maszyn wirtualnych: Po kupić rezerwację i wybraniu **zoptymalizowane pod kątem**: **wystąpienia elastyczność rozmiaru**, pokrycia Rabat zależy od rozmiaru maszyny Wirtualnej, należy wybrać. Rezerwację można zastosować do rozmiarów maszyn wirtualnych (VM) w tej samej grupie rozmiar w serii. Aby uzyskać więcej informacji, zobacz [elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Wydajności rezerwowej bazy danych SQL: Pokrycie Rabat zależy od warstwy wydajności, które można wybrać. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak jest stosowany rabat związany z rezerwacją Azure](billing-understand-reservation-charges.md).
- Usługa Azure Cosmos DB wydajności rezerwowej: Pokrycie Rabat zależy od aprowizowanej przepływności. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak stosowany jest rabat związany z rezerwacją usługi Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o Azure rezerwacji z następujących artykułów:
    - [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
    - [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
    - [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)
    - [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Dowiedz się więcej na temat rezerwacji dla planów usług:
    - [Maszyny wirtualne z wystąpień zarezerwowanych maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Wydajności rezerwowej zasobów platformy Azure Cosmos DB przy użyciu usługi Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Zasoby obliczeniowe bazy danych SQL z usługą Azure SQL Database wydajności rezerwowej](../sql-database/sql-database-reserved-capacity.md) Dowiedz się więcej o rezerwacji dla plany oprogramowania:
    - [Red Hat plany oprogramowania z listy zastrzeżeń platformy Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Plany oprogramowania SUSE z listy zastrzeżeń platformy Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
