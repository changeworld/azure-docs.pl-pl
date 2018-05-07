---
title: Określić sposób użycia zastrzeżone wystąpienia platformy Azure dla przedsiębiorstwa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można odczytać użycie zrozumienie aplikacji wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie.
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
ms.openlocfilehash: cf79926e6497c50156f2a0191997ca06bc605c16
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Określić sposób użycia wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie
Zrozumieć użycie zastrzeżonego wystąpienia przy użyciu ReservationId z [strony rezerwacji](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) i użycie pliku z [EA portalu.](https://ea.azure.com) Możesz również sprawdzić użycia rezerwacji w sekcji Podsumowanie użycia programu [EA portalu.](https://ea.azure.com)

>[!NOTE]
>Jeśli zakupiono rezerwacji w z kontekstu rozliczeń, zobacz [użycia wystąpienia zastrzeżone informacje z subskrypcji.](billing-understand-reserved-instance-usage.md)

Dla następujących sekcji założono, że uruchamiasz Standard_D1_v2 maszyny Wirtualnej systemu Windows w Azji regionu Stanów Zjednoczonych i zastrzeżenia Twoje informacje prawdopodobnie poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Ilość |1|
|SKU | Standardowa_D1|
|Region | eastus |

## <a name="reservation-application"></a>Zastrzeżenie aplikacji

Część sprzętu maszyny Wirtualnej jest objęte, ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutów rezerwacji. Aby zobaczyć, jakie oprogramowanie systemu Windows nie jest objęte zastrzeżone wystąpienia, przejdź do koszty oprogramowania zastrzeżone wystąpień maszyn wirtualnych Azure, przejdź do [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reservation-usage-in-csv"></a>Użycie rezerwacji w formacie csv
EA użycia csv można pobrać z portalu EA. W pliku csv pobrany filtrować dodatkowe informacje, a następnie wpisz w rezerwacji identyfikatora Poniższy zrzut ekranu przedstawia pola powiązane zastrzeżenie:

![EA csv zarezerwowana wystąpienia](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. ReservationId w polu informacje dodatkowe reprezentuje rezerwacji, które zostało użyte do zastosowania korzyści z maszyną wirtualną.
2. ConsumptionMeter jest MeterId dla maszyny Wirtualnej.
3. Jest to ReservationMeter kosztów $0, ponieważ koszt uruchomiona maszyna wirtualna została już wykonana przez rezerwacji. 
4. Standard_D1 jest vCPU jednej maszyny Wirtualnej i Maszynie wirtualnej są wdrażane bez korzyści hybrydowe platformy Azure. W związku z tym ten licznik obejmuje dodatkowe opłaty oprogramowania systemu Windows. Zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md) Aby znaleźć licznika odpowiadający serii D 1 rdzeń maszyny Wirtualnej. Korzyści hybrydowe platformy Azure jest używana, to dodatkowe opłaty nie zostaną zastosowane.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Użycie rezerwacji na stronie Podsumowanie użycia w portalu EA

Zastrzeżone wystąpienia użycia również zostaną wyświetlone w sekcji podsumowania użycia portalu EA: ![Podsumowanie użycia EA](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. To nie naliczane opłaty za składnik sprzętowy maszyny wirtualnej jest objętych zastrzeżone wystąpienia. 
2. Są naliczane oprogramowania systemu Windows, ponieważ korzyści hybrydowego Azure nie jest używany. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o zastrzeżonych wystąpień maszyn wirtualnych, zobacz następujące artykuły.

- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyny Wirtualnej](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zastrzeżonej maszyny wirtualnej](billing-manage-reserved-vm-instance.md)
- [Zaoszczędzić na maszynach wirtualnych z zarezerwowanych wystąpień maszyn wirtualnych](billing-save-compute-costs-reservations.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienie maszyny wirtualnej](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.