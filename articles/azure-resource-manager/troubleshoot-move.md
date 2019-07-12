---
title: Rozwiązywanie problemów z błędami podczas przenoszenia zasobów platformy Azure do nowej subskrypcji lub grupy zasobów
description: Umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723469"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Rozwiązywanie problemów z przenoszenia zasobów platformy Azure do nowej grupy zasobów lub subskrypcji

Ten artykuł zawiera sugestie ułatwiające rozwiązywanie problemów podczas przenoszenia zasobów.

## <a name="upgrade-a-subscription"></a>Uaktualnienie subskrypcji

Jeśli rzeczywiście chcesz uaktualnić swoją subskrypcję platformy Azure (np. przełączanie z bezpłatnej na płatność za rzeczywiste użycie), musisz przekonwertować subskrypcję.

* Aby uaktualnić z bezpłatnej wersji próbnej, zobacz [Zmień poziom subskrypcji bezpłatnej wersji próbnej lub programu Microsoft Imagine platformy Azure rozliczana według bieżącego użycia](../billing/billing-upgrade-azure-subscription.md).
* Aby zmienić konto zgodnie z rzeczywistym użyciem, zobacz [zmienić subskrypcję platformy Azure rozliczana według bieżącego użycia do innej oferty](../billing/billing-how-to-switch-azure-offer.md).

Jeśli nie można przekonwertować subskrypcję, [utworzyć żądanie pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md). Wybierz **zarządzania subskrypcjami** jako typ problemu.

## <a name="service-limitations"></a>Ograniczenia usługi

Niektóre usługi wymagają uwagi dodatkowe podczas przenoszenia zasobów. Jeśli przenosisz następujące usługi, upewnij się, że możesz sprawdzić, wytyczne i ograniczenia.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasyczny model wdrażania](./move-limitations/classic-model-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)
* [Sieci wirtualne](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Dużych żądań

Jeśli to możliwe, podział dużych przenosi do operacji przenoszenia oddzielne. Menedżer zasobów natychmiast zwraca błąd, jeśli istnieje więcej niż 800 zasobów w ramach jednej operacji. Jednak przenoszenia zasobów mniej niż 800 może również zakończyć się niepowodzeniem, przekroczeniem limitu czasu.

## <a name="next-steps"></a>Następne kroki

Poleceń przenieść zasoby można znaleźć [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
