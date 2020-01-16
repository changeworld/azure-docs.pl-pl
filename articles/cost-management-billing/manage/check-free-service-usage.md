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
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992831"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Sprawdzanie użycia bezpłatnej usługi działającej w ramach bezpłatnego konta platformy Azure

Za korzystanie z bezpłatnych usług działających w ramach bezpłatnego konta platformy Azure nie są naliczane opłaty, chyba że przekroczono limity dotyczące tych usług. Aby nie przekraczać tych limitów, można śledzić użycie bezpłatnych usług za pomocą witryny Azure Portal.

## <a name="check-usage-in-the-azure-portal"></a>Sprawdzanie użycia w witrynie Azure Portal

1.  Zaloguj się do [portalu Azure](https://portal.azure.com).

2.  Wyszukaj pozycję **Subskrypcje**.

    ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Wybierz subskrypcję utworzoną podczas tworzenia bezpłatnego konta platformy Azure.

4.  Przewiń w dół, aby wyświetlić tabelę zawierającą informacje o użyciu bezpłatnych usług.

    ![Zrzut ekranu przedstawiający użycie bezpłatnych usług](./media/check-free-service-usage/subscription-usage-free-services.png)

    Tabela zawiera następujące kolumny:

* **Licznik:** Określa jednostkę miary używanej usługi.
* **Użycie/limit:** Użycie i limit bieżącego miesiąca dla miernika.
* **Stan:** Stan użycia usługi. Na podstawie użycia jest określany jeden z następujących stanów:
  * **Nie w użyciu:** Nie użyto miernika lub użycie miernika nie osiągnęło systemu rozliczeń.
  * **Przekroczono \<daty >:** Przekroczono limit dla licznika na \<Data >.
  * **Mało prawdopodobne, aby przekroczyć:** Mało prawdopodobne jest przekroczenie limitu dla licznika.
  * **Przekracza \<daty >:** Istnieje możliwość przekroczenia limitu dla licznika na \<Data >.

> [!IMPORTANT]
>
> Bezpłatne usługi są dostępne tylko dla subskrypcji utworzonej podczas tworzenia bezpłatnego konta platformy Azure. Jeśli tabela bezpłatnych usług nie jest widoczna na stronie przeglądu subskrypcji, nie są one dostępne dla subskrypcji.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Uaktualnienie bezpłatnego konta platformy Azure](upgrade-azure-subscription.md)
