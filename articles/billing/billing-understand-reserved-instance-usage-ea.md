---
title: Zrozumieć użycie zastrzeżonego wystąpienia usługi Azure Enterprise — rozliczenia Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można odczytać użycie zrozumieć, jak stosowany jest Azure wystąpienia maszyny Wirtualnej zarezerwowana dla rejestracji w przedsiębiorstwie.
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
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Określić sposób użycia zastrzeżone wystąpienia platformy Azure dla rejestracji w przedsiębiorstwie
Zrozumieć użycie zastrzeżonego wystąpienia przy użyciu **ReservationId** z [strony zastrzeżenia](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i użycie pliku z [EA portal](https://ea.azure.com). Możesz również sprawdzić użycie zastrzeżonego wystąpienia w sekcji Podsumowanie użycia programu [EA portal](https://ea.azure.com).

>[!NOTE]
>Jeśli zakupiono zastrzeżone wystąpienia w kontekście rozliczeń płatności obejmujące zobacz [użycia wystąpienia zastrzeżone informacje z subskrypcji.](billing-understand-reserved-instance-usage.md)

W poniższej sekcji, że używasz maszyny Wirtualnej systemu Windows Standard_D1_v2 w Azji regionu Stanów Zjednoczonych i wystąpienia zastrzeżone informacje wygląda w poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Ilość |1|
|SKU | Standardowa_D1|
|Region | eastus |

## <a name="reserved-instance-application"></a>Zarezerwowane wystąpienie aplikacji

Część sprzętu maszyny Wirtualnej jest objęte ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutów wystąpienia zastrzeżone. Aby zobaczyć, jakie oprogramowanie systemu Windows nie jest objęte zastrzeżone wystąpienia, przejdź do koszty oprogramowania zastrzeżone wystąpień maszyn wirtualnych Azure, przejdź do [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Zarezerwowane użycia wystąpienia w formacie csv
EA użycia csv można pobrać z portalu EA. W pliku csv pobrany filtrować dodatkowe informacje, a następnie wpisz w Twojej **ReservationID**. Poniższy zrzut ekranu przedstawia pól związanych z wystąpieniem zastrzeżone:

![Csv Enterprise Agreement (EA) dla wystąpienia zastrzeżone Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** dodatkowych informacji reprezentuje pole zarezerwowane wystąpienie, które zostało użyte do zastosowania korzyści z maszyną wirtualną.
2. ConsumptionMeter jest MeterId dla maszyny Wirtualnej.
3. Jest to licznik rezerwacji kosztów $0, ponieważ koszt uruchomiona maszyna wirtualna została już wykonana przez wystąpienie zastrzeżone. 
4. Standard_D1 jest vCPU jednej maszyny Wirtualnej i Maszynie wirtualnej są wdrażane bez korzyści hybrydowe platformy Azure. W związku z tym ten licznik obejmuje dodatkowe opłaty oprogramowania systemu Windows. Zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md) Aby znaleźć licznika odpowiadający serii D 1 rdzeń maszyny Wirtualnej. Korzyści hybrydowe platformy Azure jest używana, to dodatkowe opłaty nie zostaną zastosowane.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Zarezerwowane użycia wystąpienia na stronie Podsumowanie użycia w portalu EA

Zastrzeżone wystąpienia użycia również zostaną wyświetlone w sekcji podsumowania użycia portalu EA: ![Podsumowanie użycia Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. To nie naliczane opłaty za składnik sprzętowy maszyny wirtualnej jest objętych zastrzeżone wystąpienia. 
2. Są naliczane oprogramowania systemu Windows, ponieważ korzyści hybrydowego Azure nie jest używany. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o wystąpieniach zastrzeżone Azure, zobacz następujące artykuły:

- [Oszczędzić pieniądze na maszynach wirtualnych z wystąpieniami zastrzeżone Azure](billing-save-compute-costs-reservations.md)
- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zastrzeżone](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.