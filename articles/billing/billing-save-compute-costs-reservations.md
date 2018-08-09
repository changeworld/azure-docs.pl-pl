---
title: Co to są rezerwacje platformy Azure? | Microsoft Docs
description: Więcej informacji na temat rezerwacji platformy Azure i ceny, aby zapisać na maszynach wirtualnych, baz danych SQL i innych kosztów zasobów.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626308"
---
# <a name="what-are-azure-reservations"></a>Co to są rezerwacje platformy Azure?

Moc obliczeniową pomaga rezerwacje platformy Azure możesz zaoszczędzić na wstępnie płacenia za rok lub trzy lata maszyny wirtualnej lub bazy danych SQL. Wstępnie płacenia pozwala uzyskać rabat na zasoby, których używasz. Azure rezerwacji może znacznie zmniejszyć Twojej maszyny wirtualnej lub koszty operacji obliczeniowych bazy danych SQL — do 72% przy cenach zgodnie z rzeczywistym użyciem w — jeden rok lub trzy lata podejmowania z góry zobowiązań. rezerwacje Podaj rozliczeń rabat, a nie wpływają na stan środowiska uruchomieniowego maszyn wirtualnych ani baz danych SQL.

Możesz kupić rezerwację [witryny Azure portal](https://aka.ms/reservations). Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zapłać z góry za zasoby obliczeniowe bazy danych SQL Database o pojemności usługi Azure SQL Database, zarezerwowane](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Dlaczego należy kupić rezerwację?

Jeśli masz maszyny wirtualne lub baz danych SQL, działające przez długie okresy, zakup rezerwacji zapewnia najbardziej ekonomiczna opcja. Na przykład jeśli uruchomisz stale cztery wystąpień usługi bez rezerwacji, opłaty są naliczane według stawek płatności zgodnie z rzeczywistym. Możesz kupić rezerwację dla tych zasobów, natychmiast otrzymują rabat związany z rezerwacją. Zasoby nie są naliczane według stawek płatności zgodnie z rzeczywistym.

## <a name="what-charges-does-a-reservation-cover"></a>Opłaty za co to jest cover rezerwacji?

- Rezerwacja wystąpienia maszyny wirtualnej: Rezerwacji obejmuje jedynie koszty operacji obliczeniowych maszyn wirtualnych. Nie omówiono dodatkowe opłaty za oprogramowanie, sieci i magazynu.
- Baza danych SQL zastrzeżone — rdzeń wirtualny: tylko koszty mocy obliczeniowej są dołączone do rezerwacji. Licencja jest rozliczana osobno.

Dla maszyn wirtualnych Windows i usługi SQL Database, mogą obejmują koszty licencjonowania z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kto może skorzystać kupić rezerwację?

Klienci platformy Azure z tego typu subskrypcji można kupić rezerwację:

- Subskrypcja Enterprise Agreement (MS-AZR-0017P).
- Subskrypcja [Płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Musi mieć rolę "Owner" w subskrypcji, aby kupić rezerwację. Aby kupować rezerwacje WE rejestracji enterprise, administratora przedsiębiorstwa, musisz włączyć rezerwacji zakupów w witrynie EA portal. Domyślnie to ustawienie jest włączone.
- Partnerzy programu cloud Solution Provider (CSP), można użyć witryny Azure portal lub [Centrum partnerskiego](https://docs.microsoft.com/partner-center/azure-reservations) do zakupu rezerwacji platformy Azure. 

Rabat związany z rezerwacją platformy Azure ma zastosowanie tylko do maszyn wirtualnych lub baz danych SQL skojarzone z typów subskrypcji Enterprise, płatność za rzeczywiste użycie lub dostawcy usług Kryptograficznych.

## <a name="how-is-a-reservation-billed"></a>Jak jest rozliczana rezerwacji

Rezerwacja jest obciążany opłatą za metodę płatności, powiązane z subskrypcją. W przypadku subskrypcji Enterprise kosztów rezerwacji jest odejmowany od salda zobowiązania pieniężnego. Jeśli saldo zobowiązania pieniężnego nie pokrywa kosztów rezerwacji, opłaty są naliczane nadwyżkowe użycie. Jeśli masz subskrypcję płatność za rzeczywiste użycie, natychmiast rozliczany karty kredytowej, posiadane na Twoim koncie. Jeśli opłaty są naliczane za pomocą faktury, możesz zobaczyć opłaty na następnej fakturze.

## <a name="how-is-the-reservation-discount-applied"></a>Jaki jest stosowany rabat związany z rezerwacją

Rabat związany z rezerwacją dotyczy użycia zasobów, który odpowiada atrybuty, które możesz wybrać w przypadku dokonywania zakupu rezerwacji. Atrybuty obejmują zakres gdzie zgodnych maszyn wirtualnych, baz danych SQL lub inne zasoby. Na przykład chcąc rabat związany z rezerwacją do czterech maszyn wirtualnych standardowa D2 w regionie zachodnie stany USA, wybierz subskrypcję, w którym działają maszyny wirtualne. Jeśli maszyny wirtualne są uruchomione w różnych subskrypcjach w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia rabatu związanego z rezerwacją do zastosowania w subskrypcjach. Po zakupie rezerwacji, można zmienić zakres. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacji na platformie Azure](billing-manage-reserved-vm-instance.md).

Rabat związany z rezerwacją ma zastosowanie tylko do maszyn wirtualnych lub baz danych SQL skojarzone z enterprise lub subskrypcję z płatnością za rzeczywiste użycie typów. Maszyny wirtualne lub baz danych SQL, które działają w subskrypcji przy użyciu innych typów ofert nie otrzymują rabat związany z rezerwacją. Dla rejestracji enterprise dla subskrypcji Enterprise — tworzenie i testowanie nie są uprawnieni do skorzystania z korzyści z rezerwacji.

Aby lepiej zrozumieć wpływ rezerwacji z maszyny wirtualnej lub rozliczeń bazy danych SQL, zobacz [zrozumieć, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reservation-term-expires"></a>Co się stanie po wygaśnięciu okresu rezerwacji?

Po zakończeniu okresu rezerwacji rozliczeń rabat wygasa i maszyny wirtualnej, bazy danych SQL lub inne zasoby są naliczane płatności — jako — możesz Przejdź opłaty. Azure rezerwacji nie automatycznego odnawiania. Aby kontynuować wprowadzenie rozliczeń rabat, musisz kupić nowe zastrzeżenie dla rezerwacji kwalifikujących się usług.

## <a name="next-steps"></a>Kolejne kroki

Rozpoczęcie zapisywania na maszynach wirtualnych, kupując [wystąpienia zarezerwowanego maszyny Wirtualnej](../virtual-machines/windows/prepay-reserved-vm-instances.md) lub [wydajności rezerwowej bazy danych SQL](../sql-database/sql-database-reserved-capacity.md).

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Zarządzanie zastrzeżeniami platformy Azure](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)
- [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
