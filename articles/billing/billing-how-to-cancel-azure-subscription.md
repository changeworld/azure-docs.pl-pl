---
title: Anuluj subskrypcję platformy Azure | Microsoft Docs
description: Opisuje, jak anulować subskrypcję platformy Azure, na przykład subskrypcję bezpłatnej wersji próbnej
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
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666447"
---
# <a name="cancel-your-azure-subscription"></a>Anulowanie subskrypcji platformy Azure

Możesz anulować subskrypcję platformy Azure w Azure Portal, jeśli nie potrzebujesz już subskrypcji. 

Przed anulowaniem subskrypcji:
* Utwórz kopię zapasową danych. Na przykład w przypadku przechowywania danych w usłudze Azure Storage lub SQL Pobierz kopię. Jeśli masz maszynę wirtualną, Zapisz jej obraz lokalnie.
* Zamknij usługi. Przejdź do [strony zasoby w portalu zarządzania](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)i **Zatrzymaj** wszystkie uruchomione maszyny wirtualne, aplikacje lub inne usługi.
* Rozważ Migrowanie danych. Zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).
* Usuń wszystkie zasoby i wszystkie grupy zasobów. Usunięcie ich jest wymagane, aby można było anulować subskrypcję. Każda grupa zasobów musi zostać usunięta pojedynczo. Podczas usuwania grupy zasobów należy potwierdzić usunięcie, wpisując nazwę grupy zasobów.
* Jeśli masz jakieś role niestandardowe odwołujące się do tej `AssignableScopes`subskrypcji w programie, należy zaktualizować te role niestandardowe, aby usunąć subskrypcję. Jeśli użytkownik spróbuje zaktualizować rolę niestandardową po anulowaniu subskrypcji, może wystąpić błąd. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rolami](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) niestandardowymi i [rolami niestandardowymi dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

Jeśli anulujesz płatny Plan pomocy technicznej platformy Azure, opłaty są naliczane za resztę okresu subskrypcji. Aby uzyskać więcej informacji, zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Anuluj subskrypcję w Azure Portal

1. Wybierz swoją subskrypcję na [stronie Subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Wybierz subskrypcję, którą chcesz anulować.
3. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Anuluj subskrypcję**.
    ![Zrzut ekranu, na którym jest wyświetlany przycisk Anuluj](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Postępuj zgodnie z monitami i Zakończ anulowanie.


## <a name="who-can-cancel-a-subscription"></a>Kto może anulować subskrypcję?

W poniższej tabeli opisano uprawnienia wymagane do anulowania subskrypcji.

|Typ subskrypcji     |Kto może anulować  |
|---------|---------|
|Subskrypcje utworzone podczas tworzenia konta na platformie Azure za pomocą witryny sieci Web systemu Azure. Na przykład po zarejestrowaniu się w celu uzyskania [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/)możesz korzystać [z stawek płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub jako [subskrybent programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrator konta, właściciele i współautorzy subskrypcji  |
|[Microsoft Umowa Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) i [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) — tworzenie i testowanie     |  Właściciel konta, właściciele i współautorzy subskrypcji       |
|Plan [platformy Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) i [Plan platformy Azure dla usługi DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Właściciele i współautorzy subskrypcji      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Co się stanie po anulowaniu subskrypcji?

Po anulowaniu rozliczania zostanie natychmiast zatrzymane. Anulowanie wyświetlania w portalu może być jednak potrwać do 10 minut. Jeśli anulujesz w połowie okresu rozliczeniowego, wyślemy końcową fakturę na datę typowej faktury po zakończeniu okresu.

Po anulowaniu usługi są one wyłączone. Oznacza to, że maszyny wirtualne zostaną cofnięte, tymczasowe adresy IP są zwalniane, a magazyn jest tylko do odczytu.

Czekamy na 90 dni przed trwałym usunięciem danych, na wypadek potrzeby uzyskania dostępu lub zmiany myśli. Nie naliczamy opłat za zachowywanie danych. Aby dowiedzieć się więcej, zobacz [Centrum zaufania firmy Microsoft — jak zarządzać danymi](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Ponowne aktywowanie subskrypcji

Jeśli anulujesz subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem, możesz [ją ponownie aktywować w centrum konta](billing-subscription-become-disable.md).

Jeśli subskrypcja nie jest subskrypcją z opcją płatność zgodnie z rzeczywistym użyciem, skontaktuj się z pomocą techniczną w ciągu 90 dni od anulowania, aby ponownie aktywować subskrypcję.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).
