---
title: Zwiększenie limitu sieci | Dokumenty firmy Microsoft
description: Zwiększanie limitu sieci
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547809"
---
# <a name="networking-limit-increase"></a>Zwiększanie limitu sieci

Użyj [witryny Azure portal,](https://portal.azure.com) aby zwiększyć przydział sieci.

Aby wyświetlić bieżące użycie i przydział sieci w witrynie Azure portal, otwórz subskrypcję, a następnie wybierz **pozycję Użyciae + przydziały**. Można również użyć następujących opcji, aby wyświetlić użycie sieci i limity.

* [Użycie interfejsu WIERSZA POLECENIA](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Interfejs API użycia sieci](/rest/api/virtualnetwork/virtualnetworks/listusage)

Możesz poprosić o zwiększenie, korzystając z **pomocy + pomocy technicznej** lub w **użyciach + przydziałów** w portalu.

> [!Note]
> Aby zmienić domyślny rozmiar **prefiksów publicznych adresów IP,** wybierz z listy rozwijanej opcję **Min Public IP InterNetwork Prefix Length.**

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Żądanie zwiększenia przydziału sieci na poziomie subskrypcji przy użyciu pomocy + pomocy technicznej

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć żądanie pomocy technicznej przy użyciu **pomocy + pomocy technicznej** w witrynie Azure portal.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie wybierz **pomoc + pomoc z** menu portalu Azure lub wyszukaj i wybierz pomoc + pomoc **.**

    ![Pomoc i obsługa techniczna](./media/networking-quota-request/help-plus-support.png)

1. Wybierz **pozycję Nowe żądanie pomocy technicznej**.

    ![Nowe żądanie pomocy technicznej](./media/networking-quota-request/new-support-request.png)

1. W przypadku **typu problem**wybierz pozycję **Limity usługi i subskrypcji (przydziały).**

    ![Wybieranie limitów subskrypcji z listy rozwijanej typu problemu](./media/networking-quota-request/select-quota-issue-type.png)

1. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybierz nowy system subskrypcjiSR](./media/networking-quota-request/select-subscription-support-request.png)

1. W obszarze **Typ przydziału**wybierz pozycję **Sieć**. Wybierz **dalej: Rozwiązania**.

    ![Wybierz typ przydziału](./media/networking-quota-request/select-quota-type-network.png)

1. W **obszarze SZCZEGÓŁY PROBLEMU**wybierz pozycję **Podaj szczegóły** i wprowadź dodatkowe informacje, aby pomóc w przetworzeniu żądania.

    ![Podaj szczegóły](./media/networking-quota-request/provide-details-link.png)

1. W panelu **Szczegóły przydziału** wybierz model wdrożenia, lokalizację i zasoby, które mają być uwzględnione w żądaniu.

    ![Szczegóły przydziału DM](./media/networking-quota-request/quota-details-network.png)

1. Wprowadź nowe limity, które chcesz w subskrypcji. Aby usunąć wiersz, usuń zaznaczenie zasobu z menu **Zasoby** lub wybierz ikonę odrzuć "x". Po wprowadzeniu przydziału dla każdego zasobu wybierz pozycję **Zapisz i kontynuuj** tworzenie żądania pomocy technicznej.

    ![Nowe limity](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Żądanie zwiększenia przydziału sieci na poziomie subskrypcji przy użyciu użycia + przydziałów

Postępuj zgodnie z tymi instrukcjami, aby utworzyć żądanie pomocy technicznej przy użyciu **przydziału Użycia +** w witrynie Azure Portal.

1. Z https://portal.azure.com, wyszukaj i wybierz **subskrypcje**.

    ![Subskrypcje](./media/networking-quota-request/search-for-suscriptions.png)

1. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

    ![Wybieranie subskrypcji](./media/networking-quota-request/select-subscription-change-quota.png)

1. Wybierz **użycie + przydziały**

    ![Wybieranie użycia i przydziałów](./media/networking-quota-request/select-usage-plus-quotas.png)

1. W prawym górnym rogu wybierz pozycję **Poproś o zwiększenie**.

    ![Zwiększenie żądania](./media/networking-quota-request/request-increase-from-subscription.png)

1. Wykonaj kroki rozpoczynające się od kroku 3 w [zwiększeniu przydziału żądania sieci na poziomie subskrypcji](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Informacje o ograniczeniach sieci

Aby dowiedzieć się więcej o limitach sieci, zobacz [sekcję Sieć](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) na stronie limity lub często zadawane pytania dotyczące limitów sieciowych.
