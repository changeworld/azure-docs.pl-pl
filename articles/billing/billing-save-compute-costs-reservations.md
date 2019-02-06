---
title: Co to jest Azure Reservations? | Microsoft Docs
description: Więcej informacji na temat rezerwacji platformy Azure i ceny, aby zaoszczędzić na maszynach wirtualnych, baz danych SQL, Azure Cosmos DB i innych kosztów zasobów.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 14b6125bd1467efcd6120bc367a7042ad29552bb
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744504"
---
# <a name="what-are-azure-reservations"></a>Co to jest Azure Reservations?

Rezerwacje platformy Azure pomoże Ci zaoszczędzić pieniądze, płacąc wstępnie za rok lub trzy lata maszyny wirtualnej, aby uzyskać moc obliczeniową bazy danych SQL, przepływności usługi Azure Cosmos DB lub innych zasobów platformy Azure. Wstępnie płacenia pozwala uzyskać rabat na zasoby, których używasz. Rezerwacje mogą znacznie zmniejszyć maszynę wirtualną, mocy obliczeniowej z bazy danych SQL, Azure Cosmos DB lub innego zasobu koszty do 72% przy cenach zgodnie z rzeczywistym użyciem w. Rezerwacje Podaj rozliczeń rabat, a nie wpływają na stan środowiska uruchomieniowego zasobów.

Możesz kupić rezerwację [witryny Azure portal](https://aka.ms/reservations). Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zapłać z góry za zasoby usługi Azure Cosmos DB z pojemnością zarezerwowane usługi Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Dlaczego należy kupić rezerwację?

Jeśli masz maszyny wirtualne, usługi Azure Cosmos DB lub baz danych SQL, działające przez długie okresy, zakup rezerwacji zapewnia najbardziej ekonomiczna opcja. Na przykład jeśli uruchomisz stale cztery wystąpień usługi bez rezerwacji, opłaty są naliczane według stawek płatności zgodnie z rzeczywistym. Możesz kupić rezerwację dla tych zasobów, natychmiast otrzymują rabat związany z rezerwacją. Zasoby nie są naliczane według stawek płatności zgodnie z rzeczywistym.

## <a name="what-charges-does-a-reservation-cover"></a>Opłaty za co to jest cover rezerwacji?

- Rezerwacja wystąpienia maszyny wirtualnej: Rezerwacja obejmuje jedynie koszty operacji obliczeniowych maszyn wirtualnych. Nie omówiono dodatkowe opłaty za oprogramowanie, sieci i magazynu.
- Bazy danych SQL zastrzeżone rdzeniach wirtualnych: Tylko koszty operacji obliczeniowych są dołączone do rezerwacji. Licencja jest rozliczana osobno.
- Usługa Azure Cosmos DB wydajności rezerwowej: Rezerwacja obejmuje przepływnością aprowizowaną dla zasobów, nie obejmuje koszty magazynu i sieci.

Dla maszyn wirtualnych Windows i usługi SQL Database, mogą obejmują koszty licencjonowania z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kto może skorzystać kupić rezerwację?

Klienci platformy Azure z tego typu subskrypcji kupić rezerwację:

- Enterprise agreement typu oferty subskrypcji (MS-AZR - 0017P lub MS-AZR - 0148P).
- Płatność za rzeczywiste użycie typu oferty subskrypcji (MS-AZR - 003P lub MS-AZR - 0023P).
- Partnerzy programu cloud Solution Provider (CSP), można użyć witryny Azure portal lub [Centrum partnerskiego](https://docs.microsoft.com/partner-center/azure-reservations) do zakupu rezerwacji platformy Azure.

Rabat związany z rezerwacją dotyczy tylko zasobów skojarzonych z typów subskrypcji Enterprise, płatność za rzeczywiste użycie lub dostawcy usług Kryptograficznych.

 Aby kupić plan:

- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** musi być włączona w [portalu EA portal](https://ea.azure.com). Lub, jeśli to ustawienie jest wyłączone, musi być administratorem subskrypcji umowy EA.

## <a name="how-is-a-reservation-billed"></a>Jak jest rozliczana rezerwacji

Rezerwacja jest obciążany opłatą za metodę płatności, powiązane z subskrypcją. W przypadku subskrypcji Enterprise kosztów rezerwacji jest odejmowany od salda zobowiązania pieniężnego. Jeśli saldo zobowiązania pieniężnego nie pokrywa kosztów rezerwacji, opłaty są naliczane nadwyżkowe użycie. Jeśli masz subskrypcję płatność za rzeczywiste użycie, natychmiast rozliczany karty kredytowej, posiadane na Twoim koncie. Jeśli opłaty są naliczane za pomocą faktury, możesz zobaczyć opłaty na następnej fakturze.

## <a name="how-is-the-reservation-discount-applied"></a>Jaki jest stosowany rabat związany z rezerwacją

Rabat związany z rezerwacją dotyczy użycia zasobów, który odpowiada atrybuty, które możesz wybrać w przypadku dokonywania zakupu rezerwacji. Atrybuty obejmują zakres, w którym uruchamiane zgodnych maszyn wirtualnych, baz danych SQL, Azure Cosmos DB i inne zasoby. Na przykład chcąc rabat związany z rezerwacją do czterech maszyn wirtualnych standardowa D2 w regionie zachodnie stany USA, wybierz subskrypcję, w którym działają maszyny wirtualne. Jeśli maszyny wirtualne są uruchomione w różnych subskrypcjach w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia rabatu związanego z rezerwacją do zastosowania w subskrypcjach. Po zakupie rezerwacji, można zmienić zakres. Aby uzyskać więcej informacji, zobacz [Zarządzanie zastrzeżeniami Azure](billing-manage-reserved-vm-instance.md).

Rabat związany z rezerwacją dotyczy tylko zasobów skojarzonych z typów subskrypcji Enterprise, płatność za rzeczywiste użycie lub dostawcy usług Kryptograficznych. Zasoby, które działają w subskrypcji przy użyciu innych typów ofert nie otrzymują rabat związany z rezerwacją.

Aby lepiej zrozumieć, jak rezerwacje wpływa na rozliczeniami, zobacz następujące tematy:

-  [Świadomość, że Azure Reserved VM Instances rabatów](billing-understand-vm-reservation-charges.md)
- [Zrozumienie rabat związany z rezerwacją platformy Azure](billing-understand-vm-reservation-charges.md)
- [Omówienie usługi Azure Cosmos DB rabat związany z rezerwacją](billing-understand-cosmosdb-reservation-charges.md)
- [Opis rabat związany z rezerwacją platformy Azure i użycia z systemem SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>Co się stanie po wygaśnięciu okresu rezerwacji?

Po zakończeniu okresu rezerwacji rozliczeń rabat wygasa, a maszyny wirtualne, bazy danych SQL, Azure Cosmos DB lub innego zasobu jest rozliczana w cenie płatności — jako — możesz Przejdź. Azure rezerwacji nie automatycznego odnawiania. Aby kontynuować wprowadzenie rozliczeń rabat, musisz kupić nowe zastrzeżenie dla kwalifikujących się usług i oprogramowania.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Rabat ma zastosowanie do różnych rozmiarów elastyczność rozmiaru wystąpienia

W przypadku dokonywania zakupu rezerwacji, Rabat można stosować do innych wystąpień z atrybutów, które znajdują się w tej samej grupie rozmiarów. Elastyczność w zakresie Rabat zależy od typu rezerwacji i atrybuty, które można wybrać w przypadku dokonywania zakupu rezerwacji.

- Wystąpienia zarezerwowane maszyn wirtualnych: Przy zakupie rezerwacji, jeśli zostanie wybrana **zoptymalizowane pod kątem**: **wystąpienia elastyczność rozmiaru**, pokrycia Rabat zależy od rozmiaru maszyny Wirtualnej, należy wybrać. Rezerwację można zastosować do rozmiarów maszyn wirtualnych (VM) w tej samej grupie rozmiar w serii. Aby uzyskać więcej informacji, zobacz [elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SUSE Linux Enterprise software plan: Pokrycie rabatu jest zależna od procesorów wirtualnych maszyn wirtualnych, w którym jest uruchamiane oprogramowanie SUSE. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak jest stosowany rabat plan oprogramowania SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md).
- Wydajności rezerwowej bazy danych SQL: Pokrycie Rabat zależy od warstwy wydajności, które można wybrać. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak jest stosowany rabat związany z rezerwacją Azure](billing-understand-reservation-charges.md).
- Usługa Azure Cosmos DB wydajności rezerwowej: Pokrycie Rabat zależy od aprowizowanej przepływności. Aby uzyskać więcej informacji, zobacz [zrozumieć, jak stosowany jest rabat związany z rezerwacją usługi Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="next-steps"></a>Kolejne kroki

Rozpoczęcie zapisywania na maszynach wirtualnych, kupując [wystąpienia zarezerwowanego maszyny Wirtualnej](../virtual-machines/windows/prepay-reserved-vm-instances.md), [wydajności rezerwowej bazy danych SQL](../sql-database/sql-database-reserved-capacity.md), lub [wydajności rezerwowej usługi Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md).

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)
- [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
