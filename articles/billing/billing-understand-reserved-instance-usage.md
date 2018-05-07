---
title: Określić sposób użycia zastrzeżone wystąpienia platformy Azure dla subskrypcji z | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można odczytać użycie do aplikacji omówienie wystąpienia zarezerwowane dla subskrypcji płatność za rzeczywiste użycie
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
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 142e172b3f4ab4b88bb3733f70d5e0fb252854ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z

Zrozumieć użycie zastrzeżonego wystąpienia przy użyciu ReservationId z [strony rezerwacji](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) i użycie pliku z [portalu konta usługi Azure.](https://account.azure.com)


>[!NOTE]
>W tym artykule nie ma zastosowania do EA klienci. Jeśli klient EA, zobacz [użycia zrozumieć wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie.](billing-understand-reserved-instance-usage-ea.md) Tym artykule przyjęto również zastrzeżenia dotyczy jednej subskrypcji. Jeśli rezerwacji jest stosowane do więcej niż jedną subskrypcję, korzyści rezerwacji może obejmować wiele plików csv użycia. 

Dla następujących sekcji założono, że uruchamiasz Standard_DS1_v2 maszyny Wirtualnej systemu Windows w Azji regionu Stanów Zjednoczonych i zastrzeżenia Twoje informacje prawdopodobnie poniższej tabeli:

| Pole | Wartość |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Ilość |1|
|SKU | Standardowa_DS1_v2|
|Region | eastus |

## <a name="reservation-application"></a>Zastrzeżenie aplikacji

Część sprzętu maszyny Wirtualnej jest objęte, ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutów rezerwacji. Aby zobaczyć, jakie oprogramowanie systemu Windows nie jest objęte zastrzeżone wystąpienia, przejdź do [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Instrukcja sekcji pliku CSV
W tej części sieci csv zawiera łączne użycie Twojej rezerwacji. Zastosuj filtr na pole podkategorii miernika, które zawiera "Rezerwacji-" i dane mogą wyglądać Poniższy zrzut ekranu: ![bezpośredniego rezerwacji — instrukcja](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Wiersz rezerwacji podstawowa maszyna wirtualna ma łączną liczbę godzin, które są objęte rezerwacji. Ten wiersz jest 0,00, ponieważ wystąpienie zastrzeżone obejmuje on. Windows rezerwacji Svr (1 rdzeń) wiersza obejmuje koszty oprogramowania systemu Windows.

### <a name="daily-usage-section-of-csv"></a>Dzienne użycie części csv
Odfiltruj dodatkowe informacje, a następnie wpisz w rezerwacji identyfikatora Poniższy zrzut ekranu przedstawia pola powiązane rezerwacji. 

![Dzienne użycie opłat](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. ReservationId w polu informacje dodatkowe jest rezerwacji, które zostało użyte do zastosowania korzyści z maszyną wirtualną.
2. ConsumptionMeter jest identyfikatorem licznika dla maszyny Wirtualnej.
3. Wiersz podkategorii rezerwacji podstawowej maszyny Wirtualnej licznik reprezentuje wiersza kosztu $0 w sekcji instrukcji. Koszt tej maszyny Wirtualnej została już wykonana przez rezerwacji.
4. To jest identyfikator licznika dla rezerwacji. Koszt tego licznika jest $0. Żadnej maszyny Wirtualnej, które kwalifikują się do wystąpienia zastrzeżone ma to MeterId w formacie csv, aby uwzględnić koszty. 
5. Standard_DS1_v2 jest vCPU jednej maszyny Wirtualnej i Maszynie wirtualnej są wdrażane bez korzyści hybrydowe platformy Azure. W związku z tym ten licznik obejmuje dodatkowe opłaty oprogramowania systemu Windows. Zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md) Aby znaleźć licznika odpowiadający serii D 1 rdzeń maszyny Wirtualnej. Jeśli korzyści hybrydowe platformy Azure jest używana, to dodatkowe opłaty nie została zastosowana. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o zastrzeżonych wystąpień maszyn wirtualnych, zobacz następujące artykuły.

- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyny Wirtualnej](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zastrzeżonej maszyny wirtualnej](billing-manage-reserved-vm-instance.md)
- [Zaoszczędzić na maszynach wirtualnych z zarezerwowanych wystąpień maszyn wirtualnych](billing-save-compute-costs-reservations.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienie maszyny wirtualnej](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.