---
title: Anulowanie subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: Opis sposobu anulowania subskrypcji platformy Azure, takich jak subskrypcji bezpłatnej wersji próbnej
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: genli
ms.openlocfilehash: 0bd0fca54ae5bf4292564a15adf38b7586768450
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145455"
---
# <a name="cancel-your-subscription-for-azure"></a>Anulowanie subskrypcji platformy Azure

Możesz anulować subskrypcję platformy Azure jako [administratora konta](billing-subscription-transfer.md#whoisaa). Po anulowaniu subskrypcji kończy się dostępu do zasobów i usług platformy Azure.

Zanim anulujesz subskrypcję:

* Utwórz kopię zapasową danych. Na przykład jeśli one przechowywanie danych w usłudze Azure storage i SQL, należy pobrać kopię. Jeśli masz maszynę wirtualną, Zapisz obraz go lokalnie.
* Zamknij usługi. Przejdź do [zasoby strony w portalu zarządzania](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), i **zatrzymać** dowolne uruchomione maszyny wirtualne, aplikacje lub innych usług.
* Należy rozważyć migrację danych. Zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

Jeśli anulujesz płatnego planu pomocy technicznej systemu Azure, nadal są rozliczane w pozostałej części okres obowiązywania subskrypcji. Aby uzyskać więcej informacji, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Anulowanie subskrypcji przy użyciu witryny Azure portal

1. Wybierz swoją subskrypcję z [strony subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz anulować, a następnie kliknij przycisk **anulować subskrypcję**.

    ![Zrzut ekranu przedstawiający przycisk Anuluj](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Postępuj zgodnie z instrukcjami i zakończenia anulowania.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Co się stanie po mogę anulować moją subskrypcję?

Anulowanie, rozliczanie kończy się natychmiast. Jednak może potrwać do 10 minut, zanim show anulowania w portalu.

Po tym usługi zostaną wyłączone. Oznacza to maszyny wirtualne są dezalokowany tymczasowe adresy IP są zwalniane, i magazynu jest tylko do odczytu.

Jeśli anulujesz w trakcie okresu rozliczeniowego, możemy wysyłać łączny rachunek na Data faktury typowe po upływie okresu. 

Poczekamy 90 dni przed trwałe usuwanie danych w przypadku, gdy trzeba uzyskać do niego dostęp, czy zmiany zdania. Nie opłata przechowywania danych. Aby dowiedzieć się więcej, zobacz [Microsoft Trust Center — jak Zarządzamy danych](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Ponowne aktywowanie subskrypcji

Jeśli anulujesz subskrypcję z płatnością za rzeczywiste użycie przypadkowo, możesz to zrobić [ją aktywować ponownie w Centrum kont](billing-subscription-become-disable.md).

Jeśli Twoja subskrypcja nie jest rozliczana według bieżącego użycia, skontaktuj się z działem pomocy technicznej w ciągu 90 dni od anulowania, aby ponownie aktywować swoją subskrypcję.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal masz pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
