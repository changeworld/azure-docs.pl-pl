---
title: Określić sposób użycia wystąpienia zastrzeżone Azure dla subskrypcji z - rozliczenia Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można odczytać użycie zrozumieć, jak stosowany jest zastrzeżone wystąpienia maszyny Wirtualnej Azure dla subskrypcji z.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 7bf4aea86d4d430c15d60a8d73365705ace18b5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z

Zrozumieć użycie zastrzeżonego wystąpienia maszyny Wirtualnej Azure przy użyciu ReservationId z [strony rezerwacji](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i użycie pliku z [portalu konta usługi Azure](https://account.azure.com).


>[!NOTE]
>W tym artykule nie ma zastosowania do EA klienci. Jeśli klient EA, zobacz [użycia zrozumieć wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie.](billing-understand-reserved-instance-usage-ea.md) Tym artykule przyjęto również zastosowana wystąpienia zarezerwowane do jednej subskrypcji. Jeśli wystąpienie zastrzeżone jest stosowane do więcej niż jedną subskrypcję, korzyści zastrzeżone wystąpienia mogą obejmować wiele plików csv użycia. 

W poniższej sekcji, że używasz maszyny Wirtualnej systemu Windows Standard_DS1_v2 w Azji regionu Stanów Zjednoczonych i wystąpienia zastrzeżone informacje wygląda w poniższej tabeli:

| Pole | Wartość |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Ilość |1|
|SKU | Standardowa_DS1_v2|
|Region | eastus |

## <a name="reserved-instance-application"></a>Zarezerwowane wystąpienie aplikacji

Część sprzętu maszyny Wirtualnej jest objęte ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutów wystąpienia zastrzeżone. Aby zobaczyć, jakie oprogramowanie systemu Windows nie jest objęte zastrzeżone wystąpienia, przejdź do [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Instrukcja sekcji pliku CSV
Tej sekcji z pliku CSV pokazuje całkowite użycie zastrzeżonego wystąpienia. Zastosuj filtr na pole podkategorii miernika, które zawiera "Rezerwacji-" i dane mogą wyglądać Poniższy zrzut ekranu: ![zrzut ekranu przedstawiający filtrowane szczegóły użycia wystąpienia zastrzeżone i opłat](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Wiersz rezerwacji podstawowa maszyna wirtualna ma łączną liczbę godzin, które są objęte zastrzeżone wystąpienia. Ten wiersz jest 0,00, ponieważ wystąpienie zastrzeżone obejmuje on. Windows rezerwacji Svr (1 rdzeń) wiersza obejmuje koszty oprogramowania systemu Windows.

### <a name="daily-usage-section-of-csv"></a>Dzienne użycie części csv
Odfiltruj dodatkowe informacje, a następnie wpisz w Twojej **identyfikator rezerwacji**. Poniższy zrzut ekranu przedstawia pól związanych z wystąpieniem zastrzeżone. 

![Zrzut ekranu przedstawiający dzienne szczegóły użycia i opłat](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** dodatkowych informacji pole jest zastrzeżony wystąpienie, które zostało użyte do zastosowania korzyści z maszyną wirtualną.
2. ConsumptionMeter jest identyfikatorem licznika dla maszyny Wirtualnej.
3. Wiersz rezerwacji podstawowej maszyny Wirtualnej miernika podkategorii reprezentuje wiersza kosztu $0 w sekcji instrukcji. Koszt tej maszyny Wirtualnej została już wykonana przez wystąpienie zastrzeżone.
4. To jest identyfikator licznika dla wystąpienia zastrzeżone. Koszt tego licznika jest $0. Żadnej maszyny Wirtualnej, które kwalifikują się do wystąpienia zastrzeżone ma to MeterId w formacie csv, aby uwzględnić koszty. 
5. Standard_DS1_v2 jest vCPU jednej maszyny Wirtualnej i Maszynie wirtualnej są wdrażane bez korzyści hybrydowe platformy Azure. W związku z tym ten licznik obejmuje dodatkowe opłaty oprogramowania systemu Windows. Zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md) Aby znaleźć licznika odpowiadający serii D 1 rdzeń maszyny Wirtualnej. Jeśli korzyści hybrydowe platformy Azure jest używana, to dodatkowe opłaty nie została zastosowana. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o wystąpieniach zastrzeżonych, zobacz następujące artykuły:

- [Oszczędzić pieniądze na maszynach wirtualnych z wystąpieniami zastrzeżone Azure](billing-save-compute-costs-reservations.md)
- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zastrzeżone](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
