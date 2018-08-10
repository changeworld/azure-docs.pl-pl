---
title: Opis zastrzeżenia platformy Azure dla przedsiębiorstw | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odczytać Twojego użycia, aby zrozumieć, jak zastosowano Azure zastrzeżenie dla Twojej rejestracji Enterprise.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: 5ce0103315f297996ed3f3bd88b5e53558e22e14
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628236"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Opis zastrzeżenia platformy Azure dla Twojej rejestracji Enterprise

Użyj **ReservationId** z [strony Rezerwacja](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i pliku użycia z [portalu EA](https://ea.azure.com) do oceny użycia rezerwacji. Możesz też sprawdzić użycie rezerwacji w sekcji podsumowania użycia programu [portalu EA portal](https://ea.azure.com).

Jeśli zakupiono rezerwacji w kontekście rozliczeń zgodnie z rzeczywistym użyciem, zobacz [użycia rezerwacji dla Twojej subskrypcji zgodnie z rzeczywistym użyciem.](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Użycie wystąpień zarezerwowanych maszyn wirtualnych

Poniższe sekcje założono, że działają maszyny Wirtualnej z systemem Windows maszyna wirtualna Standard_D1_v2 w regionie wschodnim regionie USA i rezerwacji swoje informacje wygląda poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Ilość |1|
|SKU | Standardowa_D1|
|Region | eastus |

Część sprzętu maszyny Wirtualnej zostało opisane, ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutami rezerwacji. Aby zobaczyć, jakie oprogramowanie Windows nie jest objęta rezerwacji, zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej platformy Azure w rezerwy](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Użycie w pliku CSV zarezerwowanych wystąpień maszyn wirtualnych

Plik CSV użycia Enterprise można pobrać z witryny Enterprise portal. W pliku CSV, odfiltruj **dodatkowe informacje** i wpisz swoje **ReservationID**. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacji:

![Csv Enterprise Agreement (EA) dla platformy Azure rezerwacji](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** w **dodatkowe informacje** reprezentuje pole rezerwacji, która jest stosowana do maszyny Wirtualnej.
2. **ConsumptionMeter** jest Identyfikatorem licznika dla maszyny Wirtualnej.
3. **Identyfikator licznika** to licznik rezerwacji kosztów 0 USD. Koszt uruchomionej maszyny Wirtualnej jest płatne według wystąpienia zarezerwowanego maszyny Wirtualnej.
4. Maszyna wirtualna Standard_D1 jest jeden procesor wirtualny maszyny Wirtualnej i maszyna wirtualna jest wdrażana bez korzyści użycia hybrydowego platformy Azure. Więc ten licznik obejmuje dodatkowych opłat w oprogramowania Windows. Licznik odpowiadający serii D 1-rdzeniowe maszyny Wirtualnej, możesz znaleźć [koszty oprogramowania Windows wystąpień maszyny Wirtualnej platformy Azure w rezerwy](billing-reserved-instance-windows-software-costs.md).  Jeśli masz korzyść użycia hybrydowego platformy Azure, nie jest stosowana ta dodatkowa opłata.

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>Użycie usługi SQL Database zastrzeżone rezerwacji wydajności

Poniższe sekcje założono, że używasz bazy danych SQL Gen 4 w regionie wschodnim regionie USA i rezerwacji swoje informacje wygląda poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Ilość |2|
|Product (Produkt)| Bazy danych SQL Database Gen 4 (2 rdzenie)|
|Region | eastus |

### <a name="usage-in-csv-file-for-sql-database-reserved-capacity"></a>Wydajności rezerwowej użycia w pliku CSV dla bazy danych SQL

Filtrowanie według **dodatkowe informacje** i wpisz swoje **identyfikator rezerwacji**. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacji.

![Wydajności rezerwowej csv Enterprise Agreement (EA) dla usługi SQL Database](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **ReservationId** w **dodatkowe informacje** pole jest rezerwacji, która jest stosowana do zasobu bazy danych SQL.
2. **ConsumptionMeter** jest identyfikator miernika zasobu bazy danych SQL.
3. **Identyfikator licznika** to licznik rezerwacji kosztów 0 USD. Dowolnego zasobu bazy danych SQL, które kwalifikują się do rezerwacji zawiera ten identyfikator miernika, w pliku CSV.

## <a name="usage-summary-page-in-enterprise-portal"></a>Strona podsumowania użycia w witrynie Enterprise portal

Użycie platformy Azure rezerwacji również zostaną wyświetlone w sekcji podsumowania użycia witryny Enterprise portal: ![Podsumowanie użycia Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Składnik sprzętowy maszyny wirtualnej nie są opłata, ponieważ jest ona objęta rezerwacji. Dla rezerwacji bazy danych SQL, zobacz wiersz z **nazwa usługi** jako baza danych SQL Azure zastrzeżone monitorowanie użycia pojemności.
2. W tym przykładzie nie ma korzyści użycia hybrydowego platformy Azure, opłaty są naliczane dla oprogramowania Windows używana z maszyną Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dotyczące maszyn wirtualnych przy użyciu wystąpienia zarezerwowane maszyn wirtualnych platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zapłać z góry za zasoby obliczeniowe bazy danych SQL Database o pojemności usługi Azure SQL Database, zarezerwowane](../sql-database/sql-database-reserved-capacity.md) 
- [Zarządzanie zastrzeżeniami platformy Azure](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.