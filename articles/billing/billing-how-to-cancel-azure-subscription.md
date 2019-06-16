---
title: Anulowanie subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: Opis sposobu anulowania subskrypcji platformy Azure, takich jak subskrypcji bezpłatnej wersji próbnej
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480222"
---
# <a name="cancel-your-subscription-for-azure"></a>Anulowanie subskrypcji platformy Azure

Tylko subskrypcji platformy Azure [administratora konta](billing-subscription-transfer.md#whoisaa) może anulować subskrypcję platformy Azure. Administrator subskrypcji platformy Azure może także [przypisać innego użytkownika jako administratora subskrypcji programu](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription) tak, aby ich może anulować subskrypcję. Po anulowaniu subskrypcji kończy się dostępu do zasobów i usług platformy Azure.

Zanim anulujesz subskrypcję:

* Utwórz kopię zapasową danych. Na przykład jeśli one przechowywanie danych w usłudze Azure storage i SQL, należy pobrać kopię. Jeśli masz maszynę wirtualną, Zapisz obraz go lokalnie.
* Zamknij usługi. Przejdź do [zasoby strony w portalu zarządzania](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), i **zatrzymać** dowolne uruchomione maszyny wirtualne, aplikacje lub innych usług.
* Należy rozważyć migrację danych. Zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).
* Należy usunąć wszystkie zasoby oraz wszystkie grupy zasobów. Usuwanie, jest wymagany, zanim anulujesz subskrypcję. Każdej grupie zasobów, muszą zostać usunięte osobno. Podczas usuwania grupy zasobów musisz potwierdzić usunięcie, wpisując nazwę grupy zasobów.
* W przypadku ról niestandardowych, które odwołują się tej subskrypcji w `AssignableScopes`, należy zaktualizować te role niestandardowe, aby usunąć subskrypcję. Próba aktualizacji roli niestandardowej po anulowaniu subskrypcji możesz otrzymać błąd. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów za pomocą ról niestandardowych](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) i [niestandardowych ról dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

Jeśli anulujesz płatnego planu pomocy technicznej systemu Azure, nadal są rozliczane w pozostałej części okres obowiązywania subskrypcji. Aby uzyskać więcej informacji, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Anulowanie subskrypcji przy użyciu witryny Azure portal

1. Wybierz swoją subskrypcję z [strony subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wybierz subskrypcję, której chcesz anulować.
3. Wybierz **Przegląd**, a następnie wybierz pozycję **anulować subskrypcję**.

    ![Zrzut ekranu przedstawiający przycisk Anuluj](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Postępuj zgodnie z instrukcjami i zakończenia anulowania.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Co się stanie po mogę anulować moją subskrypcję?

Po anulowaniu, rozliczanie kończy się natychmiast. Jednak może potrwać do 10 minut anulowania wyświetlić w portalu. Jeśli anulujesz w trakcie okresu rozliczeniowego, możemy wysyłać łączny rachunek na Data faktury typowe po upływie okresu.

Po anulowaniu, Twoje usługi zostały wyłączone. Oznacza to maszyny wirtualne są dezalokowany tymczasowe adresy IP są zwalniane, i magazynu jest tylko do odczytu.

Poczekamy 90 dni przed trwałe usuwanie danych w przypadku, gdy trzeba uzyskać do niego dostęp, czy zmiany zdania. Nie opłata przechowywania danych. Aby dowiedzieć się więcej, zobacz [Microsoft Trust Center — jak Zarządzamy danych](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Ponowne aktywowanie subskrypcji

Jeśli anulujesz subskrypcję z płatnością za rzeczywiste użycie przypadkowo, możesz to zrobić [ją aktywować ponownie w Centrum kont](billing-subscription-become-disable.md).

Jeśli Twoja subskrypcja nie jest rozliczana według bieżącego użycia, skontaktuj się z działem pomocy technicznej w ciągu 90 dni od anulowania, aby ponownie aktywować swoją subskrypcję.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
