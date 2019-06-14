---
title: Zrozumienie rabat rezerwacji dla baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rabat związany z rezerwacją jest stosowany do uruchamiania bazy danych SQL Azure.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370291"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Jak rabat związany z rezerwacją jest stosowany do bazy danych SQL Azure

Po możesz kupić pojemność usługi Azure SQL Database, zarezerwowane, rabat związany z rezerwacją jest automatycznie stosowany do bazy danych SQL, odpowiadający atrybuty i ilość rezerwacji. Rezerwacja obejmuje koszty operacji obliczeniowych, bazy danych SQL Database. Opłaty są naliczane dla oprogramowania, magazynu i sieci w normalnych stawek za użycie. Może obejmować koszty licencjonowania dla baz danych SQL za pomocą [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Zarezerwowane wystąpienia maszyn wirtualnych, można zobaczyć [discount zrozumieć Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Jaki jest stosowany rabat związany z rezerwacją

Rabat związany z rezerwacją jest "*Użyj it lub utracić — it*". Tak Jeśli nie masz żadnych godzinę dopasowywania zasobów, następnie traci ilość rezerwacji dla danej godziny. Nie można wykonać nieużywane zastrzeżone godzin przekazywania.

Podczas zamykania zasobem rabat związany z rezerwacją jest automatycznie stosuje do innego zasobu zgodnego z określonego zakresu. Jeśli żadne pasujące zasoby znajdują się w określonym zakresie, a następnie są zarezerwowane godzin *utracone*.

## <a name="discount-applied-to-sql-databases"></a>Rabatu zastosowanego do bazy danych SQL

 Rabat w wysokości rezerwowanie pojemności bazy danych SQL jest stosowany do uruchamiania bazy danych SQL w systemie godzinowym. Zastrzeżenia, który można kupić jest dopasowywany do wykorzystanie mocy obliczeniowej, wyemitowane przez uruchamianie baz danych SQL. W przypadku baz danych SQL Database, które nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych baz danych SQL Database pasujących do atrybutów rezerwacji. Rabat można stosować do bazy danych SQL, które są uruchomione jednocześnie. Jeśli nie masz bazy danych SQL uruchamianą przez całą godzinę spełniającej atrybuty rezerwacji nie uzyskasz pełni czerpać korzyści płynące z rezerwacją dla danej godziny.

W poniższych przykładach pokazano, jak stosuje rabat w wysokości rezerwowanie pojemności bazy danych SQL w zależności od liczby rdzeni zakupiono i kiedy są one uruchamiane.

- Scenariusz 1: Możesz kupić pojemności bazy danych SQL zastrzeżone dla 8-rdzeniowe bazy danych SQL. Możesz uruchomić 16 rdzeni SQL Database, która odpowiada pozostałe atrybuty rezerwacji. Opłaty są naliczane zgodnie z rzeczywistym użyciem cena 8 rdzeni wykorzystanie mocy obliczeniowej bazy danych SQL. Otrzymujesz rabat związany z rezerwacją za jedną godzinę 8-rdzeniowe wykorzystanie mocy obliczeniowej bazy danych SQL.

Pozostałe tych przykładów założono, że pojemność zastrzeżone bazy danych SQL, którego kupujesz jest dla 16-rdzeniowe bazy danych SQL, i pozostałe atrybuty rezerwacji dopasowania uruchomionej bazy danych SQL.

- Scenariusz 2: Możesz uruchomić dwie bazy danych SQL z 8 rdzeniami każda na godzinę. Rabat związany z rezerwacją 16 rdzeni jest stosowany do obliczeń użycie dla obu 8 rdzeni baz danych SQL.
- Scenariusz 3: Uruchom jedno 16 rdzeń bazy danych SQL z 13: 00 do 13:30:00. Możesz uruchomić inny 16-rdzeniowe bazy danych SQL z 1:30 do 14: 00. Oba są objęte rabat związany z rezerwacją.
- Scenariusz 4: Uruchom jedno 16 rdzeń bazy danych SQL z 13: 00 do 1:45 pm. Możesz uruchomić inny 16-rdzeniowe bazy danych SQL z 1:30 do 14: 00. Opłaty są naliczane zgodnie z rzeczywistym użyciem ceny 15-minutowy nachodzące na siebie. Rabat związany z rezerwacją dotyczy wykorzystanie mocy obliczeniowej w pozostałym czasie.

Aby zrozumieć i wyświetlić aplikację Azure rezerwacji w rozliczeniach raporty użycia, zobacz [użycia rezerwacji Understand Azure](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Opis zastrzeżenia dla subskrypcji programu CSP](/partner-center/azure-reservations)
