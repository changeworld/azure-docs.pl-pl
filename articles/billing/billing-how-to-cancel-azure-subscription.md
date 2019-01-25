---
title: Anulowanie subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: Opis sposobu anulowania subskrypcji platformy Azure, takich jak subskrypcji bezpłatnej wersji próbnej
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: banders
ms.openlocfilehash: b0d5dd5fb29c8915e2c660b8647cb0148bce1399
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901517"
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
2. Wybierz subskrypcję, której chcesz anulować.
3. Wybierz **Przegląd**, a następnie wybierz pozycję **anulować subskrypcję**.

    ![Zrzut ekranu przedstawiający przycisk Anuluj](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Postępuj zgodnie z instrukcjami i zakończenia anulowania.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Co się stanie po mogę anulować moją subskrypcję?

Anulowanie, rozliczanie kończy się natychmiast. Jednak może potrwać do 10 minut anulowania wyświetlić w portalu.

Po tym usługi zostaną wyłączone. Oznacza to maszyny wirtualne są dezalokowany tymczasowe adresy IP są zwalniane, i magazynu jest tylko do odczytu.

Jeśli anulujesz w trakcie okresu rozliczeniowego, możemy wysyłać łączny rachunek na Data faktury typowe po upływie okresu. 

Poczekamy 90 dni przed trwałe usuwanie danych w przypadku, gdy trzeba uzyskać do niego dostęp, czy zmiany zdania. Nie opłata przechowywania danych. Aby dowiedzieć się więcej, zobacz [Microsoft Trust Center — jak Zarządzamy danych](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Ponowne aktywowanie subskrypcji

Jeśli anulujesz subskrypcję z płatnością za rzeczywiste użycie przypadkowo, możesz to zrobić [ją aktywować ponownie w Centrum kont](billing-subscription-become-disable.md).

Jeśli Twoja subskrypcja nie jest rozliczana według bieżącego użycia, skontaktuj się z działem pomocy technicznej w ciągu 90 dni od anulowania, aby ponownie aktywować swoją subskrypcję.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
