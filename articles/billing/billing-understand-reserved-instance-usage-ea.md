---
title: Określić sposób użycia Azure wystąpienia zarezerwowane dla przedsiębiorstwa | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: d6e8b2544f919abeb7fde0e37fc12bd29f0171ef
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064011"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Określić sposób użycia Azure wystąpienia zarezerwowane dla rejestracji w przedsiębiorstwie
Zrozumieć użycie zastrzeżonego wystąpienia przy użyciu **ReservationId** z [strony zastrzeżenia](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i użycie pliku z [EA portal](https://ea.azure.com). Możesz również sprawdzić użycie zastrzeżonego wystąpienia w sekcji Podsumowanie użycia programu [EA portal](https://ea.azure.com).

>[!NOTE]
>Jeśli zakupiono zastrzeżone wystąpienia w kontekście rozliczeń płatności obejmujące zobacz [omówienie zastrzeżone użycia wystąpienia z subskrypcji.](billing-understand-reserved-instance-usage.md)

Dla następujących sekcji założono, że uruchamiasz Standard_D1_v2 maszyny Wirtualnej systemu Windows w Azji regionu Stanów Zjednoczonych i wystąpienia zastrzeżone Twoje informacje prawdopodobnie poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Ilość |1|
|SKU | Standardowa_D1|
|Region | eastus |

## <a name="reserved-instance-application"></a>Zarezerwowane wystąpienie aplikacji

Część sprzętu maszyny Wirtualnej jest objęte ponieważ wdrożonej maszyny Wirtualnej jest zgodna z atrybutów wystąpienia zastrzeżone. Aby zobaczyć, jakie oprogramowanie systemu Windows nie jest objęte zastrzeżone wystąpienia, przejdź do koszty oprogramowania zastrzeżone wystąpień maszyn wirtualnych Azure, przejdź do [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Użycie zarezerwowanych wystąpienia w formacie csv
EA użycia csv można pobrać z portalu EA. W pliku csv pobrany filtrować dodatkowe informacje, a następnie wpisz w Twojej **ReservationID**. Poniższy zrzut ekranu przedstawia pól związanych z wystąpieniem zastrzeżone:

![Wystąpienie zastrzeżone csv Enterprise Agreement (EA) dla platformy Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** dodatkowych informacji reprezentuje pole zarezerwowane wystąpienie, które zostało użyte do zastosowania korzyści z maszyną wirtualną.
2. ConsumptionMeter jest MeterId dla maszyny Wirtualnej.
3. Jest to licznik rezerwacji kosztów $0, ponieważ koszt uruchomiona maszyna wirtualna została już wykonana przez wystąpienie zastrzeżone. 
4. Standard_D1 jest vCPU jednej maszyny Wirtualnej i Maszynie wirtualnej są wdrażane bez korzyści hybrydowe platformy Azure. W związku z tym ten licznik obejmuje dodatkowe opłaty oprogramowania systemu Windows. Zobacz [koszty oprogramowania Windows wystąpień maszyny Wirtualnej rezerwy Azure.](billing-reserved-instance-windows-software-costs.md) Aby znaleźć licznika odpowiadający serii D 1 rdzeń maszyny Wirtualnej. Korzyści hybrydowe platformy Azure jest używana, to dodatkowe opłaty nie zostaną zastosowane.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Użycie zarezerwowanych wystąpienia na stronie Podsumowanie użycia w portalu EA

Użycie zarezerwowanych wystąpienia również zostaną wyświetlone w sekcji podsumowania użycia portalu EA: ![Podsumowanie użycia Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. To nie naliczane opłaty za składnik sprzętowy maszyny wirtualnej jest objętych zastrzeżone wystąpienia. 
2. Są naliczane oprogramowania systemu Windows, ponieważ korzyści hybrydowego Azure nie jest używany. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o wystąpieniach zastrzeżone platformy Azure, zobacz następujące artykuły:

- [Co to są zastrzeżone wystąpień maszyn wirtualnych Azure?](billing-save-compute-costs-reservations.md)
- [Przedpłaty dla maszyn wirtualnych z wystąpień zastrzeżone maszyna wirtualna platformy Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Zarządzanie wystąpieniami zarezerwowane na platformie Azure](billing-manage-reserved-vm-instance.md)
- [Zrozumienie, jak stosowany jest rabat zastrzeżone wystąpienia](billing-understand-vm-reservation-charges.md)
- [Określić sposób użycia wystąpienia zarezerwowane dla Twojej subskrypcji z](billing-understand-reserved-instance-usage.md)
- [Koszty oprogramowania systemu Windows nie jest dołączony do wystąpienia zastrzeżone](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli masz więcej pytań, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.