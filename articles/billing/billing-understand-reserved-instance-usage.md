---
title: Poznawaj możliwości korzystania rezerwacje Azure rozliczana według bieżącego użycia subskrypcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odczytać Twojego użycia, aby zrozumieć sposób stosowania Azure rezerwacji dla Twojej subskrypcji zgodnie z rzeczywistym użyciem.
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
ms.openlocfilehash: 14bd7b61038bf938f7d370eaf6e16d71b5da43ca
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652539"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Opis rezerwacji platformy Azure dla Twojej subskrypcji zgodnie z rzeczywistym użyciem

Użyj ReservationId z [strony rezerwacji](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i pliku użycia z [portalu konta usługi Azure](https://account.azure.com) do oceny użycia rezerwacji.

Jeśli jesteś klientem z umową Enterprise Agreement, zobacz [użycia rezerwacji dla Twojej rejestracji Enterprise.](billing-understand-reserved-instance-usage-ea.md).

W tym artykule założono, że rezerwacja ma zostać zastosowana dla jednej subskrypcji. Jeśli rezerwacja ma zostać zastosowana do więcej niż jedną subskrypcję, korzyść z rezerwacji może obejmować wiele plików CSV użycia.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Użycie wystąpień zarezerwowanych maszyn wirtualnych

Poniższe sekcje założono, że działają maszyny Wirtualnej z systemem Windows standardowa_ds1_v2 w regionie wschodnim regionie USA i Twoje zarezerwowane maszyny Wirtualnej wystąpienia informacji wygląda poniższej tabeli:

| Pole | Wartość |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Ilość |1|
|SKU | Standardowa_DS1_v2|
|Region | eastus |

Część sprzętu maszyny Wirtualnej zostało opisane, ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutami rezerwacji. Aby zobaczyć, jakie oprogramowanie Windows nie jest objęta wystąpienia zarezerwowanego maszyny Wirtualnej, zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej platformy Azure w rezerwy](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Instrukcja części pliku CSV dla maszyn wirtualnych

Ta sekcja pliku CSV zawiera łączne użycie rezerwacji. Zastosuj filtr według **Podkategoria miernika** pola, które zawiera **"Rezerwacji-"**. Zostanie wyświetlona zawartość podobna Poniższy zrzut ekranu:

![Zrzut ekranu przedstawiający filtrowane rezerwacji szczegóły użycia i opłat](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

**Rezerwacja — podstawowa maszyna wirtualna** wiersz zawiera łączną liczbę godzin, które są objęte rezerwacji. Ten wiersz jest 0,00 USD, ponieważ rezerwacji zastosowanie tych rozwiązań. **Svr Rezerwacja Windows (1 rdzeń)** wiersza po awarii obejmuje koszt oprogramowania Windows.

### <a name="daily-usage-section-of-csv-file"></a>Dzienne użycie części pliku CSV

Filtrowanie według **dodatkowe informacje** i wpisz swoje **identyfikator rezerwacji**. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacji.

![Zrzut ekranu przedstawiający dzienny szczegółów użycia i opłat](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** w **dodatkowe informacje** pole jest rezerwacji, która jest stosowana do maszyny Wirtualnej.
2. **ConsumptionMeter** jest Identyfikatorem licznika dla maszyny Wirtualnej.
3. **Rezerwacja — podstawowa maszyna wirtualna** **Podkategoria miernika** wiersz reprezentuje koszt 0 USD w sekcji instrukcji. Koszt uruchamiania tej maszyny Wirtualnej została już wykonana przez rezerwacji.
4. **Identyfikator licznika** jest identyfikator miernika dla rezerwacji. Ten licznik kosztuje 0 USD. Ten identyfikator miernika pojawia się dla każdej maszyny Wirtualnej, która jest uprawniony do rabat związany z rezerwacją.
5. Standardowa_ds1_v2 jest jeden procesor wirtualny maszyny Wirtualnej i maszyna wirtualna jest wdrażana bez korzyści użycia hybrydowego platformy Azure. Tak ten licznik obejmuje dodatkowych opłat w oprogramowania Windows. Licznik odpowiadający serii D 1-rdzeniowe maszyny Wirtualnej, możesz znaleźć [koszty oprogramowania Windows wystąpień maszyny Wirtualnej platformy Azure w rezerwy](billing-reserved-instance-windows-software-costs.md). Jeśli masz korzyść użycia hybrydowego platformy Azure, ta dodatkowa opłata nie została zastosowana.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Użycie dla bazy danych SQL Database i Cosmos DB zastrzeżone rezerwacji wydajności

Poniższe sekcje użyć usługi Azure SQL Database jako przykład do opisania raport użycia. Te same czynności można użyć, można również pobrać użycia usługi Azure Cosmos DB.

Załóżmy, że używasz bazy danych SQL Gen 4 w regionie wschodnim regionie USA i rezerwacji swoje informacje wygląda poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Ilość |2|
|Product (Produkt)| Bazy danych SQL Database Gen 4 (2 rdzenie)|
|Region | eastus |

### <a name="statement-section-of-csv-file"></a>Instrukcja części pliku CSV

Filtrowanie według **użycie wystąpień zarezerwowanych** pomiaru nazwę, a następnie wybierz wymagane **kategoria licznika** — bazy danych Azure SQL lub usługi Azure Cosmos DB. Zostanie wyświetlona zawartość podobna Poniższy zrzut ekranu:

![Plik CSV dla bazy danych SQL wydajności rezerwowej](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

**Użycie wystąpień zarezerwowanych** wiersz zawiera sumę — podstawowe godziny objęte rezerwacji. Stawka jest 0 USD dla tej linii rezerwacji objętych kosztów.

### <a name="detail-section-of-csv-file"></a>Sekcja szczegółów pliku CSV

Filtrowanie według **dodatkowe informacje** i wpisz swoje **identyfikator rezerwacji**. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacji wydajności bazy danych SQL zastrzeżone.

![Plik CSV dla bazy danych SQL wydajności rezerwowej](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** w **dodatkowe informacje** pole jest zastosowany do zasobu bazy danych SQL rezerwacji pojemności bazy danych SQL zastrzeżone.
2. **ConsumptionMeter** jest identyfikator miernika zasobu bazy danych SQL.
3. **Identyfikator miernika** to licznik rezerwacji. Ten licznik kosztuje 0 USD. Wszystkie zasoby bazy danych SQL, które kwalifikują się do rabat związany z rezerwacją zawiera ten identyfikator miernika, w pliku CSV.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)
- [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat związany z rezerwacją](billing-understand-vm-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania Windows nie jest dołączony do rezerwacji](billing-reserved-instance-windows-software-costs.md)