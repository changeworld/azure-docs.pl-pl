---
title: Zrozumienie rezerwacje platformy Azure z rabatami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rabat związany z rezerwacją jest stosowany do uruchamiania bazy danych SQL.
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 923337291d297832e03892b505f4efa8d3bb3f36
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300718"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Zrozumienie, jak rabat związany z rezerwacją platformy Azure są stosowane do bazy danych SQL

Po możesz kupić pojemność usługi Azure SQL Database, zarezerwowane, rabat związany z rezerwacją jest automatycznie stosowany do bazy danych SQL, odpowiadający atrybuty i ilość rezerwacji. Rezerwacja obejmuje koszty operacji obliczeniowych, bazy danych SQL Database. Opłaty są naliczane dla oprogramowania, magazynu i sieci w normalnych stawek za użycie. Może obejmować koszty licencjonowania dla baz danych SQL za pomocą [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Zarezerwowane wystąpienia maszyn wirtualnych, można zobaczyć [discount zrozumieć Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Rabat związany z rezerwacją stosowane do bazy danych SQL

 Rabat w wysokości rezerwowanie pojemności bazy danych SQL jest stosowany do uruchamiania bazy danych SQL w systemie godzinowym. Zastrzeżenia, który można kupić jest dopasowywany do wykorzystanie mocy obliczeniowej, wyemitowane przez uruchamianie baz danych SQL. Dla baz danych SQL, które nie są wykonywane całą godzinę rezerwacja jest automatycznie stosowany do innych baz danych SQL, pasujące atrybuty rezerwacji. Rabat można stosować do bazy danych SQL, które są uruchomione jednocześnie. Jeśli nie masz bazy danych SQL uruchamianą przez całą godzinę spełniającej atrybuty rezerwacji nie uzyskasz pełni czerpać korzyści płynące z rezerwacją dla danej godziny.

W poniższych przykładach pokazano, jak stosuje rabat w wysokości rezerwowanie pojemności bazy danych SQL w zależności od liczby rdzeni zakupiono i kiedy są one uruchamiane.

- Scenariusz 1: Kup pojemności bazy danych SQL zastrzeżone dla 8-rdzeniowe bazy danych SQL. Możesz uruchomić 16 rdzeni SQL Database, która odpowiada pozostałe atrybuty rezerwacji. Opłaty są naliczane zgodnie z rzeczywistym użyciem cena 8 rdzeni wykorzystanie mocy obliczeniowej bazy danych SQL. Otrzymujesz rabat związany z rezerwacją za jedną godzinę 8-rdzeniowe wykorzystanie mocy obliczeniowej bazy danych SQL.

Pozostałe tych przykładów założono, że pojemność zastrzeżone bazy danych SQL, którego kupujesz jest dla 16-rdzeniowe bazy danych SQL, i pozostałe atrybuty rezerwacji dopasowania uruchomionej bazy danych SQL.

- Scenariusz 2: Możesz uruchomić dwie bazy danych SQL z 8 rdzeniami każda na godzinę. Rabat związany z rezerwacją 16 rdzeni jest stosowany do obliczeń użycie dla obu 8 rdzeni baz danych SQL.
- Scenariusz 3: Możesz uruchomić jedną 16 rdzeń bazy danych SQL z 13: 00 do 13:30:00. Możesz uruchomić inny 16-rdzeniowe bazy danych SQL z 1:30 do 14: 00. Oba są objęte rabat związany z rezerwacją.
- Scenariusz 4: Możesz uruchomić jedną 16 rdzeń bazy danych SQL z 13: 00 do 1:45 pm. Możesz uruchomić inny 16-rdzeniowe bazy danych SQL z 1:30 do 14: 00. Opłaty są naliczane zgodnie z rzeczywistym użyciem ceny 15-minutowy nachodzące na siebie. Rabat związany z rezerwacją dotyczy wykorzystanie mocy obliczeniowej w pozostałym czasie.

Aby zrozumieć i wyświetlić aplikację Azure rezerwacji w rozliczeniach raporty użycia, zobacz [użycia rezerwacji Understand Azure](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zapłać z góry za zasoby obliczeniowe bazy danych SQL Database o pojemności usługi Azure SQL Database, zarezerwowane](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Opis zastrzeżenia dla subskrypcji programu CSP](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
