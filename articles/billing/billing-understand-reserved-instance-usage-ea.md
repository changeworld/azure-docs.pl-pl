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
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: daa7f6a116578fa8d1f2b5bf825a6f4cd48f7f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370698"
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

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Użycie dla bazy danych SQL Database i Cosmos DB zastrzeżone rezerwacji wydajności

Poniższe sekcje użyć usługi Azure SQL Database jako przykład do opisania raport użycia. Te same czynności można użyć, można również pobrać użycia usługi Azure Cosmos DB.

Załóżmy, że używasz bazy danych SQL Gen 4 w regionie wschodnim regionie USA i rezerwacji swoje informacje wygląda poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Ilość |2|
|Product (Produkt)| Bazy danych SQL Database Gen 4 (2 rdzenie)|
|Region | eastus |

### <a name="usage-in-csv-file"></a>Użycie w pliku CSV

Filtrowanie według **dodatkowe informacje** i wpisz swoje **identyfikator rezerwacji**i wybierz wymagany **kategoria licznika** — bazy danych Azure SQL lub usługi Azure Cosmos DB. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacji.

![Wydajności rezerwowej csv Enterprise Agreement (EA) dla usługi SQL Database](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **ReservationId** w **dodatkowe informacje** pole jest rezerwacji, która jest stosowana do zasobu bazy danych SQL.
2. **ConsumptionMeter** jest identyfikator miernika zasobu bazy danych SQL.
3. **Identyfikator licznika** to licznik rezerwacji kosztów 0 USD. Dowolnego zasobu bazy danych SQL, które kwalifikują się do rezerwacji zawiera ten identyfikator miernika, w pliku CSV.

## <a name="usage-summary-page-in-enterprise-portal"></a>Strona podsumowania użycia w witrynie Enterprise portal

Użycie platformy Azure rezerwacji również zostaną wyświetlone w sekcji podsumowania użycia witryny Enterprise portal: ![Podsumowanie użycia Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Składnik sprzętowy maszyny wirtualnej nie są opłata, ponieważ jest ona objęta rezerwacji. Dla rezerwacji bazy danych SQL, zobacz wiersz z **nazwa usługi** jako baza danych SQL Azure zastrzeżone monitorowanie użycia pojemności.
2. W tym przykładzie nie ma korzyści użycia hybrydowego platformy Azure, opłaty są naliczane dla oprogramowania Windows używana z maszyną Wirtualną.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)

