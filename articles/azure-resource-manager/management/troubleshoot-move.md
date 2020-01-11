---
title: Rozwiązywanie problemów z błędami przenoszenia
description: Umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891273"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Rozwiązywanie problemów z przeniesieniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji

Ten artykuł zawiera sugestie ułatwiające rozwiązywanie problemów dotyczących przemieszczania zasobów.

## <a name="upgrade-a-subscription"></a>Uaktualnianie subskrypcji

Jeśli chcesz uaktualnić subskrypcję platformy Azure (np. przełączenie z bezpłatnej do płatnej zgodnie z rzeczywistym użyciem), musisz przekonwertować swoją subskrypcję.

* Aby uaktualnić z bezpłatnej wersji próbnej, zobacz [Zmień poziom subskrypcji bezpłatnej wersji próbnej lub programu Microsoft Imagine platformy Azure rozliczana według bieżącego użycia](../../billing/billing-upgrade-azure-subscription.md).
* Aby zmienić konto zgodnie z rzeczywistym użyciem, zobacz [zmienić subskrypcję platformy Azure rozliczana według bieżącego użycia do innej oferty](../../billing/billing-how-to-switch-azure-offer.md).

Jeśli nie można przekonwertować subskrypcję, [utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Wybierz **zarządzania subskrypcjami** jako typ problemu.

## <a name="service-limitations"></a>Ograniczenia usługi

Niektóre usługi wymagają dodatkowych zagadnień podczas przesuwania zasobów. Jeśli przenosisz następujące usługi, upewnij się, że zostały sprawdzone wskazówki i ograniczenia.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasyczny model wdrażania](./move-limitations/classic-model-move-limitations.md)
* [Sieć](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Duże żądania

Jeśli to możliwe, podział dużych przenosi do operacji przenoszenia oddzielne. Menedżer zasobów natychmiast zwraca błąd, jeśli w jednej operacji znajduje się więcej niż 800 zasobów. Jednak przenoszenia zasobów mniej niż 800 może również zakończyć się niepowodzeniem, przekroczeniem limitu czasu.

## <a name="resource-not-in-succeeded-state"></a>Zasób nie jest w stanie pomyślnym

Gdy zostanie wyświetlony komunikat o błędzie informujący o tym, że nie można przenieść zasobu, ponieważ nie jest on w stanie "powodzenie", może to być zależny zasób, który blokuje przeniesienie. Zazwyczaj kod błędu to **MoveCannotProceedWithResourcesNotInSucceededState**.

Jeśli źródłowa lub docelowa Grupa zasobów zawiera sieć wirtualną, Stany wszystkich zasobów zależnych dla sieci wirtualnej są sprawdzane podczas przenoszenia. Kontrola obejmuje te zasoby bezpośrednio i pośrednio zależne od sieci wirtualnej. Jeśli którykolwiek z tych zasobów jest w stanie niepowodzenia, przeniesienie jest zablokowane. Na przykład jeśli maszyna wirtualna, która używa sieci wirtualnej, zakończyła się niepowodzeniem, przenoszenie jest zablokowane. Przenoszenie jest blokowane nawet wtedy, gdy maszyna wirtualna nie jest jednym z przenoszonych zasobów i nie znajduje się w jednej z grup zasobów dla przeniesienia.

Po otrzymaniu tego błędu dostępne są dwie opcje. Przenieś zasoby do grupy zasobów, która nie ma sieci wirtualnej, lub [skontaktuj się z pomocą techniczną](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).
