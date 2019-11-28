---
title: Monitorowanie i śledzenie użycia bezpłatnej usługi platformy Azure
description: Dowiedz się, jak sprawdzić użycie bezpłatnej usługi w witrynie Azure Portal.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c402327c0f419578fdfa7e4f95fd30e47098c918
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224079"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Sprawdzanie użycia bezpłatnej usługi działającej w ramach bezpłatnego konta platformy Azure

Za korzystanie z bezpłatnych usług działających w ramach bezpłatnego konta platformy Azure nie są naliczane opłaty, chyba że przekroczono limity dotyczące tych usług. Aby nie przekraczać tych limitów, można śledzić użycie bezpłatnych usług za pomocą witryny Azure Portal.

## <a name="check-usage-in-the-azure-portal"></a>Sprawdzanie użycia w witrynie Azure Portal

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2.  Wyszukaj pozycję **Subskrypcje**.

    ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/billing-check-usage-of-free-services/billing-search-subscriptions.png)

3.  Wybierz subskrypcję utworzoną podczas tworzenia bezpłatnego konta platformy Azure.

4.  Przewiń w dół, aby wyświetlić tabelę zawierającą informacje o użyciu bezpłatnych usług.

    ![Zrzut ekranu przedstawiający użycie bezpłatnych usług](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Tabela zawiera następujące kolumny:

* **Miernik:** Określa jednostkę miary wykorzystywanej usługi.
* **Użycie/limit:** Użycie i limit dla miernika w bieżącym miesiącu.
* **Stan:** Stan użycia usługi. Na podstawie użycia jest określany jeden z następujących stanów:
  * **Nieużywany:** Nie użyto miernika lub użycie miernika nie dotarło do systemu rozliczeń.
  * **Przekroczono \<data>:** Przekroczono limit miernika dnia \<data>.
  * **Małe prawdopodobieństwo przekroczenia:** Przekroczenie limitu dla miernika jest mało prawdopodobne.
  * **Przekracza \<data>:** Prawdopodobnie nastąpi przekroczenie limitu dla miernika dnia \<data>.

> [!IMPORTANT]
>
> Bezpłatne usługi są dostępne tylko dla subskrypcji utworzonej podczas tworzenia bezpłatnego konta platformy Azure. Jeśli tabela bezpłatnych usług nie jest widoczna na stronie przeglądu subskrypcji, nie są one dostępne dla subskrypcji.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Uaktualnienie bezpłatnego konta platformy Azure](billing-upgrade-azure-subscription.md)
