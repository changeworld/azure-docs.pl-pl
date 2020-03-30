---
title: Rozwiązywanie problemów z błędami przenoszenia
description: Usługa Azure Resource Manager umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891273"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Rozwiązywanie problemów z przenoszeniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji

Ten artykuł zawiera sugestie ułatwiające rozwiązywanie problemów podczas przenoszenia zasobów.

## <a name="upgrade-a-subscription"></a>Uaktualnianie subskrypcji

Jeśli rzeczywiście chcesz uaktualnić subskrypcję platformy Azure (na przykład przejście z bezpłatnego do płatności zgodnie z rzeczywistym), musisz przekonwertować subskrypcję.

* Aby uaktualnić bezpłatną wersję próbną, zobacz [Uaktualnianie bezpłatnej wersji próbnej lub subskrypcji platformy Microsoft Imagine Azure do programu Płatności zgodnie z rzeczywistym przejściem.](../../billing/billing-upgrade-azure-subscription.md)
* Aby zmienić konto płatności zgodnie z rzeczywistym przejściem, zobacz [Zmienianie subskrypcji usługi Azure Pay-As-You-Go na inną ofertę.](../../billing/billing-how-to-switch-azure-offer.md)

Jeśli nie możesz przekonwertować subskrypcji, [utwórz żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Wybierz **pozycję Zarządzanie subskrypcjami** dla typu problemu.

## <a name="service-limitations"></a>Ograniczenia usług

Niektóre usługi wymagają dodatkowych zagadnień podczas przenoszenia zasobów. Jeśli przenosisz następujące usługi, sprawdź wskazówki i ograniczenia.

* [Usługi aplikacji](./move-limitations/app-service-move-limitations.md)
* [Usługa Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasyczny model wdrażania](./move-limitations/classic-model-move-limitations.md)
* [Obsługa sieci](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Maszyny wirtualne](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Duże żądania

Jeśli to możliwe, podziel duże ruchy na oddzielne operacje przenoszenia. Menedżer zasobów natychmiast zwraca błąd, gdy istnieje więcej niż 800 zasobów w jednej operacji. Jednak przeniesienie mniej niż 800 zasobów może również zakończyć się niepowodzeniem przez limit czasu.

## <a name="resource-not-in-succeeded-state"></a>Zasób nie jest w stanie powiodło się

Po otrzymaniu komunikatu o błędzie, który wskazuje, że zasób nie może zostać przeniesiony, ponieważ nie jest w stanie powiódł się, może być faktycznie zasobem zależnym, który blokuje przeniesienie. Zazwyczaj kod błędu jest **MoveCannotProceedWithResourcesNotInSucceededState**.

Jeśli źródło lub docelowa grupa zasobów zawiera sieć wirtualną, stany wszystkich zasobów zależnych dla sieci wirtualnej są sprawdzane podczas przenoszenia. Kontrola obejmuje te zasoby bezpośrednio i pośrednio zależne od sieci wirtualnej. Jeśli którykolwiek z tych zasobów są w stanie awarii, przeniesienie jest zablokowany. Na przykład jeśli maszyna wirtualna, która korzysta z sieci wirtualnej nie powiodło się, ruch jest zablokowany. Przeniesienie jest blokowane nawet wtedy, gdy maszyna wirtualna nie jest jednym z zasobów są przenoszone i nie znajduje się w jednej z grup zasobów dla przeniesienia.

Po wyświetleniu tego błędu dostępne są dwie opcje. Przenieś zasoby do grupy zasobów, która nie ma sieci wirtualnej, albo [skontaktuj się z pomocą techniczną](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).
