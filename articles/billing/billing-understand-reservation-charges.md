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
ms.author: cwatson
ms.openlocfilehash: 176e282a53c19e303fd06629a0045a79fd200dea
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52580375"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Zrozumienie, jak rabat związany z rezerwacją platformy Azure są stosowane do bazy danych SQL

Po możesz kupić pojemność usługi Azure SQL Database, zarezerwowane, rabat związany z rezerwacją jest automatycznie stosowany do bazy danych SQL, odpowiadający atrybuty i ilość rezerwacji. Rezerwacja obejmuje koszty operacji obliczeniowych, bazy danych SQL Database. Opłaty są naliczane dla oprogramowania, magazynu i sieci w normalnych stawek za użycie. Może obejmować koszty licencjonowania dla baz danych SQL za pomocą [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Zarezerwowane wystąpienia maszyn wirtualnych, można zobaczyć [discount zrozumieć Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Rabat związany z rezerwacją stosowane do bazy danych SQL

 Rabat w wysokości rezerwowanie pojemności bazy danych SQL jest stosowany do uruchamiania bazy danych SQL w systemie godzinowym. Zastrzeżenia, który można kupić jest dopasowywany do wykorzystanie mocy obliczeniowej, wyemitowane przez uruchamianie baz danych SQL. W przypadku baz danych SQL Database, które nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych baz danych SQL Database pasujących do atrybutów rezerwacji. Rabat można stosować do bazy danych SQL, które są uruchomione jednocześnie. Jeśli nie masz bazy danych SQL uruchamianą przez całą godzinę spełniającej atrybuty rezerwacji nie uzyskasz pełni czerpać korzyści płynące z rezerwacją dla danej godziny.

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
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Opis zastrzeżenia dla subskrypcji programu CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
